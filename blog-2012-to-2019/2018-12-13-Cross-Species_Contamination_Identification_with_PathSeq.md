## Cross-Species Contamination Identification with PathSeq

By yfarjoun

<h3>Overview</h3>

<p>PathSeq is a computational pathogen discovery pipeline in the Genome Analysis Toolkit (GATK) for detecting microbial organisms from short-read deep sequencing of a host organism, such as human. The pipeline detects microbial reads in the host organism by performing read quality filtering, subtracting reads derived from the host, aligning the remaining (non-host) reads to a reference microbe genome, and finally generating a table of the detected microbial organisms. The GATK version improves on the previous version of the pipeline by incorporating faster computational approaches, broadening the use cases of the pipeline, and integrates the pipeline in GATKs Apache Spark framework enabling parallelized data processing (Mark et al., 2018). We've written in detail in our documentation on <a rel="nofollow" href="https://software.broadinstitute.org/gatk/documentation/article?id=10913">how to use PathSeq</a>, but I have a particularly intriguing story to share about how I used the PathSeq workflow in FireCloud to quickly identify the cause of mysteriously low sequencing alignment rates.</p>

<p>I first heard about this specific problem when a project manager in the sequencing lab told me that they were seeing low alignment rates on multiple samples from the same project, and asked if I could help. We normally see alignment rates (as reported from Picard’s <a rel="nofollow" href="https://software.broadinstitute.org/gatk/documentation/tooldocs/current/picard_analysis_CollectAlignmentSummaryMetrics.php">CollectAlignmentSummaryMetrics</a>) above 99%, but this cohort of samples was producing rates between 60% and 95%, requiring the lab to sequence more in order to reach the agreed-upon coverage for the project (which doesn’t include unaligned reads, of course).</p>

<p>I suspected bacterial contamination since (by manual inspection) the unaligned reads did not seem to be artifactual (for example they all had pretty random-seeming sequence, not all the same). To approach this problem, I used the new GATK4 PathSeq Workflow (<a rel="nofollow" href="https://doi.org/10.1093/bioinformatics/bty501">publication</a>, <a rel="nofollow" href="https://software.broadinstitute.org/gatk/documentation/article?id=10913">how-to tutorial</a>) and a small Python script. In this document I’ll walk you through how I used PathSeq on FireCloud using workflows and the beta “Notebooks” feature to quickly identify that the unaligned reads all belong to a single bacterial genus, <em>Burkholderia</em>.</p>

<p>PathSeq Data Bundle and Documentation :</p>

<ul><li>Access Resource Bundle from a FTP Server - <a rel="nofollow" href="https://software.broadinstitute.org/gatk/download/bundle"> ftp://ftp.broadinstitute.org/bundle/pathseq/</a></li>
<li>Google Cloud Bucket: <a rel="nofollow" href="https://console.cloud.google.com/storage/browser/gatk-best-practices/pathseq/">gs://gatk-best-practices/pathseq/</a></li>
<li>Pathseq Tool documentation: <a rel="nofollow" href="https://software.broadinstitute.org/gatk/documentation/tooldocs/4.0.8.1/org_broadinstitute_hellbender_tools_spark_pathseq_PathSeqPipelineSpark.php">GATK Tool Documentation Index</a> (all related tools documented under the Metagenomics category)</li>
<li>Featured PathSeq Workspace - <a rel="nofollow" href="https://portal.firecloud.org/#workspaces/help-gatk/contamination-identification-with-pathseq">contamination-identification-with-pathseq</a></li>
</ul><hr></hr><h3>Setup</h3>

<p>I prepared to import the data into my <a rel="nofollow" href="https://software.broadinstitute.org/firecloud/documentation/article?id=10746">cloned PathSeq workspace</a>.  First a set of “participant”s and a set of “sample”s were added to the data model. I wanted to perform batch processing in this experiment thus I also imported a “sample_set.”  Below are examples of simple TSV files that can be used to quickly setup the data model.</p>

<p>Example Participant TSV:</p>

<pre class="code codeBlock" spellcheck="false">    entity:participant_id   
    dummy_participant
</pre>

<p>After importing the participant, I imported the samples with three vital columns: <code class="code codeInline" spellcheck="false">entity:sample_id</code>, <code class="code codeInline" spellcheck="false">participant_id</code>, and <code class="code codeInline" spellcheck="false">WGS_bam_path</code> (the path to the bam file). I have simplified the text in the path column for the purposes of this blog post. The additional column labeled <code class="code codeInline" spellcheck="false">Aligned</code> contain the percent of aligned reads as reported by the preceding data processing pipeline. This column isn’t needed for running the PathSeq pipeline but will be used in the Python script written in the Notebook.</p>

<p>Example Sample TSV:</p>

<pre class="code codeBlock" spellcheck="false">entity:sample_id    Aligned WGS_bam_path    participant_id
sample1 68  gs://bucket/directory/file1.unmapped.bam    dummy_participant
sample2 80  gs://bucket/directory/file2.unmapped.bam    dummy_participant
sample3 76  gs://bucket/directory/file3.unmapped.bam    dummy_participant
sample4 83  gs://bucket/directory/file4.unmapped.bam    dummy_participant
sample5 86  gs://bucket/directory/file5.unmapped.bam    dummy_participant
sample6 89  gs://bucket/directory/file6.unmapped.bam    dummy_participant
sample7 92  gs://bucket/directory/file7.unmapped.bam    dummy_participant
sample8 99  gs://bucket/directory/file8.unmapped.bam    dummy_participant
sample9 99  gs://bucket/directory/file9.unmapped.bam    dummy_participant
</pre>

<p>Several comments need to be said about the file above:<br>
1. The column separator is <code class="code codeInline" spellcheck="false">&lt;TAB&gt;</code>. <br>
2. The <code class="code codeInline" spellcheck="false">entity:sample_id</code> column must be first.<br>
3. The value of the <code class="code codeInline" spellcheck="false">participant_id</code> column must exist in the <code class="code codeInline" spellcheck="false">entity:participant_id</code> of the Participant TSV.<br>
4. The column header <code class="code codeInline" spellcheck="false">WGS_bam_path</code> is referenced in the method configuration that will be used to run the PathSeq pipeline. It is important to be certain that the correct column is being referenced in the method configuration. Any discrepancy between the column names in the Data tab and method configuration will cause the method to fail.</p>

<p>After importing this data, I turn to the last TSV file: sample_set. This will enable me to analyze all of the samples with a single action. <br>
Example Sample_Set TSV:</p>

<pre class="code codeBlock" spellcheck="false">membership:sample_set_id    sample
dummy_sample_set    sample1
dummy_sample_set    sample2
dummy_sample_set    sample3
dummy_sample_set    sample4
dummy_sample_set    sample5
dummy_sample_set    sample6
dummy_sample_set    sample7
dummy_sample_set    sample8
dummy_sample_set    sample9
</pre>

<p>Here, <code class="code codeInline" spellcheck="false">dummy_sample_set</code> is an arbitrary name for the sample set, and this file specifies the “membership” of sample in sample_sets. A given sample can be in more than one sample_set.</p>

<p>After importing this third file I can see three sub-tabs within the “Data” tab (In the case I’m showing here, my data model has one participant per sample and two sample_sets):</p>

<p><img src="https://us.v-cdn.net/5019796/uploads/editor/yw/36ndwnbkiyo0.png" alt="" title="" class="embedImage-img importedEmbed-img"></img></p>

<h3>Launch Analysis</h3>

<p>To run the analysis on the desired sample_set, I go into the “Method Configuration” tab, select the “pathseq-pipeline-WGS” configuration and click “Launch Analysis” after setting the method variables. Now the data model is presented to me again in a window and I select the “entity” on which to run the configuration. Before I run a configuration on an entire cohort, I like to test it on a single sample to whether check my workspace is correctly setup. To perform a single run, I choose one of the samples in the sample sub-tab and click “Launch”.  The view switches to the “Monitor” tab, and the submission I just launched is shown to be “Queued”:<br><img src="https://us.v-cdn.net/5019796/uploads/editor/l5/s9xr3korlmf6.png" alt="" title="" class="embedImage-img importedEmbed-img"></img></p>

<p>During the first attempt, my submission reported an error because I had not given read access to the <a rel="nofollow" href="https://software.broadinstitute.org/firecloud/blog?id=11342">pet service account</a> so that it can read my files. If you upload your data into the native Google Cloud bucket that is created along with a new FireCloud workspace, this won't be an issue. But an error came up in my case because I actually had set up my workspace such that I was referencing Google Cloud buckets that I had previously created. So if this happens to you, head over to the <a rel="nofollow" href="https://console.cloud.google.com/storage/browser">Google Cloud Platform Storage Console</a>, select the bucket that holds your files, and give the "Storage-Object-Viewer" role to that service account.</p>

<p>After I added the role, I relaunched the method and since it succeeded, I proceeded to launch it over the whole sample_set.</p>

<p>To launch over an entire set, I go (as before) to the “Method Configuration” tab, select the “pathseq-pipeline-WGS” configuration and click “Launch Analysis”. But now, I go to the sample_sets tab and choose the sample_set I want to use. Before clicking the “Launch” button, I type “this.samples” into the “Define Expression” textbox:<br><img src="https://us.v-cdn.net/5019796/uploads/editor/iw/4by8z918pr0i.png" alt="" title="" class="embedImage-img importedEmbed-img"></img></p>

<p>And only then I click the “Launch” button.</p>

<p>At this point, I see my request has been “submitted” and eventually it transitions to “running” (refreshing the page updates the status). Clicking on “view” should show you the progression of all the samples. Because I already ran one sample to completion, one of the workflows completes quickly, as it uses <a rel="nofollow" href="https://software.broadinstitute.org/firecloud/documentation/article?id=9313">call caching</a> to retrieve output results from my previous run and will not actually re-compute anything. The remaining samples will run in parallel and when complete the entire submission will be marked as “done”:</p>

<p><img src="https://us.v-cdn.net/5019796/uploads/editor/4k/7hjn61o8uh5i.png" alt="" title="" class="embedImage-img importedEmbed-img"></img></p>

<p>So, where are the results? If the output variables are set in the method configuration the results will be listed under new columns under the “sample” entity in the Data tab.</p>

<p><img src="https://us.v-cdn.net/5019796/uploads/editor/bk/c50tywe9ukf7.png" alt="" title="" class="embedImage-img importedEmbed-img"></img></p>

<h3>Additional Analysis using Jupyter</h3>

<p>To complete the analysis, we want to see what is the top contaminating genus and how it is related to the “Aligned” value that we have for each sample in a scatter plot. To do this I switched my analysis to “Jupyter Notebooks”. In the Notebook, python functions were used to retrieve the metrics produced by the PathSeq workflow, extracting the vital information from metrics, then creating a sorted table with the extracted data to show a clear picture of what PathSeq found. The data was also used to generate a scatter plot using the percent of alignment from the original reads on the X axis and percent of these samples having reads aligned to top contaminating genus on the Y axis.<br>
Here is a html of the completed Jupyter Notebook used to find the top contaminate from the PathSeq output metrics. <a rel="nofollow" href="https://storage.googleapis.com/gatk-best-practices/pathseq/notebook/pathseq-topmed-blog.htm">pathseq-topmed-blog</a> . <br><img src="https://us.v-cdn.net/5019796/uploads/editor/5a/x1fna8onx7ag.png" alt="" title="" class="embedImage-img importedEmbed-img"></img></p>

<p>In my case, the resulting scatter plot produced a downward slope that shows that as the samples had less reads aligned (to hg38) more reads were were aligning to PathSeqs top genus.</p>

<p>You’ll also find a count of the top genus of the samples:<br><img src="https://us.v-cdn.net/5019796/uploads/editor/bn/unznqy165ycd.png" alt="" title="" class="embedImage-img importedEmbed-img"></img></p>

<p>Indicating that, in my case, all 78 samples are from “Burkholderia.”</p>

<h3>Closure</h3>

<p>With this data in hand, I went back to the collaborator to discuss how PathSeq detected <em>Burkholderia</em> to be the likely contaminant. Given other key details such as the location the samples were collected (not conveyed to me in advanced), it made sense to the project manager that this proteobacteria would be detected as the contaminant. As a result, the collaborator has since identified some problems in their sample-collection protocols and have taken action to improve them.</p>

<h4>Nota Bene</h4>

<p>Like many others, the term “contamination” is overloaded in sequencing parlance, and while contamination of a human sample with that of another human (at any stage of the pipeline) is certainly of interest and importance, this is not the type of contamination that can be detected by PathSeq. For that contamination I can refer you to VerifyBamID, ContEst (GATK3) and EstimateContamination (GATK4). Pathseq can only detect contamination by a different species.</p>

<h4>Featured PathSeq workspace</h4>

<p>For those interested in testing the PathSeq pipeline, a workspace has been provided in FireCloud. Ideally we’d like to have a workspace that replicates this blog but due to the data privacy regulations of TopMed we’ve instead created similar scenario regarding meat contamination. The workspace uses a few useful workflows to simulate contaminated sequence data and detect the contamination using PathSeq. Similar to this blog a Jupyter Notebook is used to sort the metrics outputs from PathSeq to identify the top contaminate. Workspace: <a rel="nofollow" href="https://portal.firecloud.org/#workspaces/help-gatk/contamination-identification-with-pathseq">contamination-identification-with-pathseq</a></p>

<h4>Citation</h4>

<p>Mark A Walker, Chandra Sekhar Pedamallu, Akinyemi I Ojesina, Susan Bullman, Ted Sharpe, Christopher W Whelan, Matthew Meyerson; (2018) GATK PathSeq: a customizable computational tool for the discovery and identification of microbial sequences in libraries from eukaryotic hosts, Bioinformatics, , bty501, <a href="https://doi.org/10.1093/bioinformatics/bty501" rel="nofollow">https://doi.org/10.1093/bioinformatics/bty501</a></p>

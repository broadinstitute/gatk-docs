## Can a biochemist with one Python course under her belt start running analyses in the cloud?

By shlee

<p><a name="top" id="top"></a> </p><div style="text-align: center;"><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/d3/3bba4972556ce351178177c5a4d83e.png" width="300" alt="image" style="text-align: ;" class="embedImage-img importedEmbed-img"></img></div><br>
Find out and learn some practical steps to cloud debugging.

<p>Specifically, I tested the alpha release of <em>Google Genomics Pipelines API</em> that uses the command-line. Down the road, we will post similarly for the UI-driven systems <em>FireCloud</em> and <em>Workbench</em>. In this particular challenge, my aim is to first genotype a trio and then a cohort of 17 whole genome BAMs that are available in the cloud. I need the resulting VCF callsets within a week.</p>

<hr></hr><h3>I approach this like an experiment.</h3>

<p>As a biochemistry PhD, I approach this challenge much like a complicated protocol whose chemistry I do not fully understand, nor need to understand, but that I need to get working for its end results.</p>

<hr></hr><h3>I start with the following pieces of information.</h3>

<ol><li>GATK commands I must meld together into WDL workflow(s), an idea on how I should parallelize the variant calling step and novice <a rel="nofollow" href="https://software.broadinstitute.org/gatk/blog?id=8608">WDL scripting</a> skills. My laptop is set up to run GATK commands and WDL scripts locally <sup><a rel="nofollow" href="#1">1</a></sup>.</li>
<li>A publicly available Docker image containing all the tools I need <sup><a rel="nofollow" href="#2">2</a></sup>.</li>
<li>A draft version of the now published <a rel="nofollow" href="https://cloud.google.com/genomics/v1alpha2/gatk">GATK-Google Genomics tutorial</a> that tells you what to install and that uses the <code class="code codeInline" spellcheck="false">gcloud alpha genomics pipelines run</code> command to run our <a rel="nofollow" href="https://github.com/broadinstitute/wdl/tree/develop/scripts/broad_pipelines">PairedEndSingleSampleWf</a> WDL script on publicly available example data and using the same Docker container <sup><a rel="nofollow" href="#3">3</a></sup>.</li>
<li>A Google Cloud bucket associated with a cloud billing project and the assurance that my runs will be drops in the ocean of our development budget <sup><a rel="nofollow" href="#4">4</a>,<a rel="nofollow" href="#5">5</a></sup>.</li>
<li>And not trivially, the location of 17 public-arena indexed BAMs in the cloud that are each over 100GB in size <sup><a rel="nofollow" href="#6">6</a></sup>.</li>
</ol><hr></hr><h3>Given the time constraint, I parallelize in two ways.</h3>

<p>One of the <a rel="nofollow" href="http://gatkforums.broadinstitute.org/gatk/discussion/7372">attractions of the cloud</a> is parallelizable compute and in WDL workflows this is embodied by the <a rel="nofollow" href="http://gatkforums.broadinstitute.org/wdl/discussion/6716">scatter</a> function. Given my time constraint, parallelism is a feature I must use. I can parallelize at two levels. First, I can process each sample concurrently for the first part of the pipeline. In the cloud, if it takes an hour to process one sample, then it takes an hour to process a hundred. Second, for some steps I can scatter over genomic territories, where the smallest parallelizable unit is a region bookmarked by gaps, i.e. <i>N</i>s.</p>

<p>I learn that nested scatters (a.k.a. scatter of scatters) is <a rel="nofollow" href="https://github.com/broadinstitute/cromwell/issues/838">yet to be supported</a>. Because of this, I break my workflow into two WDL scripts. The first script takes a single sample BAM as input and generates a GVCF in HaplotypeCaller’s ERC mode. The second script takes all the GVCFs and generates a cohort VCF callset. In terms of scattering over genomic territories, I decide to stick with a 17-way scatter over the human genome. I will spend the chunk of time this genome scattering entails judiciously by ensuring my scripts work.</p>

<hr></hr><h3>My script testing is iterative.</h3>

<p><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/44/19739c0fbef9a1588e48b7898983ad.png" width="450" alt="image" style="float: right;" class="embedImage-img importedEmbed-img"></img> I manage my script development by incrementally testing locally and in the cloud using a small dataset and then a large unit of data, e.g. a full BAM (see <strong>Table</strong>). The scripts run my small datasets on the cloud in minutes so the turnaround between job submission and feedback is quick. I find myself resorting to list of lists (a.k.a. array of <a rel="nofollow" href="https://www.quora.com/What-is-an-array">arrays</a>) to group the scatter intervals and also to group the different sample GVCFs together per genomic territory. The <strong>Figure</strong> below summarizes my process. For where you can ask for help for different sources of error, see <a rel="nofollow" href="#7">7</a>.</p>

<p><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/83/7c8283f2592bb76fa62a2b6208b2a0.png" alt="image" class="embedImage-img importedEmbed-img"></img></p>

<p>The tutorial’s example command (shown below) and its <code class="code codeInline" spellcheck="false">.yaml</code> file are what I start with to submit a <em>pipeline job</em> to Google Cloud. This sets in motion a number of processes, described <a rel="nofollow" href="https://github.com/googlegenomics/pipelines-api-examples/tree/master/wdl_runner#overview">here</a>, including the <em>WDL Runner</em> Python script that then launches Cromwell.</p>

<pre class="code codeBlock" spellcheck="false">gcloud alpha genomics pipelines run \
    --pipeline-file $my_dir/wdl_pipeline_GotC.yaml \
    --zones us-central1-f \
    --logging gs://shlee-dev/quicktest/logs \
    --inputs-from-file WDL=$my_dir/GenotypeScatteredGVCFs_cloud_quicktest.wdl \
    --inputs-from-file WORKFLOW_INPUTS=$my_dir/GenotypeScatteredGVCFs_cloud_quicktest.json \
    --inputs-from-file WORKFLOW_OPTIONS=$my_dir/empty.json \
    --inputs WORKSPACE=gs://shlee-dev/quicktest \
    --inputs OUTPUTS=gs://shlee-dev/quicktest/callsets/
</pre>

<p>Once I submit a pipeline job, I get a pipelines operations ID:</p>

<pre class="code codeBlock" spellcheck="false">Running: [operations/EKLyxLLwKhi7mbayutDpvl4gw7vetLsXKg9wcm9kdWN0aW9uUXVldWU] 
</pre>

<p>I can poll the status of the pipeline job using the operations ID in the command:</p>

<pre class="code codeBlock" spellcheck="false">gcloud alpha genomics operations describe EKLyxLLwKhi7mbayutDpvl4gw7vetLsXKg9wcm9kdWN0aW9uUXVldWU
</pre>

<p>And get a status message, e.g. running or failed, as well as some additional information. Alternatively, I can add <code class="code codeInline" spellcheck="false">--format='yaml(done, error, metadata.events, name)'</code> to the command for a summary of the status. If a job is successful, then I look for the outputs in the directory I had previously specified with <code class="code codeInline" spellcheck="false">--inputs OUTPUTS=</code>. If a job has failed, the status message may or may not include details helpful towards debugging. If it does not, then I turn to a web browser and navigate to my Google bucket <sup><a rel="nofollow" href="#4">4</a></sup>.</p>

<p><strong>Here are roughly the steps I take in looking for the source of error.</strong></p>

<ol><li>Per run, I get three <a rel="nofollow" href="https://cloud.google.com/genomics/v1alpha2/pipelines-api-troubleshooting#pipeline_operation_log_files">operation log files</a>: <code class="code codeInline" spellcheck="false">*.log</code>, <code class="code codeInline" spellcheck="false">*-stderr.log</code> and <code class="code codeInline" spellcheck="false">*-stdout.log</code>, where * is the operation ID. These I find in the logging directory I had specified with <code class="code codeInline" spellcheck="false">--logging</code>. I mouse-click on each log file from the bucket UI to view each of them in a new tab within the browser. I look for the first instance of <em>ERROR</em> or <em>Failed</em> lines within the logs and then examine these and the  <em>surrounding</em> lines for informative messages.</li>
<li><p>If the error source is ambiguous, then I look for the Cromwell job ID. This should be listed in the meta status message and/or in an operation log if the job got to the point of launching Cromwell. If it is not, then the job likely errored at the <em>Pipelines API</em> level.</p>

<p>Let’s examine an example directory structure from a log.</p>

<pre class="code codeBlock" spellcheck="false">gs://shlee-dev/platinum/ScatterHaplotypeCaller/699f1f77-be1c-4392-b3b1-5d103a43b232/call-HaplotypeCaller/shard-0/NA12882_2_S1.chrM.vcf.gz.tbi
</pre>

<p>Here, <code class="code codeInline" spellcheck="false">ScatterHaplotypeCaller</code> is my WDL workflow name, what precedes it is the gcloud workspace directory I had specified with <code class="code codeInline" spellcheck="false">--inputs WORKSPACE=</code> and the long alphanumeric string that follows corresponds to the Cromwell job ID. The Cromwell job folder in turn contains directories for the <em>launched</em> <a rel="nofollow" href="http://gatkforums.broadinstitute.org/wdl/discussion/6707/task#latest">WDL workflow tasks</a>, e.g. <code class="code codeInline" spellcheck="false">call-HaplotypeCaller</code>. Knowing the order of the tasks in my WDL workflow, I can identify the last task that ran. I drill down into the task folders to look for expected results files, and if I don’t see them, then I view the Cromwell log files, <code class="code codeInline" spellcheck="false">*.log</code>, <code class="code codeInline" spellcheck="false">*-stdout.log</code> and <code class="code codeInline" spellcheck="false">*-stderr.log</code>. Again, I search for <em>ERROR</em> or <em>Failed</em> lines and their surrounding context. GATK tool errors are usually found in the <code class="code codeInline" spellcheck="false">stderr.log</code> file.</p></li>
<li><p>If the error is at a scattered task, and all expected shards appear present, then well, I can repeat step (2) for each shard. Or, I can use the following <a rel="nofollow" href="http://tldp.org/HOWTO/Bash-Prog-Intro-HOWTO-7.html">bash for loop</a> <sup><a rel="nofollow" href="#8">8</a></sup> that iterates through <em>all</em> the shards and files ending in <code class="code codeInline" spellcheck="false">stderr.log</code> using <a rel="nofollow" href="https://en.wikipedia.org/wiki/Glob_(programming)">globbing</a>. The <code class="code codeInline" spellcheck="false">cat</code> function in conjunction with <code class="code codeInline" spellcheck="false">gsutil</code> <sup><a rel="nofollow" href="#9">9</a></sup> probes the cloud bucket file system. I grep for <em>ERROR</em> as an example because GATK tools use this convention. The script returns the name of each file then any lines with the word <em>ERROR</em>, if any, within the file.</p>

<pre class="code codeBlock" spellcheck="false">for FILE in `gsutil ls gs://shlee-dev/platinum/ScatterHaplotypeCaller/699f1f77-be1c-4392-b3b1-5d103a43b232/*/*/*stderr.log` ; \
do echo "got $FILE" ; \
gsutil cat $FILE | grep 'ERROR' ; done ;
</pre></li>
</ol><p><strong>Here is an example output of the script.</strong></p>

<pre class="code codeBlock" spellcheck="false">got gs://shlee-dev/filtering_tutorial/ScatterHaplotypeCaller/699f1f77-be1c-4392-b3b1-5d103a43b232/call-HaplotypeCaller/shard-9/HaplotypeCaller-9-stderr.log
##### ERROR ------------------------------------------------------------------------------------------
##### ERROR A USER ERROR has occurred (version 3.6-2-g69af359):
##### ERROR
##### ERROR This means that one or more arguments or inputs in your command are incorrect.
##### ERROR The error message below tells you what is the problem.
##### ERROR
##### ERROR If the problem is an invalid argument, please check the online documentation guide
##### ERROR (or rerun your command with --help) to view allowable command-line arguments for this tool.
##### ERROR
##### ERROR Visit our website and forum for extensive documentation and answers to
##### ERROR commonly asked questions https://www.broadinstitute.org/gatk
##### ERROR
##### ERROR Please do NOT post this error to the GATK forum unless you have really tried to fix it yourself.
##### ERROR
##### ERROR MESSAGE: The GATK cannot process compressed (.gz) reference sequences. Please unzip the file and try again.  Sorry for the inconvenience.
##### ERROR ------------------------------------------------------------------------------------------
</pre>

<p>Knowing the nature of the error, I can modify my WDL workflow accordingly and go through another iteration. In the case of the example error, I make sure to <a rel="nofollow" href="https://software.broadinstitute.org/gatk/documentation/article?id=1204">provide uncompressed reference sequences</a>.</p>

<hr></hr><h3>Using the cloud makes me feel the meaning of elastic.</h3>

<p>It feels refreshing that I can run as many samples and as many experiments as I can submit with my fingers on the computer. This contrasts with e.g. my experience with fractionating polyribosomes for their associated transcripts. I would run two staggered 100K x <em>g</em> ultracentrifuge runs <em>in a day</em> because the rotor could only hold ten samples at a time and I needed duplicates per variable because per run at least one tube would shatter. In cloud computing, that busted tube doesn’t set you back in terms of more physical toil. Its existence just gets buried in the billing <sup><a rel="nofollow" href="#5">5</a></sup>.</p>

<p>Also, the fact that the bucket address alone enables me to use public data <em>directly</em>, without copying files or the need for additional authentication by Illumina, hits home how seamless sharing on the cloud is.</p>

<hr></hr><h3>I come away with the aimed-for callsets.</h3>

<p>Debugging was definitely a process and I assume its extent is proportional to my inexperience. Once my script ran successfully for one BAM, running the remaining samples took little time. Because I was developing and running two workflows in tag-team style (first sample-level variant calling with HaplotypeCaller, then cohort genotyping with GenotypeGVCFs), I was too preoccupied to investigate why two of the 17 public-arena samples gave errors for the first workflow. I came away with the aimed-for trio callset and a 15-sample cohort variant callset in time for our team to prepare for the Basel workshop’s hands-on tutorial. <br><a name="1" id="1"></a></p>

<hr></hr><p><a rel="nofollow" href="#top">back to top</a></p>

<h3>Notes</h3>

<p><a name="2" id="2"></a>[<strong>1</strong>] At the time of testing in September 2016, I had a run-of-the-mill MacBook Pro with the following specs: 16GB of memory, 250GB of storage, OS X v10.10.5, Java v1.8.0_40 and the latest stable releases of GATK (v3.6), Picard (v2.5.0), Cromwell (v0.19.3) and Samtools (v1.3.1) as of September 2016. My Google Cloud SDK was v124.0.0.<br><a name="3" id="3"></a></p>

<p>[<strong>2</strong>] The Docker container is broadinstitute/genomes-in-the-cloud:2.2.3-1469027018. A newer iteration has come out called broadinstitute/genomes-in-the-cloud:2.2.4-1469632282. See the <a rel="nofollow" href="https://hub.docker.com/r/broadinstitute/genomes-in-the-cloud/">Docker repo</a> for version details. To see system and tool versions in a container, first install Docker. Then e.g. use <code class="code codeInline" spellcheck="false">docker pull broadinstitute/genomes-in-the-cloud:2.2.3-1469027018</code> to download the container and <code class="code codeInline" spellcheck="false">docker inspect broadinstitute/genomes-in-the-cloud:2.2.3-1469027018</code> to list its contents. <br><a name="4" id="4"></a></p>

<p>[<strong>3</strong>] As Geraldine outlines in the <a rel="nofollow" href="https://software.broadinstitute.org/gatk/blog?id=8104"><em>Going public with pipelines scripts</em></a> blogpost, what I present is only one of three ways to run WDL script workflows in the cloud. Also, the <a rel="nofollow" href="https://cloud.google.com/genomics/v1alpha2/gatk">Google Genomics <em>Pipelines API</em> tutorial</a> that uses the <a rel="nofollow" href="https://github.com/googlegenomics/pipelines-api-examples/tree/master/wdl_runner">wdl_runner</a> is one of several Google Genomics tutorials. You can find the others in the parent <a rel="nofollow" href="https://github.com/googlegenomics/pipelines-api-examples"><em>Pipelines-api-examples</em> repository</a>.<br><a name="5" id="5"></a></p>

<p>[<strong>4</strong>] Here’s what my bucket path looks like: <code class="code codeInline" spellcheck="false">https://console.cloud.google.com/storage/browser/shlee-dev/?project=broad-dsde-dev</code>. Notice the URL lists the associated billable project after the bucket name. This is because I set this association up at some point. Browse buckets associated with your Google email at <a rel="nofollow" href="https://console.cloud.google.com/storage/browser/">https://console.cloud.google.com/storage/browser/</a>. <br><a name="6" id="6"></a></p>

<p>[<strong>5</strong>] We will be posting estimated costs of running the PairedEndSingleSampleWf in the cloud, so be on the lookout. In the meantime here are links to <a rel="nofollow" href="https://cloud.google.com/products/calculator/">Google’s pricing calculator</a> and <a rel="nofollow" href="https://cloudpricingcalculator.appspot.com/static/data/pricelist.json">the underlying data used by the calculator</a>. Also, <a rel="nofollow" href="https://cloud.google.com/preemptible-vms/">this site</a> explains the cost-savings of preemptible instances. In general, processes that take a long time to run are more likely to be preempted  compared to shorter processes. If a certain process preempts regularly, consider (i) refactoring <a rel="nofollow" href="http://gatkforums.broadinstitute.org/wdl/discussion/6704/runtime#latest">runtime parameters</a> to reduce the probability of preemption, e.g. switch from a high to low demand zone, and (ii) whether you can shorten the time a process takes, e.g. remove a dependency that is causing a bottleneck that elongates processing time. An example of such a bottleneck is fetching information from an external server as described in <a rel="nofollow" href="https://github.com/samtools/htslib/issues/412">this github issue</a> and solved <a rel="nofollow" href="https://github.com/broadinstitute/wdl/pull/59">here</a>.<br><a name="7" id="7"></a></p>

<p>[<strong>6</strong>] The <a rel="nofollow" href="http://googlegenomics.readthedocs.io/en/latest/use_cases/discover_public_data/genomic_data_toc.html">Google Genomics <em>Cookbook</em></a>, hosted on <em>Read the Docs</em>, lists publicly available cloud data, e.g. <em>1000 Genomes Project</em> data and the <em>Illumina Platinum Genomes</em>. Each data page describes the data and provides the location of the data, e.g. the <em>Illumina Platinum Genomes</em> data bucket. On a side note, figuring out how to examine the headers of these cloud bucket BAMs was not trivial. Thankfully, I have helpful colleagues, one of whom gave me <code class="code codeInline" spellcheck="false">gsutil cp gs://directories/NA12878.bam - | samtools view -H</code> and another who gave me <code class="code codeInline" spellcheck="false">gsutil cat gs://directories/NA12878.bam | samtools view -H -</code>. <br><a name="8" id="8"></a></p>

<p>[<strong>7</strong>] Here are three distinct forums to search and ask questions on Google Cloud use, WDL scripting and GATK tools, respectively:</p>

<ul><li><p>Search the following Google Cloud sites:</p>

<ul><li>Google Cloud SDK gcloud documentation at <a rel="nofollow" href="https://cloud.google.com/sdk/gcloud/">https://cloud.google.com/sdk/gcloud/</a>.</li>
<li>Google Genomics <em>Pipelines API</em> how-to guides at <a rel="nofollow" href="https://cloud.google.com/genomics/v1alpha2/pipelines">https://cloud.google.com/genomics/v1alpha2/pipelines</a> and <a rel="nofollow" href="https://cloud.google.com/genomics/v1alpha2/pipelines-api-command-line">https://cloud.google.com/genomics/v1alpha2/pipelines-api-command-line</a>.</li>
<li>Google Genomics <em>Pipelines API</em> Troubleshooting guide at <a rel="nofollow" href="https://cloud.google.com/genomics/v1alpha2/pipelines-api-troubleshooting">https://cloud.google.com/genomics/v1alpha2/pipelines-api-troubleshooting</a>.</li>
<li>Finally, search the <em>Google Genomics Discuss</em> group’s posts at <a rel="nofollow" href="https://groups.google.com/forum/#!forum/google-genomics-discuss">https://groups.google.com/forum/#!forum/google-genomics-discuss</a>. Join the group to post a question. As of this writing, anyone can join.</li>
</ul></li>
<li><p>If the <a rel="nofollow" href="http://gatkforums.broadinstitute.org/wdl">WDL forum</a> does not answer your question, post it to  <a rel="nofollow" href="http://gatkforums.broadinstitute.org/wdl/post/question/wdl">http://gatkforums.broadinstitute.org/wdl/post/question/wdl</a>.</p></li>
<li>If the <a rel="nofollow" href="http://gatkforums.broadinstitute.org/gatk">GATK forum</a> does not answer your question, post it to  <a rel="nofollow" href="http://gatkforums.broadinstitute.org/gatk/post/question/gatk-support-forum">http://gatkforums.broadinstitute.org/gatk/post/question/gatk-support-forum</a>.</li>
</ul><p><a name="9" id="9"></a>[<strong>8</strong>] Many thanks to <a rel="nofollow" href="http://gatkforums.broadinstitute.org/gatk/profile/esalinas">esalinas</a> from the <em>FireCloud</em> team for sharing this script.</p>

<p>[<strong>9</strong>] Google SDK v135.0.0 installation includes <em>gsutils</em>, <em>gcloud</em> and <em>bq</em> command-line tools. See <a rel="nofollow" href="https://cloud.google.com/storage/docs/gsutil">here</a> for gsutil documentation.</p>

<hr></hr><p><a rel="nofollow" href="#top">back to top</a></p>

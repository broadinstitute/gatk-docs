## (How to) Call somatic copy number variants using GATK4 CNV

By shlee

<h4>A more recent CNV tutorial using v4.0.1.1 has been posted in two parts elsewhere at:</h4>

<ul><li><strong><a href="https://software.broadinstitute.org/gatk/documentation/article?id=11682" rel="nofollow">https://software.broadinstitute.org/gatk/documentation/article?id=11682</a> and</strong></li>
<li><strong><a href="https://software.broadinstitute.org/gatk/documentation/article?id=11683" rel="nofollow">https://software.broadinstitute.org/gatk/documentation/article?id=11683</a></strong></li>
</ul><p><strong>The first part mostly recapitulates the workflow on this page, while the second part adds detection of allelic ratios. Although the v4.0.1.1 tutorial is under review as of May 2, 2018, we recommend you update to the official workflow, especially if performing CNV analyses on WGS data. The official workflow has algorithmic improvements to the GATK4.beta workflow illustrated here.</strong></p>

<hr></hr><p><a rel="nofollow" href="https://us.v-cdn.net/5019796/uploads/FileUpload/ad/b9d40c6e5e090381a7d3fba8747e10.png"><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/ad/b9d40c6e5e090381a7d3fba8747e10.png" width="500" alt="image" style="float: right;" class="embedImage-img importedEmbed-img"></img></a> This demonstrative tutorial provides instructions and example data to detect somatic copy number variation (CNV) using a panel of normals (PoN). The workflow is optimized for Illumina short-read whole exome sequencing (WES) data. It is not suitable for whole genome sequencing (WGS) data nor for germline calling.</p>

<p>The tutorial recapitulates the GATK demonstration given at the <a rel="nofollow" href="https://software.broadinstitute.org/gatk/blog?id=8468">2016 ASHG meeting in Vancouver, Canada</a>, for a beta version of the CNV workflow. Because we are still actively developing the CNV tools (writing as of March 2017), the underlying algorithms and current workflow options, e.g. syntax, may change. However, the presented basic approach and general concepts will still be germaine. Please check the forum for updates.</p>

<p>Many thanks to Samuel Lee (<a rel="nofollow" href="http://gatkforums.broadinstitute.org/dsde/profile/slee">@slee</a>) for developing the example data, data figures and discussion that set the backbone of this tutorial.</p>

<p>► For a similar example workflow that pertains to earlier releases of GATK4, see <a rel="nofollow" href="http://gatkforums.broadinstitute.org/dsde/discussion/6791">Article#6791</a>. <br>
► For the mathematics behind the workflow, see <a rel="nofollow" href="https://github.com/broadinstitute/gatk-protected/blob/master/docs/CNVs/CNV-methods.pdf">this whitepaper</a>.</p>

<blockquote class="UserQuote blockquote"><div class="blockquote-content">
  <p class="blockquote-line">Different data types come with their own caveats. WGS, while providing even coverage that enables better CNV detection, is costly. SNP arrays, while the standard for CNV detection, may not be part of an analysis protocol. Being able to resolve CNVs from WES, which additionally  introduces artifacts and variance in the target capture step, requires sophisticated denoising.</p>
</div></blockquote>

<p><a name="top" id="top"></a></p>

<hr></hr><h3>Jump to a section</h3>

<ol><li><a rel="nofollow" href="#1">Collect proportional coverage using target intervals and read data using CalculateTargetCoverage</a></li>
<li><a rel="nofollow" href="#2">Create the CNV PoN using CombineReadCounts and CreatePanelOfNormals</a></li>
<li><a rel="nofollow" href="#3">Normalize a raw proportional coverage profile against the PoN using NormalizeSomaticReadCounts</a></li>
<li><a rel="nofollow" href="#4">Segment the normalized coverage profile using PerformSegmentation</a><br>
    ☞ <a rel="nofollow" href="#4.1"><em>I get an error at this step!</em></a></li>
<li>(Optional) <a rel="nofollow" href="#5">Plot segmented coverage using PlotSegmentedCopyRatio</a><br>
    ☞ <a rel="nofollow" href="#5.1"><em>What is the QC value?</em></a></li>
<li><a rel="nofollow" href="#6">Call segmented copy number variants using CallSegments</a></li>
<li><a rel="nofollow" href="#7">Discussion of interest to some</a><br>
    ☞ <a rel="nofollow" href="#7.1"><em>Why can't I use just a matched normal?</em></a><br>
    ☞ <a rel="nofollow" href="#7.2"><em>How do the results compare to SNP6 analyses?</em></a></li>
</ol><h3>Tools, system requirements and example data download</h3>

<ul><li><p>This tutorial uses a beta version of the CNV workflow tools within the GATK4 <a rel="nofollow" href="https://github.com/broadinstitute/gatk-protected/releases/tag/1.0.0.0-alpha1.2.3">gatk-protected-1.0.0.0-alpha1.2.3 pre-release</a> (Version:0288cff-SNAPSHOT from September 2016). We previously made the program jar specially available alongside the data bundle in the workshops directory <a rel="nofollow" href="https://software.broadinstitute.org/gatk/download/workshops">here</a>. The original worksheets are in the <code class="code codeInline" spellcheck="false">1610</code> folder. However, the data bundle was only available to workshop attendees. Note other tools in this program release may be unsuitable for analyses.</p>

<p>The example data is whole exome capture sequence data for chromosomes 1–7 of matched normal and tumor samples aligned to GRCh37. Because the data is from real cancer patients, we have anonymized them at multiple levels. The anonymization process preserves the noise inherent in real samples. The data is representative of Illumina sequencing technology from 2011.</p></li>
<li><p>R (install from <a rel="nofollow" href="https://www.r-project.org/">https://www.r-project.org/</a>) and certain R components. After installing R, install the components with the following command.</p>

<pre class="code codeBlock" spellcheck="false">Rscript install_R_packages.R 
</pre>

<p>We include install_R_packages.R in the tutorial data bundle. Alternatively, download it from <a rel="nofollow" href="https://github.com/broadinstitute/gatk-protected/blob/master/scripts/install_R_packages.R">here</a>.</p></li>
<li><p>XQuartz for optional <a rel="nofollow" href="#5">section 5</a>. Your system may already have this installed.</p></li>
<li>The tutorial does not require reference files. The optional plotting step that uses the PlotSegmentedCopyRatio tool plots against GRCh37 and should <strong>NOT</strong> be used for other reference assemblies.</li>
</ul><p><a name="1" id="1"></a></p>

<hr></hr><h2>1. Collect proportional coverage using target intervals and read data using CalculateTargetCoverage</h2>

<p>In this step, we collect proportional coverage using target intervals and read data. We have actually pre-computed this for you and we provide the command here for reference.</p>

<p>We process each BAM, whether normal or tumor. The tool collects coverage per read group at each target and divides these counts by the total number of reads per sample.</p>

<pre class="code codeBlock" spellcheck="false">java -jar gatk4.jar CalculateTargetCoverage \
    -I &lt;input_bam_file&gt; \
    -T &lt;input_target_tsv&gt; \
    -transform PCOV \
    -groupBy SAMPLE \
    -targetInfo FULL \
    –keepdups \
    -O &lt;output_pcov_file&gt;
</pre>

<ul><li>The target file <code class="code codeInline" spellcheck="false">-T</code> is a padded intervals list of the baited regions. You can add padding to a target list using the GATK4 PadTargets tool. For our example data, padding each exome target 250bp on either side increases sensitivity.</li>
<li>Setting the <code class="code codeInline" spellcheck="false">-targetInfo</code> option to FULL keeps the original target names from the target list.</li>
<li>The <code class="code codeInline" spellcheck="false">–keepdups</code> option asks the tool to include alignments flagged as duplicate.</li>
</ul><p>The top plot shows the raw proportional coverage for our tumor sample for chromosomes 1–7. Each dot represents a target. The y-axis plots proportional coverage and the x-axis targets. The middle plot shows the data after a median-normalization and log2-transformation. The bottom plot shows the tumor data after normalization against its matched-normal.</p>

<p><a name="1_top" id="1_top"></a> </p><div style="text-align: center;"><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/8b/9ee80d16a3cc72456850ea7b6e1006.png" alt="image" class="embedImage-img importedEmbed-img"></img></div><br><a name="1_middle" id="1_middle"></a> <div style="text-align: center;"><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/67/7d8e93a0d3dadb44876e4740556def.png" alt="image" class="embedImage-img importedEmbed-img"></img></div><br><a name="1_bottom" id="1_bottom"></a> <div style="text-align: center;"><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/bc/5e0554be7652d5371f235418cc8649.png" alt="image" class="embedImage-img importedEmbed-img"></img></div>

<blockquote class="UserQuote blockquote"><div class="blockquote-content">
  <p class="blockquote-line">For each of these progressions, how certain are you that there are copy-number events? How many copy-number variants are you certain of? What is contributing to your uncertainty?</p>
</div></blockquote>

<p><a name="2" id="2"></a><br><a rel="nofollow" href="#top">back to top</a></p>

<hr></hr><h2>2. Create the CNV PoN using CombineReadCounts and CreatePanelOfNormals</h2>

<p>In this step, we use two commands to create the CNV panel of normals (PoN).</p>

<p>The normals should represent the same sequencing technology, e.g. sample preparation and capture target kit, as that of the tumor samples under scrutiny. The PoN is meant to encapsulate sequencing noise and may also capture common germline variants. Like any control, you should think carefully about what sample set would make an effective panel. At the least, the PoN should consist of ten normal samples that were ideally subject to the same batch effects as that of the tumor sample, e.g. from the same sequencing center. Our current recommendation is 40 or more normal samples. Depending on the coverage depth of samples, adjust the number.</p>

<blockquote class="UserQuote blockquote"><div class="blockquote-content">
  <p class="blockquote-line">What is better, tissue-matched normals or blood normals of tumor samples?<br>
  What makes a better background control, a matched normal sample or a panel of normals?</p>
</div></blockquote>

<p>The first step combines the proportional read counts from the multiple normal samples into a single file. The <code class="code codeInline" spellcheck="false">-inputList</code> parameter takes a file listing the relative file paths, one sample per line, of the proportional coverage data of the normals.</p>

<pre class="code codeBlock" spellcheck="false">java -jar gatk4.jar CombineReadCounts \
    -inputList normals.txt \
    -O sandbox/combined-normals.tsv
</pre>

<p>The second step creates a single CNV PoN file. The PoN stores information such as the median proportional coverage per target across the panel and projections of systematic noise calculated with PCA (principal component analysis). Our tutorial’s PoN is built with 39 normal blood samples from cancer patients from the same cohort (not suffering from blood cancers).</p>

<pre class="code codeBlock" spellcheck="false">java -jar gatk4.jar CreatePanelOfNormals \
    -I sandbox/combined-normals.tsv \
    -O sandbox/normals.pon \
    -noQC \
    --disableSpark \
    --minimumTargetFactorPercentileThreshold 5 
</pre>

<p>This results in two files, the CNV PoN and a target_weights.txt file that typical workflows can ignore. Because we have a small number of normals, we include the <code class="code codeInline" spellcheck="false">-noQC</code> option and change the <code class="code codeInline" spellcheck="false">--minimumTargetFactorPercentileThreshold</code> to 5%.</p>

<blockquote class="UserQuote blockquote"><div class="blockquote-content">
  <p class="blockquote-line">Based on what you know about <a rel="nofollow" href="http://setosa.io/ev/principal-component-analysis/">PCA</a>, what do you think are the effects of using more normal samples? A panel with some profiles that are outliers?</p>
</div></blockquote>

<p><a name="3" id="3"></a><br><a rel="nofollow" href="#top">back to top</a></p>

<hr></hr><h2>3. Normalize a raw proportional coverage profile against the PoN using NormalizeSomaticReadCounts</h2>

<p>In this step, we normalize the raw proportional coverage (PCOV) profile using the PoN. Specifically, we normalize the tumor coverage against the PoN’s target medians and against the principal components of the PoN.</p>

<pre class="code codeBlock" spellcheck="false">java -jar gatk4.jar NormalizeSomaticReadCounts \
    -I cov/tumor.tsv \
    -PON sandbox/normals.pon \
    -PTN sandbox/tumor.ptn.tsv \
    -TN sandbox/tumor.tn.tsv
</pre>

<p>This produces the pre-tangent-normalized file <code class="code codeInline" spellcheck="false">-PTN</code> and the tangent-normalized file <code class="code codeInline" spellcheck="false">-TN</code>, respectively. Resulting data is log2-transformed.</p>

<p>Denoising with a PoN is critical for calling copy-number variants from WES coverage profiles. It can also improve calls from WGS profiles that are typically more evenly distributed and subject to less noise. Furthermore, denoising with a PoN can greatly impact results for (i) samples that have more noise, e.g. those with lower coverage, lower purity or higher activity, (ii) samples lacking a matched normal and (iii) detection of smaller events that span only a few targets.</p>

<p><a name="4" id="4"></a><br><a rel="nofollow" href="#top">back to top</a></p>

<hr></hr><h2>4. Segment the normalized coverage profile using PerformSegmentation</h2>

<p>Here we segment the normalized coverage profile. Segmentation groups contiguous targets with the same copy ratio.</p>

<pre class="code codeBlock" spellcheck="false">java -jar gatk4.jar PerformSegmentation \
    -TN sandbox/tumor.tn.tsv \
    -O sandbox/tumor.seg \
    -LOG
</pre>

<p>For our tumor sample, we reduce the ~73K individual targets to 14 segments. The <code class="code codeInline" spellcheck="false">-LOG</code> parameter tells the tool that the input coverages are log2-transformed.</p>

<p>View the resulting file with <code class="code codeInline" spellcheck="false">cat sandbox/tumor.seg</code>. <br><a name="4_performsegmentation" id="4_performsegmentation"></a> </p><div style="text-align: center;"><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/a9/fdc02583a59e92ef11b7b8604bda65.png" width="360" alt="image" style="text-align: ;" class="embedImage-img importedEmbed-img"></img></div>

<blockquote class="UserQuote blockquote"><div class="blockquote-content">
  <p class="blockquote-line">Which chromosomes have events?</p>
</div></blockquote>

<p><a name="4.1"></a></p>

<h3>☞ I get an error at this step!</h3>

<p>This command will error if you have not installed R and certain R components. Take a few minutes to install R from <a rel="nofollow" href="https://www.r-project.org/">https://www.r-project.org/</a>. Then install the components with the following command.</p>

<pre class="code codeBlock" spellcheck="false">Rscript install_R_packages.R 
</pre>

<p>We include install_R_packages.R in the tutorial data bundle. Alternatively, download it from <a rel="nofollow" href="https://github.com/broadinstitute/gatk-protected/blob/master/scripts/install_R_packages.R">here</a>.</p>

<p><a name="5" id="5"></a><br><a rel="nofollow" href="#top">back to top</a></p>

<hr></hr><h2>5. (Optional) Plot segmented coverage using PlotSegmentedCopyRatio</h2>

<p>This is an optional step that plots segmented coverage.</p>

<p>This command requires <a rel="nofollow" href="https://www.xquartz.org/">XQuartz</a> installation. If you do not have this dependency, then view the results in the precomputed_results folder instead. Currently plotting only supports human assembly b37 autosomes. Going forward, this tool will accommodate other references and the workflow will support calling on sex chromosomes.</p>

<pre class="code codeBlock" spellcheck="false">java -jar gatk4.jar PlotSegmentedCopyRatio \
    -TN sandbox/tumor.tn.tsv \
    -PTN sandbox/tumor.ptn.tsv \
    -S sandbox/tumor.seg \
    -O sandbox \
    -pre tumor \
    -LOG
</pre>

<p>The <code class="code codeInline" spellcheck="false">-O</code> defines the output directory, and the <code class="code codeInline" spellcheck="false">-pre</code> defines the basename of the files. Again, the <code class="code codeInline" spellcheck="false">-LOG</code>parameter tells the tool that the inputs are log2- transformed. The output folder contains seven files--three PNG images and four text files.</p>

<p><a name="5_BaA" id="5_BaA"></a> </p><div style="text-align: center;"><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/6e/6378b446bc9560ff0263cb17cefa48.png" alt="image" class="embedImage-img importedEmbed-img"></img></div>

<ul><li>Before_After.png (shown above) plots copy-ratios pre (top) and post (bottom) tangent-normalization across the chromosomes. The plot automatically adjusts the y-axis to show all available data points. Dotted lines represent centromeres.</li>
<li>Before_After_CR_Lim_4.png shows the same but fixes the y-axis range from 0 to 4 for comparability across samples.</li>
<li>FullGenome.png colors differential copy-ratio segments in alternating blue and orange. The horizontal line plots the segment mean. Again the y-axis ranges from 0 to 4.</li>
</ul><blockquote class="UserQuote blockquote"><div class="blockquote-content">
  <p class="blockquote-line">Open each of these images. How many copy-number variants do you see?</p>
</div></blockquote>

<p><a name="5.1"></a></p>

<h3>☞ What is the QC value?</h3>

<p>Each of the four text files contain a single quality control (QC) value. This value is the median of absolute differences (MAD) in copy-ratios of adjacent targets. Its calculation is robust to actual copy-number variants and should decrease post tangent-normalization.</p>

<ul><li>preQc.txt gives the QC value before tangent-normalization.</li>
<li>postQc.txt gives the post-tangent-normalization QC value.</li>
<li>dQc.txt gives the difference between pre and post QC values.</li>
<li>scaled_dQc.txt gives the fraction difference (preQc - postQc)/(preQc).</li>
</ul><p><a name="6" id="6"></a><br><a rel="nofollow" href="#top">back to top</a></p>

<hr></hr><h2>6. Call segmented copy number variants using CallSegments</h2>

<p>In this final step, we call segmented copy number variants. The tool makes one of three calls for each segment--neutral (0), deletion (-) or amplification (+). These deleted or amplified segments could represent somatic events.</p>

<pre class="code codeBlock" spellcheck="false">java -jar gatk4.jar CallSegments \
    -TN sandbox/tumor.tn.tsv \
    -S sandbox/tumor.seg \
    -O sandbox/tumor.called
</pre>

<p>View the results with cat sandbox/tumor.called.</p>

<p><a name="6_callsegments" id="6_callsegments"></a> </p><div style="text-align: center;"><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/d4/b00e28a883bf5702e9a436d11f3ce2.png" width="420" alt="image" style="text-align: ;" class="embedImage-img importedEmbed-img"></img></div>

<blockquote class="UserQuote blockquote"><div class="blockquote-content">
  <p class="blockquote-line">Besides the last column, how is this result different from that of step 4?</p>
</div></blockquote>

<p><a name="7" id="7"></a><br><a rel="nofollow" href="#top">back to top</a></p>

<hr></hr><h2>7. Discussion of interest to some</h2>

<p><a name="7.1"></a></p>

<h3>☞ Why can't I use just a matched normal?</h3>

<p>Let’s compare results from the raw coverage (top), from normalizing using the matched-normal only (middle) and from normalizing using the PoN (bottom).</p>

<p><a name="7_raw" id="7_raw"></a> </p><div style="text-align: center;"><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/28/e75857322a5c9e9a2c2d2d045f8ce5.png" alt="image" class="embedImage-img importedEmbed-img"></img></div><br><a name="7_matched" id="7_matched"></a> <div style="text-align: center;"><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/d0/425bc2c5d7251bcc7195d1c758daae.png" alt="image" class="embedImage-img importedEmbed-img"></img></div><br><a name="7_pon" id="7_pon"></a> <div style="text-align: center;"><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/58/15ca12b0618250b498391fe5562fb9.png" alt="image" class="embedImage-img importedEmbed-img"></img></div>

<blockquote class="UserQuote blockquote"><div class="blockquote-content">
  <p class="blockquote-line">What is different between the plots? Look closely.</p>
</div></blockquote>

<p><a name="7.2"></a></p>

<p>The matched-normal normalization appears to perform well. However, its noisiness brings uncertainty to any call that would be made, even if visible by eye. Furthermore, its level of noise obscures detection of the 4th variant that the PoN normalization reveals.</p>

<h3>☞How do the results compare to SNP6 analyses?</h3>

<p>As with any algorithmic analysis, it’s good to confirm results with orthogonal methods. If we compare calls from the original unscrambled tumor data against GISTIC SNP6 array analysis of the same sample, we similarly find <em>three</em> deletions and a single large amplification.</p>

<p><a rel="nofollow" href="#top">back to top</a></p>

<hr></hr>
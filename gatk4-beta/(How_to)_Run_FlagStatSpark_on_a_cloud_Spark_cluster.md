## (How to) Run FlagStatSpark on a cloud Spark cluster

By shlee

<h4>Document is in <code class="code codeInline" spellcheck="false">BETA</code>. It may be incomplete and/or inaccurate. Post suggestions to the <em>Comments</em> section and be sure to read about updates also within the <em>Comments</em> section.</h4>

<ul><li>For a concise tutorial just on setting up Dataproc, see <a rel="nofollow" href="https://software.broadinstitute.org/gatk/documentation/article?id=11093">Tutorial#11093</a>.</li>
<li>It may take a minute or a browser refresh for images to load. If an image appears broken, click on it to view in a separate tab.</li>
</ul><hr></hr><p><a rel="nofollow" href="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/tutorial10060_flowchart_shlee.png"><img src="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/tutorial10060_flowchart_shlee.png" width="540" alt="image" style="float: right;" class="embedImage-img importedEmbed-img"></img></a> This tutorial outlines the most straightforward way to run a GATK4 Spark tool using Google Cloud’s Dataproc. We cover how to spin up a Dataproc cluster via a browser (<strong>section 1</strong>) and also via a <em>gcloud</em> command (<strong>section 3</strong>). We use GATK4 FlagStatSpark as an example tool to survey SAM flags on a tiny 53MB BAM file (<strong>section 2</strong>) and on an entire 12.21GB exome BAM file (<strong>section 4</strong>). Both files are made available in a publicly accessible Google Cloud storage bucket. We conclude by examining the impact of adjusting some Spark parameters on job run times (<strong>section 5</strong>).</p>

<p>First things first. This tutorial costs about fourteen cents <sup><a rel="nofollow" href="#a">a</a></sup>.</p>

<p><a rel="nofollow" href="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/what_does_it_cost.png"><div style="text-align: center;"><img src="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/what_does_it_cost.png" width="450" alt="image" style="text-align: ;" class="embedImage-img importedEmbed-img"></img></div></a></p>

<p>Second, we remind you that GATK4 is in BETA as of this writing. Spark capabilities are still under active development and have yet to become full-featured across the tools. We write at this time to enable use of the spark capability of GermlineCNVCaller, a tool for germline copy number variant (CNV) detection that also runs in single-machine mode.</p>

<p><a name="top" id="top"></a></p>

<hr></hr><h3>Jump to a section</h3>

<ol><li><a rel="nofollow" href="#1">Spin up a Dataproc cluster via a browser</a><br>
    ☞ <a rel="nofollow" href="#1.1"><em>How can I ssh into a cluster’s node?</em></a></li>
<li><a rel="nofollow" href="#2">Run GATK4 FlagStatSpark on a small bam in the cloud</a><br>
    ☞ <a rel="nofollow" href="#2.1"><em>How is FlagStatSpark different from the non-Spark FlagStat?</em></a><br>
    ☞ <a rel="nofollow" href="#2.2"><em>How is GATK4 FlagStatSpark and FlagStat different from Samtools flagstat?</em></a></li>
<li><a rel="nofollow" href="#3">Spin up a Dataproc cluster using a gcloud command</a></li>
<li><a rel="nofollow" href="#4">Run GATK4 FlagStatSpark on a WES bam in the cloud</a></li>
<li><a rel="nofollow" href="#5">Some discussion on Spark parameters to optimize runs</a><br>
    ☞ <a rel="nofollow" href="#5.1"><em>What is jar caching and how do I enable it?</em></a><br>
    ☞ <a rel="nofollow" href="#5.2"><em>How can I script this process?</em></a></li>
<li><a rel="nofollow" href="#6">Related resources</a></li>
</ol><hr></hr><h3>Tools involved</h3>

<p>Prepare the following to be able to actively follow along. If you need more detailed instructions, search the GATK forum or Google Cloud documentation.</p>

<ul><li>Install <a rel="nofollow" href="https://github.com/broadinstitute/gatk/releases">the latest GATK4 release</a>. This tutorial uses GATK4.beta.2. For quick installation instructions, see <a rel="nofollow" href="https://software.broadinstitute.org/gatk/documentation/article.php?id=9881">Article#9881</a>.</li>
<li><p>A Google Cloud billing project with API enabled <sup><a rel="nofollow" href="#a">a</a></sup>. Link your billing account to your project at <em>Menu</em> &gt; <em>Billing</em> &gt; <em>Overview</em>. <br><a rel="nofollow" href="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/gcs_console_home.png"><img src="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/gcs_console_home.png" width="270" alt="image" style="float: right;" class="embedImage-img importedEmbed-img"></img></a></p></li>
<li><p>Install <a rel="nofollow" href="https://cloud.google.com/sdk/downloads">Google Cloud SDK</a>. This program requires Python v2.7.x, as well as Java v1.7+, which you should already have because GATK4 requires <em>Java v1.8</em>. Set your Cloud SDK to use the billing project by typing <code class="code codeInline" spellcheck="false">gcloud init</code> into your bash command line. Follow the directions for configuration. Be mindful in setting the region and zone <sup><a rel="nofollow" href="#b">b</a></sup>,  and be sure to enable API compute. View the configuration with <code class="code codeInline" spellcheck="false">gcloud config list</code>.</p></li>
<li>(Optional) For local Spark runs, be sure to install Spark from <a rel="nofollow" href="http://spark.apache.org/">http://spark.apache.org/</a>.</li>
</ul><hr></hr><h3>Example data</h3>

<p>Again, the data files are in Google Cloud Storage, as <a rel="nofollow" href="https://cloud.google.com/storage/docs/managing-buckets?hl=en_US&amp;_ga=1.260034435.84400892.1488925957#manage-class"><em>Multi-Regional</em> data accessible globally</a>. The point of this tutorial is that you need not download any large data. We keep the BAMs in the cloud and take our analysis to the cloud. Here are the cloud locations of the tutorial BAM files. FlagStat needs only the BAM.</p>

<p><strong>53MB snippet from <a rel="nofollow" href="https://gatkforums.broadinstitute.org/gatk/discussion/6484">Tutorial#6484</a></strong>. This particular dataset is also downloadable from our FTP site.</p>

<ul><li>gs://gatk-tutorials/how-to/6484_snippet.bam</li>
<li>gs://gatk-tutorials/how-to/6484_snippet.bai</li>
</ul><p><strong>12.21GB WES data from 1000 Genomes Project aligned to GRCh38DH</strong>. The 1000 Genomes Project provides GRCh37-aligned files at gs://genomics-public-data/1000-genomes/bam. The GRCh38 equivalent files are currently only available in CRAM format from the project’s FTP site and have known issues detailed in the site README. Because CRAM support is yet to become widely available for GATK4 tools, for the purposes of this tutorial we provide the decompressed BAM at:</p>

<ul><li>gs://gatk-test-data/exome_bam/1000G_wex_hg38/HG00133.alt_bwamem_GRCh38DH.20150826.GBR.exome.bam</li>
<li>gs://gatk-test-data/exome_bam/1000G_wex_hg38/HG00133.alt_bwamem_GRCh38DH.20150826.GBR.exome.bai</li>
</ul><p>Check if you can access these files with the following command.</p>

<pre class="code codeBlock" spellcheck="false">gsutil ls -lr gs://gatk-test-data/exome_bam/1000G_wex_hg38/HG00133.alt_bwamem_GRCh38DH.20150826.GBR.exome.bam
</pre>

<p>You should see the size of the BAM listed at roughly 12.21GB.</p>

<p><a rel="nofollow" href="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/probe_exome_bam_newlocation.png"><div style="text-align: center;"><img src="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/probe_exome_bam_newlocation.png" width="" alt="image" style="text-align: ;" class="embedImage-img importedEmbed-img"></img></div></a></p>

<p><a name="1" id="1"></a></p>

<hr></hr><h2>1. Spin up a Dataproc cluster via a browser</h2>

<p>You can either spin up a cluster (i) via the command line using <em>gcloud</em> or (ii) use a browser to click through Google Cloud Platform’s Dataproc form. Given the second option shows the possible parameters, it is useful to learn how to navigate. Let’s start with this latter approach. <a rel="nofollow" href="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/navigate_to_dataproc.png"><img src="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/navigate_to_dataproc.png" width="230" alt="image" style="float: right;" class="embedImage-img importedEmbed-img"></img></a></p>

<p>In the browser, from your Google Cloud console, click on the main menu’s triple-bar icon that looks like an abstract hamburger in the upper-left corner. Navigate to <em>Menu</em> &gt; <em>Dataproc</em> &gt; <em>Clusters</em>.</p>

<blockquote class="UserQuote blockquote"><div class="blockquote-content">
  <p class="blockquote-line">If this is the first time you land here, then click the <code class="code codeInline" spellcheck="false">Enable API</code> button and wait a few minutes as it enables.</p>
</div></blockquote>

<p>Fill out the cluster creation form.</p>

<ul><li>Name the cluster something you can easily remember. The name must be all lowercase, start and end with a letter and contain no spaces nor special characters except for dashes. Cluster names within a project must be unique and it’s possible to reuse the names of deleted clusters. I name mine <em>parsley</em> because I am growing parsley this summer. <a rel="nofollow" href="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/create_a_cluster.png"><img src="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/create_a_cluster.png" width="220" alt="image" style="float: right;" class="embedImage-img importedEmbed-img"></img></a></li>
<li>Leave the <code class="code codeInline" spellcheck="false">Region</code> setting to the default <code class="code codeInline" spellcheck="false">global</code> when creating Dataproc clusters, unless your job launches, data and clusters all reside within the same region. Then set it to the specific region <sup><a rel="nofollow" href="#b">b</a></sup>. Set the <code class="code codeInline" spellcheck="false">Zone</code> to match what you have set for your Cloud SDK, which presumably in turn matches where most of your data resides.</li>
<li>Set <em>Master</em> and <em>Worker</em> node properties. For the purposes of this exercise, we use the most minimal settings possible for each option. Select <code class="code codeInline" spellcheck="false">n1-standard-1 (1 vCPU, 3.75 GB memory)</code> and 10GB disk size for both the master and the worker nodes. We use the default minimum two worker nodes. For tools such as GermlineCNVCaller, you will want to use more resources, e.g select <code class="code codeInline" spellcheck="false">n1-standard-16 (16 vCPU, 60.0 GB memory)</code> for both master and worker nodes and ten worker nodes for 200 to 400 samples.</li>
<li>The <em>advanced options</em> are accordioned under <em>Preemptible workers, bucket, network, version, initialization, &amp; access options</em>. Under this section, <code class="code codeInline" spellcheck="false">Image Version</code> changes the Dataproc version, which by default is set to the latest stable version. As of this writing, the default <code class="code codeInline" spellcheck="false">Image Version</code> is v1.1. Selecting another version, e.g. an earlier version, may cause the jobs in this tutorial to error. Of note is the option <code class="code codeInline" spellcheck="false">Initialization actions</code>. This argument allows you to run your own script(s), stored in a google cloud bucket, e.g. to install programs on the nodes.</li>
</ul><p><a rel="nofollow" href="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/parsley_cluster.png"><img src="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/parsley_cluster.png" width="450" alt="image" style="float: left;" class="embedImage-img importedEmbed-img"></img></a> <br>
It will take a few minutes for the cluster to provision. In this view, so long as the status of this VM is <em>Running</em>, it is incurring fees <sup><a rel="nofollow" href="#c">c</a></sup>.</p>

<p>View the individual cluster nodes under <em>Menu</em> &gt; <em>Compute Engine</em> &gt; <em>VM instances</em>. If there are many VMs, type the name of the cluster into the textbox to filter for specific cluster VMs. Each of the master and worker nodes list separately, e.g. parsley-m, parsley-w-0 and parsley-w1, where <code class="code codeInline" spellcheck="false">m</code> stands for master and <code class="code codeInline" spellcheck="false">w</code> stands for worker. <a rel="nofollow" href="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/parsley_vms.png"><div style="text-align: center;"><img src="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/parsley_vms.png" width="450" alt="image" style="text-align: ;" class="embedImage-img importedEmbed-img"></img></div></a> <br><a name="1.1"></a></p>

<hr></hr><h3>☞ How can I ssh into a cluster’s node?</h3>

<p>Now here is something handy. See the <em>SSH</em> menu under <em>Connect</em>? Select this and then select <em>View gcloud command</em>. Copy and paste this command into bash. You may need to click the right-hand <em>HIDE INFO PANEL</em> option to see the column.</p>

<pre class="code codeBlock" spellcheck="false">gcloud compute --project "broad-dsde-dev" ssh --zone "us-central1-a" "parsley-m"
</pre>

<p>This is basically akin to ssh'ing into a server. If we ssh into the master node, we see the following message and prompt.<br><a rel="nofollow" href="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/ssh.png"><div style="text-align: center;"><img src="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/ssh.png" width="" alt="image" style="text-align: ;" class="embedImage-img importedEmbed-img"></img></div></a></p>

<p>The message we see is specific to the latest stable image version (v1.1) at the time of this writing. We can check the java version to see the node is using OpenJDK v1.8.0_131.<br><a rel="nofollow" href="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/ssh_java.png"><div style="text-align: center;"><img src="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/ssh_java.png" width="" alt="image" style="text-align: ;" class="embedImage-img importedEmbed-img"></img></div></a></p>

<p>Another way to interact with the clusters, that this tutorial does not cover and that we recommend exploring, is to use it to monitor the job progress from the cluster’s Hadoop application. The application creates an HTML report that is viewable from a local browser. This requires creating a tunnel to the cloud VM. See instructions at <a href="https://cloud.google.com/dataproc/docs/concepts/cluster-web-interfaces" rel="nofollow">https://cloud.google.com/dataproc/docs/concepts/cluster-web-interfaces</a>. Similarly, and this is a digression, we could view BAM files with IGV <sup><a rel="nofollow" href="#d">d</a></sup>.<br><a name="2" id="2"></a><br><a rel="nofollow" href="#top">back to top</a></p>

<hr></hr><h2>2. Run GATK4 FlagStatSpark on a small bam in the cloud</h2>

<p>For runs that you expect to take a long time, be sure to enable TMUX, Screen or equivalent terminal multiplexer in your bash session.</p>

<p>Type following command to run FlagStatSpark on a publically accessible cloud BAM. For an introduction to SAM flags, see <a rel="nofollow" href="https://gatkforums.broadinstitute.org/gatk/discussion/7019/">Blog#7019</a>.</p>

<pre class="code codeBlock" spellcheck="false">gatk-launch FlagStatSpark \
    -I gs://gatk-tutorials/how-to/6484_snippet.bam \
    --disableReadFilter WellformedReadFilter \
    -- --sparkRunner GCS --cluster parsley 
</pre>

<p>The first half of the command refers to tool-specific arguments and the second half to Spark-specific arguments. The command separates these sections with the standalone double-dashes <code class="code codeInline" spellcheck="false">--</code>.</p>

<ul><li>Provide the input BAM’s cloud URL with <code class="code codeInline" spellcheck="false">-I</code> and disable the <code class="code codeInline" spellcheck="false">WellformedReadFilter</code> read filter (more about this later).</li>
<li>Specify the Spark cluster provider with <code class="code codeInline" spellcheck="false">--sparkRunner GCS</code>, which refers to <em>Google Cloud Service</em>.</li>
<li>Provide the cluster name, e.g. <code class="code codeInline" spellcheck="false">--cluster parsley</code>.</li>
</ul><p>FlagStatSpark writes results to standard output (stdout), directly to the bash session. Amongst other standard messages, the SAM flag counts of interest are:</p>

<p><a rel="nofollow" href="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/flagstatspark_snippet_results.png"><div style="text-align: center;"><img src="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/flagstatspark_snippet_results.png" width="" alt="image" style="text-align: ;" class="embedImage-img importedEmbed-img"></img></div></a></p>

<p>We also see the run completes in less than a minute. In the next sections, we create and use a new cluster that is identical in specs to <em>parsley</em>. You can opt to skip <strong>section 3</strong> and to run <strong>section 4</strong>'s command on <em>parsley</em>. Otherwise, click on the cluster from <em>Menu</em>&gt;<em>Dataproc</em>&gt;<em>Clusters</em> and click on <em>Delete</em> to delete it.</p>

<p><a name="2.1"></a></p>

<hr></hr><h3>☞ How is FlagStatSpark different from the non-Spark FlagStat?</h3>

<p>As the name implies, FlagStatSpark is the Spark version of FlagStat. Many GATK4 tools, if Spark-enabled, contain <em>Spark</em> within the tool name. However, there are a number of tool names that lack the Spark label that are in fact Spark-capable. See <a rel="nofollow" href="https://software.broadinstitute.org/gatk/documentation/tooldocs/4.beta-latest">tool documentation</a> for details.</p>

<p>On my local computer, if I run either FlagStatSpark or FlagStat on the same data, I will get the same counts. However, the stdouts are noticeably different. The Spark version of the tool automatically runs on my laptop using available cores. Both tools run quickly, in a matter of seconds. For more details on how to tweak a local Spark run, see <a href="https://github.com/broadinstitute/gatk#sparkcluster" rel="nofollow">https://github.com/broadinstitute/gatk#sparkcluster</a>.</p>

<p><a name="”2.2”"></a></p>

<hr></hr><h3>☞ How is GATK4 FlagStatSpark and FlagStat different from Samtools flagstat?</h3>

<p>This GATK implementation of <strong>Samtools flagstat</strong> can give different results from the original tool. To get equivalent results, we must disable the <code class="code codeInline" spellcheck="false">WellformedReadFilter</code> that the GATK4 Engine uses to remove what it considers <em>malformed</em> reads. For details on filters, see <a rel="nofollow" href="https://software.broadinstitute.org/gatk/documentation/tooldocs/4.beta-latest">Read Filters documentation</a>.  GATK4 employs the <code class="code codeInline" spellcheck="false">WellformedReadFilter</code> for FlagStat because most BAM processing tools use this filter. It is practical for GATK4 FlagStat to survey SAM flags only for reads that GATK4 tools actually consider.<br><a name="3" id="3"></a><br><a rel="nofollow" href="#top">back to top</a></p>

<hr></hr><h2>3. Spin up a Dataproc cluster using a gcloud command</h2>

<p>Instead of clicking through a GUI on the browser to generate a cluster, we can use the <em>gcloud</em> program to generate clusters via the bash command line.</p>

<p>Here is the command that creates a new cluster we call <code class="code codeInline" spellcheck="false">basil</code> that is identical in specifications to <code class="code codeInline" spellcheck="false">parsley</code>. Be sure to fill in the <code class="code codeInline" spellcheck="false">project_id</code>.</p>

<pre class="code codeBlock" spellcheck="false">gcloud dataproc clusters create basil \
    --zone us-central1-a \
    --master-machine-type n1-standard-1 \
    --master-boot-disk-size 10 \
    --num-workers 2 \
    --worker-machine-type n1-standard-1 \
    --worker-boot-disk-size 10 \
    --project project_id
</pre>

<p>Again, we are careful to match our <code class="code codeInline" spellcheck="false">--zone</code> to that of our local Cloud SDK setup, e.g. in my case <code class="code codeInline" spellcheck="false">us-central1-a</code>. By omitting the <code class="code codeInline" spellcheck="false">--region</code> argument, Dataproc sets it to the default <code class="code codeInline" spellcheck="false">global</code>. Refresh the browser to see the cluster appear in the console, or use <code class="code codeInline" spellcheck="false">gcloud dataproc clusters list</code>.</p>

<p><a name="4" id="4"></a><br><a rel="nofollow" href="#top">back to top</a></p>

<hr></hr><h2>4.  Run GATK4 FlagStatSpark on a large BAM in the cloud</h2>

<p>Next, we run FlagStatSpark on a 12.21GB <em>1000 Genomes Project</em> WES BAM using the cluster <code class="code codeInline" spellcheck="false">basil</code>. The approach we are taking to run FlagStatSpark, using Dataproc, directly distributes the BAM record data to worker nodes. Spark distributes the data across the worker nodes in partitions called <em>Resilient Distributed Datasets</em> or RDDs. The cluster’s two worker nodes sum to 20GB is disk space and happen to be able to hold the size of the WES BAM’s data. However, for our case, this is not a necessary requirement <sup><a rel="nofollow" href="#e">e</a></sup>.</p>

<p>The FlagStatSpark command is pretty much the same as that in <strong>section 2</strong>, except we use a different cloud BAM and the new cluster.</p>

<pre class="code codeBlock" spellcheck="false">gatk-launch FlagStatSpark \
    -I gs://gatk-test-data/exome_bam/1000G_wex_hg38/HG00133.alt_bwamem_GRCh38DH.20150826.GBR.exome.bam \
    --disableReadFilter WellformedReadFilter \
    -- --sparkRunner GCS --cluster basil
</pre>

<p>Results show the following flag counts and a roughly 11.5 minute runtime.<br><a rel="nofollow" href="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/flagstatspark_wes_results.png"><div style="text-align: center;"><img src="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/flagstatspark_wes_results.png" width="" alt="image" style="text-align: ;" class="embedImage-img importedEmbed-img"></img></div></a></p>

<p>We can view each node’s use of <em>CPU</em>, <em>disk</em>, <em>disk operations</em>, <em>network</em> and <em>network packets</em> under the console’s <em>Compute Engine</em> by clicking on the node. Similarly, we can see the same for the entire job under <em>Dataproc</em> by clicking on the cluster.</p>

<p>When we view <code class="code codeInline" spellcheck="false">basil</code>’s resource utilization charts, we observe something counterintuitive. Based on CPU utilization, it appears that one worker, w-0, performs most of the work, while the other worker, w-1, is mostly inactive. This comes as a surprise, because we expect some optimized distribution and throughput for the work, namely even distribution for shorter runtimes, given we enable <a rel="nofollow" href="https://spark.apache.org/docs/latest/job-scheduling.html#dynamic-resource-allocation"><em>dynamic allocation</em></a> by omitting executor options <sup><a rel="nofollow" href="#f">f</a></sup>. If we increase the number of worker nodes, we see work distributes more equitably across all but one worker. We still observe the single worker that carries a minimal load. We discuss how to remedy such apparent lazy workers in <strong>section 5</strong>.</p>

<p><a rel="nofollow" href="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/wes_n1standard1_cpu_disk.png"><div style="text-align: center;"><img src="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/wes_n1standard1_cpu_disk.png" width="540" alt="image" style="text-align: ;" class="embedImage-img importedEmbed-img"></img></div></a></p>

<p>Finally, clean up so as not to incur unnecessary charges. Delete clusters as well as any log and staging directories the job creates in your storage bucket <sup><a rel="nofollow" href="#g">g</a></sup>. In the browser, toggle the box next to the cluster name and select <em>Delete</em>. From the command line use</p>

<pre class="code codeBlock" spellcheck="false">gcloud dataproc clusters delete basil
</pre>

<p><a name="5" id="5"></a><br><a rel="nofollow" href="#top">back to top</a></p>

<hr></hr><h2>5. Some discussion on Spark parameters to optimize runs</h2>

<p>So far, this tutorial familiarizes you to spinning up a Dataproc cluster and running a GATK4 Spark tool. It does not showcase Spark capabilities.</p>

<p>Multiple factors impact the performance of tools on Spark and these factors vary for different tools and data, e.g. a tool that counts versus performs algorithmic functions, network connections and the size and location of data. The appeal of Spark is in the ability of workers to each carry forward transformed data to the next analysis without having to read or write the intermediary data from and to files. GATK4 offers a number of pipelines, e.g. BQSRPipelineSpark, and running a pipeline should provide improvements to wall-clock performance compared to a pipeline consisting of the equivalent individual tools, whether Spark or non-Spark. Similarly, GATK4’s <a rel="nofollow" href="https://software.broadinstitute.org/gatk/documentation/tooldocs/4.beta-latest/org_broadinstitute_hellbender_tools_coveragemodel_germline_GermlineCNVCaller.php">GermlineCNVCaller</a> is a tool that in actuality is a pipeline that mirrors the functionality of the multiple tools in the Somatic CNV workflow outlined in <a rel="nofollow" href="https://gatkforums.broadinstitute.org/gatk/discussion/9143/">Tutorial#9143</a>.</p>

<p>We can amend our FlagStatSpark command to specify the following Spark components related to <em>executors</em>. A Spark executor runs tasks. It can run tasks multiply on two fronts--(i) over its lifetime and (ii) concurrently (see <a rel="nofollow" href="http://blog.cloudera.com/blog/2014/05/apache-spark-resource-management-and-yarn-app-models/">this Cloudera blogpost</a>). A worker node can have multiple executors and these executors can share cores via task switching.</p>

<ul><li><code class="code codeInline" spellcheck="false">--executor-memory</code> controls the executor heap size</li>
<li><code class="code codeInline" spellcheck="false">--num-executors</code> specifies the total number of executors for the cluster</li>
<li><code class="code codeInline" spellcheck="false">--executor-cores</code> is the number of cores per executor</li>
</ul><p>When we specify the <code class="code codeInline" spellcheck="false">--num-executors</code> argument for a GATK Spark tool, we turn off the default <em>dynamic allocation</em> <sup><a rel="nofollow" href="#f">f</a></sup>.</p>

<p>We can tune the executor parameters to a combination that hits a sweet spot of efficient cluster utilization for our particular data. In testing combinations, we follow guidelines from <a rel="nofollow" href="https://blog.cloudera.com/blog/2015/03/how-to-tune-your-apache-spark-jobs-part-2/">a Cloudera blogpost by Sandy Ryza</a> that gives a rule-of-thumb recommendation and that illustrates with bad and good allocations.<br><a rel="nofollow" href="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/wes_increase_executors_chart.png"><div style="text-align: center;"><img src="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/wes_increase_executors_chart.png" width="300" alt="image" style="text-align: ;" class="embedImage-img importedEmbed-img"></img></div></a></p>

<p>We start with identical cluster setups that use <code class="code codeInline" spellcheck="false">n1-standard-4</code>, 15GB nodes for the master and two workers. We vary the three executor parameters, and find we can reduce the wallclock time of running FlagStatSpark on the WES data by over half, from ~6 minutes with dynamic allocation to roughly two and a half minutes with six or more executors. Note that runtimes are subject to some amount of variance, so the same command run at different times can complete with different times. The exact cluster parameters for the data points in the chart above are in the table below.<br><a rel="nofollow" href="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/wes_increase_executors_table.png"><div style="text-align: center;"><img src="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/wes_increase_executors_table.png" width="540" alt="image" style="text-align: ;" class="embedImage-img importedEmbed-img"></img></div></a></p>

<p>For the datapoint at seven executors, the corresponding FlagStatSpark command is:</p>

<pre class="code codeBlock" spellcheck="false">gatk-launch FlagStatSpark \
    -I gs://gatk-test-data/exome_bam/1000G_wex_hg38/HG00133.alt_bwamem_GRCh38DH.20150826.GBR.exome.bam \
    --disableReadFilter WellformedReadFilter \
    -- --sparkRunner GCS --cluster parsley \
    --num-executors 7 \
    --executor-memory 4G \
    --executor-cores 3
</pre>

<p>If we peruse the CPU utilization charts for these jobs, we see that the two workers carryout more equitable workloads. The comparison below shows CPU and I/O utilization by FlagStatSpark on the WES BAM for <em>dynamically allocated</em> versus <em>seven</em> executors. <br><a rel="nofollow" href="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/wes_n1standard4_cpu_disk_dynamic.png"><div style="text-align: center;"><img src="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/wes_n1standard4_cpu_disk_dynamic.png" width="720" alt="image" style="text-align: ;" class="embedImage-img importedEmbed-img"></img></div></a></p>

<p>Finally, we can test the effect of increasing the number of worker nodes, while keeping the number of executors proportional, by running FlagStatSpark on a ~113GB WGS BAM <sup><a rel="nofollow" href="#e">e</a></sup>. Here we see diminishing returns after a certain number of executors, i.e. forty and greater. <br><a rel="nofollow" href="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/wgs_increase_workers.png"><div style="text-align: center;"><img src="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/wgs_increase_workers.png" width="300" alt="image" style="text-align: ;" class="embedImage-img importedEmbed-img"></img></div></a></p>

<p><a name="5.1"></a></p>

<hr></hr><h3>☞ What is jar caching and how do I enable it?</h3>

<p>See <a rel="nofollow" href="https://software.broadinstitute.org/gatk/documentation/article?id=10105">Dictionary Article#10105</a>.</p>

<p><a name="5.2"></a></p>

<hr></hr><h3>☞ How can I script this process?</h3>

<p>The simplest approach is to use a bash script. Run the script locally for authentication purposes.</p>

<p>Here is a simple interactive script that spins up a cluster, runs FlagStatSpark on a user-specified cloud BAM and cleans up afterwards by deleting the cluster: <a rel="nofollow" href="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/gatk4_flagstatspark_dataproc.sh">gatk4_flagstatspark_dataproc.txt</a>. This script cannot run on the free trial account <sup><a rel="nofollow" href="#a">a</a></sup>. Download and save with <code class="code codeInline" spellcheck="false">.sh</code> extension. Navigate to the directory the script is saved in and run it by typing</p>

<pre class="code codeBlock" spellcheck="false">./gatk4_flagstatspark_dataproc.sh
</pre>

<p>If you have problems running the script, you may need to change the ownership with</p>

<pre class="code codeBlock" spellcheck="false">chmod u+x gatk4_flagstatspark_dataproc.sh 
</pre>

<p>Here is what the script contains.<br><a rel="nofollow" href="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/flagstatspark_bashscript.png"><div style="text-align: center;"><img src="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10060_figures/flagstatspark_bashscript.png" width="" alt="image" style="text-align: ;" class="embedImage-img importedEmbed-img"></img></div></a></p>

<p><a name="6" id="6"></a><br><a rel="nofollow" href="#top">back to top</a></p>

<hr></hr><h2>6. Related notes and resources</h2>

<p><a name="a" id="a"></a>[a] As of this writing (July 2017), there is a <a rel="nofollow" href="https://console.cloud.google.com/freetrial?_ga=1.103752329.84400892.1488925957">free Google Cloud Platform trial that gives you $300 in credit</a> that is good for a year. I signed up in a few minutes for the explicit purpose of getting <a rel="nofollow" href="https://cloud.google.com/dataproc/docs/resources/pricing">a sense of pricing</a>. Because the account is free, it has limitations, namely we can only have eight CPU cores. For a minimal Dataproc cluster consisting of three identical nodes, this means we can only use two of the offered machine types. This is inadequate to demonstrate the efficiency of Spark, and so in <strong>section 5</strong> we plot run times from some other setups by switching to a non-trial account.</p>

<p><a name="b" id="b"></a>[b] The <a rel="nofollow" href="https://cloud.google.com/compute/docs/regions-zones/regions-zones"><code class="code codeInline" spellcheck="false">Region</code> and <code class="code codeInline" spellcheck="false">Zone</code> settings are important</a> and can be a bit tricky. Check what these are set to with <code class="code codeInline" spellcheck="false">gcloud config list compute/region</code> and <code class="code codeInline" spellcheck="false">gcloud config list compute/zone</code>. If you haven’t set these, you can either choose to forge ahead without setting these or set them by initializing with <code class="code codeInline" spellcheck="false">gcloud init</code>. You can change these directly, e.g. with <code class="code codeInline" spellcheck="false">gcloud config set compute/zone us-central1-a</code> and <code class="code codeInline" spellcheck="false">gcloud config set compute/region global</code>.</p>

<ul><li>For Dataproc cluster creation, selecting a region limits the zones you can choose from. In this context, <code class="code codeInline" spellcheck="false">Region</code> refers to regions from which you can <em>control the cluster</em>, i.e. run jobs. Because we write this from the U.S. East Coast, from Boston, but our cluster resides in the Midwest (in <code class="code codeInline" spellcheck="false">us-central1</code>), we keep the <code class="code codeInline" spellcheck="false">Region</code> setting to <code class="code codeInline" spellcheck="false">global</code>. Otherwise, our job submissions will error about non-extant clusters, e.g. <br><code class="code codeInline" spellcheck="false">ERROR: (gcloud.dataproc.jobs.submit.spark) NOT_FOUND: No current cluster for project id 'XYZ' with name 'ABC'</code><br>
Conceptually, regions are made up of zones and so when we specify the <code class="code codeInline" spellcheck="false">Zone</code>, we are in fact also implying a region, which is what can make setting these two parameters confusing. To reiterate, leave the <code class="code codeInline" spellcheck="false">Region</code> setting to the default <code class="code codeInline" spellcheck="false">global</code> when creating Dataproc clusters, unless your job launches, data and clusters all reside within the same region. Then set it to the specific region. Be sure to set the <code class="code codeInline" spellcheck="false">Zone</code> to match what you have set for your Cloud SDK, which presumably in turn matches where most of your data resides. Accessing data within a zone is free while accessing data across zones incurs a fee, e.g. one cent per egress. See <a href="https://cloud.google.com/compute/pricing" rel="nofollow">https://cloud.google.com/compute/pricing</a>.</li>
</ul><p><a name="c" id="c"></a>[c] As of this writing, <a rel="nofollow" href="https://cloud.google.com/dataproc/docs/resources/pricing">Dataproc rounds billing up to ten minutes if you use less, and then bills by the minute</a>.</p>

<p><a name="d" id="d"></a>[d] For viewing BAM files, SSH to install IGV on the VM, use IGV where the data resides and view by tunneling. Transmitting imagery through the tunnel expends less compute than transmitting read data across a network.</p>

<p><a name="e" id="e"></a>[e] The worker nodes do not necessarily have to hold all the starting data at the beginning. The job can run by streaming the data as it runs, i.e. it can rely on deliveries of data packets as it processes the data. FlagStat goes through BAM records to count SAM flags, which is a process that requires minimal compute. Furthermore, the tool need not write a large BAM, and needs only to write a small table of counts. So we can skip factoring for disk space to write results. We can in fact run FlagStatSpark on a 113GB WGS BAM on a cluster whose worker node disks sum to a fraction of the size without issue, e.g. where workers sum to 30GB of disk. If we use the <em>n1-standard-4</em> machine for two 15GB disk workers and the seven executor setup from <strong>section 5</strong>, FlagStatSpark completes processing the WGS BAM in roughly 28 minutes. If we normalize the WGS runtime against the number of SAM records it contains compared to the WES data, its FlagStatSpark runtime takes 1.61 times longer. If we normalize using the sizes of the BAM file, a rough heuristic to factor for length of reads that assumes similar complexity of base quality profiles, then the WGS runtime takes 1.39 times longer. We conjecture this lag results from the additional time spent on ferrying over data as well as on clearing processed data.</p>

<p><a name="f" id="f"></a>[f] <a rel="nofollow" href="https://github.com/broadinstitute/gatk/blob/master/README.md">GATK4’s README</a> as of the gatk4.beta.3 release states:</p>

<blockquote class="UserQuote blockquote"><div class="blockquote-content">
  <p class="blockquote-line">You can also omit the "--num-executors" argument to enable <a rel="nofollow" href="https://spark.apache.org/docs/latest/job-scheduling.html#dynamic-resource-allocation">dynamic allocation</a> if you configure the cluster properly (see the Spark website for instructions).</p>
</div></blockquote>

<p><a name="g" id="g"></a>[g] What do we mean by log and staging directories? In our storage bucket, we see that Dataproc creates cluster and job logs for each cluster and each <code class="code codeInline" spellcheck="false">gatk-launch</code> command. If we look into the job log, we see the run saves the standard output, which for this tool actually contains FlagStat results of interest. Additionally, the staging folder contains the <code class="code codeInline" spellcheck="false">gatk-package-4.beta.2-spark.jar</code>, which is 131.6MB.</p>

<hr></hr><ul><li>For runs that you expect to take a long time, be sure to enable <em>TMUX</em>, <em>Screen</em> or equivalent terminal multiplexer in your Terminal session.</li>
<li><p>Great 2-part blogpost on tuning Spark jobs by Sandy Ryza:</p>

<ul><li><a href="http://blog.cloudera.com/blog/2015/03/how-to-tune-your-apache-spark-jobs-part-1/" rel="nofollow">http://blog.cloudera.com/blog/2015/03/how-to-tune-your-apache-spark-jobs-part-1/</a></li>
<li><a href="https://blog.cloudera.com/blog/2015/03/how-to-tune-your-apache-spark-jobs-part-2/" rel="nofollow">https://blog.cloudera.com/blog/2015/03/how-to-tune-your-apache-spark-jobs-part-2/</a></li>
</ul></li>
<li><p>About YARN and resource allocation: <a href="https://spark.apache.org/docs/latest/running-on-yarn.html" rel="nofollow">https://spark.apache.org/docs/latest/running-on-yarn.html</a></p></li>
<li><p>GATK Spark Resources:</p>

<ul><li><a href="https://github.com/broadinstitute/gatk#sparkcluster" rel="nofollow">https://github.com/broadinstitute/gatk#sparkcluster</a></li>
<li><a href="https://github.com/broadinstitute/gatk#dataproc" rel="nofollow">https://github.com/broadinstitute/gatk#dataproc</a></li>
<li><a href="https://github.com/broadinstitute/gatk#spark_further_reading" rel="nofollow">https://github.com/broadinstitute/gatk#spark_further_reading</a></li>
</ul></li>
<li><p>Be aware of <a rel="nofollow" href="https://github.com/broadinstitute/gatk/issues/2793">https://github.com/broadinstitute/gatk/issues/2793</a>, an issue in writing results to Google Cloud storage. This should be fixed for later leases.</p></li>
</ul><hr></hr><p><a rel="nofollow" href="#top">back to top</a></p>

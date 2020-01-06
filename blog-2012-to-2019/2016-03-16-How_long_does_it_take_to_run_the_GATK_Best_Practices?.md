## How long does it take to run the GATK Best Practices?

By Geraldine_VdAuwera

<p><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/87/74b9fa0064afc83c6f8dc5f08c13ef.png" height="150" border="none" alt="image" style="float: right;" class="embedImage-img importedEmbed-img"></img></p>

<p>When you're setting up a variant discovery pipeline, you face two problems: deciding what tools to run (with what options), and how to run them efficiently so that it doesn't take forever. Between our documentation and our support forum, we can get you most if not all the way to solving the first problem, unless you're working with something really unusual.</p>

<p>However, the second problem is not something we've been able to help much with. We only benchmark computational requirements/performance for the purposes of our in-house pipelines, which are very specific to our particular infrastructure, and we don't have the resources to test different configurations. As a result it's been hard for us to give satisfying answers to questions like "How long should this take?" or "How much RAM do I need?" -- and we're aware this is a big point of pain.</p>

<p>So I'm really pleased to announce that a team of engineers at Intel have been developing a system to profile pipelines that implement our Best Practices workflows on a range of hardware configurations. This is a project we've been supporting by providing test data and implementation advice, and it's really gratifying to see it bear fruit: the team recently published their first round of profiling, done on the per-sample segment of the germline variation pipeline (from BWA to HaplotypeCaller; FASTQ to GVCF) on a trio of whole genomes.</p>

<p>The <a rel="nofollow" href="http://www.intel.com/content/www/us/en/healthcare-it/solutions/documents/deploying-gatk-best-practices-paper.html">white paper</a> is available from the <a rel="nofollow" href="http://www.intel.com/content/www/us/en/healthcare-it/solutions/genomicscode-gatk.html">GATK-specific page of Intel's Health-IT initiative website</a> and contains some very useful insights regarding key bottlenecks in the pipeline. It also details the applicability of parallelizing options for each tool, as well as the effect of using different numbers of threads on performance, when run on a single 36-core machine. Spoiler alert: more isn't always better!</p>

<p>Read on for a couple of highlights of what I thought was especially interesting in the Intel team's white paper.</p>

<hr></hr><h3>Finding the thread count that's right for you: not just a <a rel="nofollow" href="http://www.bedbathandbeyond.com/store/s/thread-count"><em>Bed, Bath &amp; Beyond</em></a> question</h3>

<p>First, this figure showing how parallelization affects turnaround time for each tool in the pipeline does a great job of identifying where parallelization makes a the biggest difference (hellooooo BWA), as well as where the addition of more parallel threads shows quickly diminishing returns.</p>

<p>Note that here the term "thread" refers both to parallelized execution that is achieved through multithreading approaches (in GATK, these are invoked using <code class="code codeInline" spellcheck="false">-nt</code> and/or <code class="code codeInline" spellcheck="false">-nct</code> depending on the tool) as well as through local scatter-gather, which consists of running the tools over slices of data generated using Queue, the companion program to GATK. In terms of computational resources the result is similar: a certain number of cores on the machine are dedicated to running the given component. Full details are given in the script that accompanies the white paper; in a nutshell, multithreading was used for BWA-mem and RealignerTargetCreator, whereas scatter-gather was used to parallelize IndelRealigner, BaseRecalibrator, PrintReads and HaplotypeCaller.</p>

<p><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/6a/cf20af0f34f48c06e8ae78983d5ec4.png" alt="image" class="embedImage-img importedEmbed-img"></img></p>

<p>You can see that for the genome mapping step, done with BWA-mem, going from 1 to just 4 threads leads to a 4-fold decrease in runtime, which is <em>yuuuge</em> since it's the most time-consuming step in the pipeline. And beyond that you can still bring runtime down further by throwing more threads at the problem, until the relative gains bottom out somewhere between 8 and 36 -- at which point you're spending very little time on BWA mapping. Your mileage may vary, but for reference, in Broad's production pipeline we give BWA-mem 12 threads.</p>

<p>On the GATK end of the pipeline, HaplotypeCaller also shows an about 4-fold speedup when going from a single core to 4 scatter-gather jobs run on different cores, but beyond that the gains from additional parallelization tend to be progressively more modest. Multithreading with <code class="code codeInline" spellcheck="false">-nct</code> is not used at all because it has proved fairly unstable in HaplotypeCaller, leading to occasional unpredictable crashes.</p>

<p>The remaining steps see less dramatic improvement, comparatively speaking, though BaseRecalibrator and PrintReads with <code class="code codeInline" spellcheck="false">-BQSR</code> do show a decent 2-fold speedup when run on four cores instead of one. But this shows fairly clearly that there's little point to blindly throwing more parallelization at these tools.</p>

<hr></hr><h3>Are we there yet? Are we there yet? Are we there yet?</h3>

<p>In this second figure we're looking at CPU utilization throughout the pipeline, i.e. how much <em>computing</em> the machine is doing at any given time -- as opposed to doing boring things like reading and writing data to and from files (I/O), which is like driving from Omaha to Denver (a long flat drive where nothing exciting happens, but things get fun once you get there).</p>

<p>Note that this figure corresponds specifically to an "optimized run", <em>i.e.</em> a specific configuration of the pipeline where each tool was parallelized optimally based on earlier results.</p>

<p><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/fe/988e9bafc6e15a62dd8d530e8a4376.png" alt="image" class="embedImage-img importedEmbed-img"></img></p>

<p>You can see that BWA-mem is a busy beaver, spending pretty much all of its time furiously calculating mapping scores and writing out results to the output SAM file as it goes. In contrast, if we look at the tools that write out BAM files, we see a flurry of activity up front, then the line goes flat during a long period spent just writing out results to the output BAM file.</p>

<p>In the case of BaseRecalibrator, the tool only outputs a recalibration table, which doesn't take very long at all. Then looking at PrintReads (run with <code class="code codeInline" spellcheck="false">-BQSR</code>) you see a similar activity profile to BaseRecalibrator's, which corresponds to the on-the-fly recalibration done by the engine before the recalibrated data is written to the final pre-processed BAM file that will be fed to HaplotypeCaller.</p>

<p>Finally you see that HaplotypeCaller itself is the most compute-intensive tool in the GATK end of the pipeline; although this is not shown in the figure, I can tell you that much of its time is spent on graph assembly and pairHMM alignment of haplotypes. Note that here HaplotypeCaller is writing out a GVCF file; if you were to run HaplotypeCaller in regular mode (not using <code class="code codeInline" spellcheck="false">ERC GVCF</code>) you would see a shorter period of I/O flatline because the variants-only VCF output amounts to a much smaller file.</p>

<hr></hr><h3>What's next?</h3>

<p>If the above made you want to know more, head on over to Intel's Health-IT website and get the full <a rel="nofollow" href="http://www.intel.com/content/www/us/en/healthcare-it/solutions/documents/deploying-gatk-best-practices-paper.html">white paper</a>.</p>

<p>As I mentioned this is only the first pass in an ongoing project. The next step is going to involve implementing the joint genotyping and filtering with VQSR for the WGS trio, as well as profiling the equivalent exome pipeline on a cohort of ~30 exomes.</p>

<p>You may have noticed that this first pass was done one a single (albeit multi-core) machine; this was done on purpose to provide a baseline for end-to-end execution with the simplest configuration. Our friends at Intel will be looking at multi-machine setup in a future iteration, and for our part we'll have some new developments for you on the pipelining front soon -- so stay tuned to this blog or follow <a href="https://gatkforums.broadinstitute.org/gatk/profile/gatk_dev" rel="nofollow">@gatk_dev</a> on Twitter!</p>

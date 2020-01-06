## Version highlights for GATK version 3.2

By Geraldine_VdAuwera

<p>Better late than never (right?), here are the version highlights for GATK 3.2. Overall, this release is essentially a collection of bug fixes and incremental improvements that we wanted to push out to not keep folks waiting while we're working on the next big features. Most of the bug fixes are related to the HaplotypeCaller and its "reference confidence model" mode (which you may know as <code class="code codeInline" spellcheck="false">-ERC GVCF</code>). But there are also a few noteworthy improvements/changes in other tools which I'll go over below.</p>

<hr></hr><h3>Working out the kinks in the "reference confidence model" workflow</h3>

<p>The "reference confidence model" workflow, which I hope you have heard of by now, is that awesome new workflow we released in March 2014, which was the core feature of the GATK 3.0 version. It solves the N+1 problem and allows you to perform joint variant analysis on ridiculously large cohorts without having to enslave the entire human race and turning people into batteries to power a planet-sized computing cluster. More on that later (omg we're writing a paper on it, <em>finally!</em>).</p>

<p>You can read the full list of improvements we've made to the tools involved in the workflow (mainly HaplotypeCaller and Genotype GVCFs) in Eric's (unusually detailed) Release Notes for this version. The ones you are most likely to care about are that the "missing PLs" bug is fixed, GenotypeGVCFs now accepts arguments that allow it to emulate the HC's genotyping capabilities more closely (such as <code class="code codeInline" spellcheck="false">--includeNonVariantSites</code>), the AB annotation is fully functional, reference DPs are no longer dropped, and CatVariants now accepts lists of VCFs as input. OK, so that last one is not really specific to the reference model pipeline, but that's where it really comes in handy (imagine generating a command line with thousands of VCF filenames -- it's not pretty).</p>

<h3>HaplotypeCaller now emits post-realignment coverage metrics</h3>

<p>The coverage metrics (DP and AD) reported by HaplotypeCaller are now those calculated <strong>after</strong> the HC's reassembly step, based on the reads having been realigned to the most likely haplotypes. So the metrics you see in the variant record should match what you see if you use the <code class="code codeInline" spellcheck="false">-bamout</code> option and visualize the reassembled ActiveRegion in a genome browser such as IGV. Note that if any of this is not making sense to you, say so in the comments and we'll point you to the <strong>new HaplotypeCaller documentation</strong>! Or, you know, look for it in the Guide.</p>

<h3>R you up to date on your libraries?</h3>

<p>We updated the plotting scripts used by BQSR and VQSR to use the latest version of ggplot2, to get rid of some deprecated function issues. If your Rscripts are suddenly failing, you'll need to update your R libraries.</p>

<h3>A sincere apology to GATK-based tool developers</h3>

<p>We're sorry for making you jump through all these hoops recently. As if the switch to Maven wasn't enough, we have now completed a massive reorganization/renaming of the codebase that will probably cause you some headaches when you port your tools to the newest version. But we promise this is the last big wave, and ultimately this will make your life easier once we get the GATK core framework to be a proper maven artifact.</p>

<p>In a nutshell, the base name of the codebase has changed from <code class="code codeInline" spellcheck="false">sting</code> to <code class="code codeInline" spellcheck="false">gatk</code> (which hopefully makes more sense), and the most common effect is that <code class="code codeInline" spellcheck="false">sting.gatk</code> classpath segments are now <code class="code codeInline" spellcheck="false">gatk.tools</code>. This, by the way, is why we had a bunch of broken documentation links; most of these have been fixed (yay symlinks) but there may be a few broken URLs remaining. If you see something, say something, and we'll fix it.</p>

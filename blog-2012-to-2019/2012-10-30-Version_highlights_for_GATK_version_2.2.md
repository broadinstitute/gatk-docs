## Version highlights for GATK version 2.2

By Geraldine_VdAuwera

<h3>Overview:</h3>

<p>We're very excited to present release version 2.2 to the public. As those of you who have been with us for a while know, it's been a much longer time than usual since the last minor release (v 2.1). Ah, but don't let the "minor" name fool you - this release is chock-full of major improvements that are going to make a big difference to pretty much everyone's use of the GATK. That's why it took longer to put together; we hope you'll agree it was worth the wait!</p>

<p>The biggest changes in this release fall in two categories: enhanced performance and improved accuracy. This is rounded out by a gaggle of bug fixes and updates to the resource bundle.</p>

<h3>Performance enhancements</h3>

<p>We know y'all have variants to call and papers to publish, so we've pulled out all the stops to make the GATK run faster without costing 90% of your grant in computing hardware. First, we're introducing a new multi-threading feature called <strong>Nanoscheduler</strong> that we've added to the GATK engine to expand your options for parallel processing. Thanks to the Nanoscheduler, we're finally able to bring multi-threading back to the BaseRecalibrator. We've also made some seriously hard-core algorithm optimizations to ReduceReads and the two variant callers, UnifiedGenotyper and HaplotypeCaller, that will cut your runtimes down so much you won't know what to do with all the free time. Or, you'll actually be able to get those big multisample analyses done in a reasonable amount of time…</p>

<h4>- Introducing the Nanoscheduler</h4>

<p>This new multi-threading feature of the GATK engine allows you to take advantage of having multiple cores per machine, whether in your desktop computer or on your server farm. Basically, the Nanoscheduler creates clones of the GATK, assigns a subset of the job to each and runs it on a different core of the machine. Usage is similar to the <code class="code codeInline" spellcheck="false">-nt</code> mode you may already be familiar with, except you call this one with the new <code class="code codeInline" spellcheck="false">-nct</code> argument. Note that the Nanoscheduler currently reserves one thread for itself, which acts like a manager (it bosses the other threads around but doesn't get much work done itself) so to see any real performance gain you'll need to use at least <code class="code codeInline" spellcheck="false">-nct 3</code>, which yields two "worker" threads. This is a limitation of the current implementation which we hope to resolve soon. See the updated document on <a href="">Parallelism with the GATK (v2)</a> (link coming soon) for more details of how the Nanoscheduler works, as well as recommendations on how to optimize parallelization for each of the main GATK tools.</p>

<h4>- Multi-threading power returns to BaseRecalibrator</h4>

<p>Many of you have complained that the rebooted BaseRecalibrator in GATK2 takes forever to run. Rightly so, because until now, you couldn't effectively run it in multi-threaded mode. The reason for that is fairly technical, but in essence, whenever a thread started working on a chunk of data it locked down access to the rest of the dataset, so any other threads would have to wait for it to finish working before they could begin. That's not really multi-threading, is it? No, we didn't think so either. So we rewrote the BaseRecalibrator to not do that anymore, and we gave it a much saner and effective way of handling thread safety: each thread locks down just the chunk of data it's assigned to process, not the whole dataset. The graph below shows the performance gains of the new system over the old one. Note that in practice, this is operated by the Nanoscheduler (see above); so remember, if you want to parallelize BaseRecalibrator, use <code class="code codeInline" spellcheck="false">-nct</code>, not <code class="code codeInline" spellcheck="false">-nt</code>, and be sure to assign three or more threads.</p>

<p><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/db/ee795460ba9f10a7c8c14d0f588e4f.png" alt="image" class="embedImage-img importedEmbed-img"></img></p>

<h4>- Reduced runtimes for ReduceReads (Full version only)</h4>

<p>Without going into the gory technical details, we optimized the underlying compression algorithm that powers ReduceReads, and we're seeing some very significant improvements in runtime. For a "best-case scenario" BAM file, <em>i.e.</em> a well-formatted BAM with no funny business, the average is about a three-fold decrease in runtime. Yes, it's three times faster! And if that doesn't impress you, you may be interested to know that for "worst-case scenario" BAM files (which are closer to what we see in the wild, so to speak, than in our climate-controlled test facility) we see orders of magnitude of difference in runtimes. That's tens to hundreds of times faster. To many of you, that will make the difference between being able to reduce reads or not. Considering how reduced BAMs can help bring down storage needs and runtimes in downstream operations as well -- it's a pretty big deal.</p>

<h4>- Faster joint calling with UnifiedGenotyper</h4>

<p>Ah, another algorithm optimization that makes things go faster. This one affects the EXACT model that underlies how the UG calls variants. We've modified it to use a new approach to multiallelic discovery, which greatly improves scalability of joint calling for multi-sample projects. Previously, the relationship between the number of possible alternate alleles and the difficulty of the calculation (which directly impacts runtime) was exponential. So you had to place strict limits on the number of alternate alleles allowed (like 3, tops) if you wanted the UG run to finish during your lifetime. With the updated model, the relationship is linear, allowing the UG to comfortably handle around 6 to 10 alternate alleles without requiring some really serious hardware to run on. This will mostly affect projects with very diverse samples (as opposed to more monomorphic ones).</p>

<h4>- Making the HaplotypeCaller go <strong>Whoosh!</strong> (Full version only)</h4>

<p>The last algorithm optimization for this release, but certainly not the least (there is no least, and no parent ever has a favorite child), this one affects the likelihood model used by the HaplotypeCaller. Previously, the HaplotypeCaller's HMM required calculations to be made in logarithmic space in order to maintain precision. These log-space calculations were very costly in terms of performance, and took up to 90% of the runtime of the HaplotypeCaller. Everyone and their little sister has been complaining that it operates on a geological time scale, so we modified it to use a new approach that gets rid of the log-space calculations without sacrificing precision. Words cannot express how well that worked, so here's a graph.</p>

<p><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/f5/132730c76da5858596aa671b60e2c9.png" alt="image" class="embedImage-img importedEmbed-img"></img></p>

<p>This graph shows runtimes for HaplotypeCaller and UnifiedGenotyper before (left side) and after (right side) the improvements described above. Note that the version numbers refer to development versions and do not map directly to the release versions.</p>

<h3>Accuracy improvements</h3>

<p>Alright, going faster is great, I hear you say, but are the results any good? We're a little insulted that you asked, but we get it -- you have responsibilities, you have to make sure you get the best results humanly possible (and then some). So yes, the results are just as good with the faster tools -- and we've actually added a couple of features to make them even better than before. Specifically, the BaseRecalibrator gets a makeover that improves indel scores, and the UnifiedGenotyper gets equipped with a nifty little trick to minimize the impact of low-grade sample contamination.</p>

<h4>- Seeing alternate realities helps BaseRecalibrator grok indel quality scores (Full version only)</h4>

<p>When we brought multi-threading back to the BaseRecalibrator, we also revamped how the tool evaluates each read. Previously, the BaseRecalibrator accepted the read alignment/position issued by the aligner, and made all its calculations based on that alignment. But aligners make mistakes, so we've rewritten it to also consider other possible alignments and use a probabilistic approach to make its calculations. This delocalized approach leads to improved accuracy for indel quality scores.</p>

<h4>- Pruning allele fractions with UnifiedGenotyper to counteract sample contamination (Full version only):</h4>

<p>In an ideal world, your samples would never get contaminated by other DNA. This is not an ideal world. Sample contamination happens more often than you'd think; usually at a low-grade level, but still enough to skew your results. To counteract this problem, we've added a contamination filter to the UnifiedGenotyper. Given an estimated level of contamination, the genotyper will downsample reads by that fraction for each allele group. By default, this number is set at 5% for high-pass data. So in other words, for each allele it detects, the genotyper throws out 5% of reads that have that allele.</p>

<p>We realize this may raise a few eyebrows, but trust us, it works, and it's safe. This method respects allelic proportions, so if the actual contamination is lower, your results will be unaffected, and if a significant amount of contamination is indeed present, its effect on your results will be minimized. If you see differences between results called with and without this feature, you have a contamination problem.</p>

<p>Note that this feature is turned <strong>ON</strong> by default. However it only kicks in above a certain amount of coverage, so it doesn't affect low-pass datasets.</p>

<h3>Bug fixes</h3>

<p>We've added a lot of systematic tests to the new tools and features that were introduced in GATK 2.0 and 2.1 (Full versions), such as ReduceReads and the HaplotypeCaller. This has enabled us to flush out a lot of the "growing pains" bugs, in addition to those that people have reported on the forum, so all that is fixed now. We realize many of you have been waiting a long time for some of these bug fixes, so we thank you for your patience and understanding. We've also fixed the few bugs that popped up in the mature tools; these are all fixed in both Full and Lite versions of course.</p>

<p>Details will be available in the new <a rel="nofollow" href="http://www.broadinstitute.org/gatk/guide/version-history">Change log</a> shortly.</p>

<h3>Resource bundle updates</h3>

<p>Finally, we've updated the resource bundle with a variant callset that can be used as a standard for setting up your variant calling pipelines. Briefly, we generated this callset from the raw BAMs of our favorite trio (CEU Trio) according to our Best Practices (using the UnifiedGenotyper on unreduced BAMs). We additionally phased the calls using PhaseByTransmission. We've also updated the HapMap VCF.</p>

<p>Note that from now on, we plan to generate a new callset with each major and minor release, and the numbering of the bundle versions will follow the GATK version numbers to avoid any confusion.</p>

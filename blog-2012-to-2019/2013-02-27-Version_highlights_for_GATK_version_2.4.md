## Version highlights for GATK version 2.4

By Geraldine_VdAuwera

<h3>Overview</h3>

<p>We are very proud (and more than a little relieved) to finally present version 2.4 of the GATK! It's been a long time coming, but we're certain you'll find it well worth the wait. This release is bursting at the seams with new features and improvements, as you'll read below. It is also very probably going to be our least-buggy initial release yet, thanks to the phenomenal effort that went into adding extensive automated tests to the codebase.</p>

<p><strong>Important note: Keep in mind that this new release comes with a brand new license, as we announced a few weeks ago <a rel="nofollow" href="http://gatkforums.broadinstitute.org/discussion/2091/upcoming-changes-to-the-license-the-retirement-of-gatk-lite-by-v-2-4">here</a>. Be sure to at least check out the figure that explains the different packages we (and our commercial partner Appistry) offer, and get the one that is appropriate for your use of the GATK.</strong></p>

<p><a rel="nofollow" href="http://gatkforums.broadinstitute.org/discussion/2091/upcoming-changes-to-the-license-the-retirement-of-gatk-lite-by-v-2-4"><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/c4/7a06b415230d85b6c13965ce31797b.png" alt="image" class="embedImage-img importedEmbed-img"></img></a></p>

<p>With that disclaimer out of the way, here are the feature highlights of version 2.4!</p>

<h3>Better, faster, more productive</h3>

<p>Let's start with what everyone wants to hear about: improvements in speed and accuracy. There are in fact far more improvements in accuracy than are described here, again because of the extensive test coverage we've added to the codebase. But here are the ones that we believe will have the most impact on your work.</p>

<h4>- Base Quality Score Recalibration gets a Bayesian boost</h4>

<p>We realized that even though BaseRecalibrator was doing a fabulous job in general, the calculation for the empirical quality of a bin (e.g. all bases at the 33rd cycle of a read) was not always accurate. Specifically, we would draw the same conclusions from bins with many or few observations -- but in the latter case that was not necessarily correct (we were seeing some Q6s get recalibrated up to Q30s, for example). We changed this behavior so that the BaseRecalibrator now calculates a proper Bayesian estimate of the empirical quality. As a result, for bins with very little data, the likelihood is dwarfed by a prior probability that tends towards the original quality; there is no effect on large bins, which were already fine. This brings noticeable improvements in the genotype likelihoods being produced from the genotypes, in particular for the heterozygous state (as expected).</p>

<h4>- HaplotypeCaller catching up to UnifiedGenotyper on speed, gets ahead on accuracy</h4>

<p>You may remember that in the highlights for version 2.2, we were excited to announce that the HaplotypeCaller was no longer operating on geological time scales. Well, now the HC has made another big leap forward in terms of speed -- and it is now almost as fast as the UnifiedGenotyper. If you were reluctant to move from the UG to the HC based on runtime, that shouldn't be an issue anymore! Or, if you were  unconvinced by the merits of the new calling algorithm,  you'll be interested to know that our internal tests show that the HaplotypeCaller is now more accurate in calling variants (SNPs as well as Indels) than the UnifiedGenotyper.</p>

<p><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/aa/44374a5788cf46b86ba171fe7d1a1d.png" alt="image" class="embedImage-img importedEmbed-img"></img><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/93/82013205fa67822a91b44d57b9f645.png" alt="image" class="embedImage-img importedEmbed-img"></img></p>

<p>How did we make this happen? There are too many changes to list here, but one of the key modifications that makes the HaplotypeCaller much faster (without sacrificing any accuracy!) is that we've greatly optimized how local Smith-Waterman re-assembly is applied. Previously, when the HC encountered a region where reassembly was needed, it performed SW re-assembly on the entire region, which was computationally very demanding. In the new implementation, the HC generates a "bubble" (yes, that's the actual technical term) around each individual haplotype, and applies the SW re-assembly only within that bubble. This brings down the computational challenge by orders of magnitude.</p>

<h3>New tools, extended capabilities</h3>

<p>We're not just fluffing up the existing tools -- we're also adding new tools to extend the capabilities of our toolkit.</p>

<h4>- New filtering options to better control your data  </h4>

<p>A new Read Filter, ReassignOneMappingQualityFilter, allows you to -- well, it's in the name -- reassign one mapping quality. This is useful for example to process data output by programs like TopHat which use MAPQ = 255 to convey meaningful information. The GATK would normally ignore any reads with that mapping quality. With the new filter, you can selectively reassign that quality to something else so that those reads will get utilized, without affecting the rest of your dataset.</p>

<p>In addition, the recently introduced contamination filter gets upgraded with the option to apply decontamination individually per sample.  </p>

<h4>- Useful tool options get promoted to standalone tools</h4>

<p>Version 2.4 includes several new tools that grew out of existing tool options. The rationale for making them standalone tools is that they represent particularly useful capabilities that merit expansion, and expanding them within their "mother tool" was simply too cumbersome.</p>

<ul><li><p>GenotypeConcordance graduates from being a module of VariantEval, to being its own fully-fledged tool. This comes with many bug fixes and an overhaul of how the concordance results are tabulated, which we hope will cause less confusion than it has in the past!</p></li>
<li><p>RegenotypeVariants takes over -- and improves upon -- the functionality previously provided by the <code class="code codeInline" spellcheck="false">--regenotype</code> option of SelectVariants. This tool allows you to refresh the genotype information in a VCF file after samples have been added or removed.</p></li>
</ul><p>And we're also adding CatVariants, a tool to quickly combine multiple VCF files whose records are non-overlapping (e.g. as produced during scatter-gather using Queue). This should be a useful alternative to CombineVariants, which is primarily meant for more complex combination operations.</p>

<h3>Nightly builds</h3>

<p>Going forward, we have decided to provide <a rel="nofollow" href="http://www.broadinstitute.org/gatk/nightly">nightly automated builds</a> from our development tree. This means that you can get the very latest development version -- no need to wait weeks for bug fixes or new features anymore! <strong>However</strong>, this comes with a <strong>gigantic caveat emptor</strong>: these are bleeding-edge versions that are likely to contain bugs, and features that have never been tested in the wild. And they're automatically generated at night, so we can't even guarantee that they'll run. All we can say of any of them is that the code was able to compile -- beyond that, we're off the hook. We won't answer support questions about the new stuff. So in short: <strong>you want to try the nightlies, you do so at your own risk</strong>.</p>

<p>If any of the above scares or confuses you, no problem -- just stay well clear of the owl and you won't get bitten.</p>

<p>But hey, if you're feeling particularly brave or lucky, have fun <img class="emoji" src="https://gatkforums.broadinstitute.org/resources/emoji/smile.png" title=":)" alt=":)" height="20"></img></p>

<p><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/00/0f8ae8c709a5b4813fb369069702fc.png" alt="image" class="embedImage-img importedEmbed-img"></img></p>

<h3>Documentation upgrades</h3>

<p>The release of version 2.4 also coincides with some upgrades to the documentation that are significant enough to merit a brief mention.</p>

<h4>- Every release gets a versioned Guide Book PDF</h4>

<p>From here on, every release (including minor releases, such as 2.3-9) will be accompanied by the generation of a <a rel="nofollow" href="http://www.broadinstitute.org/gatk/guide/version-history">PDF Guide Book</a> that contains the online documentation articles as they are at that time. It will not only allow you to peruse the documentation offline, but it will also serve as versioned documentation. This way, if in the future you need to go back and examine results you obtained with an older version of the GATK, you can find easily find the documentation that was valid at that time. Note that the Technical Documentation (which contains the exhaustive lists of arguments for each tool) is not included in the Guide Book since it can be generated directly from the source code.  </p>

<p><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/ec/bbfc8a69c235a13c8597a3d897668d.png" alt="image" class="embedImage-img importedEmbed-img"></img></p>

<h4>- Technical Documentation gets a Facelift</h4>

<p>Speaking of the Technical Documentation, we are happy to announce that we've enriched those pages with additional information, including  available parallelization options and default read filters for each tool, where applicable. We've also reorganized the main categories in the Technical Documentation index to make it easier to browse tools and find what you need.</p>

<p><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/de/1b39f236799567bef80efed3a4e82a.png" alt="image" class="embedImage-img importedEmbed-img"></img></p>

<h3>Developer alert</h3>

<p>Finally, a few words for developers who have previous experience with the GATK codebase. The VariantContext and related classes have been moved out of the GATK codebase and into the Picard public repository. The GATK now uses the resulting Variant.jar as an external library (currently version 1.85.1357). We've also updated the Picard and Tribble jars to version 1.84.1337.</p>

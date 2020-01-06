## Version highlights for GATK release 3.0

By Geraldine_VdAuwera

<p>Better late than never, here is the now-traditional "Highlights" document for GATK version 3.0, which was released two weeks ago. It will be a very short one since we've already gone over the new features in detail in separate articles --but it's worth having a recap of everything in one place. So here goes.</p>

<hr></hr><h3>Work smarter, not harder</h3>

<p>We are delighted to present our new Best Practices workflow for variant calling in which multisample calling is replaced by a winning combination of single-sample calling in gVCF mode and joint genotyping analysis. This allows us to both bypass performance issues and solve the so-called "N+1 problem" in one fell swoop. For full details of why and how this works, please see <a rel="nofollow" href="http://www.broadinstitute.org/gatk/guide/article?id=3893">this document</a>. In the near future, we will update our Best Practices page to make it clear that the new workflow is now the recommended way to go for calling variants on cohorts of samples. We've already received some pretty glowing feedback from early adopters, so be sure to try it out for yourself!</p>

<h3>Jumping on the RNAseq bandwagon</h3>

<p>All the cool kids were doing it, so we had to join the party. It took a few months of experimentation, a couple of new tools and some tweaks to the HaplotypeCaller, but you can now call variants on RNAseq with GATK! <a rel="nofollow" href="http://www.broadinstitute.org/gatk/guide/article?id=3891">This document</a> details our Best Practices recommendations for doing so, along with a non-trivial number of caveats that you should keep in mind as you go.</p>

<h3>Goodbye to ReduceReads</h3>

<p>Nice try, but no. This tool is obsolete now that we have the gVCF/reference model pipeline (see above). Note that this means that GATK 3.0 will not support BAM files that were processed using ReduceReads!</p>

<h3>Changes for developers</h3>

<p>We've switched the build system from Ant to Maven, which should make it much easier to use GATK as a library against which you can develop your own tools. And on a related note, we're also making significant changes to the internal structure of the GATK codebase. Hopefully this will not have too much impact on external projects, but there will be a doc very shortly describing how the new build system works and how the codebase is structured.</p>

<h3>Hardware optimizations held for 3.1</h3>

<p>For reasons that will be made clear in the near future, we decided to hold the previously announced hardware optimizations until version 3.1, which will be released very soon. Stay tuned!</p>

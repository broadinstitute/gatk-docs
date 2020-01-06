## Version highlights for GATK version 3.6

By Geraldine_VdAuwera

<p>What better way to start the summer than with a new GATK release?</p>

<p>Umm no don't answer that, there's loads of good options. You could have a barbecue, eat some ice cream, go on a hike if that's the sort of thing that floats your kayak... Or you might live somewhere where winter is just starting and everything I just said there was terribly insensitive. Sorry.</p>

<p>Ahem. As I mentioned in my recent <a rel="nofollow" href="https://www.broadinstitute.org/gatk/blog?id=7601">sneak preview</a> blog post, the bulk of our development effort (speed! copy number! unicorns!) is now going into the GATK4 project. Accordingly, development in the GATK3 framework is winding down, so this release consists mainly of bug fixes, added convenience functions, and relatively minor behavior tweaks.</p>

<p>That being said we do have a few new experimental features in the VQSR tools (which haven't yet been fully ported to GATK4, hence the ongoing development in GATK3) that are pushing the envelope of allele-specific filtering. So that's interesting, if not yet fully documented (someone should really get on that). And you'll probably care about some of those tweaks I casually mentioned above -- in fact I guarantee that at least one of these things will matter to you in some way. <small>If you read through the whole thing and don't find <em>anything</em> relevant to you, tell me in the comments that I was wrong. That's what the internet is for.</small></p>

<p>As usual, here I go over the changes that matter the most / to the most; consider going through the <a rel="nofollow" href="https://www.broadinstitute.org/gatk/blog?id=7711">release notes</a> as well for a full list of changes.</p>

<hr></hr><h3>One version of Java to rule them all</h3>

<p>Possibly the most sweeping change in this version is that it introduces support for Java 8. As noted recently, when we switched our test framework to Java 8 we encountered multiple failures in GATK 3.5 tests, which I discussed <a rel="nofollow" href="https://www.broadinstitute.org/gatk/blog?id=7647">here</a>. We fixed the underlying issues, so from version 3.6 onward GATK now runs reliably on Java 8. As a nice bonus, this puts us back into sync with HTSJDK and the Picard toolkit, which had been running on Java 8 for a few months already. If you were doing it right, you had both versions of Java installed and ran each toolkit, GATK and Picard, on the appropriate version. How much hassle? Too much hassle! — Now you can run everything on Java 8.</p>

<hr></hr><h3>ET finally gets home; discovers phone bill, flees to Canada</h3>

<p>Here's another change that will affect everyone regardless of use case, in a good way: we removed the <a rel="nofollow" href="https://www.broadinstitute.org/gatk/guide/article?id=1250">Phone Home</a> usage reporting system. It served its purpose for many years, but we've outgrown it. So we ripped it all out. If you previously used a key to deactivate it with the <code class="code codeInline" spellcheck="false">-et NO_ET</code> and <code class="code codeInline" spellcheck="false">—key &lt;your_key&gt;</code> arguments, you can stop and take those out. Or if you're just too busy, leave them in -- the Phone Home code is all gone but we left in the argument definitions, so the parser shouldn't fail if you leave them in your commands. This shouldn't break any scripts.</p>

<hr></hr><h3>Indel realignment tools drop out, go open-source</h3>

<p>In the next few days we'll be making some updates to our Best Practices documentation to reflect updates to our production pipelines, and one thing you'll notice is that local realignment around indels is no longer included in the pre-processing part of the pipelines. More on that in a follow-up post; in a nutshell, indel realignment is just not useful enough anymore when you're calling variants with haplotype-based tools like HaplotypeCaller and MuTect2. This does mean we will no longer support the indel realignment tools as actively; but since others may care more about preserving and possibly even expanding this functionality, we've decided to move the relevant tools, IndelRealigner and RealignerTargetCreator, and related classes to the part of the GATK that is open-sourced under the MIT license.</p>

<hr></hr><h3>Confidence matters</h3>

<p>In response to popular demand, we made two frequently requested enhancements to the output of the variant discovery tools.</p>

<p>First, HaplotypeCaller and GenotypeGVCFs will now emit a no-call (./.) for any sample where GQ is zero, in both normal and GVCF modes, instead of emitting a specific genotype in which we have zero confidence. Note that these do not necessarily indicate that the variant call itself is bad, but that we are unable to choose between two (or more) genotypes for that particular sample.</p>

<p>Second, GenotypeGVCFs will now emit a QUAL value for invariant sites (i.e. where all samples are genotyped homozygous-variant) when run in <code class="code codeInline" spellcheck="false">-allSites</code> mode. By the way, we're aware that many of you would like to see a GVCF-like output option for the final VCF, in which you'd get essentially the same informational content as an all-sites VCF but with some form of interval-block compression. We still do not provide that functionality, and we do not have any immediate plans to work on implementing it in GATK3; however we are talking about how to make something like that happen in GATK4. Why the feet-dragging? Well, while it's not hard to do something like that badly, doing it correctly is quite a bit harder, so it will take some non-trivial development effort —- and since it's not something we currently need at Broad for our production needs, it's difficult to prioritize. But hey, the more you tell us you want this, the more we can justify putting it in the roadmap, so leave a comment below if you care about this.</p>

<hr></hr><h3>Bug in the spotlight</h3>

<p>Today's bug of infamy concerns the handling of allele depths (AD) in the joint variant discovery workflow, when the AD of the NON_REF allele is non-zero. This was an interesting little edge case. First we have HaplotypeCaller emitting a GVCF where, at a given position, the AD for the NON_REF allele is non-zero. This is fine, even expected. The problem arises during GVCF merging or joint genotyping, when CombineGVCFs or GenotypeGVCFs takes this NON_REF AD count and -- this is the bug -- copies it over to one of the alleles seen in another sample. This results in observing sum(AD) &gt; DP, which is obviously not good. The new behavior, post-fix, is that we still record the NON_REF AD in the single-sample GVCF, but we don't copy it over to concrete alleles in CombineGVCFs or GenotypeGVCFs. Because, duh.</p>

<hr></hr><h3>Dude, where's my read?</h3>

<p>Some days it seems like the number one preoccupation of GATK forum denizens is tracking down what happened to every last read in a pileup. That's probably because HaplotypeCaller and MuTect2, the two stars of our little show, can be awfully picky about what reads they include at key stages of the calling process. Their (totally justified) tendency to discount reads that they don't consider useful often causes confusion and/or concerns that reads are somehow being dropped for no reason. That's a concern we'd like to help alleviate. HaplotypeCaller and MuTect2 both already have an option to output a bam file showing the post-assembly read alignments and candidate haplotypes, which we call a <a rel="nofollow" href="https://www.broadinstitute.org/gatk/guide/article?id=5484">bamout</a> and by default shows the state of the data used by HC and M2 to make their calls. Now we've added an option to include in that output any reads that were not considered informative or were dropped for any reason internal to the caller (separate from any read filtering done by the engine). The option is called <code class="code codeInline" spellcheck="false">--emitDroppedReads</code> and should be used in conjunction with <code class="code codeInline" spellcheck="false">-bamout</code>.</p>

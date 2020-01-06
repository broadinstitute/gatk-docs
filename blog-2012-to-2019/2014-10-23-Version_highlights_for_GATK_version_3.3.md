## Version highlights for GATK version 3.3

By Geraldine_VdAuwera

<p>Another season, another GATK release. Personally, Fall is my favorite season, and while I don’t want to play favorites with versions (though unlike with children, you’re allowed to say that the most recent one is the best --and you can tell I was a youngest child) this one is pretty special to me.</p>

<p>Because <code class="code codeInline" spellcheck="false">-ploidy</code>! Yeah, that’s really all I need to say about that. I was a microbiologist once. And I expect many plant people will be happy too.</p>

<p>Other cool stuff detailed below includes: full functionality for the genotype refinement workflow tools; physical phasing and appropriate handling of dangly bits by HaplotypeCaller (must… resist… jokes…); a wealth of new documentation for variant annotations; and a slew of bug fixes that I won’t go over but are listed in the <a rel="nofollow" href="https://www.broadinstitute.org/gatk/blog?id=4739">release notes</a>.</p>

<hr></hr><h3>Genotype refinement workflow with all the trimmings</h3>

<p>As <a rel="nofollow" href="https://www.broadinstitute.org/gatk/blog?id=4734">announced earlier this week</a>, we recently developed a workflow for refining genotype calls, intended for researchers who need highly accurate genotype information as well as preliminary identification of possible <em>de novo</em> mutations (see the <a rel="nofollow" href="https://www.broadinstitute.org/gatk/guide/article?id=4723">documentation</a> for details). Although all the tools involved were already available in GATK 3.2, some functionalities were not, so we’re very happy to finally make all of them available in this new version. Plus, we like the new StrandOddsRatio annotation (which sort of replaces FisherStrand for estimating strand bias) so much that we made it a standard one, and it now gets annotated by default.</p>

<hr></hr><h3>Non-diploids, rejoice!</h3>

<p>This is also a feature that was <a rel="nofollow" href="https://www.broadinstitute.org/gatk/blog?id=4535">announced a little while ago</a>, but until now was only fully available in the <a rel="nofollow" href="https://www.broadinstitute.org/gatk/nightly">nightly builds</a>, which are technically unsupported unless we tell you to use them to get past a bad bug. In this new release, both HaplotypeCaller and GenotypeGVCFs are able to deal with non-diploid organisms (whether haploid or exotically polyploid). In the case of HaplotypeCaller, you need to specify the ploidy of your non-diploid sample with the <code class="code codeInline" spellcheck="false">-ploidy</code> argument. HC can only deal with one ploidy at a time, so if you want to process different chromosomes with different ploidies (e.g. to call X and Y in males) you need to run them separately. On the bright side, you can combine the resulting files afterward. In particular, if you’re running the <code class="code codeInline" spellcheck="false">-ERC GVCF</code> workflow, you’ll find that both CombineGVCFs and GenotypeGVCFs are able to handle mixed ploidies (between locations <em>and</em> between samples). Both tools are able to correctly work out the ploidy of any given sample at a given site based on the composition of the GT field, so they don’t require you to specify the <code class="code codeInline" spellcheck="false">-ploidy</code> argument.</p>

<hr></hr><h3>HaplotypeCaller gets physical</h3>

<p>You know how HC performs a complete reassembly of reads in an ActiveRegion? (If you don’t, go read <a rel="nofollow" href="https://www.broadinstitute.org/gatk/guide/article?id=4146">this</a> now. Go on, we’ll wait for you.) Well, this involves building an assembly graph, of course (of course!), and it produces a list of haplotypes. Fast-forward a couple of steps, and you end up with a list of variants. That’s great, but until now, those variants were unphased, meaning the HC didn’t give you any information about whether any two variants’ alleles were on the same haplotype (meaning, on the same physical piece of DNA) or not. For example, you’d want to know whether you had this:</p>

<p><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/68/58b49422caa3758b0de367dac496e2.png" alt="image" class="embedImage-img importedEmbed-img"></img></p>

<p>or this:</p>

<p><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/4e/e0653b703db433d3108ddbfc408fa7.png" alt="image" class="embedImage-img importedEmbed-img"></img></p>

<p>But HC wouldn’t tell you which it was in its output. Which was a shame, because the HC sees that information! It took a little tweaking to get it to talk, but now it emits physical phasing by default in its GVCF output (both banded GVCF and BP_RESOLUTION).</p>

<p>In a nutshell, phased records will look like this:</p>

<pre class="code codeBlock" spellcheck="false">1   1372243  .  T  &lt;NON_REF&gt;  .  .  END=1372267  &lt;snip&gt;  &lt;snip&gt;
1   1372268  .  G  A,&lt;NON_REF&gt;  .  .  &lt;snip&gt;  GT:AD:DP:GQ:PGT:PID:PL:SB 0/1:30,40,0:70:99:0|1:1372268_G_A:&lt;snip&gt;
1   1372269  .  G  T,&lt;NON_REF&gt;  .  .  &lt;snip&gt;  GT:AD:DP:GQ:PGT:PID:PL:SB 0/1:30,41,0:71:99:0|1:1372268_G_A:&lt;snip&gt;
1   1372270  .  C  &lt;NON_REF&gt;  .  .  END=1372299  &lt;snip&gt;  &lt;snip&gt;
</pre>

<p>You see that the phasing info is encoded in two new sample-level annotations, PID (for phase identifier) and PGT (phased genotype). More than two variants can be phased in a group with the same PID, and that can include mixed types of variants (e.g. SNPs and indels).</p>

<p><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/fe/00a776d934adbd12663ffc698c21d0.png" alt="image" class="embedImage-img importedEmbed-img"></img></p>

<p>The one big caveat related to the physical phasing output by HC in GVCFs is that, like the GVCF itself, <em>it is not intended to be used directly for analysis</em>! You <strong>must</strong> run your GVCFs through GenotypeGVCFs in order to get the finalized, properly formatted, ready-for-analysis calls.</p>

<hr></hr><h3>Heads or tails</h3>

<p>Speaking of HaplotypeCaller getting more helpful all the time, here’s some more of that. This still has to do with the graph assembly, and specifically, with how HC handles the bits at the edges of the graph, which are called <strong>dangling heads</strong> and <strong>dangling tails</strong>. Without going too far into the details, let’s just say that sometimes you have a variant that’s near the edge of a covered region, and due to technical reasons (<em>cough</em> kmer size <em>cough</em>) the end of the variant path can’t be tied back into the reference path, so it just dangles there (like, say, Florida) and gets trimmed off in the next step (rising ocean levels). And thus the variant is lost (boo).</p>

<p><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/71/6d88e8c187e7a3df0af6b881b1c73c.png" alt="image" class="embedImage-img importedEmbed-img"></img></p>

<p>We originally started paying attention to this because it often happens at the edge of exons near splice junctions in RNAseq data, but it can also happen in DNA data. The solution was to give HC the ability to recover these cliff-dwelling variants by merging the dangling ends back into the graph using special logic tailored for those situations. If you have been using our RNAseq Best Practices, then you may recognize this as the logic invoked by the <code class="code codeInline" spellcheck="false">--recoverDanglingHeads</code> argument. In the new version, the functionality has been improved further and is now enabled by default for all variant calling (so you no longer need to specify that argument for RNAseq analysis). The upshot is that sensitivity is improved, especially for RNAseq data but also for DNA.</p>

<hr></hr><h3>Variant annotations finally make sense</h3>

<p>Finally, I want to attract everyone’s attention to the <a rel="nofollow" href="https://www.broadinstitute.org/gatk/guide/tooldocs/#VariantAnnotations">Variant Annotations section of the Tool Documentation</a>, which has just undergone a comprehensive overhaul. All annotations now have some kind of documentation outlining their general purpose, output, interpretation, caveats and some notes about how they’re calculated where applicable. Tell us what you think; we are feedback junkies.</p>

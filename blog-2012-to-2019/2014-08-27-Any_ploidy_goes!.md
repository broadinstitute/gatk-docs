## Any ploidy goes!

By Geraldine_VdAuwera

<p>Until now, HaplotypeCaller was only capable of calling variants in diploid organisms due to some assumptions made in the underlying algorithms. I'm happy to announce that we now have a generalized version that is capable of handling any ploidy you specify at the command line!</p>

<p>This new feature, which we're calling "omniploidy", is technically still under development, but we think it's mature enough for the more adventurous to try out as a beta test ahead of the next official release. We'd especially love to get some feedback from people who work with non-diploids on a regular basis, so we're hoping that some of you microbiologists and assorted plant scientists will take it out for a spin and let us know how it behaves in your hands.</p>

<p>It's available in the latest <a rel="nofollow" href="http://www.broadinstitute.org/gatk/nightly">nightly builds</a>; just use the <code class="code codeInline" spellcheck="false">-ploidy</code> argument to give it a whirl. If you have any questions or feedback, please post a comment on this article in the forum.</p>

<p><em>Caveat: the downstream tools involved in the new GVCF-based workflow (GenotypeGVCFs and CombineGVCFs) are not yet capable of handling non-diploid calls correctly -- but we're working on it.</em></p>

<h4>UPDATE:</h4>

<p>We have added omniploidy support to the GVCF handling tools, with the following limitations:</p>

<ul><li><p>When running, you need to indicate the sample ploidy that was used to generate the GVCFs with <code class="code codeInline" spellcheck="false">-ploidy</code>. As usual 2 is the default ploidy.</p></li>
<li><p>The system does <strong>not</strong> support mixed ploidy across samples nor positions. An error message will be thrown if you attempt to genotype GVCFs that have a mixture, or that have some genotype whose ploidy does not match the <code class="code codeInline" spellcheck="false">-ploidy</code> argument.</p></li>
</ul><h4>LATEST UPDATE:</h4>

<p>As of GATK version 3.3-0, the GVCF tools are capable of ad-hoc ploidy detection, and can handle mixed ploidies. See the <a rel="nofollow" href="https://www.broadinstitute.org/gatk/blog?id=4741">release highlights</a> for details.</p>

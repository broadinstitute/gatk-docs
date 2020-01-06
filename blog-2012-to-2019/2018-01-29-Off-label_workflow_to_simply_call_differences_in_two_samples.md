## Off-label workflow to simply call differences in two samples

By shlee

<p><a rel="nofollow" href="https://us.v-cdn.net/5019796/uploads/editor/1d/z6c94rks9rbm.png"><img src="https://us.v-cdn.net/5019796/uploads/editor/1d/z6c94rks9rbm.png" width="320" alt="image" style="float: right;" class="embedImage-img importedEmbed-img"></img></a><br>
Given my years as a biochemist, if given two samples to compare, my first impulse is to want to know what are the functional differences, i.e. differences in proteins expressed between the two samples. I am interested in genomic alterations that ripple down the central dogma to transform a cell.</p>

<p>Please note the workflow that follows is NOT a part of the Best Practices. This is an illustrative, unsupported workflow. For the official <em>Somatic Short Variant Calling</em> Best Practices workflow, see <a rel="nofollow" href="https://gatkforums.broadinstitute.org/gatk/discussion/11136">Tutorial#11136</a>.</p>

<p>To call every <em>allele</em> that is different between two samples, I have devised a two-pass workflow that takes advantage of Mutect2 features. This workflow uses Mutect2 in <em>tumor-only mode</em> and appropriates the <code class="code codeInline" spellcheck="false">--germline-resource</code> argument to supply a single-sample VCF with allele fractions instead of population allele frequencies. The workflow assumes the two case samples being compared originate from the same parental line and the ploidy and mutation rates make it unlikely that any site accumulates more than one allele change.</p>

<hr></hr><h4>First, call on each sample using Mutect2's <em>tumor-only mode</em>.</h4>

<pre class="code codeBlock" spellcheck="false">gatk Mutect2 \
-R ref.fa \
-I A.bam \
-tumor A \
-O A.vcf

gatk Mutect2 \
-R ref.fa \
-I B.bam \
-tumor B \
-O B.vcf
</pre>

<h4>Second, for each single-sample VCF, move the sample-level <code class="code codeInline" spellcheck="false">AF</code> allele-fraction annotation to the INFO field and simplify to a sites-only VCF.</h4>

<p>This is a heuristic solution in which we substitute sample-level <em>allele fractions</em> for the expected population germline <em>allele frequencies</em>. Mutect2 is actually designed to use population germline <em>allele frequencies</em> in somatic likelihood calculations, so this substitution allows us to fulfill the requirement for an <code class="code codeInline" spellcheck="false">AF</code> annotation with plausible fractional values. The terminal screenshots highlight the data transpositions.</p>

<p>Before:<br><a rel="nofollow" href="https://us.v-cdn.net/5019796/uploads/editor/tt/b0e55c5yso64.png"><div style="text-align: center;"><img src="https://us.v-cdn.net/5019796/uploads/editor/tt/b0e55c5yso64.png" alt="image" class="embedImage-img importedEmbed-img"></img></div></a></p>

<p>After:<br><a rel="nofollow" href="https://us.v-cdn.net/5019796/uploads/editor/50/8qf7ndj3sseu.png"><div style="text-align: center;"><img src="https://us.v-cdn.net/5019796/uploads/editor/50/8qf7ndj3sseu.png" alt="image" class="embedImage-img importedEmbed-img"></img></div></a></p>

<h4>Third, call on each sample in a second pass, again in <em>tumor-only mode</em>, with the following additions.</h4>

<pre class="code codeBlock" spellcheck="false">gatk Mutect2 \
-R ref.fa \
-I A.bam \
-tumor A \
--germline-resource Baf.vcf \
--af-of-alleles-not-in-resource 0 \
--max-population-af 0 \
-pon pon_maskAB.vcf \
-O A-B.vcf

gatk Mutect2 \
-R ref.fa \
-I B.bam \
-tumor B \
--germline-resource Aaf.vcf \
--af-of-alleles-not-in-resource 0 \
--max-population-af 0 \
-pon pon_maskAB.vcf \
-O B-A.vcf
</pre>

<ul><li>Provide the matched single-sample callset for the case sample with the <code class="code codeInline" spellcheck="false">--germline-resource</code> argument.</li>
<li>Avoid calling any allele in the <code class="code codeInline" spellcheck="false">--germline-resource</code> by setting <code class="code codeInline" spellcheck="false">--max-population-af</code> to zero.</li>
<li>Maximize the probability of calling any differing allele by setting <code class="code codeInline" spellcheck="false">--af-of-alleles-not-in-resource</code> to zero.</li>
<li>Prefilter sites with artifacts and cross-sample contamination with a <a rel="nofollow" href="https://gatkforums.broadinstitute.org/gatk/discussion/11053/panel-of-normals-pon">panel of normals</a> (PoN) in which confident variant sites for both sample A and B have been removed, e.g. with <code class="code codeInline" spellcheck="false">gatk SelectVariants –V pon.vcf -XL AandB_haplotypecaller.vcf –O pon_maskAB.vcf</code>.</li>
</ul><h4>Fourth, filter out unlikely calls with FilterMutectCalls.</h4>

<pre class="code codeBlock" spellcheck="false">gatk FilterMutectCalls \
-V A-B.vcf \
-O A-B-filter.vcf

gatk FilterMutectCalls \
-V B-A.vcf \
-O B-A-filter.vcf
</pre>

<p>FilterMutectCalls provides many filters, e.g. that account for low base quality, for events that are clustered, for low mapping quality and for short-tandem-repeat contractions. Of the filters, let's consider the multiallelic filter. It discounts sites with more than two variant alleles that pass the tumor LOD threshold.</p>

<ul><li>We assume case sample variant sites will have a maximum of one allele that is different from the <code class="code codeInline" spellcheck="false">--germline-resource</code> control. A single allele call will pass the multiallelic filter. However, if we emit any shared variant allele alongside the differing allele, e.g. for a heterozygous site without ref alleles, then the call becomes multiallelic and will be filtered, which is not what we want. We previously set Mutect2’s <code class="code codeInline" spellcheck="false">--max-population-af</code> to zero to ensure only the differing allele is called, and so here we can rely on FilterMutectCalls to filter <a rel="nofollow" href="https://software.broadinstitute.org/gatk/documentation/article?id=11127">artifactual multiallelic sites</a>.</li>
<li>If multiple variant alleles are expected per call, then FilterMutectCall’s multiallelic filtering will be undesirable. For example, if changes to allele fractions for alleles that are shared was of interest for the two samples derived from the same parental line, and Mutect2 <code class="code codeInline" spellcheck="false">--max-population-af</code> was set to one in the previous step to additionally emit the shared variant alleles, then you would expect multiallelic calls. These will be indistinguishable from artifactual multiallelic sites.</li>
</ul><hr></hr><p>This workflow produces contrastive variants. If the samples are a tumor and its matched normal, then the calls include sites where heterozygosity was lost.</p>

<p>We know that <a rel="nofollow" href="https://en.wikipedia.org/wiki/Loss_of_heterozygosity">loss of heterozygosity</a> (LOH) plays a role in tumorigenesis (<a rel="nofollow" href="https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4522148/">doi:10.1186/s12920-015-0123-z</a>). This leads us to believe the heterozygosity of proteins we express contributes to our health. If this is true, then for somatic studies, if cataloging the gain of alleles is of interest, then cataloging the loss of alleles should also be of interest. Can we assume just because variants are germline that they do not play a role in disease processes? How can we account for the combinatorial effects of the diploid nature of our genomes?</p>

<p>Remember regions of LOH do not necessarily represent a haploid state but can be copy-neutral or even copy-amplified. It may be that as one parental chromosome copy is lost, the other is duplicated to maintain copy number, which presumably compensates for dosage effects as is the case in <a rel="nofollow" href="https://en.wikipedia.org/wiki/Uniparental_disomy">uniparental isodisomy</a>.</p>

<hr></hr>
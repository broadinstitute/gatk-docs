## Selecting variants of interest from a callset

By delangel

<h3>This document describes why you might want to extract a subset of variants from a callset and how you would achieve this.</h3>

<hr></hr><p>Often, a VCF containing many samples and/or variants will need to be subset in order to facilitate certain analyses (e.g. comparing and contrasting cases vs. controls; extracting variant or non-variant loci that meet certain requirements, displaying just a few samples in a browser like IGV, etc.).  The GATK tool that we use the most for subsetting calls in various ways is SelectVariants; it enables easy and convenient subsetting of VCF files according to many criteria.</p>

<p>Select Variants operates on VCF files (also sometimes referred to as ROD in our documentation, for Reference Ordered Data) provided at the command line using the GATK's built in <code class="code codeInline" spellcheck="false">--variant</code> option. You can provide multiple VCF files for Select Variants, but at least one must be named 'variant' and this will be the file (or set of files) from which variants will be selected. Other files can be used to modify the selection based on concordance or discordance between the callsets (see --discordance / --concordance arguments in the <a rel="nofollow" href="https://www.broadinstitute.org/gatk/gatkdocs/org_broadinstitute_gatk_tools_walkers_variantutils_SelectVariants.php">tool documentation</a>).</p>

<p>There are many options for setting the selection criteria, depending on what you want to achieve. For example, given a single VCF file, one or more samples can be extracted from the file, based either on a complete sample name, or on a pattern match. Variants can also be selected based on annotated properties, such as depth of coverage or allele frequency. This is done using <a rel="nofollow" href="http://www.broadinstitute.org/gatk/guide/article?id=1255">JEXL expressions</a>; make sure to read the linked document for details, especially the section on working with complex expressions.</p>

<p>Note that in the output VCF, some annotations such as AN (number of alleles), AC (allele count), AF (allele frequency), and DP (depth of coverage) are recalculated as appropriate to accurately reflect the composition of the subset callset. See further below for an explanation of how that works.</p>

<hr></hr><h3>Command-line arguments</h3>

<p>For a complete, detailed argument reference, refer to the GATK document page <a rel="nofollow" href="http://www.broadinstitute.org/gatk/gatkdocs/org_broadinstitute_gatk_tools_walkers_variantutils_SelectVariants.php">here</a>.</p>

<hr></hr><h3>Subsetting by sample and ALT alleles</h3>

<p>SelectVariants now keeps (r5832) the alt allele, even if a record is AC=0 after subsetting the site down to selected samples.  For example, when selecting down to just sample NA12878 from the OMNI VCF in 1000G (1525 samples), the resulting VCF will look like:</p>

<pre class="code codeBlock" spellcheck="false">1       82154   rs4477212       A       G       .       PASS    AC=0;AF=0.00;AN=2;CR=100.0;DP=0;GentrainScore=0.7826;HW=1.0     GT:GC   0/0:0.7205
1       534247  SNP1-524110     C       T       .       PASS    AC=0;AF=0.00;AN=2;CR=99.93414;DP=0;GentrainScore=0.7423;HW=1.0  GT:GC   0/0:0.6491
1       565286  SNP1-555149     C       T       .       PASS    AC=2;AF=1.00;AN=2;CR=98.8266;DP=0;GentrainScore=0.7029;HW=1.0   GT:GC   1/1:0.3471
1       569624  SNP1-559487     T       C       .       PASS    AC=2;AF=1.00;AN=2;CR=97.8022;DP=0;GentrainScore=0.8070;HW=1.0   GT:GC   1/1:0.3942
</pre>

<p>Although NA12878 is 0/0 at the first sites, ALT allele is preserved in the VCF record.  This is the correct behavior, as reducing samples down shouldn't change the character of the site, only the AC in the subpopulation.  This is related to the tricky issue of isPolymorphic() vs. isVariant().</p>

<ul><li><p>isVariant =&gt; is there an ALT allele?</p></li>
<li><p>isPolymorphic =&gt; is some sample non-ref in the samples?</p></li>
</ul><p>For clarity, in previous versions of SelectVariants, the first two monomorphic sites lose the ALT allele, because NA12878 is hom-ref at this site, resulting in VCF that looks like:</p>

<pre class="code codeBlock" spellcheck="false">1       82154   rs4477212       A       .       .       PASS    AC=0;AF=0.00;AN=2;CR=100.0;DP=0;GentrainScore=0.7826;HW=1.0     GT:GC   0/0:0.7205
1       534247  SNP1-524110     C       .       .       PASS    AC=0;AF=0.00;AN=2;CR=99.93414;DP=0;GentrainScore=0.7423;HW=1.0  GT:GC   0/0:0.6491
1       565286  SNP1-555149     C       T       .       PASS    AC=2;AF=1.00;AN=2;CR=98.8266;DP=0;GentrainScore=0.7029;HW=1.0   GT:GC   1/1:0.3471
1       569624  SNP1-559487     T       C       .       PASS    AC=2;AF=1.00;AN=2;CR=97.8022;DP=0;GentrainScore=0.8070;HW=1.0   GT:GC   1/1:0.3942
</pre>

<p>If you really want a VCF without monomorphic sites, use the option to drop monomorphic sites after subsetting.</p>

<hr></hr><h3>How do the AC, AF, AN, and DP fields change?</h3>

<p>Let's say you have a file with three samples.  The numbers before the ":" will be the genotype (0/0 is hom-ref, 0/1 is het, and 1/1 is hom-var), and the number after will be the depth of coverage.</p>

<pre class="code codeBlock" spellcheck="false">BOB        MARY        LINDA
1/0:20     0/0:30      1/1:50
</pre>

<p>In this case, the INFO field will say AN=6, AC=3, AF=0.5, and DP=100 (in practice, I think these numbers won't necessarily add up perfectly because of some read filters we apply when calling, but it's approximately right).</p>

<p>Now imagine I only want a file with the samples "BOB" and "MARY".  The new file would look like:</p>

<pre class="code codeBlock" spellcheck="false">BOB        MARY
1/0:20     0/0:30
</pre>

<p>The INFO field will now have to change to reflect the state of the new data.  It will be AN=4, AC=1, AF=0.25, DP=50.</p>

<p>Let's pretend that MARY's genotype wasn't 0/0, but was instead "./." (no genotype could be ascertained).  This would look like</p>

<pre class="code codeBlock" spellcheck="false">BOB        MARY
1/0:20     ./.:.
</pre>

<p>with AN=2, AC=1, AF=0.5, and DP=20.</p>

<hr></hr><h3>Additional information</h3>

<p>For information on how to construct regular expressions for use with this tool, see the method article on <a rel="nofollow" href="https://www.broadinstitute.org/gatk/guide/article?id=1255">variant filtering with JEXL</a>, or "Summary of regular-expression constructs" section <a rel="nofollow" href="http://docs.oracle.com/javase/1.4.2/docs/api/java/util/regex/Pattern.html">here</a> for more hardcore reading.</p>

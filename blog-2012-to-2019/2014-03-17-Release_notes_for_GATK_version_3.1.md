## Release notes for GATK version 3.1

By ebanks

<p>GATK 3.1 was released on March 18, 2014.  Highlights are listed below.  Read the detailed version history overview here: <a href="http://www.broadinstitute.org/gatk/guide/version-history" rel="nofollow">http://www.broadinstitute.org/gatk/guide/version-history</a></p>

<hr></hr><h2>Haplotype Caller</h2>

<ul><li>Added new capabilities to the Haplotype Caller to use hardware-based optimizations. Can be enabled with <code class="code codeInline" spellcheck="false">--pair_hmm_implementation VECTOR_LOGLESS_CACHING</code>. Please see the 3.1 Version Highlights for more details about expected speed ups and some background on the collaboration that made these possible.</li>
<li>Fixed bugs in computing the weights of edges in the assembly graph.  This was causing bad genotypes to be output when running the Haplotype Caller over multiple samples simultaneously (as opposed to creating gVCFs in the new recommended pipeline, which was working as expected).</li>
</ul><h2>Variant Recalibrator</h2>

<ul><li>Fixed issue where output could be non-deterministic with very large data sets.</li>
</ul><h2>CalculateGenotypePosteriors</h2>

<ul><li>Fixed several bugs where bad input were causing the tool to crash instead of gracefully exiting with an error message.</li>
</ul><h2>Miscellaneous</h2>

<ul><li>RandomlySplitVariants can now output splits comprised of more than 2 output files.</li>
<li>FastaAlternateReferenceMaker can now output heterozygous sites using IUPAC ambiguity encoding.</li>
<li>Picard, Tribble, and Variant jars updated to version 1.109.1722.</li>
</ul>
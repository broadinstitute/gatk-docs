## Release notes for GATK version 2.5

By ebanks

<p>GATK 2.5 was released on April 30, 2013. Highlights are listed below. Read the detailed version history overview here: <a href="http://www.broadinstitute.org/gatk/guide/version-history" rel="nofollow">http://www.broadinstitute.org/gatk/guide/version-history</a></p>

<h2>Reduce Reads</h2>

<ul><li>DRASTIC improvements in the compression algorithm plus myriad bug fixes.  Too many to list here; see detailed version history for more information.</li>
</ul><h2>Unified Genotyper</h2>

<ul><li>Fixed bug for indel calling with really long reads (assigning the wrong genotypes).</li>
<li>Automatic contamination fixing now works on reduced reads.</li>
<li>Fixed rare bug in the general ploidy SNP likelihood model when there are no informative reads in a pileup.</li>
<li>Fixed bug where haplotypes with 0 bases were being created.</li>
<li>Fixed problem where our internal PairHMM was generating positive likelihoods.</li>
</ul><h2>Haplotype Caller</h2>

<ul><li>Comprehensive performance improvements to the accuracy of calling both SNPs and indels; runtime is also much improved (but still slower than the Unified Genotyper; we expect it to be faster than UG in the next release though).  See detailed version history for more information.</li>
<li>Fixed bug for calling on reduced reads (counts were not being assigned correctly).</li>
<li>Fixed problem where our internal PairHMM was generating positive likelihoods.</li>
<li>Can now write BAMs showing the assembled haplotypes.</li>
</ul><h2>Diagnose Targets</h2>

<ul><li>Significantly refactored this tool; it now works with a "plugin" system (see documentation for more information).</li>
<li>Fixed bug where LOW_MEDIAN_COVERAGE was output when no reads are covering the interval.</li>
<li>Fixed bug where intervals were skipped when they were not covered by any reads.</li>
</ul><h2>Base Recalibrator</h2>

<ul><li>Fixed the tool to work correctly with empty BQSR tables.</li>
<li>Fixed issue where Print Reads was running out of disk space when using the -BQSR option even for small bam files.</li>
<li>Fixed bug for RNA seq alignments with Ns.</li>
</ul><h2>Select Variants</h2>

<ul><li>Fixed bug where using the --exclude_sample_file argument was giving bad results.</li>
<li>Fixed bug when using the --keepOriginalAC argument which caused it to emit bad VCFs.</li>
<li>Fixed bug where maxIndelSize argument wasn't getting applied to deletions.</li>
</ul><h2>Variant Annotator</h2>

<ul><li>Added support for snpEff "GATK compatibility mode".</li>
<li>Can now list available annotations by doing <code class="code codeInline" spellcheck="false">java -cp GenomeAnalysisTK.jar org.broadinstitute.sting.tools.ListAnnotations</code></li>
<li>QualByDepth remaps QD values &gt; 40 to a gaussian around 30.</li>
<li>Removed several deprecated annotations (AverageAltAlleleLength, MappingQualityZeroFraction, and TechnologyComposition) and others are no longer marked as experimental.</li>
</ul><h2>Variant Filtration</h2>

<ul><li>Don't allow users to specify keys and IDs that contain angle brackets or equals signs (which are not allowed in the VCF specification).</li>
<li>Added feature that allows one to filter sites outside of a given mask.</li>
</ul><h2>Left Align Variants</h2>

<ul><li>Renamed to LeftAlignAndTrimVariants.</li>
<li>Added ability to trim common bases in front of indels before left-aligning.</li>
<li>Added ability to split multiallelic records and then left align them.</li>
</ul><h2>Miscellaneous</h2>

<ul><li>We removed the auto-creation of fai/dict files for fasta references because it was too buggy.</li>
<li>Fixed bug where we could fail to find the intersection of unsorted/missorted interval lists.</li>
<li>Fixed <a href="https://gatkforums.broadinstitute.org/gatk/profile/PG" rel="nofollow">@PG</a> tag uniqueness issue with BAMs we were producing.</li>
<li>Fixed rare bug in GenotypeConcordance for multi-allelic sites.</li>
<li>Added check for reads without stored bases (i.e. that use '*') which we do not support.</li>
<li>Added support to reduce reads to CallableLoci.</li>
<li>Added a new walker to split MNPs into their allelic primitives (SNPs).</li>
<li>We no longer allow the use of compressed (.gz) references in the GATK.</li>
<li>Picard/Tribble/Variant jars updated to version 1.90.1442.</li>
</ul>
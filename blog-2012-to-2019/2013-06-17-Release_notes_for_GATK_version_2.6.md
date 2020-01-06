## Release notes for GATK version 2.6

By ebanks

<p>GATK 2.6 was released on June 20, 2013. Highlights are listed below. Read the detailed version history overview here: <a href="http://www.broadinstitute.org/gatk/guide/version-history" rel="nofollow">http://www.broadinstitute.org/gatk/guide/version-history</a></p>

<p><strong>Important note: with this release the GATK has officially moved to using Java 7.</strong></p>

<h2>Reduce Reads</h2>

<ul><li>Small runtime performance improvements contributed by Michael McCowan.</li>
<li>Added fix for the "Removed too many insertions, header is now negative" bug.</li>
<li>Fixed bug that arises in multi-sample mode and causes the tool to crash.</li>
<li>Added --cancer_mode argument to force the user to explicitly enable multi-sample mode.</li>
</ul><h2>Unified Genotyper</h2>

<ul><li>Runtime performance improvements when calling indels; calling indels in a single sample is almost 2x faster in our tests.</li>
<li>Fixed bug for bad AD values in some cases.</li>
<li>Fixed bug for GENOTYPE_GIVEN_ALLELES mode where it silently fails to genotype indels in some cases.</li>
</ul><h2>Haplotype Caller</h2>

<ul><li>We have been working hard to reduce the number of false negatives (i.e. missed sites) for the Haplotype Caller and as such added a bunch of improvements to this tool.  The sensitivity is now better than that of the Unified Genotyper is all of our whole genome tests for both SNPs and indels.  Feel free to peruse the detailed version history for more information.</li>
<li>The Haplotype Caller now annotates IDs from dbSNP properly.</li>
<li>The Haplotype Caller now emits per-sample DP.</li>
<li>Fixed bug for bad AD values in some cases.</li>
<li>Fixed bug with error: "Only one of refStart or refStop must be &lt; 0, not both" that arose from soft-clipped reads at the beginning of contigs.</li>
<li>Implemented a much improved version of GENOTYPE_GIVEN_ALLELES mode in the Haplotype Caller that works so much better.</li>
</ul><h2>Indel Realigner</h2>

<ul><li>Fixed bug where secondary alignments were not being handled correctly.</li>
</ul><h2>Genotype Concordance</h2>

<ul><li>Added an overall genotype concordance metric to the output.</li>
<li>Fixed a bug in the printout of molten data in how it treated the genotypes.</li>
</ul><h2>Diagnose Targets</h2>

<ul><li>Diagnose Targets now has an option to output missing intervals.</li>
<li>Fixed bug where sometimes intervals were emitted out of order.</li>
</ul><h2>Base Recalibrator</h2>

<ul><li>Fixed bug for reads with indel CIGAR operators (I or D) at the start/end of the read.</li>
<li>Introduced a new tool, AnalyzeCovariates, to generate the BQSR quality assessment plots as a separate step, instead of doing it through the BaseRecalibrator.</li>
</ul><h2>Combine Variants</h2>

<ul><li>We no longer add PASS to the FILTER field of unfiltered records.</li>
</ul><h2>Variant Annotator</h2>

<ul><li>The RMSMappingQuality annotation now works properly with reduced reads.</li>
<li>The various rank sum tests no longer use reduced reads in their calculations (because those reads do not represent distinct observations).</li>
<li>Fixed bug in the BaseQualityRankSumTest annotation where it was not actually using the base qualities.</li>
<li>Added a new annotation DepthPerSampleHC that is used by default in the HaplotypeCaller.</li>
</ul><h2>Miscellaneous</h2>

<ul><li>James Warren contributed a patch to have references with non-suffix ".fa" parse correctly.</li>
<li>We now emit the GATK version number in the header of VCFs that we produce.</li>
<li>Fixed bug in the up front downsampling used by the GATK: reduced reads are no longer allowed to be eliminated during downsampling.</li>
<li>dbSNP rsID matching is now smarter: variants are considered matching if they have the same reference allele and at least 1 common alternative allele.</li>
<li>We now warn users about using the GATK with RNA-seq data.</li>
<li>We now check that -compress arguments are within allowable range 0-9.</li>
<li>-rf ReassignMappingQuality can now be used to reassign mapping qualities to 60 before the engine filters them out with MappingQualityUnassigned.</li>
<li>Fixed bug where requesting gzip VCF output with multi-threading was causing the GATK to fail.</li>
<li>We now require a minimum -dcov value of 200 for Locus and ActiveRegion walkers when downsampling to coverage.</li>
<li>Zero-length and repeated cigar elements are collapsed down by default in the engine.</li>
<li>-ds option removed from PrintReads because it was redundant with the engine-level -dfrac argument.</li>
<li>Fixed bug where the --defaultBaseQualities argument didn't always work.</li>
<li>The engine now produces much more accurate read counts for Read traversals.</li>
<li>Count Reads now uses a Long instead of an Integer for counts to prevent overflows.</li>
<li>Locus Walkers now only try to clip adaptors when both reads of the pair are on opposite strands.</li>
<li>Fixed VCF issue where PLs were capped at 32767.</li>
<li>Picard/Tribble/Variant jars updated to version 1.91.1453.</li>
</ul>
## Release notes for GATK version 2.3

By ebanks

<p>GATK 2.3 was released on December 17, 2012. Highlights are listed below. Read the detailed version history overview here: <a href="http://www.broadinstitute.org/gatk/guide/version-history" rel="nofollow">http://www.broadinstitute.org/gatk/guide/version-history</a></p>

<h2>Base Quality Score Recalibration</h2>

<ul><li>Soft clipped bases are no longer counted in the delocalized BQSR.</li>
<li>The user can now set the maximum allowable cycle with the --maximum_cycle_value argument.</li>
</ul><h2>Unified Genotyper</h2>

<ul><li>Minor (5%) run time improvements to the Unified Genotyper.</li>
<li>Fixed bug for the indel model that occurred when long reads (e.g. Sanger) in a pileup led to a read starting after the haplotype.</li>
<li>Fixed bug in the exact AF calculation where log10pNonRefByAllele should really be log10pRefByAllele.</li>
</ul><h2>Haplotype Caller</h2>

<ul><li>Fixed the performance of GENOTYPE_GIVEN_ALLELES mode, which often produced incorrect output when passed complex events.</li>
<li>Fixed the interaction with the allele biased downsampling (for contamination removal) so that the removed reads are not used for downstream annotations.</li>
<li>Implemented minor (5-10%) run time improvements to the Haplotype Caller.</li>
<li>Fixed the logic for determining active regions, which was a bit broken when intervals were used in the system.</li>
</ul><h2>Variant Annotator</h2>

<ul><li>The FisherStrand annotation ignores reduced reads (because they are always on the forward strand).</li>
<li>Can now be run multi-threaded with -nt argument.</li>
</ul><h2>Reduce Reads</h2>

<ul><li>Fixed bug where sometime the start position of a reduced read was less than 1.</li>
<li>ReduceReads now co-reduces bams if they're passed in toghether with multiple -I.</li>
</ul><h2>Combine Variants</h2>

<ul><li>Fixed the case where the PRIORITIZE option is used but no priority list is given.</li>
</ul><h2>Phase By Transmission</h2>

<ul><li>Fixed bug where the AD wasn't being printed correctly in the MV output file.</li>
</ul><h2>Miscellaneous</h2>

<ul><li>A brand new version of the per site down-sampling functionality has been implemented that works much, much better than the previous version.</li>
<li>More efficient initial file seeking at the beginning of the GATK traversal.</li>
<li>Fixed the compression of VCF.gz where the output was too big because of unnecessary call to flush().</li>
<li>The allele biased downsampling (for contamination removal) has been rewritten to be smarter; also, it no longer aborts if there's a reduced read in the pileup.</li>
<li>Added a major performance improvement to the GATK engine that stemmed from a problem with the NanoSchedule timing code.</li>
<li>Added checking in the GATK for mis-encoded quality scores.</li>
<li>Fixed downsampling in the ReadBackedPileup class.</li>
<li>Fixed the parsing of genome locations that contain colons in the contig names (which is allowed by the spec).</li>
<li>Made ID an allowable INFO field key in our VCF parsing.</li>
<li>Multi-threaded VCF to BCF writing no longer produces an invalid intermediate file that fails on merging.</li>
<li>Picard jar remains at version 1.67.1197.</li>
<li>Tribble jar updated to version 119.</li>
</ul>
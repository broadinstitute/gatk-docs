## Release notes for GATK version 2.8

By ebanks

<p>GATK 2.8 was released on December 6, 2013. Highlights are listed below. Read the detailed version history overview here: <a href="http://www.broadinstitute.org/gatk/guide/version-history" rel="nofollow">http://www.broadinstitute.org/gatk/guide/version-history</a></p>

<p><strong>Note that this release is relatively smaller than previous ones.  We are working hard on some new tools and frameworks that we are hoping to make available to everyone for our next release.</strong></p>

<hr></hr><h2>Unified Genotyper</h2>

<ul><li>Fixed bug where indels in very long reads were sometimes being ignored and not used by the caller.</li>
</ul><h2>Haplotype Caller</h2>

<ul><li>Improved the indexing scheme for gVCF outputs using the reference calculation model.</li>
<li>The reference calculation model now works with reduced reads.</li>
<li>Fixed bug where an error was being generated at certain homozygous reference sites because the whole assembly graph was getting pruned away.</li>
<li>Fixed bug for homozygous reference records that aren't GVCF blocks and were being treated incorrectly.</li>
</ul><h2>Variant Recalibrator</h2>

<ul><li>Disable tranche plots in INDEL mode.</li>
<li>Various VQSR optimizations in both runtime and accuracy.  Some particular details include: for very large whole genome datasets with over 2M variants overlapping the training data randomly downsample the training set that gets used to build; annotations are ordered by the difference in means between known and novel instead of by their standard deviation; removed the training set quality score threshold; now uses 2 gaussians by default for the negative model; numBad argument has been removed and the cutoffs are now chosen by the model itself by looking at the LOD scores.</li>
</ul><h2>Reduce Reads</h2>

<ul><li>Fixed bug where mapping quality was being treated as a byte instead of an int, which caused high MQs to be treated as negative.</li>
</ul><h2>Diagnose Targets</h2>

<ul><li>Added calculation for GC content.</li>
<li>Added an option to filter the bases based on their quality scores.</li>
</ul><h2>Combine Variants</h2>

<ul><li>Fixed bug where annotation values were parsed as Doubles when they should be parsed as Integers due to implicit conversion; submitted by Michael McCowan.</li>
</ul><h2>Select Variants</h2>

<ul><li>Changed the behavior for PL/AD fields when it encounters a record that has lost one or more alternate alleles: instead of stripping them out these fields now get fixed.</li>
</ul><h2>Miscellaneous</h2>

<ul><li>SplitSamFile now produces an index with the BAM.</li>
<li>Length metric updates to QualifyMissingIntervals.</li>
<li>Provide close methods to clean up resources used while creating AlignmentContexts from BAM file regions; submitted by Brad Chapman.</li>
<li>Picard jar updated to version 1.104.1628.</li>
<li>Tribble jar updated to version 1.104.1628.</li>
<li>Variant jar updated to version 1.104.1628.</li>
</ul>
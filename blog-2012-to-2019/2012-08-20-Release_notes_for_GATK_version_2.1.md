## Release notes for GATK version 2.1

By ebanks

<h2>Base Quality Score Recalibration</h2>

<ul><li>Multi-threaded support in the BaseRecalibrator tool has been temporarily suspended for performance reasons; we hope to have this fixed for the next release.</li>
<li>Implemented support for SOLiD no call strategies other than throwing an exception.</li>
<li>Fixed smoothing in the BQSR bins.</li>
<li>Fixed plotting R script to be compatible with newer versions of R and ggplot2 library.</li>
</ul><h2>Unified Genotyper</h2>

<ul><li>Renamed the per-sample ML allelic fractions and counts so that they don't have the same name as the per-site INFO fields, and clarified the description in the VCF header.</li>
<li>UG now makes use of base insertion and base deletion quality scores if they exist in the reads (output from BaseRecalibrator).</li>
<li>Changed the -maxAlleles argument to -maxAltAlleles to make it more accurate.</li>
<li>In pooled mode, if haplotypes cannot be created from given alleles when genotyping indels (e.g. too close to contig boundary, etc.) then do not try to genotype.</li>
<li>Added improvements to indel calling in pooled mode: we compute per-read likelihoods in reference sample to determine whether a read is informative or not.</li>
</ul><h2>Haplotype Caller</h2>

<ul><li>Added LowQual filter to the output when appropriate.</li>
<li>Added some support for calling on Reduced Reads.  Note that this is still experimental and may not always work well.</li>
<li>Now does a better job of capturing low frequency branches that are inside high frequency haplotypes.</li>
<li>Updated VQSR to work with the MNP and symbolic variants that are coming out of the HaplotypeCaller.</li>
<li>Made fixes to the likelihood based LD calculation for deciding when to combine consecutive events.</li>
<li>Fixed bug where non-standard bases from the reference would cause errors.</li>
<li>Better separation of arguments that are relevant to the Unified Genotyper but not the Haplotype Caller.</li>
</ul><h2>Reduce Reads</h2>

<ul><li>Fixed bug where reads were soft-clipped beyond the limits of the contig and the tool was failing with a NoSuchElement exception.</li>
<li>Fixed divide by zero bug when downsampler goes over regions where reads are all filtered out.</li>
<li>Fixed a bug where downsampled reads were not being excluded from the read window, causing them to trail back and get caught by the sliding window exception.</li>
</ul><h2>Variant Eval</h2>

<ul><li>Fixed support in the AlleleCount stratification when using the MLEAC (it is now capped by the AN).</li>
<li>Fixed incorrect allele counting in IndelSummary evaluation.</li>
</ul><h2>Combine Variants</h2>

<ul><li>Now outputs the first non-MISSING QUAL, instead of the maximum.</li>
<li>Now supports multi-threaded running (with the -nt argument).</li>
</ul><h2>Select Variants</h2>

<ul><li>Fixed behavior of the --regenotype argument to do proper selecting (without losing any of the alternate alleles).</li>
<li>No longer adds the DP INFO annotation if DP wasn't used in the input VCF.</li>
<li>If MLEAC or MLEAF is present in the original VCF and the number of samples decreases, remove those annotations from the output VC (since they are no longer accurate).</li>
</ul><h2>Miscellaneous</h2>

<ul><li>Updated and improved the BadCigar read filter.</li>
<li>GATK now generates a proper error when a gzipped FASTA is passed in.</li>
<li>Various improvements throughout the BCF2-related code.</li>
<li>Removed various parallelism bottlenecks in the GATK.</li>
<li>Added support of X and = CIGAR operators to the GATK.</li>
<li>Catch NumberFormatExceptions when parsing the VCF POS field.</li>
<li>Fixed bug in FastaAlternateReferenceMaker when input VCF has overlapping deletions.</li>
<li>Fixed AlignmentUtils bug for handling Ns in the CIGAR string.</li>
<li>We now allow lower-case bases in the REF/ALT alleles of a VCF and upper-case them.</li>
<li>Added support for handling complex events in ValidateVariants.</li>
<li>Picard jar remains at version 1.67.1197.</li>
<li>Tribble jar remains at version 110.</li>
</ul>
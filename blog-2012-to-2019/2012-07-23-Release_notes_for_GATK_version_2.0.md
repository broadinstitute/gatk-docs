## Release notes for GATK version 2.0

By ebanks

<p>The GATK 2.0 release includes both the addition of brand-new (and often still experimental) tools and updates to the existing stable tools.</p>

<h2>New Tools</h2>

<ul><li>Base Recalibrator (BQSR v2), an upgrade to CountCovariates/TableRecalibration that generates base substitution, insertion, and deletion error models.</li>
<li>Reduce Reads, a BAM compression algorithm that reduces file sizes by 20x-100x while preserving all information necessary for accurate SNP and indel calling. ReduceReads enables the GATK to call tens of thousands of deeply sequenced NGS samples simultaneously.</li>
<li>HaplotypeCaller, a multi-sample local de novo assembly and integrated SNP, indel, and short SV caller.</li>
<li>Plus powerful extensions to the Unified Genotyper to support variant calling of pooled samples, mitochondrial DNA, and non-diploid organisms. Additionally, the extended Unified Genotyper introduces a novel error modeling approach that uses a reference sample to build a site-specific error model for SNPs and indels that vastly improves calling accuracy.</li>
</ul><h2>Base Quality Score Recalibration</h2>

<ul><li>IMPORTANT: the Count Covariates and Table Recalibration tools (which comprise BQSRv1) have been retired!  Please see the BaseRecalibrator tool (BQSRv2) for running recalibration with GATK 2.0.</li>
</ul><h2>Unified Genotyper</h2>

<ul><li>Handle exception generated when non-standard reference bases are present in the fasta.</li>
<li>Bug fix for indels: when checking the limits of a read to clip, it wasn't considering reads that may already have been clipped before.</li>
<li>Now emits the MLE AC and AF in the INFO field.</li>
<li>Don't allow N's in insertions when discovering indels.</li>
</ul><h2>Phase By Transmission</h2>

<ul><li>Multi-allelic sites are now correctly ignored.</li>
<li>Reporting of mendelian violations is enhanced.</li>
<li>Corrected TP overflow.</li>
<li>Fixed bug that arose when no PLs were present.</li>
<li>Added option to output the father's allele first in phased child haplotypes.</li>
<li>Fixed a bug that caused the wrong phasing of child/father pairs.</li>
</ul><h2>Variant Eval</h2>

<ul><li>Improvements to the validation report module: if eval has genotypes and comp has genotypes, then subset the genotypes of comp down to the samples being evaluated when considering TP, FP, FN, TN status.</li>
<li>If present, the AlleleCount stratification uses the MLE AC by default (and otherwise drops down to use the greedy AC).</li>
<li>Fixed bugs in the VariantType and IndelSize stratifications.</li>
</ul><h2>Variant Annotator</h2>

<ul><li>FisherStrand annotation no longer hard-codes in filters for bases/reads (previously used MAPQ &gt; 20 &amp;&amp; QUAL &gt; 20).</li>
<li>Miscellaneous bug fixes to experimental annotations.</li>
<li>Added a Clipping Rank Sum Test to detect when variants are present on reads with differential clipping.</li>
<li>Fixed the ReadPos Rank Sum Test annotation so that it no longer uses the un-hardclipped start as the alignment start.</li>
<li>Fixed bug in the NBaseCount annotation module.</li>
<li>The new TandemRepeatAnnotator is now a standard annotation while HRun has been retired.</li>
<li>Added PED support for the Inbreeding Coefficient annotation.</li>
<li>Don't compute QD if there is no QUAL.</li>
</ul><h2>Variant Quality Score Recalibration</h2>

<ul><li>The VCF index is now created automatically for the recalFile.</li>
</ul><h2>Variant Filtration</h2>

<ul><li>Now allows you to run with type unsafe JEXL selects, which all default to false when matching.</li>
</ul><h2>Select Variants</h2>

<ul><li>Added an option which allows the user to re-genotype through the exact AF calculation model (if PLs are present) in order to recalculate the QUAL and genotypes.</li>
</ul><h2>Combine Variants</h2>

<ul><li>Added --mergeInfoWithMaxAC argument to keep info fields from the input with the highest AC value.</li>
</ul><h2>Somatic Indel Detector</h2>

<ul><li>GT header line is now output.</li>
</ul><h2>Indel Realigner</h2>

<ul><li>Automatically skips Ion reads just like it does with 454 reads.</li>
</ul><h2>Variants To Table</h2>

<ul><li>Genotype-level fields can now be specified.</li>
<li>Added the --moltenize argument to produce molten output of the data.</li>
</ul><h2>Depth Of Coverage</h2>

<ul><li>Fixed a NullPointerException that could occur if the user requested an interval summary but never provided a -L argument.</li>
</ul><h2>Miscellaneous</h2>

<ul><li>BCF2 support in tools that output VCFs (use the .bcf extension).</li>
<li>The GATK Engine no longer automatically strips the suffix "Walker" after the end of tool names; as such, all tools whose name ended with "Walker" have been renamed without that suffix.</li>
<li>Fixed bug when specifying a JEXL expression for a field that doesn't exist: we now treat the whole expression as false (whereas we were rethrowing the JEXL exception previously).</li>
<li>There is now a global --interval_padding argument that specifies how many basepairs to add to each of the intervals provided with -L (on both ends).</li>
<li>Removed all code associated with extended events.</li>
<li>Algorithmically faster version of DiffEngine.</li>
<li>Better down-sampling fixes edge case conditions that used to be handled poorly.  Read Walkers can now use down-sampling.</li>
<li>GQ is now emitted as an int, not a float.</li>
<li>Fixed bug in the Beagle codec that was skipping the first line of the file when decoding.</li>
<li>Fixed bug in the VCF writer in the case where there are no genotypes for a record but there are genotypes in the header.</li>
<li>Miscellaneous fixes to the VCF headers being produced.</li>
<li>Fixed up the BadCigar read filter.</li>
<li>Removed the old deprecated genotyping framework revolving around the misordering of alleles.</li>
<li>Extensive refactoring of the GATKReports.</li>
<li>Picard jar updated to version 1.67.1197.</li>
<li>Tribble jar updated to version 110.</li>
</ul>
## Release notes for GATK version 2.4

By ebanks

<p>GATK 2.4 was released on February 26, 2013. Highlights are listed below. Read the detailed version history overview here: <a href="http://www.broadinstitute.org/gatk/guide/version-history" rel="nofollow">http://www.broadinstitute.org/gatk/guide/version-history</a></p>

<p><strong>Important note 1 for this release: with this release comes an updated licensing structure for the GATK.  Different files in our public repository are protected with different licenses, so please see the text at the top of any given file for details as to its particular license.</strong></p>

<p><strong>Important note 2 for this release: the GATK team spent a tremendous amount of time and engineering effort to add extensive tests for many of our core tools (a process that will continue into future releases).  Unsurprisingly, as part of this process many small (and some not so small) bugs were uncovered during testing that we subsequently fixed.  While we usually attempt to enumerate in our release notes all of the bugs fixed during a given release, that would entail quite a Herculean effort for release 2.4; so please just be aware that there were many smaller fixes that may be omitted from these notes.</strong></p>

<h2>Base Quality Score Recalibration</h2>

<ul><li>The underlying calculation of the recalibration has been improved and generalized so that the empirical quality is now calculated through a Bayesian estimate.  This radically improves the accuracy in particular for bins with small numbers of observations.</li>
<li>Added many run time improvements so that this tool now runs much faster.</li>
<li>Print Reads writes a header when used with the -BQSR argument.</li>
<li>Added a check to make sure that BQSR is not being run on a reduced bam (which would be bad).</li>
<li>The --maximum_cycle_value argument can now be specified during the Print Reads step to prevent problems when running on bams with extremely long reads.</li>
<li>Fixed bug where reads with an existing BQ tag and soft-clipped bases could cause the tool to error out.</li>
</ul><h2>Unified Genotyper</h2>

<ul><li>Fixed the QUAL calculation for monomorphic (homozygous reference) sites (the math for previous versions was not correct).</li>
<li>Biased downsampling (i.e. contamination removal) values can now be specified as per-sample fractions.</li>
<li>Fixed bug where biased downsampling (i.e. contamination removal) was not being performed correctly in the presence of reduced reads.</li>
<li>The indel likelihoods calculation had several bugs (e.g. sometimes the log likelihoods were positive!) that manifested themselves in certain situations and these have all been fixed.</li>
<li>Small run time improvements were added.</li>
</ul><h2>Haplotype Caller</h2>

<ul><li>Extensive performance improvements were added to the Haplotype Caller.  This includes run time enhancements (it is now <strong>much</strong> faster than previous versions) plus improvements in accuracy for both SNPs and indels.  Internal assessment now shows the Haplotype Caller calling variants more accurately than the Unified Genotyper.  The changes for this tool are so extensive that they cannot easily be enumerated in these notes.</li>
</ul><h2>Variant Annotator</h2>

<ul><li>The QD annotation is now divided by the average length of the alternate allele (weighted by the allele count); this does not affect SNPs but makes the calculation for indels much more accurate.</li>
<li>Fixed Fisher Strand annotation where p-values sometimes summed to slightly greater than 1.0.</li>
<li>Fixed Fisher Strand annotation for indels where reduced reads were not being handled correctly.</li>
<li>The Haplotype Score annotation no longer applies to indels.</li>
<li>Added the Variant Type annotation (not enabled by default) to annotate the VCF record with the variant type.</li>
<li>The DepthOfCoverage annotation has been renamed to Coverage.</li>
</ul><h2>Reduce Reads</h2>

<ul><li>Several small run time improvements were added to make this tool slightly faster.</li>
<li>By default this tool now uses a downsampling value of 40x per start position.</li>
</ul><h2>Indel Realigner</h2>

<ul><li>Fixed bug where some reads with soft clipped bases were not be realigned.</li>
</ul><h2>Combine Variants</h2>

<ul><li>Run time performance improvements added where one uses the PRIORITIZE or REQUIRE_UNIQUE options.</li>
</ul><h2>Select Variants</h2>

<ul><li>The --regenotype functionality has been removed from SelectVariants and transferred into its own tool: RegenotypeVariants.</li>
</ul><h2>Variant Eval</h2>

<ul><li>Removed the GenotypeConcordance evaluation module (which had many bugs) and converted it into its own tested, standalone tool (called GenotypeConcordance).</li>
</ul><h2>Miscellaneous</h2>

<ul><li>The VariantContext and related classes have been moved out of the GATK codebase and into Picard's public repository.  The GATK now uses the variant.jar as an external library.</li>
<li>Added a new Read Filter to reassign just a particular mapping quality to another one (see the ReassignOneMappingQualityFilter).</li>
<li>Added the Regenotype Variants tool that allows one to regenotype a VCF file (which must contain likelihoods in the PL field) after samples have been added/removed.</li>
<li>Added the Genotype Concordance tool that calculates the concordance of one VCF file against another.</li>
<li>Bug fix for VariantsToVCF for records where old dbSNP files had '-' as the reference base.</li>
<li>The GATK now automatically converts IUPAC bases in the reference to Ns and errors out on other non-standard characters.</li>
<li>Fixed bug for the DepthOfCoverage tool which was not counting deletions correctly.</li>
<li>Added Cat Variants, a standalone tool to quickly combine multiple VCF files whose records are non-overlapping (e.g. as produced during scatter-gather).</li>
<li>The Somatic Indel Detector has been removed from our codebase and moved to the Broad Cancer group's private repository.</li>
<li>Fixed Validate Variants rsID checking which wasn't working if there were multiple IDs.</li>
<li>Picard jar updated to version 1.84.1337.</li>
<li>Tribble jar updated to version 1.84.1337.</li>
<li>Variant jar updated to version 1.85.1357.</li>
</ul>
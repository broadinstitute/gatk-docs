## Release notes for GATK version 3.5

By Geraldine_VdAuwera

<p>GATK 3.5 was released on November 25, 2015. Itemized changes are listed below. For more details, see the user-friendly version highlights.</p>

<hr></hr><h3>New tools</h3>

<ul><li>MuTect2: somatic SNP and indel caller based on HaplotypeCaller and the original MuTect.</li>
<li>ContEst: estimation of cross-sample contamination (primarily for use in somatic variant discovery).</li>
<li>GatherBqsrReports: utility to gather recalibration tables from scatter-parallelized BaseRecalibrator runs.</li>
</ul><hr></hr><h3>Variant Context Annotations</h3>

<ul><li><p>Added allele-specific version of existing annotations: AS_BaseQualityRankSumTest, AS_FisherStrand, AS_MappingQualityRankSumTest, AS_RMSMappingQuality, AS_RankSumTest, <br>
AS_ReadPosRankSumTest, AS_StrandOddsRatio, AS_QualByDepth and AS_InbreedingCoeff.</p></li>
<li><p>Added BaseCountsBySample annotation. Intended to provide insight into the pileup of bases used by HaplotypeCaller in the calling process, which may differ from the pileup observed in the original bam file because of the local realignment and additional filtering performed internally by HaplotypeCaller. Can only be requested from HaplotypeCaller, not VariantAnnotator.</p></li>
<li><p>Added ExcessHet annotation. Estimates excess heterozygosity in a population of samples. Related to but distinct from InbreedingCoeff, which estimates evidence for inbreeding in a population. ExcessHet scales more reliably to large cohort sizes.</p></li>
<li><p>Added FractionInformativeReads annotation. Reports the number of reads that were considered informative by HaplotypeCaller (over all samples).</p></li>
<li><p>Enforced calculating GenotypeAnnotations before InfoFieldAnnotations. This ensures that the AD value is available to use in the QD calculation.</p></li>
<li><p>Reorganized standard annotation groups processing to ensure that all default annotations always get annotated regardless of what is specified on the command line. This fixes a bug where default annotations were getting dropped when the command line included annotation requests.</p></li>
<li><p>Made GenotypeGVCFs subset StrandAlleleCounts intelligently, i.e. subset the SAC values to the called alleles. Previously, when the StrandAlleleCountsBySample (SAC) annotation was present in GVCFs, GenotypeGVCFs carried it over to the final VCF essentially unchanged. This was problematic because SAC includes the counts for all alleles originally present (including NON-REF) even when some are not called in the final VCF. When the full list of original alleles is no longer available, parsing SAC could become difficult if not impossible.</p></li>
<li><p>Added new MQ jittering functionality to improve how VQSR handles MQ. Note that HaplotypeCaller now calculates a new annotation called RAW_MQ per-sample, which is then integrated per-cohort by GenotypeGVCFs to produce the MQ annotation.</p></li>
<li><p>VariantAnnotator can now annotate FILTER field from an external resource. Usage: <code class="code codeInline" spellcheck="false">--resource:foo resource.vcf --expression foo.FILTER</code></p></li>
<li><p>VariantAnnotator can now check allele concordance when annotating with an external resource. Usage: <code class="code codeInline" spellcheck="false">--resourceAlleleConcordance</code></p></li>
<li><p>Bug fix: The annotation framework was improved to allow for the collection of sufficient statistics during GVCF creation which are then used to compute the final annotation during the genotyping. This avoids the use of median as the representative annotation from the collection of values (one from each sample). TL;DR annotations will be more accurate when using the GVCF workflow for joint discovery.</p></li>
</ul><hr></hr><h3>Variant manipulation tools</h3>

<ul><li><p>Allowed overriding hard-coded cutoff for allele length in ValidateVariants and in LeftAlignAndTrimVariants. Usage: <code class="code codeInline" spellcheck="false">--reference_window_stop N</code> where N is the desired cutoff.</p></li>
<li><p>Also in LeftAlignAndTrimVariants, trimming multiallelic alleles is now the default behavior.</p></li>
<li><p>Fixed ability to mask out snps with <code class="code codeInline" spellcheck="false">--snpmask</code> in FastaAlternateReferenceMaker.</p></li>
<li><p>Also in FastaAlternateReferenceMaker, fixed merging of contiguous intervals properly, and made the tool produce more informative contig names.</p></li>
<li><p>Fixed a bug in CombineVariants that occurred when one record has a spanning deletion and needs a padded reference allele.</p></li>
<li><p>Added a new VariantEval evaluation module, MetricsCollection, that summarizes metrics from several EV modules.</p></li>
<li><p>Enabled family-level stratification in MendelianViolationEvaluator of VariantEval (if a ped file is provided), making it possible to count Mendelian violations for each family in a callset with multiple families.</p></li>
<li><p>Added the ability to SelectVariants to enforce 4.2 version output of the VCF spec when processing older files. Use case: the 4.2 spec specifies that GQ must be an integer; by default we don’t enforce it (so if reading an older file that used decimals, we don’t change it) but the new argument <code class="code codeInline" spellcheck="false">--forceValidOutput</code> converts the values on request. Not made default because of some performance slowdown -- so writing VCFs is now fast by default, compliant by choice.</p></li>
<li><p>Improved VCF sequence dictionary validation. Note that as a side effect of the additional checks, some users have experienced an error that starts with "ERROR MESSAGE: Lexicographically sorted human genome sequence detected in variant." that is due to unintentional activation of a check that is not necessary. This will be fixed in the next release; in the meantime <code class="code codeInline" spellcheck="false">-U ALLOW_SEQ_DICT_INCOMPATIBILITY</code> can be used (with caution) to override the check.</p></li>
</ul><hr></hr><h3>GVCF tools</h3>

<ul><li><p>Various improvements to the tools’ performance, especially HaplotypeCaller, by making the code more efficient and cutting out crud.</p></li>
<li><p>GenotypeGVCFs now emits a no-call (./.) when the evidence is too ambiguous to make a call at all (e.g. all the PLs are zero). Previously this would have led to a hom-ref call with RGQ=0.</p></li>
<li><p>Fixed a bug in GenotypeGVCFs that sometimes generated invalid VCFs for haploid callsets. The tool was carrying over the AD from alleles that had been trimmed out, causing field length mismatches.</p></li>
<li><p>Changed the genotyping implementation for haploid organisms to address performance problems reported when running GenotypeGVCFs on haploid callsets. Note that this change may lead to a slight loss of sensitivity at low-coverage sites -- let us know if you observe anything dramatic.</p></li>
</ul><hr></hr><h3>Genotyping engine tweaks</h3>

<ul><li><p>Ensured inputPriors get used if they are specified to the genotyper (previously they were ignored). Also improved docs on <code class="code codeInline" spellcheck="false">--heterozygosity</code> and <code class="code codeInline" spellcheck="false">--indel_ heterozygosity</code> priors.</p></li>
<li><p>Fixed bug that affected the <code class="code codeInline" spellcheck="false">--ignoreInputSamples</code> behavior of CalculateGenotypePosteriors.</p></li>
<li><p>Limited emission of the scary warning message about max number of alleles (“this tool is set to genotype at most x alleles but we found more; only x will be used”) to a single occurrence unless DEBUG logging mode is activated.  Otherwise it fills up our output logs.</p></li>
</ul><hr></hr><h3>Miscellaneous tool fixes</h3>

<ul><li><p>Added option to OverclippedReadFilter to not require soft-clips on both ends. Contributed by Jacob Silterra.</p></li>
<li><p>Fixed a bug in IndelRealigner where the tool was incorrectly "fixing" mates when supplementary alignments are present. The patch involves ignoring supplementary alignments.</p></li>
<li><p>Fixed a bug in CatVariants. Previously, VCF files were being sorted solely on the base pair position of the first record, ignoring the chromosome. This can become problematic when merging files from different chromosomes, especially if you have multiple VCFs per chromosome. Contributed by John Wallace.</p></li>
</ul><hr></hr><h3>Engine-level behaviors and capabilities</h3>

<ul><li><p>Support for reading and writing CRAM files. Some improvements are still expected in htsjdk. Contributed by Vadim Zalunin at EBI and collaborators at the Sanger Institute.</p></li>
<li><p>Made interval-list output format dependent on the file extension (for RealignerTargetCreator). If the extension is <code class="code codeInline" spellcheck="false">.interval_list</code>, output will be formatted as a proper Picard interval list (with sequence dictionary). Otherwise it will be a basic GATK interval list as previously.</p></li>
<li><p>Adding static binning capability for base recalibration (BQSR).</p></li>
</ul><hr></hr><h3>Queue</h3>

<ul><li><p>Added a new JobRunner called ParallelShell that will run jobs locally on one node concurrently as specified by the DAG, with the option to limit the maximum number of concurrently running jobs using the flag <code class="code codeInline" spellcheck="false">maximumNumberOfJobsToRunConcurrently</code>. Contributed by Johan Dahlberg.</p></li>
<li><p>Updated extension for Picard CalculateHsMetrics to include <code class="code codeInline" spellcheck="false">PER_TARGET_COVERAGE</code> argument and added extension for Picard CollectWgsMetrics.</p></li>
</ul><hr></hr><h3>Deprecation notice</h3>

<p>Removed:</p>

<ul><li>BeagleOutputToVCF, VariantsToBeagleUnphased, ProduceBeagleInput. These are tools for handling Beagle data. The latest versions of Beagle support VCF input and output, so there is no longer any reason for us to provide converters.</li>
<li>ReadAdaptorTrimmer and VariantValidationAssessor. These were experimental tools which we think are not useful and not operating on a sufficiently sound basis.</li>
<li>BaseCoverageDistribution and CoveredByNSamplesSites. These tools were redundant with DiagnoseTargets and/or DepthOfCoverage.</li>
<li>LiftOverVariants, FilterLiftedVariants and liftOverVCF.pl. The Picard liftover tool LiftoverVCF works better and is easier to operate.</li>
<li>sortByRef.pl. Use Picard SortVCF instead.</li>
<li>ListAnnotations. This was intended as a utility for listing annotations easily from command line, but it has not proved useful.</li>
</ul><hr></hr><h3>Meta</h3>

<ul><li>Made various documentation improvements.</li>
<li>Updated date and street address in license text.</li>
<li>Moved htsjdk &amp; picard to version 1.141</li>
</ul>
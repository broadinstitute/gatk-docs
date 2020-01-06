## Release notes for GATK version 3.7

By Geraldine_VdAuwera

<p>GATK 3.7 was released on December 12, 2016. Itemized changes are listed below. For more details, see the user-friendly <a rel="nofollow" href="https://software.broadinstitute.org/gatk/blog?id=8692">version highlights</a>.</p>

<hr></hr><h3>HaplotypeCaller + GGVCFs</h3>

<ul><li>39da22b - Changes to use the median rather than the second best likelihood for the NON_REF allele</li>
<li>a8797f2 - Fixed merging of GVCF blocks by fixing rounding of GQ values in ReferenceConfidenceModel</li>
<li>ce4ed1f - Remove NON_REF from allSites VCF output</li>
<li>ba21b22 - Do not emit GVCF block definitions in the header of the final VCF emitted by GenotypeGVCFs</li>
<li>6670d12 - Changed maximum allowed GQB value to 100</li>
<li>e7bd143 - Added exception for GQB values greater than MAX_GENOTYPE_QUAL and tests</li>
<li>9ae9b26 - Deprecate -stand_emit_conf</li>
<li>04a70bb - Remove -stand_emit_conf argument</li>
<li>5b8bf1c - Change default value of STANDARD_CONFIDENCE_FOR_CALLING to 10</li>
<li>a8db074 - Backport new AFCalculator</li>
<li>ad3e4f4 - Backport numerics changes in new qual</li>
<li>dc0fa3f - Fixes NaN issue in new Qual calculator</li>
</ul><hr></hr><h3>MuTect2</h3>

<ul><li>d7f1a9c - Lots of small improvements to Mutect2 code</li>
<li>408d31d - More small refactorings of Mutect2 code</li>
<li>ff1e3a3 - Finish porting MuTect1 clustered read position filter</li>
<li>ce7d4bd - Port the strand bias filter from M1 and refactored code around SomaticGenotypingEngine; added a new integration test</li>
<li>1350c0e - Add new annotator for M1 clustered read position filter and M1 strand bias filter</li>
<li>095a469 - Cleaned up SomaticGenotypingEngine::callMutations and added some TODOs</li>
<li>e6d0318 - Expose downsampling arguments in Mutect</li>
</ul><hr></hr><h3>Allele prioritization and culling</h3>

<ul><li>a557ff3 - RCM Variant sites merger won't output PL when there are too many alleles in order to avoid memory issues with large cohort runs</li>
<li>7296dbf - Remove alt alleles, when genotype count is explosively large, based on alleles' highest supporting haplotype score; max tolerable genotype count is controlled by a default value overridable by user</li>
<li>5b09639 - Impose a maximum allele list message length</li>
<li>7cd8a66 - Make sure that multi-allelic uninformative PLs (0,0,...,0) stay uninformative after biallelization</li>
<li>f182fc1 - Make alt allele removal by likelihoods robust to ref allele indices</li>
<li>1709765 - Fixed a max priority Q error while removing alt alleles when faced with high ploidy and allele count</li>
</ul><hr></hr><h3>Misc annotations</h3>

<ul><li>1f4fa57 - Change to max value of ExcessHet</li>
<li>186d616 - Fix for int overflow in RankSum calculation</li>
<li>5931311 - Remove RankSumTest and RMSAnnotation from hom-ref sites</li>
<li>4c1365f - BaseCountsBySample counting bases at a particular position</li>
<li>f0874d1 - Bypass spanning deletions in Rank Sum tests</li>
<li>a327c24 - Makes Fisher's exact test match R and GATK4 results</li>
<li>92a5aad - Fixed logic error and tidied AlleleBalance and AlleleBalanceBySample</li>
</ul><hr></hr><h3>Misc tools</h3>

<ul><li>41c9fed - Add read group identifier to column names in ReadLengthDistribution</li>
<li>d96c02b - Allows GatherBqsrReports to accept a .list file as input</li>
<li>22a3008 - SelectVariants works with non-diploids</li>
<li>0dd96ac - Added TreeReduce interface to VariantFiltration</li>
<li>b85fea3 - Fix for genotype filters issue in VariantFiltration</li>
<li>4341a2e - Assorted documentation fixes</li>
</ul><hr></hr><h3>Engine options</h3>

<ul><li>69af359 - Added option to merge GenomeLocs that are abutting (contiguous) rather than actually overlapping</li>
<li>541243b - Make interval padding work for "exclude intervals"</li>
<li>4e4ac94 - Enable control of reporting periodicity</li>
<li>68f1822 - Set HTSJDK log level</li>
<li>359f078 - Throw an exception if the BQSR input covariates file is not found</li>
</ul><hr></hr><h3>Under the hood</h3>

<ul><li>975c17a - Throw an exception for invalid Picard intervals</li>
<li>ce1d4c8 - Upgrade Apache Commons Collections to version 3.2.2</li>
<li>355d053 - Move htsjdk and picard to version 2.5.0</li>
<li>08b8eab - Move htsjdk to ver 2.6.1 and picard to ver 2.6.0</li>
<li>4d45102 - Move htsjdk to ver 2.8.1 and picard to ver 2.7.2</li>
<li>f410451 - Change HashMap to LinkedHashMap for predictable ordering</li>
<li>5fac5c8 - Added support for directly reading SRA runs</li>
<li>a5cc81a - Remove SRA group</li>
<li>899453b - Write saved WARN messages to stderr instead of stdout</li>
<li>bed804d - Replace SAMFileReader with calls to SamReaderFactory</li>
<li>a48d36d - Assign correct ambiguity code for * allele</li>
<li>496564f - Removed spanning deletions if the deletion was removed when subsetting</li>
<li>a37886f - Fix adapter boundary for positive strand in handling of overlapping read pairs</li>
<li>f6b18c8 - Fix issue where VCF files with "bcf" in the name were output to BCF</li>
<li>4d6d207 - Replace VariantContextWriterFactory with VariantContextWriterBuilder</li>
<li>6b8740e - Make exit system file type message generic</li>
</ul><hr></hr><h3>Tests</h3>

<ul><li>e6d34af - Make ReadPosRankSumTest.isUsableRead() account for deletions</li>
<li>e4786ed - Added regression test for genotyping of spanning deletions in GenotypeGCVFs</li>
<li>f709898 - Add integration test using -maxNumPLValues for GenotypeGVCFs</li>
<li>dfcec64 - Fix BetaTestingAnnotation group Add test</li>
<li>f7ff6b8 - Change a truth VCF in VQSR tests</li>
<li>d5ad2f0 - Make getElementForRead() in RankSumTest robust</li>
</ul>
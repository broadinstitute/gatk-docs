## Release notes for GATK version 3.4

By Geraldine_VdAuwera

<p>GATK 3.4 was released on May 15, 2015. Itemized changes are listed below. For more details, see the user-friendly version highlights.</p>

<hr></hr><h3>New tool</h3>

<ul><li>ASEReadCounter: A tool to count read depth in a way that is appropriate for allele specific expression (ASE) analysis. It counts the number of reads that support the REF allele and the ALT allele, filtering low qual reads and bases and keeping only properly paired reads. See Highlights for more details.</li>
</ul><h3>HaplotypeCaller &amp; GenotypeGVCFs</h3>

<ul><li>Important fix for genotyping positions over spanning deletions. Previously, if a SNP occurred in sample A at a position that was in the middle of a deletion for sample B, sample B would be genotyped as homozygous reference there (but it's NOT reference - there's a deletion). Now, sample B is genotyped as having a symbolic DEL allele. See Highlights for more details.</li>
<li>Deprecated <code class="code codeInline" spellcheck="false">--mergeVariantsViaLD</code> argument in HaplotypeCaller since it didn’t work. To merge complex substitutions, use ReadBackedPhasing as a post-processing step.</li>
<li>Removed exclusion of MappingQualityZero, SpanningDeletions and TandemRepeatAnnotation from the list of annotators that cannot be annotated by HaplotypeCaller. These annotations are still not recommended for use with HaplotypeCaller, but this is no longer enforced by a hardcoded ban.</li>
<li>Clamp the HMM window starting coordinate to 1 instead of 0 (contributed by nsubtil).</li>
<li>Fixed the implementation of <code class="code codeInline" spellcheck="false">allowNonUniqueKmersInRef</code> so that it applies to all kmer sizes. This resolves some assembly issues in low-complexity sequence contexts and improves calling sensitivity in those regions.</li>
<li>Initialize annotations so that <code class="code codeInline" spellcheck="false">--disableDithering</code> actually works.</li>
<li>Automatic selection of indexing strategy based on <code class="code codeInline" spellcheck="false">.g.vcf</code> file extension. See Highlights for more details.</li>
<li>Removed normalization of QD based on length for indels. Length-based normalization is now only applied if the annotation is calculated in UnifiedGenotyper.</li>
<li>Added the RGQ (Reference GenotypeQuality) FORMAT annotation to monomorphic sites in the VCF output of GenotypeGVCFs.  Now, instead of stripping out the GQs for monomorphic ohm-ref sites, we transfer them to the RGQ. This is extremely useful for people who want to know how confident the hom-ref genotype calls are. See Highlights for more details.</li>
<li>Removed GenotypeSummaries from default annotations.</li>
<li>Added <code class="code codeInline" spellcheck="false">-uniquifySamples</code> to GenotypeGVCFs to make it possible to genotype together two different datasets containing the same sample.</li>
<li>Disallow changing <code class="code codeInline" spellcheck="false">-dcov</code> setting for HaplotypeCaller (pending a fix to the downsampling control system) to prevent buggy behavior. See Highlights for more details.</li>
<li>Raised per-sample limits on the number of reads in ART and HC. Active Region Traversal was using per sample limits on the number of reads that were too low, especially now that we are running one sample at a time. This caused issues with high confidence variants being dropped in high coverage data.</li>
<li>Removed explicit limitation (20) of the maximum ploidy of the reference-confidence model. Previously there was a fixed-size maximum ploidy indel RCM likelihood cache; this was changed to a dynamically resizable one. There are still some de facto limitations which can be worked around by lowering the max alt alleles parameter.</li>
<li>Made GQ of Hom-Ref Blocks in GVCF output be consistent with PLs.</li>
<li>Fixed a bug where HC was not realigning against the reference but against the best haplotype for the read.</li>
<li>Fixed a bug (in HTSJDK) that was causing GenotypeGVCFs to choke on sites with large numbers of alternate alleles (&gt;140).</li>
<li>Modified the way GVCFBlock header lines are named because the new HTSJDK version disallows duplicate header keys (aside from special-cased keys such as INFO and FORMAT).</li>
</ul><h3>CombineGVCFs</h3>

<ul><li>Added option to break blocks at every N sites. Using <code class="code codeInline" spellcheck="false">--breakBandsAtMultiplesOf N</code> will ensure that no reference blocks span across genomic positions that are multiples of N.  This is especially important in the case of scatter-gather where you don't want your scatter intervals to start in the middle of blocks (because of a limitation in the way <code class="code codeInline" spellcheck="false">-L</code> works in the GATK for VCF records with the END tag). See Highlights for more details.</li>
<li>Fixed a bug that caused the tool to stop processing after the first contig.</li>
<li>Fixed a bug where the wrong REF allele was output to the combined gVCF.</li>
</ul><h3>VariantRecalibrator</h3>

<ul><li>Switched VQSR tranches plot ordering rule (ordering is now based on tranche sensitivity instead of novel titv).</li>
<li>VQSR VCF header command line now contains annotations and tranche levels.</li>
</ul><h3>SelectVariants</h3>

<ul><li>Added <code class="code codeInline" spellcheck="false">-trim</code> argument to trim (simplify) alleles to a minimal representation.</li>
<li>Added <code class="code codeInline" spellcheck="false">-trimAlternates</code> argument to remove all unused alternate alleles from variants.  Note that this is pretty aggressive for monomorphic sites.</li>
<li>Changed the default behavior to trim (remove) remaining alleles when samples are subset, and added the <code class="code codeInline" spellcheck="false">-noTrim</code> argument to preserve original alleles.</li>
<li>Added <code class="code codeInline" spellcheck="false">--keepOriginalDP</code> argument.</li>
</ul><h3>VariantAnnotator</h3>

<ul><li>Improvements to the allele trimming functionalities.</li>
<li>Added functionality to support multi-allelic sites when annotating a VCF with annotations from another callset. See Highlights for more details.</li>
</ul><h3>CalculateGenotypePosteriors</h3>

<ul><li>Fixed user-reported bug featuring "trio" family with two children, one parent.</li>
<li>Added error handling for genotypes that are called but have no PLs.</li>
</ul><h3>Various tools</h3>

<ul><li>BQSR: Fixed an issue where GATK would skip the entire read if a SNP is entirely contained within a sequencing adapter (contributed by nsubtil); and improved how uncommon platforms (as encoded in RG:PL tag) are handled.</li>
<li>DepthOfCoverage: Now logs a warning if incompatible arguments are specified.</li>
<li>SplitSamFile: Fixed a bug that caused a NullPointerException.</li>
<li>SplitNCigarReads: Fixed issue to make <code class="code codeInline" spellcheck="false">-fixNDN</code> flag fully functional.</li>
<li>IndelRealigner: Fixed an issue that was due to reads that have an incorrect CIGAR length.</li>
<li>CombineVCFs: Minor change to an error check that was put into 3.3 so that identical samples don't need <code class="code codeInline" spellcheck="false">-genotypeMergeOption</code>.</li>
<li>VariantsToBinaryPED: Corrected swap between mother and father in PED file output.</li>
<li>GenotypeConcordance: Monomorphic sites in the truth set are no longer called "Mismatching Alleles" when the comp genotype has an alternate allele.</li>
<li>ReadBackedPhasing: Fixed a couple of bugs in MNP merging.</li>
<li>CatVariants: Now allows different input / output file types, and spaces in directory names.</li>
<li>VariantsToTable: Fixed a bug that affected the output of the FORMAT record lists when <code class="code codeInline" spellcheck="false">-SMA</code> is specified. Note that FORMAT fields behave the same as INFO fields - if the annotation has a count of A (one entry per Alt Allele), it is split across the multiple output lines.  Otherwise, the entire list is output with each field.</li>
</ul><h3>Read Filters</h3>

<ul><li>Added erroneous CIGAR length to criteria for BadCigarFilter.</li>
<li>Corrected logical expression in MateSameStrandFilter (contributed by user seru71).</li>
<li>Handle X and = CIGAR operators appropriately</li>
<li>Added <code class="code codeInline" spellcheck="false">-drf</code> argument to disable default read filters. Limited to specific tools and specific filters (currently only DuplicateReadFilter).</li>
</ul><h3>Annotations</h3>

<ul><li>Calculate StrandBiasBySample using all alternate alleles as “REF vs. any ALT”.</li>
<li>Modified InbreedingCoeff so that it works when genotyping uniquified samples (see GenotypeGVCFs changes).</li>
<li>Changed GC Content value type from Integer to Float.</li>
<li>Added StrandAlleleCountsBySample annotation. This annotation outputs the number of reads supporting each allele, stratified by sample and read strand; callable from HaplotypeCaller only.</li>
<li>Made annotators emit a warning if they can't be applied.</li>
</ul><h3>GATK Engine &amp; common features</h3>

<ul><li>Fixed logging of 'out' command line parameter in VCF headers; changed []-type arrays to lists so argument parsing works in VCF header commandline output.</li>
<li>Modified GATK command line header for unique keys. The GATK command line header keys were being repeated in the VCF and subsequently lost to a single key value by HTSJDK.  This resolves the issue by appending the name of the walker after the text "GATKCommandLine" and a number after that if the same walker was used more than once in the form: GATKCommandLine.(walker name) for the first occurrence of the walker, and GATKCommandLine.(walker name).# where # is the number of the occurrence of the walker (e.g. GATKCommandLine.SomeWalker.2 for the second occurrence of SomeWalker).</li>
<li>Handle X and = CIGAR operators appropriately.</li>
<li>Added barebones read/write CRAM support (no interval seeking!). See Highlights for more details.</li>
<li>Cleaned up logging outputs / streams; messages (including HMM log messages) that were going to stdout now going to stderr.</li>
<li>Improved error messages; when an error is related to a specific file, the engine now includes the file name in the error message.</li>
<li>Fixed BCF writing when FORMAT annotations contain arrays.</li>
</ul><h3>Queue</h3>

<ul><li>Added <code class="code codeInline" spellcheck="false">-qsub-broad</code> argument. When -qsub-broad is specified instead of <code class="code codeInline" spellcheck="false">-qsub</code>, Queue will use the <code class="code codeInline" spellcheck="false">h_vmem</code> parameter instead of <code class="code codeInline" spellcheck="false">h_rss</code> to specify memory limit requests. This was done to accommodate changes to the Broad’s internal job scheduler. Also causes the GridEngine native arguments to be output by default to the logger, instead of only when in debug mode.</li>
<li>Fixed the scala wrapper for Picard MarkDuplicates (needed because MarkDuplicates was moved to a different package within Picard).</li>
<li>Added optional element "includeUnmapped" to the PartitionBy annotation. The value of this element (default true) determines whether Queue will explicitly run this walker over unmapped reads. This patch fixes a runtime error when FindCoveredIntervals was used with Queue.</li>
</ul><h3>Documentation</h3>

<ul><li>Plentiful enhancements and fixes to various tool docs, especially annotations and read filters.</li>
</ul><h3>For developers</h3>

<ul><li>Upgraded SLF4J to allow new convenient logging syntaxes.</li>
<li>Patched maven pom file for <code class="code codeInline" spellcheck="false">slf4j-log4j12</code> version (contributed by user Biocyberman).</li>
<li>Updated HTSJDK version (now pulling it in from Maven Central); various edits made to match.</li>
<li>Collected VCF IDs and header lines into one place (GATKVCFConstants).</li>
<li>Made various changes that lead to reduced build times.</li>
</ul>
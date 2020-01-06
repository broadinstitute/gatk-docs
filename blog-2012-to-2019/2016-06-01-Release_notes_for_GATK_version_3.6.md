## Release notes for GATK version 3.6

By Geraldine_VdAuwera

<p>GATK 3.6 was released on June 1, 2016. Itemized changes are listed below. For more details, see the user-friendly <a rel="nofollow" href="https://www.broadinstitute.org/gatk/blog?id=7712">version highlights</a>.</p>

<hr></hr><h3>Variant calling features</h3>

<ul><li><p>HaplotypeCaller will now emit a no-call (./.) for any sample where GQ is zero, in both normal and GVCF modes, instead of emitting a specific genotype in which we have zero confidence.</p></li>
<li><p>GenotypeGVCFs will now emit a QUAL value for hom-ref sites when run in <code class="code codeInline" spellcheck="false">-allSites</code> mode.</p></li>
<li><p>Implemented tracking of dropped reads by HaplotypeCaller and MuTect2 (see highlights for details).</p></li>
<li><p>Assorted optimizations to the joint calling code, expected to speed up genotyping (not the overall tool run) by about 10 percent.</p></li>
<li><p>Enabled MuTect2 to annotate all the same regular (non-AS) annotations as HaplotypeCaller on request.</p></li>
</ul><hr></hr><h3>Assorted new functionality</h3>

<ul><li>New ranksum annotations (allele-specific insert size and MQ of mate).</li>
<li>New <code class="code codeInline" spellcheck="false">-AS</code> mode to run VQSR in an allele-specific manner (both VariantRecalibrator and ApplyRecalibration) (still experimental).</li>
<li>VariantRecalibrator can now output the recalibration model to a file (in GATKReport format — use the R library gsalib for reading).</li>
<li>Added ability to have VariantRecalibrator retry building the recalibration model if it fails initially. Meant as a workaround for runs on small datasets that fail randomly because the model isn't robust enough. Default behavior remains a single try. Contributed by <a href="https://gatkforums.broadinstitute.org/gatk/profile/depristo" rel="nofollow">@depristo</a> / Mark DePristo.</li>
<li>ValidateVariants can now perform validation checks specific to GVCFs with the option <code class="code codeInline" spellcheck="false">--gvcf</code>.</li>
<li>VariantsToTable now determines each allele's type when <code class="code codeInline" spellcheck="false">-F</code> TYPE and <code class="code codeInline" spellcheck="false">-SMA</code> are specified together.</li>
<li>LeftAlignAndTrimVariants now retains genotypes that remain valid after splitting with <code class="code codeInline" spellcheck="false">—splitMultiallelics</code> (previously all were discarded).</li>
<li>SelectVariants can now select sites based on the number or fraction of samples that have no-call genotypes (./.) using <code class="code codeInline" spellcheck="false">—maxNOCALLnumber</code> and <code class="code codeInline" spellcheck="false">—maxNOCALLfraction</code>, respectively.</li>
<li>DepthOfCoverage now supports collecting coverage statistics for overlapping exons/genes. Contributed by <a href="https://gatkforums.broadinstitute.org/gatk/profile/seru71" rel="nofollow">@seru71</a> / Pawel Sztromwasser.</li>
</ul><hr></hr><h3>Assorted bug fixes</h3>

<ul><li>Handling of allele depths when the NON_REF allele is non-zero (see highlights for details)</li>
<li>A sample ploidy check that may have minor performance implications</li>
<li>Threshold evaluation in the max alt alleles filter of MuTect2</li>
<li>MQ annotation calculation when processing BP resolution GVCFs</li>
<li>RankSum calculations on small sample sizes</li>
<li>PrintReads’ ability to emit a <a href="https://gatkforums.broadinstitute.org/gatk/profile/PG" rel="nofollow">@PG</a> header record</li>
<li>Writing GVCFs to stdout instead of to file</li>
<li>Order of column headers in sample_gene_summary reports output by DepthOfCoverage</li>
<li>MNP-merging behavior of ReadBackedPhasing: treatment of spanning deletions and consecutive SNPs</li>
<li>SelectVariants and VariantFiltration’s ability to update genotype summary annotations (AC, AN and AF)</li>
<li>Subsetting alleles from StrandAlleleCountsBySample annotation</li>
</ul><hr></hr><h3>Workarounds for weird sites</h3>

<ul><li><p>Added an argument to HaplotypeCaller and GenotypeGVCFs, <code class="code codeInline" spellcheck="false">-maxNumPLValues</code>, that controls the maximum number of PL values that can be emitted for a given site. If the number of PLs resulting from the combination of observed alleles and ploidy exceeds this value, no PLs will be emitted. This will cause subsetting errors in SelectVariants but empowers the user to identify and work around difficult sites where this happens.</p></li>
<li><p>Extended the functionality of the engine-level argument <code class="code codeInline" spellcheck="false">—reference_window_stop</code> to set the reference window size used by VariantAnnotator when annotating hompolymers through the HomopolymerRun annotation. This makes it possible to deal with the problem of homopolymer stretches that are longer than the default window size.</p></li>
</ul><hr></hr><h3>Deleted functionality</h3>

<ul><li>Removed Phone Home usage tracking system (see highlights for details)</li>
<li>Deprecated GenotypeAndValidate tool which was massively outdated and had no unit or integration tests</li>
</ul><hr></hr><h3>Tools moved to the open-source core of GATK</h3>

<ul><li>IndelRealigner and RealignerTargetCreator</li>
<li>Post-IR MQ reverter filter to public</li>
<li>Moved BQSRGatherer and dependencies to the public module</li>
</ul><hr></hr><h3>Core / engine functionality</h3>

<ul><li>Enabled Java 8 support (see highlights for details)</li>
<li>Updated htsjdk &amp; picard to version 2.4.1</li>
<li>Tweaks to the genome coordinates parsing system and contig names to support the Hg38 reference</li>
<li>Assorted improvements in the handling of errors, warnings and log output. The engine will now output a summary of WARN messages encountered during a run so you don’t have to parse the full log to see if anything worrying-but-not-fatal happened.</li>
</ul><hr></hr><h3>Queue</h3>

<ul><li><p>Expose time between checks for whether new jobs can be submitted as a user-settable parameter on CLi. Useful when testing pipelines to make idle time shorter. Contributed by <a href="https://gatkforums.broadinstitute.org/gatk/profile/dakl" rel="nofollow">@dakl</a> / Daniel Klevebring.</p></li>
<li><p>Remove <code class="code codeInline" spellcheck="false">mem_free</code> from resident memory request params for Queue because it doesn't work and wouldn't actually reserve memory.</p></li>
</ul><hr></hr><h3>Tool documentation</h3>

<ul><li>Improvements and clarifications to many tool docs</li>
<li>Refreshed organization and naming of tool categories</li>
<li>Fixed display of default values for arguments</li>
<li>Switched default doc output to html to make the tool docs provided for nightly builds more readable</li>
</ul>
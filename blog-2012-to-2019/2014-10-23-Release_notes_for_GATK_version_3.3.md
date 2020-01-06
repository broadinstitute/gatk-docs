## Release notes for GATK version 3.3

By Geraldine_VdAuwera

<p>GATK 3.3 was released on October 23, 2014.  Itemized changes are listed below.  For more details, see the <a rel="nofollow" href="http://www.broadinstitute.org/gatk/guide/version-history">user-friendly version highlights</a>.</p>

<hr></hr><h2>Haplotype Caller</h2>

<ul><li>Improved the accuracy of dangling head merging in the HC assembler (now enabled by default).</li>
<li>Physical phasing information is output by default in new sample-level PID and PGT tags.</li>
<li>Added the <code class="code codeInline" spellcheck="false">--sample_name</code> argument. This is a shortcut for people who have multi-sample BAMs but would like to use <code class="code codeInline" spellcheck="false">-ERC GVCF</code> mode with a particular one of those samples.</li>
<li>Support added for generalized ploidy. The global ploidy is specified with the <code class="code codeInline" spellcheck="false">-ploidy</code> argument.</li>
<li>Fixed IndexOutOfBounds error associated with tail merging.</li>
</ul><h2>Variant Recalibrator</h2>

<ul><li>New <code class="code codeInline" spellcheck="false">--ignore_all_filters</code> option. If specified, the variant recalibrator will ignore all input filters and treat sites as unfiltered.</li>
</ul><h2>GenotypeGVCFs</h2>

<ul><li>Support added for generalized ploidy. The global ploidy is specified with the <code class="code codeInline" spellcheck="false">-ploidy</code> argument.</li>
<li>Bug fix for the case when we assumed ADs were in the same order if the number of alleles matched.</li>
<li>Changed the default GVCF GQ Bands from 5,20,60 to be 1..60 by 1s, 60...90 by 10s and 99 in order to give finer resolution.</li>
<li>Bug fix in the exact model when calling multi-allelic variants. QUAL field is now more accurate.</li>
</ul><h2>RNAseq analysis</h2>

<ul><li>Bug fixes for working with unmapped reads.</li>
</ul><h2>CalculateGenotypePosteriors</h2>

<ul><li>New annotation for low- and high-confidence possible de novos (only annotates biallelics).</li>
<li>FamilyLikelihoodsUtils now add joint likelihood and joint posterior annotations.</li>
<li>Restricted population priors based on discovered allele count to be valid for 10 or more samples.</li>
</ul><h2>DepthOfCoverage</h2>

<ul><li>Fixed rare bug triggered by hash collision between sample names.</li>
</ul><h2>SelectVariants</h2>

<ul><li>Updated the <code class="code codeInline" spellcheck="false">--keepOriginalAC functionality</code> in SelectVariants to work for sites that lose alleles in the selection.</li>
</ul><h2>PrintReads</h2>

<ul><li>Read groups that are excluded by <code class="code codeInline" spellcheck="false">sample_name</code>, <code class="code codeInline" spellcheck="false">platform</code>, or <code class="code codeInline" spellcheck="false">read_group</code> arguments no longer appear in the header.</li>
<li>The performance penalty associated with filtering by read group has been essentially eliminated.</li>
</ul><h2>Annotations</h2>

<ul><li>StrandOddsRatio is now a standard annotation that is output by default.</li>
<li>We used to output zero for FS if there was no data available at a site, now we omit FS.</li>
<li>Extensive rewrite of the annotation documentation.</li>
</ul><h2>Queue</h2>

<ul><li>Fixed Queue bug with bad localhost addresses.</li>
<li>Fixed issue related to spaces in job names that were fine in GridEngine 6 but break in (Son of) GE8.</li>
<li>Improved scatter contigs algorithm to be fairer when splitting many contigs into few parts (contributed by <a href="https://gatkforums.broadinstitute.org/gatk/profile/smowton%29" rel="nofollow">@smowton)</a></li>
</ul><h2>Documentation</h2>

<ul><li>We now generate PHP files instead of HTML.</li>
<li>We now output a JSON version of the tool documentation that can be used to generate wrappers for GATK commands.</li>
</ul><h2>Miscellaneous</h2>

<ul><li>Output arguments <code class="code codeInline" spellcheck="false">--no_cmdline_in_header</code>, <code class="code codeInline" spellcheck="false">--sites_only</code>, and <code class="code codeInline" spellcheck="false">--bcf</code> for VCF files, and <code class="code codeInline" spellcheck="false">--bam_compression</code>, <code class="code codeInline" spellcheck="false">--simplifyBAM</code>, <code class="code codeInline" spellcheck="false">--disable_bam_indexing</code>, and <code class="code codeInline" spellcheck="false">--generate_md5</code> for BAM files moved to the engine level.</li>
<li>htsjdk updated to version 1.120.1620</li>
</ul>
## Release notes for GATK version 3.2

By ebanks

<p>GATK 3.2 was released on July 14, 2014.  Itemized changes are listed below.  For more details, see the <a rel="nofollow" href="http://www.broadinstitute.org/gatk/guide/version-history">user-friendly version highlights</a>.</p>

<hr></hr><p>We also want to take this opportunity to thank super-user Phillip Dexheimer for all of his excellent contributions to the codebase, especially for this release.</p>

<hr></hr><h2>Haplotype Caller</h2>

<ul><li>Various improvements were made to the assembly engine and likelihood calculation, which leads to more accurate genotype likelihoods (and hence better genotypes).</li>
<li>Reads are now realigned to the most likely haplotype before being used by the annotations, so AD and DP will now correspond directly to the reads that were used to generate the likelihoods.</li>
<li>The caller is now more conservative in low complexity regions, which significantly reduces false positive indels at the expense of a little sensitivity; mostly relevant for whole genome calling.</li>
<li>Small performance optimizations to the function to calculate the log of exponentials and to the Smith-Waterman code (thanks to Nigel Delaney).</li>
<li>Fixed small bug where indel discovery was inconsistent based on the active-region size.</li>
<li>Removed scary warning messages for "VectorPairHMM".</li>
<li>Made VECTOR_LOGLESS_CACHING the default implementation for PairHMM.</li>
<li>When we subset PLs because alleles are removed during genotyping we now also subset the AD.</li>
<li>Fixed bug where reference sample depth was dropped in the DP annotation.</li>
</ul><h2>Variant Recalibrator</h2>

<ul><li>The -mode argument is now required.</li>
<li>The plotting script now uses the <code class="code codeInline" spellcheck="false">theme</code> instead of <code class="code codeInline" spellcheck="false">opt</code> functions to work with recent versions of the ggplot2 R library.</li>
</ul><h2>AnalyzeCovariates</h2>

<ul><li>The plotting script now uses the <code class="code codeInline" spellcheck="false">theme</code> instead of <code class="code codeInline" spellcheck="false">opt</code> functions to work with recent versions of the ggplot2 R library.</li>
</ul><h2>Variant Annotator</h2>

<ul><li>SB tables are created even if the ref or alt columns have no counts (used in the FS and SOR annotations).</li>
</ul><h2>Genotype GVCFs</h2>

<ul><li>Added missing arguments so that now it models more closely what's available in the Haplotype Caller.</li>
<li>Fixed recurring error about missing PLs.</li>
<li>No longer pulls the headers from all input rods including dbSNP, rather just from the input variants.</li>
<li>--includeNonVariantSites should now be working.</li>
</ul><h2>Select Variants</h2>

<ul><li>The dreaded "Invalid JEXL expression detected" error is now a kinder user error.</li>
</ul><h2>Indel Realigner</h2>

<ul><li>Now throws a user error when it encounters reads with I operators greater than the number of read bases.</li>
<li>Fixed bug where reads that are all insertions (e.g. 50I) were causing it to fail.</li>
</ul><h2>CalculateGenotypePosteriors</h2>

<ul><li>Now computes posterior probabilities only for SNP sites with SNP priors (other sites have flat priors applied).</li>
<li>Now computes genotype posteriors using likelihoods from all members of the trio.</li>
<li>Added annotations for calling potential de novo mutations.</li>
<li>Now uses PP tag instead of GP tag because posteriors are Phred-scaled.</li>
</ul><h2>Cat Variants</h2>

<ul><li>Can now process .list files with -V.</li>
<li>Can now handle BCF and Block-Compressed VCF files.</li>
</ul><h2>Validate Variants</h2>

<ul><li>Now works with gVCF files.</li>
<li>By default, all strict validations are performed; use --validationTypeToExclude to exclude specific tests.</li>
</ul><h2>FastaAlternateReferenceMaker</h2>

<ul><li>Now use '--use_IUPAC_sample sample_name' to specify which sample's genotypes should be used for the IUPAC encoding with multi-sample VCF files.</li>
</ul><h2>Miscellaneous</h2>

<ul><li><strong>Refactored maven directories and java packages replacing "sting" with "gatk".</strong></li>
<li>Extended on-the-fly sample renaming feature to VCFs with the --sample_rename_mapping_file argument.</li>
<li>Added a new read transformer that refactors NDN cigar elements to one N element.</li>
<li>Now a Tabix index is created for block-compressed output formats.</li>
<li>Switched outputRoot in SplitSamFile to an empty string instead of null (thanks to Carlos Barroto).</li>
<li>Enabled the AB annotation in the reference model pipeline (thanks to John Wallace).</li>
<li>We now check that output files are specified in a writeable location.</li>
<li>We now allow blank lines in a (non-BAM) list file.</li>
<li>Added legibility improvements to the Progress Meter.</li>
<li>Allow for non-tab whitespace in sample names when performing on-the-fly sample-renaming (thanks to Mike McCowan).</li>
<li>Made IntervalSharder respect the IntervalMergingRule specified on the command line.</li>
<li>Sam, tribble, and variant jars updated to version 1.109.1722; htsjdk updated to version 1.112.1452.</li>
</ul>
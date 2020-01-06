## Release notes for GATK version 2.2

By ebanks

<p>GATK release 2.2 was released on October 31, 2012. Highlights are listed below. Read the detailed version history overview here: <a href="http://www.broadinstitute.org/gatk/guide/version-history" rel="nofollow">http://www.broadinstitute.org/gatk/guide/version-history</a></p>

<h2>Base Quality Score Recalibration</h2>

<ul><li>Improved the algorithm around homopolymer runs to use a "delocalized context".</li>
<li>Massive performance improvements that allow these tools to run efficiently (and correctly) in multi-threaded mode.</li>
<li>Fixed bug where the tool failed for reads that begin with insertions.</li>
<li>Fixed bug in the scatter-gather functionality.</li>
<li>Added new argument to enable emission of the .pdf output file (see --plot_pdf_file).</li>
</ul><h2>Unified Genotyper</h2>

<ul><li>Massive runtime performance improvement for multi-allelic sites; -maxAltAlleles now defaults to 6.</li>
<li>The genotyper no longer emits the Stand Bias (SB) annotation by default.  Use the --computeSLOD argument to enable it.</li>
<li>Added the ability to automatically down-sample out low grade contamination from the input bam files using the --contamination_fraction_to_filter argument; by default the value is set at 0.05 (5%).</li>
<li>Fixed annotations (AD, FS, DP) that were miscalculated when run on a Reduce Reads processed bam.</li>
<li>Fixed bug for the general ploidy model that occasionally caused it to choose the wrong allele when there are multiple possible alleles to choose from.</li>
<li>Fixed bug where the inbreeding coefficient was computed at monomorphic sites.</li>
<li>Fixed edge case bug where we could abort prematurely in the special case of multiple polymorphic alleles and samples with drastically different coverage.</li>
<li>Fixed bug in the general ploidy model where it wasn't counting errors in insertions correctly.</li>
<li>The FisherStrand annotation is now computed both with and without filtering low-qual bases (we compute both p-values and take the maximum one - i.e. least significant).</li>
<li>Fixed annotations (particularly AD) for indel calls; previous versions didn't accurately bin reads into the reference or alternate sets correctly.</li>
<li>Generalized ploidy model now handles reference calls correctly.</li>
</ul><h2>Haplotype Caller</h2>

<ul><li>Massive runtime performance improvement for multi-allelic sites; -maxAltAlleles now defaults to 6.</li>
<li>Massive runtime performance improvement to the HMM code which underlies the likelihood model of the HaplotypeCaller.</li>
<li>Added the ability to automatically down-sample out low grade contamination from the input bam files using the --contamination_fraction_to_filter argument; by default the value is set at 0.05 (5%).</li>
<li>Now requires at least 10 samples to merge variants into complex events.</li>
</ul><h2>Variant Annotator</h2>

<ul><li>Fixed annotations for indel calls; previous versions either didn't compute the annotations at all or did so incorrectly for many of them.</li>
</ul><h2>Reduce Reads</h2>

<ul><li>Fixed several bugs where certain reads were either dropped (fully or partially) or registered as occurring at the wrong genomic location.</li>
<li>Fixed bugs where in rare cases N bases were chosen as consensus over legitimate A,C,G, or T bases.</li>
<li>Significant runtime performance optimizations; the average runtime for a single exome file is now just over 2 hours.</li>
</ul><h2>Variant Filtration</h2>

<ul><li>Fixed a bug where DP couldn't be filtered from the FORMAT field, only from the INFO field.</li>
</ul><h2>Variant Eval</h2>

<ul><li>AlleleCount stratification now supports records with ploidy other than 2.</li>
</ul><h2>Combine Variants</h2>

<ul><li>Fixed bug where the AD field was not handled properly.  We now strip the AD field out whenever the alleles change in the combined file.</li>
<li>Now outputs the first non-missing QUAL, not the maximum.</li>
</ul><h2>Select Variants</h2>

<ul><li>Fixed bug where the AD field was not handled properly.  We now strip the AD field out whenever the alleles change in the combined file.</li>
<li>Removed the -number argument because it gave biased results.</li>
</ul><h2>Validate Variants</h2>

<ul><li>Added option to selectively choose particular strict validation options.</li>
<li>Fixed bug where mixed genotypes (e.g. ./1) would incorrectly fail.</li>
<li>improved the error message around unused ALT alleles.</li>
</ul><h2>Somatic Indel Detector</h2>

<ul><li>Fixed several bugs, including missing AD/DP header lines and putting annotations in correct order (Ref/Alt).</li>
</ul><h2>Miscellaneous</h2>

<ul><li>New CPU "nano" parallelization option (-nct) added GATK-wide (see docs for more details about this cool new feature that allows parallelization even for Read Walkers).</li>
<li>Fixed raw HapMap file conversion bug in VariantsToVCF.</li>
<li>Added GATK-wide command line argument (-maxRuntime) to control the maximum runtime allowed for the GATK.</li>
<li>Fixed bug in GenotypeAndValidate where it couldn't handle both SNPs and indels.</li>
<li>Fixed bug where VariantsToTable did not handle lists and nested arrays correctly.</li>
<li>Fixed bug in BCF2 writer for case where all genotypes are missing.</li>
<li>Fixed bug in DiagnoseTargets when intervals with zero coverage were present.</li>
<li>Fixed bug in Phase By Transmission when there are no likelihoods present.</li>
<li>Fixed bug in fasta .fai generation.</li>
<li>Updated and improved version of the BadCigar read filter.</li>
<li>Picard jar remains at version 1.67.1197.</li>
<li>Tribble jar remains at version 110.</li>
</ul>
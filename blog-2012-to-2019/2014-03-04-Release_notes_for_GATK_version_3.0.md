## Release notes for GATK version 3.0

By ebanks

<p>GATK 3.0 was released on March 5, 2014.  Highlights are listed below.  Read the detailed version history overview here: <a href="http://www.broadinstitute.org/gatk/guide/version-history" rel="nofollow">http://www.broadinstitute.org/gatk/guide/version-history</a></p>

<p>One important change for those who prefer to build from source is that we now use maven instead of ant. See the relevant documentation for building the GATK with our new build system.</p>

<hr></hr><h2>SplitNCigarReads</h2>

<ul><li>This is a new GATK tool to be used for variant calling in RNA-seq data.  Its purpose is to split reads that contain N Cigar operators (due to a limitation in the GATK that we will eventually handle internally) and to trim (and generally clean up) imperfect alignments.</li>
</ul><h2>Haplotype Caller</h2>

<ul><li>Fixed bug where dangling tail merging in the assembly graph occasionally created a cycle.</li>
<li>Added experimental code to retrieve dangling heads in the assembly graph, which is needed for calling variants in RNA-seq data.</li>
<li>Generally improved gVCF output by making it more accurate.  This includes many updates so that the single sample gVCFs can be accurately genotyped together by GenotypeGVCFs.</li>
<li>Fixed a bug in the PairHMM class where the transition probability was miscalculated resulting in probabilities larger than 1.</li>
<li>Fixed bug in the function to find the best paths from an alignment graph which was causing bad genotypes to be emitted when running with multiple samples together.</li>
</ul><h2>CombineGVCFs</h2>

<ul><li>This is a new GATK tool to be used in the Haplotype Caller pipeline with large cohorts.  Its purpose is to combine any number of gVCF files into a single merged gVCF.  One would use this tool for hierarchical merges of the data when there are too many samples in the project to throw at all at once to GenotypeGVCFs.</li>
</ul><h2>GenotypeGVCFs</h2>

<ul><li>This is a new GATK tool to be used in the Haplotype Caller pipeline.  Its purpose is to take any number of gVCF files and to genotype them in order to produce a VCF with raw SNP and indel calls.</li>
</ul><h2>SimulateReadsForVariants</h2>

<ul><li>This is a new GATK tool that might be useful to some.  Given a VCF file, this tool will generate simulated reads that support the variants present in the file.</li>
</ul><h2>Unified Genotyper</h2>

<ul><li>Fixed bug when clipping long reads in the HMM; some reads were incorrectly getting clipped.</li>
</ul><h2>Variant Recalibrator</h2>

<ul><li>Added the capability to pass in a single file containing a list of VCFs (must end in ".list") instead of having to enumerate all of the files on the command-line.  Duplicate entries are not allowed in the list (but the same file can be present in separate lists).</li>
</ul><h2>Reduce Reads</h2>

<ul><li>Removed from the GATK.  It was a valiant attempt, but ultimately we found a better way to process large cohorts.  Reduced BAMs are no longer supported in the GATK.</li>
</ul><h2>Variant Annotator</h2>

<ul><li>Improved the FisherStrand (FS) calculation when used in large cohorts.  When the table gets too large, we normalize it down to values that are more reasonable.  Also, we don't include a particular sample's contribution unless we observe both ref and alt counts for it.  We expect to improve on this even further in a future release.</li>
<li>Improved the QualByDepth (QD) calculation when used in large cohorts.  Now, when the AD annotation is present for a given genotype then we only use its depth for QD if the variant depth &gt; 1.  Note that this only works in the gVCF pipeline for now.</li>
<li>In addition, fixed the normalization for indels in QD (which was over-penalizing larger events).</li>
</ul><h2>Combine Variants</h2>

<ul><li>Added the capability to pass in a single file containing a list of VCFs (must end in ".list") instead of having to enumerate all of the files on the command-line.  Duplicate entries are not allowed in the list (but the same file can be present in separate lists).</li>
</ul><h2>Select Variants</h2>

<ul><li>Fixed a huge bug where selecting out a subset of samples while using multi-threading (-nt) caused genotype-level fields (e.g. AD) to get swapped among samples.  This was a bad one.</li>
<li>Fixed a bug where selecting out a subset of samples at multi-allelic sites occasionally caused the alternate alleles to be re-ordered but the AD values were not updated accordingly.</li>
</ul><h2>CalculateGenotypePosteriors</h2>

<ul><li>Fixed bug where it wasn't checking for underflow and occasionally produced bad likelihoods.</li>
<li>It no longer strips out the AD annotation from genotypes.</li>
<li>AC/AF/AN counts are updated after fixing genotypes.</li>
<li>Updated to handle cases where the AC (and MLEAC) annotations are not good (e.g. they are greater than AN somehow).</li>
</ul><h2>Indel Realigner</h2>

<ul><li>Fixed bug where a realigned read can sometimes get partially aligned off the end of the contig.</li>
</ul><h2>Read Backed Phasing</h2>

<ul><li>Updated the tool to use the VCF 4.1 framework for phasing; it now uses HP tags instead of '|' to convey phase information.</li>
</ul><h2>Miscellaneous</h2>

<ul><li>Thanks to Phillip Dexheimer for several Queue related fixes and patches.</li>
<li>Thanks to Nicholas Clarke for patches to the timer which occasionally had negative elapsed times.</li>
<li>Providing an empty BAM list no results in a user error.</li>
<li>Fixed a bug in the gVCF writer where it was dropping the first few reference blocks at the beginnings of all but the first chromosome.  Also, several unnecessary INFO field annotations were dropped from the output.</li>
<li>Logger output now goes to STDERR instead of STDOUT.</li>
<li>Picard, Tribble, and Variant jars updated to version 1.107.1683.</li>
</ul>
## Improvements in Germline Short Variants Calling Part 1

By gauthier

<h2>Be Big, Feel Small</h2>

<p>A few years ago we celebrated the Broad Institute’s tenth birthday with a call for introspection: what have we done well? What should we keep doing? What should we change? By then we had grown to over 4000 employees and affiliates. Out of that concern grew “Be Big, Feel Small” -- a committee to ensure that the Broad enjoys all the advantages of “being big” while still maintaining a culture and community that “feels small”. Yes it’s nice to have a community large enough to capture diversity of culture, research interests, and experience, but it’s a little disheartening when you don’t know the name of the person in the office across the hall.</p>

<p>The GATK user community faces some of the same challenges.  There’s certainly a wide variety of research questions and bioinformatic applications, but sometimes it seems like 1% of the users process 99% of the data.  While working on some of the Broad flagship projects like gnomAD, we have made a lot of improvements to benefit the Broad production pipeline. At the same time, we’ve done our best to make sure that we don’t lose sight of the needs of the community working with smaller cohorts or single-sample data.</p>

<hr></hr><h2>Variant calling</h2>

<h4>Calling single-sample spanning deletions</h4>

<p>The latest release contains an update to call spanning deletions at the single-sample level in HaplotypeCaller.  We added this logic to CombineGVCFs and GenotypeGVCFs a long time ago when we ran into many of these events in a large cohort.  However, such an event can happen in a single sample as well. Though there are typically only one or two per genome, these events are even more interesting at the single-sample level since both the individual’s haplotypes are non-reference at the SNP position. With the addition of the * allele, now they are being represented in a way that highlights that fact.</p>

<h4>Efficient GVCFs for improved analysis and storage</h4>

<p>While we don’t typically recommend direct analysis of GVCFs without joint genotyping, due to the presence of low quality variants, some users find that output format useful in the single-sample case for its representation of confidence in the reference allele.  Particularly in the clinical context, it’s difficult to estimate the confidence that a pathological variant like a certain BRCA mutation doesn’t exist in a sample.  Such a variant would be absent from the post-GenotypeGVCFs default output if the individual were confidently reference but also if the individual’s sequencing data contained any data at all. Aside from low quality alleles, GVCFs are not a particularly efficient storage format due to high resolution output for lower coverage regions.  The new ReblockGVCF tool can be used to merge much of this low quality data and remove low quality alleles, making GVCFs more efficient for storage and analysis-ready.</p>

<h4>GATK users improve single-sample GVCFs</h4>

<p>One of the benefits of deciding to make GATK4 open-source is the ease of incorporating features developed by the user community. <strong>Nils Homer</strong> and <strong>Tim Fennell</strong>, formerly of the Broad and founders of Fulcrum Genomics, introduced changes in 4.1 to make single-sample GVCFs easier to analyze and more informative. MNP output was never a priority in germline calling because of the additional complications for annotations and joint genotyping. However after the feature was added for Mutect2, typically a single-sample caller, Nils and Tim made a case for it to be turned on for analogous single-sample output by HaplotypeCaller and put in the work to make the change.  They also made the output GVCFs more spec-compliant by supplementing the previous PID and PGT phasing information with phased genotype calls and phase set (PS) annotations. Now downstream analysis software like rtg-tools can properly evaluate complex variants.</p>

<h2>Sample merging</h2>

<h4>Multi-interval support for GenomicsDB</h4>

<p>GenomicsDB has become much more robust with bug fixes, including handling of poorly formatted sample names, but probably the most widely appreciated feature is multi-interval support.  The Broad production pipeline has already used a previous version of GenomicsDB to produce the &gt;15,000 genome callset for <a rel="nofollow" href="https://gnomad.broadinstitute.org/about" title="gnomAD">gnomAD</a> v2, but the ability to import over multiple intervals is crucial for our exome joint callsets.  Previously, GenomicsDB could only support a single interval, so even small data sets had to be split at the contig level or with even finer resolution.  Even a small gene panel over a small cohort had to effectively be parallelized.  With the latest version, any invocation can contain an arbitrary number of intervals finally enabling a single task to import multiple contigs.</p>

<h2>Cohort genotyping</h2>

<h4>Lower memory requirements for GenotypeGVCFs</h4>

<p>Now that GenotypeGVCFs in GATK4 only accepts a single input, its only responsibilities are to evaluate the quality of the called alleles, drop the ones not meeting the threshold, and genotype the samples according to the remaining high quality alleles.  Even for this seemingly simple job, GenotypeGVCFs needed an exorbitant amount of memory for large whole genome cohorts. In GATK 4.1 the “new QUAL” model becomes the default, improving GenotypeGVCFs efficiency and also providing more accurate results at multi-allelic sites.  What is the much talked about “new QUAL”?  David Benjamin, who implemented the algorithm, will explain in an upcoming blog post.</p>

<h4>Back by popular demand: <code class="code codeInline" spellcheck="false">--include-non-variant-sites</code>:</h4>

<p>In streamlining of GenotypeGVCFs between GATK3 and GATK4, the option to emit non-variant sites was left out. Little did we realize how popular that feature is.  Users clamored for it on the forum and on Github.  Finally in GATK 4.1 you’ll find that non-variant sites are once more available, along with their per-sample RGQ output, giving the confidence in the homozygous reference genotype.</p>

<h2>Accurate and efficient analyses for genomics projects big and small</h2>

<p>The scale of experimental designs in genomics varies widely depending on application.  Projects like HapMap, 1000 Genomes, ExAC and gnomAD have endeavored to survey genetic variation across the most samples from the most diverse populations, but more and more common as we move toward an age of precision medicine are the single-sample analysis cases.  The Broad is participating in the ambitious, national All of Us initiative that aims to sequence at least a million diverse people across the US.  While this is a big effort, generating a big cohort, one of the goals is to make it feel small by, among other things, returning some results back to the individual participants.  We at the Broad are already working on projects to enable joint calling and analysis of the whole million participant cohort.  At the same time, we’re responsible for producing the individual results that will be interpreted and returned to participants.  Big or small, the GATK team remains committed to generating increasingly accurate analysis of genomic data.</p>

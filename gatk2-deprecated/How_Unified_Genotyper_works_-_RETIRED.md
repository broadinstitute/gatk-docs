## How Unified Genotyper works - RETIRED

By Geraldine_VdAuwera

<h3>Please note that this tool has been retired in favor of HaplotypeCaller, which is a much better tool that produces superior results. This document is maintained for archival purposes but should not be taken as a recommendation for new projects.</h3>

<hr></hr><p><strong>For a complete, detailed argument reference, refer to the <a rel="nofollow" href="http://www.broadinstitute.org/gatk/gatkdocs/org_broadinstitute_sting_gatk_walkers_genotyper_UnifiedGenotyper.html">technical documentation page</a>.</strong></p>

<h3>1. Slides</h3>

<p>The GATK requires the reference sequence in a single reference sequence in FASTA format, with all contigs in the same file. The GATK requires strict adherence to the FASTA standard. Only the standard ACGT bases are accepted; no non-standard bases (W, for example) are tolerated. Gzipped fasta files will not work with the GATK, so please make sure to unzip them first. Please see [Preparing the essential GATK input files: the reference genome] for more information on preparing FASTA reference sequences for use with the GATK.</p>

<h4>Genotype likelihoods</h4>

<p><img src="http://www.broadinstitute.org/gatk/media/pics/UGgenotypeLikelhoods.jpg" alt="image" class="embedImage-img importedEmbed-img"></img></p>

<h4>Multiple-sample allele frequency and genotype estimates</h4>

<p><img src="http://www.broadinstitute.org/gatk/media/pics/UGmultisample.jpg" alt="image" class="embedImage-img importedEmbed-img"></img></p>

<h3>2. Relatively Recent Changes</h3>

<p>The Unified Genotyper now makes multi-allelic variant calls!</p>

<h4>Fragment-based calling</h4>

<p>The Unified Genotyper calls SNPs via a two-stage inference, first from the reads to the sequenced fragments, and then from these inferred fragments to the chromosomal sequence of the organism. This two-stage system properly handles the correlation of errors between read pairs when the sequenced fragments contains errors itself. See <a rel="nofollow" href="http://db.tt/iazBvhJj">Fragment-based calling PDF</a> for more details and analysis.</p>

<h4>The Allele Frequency Calculation</h4>

<p>The allele frequency calculation model used by the Unified Genotyper computes a mathematically precise estimation of the allele frequency at a site given the read data. The mathematical derivation is similar to the one used by <a rel="nofollow" href="http://samtools.sourceforge.net/mpileup.shtml">Samtools</a>' <code class="code codeInline" spellcheck="false">mpileup</code> tool.  Heng Li has graciously allowed us to post the mathematical calculations backing the EXACT model <a rel="nofollow" href="http://www.broadinstitute.org/gatk/media/docs/Samtools.pdf">here</a>.  Note that the calculations in the provided document assume just a single alternate allele for simplicity, whereas the Unified Genotyper has been extended to handle genotyping multi-allelic events.  A slide showing the mathematical details for multi-allelic calling is available <a rel="nofollow" href="http://www.broadinstitute.org/gatk/media/docs/Multiallelic.pdf">here</a>.</p>

<h3>3. Indel Calling with the Unified Genotyper</h3>

<p>While the indel calling capabilities of the Unified Genotyper are still under active development, they are now in a stable state and are supported for use by external users.  Please note that, as with SNPs, the Unified Genotyper is fairly aggressive in making a call and, consequently, the false positive rate will be high in the raw call set.  We expect users to properly filter these results as per our best practices (which will be changing continually).</p>

<p>Note also that it is critical for the correct operation of the indel calling that the BAM file to be called is previously indel-realigned (see the IndelRealigner section on details). We strongly recommend doing joint Smith-Waterman alignment and not only per-lane or per-sample alignment at known sites. This is important because the caller is only empowered to genotype indels which are already present in reads.</p>

<p>Finally, while many of the parameters are common between indel and SNP calling, some parameters have different meaning or operate differently. For example, <code class="code codeInline" spellcheck="false">--min_base_quality_score</code> has a fixed, well defined operation for SNPs (bases at a particular location with base quality lower than this threshold are ignored). However, indel calling is by definition delocalized and haplotype-based, so this parameter does not make sense. Instead, the indel caller will clip both ends of the reads if their quality is below a certain threshold (Q20), up to the point where there is a base in the read exceeding this threshold.</p>

<h3>4. Miscellaneous notes</h3>

<p>Note that the Unified Genotyper will not call indels in 454 data!</p>

<p>It's common to want to operate only over a part of the genome and to output SNP calls to standard output, rather than a file.  The <code class="code codeInline" spellcheck="false">-L</code> option lets you specify the region to process.  If you set <code class="code codeInline" spellcheck="false">-o</code> to <code class="code codeInline" spellcheck="false">/dev/stdout</code> (or leave it out completely), output will be sent to the standard output of the console.</p>

<p>You can turn off logging completely by setting <code class="code codeInline" spellcheck="false">-l OFF</code> so that the GATK operates in silent mode.</p>

<p>By default the Unified Genotyper downsamples each sample's coverage to no more than 250x (so there will be at most 250 * number_of_samples reads at a site).  Unless there is a good reason for wanting to change this value, we suggest using this default value especially for exome processing; allowing too much coverage will require a lot more memory to run.  When running on projects with many samples at low coverage (e.g. 1000 Genomes with 4x coverage per sample) we usually lower this value to about 10 times the average coverage: <code class="code codeInline" spellcheck="false">-dcov 40</code>.</p>

<p>The Unified Genotyper does not use reads with a mapping quality of 255 ("unknown quality" according to the SAM specification).  This filtering is enforced because the genotyper caps a base's quality by the mapping quality of its read (since the probability of the base's being correct depends on both qualities). We rely on sensible values for the mapping quality and therefore using reads with a 255 mapping quality is dangerous.</p>

<ul><li><p>That being said, if you are working with a data type where alignment quality cannot be determined, there is a (completely unsupported) workaround: the ReassignMappingQuality filter enables you to reassign the mapping quality of all reads on the fly. For example, adding <code class="code codeInline" spellcheck="false">-rf ReassignMappingQuality -DMQ 60</code> to your command-line would change all mapping qualities in your bam to 60.</p></li>
<li><p>Or, if you are working with data from a program like TopHat which uses MAPQ 255 to convey meaningful information, you can use the ReassignOneMappingQuality filter (new in 2.4) to assign a different MAPQ value to those reads so they won't be ignored by GATK tools. For example, adding  <code class="code codeInline" spellcheck="false">-rf ReassignOneMappingQuality -RMQF 255 -RMQT 60</code> would change the mapping qualities of reads with MAPQ 255 in your bam to MAPQ 60.</p></li>
</ul><h3>5. Explanation of callable base counts</h3>

<p>At the end of a GATK UG run, you should see if you have -l INFO enabled a report that looks like:</p>

<pre class="code codeBlock" spellcheck="false">INFO  00:23:29,795 UnifiedGenotyper - Visited bases                         
247249719
INFO  00:23:29,796 UnifiedGenotyper - Callable bases 
219998386
INFO  00:23:29,796 UnifiedGenotyper - Confidently called bases 
219936125
INFO  00:23:29,796 UnifiedGenotyper - % callable bases of all loci        
88.978
INFO  00:23:29,797 UnifiedGenotyper - % confidently called bases of all loci  
88.953
INFO  00:23:29,797 UnifiedGenotyper - % confidently called bases of callable loci
88.953
INFO  00:23:29,797 UnifiedGenotyper - Actual calls made      
303126
</pre>

<p>This is what these lines mean:</p>

<ul><li><strong>Visited bases</strong></li>
</ul><p>This the total number of reference bases that were visited.</p>

<ul><li><strong>Callable bases</strong></li>
</ul><p>Visited bases minus reference Ns and places with no coverage, which we never try to call.</p>

<ul><li><strong>Confidently called bases</strong></li>
</ul><p>Callable bases that exceed the emit confidence threshold, either for being non-reference or reference. That is, if <code class="code codeInline" spellcheck="false">T</code> is the min confidence, this is the count of bases where <code class="code codeInline" spellcheck="false">QUAL &gt; T</code> for the site being reference in all samples and/or <code class="code codeInline" spellcheck="false">QUAL &gt; T</code> for the site being non-reference in at least one sample.</p>

<p>Note a subtle implication of the last statement, with all samples vs. any sample: calling multiple samples tends to reduce the percentage of confidently callable bases, as in order to be confidently reference one has to be able to establish that <strong>all</strong> samples are reference, which is hard because of the stochastic coverage drops in each sample.</p>

<p>Note also that confidently called bases will rise with additional data per sample, so if you don't dedup your reads, include lots of poorly mapped reads, the numbers will increase.  Of course, just because you confidently call the site doesn't mean that the data processing resulted in high-quality output, just that we had sufficient statistical evident based on your input data to called ref / non-ref.</p>

<h3>6. Calling sex chromosomes</h3>

<p>The GATK can be used to call the sex (X and Y) chromosomes, without explicit knowledge of the gender of the samples.  In an ideal world, with perfect upfront data processing, we would get perfect genotypes on the sex chromosomes without knowledge of who is diploid on X and has no Y, and who is hemizygous on both.  However, misalignment and mismapping contributes especially to these chromosomes, as their reference sequence is clearly of lower quality than the autosomal regions of the genome. Nevertheless, it is possible to get reasonably good SNP calls, even with simple data processing and basic filtering.  Results with proper, full data processing as per the best practices in the GATK should lead to very good calls.  You can view a presentation "The GATK Unified Genotyper on chrX and chrY" in the <a rel="nofollow" href="http://db.tt/Tl0WVkU">GSA Public Drop Box</a>.</p>

<p>Our general approach to calling on X and Y is to treat them just as we do the autosomes and then applying a gender-aware tools to correct the genotypes afterwards.  It makes sense to filter out sites across all samples (outside PAR) that appear as confidently het in males, as well as sites on Y that appear confidently non-reference in females.  Finally, it's possible to simply truncate the genotype likelihoods for males and females as appropriate from their diploid likelihoods -- AA, AB, and BB -- to their haploid equivalents -- AA and BB -- and adjust the genotype calls to reflect only these two options.  We applied this approach in 1000G, but we only did it as the data went into imputation, so there's no simple tool to do this, unfortunately.  The GATK team is quite interested in a general sex correction tool (analogous to the <a rel="nofollow" href="http://www.broadinstitute.org/gatk/gatkdocs/org_broadinstitute_sting_gatk_walkers_phasing_PhaseByTransmission.html">PhaseByTransmission</a> tool for trios), so please do contact us if you are interested in contributing such a tool to the GATK codebase.</p>

<h3>7. Related materials</h3>

<ul><li>Explanation of the VCF Output</li>
</ul><p>See <a rel="nofollow" href="http://www.broadinstitute.org/gatk/guide/article?id=1268">Understanding the Unified Genotyper's VCF files</a>.</p>

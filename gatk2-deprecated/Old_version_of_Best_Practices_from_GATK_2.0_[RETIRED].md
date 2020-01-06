## Old version of Best Practices from GATK 2.0 [RETIRED]

By Mark_DePristo

<h3>This article is severely out of date. The recommendations detailed here are NOT applicable to GATK versions 3.x and above. For the latest GATK Best Practices, please see <a href="http://www.broadinstitute.org/gatk/guide/" rel="nofollow">http://www.broadinstitute.org/gatk/guide/</a></h3>

<hr></hr><h1>Introduction</h1>

<h2>1. The basic workflow</h2>

<p>Our current best practice for making SNP and indel calls is divided into four sequential steps: initial mapping, refinement of the initial reads, multi-sample indel and SNP calling, and finally variant quality score recalibration.  These steps are the same for targeted resequencing, whole exomes, deep whole genomes, and low-pass whole genomes.</p>

<p><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/1e/51b52b53391578e1f0f20680e2fd68.png" alt="image" class="embedImage-img importedEmbed-img"></img></p>

<p>Example commands for each tool are available on the individual tool's wiki entry. There is also a <a rel="nofollow" href="http://www.broadinstitute.org/gatk/guide/article?id=1247">list of which resource files to use with which tool</a>.</p>

<p><strong>Note that due to the specific attributes of a project the specific values used in each of the commands may need to be selected/modified by the analyst.  Care should be taken by the analyst running our tools to understand what each parameter does and to evaluate which value best fits the data and project design.</strong></p>

<h2>2. Lane, Library, Sample, Cohort</h2>

<p>There are four major organizational units for next-generation DNA sequencing processes that used throughout this documentation:</p>

<ul><li>Lane: The basic machine unit for sequencing.  The lane reflects the basic independent run of an NGS machine.  For Illumina machines, this is the physical sequencing lane.</li>
<li>Library: A unit of DNA preparation that at some point is physically pooled together.  Multiple lanes can be run from aliquots from the same library.  The DNA library and its preparation is the natural unit that is being sequenced.  For example, if the library has limited complexity, then many sequences are duplicated and will result in a high duplication rate across lanes.</li>
<li>Sample: A single individual, such as human CEPH NA12878.  Multiple libraries with different properties can be constructed from the original sample DNA source.  Here we treat samples as independent individuals whose genome sequence we are attempting to determine.  From this perspective, tumor / normal samples are different despite coming from the same individual.</li>
<li>Cohort: A collection of samples being analyzed together.  This organizational unit is the most subjective and depends intimately on the design goals of the sequencing project.  For population discovery projects like the 1000 Genomes, the analysis cohort is the ~100 individual in each population.  For exome projects with many samples (e.g., ESP with 800 EOMI samples) deeply sequenced we divide up the complete set of samples into cohorts of ~50 individuals for multi-sample analyses.</li>
</ul><p>This document describes how to call variation within a single analysis cohort, comprised for one or many samples, each of one or many libraries that were sequenced on at least one lane of an NGS machine.</p>

<p>Note that many GATK commands can be run at the lane level, but will give better results seeing all of the data for a single sample, or even all of the data for all samples.  Unfortunately, there's a trade-off in computational cost by running these commands across all of your data simultaneously.</p>

<h2>3. Testing data: 64x HiSeq on chr20 for NA12878</h2>

<p>In order to help individuals get up to speed, evaluate their command lines, and generally become familiar with the GATK tools we recommend you download the raw and realigned, recalibrated NA12878 test data from the GATK resource bundle.  It should be possible to apply all of the approaches outlined below to get excellent results for realignment, recalibration, SNP calling, indel calling, filtering, and variant quality score recalibration using this data.</p>

<h2>4. Where can I find out more about the new GATK 2.0 tools you are talking about?</h2>

<p>In our <a rel="nofollow" href="https://www.dropbox.com/sh/e31kvbg5v63s51t/6GdimgsKss">GATK 2.0 slide archive</a>.</p>

<hr></hr><h1>Phase I: Raw data processing</h1>

<h2>1. Raw FASTQs to raw reads via mapping</h2>

<p>The GATK data processing pipeline assumes that one of the many NGS read aligners (see <a rel="nofollow" href="http://bib.oxfordjournals.org/content/11/5/473.abstract">this review</a>) has been applied to your raw FASTQ files.  For Illumina data we recommend <a rel="nofollow" href="http://bio-bwa.sourceforge.net/">BWA</a> because it is accurate, fast, well-supported, open-source, and emits SAM files natively (which are then easy to convert to BAM).</p>

<h2>2. Raw reads to analysis-ready reads</h2>

<p>The three key processes used here are:</p>

<ul><li>Local realignment around indels: Reads that align on the edges of indels often get mapped with mismatching bases that might look like evidence for SNPs. We look for the most consistent placement of the reads with respect to the indel in order to clean up these artifacts.</li>
<li>MarkDuplicates: Duplicately sequenced molecules shouldn't be counted as additional evidence for or against a putative variant. By marking these reads as duplicates the algorithms in the GATK know to ignore them.</li>
<li>Base quality score recalibration: The per-base estimate of error known as the base quality score is the foundation upon which all statistically calling algorithms are based. We've found that the estimates provided by the sequencing machines are often inaccurate, and worse, biased. Through recalibration an empirically accurate error model is assigned to the bases to create an analysis-ready bam file. Note: if you have old data that has been recalibrated with an old version of BQSR, you need to rerun your data with the new version so insertion and deletion qualities can be added to your recalibrated BAM file.</li>
</ul><p>There are several options here from the easy and fast basic protocol to the more comprehensive but computationally expensive pipeline. For example, there are two types of realignment which constitute a vastly different amount of processing power required:</p>

<ul><li>Realignment only at known sites, which is very efficient, can operate with little coverage (1x per lane genome wide) but can only realign reads at known indels.</li>
<li>Fully local realignment uses mismatching bases to determine if a site should be realigned, and relies on sufficient coverage to discover the correct indel allele in the reads for alignment.  It is much slower (involves SW step) but can discover new indel sites in the reads.  If you have a database of known indels (for human, this database is extensive) then at this stage you would also include these indels during realignment, which vastly improves sensitivity, specificity, and speed.</li>
</ul><h3>Fast: lane-level realignment (at known sites only) and lane-level recalibration</h3>

<p>This protocol uses lane-level local realignment around known indels (very fast, as there's no sample level processing) to clean up lane-level alignments.  This results in better quality scores, as they are less biased for indel alignment artefacts.</p>

<pre class="code codeBlock" spellcheck="false">
for each lane.bam
    dedup.bam &lt;- MarkDuplicate(lane.bam)
    realigned.bam &lt;- realign(dedup.bam) [at only known sites, if possible, otherwise skip]
    recal.bam &lt;- recal(realigned.bam)
</pre>

<h3>Fast + per-sample processing</h3>

<p>Here we are essentially just merging the recalibrated lane.bams for a sample, dedupping the reads, and calling it quite.  It doesn't perform indel realignment across lanes, so it leaves in some indels artifacts.  For humans, which now have an extensive list of indels (get them from the GATK bundle!) the lane-level realignment around known indels is going to make up for the lack of cross-lane realignment.  This protocol is appropriate if you are going to use callers like the HaplotypeCaller, UnifiedGenotyper with BAQ, or samtools with BAQ that are less sensitive to the initial alignment of reads, or if your project has limited coverage per sample (&lt; 8x) where per-sample indel realignment isn't more empowered than per-lane realignment.  For other situations or for organisms with limited database of segregating indels, it's better to use the advanced protocol if you have deep enough data per sample.</p>

<pre class="code codeBlock" spellcheck="false">
for each sample
    recals.bam &lt;- merged lane-level recal.bams for sample
    dedup.bam &lt;- MarkDuplicates(recals.bam)
    sample.bam &lt;- dedup.bam
</pre>

<h3>Better: recalibration per lane then per-sample realignment with known indels</h3>

<p>As with the basic protocol, this protocol assumes the per-lane processing has been already completed.  This protocol is essentially the basic protocol but with per-sample indel realignment.</p>

<pre class="code codeBlock" spellcheck="false">
for each sample
    recals.bam &lt;- merged lane-level recal.bams for sample
    dedup.bam &lt;- MarkDuplicates(recals.bam)
    realigned.bam &lt;- realign(dedup.bam) [with known sites included if available]
    sample.bam &lt;- realigned.bam
</pre>

<p>This is the protocol we use at the Broad in our fully automated pipeline because it gives an optimal balance of performance, accuracy and convenience.</p>

<h3>Best: per-sample realignment with known indels then recalibration</h3>

<p>Rather than doing the lane level cleaning and recalibration, this process aggregates all of the reads for each sample and then does a full dedupping, realign, and recalibration, yielding the best single-sample results.  The big change here is sample-level cleaning followed by recalibration, giving you the most accurate quality scores possible for a single sample.</p>

<pre class="code codeBlock" spellcheck="false">
for each sample
    lanes.bam &lt;- merged lane.bams for sample
    dedup.bam &lt;- MarkDuplicates(lanes.bam)
    realigned.bam &lt;- realign(dedup.bam) [with known sites included if available]
    recal.bam &lt;- recal(realigned.bam)
    sample.bam &lt;- recal.bam
</pre>

<p>This protocol can be hard to implement in practice unless you can afford to wait until all of the data is available to do data processing for your samples.</p>

<h3>Misc. notes on the process</h3>

<ul><li>MarkDuplicates needs only be run at the library level. So the sample-level dedupping isn't necessary if you only ever have a library on a single lane.  If you run the sample library on many lanes (as can be necessary for whole exome, for example), you should dedup at the library level.</li>
<li>The base quality score recalibrator is read group aware, so running it on a merged BAM files containing multiple read groups is the same as running it on each bam file individually.  There's some memory cost (so it's best not to recalibrate many read groups simultaneously) but for reasonable projects this is fine.</li>
<li>Local realignment preserves read meta-data, so you can realign and then recalibrate just fine.</li>
<li>Multi-sample realignment with known sites and recalibration isn't really recommended any longer.  It's extremely computational expensive and isn't necessary for advanced callers with advanced filters like the Unified Genotyper / HaplotypeCaller and VQSR.  It's better to use one of the protocols above and then an advanced caller that is robust to indel artifacts.</li>
<li>However, note that for contrastive calling projects -- such as cancer tumor/normals -- we recommend realigning both the tumor and the normal together in general to avoid slight alignment differences between the two tissue types.</li>
</ul><h2>3. Reducing BAMs to minimize file sizes and improve calling performance</h2>

<p>ReduceReads is a novel (perhaps even breakthrough?) GATK 2.0 data compression algorithm.  The purpose of ReducedReads is to take a BAM file with NGS data and reduce it down to just the information necessary to make accurate SNP and indel calls, as well as genotype reference sites (hard to achieve) using GATK tools like UnifiedGenotyper or HaplotypeCaller.  ReduceReads accepts as an input a BAM file and produces a valid BAM file (it works in IGV!) but with a few extra tags that the GATK can use to make accurate calls.</p>

<p>You can find more information about <a rel="nofollow" href="https://www.dropbox.com/s/3b87mwjd1pif0jc/ReduceReads.pdf">reduced reads in some of our presentations</a> in the archive.</p>

<p>ReduceReads works well for exomes or high-coverage (at least 20x average coverage) whole genome BAM files.  In this case we highly recommend using ReduceReads to minimize the file sizes.  Note that ReduceReads performs a lossy compression of the sequencing data that works well with the downstream GATK tools, but may not be supported by external tools.  Also, we recommend that you archive your original BAM file, or at least a copy of your original FASTQs, as ReduceReads is highly lossy and doesn't quality as an archive data compression format.</p>

<p>Using ReduceReads on your BAM files will cut down the sizes to approximately 1/100 of their original sizes, allowing the GATK to process tens of thousands of samples simultaneously without excessive IO and processing burdens.  Even for single samples ReduceReads cuts the memory requirements, IO burden, and CPU costs of downstream tools significantly (10x or more) and so we recommend you preprocess analysis-ready BAM files with ReducedReads.</p>

<pre class="code codeBlock" spellcheck="false">
for each sample
    sample.reduced.bam &lt;- ReduceReads(sample.bam)
</pre>

<hr></hr><h1>Phase II: Initial variant discovery and genotyping</h1>

<h2>1. Input BAMs for variant discovery and genotyping</h2>

<p>After the raw data processing step, the GATK variant detection process assumes that you have aligned, duplicate marked, and recalibrated BAM files for all of the samples in your cohort.  Because the GATK can dynamically merge BAM files, it isn't critical to have merged files by lane into sample bams, or even samples bams into cohort bams.  In general we try to create sample level bams for deep data sets (deep WG or exomes) and merged cohort files by chromosome for WG low-pass.</p>

<p>For this part of the this document, I'm going to assume that you have a single realigned, recalibrated, dedupped BAM per sample, called sampleX.bam, for X from 1 to N samples in your cohort.  Note that some of the data processing steps, such as multiple sample local realignment, will merge BAMS for many samples into a single BAM.  If you've gone down this route, you just need to modify the GATK commands as necessary to take not multiple BAMs, one for each sample, but a single BAM for all samples.</p>

<h2>2. Multi-sample SNP and indel calling</h2>

<p>The next step in the standard GATK data processing pipeline, whole genome or targeted, deep or shallow, is to apply the Haplotype Caller or Unified Genotyper to identify sites among the cohort samples that are statistically non-reference.  This will produce a multi-sample <a rel="nofollow" href="http://www.1000genomes.org/wiki/Analysis/variant-call-format">VCF file</a>, with sites discovered across samples and genotypes assigned to each sample in the cohort.  It's in this stage that we use the meta-data in the BAM files extensively -- read groups for reads, with samples, platforms, etc -- to enable us to do the multi-sample merging and genotyping correctly.  It was a pain for data processing, yes, but now life is easy for downstream calling and analysis.</p>

<h3>Selecting an appropriate quality score threshold</h3>

<p>A common question is the confidence score threshold to use for variant detection.  We recommend:</p>

<ul><li>Deep (&gt; 10x coverage per sample) data: we recommend a minimum confidence score threshold of Q30.</li>
<li>Shallow (&lt; 10x coverage per sample) data: because variants have by necessity lower quality with shallower coverage we recommend a minimum confidence score of Q4 in projects with 100 samples or fewer and Q10 otherwise.</li>
</ul><h3>Standard protocol: HaplotypeCaller</h3>

<pre class="code codeBlock" spellcheck="false">
raw.vcf &lt;- HaplotypeCaller(sample1.bam, sample2.bam, ..., sampleN.bam)
</pre>

<h3>Standard protocol: UnifiedGenotyper</h3>

<pre class="code codeBlock" spellcheck="false">
raw.vcf &lt;- UnifiedGenotyper(sample1.bam, sample2.bam, ..., sampleN.bam)
</pre>

<h3>Choosing HaplotypeCaller or UnifiedGenotyper</h3>

<ul><li>We believe the best possible caller in the GATK is the HaplotypeCaller, which combines a local de novo assembler with a more advanced HMM likelihood function than the UnifiedGenotyper.  It should produce excellent SNP, MNP, indel, and short SV calls.  It should be the go-to calling algorithm for most projects.  It is, for example, how we make our <a rel="nofollow" href="https://www.dropbox.com/s/deqtr8ur3a4agq5/Broad%20phase%202%20chr20%20callset.pdf">Phase II call set for 1000 Genomes</a>.</li>
<li>Currently the HaplotypeCaller only supports diploid calling.  If you want to call non-diploid samples you'll need to use the UnifiedGenotyper.</li>
<li>At the moment the HaplotypeCaller does not support multithreading. For now you should indeed stick with the UG if you wish to use the <code class="code codeInline" spellcheck="false">-nt</code> option. However you can use Queue to parallelize execution of HaplotypeCaller.</li>
<li>If for some reason you cannot use the HaplotyperCaller do fall back to the UnifiedGenotyper protocol below.  Otherwise try out the HaplotypeCaller and let us know about your experiences here on the forum!</li>
</ul><hr></hr><h1>Phase III: Integrating analyses: getting the best call set possible</h1>

<p>This raw VCF file should be as sensitive to variation as you'll get without imputation.  At this stage, you can assess things like sensitivity to known variant sites or genotype chip concordance.  The problem is that the raw VCF will have many sites that aren't really genetic variants but are machine artifacts that make the site statistically non-reference.  All of the subsequent steps are designed to separate out the false positive machine artifacts from the true positive genetic variants.</p>

<h2>1. Statistical filtering of the raw calls</h2>

<p>The process used here is the Variant quality score recalibrator which builds an adaptive error model using known variant sites and then applies this model to estimate the probability that each variant in the callset is a true genetic variant or a machine/alignment artifact. All filtering criteria are learned from the data itself.</p>

<h2>2. Analysis ready VCF protocol</h2>

<p>Take a look at our <a rel="nofollow" href="http://www.broadinstitute.org/gatk/guide/article?id=1259">FAQ page</a> for specific recommendations on which training sets and command-line arguments to use with various project designs. It is expected that analysis will be required by the user when determining the best way to use this tool for different projects. These recommendations are only meant as jumping off points!</p>

<p>We've found that the best results are obtained with the VQSR when it is used to build separate adaptive error models for SNPs versus INDELs:</p>

<pre class="code codeBlock" spellcheck="false">
    snp.model &lt;- BuildErrorModelWithVQSR(raw.vcf, SNP)
    indel.model &lt;- BuildErrorModelWithVQSR(raw.vcf, INDEL)
    recalibratedSNPs.rawIndels.vcf &lt;- ApplyRecalibration(raw.vcf, snp.model, SNP)
    analysisReady.vcf &lt;- ApplyRecalibration(recalibratedSNPs.rawIndels.vcf, indel.model, INDEL)
</pre>

<h2>3. Notes about small whole exome projects or small target experiments</h2>

<p>In our testing we've found that in order to achieve the best exome results one needs to use an exome callset with at least 30 samples.  Also, for experiments that employ targeted resequencing of a small region (for example, a few hundred genes), VQSR may not be empowered regardless of the number of samples in the experiment.  For users with experiments containing fewer exome samples or with a small target region there are several options to explore (listed in priority order of what we think will give the best results):</p>

<ul><li>Add additional samples for variant calling, either by sequencing additional samples or using publicly available exome bams from the 1000 Genomes Project (this option is used by the Broad exome production pipeline).</li>
<li>Use the VQSR with the smaller SNP callset but experiment with the argument settings. For example, try adding <code class="code codeInline" spellcheck="false">--maxGaussians 4 --percentBad 0.05</code> to your command line. Note that this is very dependent on your dataset, and you may need to try some very different settings. It may even not work at all. Unfortunately we cannot give you any specific advice, so please do not post questions on the forum asking for help finding the right parameters.</li>
<li>Use hard filters (detailed below).</li>
</ul><h3>Recommendations for very small data sets (in terms of both number of samples or size of targeted regions)</h3>

<p><strong>These recommended arguments for VariantFiltration are only to used when ALL other options are not available</strong>:</p>

<p>You will need to compose filter expressions (see <a rel="nofollow" href="http://www.broadinstitute.org/gatk/gatkdocs/org_broadinstitute_sting_gatk_walkers_filters_VariantFiltration.html">here</a>, <a rel="nofollow" href="http://www.broadinstitute.org/gatk/guide/article?id=51">here</a> and <a rel="nofollow" href="http://www.broadinstitute.org/gatk/guide/article?id=1255">here</a> for details) to filter on the following annotations and values:</p>

<p>For SNPs:</p>

<ul><li><code class="code codeInline" spellcheck="false">QD &lt; 2.0</code></li>
<li><code class="code codeInline" spellcheck="false">MQ &lt; 40.0</code></li>
<li><code class="code codeInline" spellcheck="false">FS &gt; 60.0</code></li>
<li><code class="code codeInline" spellcheck="false">HaplotypeScore &gt; 13.0</code></li>
<li><code class="code codeInline" spellcheck="false">MQRankSum &lt; -12.5</code></li>
<li><code class="code codeInline" spellcheck="false">ReadPosRankSum &lt; -8.0</code></li>
</ul><p>For indels:</p>

<ul><li><code class="code codeInline" spellcheck="false">QD &lt; 2.0</code></li>
<li><code class="code codeInline" spellcheck="false">ReadPosRankSum &lt; -20.0</code></li>
<li><code class="code codeInline" spellcheck="false">InbreedingCoeff &lt; -0.8</code></li>
<li><code class="code codeInline" spellcheck="false">FS &gt; 200.0</code></li>
</ul><p>Note that the InbreedingCoeff statistic is a population-level calculation that is only available with 10 or more samples. If you have fewer samples you will need to omit that particular filter statement.</p>

<p><strong>For shallow-coverage (&lt;10x): you cannot use filtering to reliably separate true positives from false positives.  You must use the protocol involving variant quality score recalibration.</strong></p>

<p>The maximum DP (depth) filter only applies to whole genome data, where the probability of a site having exactly N reads given an average coverage of M is a well-behaved function.  First principles suggest this should be a binomial sampling but in practice it is more a Gaussian distribution.  Regardless, the DP threshold should be set a 5 or 6 sigma from the mean coverage across all samples, so that the DP &gt; X threshold eliminates sites with excessive coverage caused by alignment artifacts.  Note that <strong>for exomes, a straight DP filter shouldn't be used</strong> because the relationship between misalignments and depth isn't clear for capture data.</p>

<p>That said, all of the caveats about determining the right parameters, etc, are annoying and are largely eliminated by variant quality score recalibration.</p>

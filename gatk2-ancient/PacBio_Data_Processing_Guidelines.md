## PacBio Data Processing Guidelines

By delangel

<h2>Please note that this article has not been updated since 2011. It is now considered deprecated because the procedures described have not been tested with any recent versions of our tools.</h2>

<h3>Introduction</h3>

<p>Processing data originated in the Pacific Biosciences RS platform has been evaluated by the GSA and publicly presented in numerous occasions. The guidelines we describe in this document were the result of a systematic technology development experiment on some datasets (human, E. coli and Rhodobacter) from the Broad Institute. These guidelines produced better results than the ones obtained using alternative pipelines up to this date (september 2011) for the datasets tested, <strong>but there is no guarantee that it will be the best for every dataset and that other pipelines won't supersede it in the future</strong>.</p>

<p>The pipeline we propose here is illustrated in a Q script (PacbioProcessingPipeline.scala) distributed with the GATK as an example for educational purposes. This pipeline has not been extensively tested and is not supported by the GATK team.  You are free to use it and modify it for your needs following the guidelines below.</p>

<p><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/c3/810af2dbd7749152a2e17b9cf60a7a.jpeg" alt="image" class="embedImage-img importedEmbed-img"></img></p>

<h3>BWA alignment</h3>

<p>First we take the filtered_subreads.fq file output by the Pacific Biosciences RS SMRT pipeline and align it using BWA. We use BWA with the <code class="code codeInline" spellcheck="false">bwasw</code> algorithm and allow for relaxing the gap open penalty to account for the excess of insertions and deletions known to be typical error modes of the data. For an idea on what parameters to use check suggestions given by the BWA author in the <a rel="nofollow" href="http://bio-bwa.sourceforge.net/bwa.shtml">BWA manual page</a> that are specific to Pacbio. The goal is to account for Pacific Biosciences RS known error mode and benefit from the long reads for a high scoring overall match. (for older versions, you can use the filtered_subreads.fasta and combine the base quality scores extracted from the h5 files using Pacific Biosciences SMRT pipeline python tools)</p>

<p>To produce a BAM file that is sorted by coordinate with adequate read group information we use <a rel="nofollow" href="http://picard.sourceforge.net/command-line-overview.shtml">Picard tools</a>: SortSam and AddOrReplaceReadGroups. These steps are necessary because all subsequent tools require that the BAM file follow these rules. It is also generally considered good practices to have your BAM file conform to these specifications.</p>

<h3>Best Practices for Variant Calling</h3>

<p>Once we have a proper BAM file, it is important to estimate the empirical quality scores using statistics based on a known callset (e.g. latest dbSNP) and the following covariates: QualityScore, Dinucleotide and ReadGroup.  You can follow the GATK's <a rel="nofollow" href="http://www.broadinstitute.org/gatk/guide/article?id=1186">Best Practices for Variant Detection</a> according the type of data you have, with the exception of indel realignment, because the tool has not been adapted for Pacific Biosciences RS data.</p>

<h3>Problems with Variant Calling with Pacific Biosciences</h3>

<ul><li>Calling must be more permissive of indels in the data.</li>
</ul><p>You will have to adjust your calling thresholds in the Unified Genotyper to allow sites with a higher indel rate to be analyzed.</p>

<ul><li>Base quality thresholds should be adjusted to the specifics of your data.</li>
</ul><p>Be aware that the Unified Genotyper has cutoffs for base quality score and if your data is on average Q20 (a common occurrence with Pacific Biosciences RS data) you may need to adjust your quality thresholds to allow the GATK to analyze your data. There is no right answer here, you have to choose parameters consistent with your average base quality scores, evaluate the calls made with the selected threshold and modify as necessary.</p>

<ul><li>Reference bias</li>
</ul><p>To account for the high insertion and deletion error rate of the Pacific Biosciences data instrument, we often have to set the gap open penalty to be lower than the base mismatch penalty in order to maximize alignment performance. Despite aligning most of the reads successfully, this creates the side effect that the aligner will sometimes prefer to "hide" a true SNP inside an insertion. The result is accurate mapping, albeit with a reference-biased alignment. It is important to note however, that reference bias is an artifact of the alignment process, not the data, and can be greatly reduced by locally realigning the reads based on the reference and the data. Presently, the available software for local realignment is not compatible with the length and the high indel rate of Pacific Bioscience data, but we expect new tools to handle this problem in the future. Ultimately reference bias will mask real calls and you will have to inspect these by hand.</p>

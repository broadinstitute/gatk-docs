## New Features and Improvements  in Mutect2

By davidben

<h3>Improving the science</h3>

<p>As of GATK 4.0, Mutect2 did not work well with the extremely high depths seen in mitochondrial samples and cfDNA liquid biopsies. Thanks to a new active region calculation and a few new filters, Mutect2 now works off the shelf for these cases.</p>

<p>We have been emboldened to improve the local assembly code shared with HaplotypeCaller (HC). Adaptive assembly graph pruning advances both high- and low- depth calling. This feature (default in Mutect2 and optional in HC) uses a probabilistic model that accounts for depth, removing spurious paths in the assembly graph while retaining real mutations.  Processing fewer false paths reduces runtime and improves accuracy, even in an assembly region with varying depth, such as one that spans the end of an exome capture target.</p>

<p>Mutect2 can now monitor a tumor after chemotherapy using the new “GGA” (genotype given alleles) mode.   Inputting pre-treatment somatic variants as the given alleles forces Mutect2 to genotype those alleles in a given vcf in addition to those that it would otherwise discover (in contrast to HC’s GGA mode).  You can also use GGA mode to give special attention to calls from other tools, as in a head-to-head comparison, or to known driver mutations.</p>

<p>Mutect2 now allows joint somatic genotyping of an arbitrary number of tumor and normal samples. With this change, you only need to specify which samples are normal. Mutect2 assumes anything else is a tumor sample. Allowing multiple samples from the same individual enables tracking a tumor and possibly its metastases over time. The feature, currently in beta, will become important as liquid biopsies increase in popularity.</p>

<h3>Other new or overhauled tools</h3>

<p>Several tools in the orbit of Mutect2 are also entirely new or overhauled:</p>

<ul><li>A new <strong>Bayesian orientation bias model catches artifact modes</strong> prevalent in Illumina’s new Novaseq machines that the old model missed. It filters oxoG and deamination (FFPE) artifacts almost identically to its predecessor, and automatically discovers which artifacts to filter, if any. You can find the new model in CollectF1R2Counts and LearnReadOrientationModel.</li>
<li>While the default Mutect2 filters achieve a very low false positive rate, the new <strong>FilterAlignmentArtifacts tool is useful when even more stringent filtering is needed</strong>. The new tool catches mapping errors by re-aligning reads and checking for multi-mapping, reducing the number of false negatives by realigning reads and their mates together. Because it realigns to the best existing reference regardless of the original alignment, you can now look for mapping errors in an hg19 bam by checking it against the hg38 reference.</li>
<li></li>
<li><strong>An overhauled CalculateContamination tool maximizes accuracy</strong> even in the face of large numbers of copy number variations, outperforming other known tools.</li>
</ul><h3>Sensitivity and precision improvements</h3>

<p>Both sensitivity and precision are better than they were a year ago. In the coming weeks and months, watch out for additional improvements, including:</p>

<ul><li><p>Improvements in Mutect2 that account for PCR error and allow you to keep both reads. Mutect2 currently discards one read of a mate pair that overlaps over a variant site on the grounds that the reads are not independent sources of evidence, since they might arise from the same PCR error.</p></li>
<li><p>A somatic likelihoods model that will force a read and its mate to support the same haplotype, since they must be from the same piece of DNA.</p></li>
<li><p>A Bayesian model that enables FilterMutectCalls to learn the spectrum of tumor allele fractions and better distinguish between false positives and real somatic variants.</p></li>
<li><p>An improved FilterMutectCalls tool able to learn whether a sample has few or many somatic mutations - and filter accordingly.</p></li>
</ul><h3>Performance Improvements</h3>

<p>On the software engineering side of things, we have made Mutect2 faster and cheaper. Whole-genome pairs of 60x tumors and 60x normals take ~24 hours of total CPU time and cost about a dollar on Google cloud in our public validations on Firecloud. <a rel="nofollow" href="https://github.com/broadinstitute/gatk/tree/master/scripts/mutect2_wdl" title="The Mutect2 WDL">The Mutect2 WDL</a> can parallelize this to reduce the wall clock time to an hour or so! The WDL supports direct reading from google buckets, so a Mutect2 run streams only the reads it needs.</p>

<h3>Want to help improve Mutect2?</h3>

<p>This is just the tip of our wish list for the next year, and we look forward to getting a lot done. While the joint assembly and genotyping work well, our initial attempt at multi-sample filtering will need improvement.  We would be very grateful for suggestions, requests, suggestions for improvement, and requests for features by early adopters.</p>

## A new workflow for refining genotype calls

By Geraldine_VdAuwera

<p>The core GATK Best Practices workflow has historically focused on variant discovery --that is, the existence of genomic variants in one or more samples in a cohorts-- and consistently delivers high quality results when applied appropriately. However, we know that the quality of the individual genotype calls coming out of the variant callers can vary widely based on the quality of the BAM data for each sample. To address the increasing need for more accurate genotypes, especially in the context of analyses performed on families, we have developed a new workflow that uses additional data such as pedigrees and genotype priors to improve the accuracy of genotype calls, to filter genotype calls that are not reliable enough for downstream analysis and to tag possible <em>de novo</em> mutations.</p>

<p>The Genotype Refinement workflow is meant to serve as an optional extension of the variant calling workflow, intended for researchers whose work requires high-quality identification of individual genotypes. It is documented in a <a rel="nofollow" href="https://www.broadinstitute.org/gatk/guide/article?id=4723">new method article</a>, with <a rel="nofollow" href="https://www.broadinstitute.org/gatk/guide/article?id=4726">mathematical details</a> available separately. See the corresponding <a rel="nofollow" href="https://www.broadinstitute.org/gatk/guide/article?id=4727">tutorial</a> for step-by-step instructions on how to actually run it in practice.</p>

<p>Note that although all tools involved in this workflow are already available in GATK 3.2, some functionalities will only be available in the latest development version (see <a rel="nofollow" href="https://www.broadinstitute.org/gatk/nightly">nightly builds</a> in the <a rel="nofollow" href="https://www.broadinstitute.org/gatk/download">Downloads</a> section) until the release of version 3.3 (which is imminent).</p>

<p>Let us know how the new workflow works for you; as usual, we crave feedback and are happy to answer any and all questions.</p>

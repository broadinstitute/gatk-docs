## Release notes for GATK version 3.8

By Geraldine_VdAuwera

<p>GATK 3.8 was released on July 28, 2017. Itemized changes are listed below. For more information on what you should care about, see the user-friendly <a rel="nofollow" href="https://software.broadinstitute.org/gatk/blog?id=10063">version highlights</a>.</p>

<hr></hr><h3>Performance improvements / accelerations</h3>

<ul><li>Add the Intel GKL as a dependency, and turn on the Intel inflater/deflater by default</li>
<li>Use GKL's implementation of Vector Logless Pair HMM (HaplotypeCaller and MuTect2)</li>
<li>Add FPGA support for PairHMM via GKL (experimental)</li>
</ul><hr></hr><h3>Notable tool behavior change</h3>

<ul><li>In VariantsToTable, flip handling of missing annotation values: allow by default (print NA for null object), deprecate --allowMissingData, add --errorIfMissingData flag</li>
</ul><hr></hr><h3>Improvements to germline calling toolchain</h3>

<ul><li>Change calculation of rank sum to be the median of the per-sample rank sums (because the rank sum across all sample data did worse for biallelics)</li>
<li>Improve VQSR model serialization functionality and allow serialized model reports to be read into the variant recalibrator to recreate a GMM from a previous VQSR run</li>
<li>Allow disabling of BadMateFilter in HaplotypeCaller</li>
</ul><hr></hr><h3>Bug fixes</h3>

<ul><li>Fix LeftAlignAndTrimVariants -split to not change no-call genotypes to hom-ref</li>
<li>Fix ClippingOP.getNewAlignmentStartOffset() to handle N cases properly</li>
<li>Fix FindCoveredIntervals so -minBQ, -minMQ and -cov work as expected</li>
<li>Fix thread safety bugs (upstreamDeletionsLoc and practicalAlleleCountForPloidy)</li>
<li>Fix bug in depth of coverage for partitioning by library</li>
<li>In GenotypeGVCFs, total ploidy does not have to equal the number of priors</li>
<li>Explicitly report AD and DP as zero if there is no read coverage</li>
<li>GenotypeGVCFs and CombineGVCFs now throw errors when AS annotations are not present if allele-specific mode is activated</li>
<li>Copy Program Records (PG) from input BAM to -bamout file (failure to do so caused some validation failures)</li>
</ul><hr></hr><h3>Docs / logging</h3>

<ul><li>Assorted corrections and improvements in documentation, error messages and logging rules</li>
<li>Archive GATK3-specific docs from the forum</li>
</ul><hr></hr><h3>Misc engine</h3>

<ul><li>Bump htsjdk to ver 2.10.1 and Picard to ver 2.10.2</li>
</ul>
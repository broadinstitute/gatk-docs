## New! Mitochondrial Analysis with Mutect2

By mshand

<h2>Overcoming barriers to understanding the mitochondrial genome</h2>

<p>Announcing a brand new “Best Practices” pipeline for calling SNPs and INDELs in the mitochondrial genome! Calling low VAF alleles (variant allele fraction) in the mitochondrial genome presents special problems, but come with great rewards, including diagnosing rare diseases and identifying asymptomatic carriers of pathogenic diseases. We’re excited to begin using this pipeline on tens of thousands of diverse samples from the gnomAD project (<a href="http://gnomad.broadinstitute.org/about" rel="nofollow">http://gnomad.broadinstitute.org/about</a>) to gain greater understanding of population genetics from the perspective of mitochondrial DNA.</p>

<hr></hr><h3>Mitochondrial genome - a history of challenges</h3>

<p>We had been often advised to “try using a somatic caller,” since we expect mitochondria to have variable allele fraction variants, but we never actually tried it ourselves. Over the past year we focused on creating truth-data for low allele fraction variants on the mitochondria and developing a production-quality, high throughput pipeline that overcomes the unique challenges of calling SNPs and INDELs on the mitochondria offers.</p>

<p>See below the four challenges to unlocking the mitochondrial genome and how we’ve improved our pipeline to overcome them.</p>

<h4>1. Mitochondria have a circular genome</h4>

<p>Though the genome is linearized in the typical references we use, the breakpoint is artificial -- purely for the sake of bioinformatic convenience. Since the breakpoint is inside the “control region”, which is non-coding but highly variable across people, we want to be sensitive to variation in that region, to capture the most genetic diversity.</p>

<h4>2. A pushy genome makes for difficult mapping</h4>

<p>The mitochondrial genome has inserted itself into the autosomal genome many times throughout human evolution - and continues to do so. These regions in the autosomal genome, called Nuclear Mitochondrial DNA segments (NuMTs), make mapping difficult: if the sequences are identical, it’s hard to know if a read belongs in an autosomal NuMT or the mitochondrial contig.</p>

<h4>3. Most mitochondria are normal</h4>

<p>Variation in the mitochondria can have very low heteroplasmy. In fact, the variation “signal” can be comparable to the inherent sequencer noise, but the scientific community tasked us with calling 1% allele fraction sites with as much accuracy as we can. Our pipeline achieves 99% sensitivity at 5% VAF at depths greater than 1000. With depth in the thousands or tens of thousands of reads for most whole genome mitochondrial samples, it should be possible to call most 1% allele fraction sites with high confidence.</p>

<h4>4. High depth coverage is a blessing… and a curse</h4>

<p>The mitochondrial contig typically has extremely high depth in whole genome sequence data:<br>
around 2000x for a typical blood sample compared to autosomes (typically ~30x coverage). Samples from mitochondria-rich tissues like heart and muscle have even higher depth (e.g. 80,000x coverage). This depth is a blessing for calling low-allele fraction sites with confidence, but can overwhelm computations that use algorithms not designed to handle the large amounts of data that come with this extreme depth.</p>

<h3>Solving a geometry problem by realigning twice</h3>

<p>We’ve solved the first problem by extracting reads that align to carefully selected NuMT regions and the mitochondria itself, from a whole genome sample. We take these aligned, recalibrated reads and realign them twice: once to the canonical mitochondria reference, and once to a “rotated” mitochondria reference that moves the breakpoint from the control region to the opposite side of the circular contig.</p>

<p>To help filter out NuMTs, we mark reads with their original alignment position before realigning to the mitochondrial contig. Then we use Mutect2 filters tuned to the high depth we expect from the mitochondria, by running Mutect2 in “--mitochondria-mode”. We increase accuracy on the “breakpoint” location by calling only the non-control region on the original mitochondria reference, and call the control region on the shifted reference (now in the middle of the linearized chromosome). We then shift the calls on the rotated reference back to the original mitochondrial reference and merge the VCFs.</p>

<h3>Adaptive pruning for high sensitivity and precision</h3>

<p>The rest of the problems benefited from recent improvements the local assembly code that  Mutect2 shares with HaplotypeCaller (HC).  Incorporating the new adaptive pruning strategy in the latest version of Mutect2 will improve sensitivity and precision for samples with varying depth across the mitochondrial reference, and enable us to adapt our pipeline to exome and RNA samples. See the blog post on the newest version of Mutect2 here.</p>

<h3>Unblocking genetic bottlenecks with a new pipeline</h3>

<p>The new pipeline’s high sensitivity to low allele fraction variants is especially powerful since low AF variants may be at higher AF in other tissue.</p>

<p>Our pipeline harnesses the power of low AFs to help:</p>

<h5>1. Diagnose rare diseases</h5>

<p>Mutations can be at high allele fraction in affected tissues but low in blood samples typically used for genetic testing.</p>

<h5>2. Identify asymptomatic carriers of pathogenic variants</h5>

<p>If you carry a pathogenic allele even at low VAF, you can pass this along at high VAF to your offspring.</p>

<h5>3. Discover somatic variants in tissues or cell lineages</h5>

<p>For example, studies have used rare somatic mtDNA variants for lineage tracing in single-cell RNA-seq studies.</p>

<p>You can find the WDLs used to run this pipeline in the GATK repo under the scripts directory (<a href="https://github.com/broadinstitute/gatk/blob/master/scripts/mitochondria_m2_wdl/MitochondriaPipeline.wdl" rel="nofollow">https://github.com/broadinstitute/gatk/blob/master/scripts/mitochondria_m2_wdl/MitochondriaPipeline.wdl</a>). Keep an eye out for an official “Best Practices” pipeline, coming soon in the gatk-workflows repo and in Firecloud.</p>

<p><strong>Caveat</strong>: We're not so confident in calls under 5%AF (due to false positives from NuMTs). We're working on a longer term fix for this for a future release.</p>

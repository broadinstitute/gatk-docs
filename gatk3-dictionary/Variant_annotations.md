## Variant annotations

By shlee

<p>Variant annotations are available to HaplotypeCaller, Mutect2, VariantAnnotator and GenotypeGVCFs. These are listed under <em>Annotations</em> in the <a rel="nofollow" href="https://software.broadinstitute.org/gatk/documentation/tooldocs/current/">Tool Documentation</a>.</p>

<ul><li>HaplotypeCaller and Mutect2 calculate annotations based on realigned reads.</li>
<li>If given the optional BAM input, VariantAnnotator will calculate annotations based on the pileup. Otherwise, VariantAnnotator and GenotypeGVCFs calculate summary metrics based on existing VCF fields.</li>
<li>Some annotations, when called by different tools, may give different results.</li>
</ul><p>See related forum discussion <a rel="nofollow" href="https://gatkforums.broadinstitute.org/gatk/discussion/8726/why-dp-in-format-filed-cant-be-re-annotate-base-on-input-bam-file-by-variantannotator">here</a>. To see a list of annotation modules currently available from the GATK4 codebase, see <a rel="nofollow" href="https://github.com/broadinstitute/gatk/tree/master/src/main/java/org/broadinstitute/hellbender/tools/walkers/annotator">here</a>.</p>

<hr></hr>
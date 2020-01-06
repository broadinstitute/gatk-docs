## Notes on differences in GATK 3.5 test results between Java 7 to 8

By Geraldine_VdAuwera

<p>In <a rel="nofollow" href="https://www.broadinstitute.org/gatk/blog?id=7601">my last blog post</a>, I mentioned that GATK 3.6 would support Java 8. I also mentioned that we had some evidence from our Java migration testing that GATK 3.5 (and presumably older versions as well) may produce correctness errors if run on Java 8. Since then quite a few people have expressed concern because they have been running GATK 3.5 or older versions on Java 8 already. Most wanted to know what would be the nature and amplitude of these problems, and whether they should re-run the affected data on Java 7.</p>

<p>We don't have definitive answers for these questions because we haven't performed end-to-end testing of GATK 3.5 on Java 8. Once we noticed that some of the automated tests were failing when we switched Java versions, we hunted down the source of the test failures (some Java list structures for which iteration order is not the same between Java versions) and fixed them.</p>

<p>So for us, the story stops there. But we understand that those of you who have been misguidedly running GATK on Java 8 need more information to decide what to do. I thought that perhaps sharing the relevant details from our migration test results might help, so I compiled a summary of per-tool tests that were affected, with some developer notes and values that were discussed in the issue ticket.</p>

<hr></hr><p>Overall it seems that the pre-processing tools were not affected at all, so you shouldn't need to go back and reprocess any BAM files -- that's some good news right there. Most of the test failures were due to differences in variant annotation values produced by the variant callers. Most of these were very small differences, but a handful were enough to grab our attention.</p>

<p>To be clear, we didn't observe any differences in what was called or not coming out of the callers; however the differences in annotation values could potentially affect filtering decisions in the next steps. So while we can't say definitively whether the end result of the full variant calling pipeline would be the same or not, we suspect there could be differences at least in marginal calls. That being said, based on what we observed in the tests, it seems these differences are mostly on the scale of what you might normally see occur due to downsampling (where using different subsets of reads yields slightly different annotation values, occasionally bumping a variant across a filtering threshold).</p>

<p>If you have a dataset that is affected by this, I would recommend running either one sample again, or a chromosome's worth of intervals across multiple samples, and evaluating how much difference there is in the annotation values. That will allow you to estimate whether your overall results might be affected substantially or not, and whether it's worth re-running samples or not.</p>

<hr></hr><h4>UnifiedGenotyper and HaplotypeCaller</h4>

<ul><li>"GeneralPloidySuite": order of ALT alleles</li>
<li>"IndelCalling": QD 29.00 vs 29.72; InbreedingCoeff 0.3761 vs 0.3495</li>
<li>"NormalCalling": QUAL differences</li>
<li>"ComplexAndSymbolicVariants": InbreedingCoeff value differences</li>
<li>Multiple instances of different QUAL, QD, PL and any PL-dependent annotations</li>
</ul><p><em>Small PL fluctuations -- biggest difference is one GT drops from GQ 13 to GQ 1, which is why MLEAC/MLEAF change.</em></p>

<h4>MuTect2</h4>

<ul><li>TLOD 41.81 vs 36.02</li>
<li>AF 0.246 vs 0.22</li>
</ul><h4>VariantAnnotator</h4>

<ul><li>InbreedingCoeff values are different</li>
</ul><p><em>InbreedingCoeff depends on PL</em></p>

<h4>ReadBackedPhasing</h4>

<ul><li>PhasingInconsistent in new output or old output</li>
</ul><h4>VariantRecalibration</h4>

<ul><li>VQSLOD changes</li>
</ul><h4>CombineVariants</h4>

<ul><li>Order of alleles</li>
<li><code class="code codeInline" spellcheck="false">UG_center_to_keep=[BGI]</code> vs <code class="code codeInline" spellcheck="false">##UG_center_to_keep=[WUGSC]</code></li>
<li><code class="code codeInline" spellcheck="false">#FILTER=</code> vs <code class="code codeInline" spellcheck="false">| ##FILTER=</code></li>
</ul><h4>GenotypeGVCFs</h4>

<ul><li>InbreedingCoeff -0.0000 vs 0.0000</li>
</ul><h4>DepthOfCoverage</h4>

<ul><li>Ordering of values</li>
</ul><h4>Pileup</h4>

<ul><li>Ordering of reads</li>
</ul><h4>CalibrateGenotypeLikelihoods</h4>

<ul><li>Ordering of values</li>
</ul>
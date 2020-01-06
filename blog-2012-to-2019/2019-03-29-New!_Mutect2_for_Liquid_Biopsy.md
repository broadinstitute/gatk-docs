## New! Mutect2 for Liquid Biopsy

By fleharty

<h2>Adapting a proven tool to liquid biopsy studies</h2>

<p><strong><em>By Mark Fleharty &amp; Madeleine Duran</em></strong></p>

<p>Accurate detection of somatic events from liquid biopsies has the potential to revolutionize precision medicine in cancer. But identifying variants at the low allele fractions in blood requires higher base qualities than are typically reported by the Illumina platform. We are happy to unveil a pipeline that overcomes this challenge with an improved Mutect2 and a custom lab process that uses duplex consensus reads to reduce false-negatives. Our pipeline is able to call SNPs from liquid biopsies with 90% sensitivity for somatic SNPs present at a 1% allele fraction - while calling no more than one false positive per megabase, welcome news for patients who today must endure invasive biopsies to detect and track cancer.</p>

<hr></hr><h3>Three considerations for the viable liquid biopsy</h3>

<ul><li><h4>High quality bases are absolutely necessary</h4>

<p>Many artifacts are amplified when sequencing at low allele fractions, meaning it can be difficult to distinguish between biological variants and artifact.</p></li>
<li><h4>Correcting for PCR errors using duplex reads helps reduce false positive rates</h4>

<p>By requiring observations from both strands of the original double stranded molecule, we significantly reduce the effects of sequencing PCR error.</p></li>
<li><h4>High depths are critical</h4>

<p>Duplex depths around 800x are necessary to make reliable calls at 1% allele fraction.  Generating such high depths increases the occurrence of errors that lead to false positives. Depending on the efficiency of duplex recovery, the actual amount of sequencing could be 10-20 times more sequencing.</p></li>
</ul><h3>Meeting the challenges with double strand sequencing</h3>

<p><a rel="nofollow" href="https://en.wikipedia.org/wiki/Duplex_sequencing" title="Duplex sequencing">Duplex sequencing</a> produces reads with considerably fewer PCR and sequencer errors, enabling calling of low allele fraction variants with low false positive rates.Our liquid biopsies take advantage of this with a custom lab process that incorporates 6bp duplex unique molecular indices (UMI). We benchmarked this pipeline by calling SNPs in a 402 gene panel with 2Mb of target territory. The UMIs increase the available depth of reads and, more importantly, reduce sequencer and PCR error by utilizing consensus-called reads.  We use fgbio for calling duplex consensus reads and GATK4.1 Mutect2 for variant calling.  Our pipeline requires that we make an observation of both strands to form a duplex consensus read.</p>

<p><img src="https://us.v-cdn.net/5019796/uploads/editor/0k/1bgmf8nacify.png" alt="" title="" class="embedImage-img importedEmbed-img"></img></p>

<p><img src="https://us.v-cdn.net/5019796/uploads/editor/hx/mag90716t6cd.png" alt="" title="" class="embedImage-img importedEmbed-img"></img></p>

<p>Figure 1.  (a) Sensitivities for allele fractions 1% and above are &gt; 90%.  (b) Two false positives are detected using normal-normal analysis.</p>

<h3>New Data Type, New Error Modes</h3>

<p>When we apply BQSR we frequently find quality scores on the order of q55, an estimate of one error in 300,000 base calls. Qualities like this mean we can make high-confidence variant calls with as few as two reads! Unfortunately, however, with new data types also come new error modes. Read on for more details on these errors, as well as how Mutect2 tackles each one.</p>

<h4>Overcoming PCR error</h4>

<p>Although PCR error is present in all small-panel sequencing, it’s a more serious source of errors at the allele fractions in liquid biopsy studies.  Mutect2 now filters such artifacts with the addition of  a modified strand bias filter that requires observing at least one alt supporting read in the positive and negative directions.</p>

<p><img src="https://us.v-cdn.net/5019796/uploads/editor/4d/a9aa53lwpfo1.png" alt="" title="" class="embedImage-img importedEmbed-img"></img></p>

<p>Figure caption: This IGV screenshot shows a likely PCR artifact: five reads with the alt, but at duplex read depths of about 700x. A modified strand bias filter in Mutect2 removes such alts if they are not observed in both directions.</p>

<h4>Reducing false positives by harnessing N base calls</h4>

<p>N base calls, where some of the duplicate raw reads disagree with each other at a particular loci, are common in duplex consensus reads.</p>

<p>From time to time these calls look reasonable, and can skew results (see Figure 2). Observing known false positives revealed a large number of Ns compared to the number of alt-reads in such cases. We found the presence of large numbers of Ns relative to the number of alt-reads is a good indicator of a false positive. <br><img src="https://us.v-cdn.net/5019796/uploads/editor/gn/yoyw0xfj0dqw.png" alt="" title="" class="embedImage-img importedEmbed-img"></img></p>

<p>Figure 2: IGV screenshot of an apparent variant with duplex evidence in both strand orientations. We know this particular variant is not real because it was not in our truth dataset.</p>

<h3>Benchmarking the difference</h3>

<p>We benchmarked this technology using pooled sample analysis to simulate somatic variants from a tumor-normal analysis. These pooled samples were spiked in at 5%, 2.5% and 1%, as an independent measure of false positive rate (FPR). The normal-normal replicates were taken from the biological source material. Sensitivity for events at ~1% observed allele fraction (2.5% spike-in) exceeds 90% given samples with mean target coverage of 800x duplex coverage with FPR &lt; 1/Mb, our measured false positive rate for the data shown below is 2 FP over 8 Mb of territory.</p>

<h3>In Conclusion</h3>

<p>We are excited to unveil this pipeline for delivering SNPs from a 402 gene liquid biopsy assay. Newly added filters in Mutect2 enable us to take advantage of consensus calling to increase the sensitivity of the assay and enable a path towards a future where patients will be able to replace painful and dangerous biopsies with simple blood draws to track and treat their disease.<br>
In the coming weeks, look for the new pipeline  as an official GATK workflow published by the Broad Institute's Data Sciences Platform in <a rel="nofollow" href="https://github.com/gatk-workflows" title="this github repo">this github repo</a>.</p>

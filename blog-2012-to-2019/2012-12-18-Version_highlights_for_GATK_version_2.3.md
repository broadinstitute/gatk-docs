## Version highlights for GATK version 2.3

By Geraldine_VdAuwera

<h3>Overview</h3>

<p>Release version 2.3 is the last before the winter holidays, so we've done our best not to put in anything that will break easily. Which is not to say there's nothing important - this release contains a truckload of feature tweaks and bug fixes (see the release notes in the next tab for full list). And we do have one major new feature for you: a brand-spanking-new downsampler  to replace the old one.</p>

<h3>Feature improvement highlights</h3>

<h4>- Sanity check for mis-encoded quality scores</h4>

<p>It has recently come to our attention that some datasets are not encoded in the standard format (Q0 == ASCII 33 according to the SAM specification, whereas in some datasets including older Illumina data, encoding starts at ASCII 64). This is a problem because the GATK assumes that it can use the quality scores as they are. If they are in fact encoded using a different scale, our tools will make an incorrect estimation of the quality of your data, and your analysis results will be off. To prevent this from happening, we've added a sanity check of the quality score encodings that will abort the program run if they are not standard. If this happens to you, you'll need to run again with the flag <code class="code codeInline" spellcheck="false">--fix_misencoded_quality_scores</code> (<code class="code codeInline" spellcheck="false">-fixMisencodedQuals</code>). What will happen is that the engine will simply subtract 31 from every quality score as it is read in, and proceed with the corrected values. Output files will include the correct scores where applicable.</p>

<h4>- Overall GATK performance improvement</h4>

<p>Good news on the performance front: we eliminated a bottleneck in the GATK engine that increased the runtime of many tools by as much as 10x, depending on the exact details of the data being fed into the GATK. The problem was caused by the internal timing code invoking expensive system timing resources far too often. Imagine you looked at your watch every two seconds -- it would take you ages to get anything done, right? Anyway, if you see your tools running unusually quickly, don't panic! This may be the reason, and it's a good thing.</p>

<h4>- Co-reducing BAMs with ReduceReads (Full version only)</h4>

<p>You can now co-reduce separate BAM files by passing them in with multiple <code class="code codeInline" spellcheck="false">-I</code> or as an input list. The motivation for this is that samples that you plan to analyze together (e. g. tumor-normal pairs or related cohorts) should be reduced together, so that if a disagreement is triggered at a locus for one sample, that locus will remain unreduced in all samples. You will therefore conserve the full depth of information for later analysis of that locus.</p>

<h3>Downsampling, overhauled</h3>

<p>The downsampler is the component of the GATK engine that handles downsampling, i. e. the process of removing a subset of reads from a pileup. The goal of this process is to speed up execution of the desired analysis, particularly in genome regions that are covered by excessive read depth.</p>

<p>In this release, we have replaced the old downsampler with a brand new one that extends some options and performs much better overall.</p>

<h4>- Downsampling to coverage for read walkers</h4>

<p>The GATK offers two different options for downsampling:</p>

<ul><li><code class="code codeInline" spellcheck="false">--downsample_to_coverage</code> (<code class="code codeInline" spellcheck="false">-dcov</code>) enables you to set the maximum amount of coverage to keep at any position</li>
<li><code class="code codeInline" spellcheck="false">--downsample_to_fraction</code> (<code class="code codeInline" spellcheck="false">-dfrac</code>) enables you to remove a proportional amount of the reads at any position (e. g. take out half of all the reads)</li>
</ul><p>Until now, it was not possible to use the <code class="code codeInline" spellcheck="false">--downsample_to_coverage</code> (<code class="code codeInline" spellcheck="false">-dcov</code>) option with read walkers; you were limited to using <code class="code codeInline" spellcheck="false">--downsample_to_fraction</code> (<code class="code codeInline" spellcheck="false">-dfrac</code>). In the new release, you will be able to downsample to coverage for read walkers.</p>

<p>However, please note that the process is a little different. The normal way of downsampling to coverage (e. g. for locus walkers) involves downsampling over the entire pileup of reads in one take. Due to technical reasons, it is still not possible to do that exact process for read walkers; instead the read-walker-compatible way of doing it involves downsampling within subsets of reads that are all aligned at the same starting position. This different mode of operation means you shouldn't use the same range of values; where you would use <code class="code codeInline" spellcheck="false">-dcov 100</code> for a locus walker, you may need to use <code class="code codeInline" spellcheck="false">-dcov 10</code> for a read walker. And these are general estimates - your mileage may vary depending on your dataset, so we recommend testing before applying on a large scale.</p>

<h4>- No more downsampling bias!</h4>

<p>One important property of the downsampling process is that it should be as random as possible to avoid introducing biases into the selection of reads that will be kept for analysis. Unfortunately our old downsampler - specifically, the part of the downsampler that performed the downsampling to coverage - suffered from some biases. The most egregious problem was that as it walked through the data, it tended to privilege more recently encountered reads and displaced "older" reads. The new downsampler no longer suffers from these biases.</p>

<h4>- More systematic testing</h4>

<p>The old downsampler was embedded in the engine code in a way that made it hard to test in a systematic way. So when we implemented the new downsampler, we reorganized the code to make it a standalone engine component - the equivalent of promoting it from the cubicle farm to its own corner office. This has allowed us to cover it much better with systematic tests, so we have better assessment of whether it's working properly.</p>

<h4>- Option to revert to the old downsampler</h4>

<p>The new downsampler is enabled by default and we are confident that it works much better than the old one. BUT as with all brand-spanking-new features, early adopters may run into unexpected rough patches. So we're providing a way to disable it and use the old one, which is still in the box for now: just add <code class="code codeInline" spellcheck="false">-use_legacy_downsampler</code> to your command line. Obviously if you use this AND <code class="code codeInline" spellcheck="false">-dcov</code> with a read walker, you'll get an error, since the old downsampler can't downsample to coverage for read walkers.</p>

## Using the reference confidence calculation mode and generating a GVCF [RETIRED]

By Mark_DePristo

<h3>This document is out of date and has been replaced by the following: <a href="http://www.broadinstitute.org/gatk/guide/article?id=4017" rel="nofollow">http://www.broadinstitute.org/gatk/guide/article?id=4017</a></h3>

<p>As of GATK 2.7, the HaplotypeCaller is now capable of emitting a per-bp or summarized confidence estimate for a site being strictly homozygous-reference.</p>

<h2>How it works</h2>

<p>The mode works by assembling the reads to create potential haplotypes, realigning the reads to their most likely haplotypes, and then projecting these reads back onto the reference sequence via their haplotypes to compute alignments of the reads to the reference. For each position in the genome we have either a non-reference call (via the standard calling mechanism) or we can estimate the chance that some (unknown) non-reference allele is segregating at this position by examining the realigned reads that span the reference base. At this base we perform two calculations:</p>

<ul><li>Estimate the confidence that no SNP exists at the site by contrasting all reads with the ref base vs all reads with any non-reference base.</li>
<li>Estimate the confidence that no indel of size &lt; X (determined by command line parameter) could exist at this site by calculating the number of reads that provide evidence against such an indel, and from this value estimate the chance that we would not have seen the allele confidently.</li>
</ul><p>Based on this, we emit the genotype likelihoods (<code class="code codeInline" spellcheck="false">PL</code>) and compute the <code class="code codeInline" spellcheck="false">GQ</code> (from the <code class="code codeInline" spellcheck="false">PL</code>s) for the least confidence of these two models.</p>

<p>We use a symbolic allele, <code class="code codeInline" spellcheck="false">&lt;NON_REF&gt;</code>, to indicate that the site is homozygous reference, and because we have an ALT allele we can provide allele-specific <code class="code codeInline" spellcheck="false">AD</code> and <code class="code codeInline" spellcheck="false">PL</code> field values.</p>

<h2>How to use it</h2>

<p>This mode can be enabled with the command line argument <code class="code codeInline" spellcheck="false">--emitRefConfidence (-ERC)</code>, which has 3 options:</p>

<ul><li><code class="code codeInline" spellcheck="false">NONE</code>: don't emit anything (default)</li>
<li><code class="code codeInline" spellcheck="false">BP_RESOLUTION</code>: emit detailed information for each BP</li>
<li><code class="code codeInline" spellcheck="false">GVCF</code>: emit a block summarized version of the <code class="code codeInline" spellcheck="false">BP_RESOLUTION</code> data</li>
</ul><h3>BP_RESOLUTION mode</h3>

<p>This mode emits a very detailed model for the reference confidence at each reference base within the requested calling intervals. The output looks like:</p>

<pre class="code codeBlock" spellcheck="false">20      10000435        .       T       &lt;NON_REF&gt;       .       .       .       GT:AD:CD:DP:GQ:PL       0/0:54,0:53:54:99:0,160,2385
20      10000436        .       G       &lt;NON_REF&gt;       .       .       .       GT:AD:CD:DP:GQ:PL       0/0:53,0:53:53:99:0,159,2011
20      10000437        .       C       &lt;NON_REF&gt;       .       .       .       GT:AD:CD:DP:GQ:PL       0/0:53,0:53:53:99:0,159,1971
20      10000438        .       C       &lt;NON_REF&gt;       .       .       .       GT:AD:CD:DP:GQ:PL       0/0:53,0:53:53:99:0,159,1816
20      10000439        .       T       G       1553.77 .       AC=2;AF=1.00;AN=2;DP=56;FS=0.000;MLEAC=2;MLEAF=1.00;MQ=221.40;MQ0=0;QD=27.75    GT:AD:DP:GQ:PL  1/1:0,55:55:99:1582,164,0
20      10000440        .       T       &lt;NON_REF&gt;       .       .       .       GT:AD:CD:DP:GQ:PL       0/0:55,0:55:55:99:0,166,2063
20      10000441        .       T       &lt;NON_REF&gt;       .       .       .       GT:AD:CD:DP:GQ:PL       0/0:55,0:55:55:99:0,165,2000
20      10000442        .       T       &lt;NON_REF&gt;       .       .       .       GT:AD:CD:DP:GQ:PL       0/0:56,0:56:56:99:0,168,2095
20      10000443        .       A       &lt;NON_REF&gt;       .       .       .       GT:AD:CD:DP:GQ:PL       0/0:56,0:56:56:99:0,169,2089
20      10000444        .       A       &lt;NON_REF&gt;       .       .       .       GT:AD:CD:DP:GQ:PL       0/0:56,0:56:56:99:0,168,2093
20      10000445        .       C       &lt;NON_REF&gt;       .       .       .       GT:AD:CD:DP:GQ:PL       0/0:56,0:56:56:99:0,169,2137
</pre>

<p>This shows each reference base in a 11 bp interval. Only one site (10000439) contains a SNP call. The other 10 bases are reference. For each reference base not part of a variant call we emit a VCF record with have the special symbolic allele <code class="code codeInline" spellcheck="false">&lt;NON_REF&gt;</code> indicating the call is between the reference base and any possible non-reference allele that might be segregating at this site. The genotype fields include the standard GATK <code class="code codeInline" spellcheck="false">GT</code>, <code class="code codeInline" spellcheck="false">AD</code>, <code class="code codeInline" spellcheck="false">DP</code>, <code class="code codeInline" spellcheck="false">GQ</code>, and <code class="code codeInline" spellcheck="false">PL</code> fields, all calculated as ref vs. any non-reference base. There's a new special <code class="code codeInline" spellcheck="false">CD</code> field as well, that gives the number of indel informative reads at this location, given the maximum size of the indel to consider.</p>

<p>Note that there's no site-level <code class="code codeInline" spellcheck="false">QUAL</code> field value.  We discussed this internally and since the <code class="code codeInline" spellcheck="false">QUAL</code> is the probability that the site is polymorphic, all of the <code class="code codeInline" spellcheck="false">QUAL</code> field values should be 0 here, so we decided to drop it.</p>

<h3>GVCF mode</h3>

<p><code class="code codeInline" spellcheck="false">GVCF</code> mode summarizes the output of <code class="code codeInline" spellcheck="false">BP_RESOLUTION</code> by grouping sequential reference calls with similar <code class="code codeInline" spellcheck="false">GQ</code> values into blocks, and emitting summarized blocks instead of the values at each base pair. The blocking is discrete, deterministic, and can be controlled by the command line parameter <code class="code codeInline" spellcheck="false">GVCFGQBands</code>. The algorithm creates bands of <code class="code codeInline" spellcheck="false">GQ</code> values and merges sequential blocks with <code class="code codeInline" spellcheck="false">GQ</code> values within the same band into a single synthetic block.</p>

<p>For example, if the <code class="code codeInline" spellcheck="false">GVCFBQBands</code> argument is <code class="code codeInline" spellcheck="false">-GQB 10 -GQB 20</code> then we'll create blocks in the GVCF where sequential <code class="code codeInline" spellcheck="false">&lt;NON_REF&gt;</code> calls have <code class="code codeInline" spellcheck="false">GQ</code> values either &lt; 10, between 10 and 19, or are 20 or greater.</p>

<p>The <code class="code codeInline" spellcheck="false">GVCF</code> <code class="code codeInline" spellcheck="false">INFO</code> and <code class="code codeInline" spellcheck="false">FORMAT</code> fields have a few new annotations.  First, <code class="code codeInline" spellcheck="false">END</code> and <code class="code codeInline" spellcheck="false">BLOCK_SIZE</code> indicate the extent of the reference block and its size (redundant yes but easy to read). For each <code class="code codeInline" spellcheck="false">GVCF</code> sample field there's now only <code class="code codeInline" spellcheck="false">GT</code>, <code class="code codeInline" spellcheck="false">DP</code> and <code class="code codeInline" spellcheck="false">GQ</code> from the standard annotations. <code class="code codeInline" spellcheck="false">GT</code> is always 0/0 while <code class="code codeInline" spellcheck="false">DP</code> and <code class="code codeInline" spellcheck="false">GQ</code> are representative values (currently median) from the bp resolution <code class="code codeInline" spellcheck="false">DP</code> and <code class="code codeInline" spellcheck="false">GQ</code> values within the block. The <code class="code codeInline" spellcheck="false">MIN_DP</code> and <code class="code codeInline" spellcheck="false">MIN_GQ</code> are the minimum values seen within the block for their respective fields.</p>

<p>The output for the above example in <code class="code codeInline" spellcheck="false">GVCF</code> mode looks like:</p>

<pre class="code codeBlock" spellcheck="false">20      10000435        .       T       &lt;NON_REF&gt;       .       .       BLOCK_SIZE=4;END=10000438       GT:DP:GQ:MIN_DP:MIN_GQ  0/0:53:99:53:159
20      10000439        .       T       G       1553.77 .       AC=2;AF=1.00;AN=2;DP=56;FS=0.000;MLEAC=2;MLEAF=1.00;MQ=221.40;MQ0=0;QD=27.75    GT:AD:DP:GQ:PL  1/1:0,55:55:99:1582,164,0
20      10000440        .       T       &lt;NON_REF&gt;       .       .       BLOCK_SIZE=6;END=10000445       GT:DP:GQ:MIN_DP:MIN_GQ  0/0:56:99:55:165
</pre>

<p>Here's a GVCF output for 100 bp around the same variant:</p>

<pre class="code codeBlock" spellcheck="false">20      10000400        .       T       &lt;NON_REF&gt;       .       .       BLOCK_SIZE=39;END=10000438      GT:DP:GQ:MIN_DP:MIN_GQ  0/0:57:99:53:128
20      10000439        .       T       G       1553.77 .       AC=2;AF=1.00;AN=2;DP=56;FS=0.000;MLEAC=2;MLEAF=1.00;MQ=221.40;MQ0=0;QD=27.75    GT:AD:DP:GQ:PL  1/1:0,55:55:99:1582,164,0
20      10000440        .       T       &lt;NON_REF&gt;       .       .       BLOCK_SIZE=61;END=10000500      GT:DP:GQ:MIN_DP:MIN_GQ  0/0:54:99:49:136
</pre>

<h2>Current caveats</h2>

<ul><li>Currently only works for single sample calling</li>
<li>Currently slows down the calculation significantly (but this will be improved in the future)</li>
<li>Only <code class="code codeInline" spellcheck="false">GVCF</code> mode is likely to work with downstream GATK tools right now, as the special <code class="code codeInline" spellcheck="false">&lt;NON_REF&gt;</code> allele may be treated as a polymorphic site.  If you want to use the BP resolution mode you will likely need to split the VCF into the ref sites and polymorphic sites, perform standard filtering etc to the polymorphic VCF, and then merge the ref and non-ref VCFs back together</li>
</ul><h2>Feedback</h2>

<p>We are interested in community feedback on the output format and additional information we should provide to make the reference model as useful as possible.  Please post suggestions as comments here.</p>

## IMPORTANT -- Bug alert for GATK4 GenomicsDBImport

By Geraldine_VdAuwera

<p>We have identified a major bug in the GenomicsDBImport tool that affects all released beta versions of GATK4 up to 4.beta.5 (inclusive). The bug occurs under specific conditions (detailed below) and causes the output of joint calling to be scrambled across samples, <em>i.e.</em> the sample names will not be associated with the correct sample data. For example, the data for <code class="code codeInline" spellcheck="false">sample1</code> may be labeled as <code class="code codeInline" spellcheck="false">sample3</code>. The good news is that the results are recoverable as long as you have a record of the exact parameters used in the original command.</p>

<p>So if you have used this tool, please read the detailed description of the bug conditions and recovery procedure below. We apologize for any inconvenience this may cause you.</p>

<p>Going forward, everyone who plans to use GenomicsDBImport should upgrade to GATK4 version 4.beta.6 or later.</p>

<hr></hr><h3>Conditions under which the bug occurs</h3>

<p>The bug occurs when all of the following conditions are met:</p>

<ul><li>You used a version of GenomicsDBImport from before GATK4 version 4.beta.6 (for nightlies, up to 4.beta.5-66-g9609cb3-SNAPSHOT);</li>
<li>You used the <code class="code codeInline" spellcheck="false">--batchSize</code> argument with a setting other than 0;</li>
<li>You imported multiple batches, meaning you had more input GVCFs than what your batch size was set to;</li>
<li>The input GVCFs were not sorted according to Java’s natural sorting of strings (see details below).</li>
</ul><p>That last point applies if you specified files using the <code class="code codeInline" spellcheck="false">-V</code> argument or if you used a <code class="code codeInline" spellcheck="false">sampleNameMap</code> file. Note that Java sorts strings lexicographically, not in a numerically aware fashion, so <code class="code codeInline" spellcheck="false">sample1, sample2, …, sample9, sample10, sample11</code> would be considered "Out of order".</p>

<h4>Concrete example</h4>

<p>Given the following <code class="code codeInline" spellcheck="false">samples.txt</code> file:</p>

<pre class="code codeBlock" spellcheck="false">HG00096 HG00096.g.vcf.gz
NA19625 NA19625.g.vcf.gz
HG00268 HG00268.g.vcf.gz
</pre>

<p>This command using <code class="code codeInline" spellcheck="false">sampleNameMap</code>:</p>

<pre class="code codeBlock" spellcheck="false"> ./gatk-launch GenomicsDBImport --sampleNameMap samples.txt --batchSize 2 --genomicsDBWorkspace workspace -L chr21
</pre>

<p>or this one using <code class="code codeInline" spellcheck="false">-V</code>:</p>

<pre class="code codeBlock" spellcheck="false">./gatk-launch GenomicsDBImport -V HG00096.g.vcf.gz -V NA19625.g.vcf.gz -V HG00268.g.vcf.gz --batchSize 2 --genomicsDBWorkspace workspace -L chr1
</pre>

<p>would be affected by the bug and produce corrupted output.</p>

<hr></hr><h3>Recovery procedure</h3>

<p>We have a new tool in GATK4 version 4.beta.6 called FixCallSetSampleOrdering. This tool will take a corrupted callset and reassign the correct names.  It requires that you use the EXACT SAME settings you used in your GenomicsDBImport command for the <code class="code codeInline" spellcheck="false">--batchSize</code> argument and for the method and order you used to specify input files. For the examples shown above, the recovery command would be:</p>

<pre class="code codeBlock" spellcheck="false">./gatk-launch FixCallSetSampleOrder --sampleNameMap samples.txt --batchSize 2 -O fixedCallSet.vcf
</pre>

<p>To be clear, if you specified the input GVCFs with <code class="code codeInline" spellcheck="false">-V</code> originally, you’ll need to create a sample name map with the ordering you used in your command in order to run the fixup tool.</p>

<p>Note that the settings aren’t recorded anywhere in the final output, so if you didn't keep a record of what those settings were, you may need to re-call your dataset from the original BAMs.  However, please contact us before doing so.  We are working on a new fingerprinting tool that should be able to recover the correct names in many cases without recalling. It will also be able to validate that the sample names are correct.</p>

<p>We apologize again for any disruption this may cause to your work.</p>

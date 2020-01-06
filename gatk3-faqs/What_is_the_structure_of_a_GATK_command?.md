## What is the structure of a GATK command?

By Geraldine_VdAuwera

<h4>Overview</h4>

<p>This document describes how GATK commands are structured and how to add arguments to basic command examples.</p>

<hr></hr><h3>Basic java syntax</h3>

<p>Commands for GATK always follow the same basic syntax:</p>

<pre class="code codeBlock" spellcheck="false">java [Java arguments] -jar GenomeAnalysisTK.jar [GATK arguments]
</pre>

<p>The core of the command is <code class="code codeInline" spellcheck="false">java -jar GenomeAnalysisTK.jar</code>, which starts up the GATK program in a Java Virtual Machine (JVM). Any additional java-specific arguments (such as -Xmx to increase memory allocation) should be inserted between <code class="code codeInline" spellcheck="false">java</code> and <code class="code codeInline" spellcheck="false">-jar</code>, like this:</p>

<pre class="code codeBlock" spellcheck="false">java -Xmx4G -jar GenomeAnalysisTK.jar [GATK arguments]
</pre>

<p>The order of arguments between <code class="code codeInline" spellcheck="false">java</code> and <code class="code codeInline" spellcheck="false">-jar</code> is not important.</p>

<hr></hr><h3>GATK arguments</h3>

<p>There are two universal arguments that are required for every GATK command (with very few exceptions, the <code class="code codeInline" spellcheck="false">clp</code>-type utilities), <code class="code codeInline" spellcheck="false">-R</code> for Reference (e.g. <code class="code codeInline" spellcheck="false">-R human_b37.fasta</code>) and <code class="code codeInline" spellcheck="false">-T</code> for Tool name (e.g. <code class="code codeInline" spellcheck="false">-T HaplotypeCaller</code>).</p>

<p>Additional arguments fall in two categories:</p>

<ul><li><p>Engine arguments like <code class="code codeInline" spellcheck="false">-L</code> (for specifying a list of intervals) which can be given to all tools and are technically optional but may be effectively required at certain steps for specific analytical designs (e.g. the <code class="code codeInline" spellcheck="false">-L</code> argument for calling variants on exomes);</p></li>
<li><p>Tool-specific arguments which may be required, like <code class="code codeInline" spellcheck="false">-I</code> (to provide an input file containing sequence reads to tools that process BAM files) or optional, like <code class="code codeInline" spellcheck="false">-alleles</code> (to provide a list of known alleles for genotyping).</p></li>
</ul><p>The ordering of GATK arguments is not important, but we recommend always passing the tool name (<code class="code codeInline" spellcheck="false">-T</code>) and reference (<code class="code codeInline" spellcheck="false">-R</code>) first for consistency. It is also a good idea to consistently order arguments by some kind of logic in order to make it easy to compare different commands over the course of a project. It’s up to you to choose what that logic should be.</p>

<p>All available engine and tool-specific arguments are listed in the <a rel="nofollow" href="https://www.broadinstitute.org/gatk/guide/tooldocs">tool documentation section</a>. Arguments typically have both a long name (prefixed by <code class="code codeInline" spellcheck="false">--</code>) and a short name (prefixed by <code class="code codeInline" spellcheck="false">-</code>). The GATK command line parser recognizes both equally, so you can use whichever you prefer, depending on whether you prefer commands to be more verbose or more succinct.</p>

<p>Finally, a note about flags. Flags are arguments that have boolean values, i.e. TRUE or FALSE. They are typically used to enable or disable specific features; for example, <code class="code codeInline" spellcheck="false">--keep_program_records</code> will make certain GATK tools output additional information in the BAM header that would be omitted otherwise. In GATK, all flags are set to FALSE by default, so if you want to set one to TRUE, all you need to do is add the flag name to the command. You don't need to specify an actual value.</p>

<hr></hr><h3>Examples of complete GATK command lines</h3>

<p>This is a very simple command that runs HaplotypeCaller in default mode on a single input BAM file containing sequence data and outputs a VCF file containing raw variants.</p>

<pre class="code codeBlock" spellcheck="false">java -Xmx4G -jar GenomeAnalysisTK.jar -R human_b37.fasta -T HaplotypeCaller -I sample1.bam -o raw_variants.vcf
</pre>

<p>If the data is from exome sequencing, we should additionally provide the exome targets using the <code class="code codeInline" spellcheck="false">-L</code> argument:</p>

<pre class="code codeBlock" spellcheck="false">java -Xmx4G -jar GenomeAnalysisTK.jar -R human_b37.fasta -T HaplotypeCaller -I sample1.bam -o raw_variants.vcf -L exome_intervals.list
</pre>

<p>If we just want to genotype specific sites of interest using known alleles based on results from a previous study, we can change the HaplotypeCaller’s genotyping mode using <code class="code codeInline" spellcheck="false">-gt_mode</code>, provide those alleles using <code class="code codeInline" spellcheck="false">-alleles</code>, and restrict the analysis to just those sites using <code class="code codeInline" spellcheck="false">-L</code>:</p>

<pre class="code codeBlock" spellcheck="false">java -Xmx4G -jar GenomeAnalysisTK.jar -R human_b37.fasta -T HaplotypeCaller -I sample1.bam -o raw_variants.vcf -L known_alleles.vcf -alleles known_alleles.vcf -gt_mode GENOTYPE_GIVEN_ALLELES
</pre>

<p>For more examples of commands and for specific tool commands, see the <a rel="nofollow" href="https://www.broadinstitute.org/gatk/guide/tooldocs">tool documentation section</a>.</p>

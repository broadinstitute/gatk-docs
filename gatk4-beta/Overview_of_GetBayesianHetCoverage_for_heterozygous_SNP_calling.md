## Overview of GetBayesianHetCoverage for heterozygous SNP calling

By LeeTL1220

<p>The GetBayesianHetCoverage tool has two main run modes: (1) tumor-only, and (2) matched normal-tumor. It has an I/O similar to the GetHetCoverage tool and may be seamlessly interchanged with GetHetCoverage in the workflow. The output file, however, contains extra columns (see below).</p>

<p>Tumor-only mode:</p>

<p>Command line:</p>

<pre class="code codeBlock" spellcheck="false">java -jar gatk-protected.jar GetBayesianHetCoverage
    --reference &lt;reference genome FASTA file&gt;
    --snpIntervals &lt;common SNPs IntervalList file&gt;
    --tumor &lt;tumor BAM file&gt;
    --tumorHets &lt;output file path for tumor Het SNP coverage&gt;
</pre>

<p>Useful optional arguments:</p>

<pre class="code codeBlock" spellcheck="false">    --hetCallingStringency &lt;Het SNP calling stringency&gt;
    --minimumAbnormalFraction &lt;estimate of the minimum fraction of cells with CNV events&gt;
    --maximumAbnormalFraction &lt;estimate of the maximum fraction of cells with CNV events&gt;
    --maximumCopyNumber &lt;estimate of the maximum copy number for cells with CNV events&gt;
    --quadratureOrder &lt;grid size for numerical integrations&gt;
</pre>

<p>Output:</p>

<p>One tab-separated file containing Het SNPs called from the tumor BAM file, including Ref/Alt alleles, their counts, and the pileup size at each genomic position.</p>

<p>Matched normal-tumor mode:</p>

<p>Command line:</p>

<pre class="code codeBlock" spellcheck="false">java -jar gatk-protected.jar GetBayesianHetCoverage
    --reference &lt;reference genome FASTA file&gt;
    --snpIntervals &lt;common SNPs IntervalList file&gt;
    --tumor &lt;tumor BAM file&gt;
    --tumorHets &lt;output file path for tumor Het SNP events&gt;
    --normal &lt;normal BAM file&gt;
    --normalHets &lt;output file path for normal Het SNP events&gt;
</pre>

<p>Useful optional arguments:</p>

<pre class="code codeBlock" spellcheck="false">    --hetCallingStringency &lt;Het SNP calling stringency&gt;
</pre>

<p>Output:</p>

<p>Two tab-separated files containing Het SNPs called from the normal and tumor BAM files, respectively. The normal output file contains Ref/Alt alleles, their counts, the pileup size, and log odds of being a Het SNP at each genomic position. The tumor output file contains statistics collected from the tumor BAM file on the Het SNPs called from the normal BAM file: Ref/Alt alleles, their counts in tumor BAM, and the pileup size at each genomic position.</p>

<p>Note: the tool may also be run in the normal-only mode but it is not intended to be used with the ACNV workflow. It may be used as a standalone Het SNP caller with a somewhat similar functionality to MuTect and UnifiedGenotyper. Note that it only calls Het SNPs from a given list of possible candidates (see below).</p>

<p>Normal-only mode:</p>

<p>Command line:</p>

<pre class="code codeBlock" spellcheck="false">java -jar gatk-protected.jar GetBayesianHetCoverage
    --reference &lt;reference genome FASTA file&gt;
    --snpIntervals &lt;common SNPs IntervalList file&gt;
    --normal &lt;normal BAM file&gt;
    --normalHets &lt;output file path for normal Het SNP events&gt;
</pre>

<p>Useful optional arguments:</p>

<pre class="code codeBlock" spellcheck="false">    --hetCallingStringency &lt;Het SNP calling stringency&gt;
</pre>

<p>Output:</p>

<p>One tab-separated file containing Het SNPs called from the normal BAM file, including Ref/Alt alleles, their counts, the pileup size, and log odds of being a Het SNP at each genomic position.</p>

<p>Other use optional arguments (can be used in all modes):</p>

<pre class="code codeBlock" spellcheck="false">    --minimumMappingQuality &lt;minimum PHRED-scaled mapping quality&gt;
    --minimumBaseQuality &lt;minimum PHRED-scaled base quality&gt;
</pre>

## Description and examples of the steps in the ACNV case workflow

By LeeTL1220

<p>Once you have run GATK CNV, you can run ACNV for revised segments based on both the target-coverage profile and the ref/alt counts at heterozygous SNPs.  ACNV will report estimates for the posterior probabilities for copy ratio and minor-allele fraction in each segment.</p>

<h2>The ACNV case workflow (description and examples)</h2>

<h4>Requirements</h4>

<ol><li>Java 1.8</li>
<li>A functioning GATK4-protected jar (hellbender-protected.jar or gatk-protected.jar)</li>
<li>Reference genome (fasta files) with fai and dict files.  This can be downloaded as part of the GATK resource bundle: <a href="http://www.broadinstitute.org/gatk/guide/article?id=1213" rel="nofollow">http://www.broadinstitute.org/gatk/guide/article?id=1213</a></li>
<li>Samples must be paired.  You will need both a case sample (typically, a tumor) and a control sample (typically, a blood normal).  We are working on alleviating this requirement.</li>
<li>A list of common heterozygous SNP sites.  Currently, this needs to be in the Picard interval-list format.  See <a href="http://gatkforums.broadinstitute.org/gatk/discussion/7812/creating-a-list-of-common-snps-for-use-with-getbayesianhetcoverage" rel="nofollow">http://gatkforums.broadinstitute.org/gatk/discussion/7812/creating-a-list-of-common-snps-for-use-with-getbayesianhetcoverage</a></li>
<li>A completed run of GATK CNV for the case sample.</li>
</ol><h5>Overview of steps</h5>

<ol><li>Identify heterozygous SNPs in the normal and aggregate read counts at these sites in the tumor.</li>
<li>Segment the case sample (based on both the read counts from step 1 and input from GATK CNV) and estimate copy ratio and minor-allele fraction in each segment.</li>
<li>Call copy-neutral loss-of-heterozygosity and balanced segments.  This step will also create files that can be used as input for ABSOLUTE (Broad-internal versions only) and TITAN.</li>
</ol><h5>Step 1. Het Pulldown</h5>

<p>** These instructions describe one method for Het Pulldown for matched samples.  For more options, including tumor-only, please see: <a href="http://gatkforums.broadinstitute.org/gatk/discussion/7719/overview-of-getbayesianhetcoverage-for-heterozygous-snp-calling" rel="nofollow">http://gatkforums.broadinstitute.org/gatk/discussion/7719/overview-of-getbayesianhetcoverage-for-heterozygous-snp-calling</a> **</p>

<h6>Inputs</h6>

<ul><li>control_bam -- BAM file for control sample (normal).</li>
<li>case_bam -- BAM file for case sample (tumor).</li>
<li>reference_sequence -- FASTA file for b37 reference.</li>
<li>snp_file -- Picard interval list of common SNP sites at which to test for heterozygosity in the control sample .</li>
</ul><h6>Outputs</h6>

<ul><li>normal_het_pulldown -- TSV file with M entries containing ref/alt counts, ref/alt bases, etc., where M is the number of hets called in the control sample.</li>
<li>tumor_het_pulldown -- TSV file with M entries containing ref/alt counts, ref/alt bases, etc. for sites in the case sample that were called as het in the control sample, where M is the number of hets called in the control sample.</li>
</ul><p>Format for both output files:</p>

<pre class="code codeBlock" spellcheck="false">CONTIG  POSITION        REF_COUNT       ALT_COUNT       REF_NUCLEOTIDE  ALT_NUCLEOTIDE  READ_DEPTH
1       809876  5       16      A       G       21
1       881627  23      12      G       A       35
1       882033  9       10      G       A       19
1       900505  26      24      G       C       50
....snip....
</pre>

<h6>Invocation</h6>

<pre class="code codeBlock" spellcheck="false">java -jar &lt;path_to_gatk_protected_jar&gt; GetBayesianHetCoverage --reference &lt;reference_sequence&gt;
    --snpIntervals &lt;snp_file&gt; --tumor &lt;case_bam&gt; --tumorHets &lt;tumor_het_pulldown&gt; --normal &lt;control_bam&gt;
    --normalHets &lt;normal_het_pulldown&gt; --hetCallingStringency 30
</pre>

<h5>Step 2. Allelic CNV</h5>

<h6>Inputs</h6>

<ul><li>tumor_het_pulldown -- Generated in step 1.</li>
<li>coverage_profile -- Tangent-normalized coverage TSV file obtained in the GATK CNV case workflow.</li>
<li>called_segments -- Called-segments TSV file obtained in the GATK CNV case workflow.</li>
<li>output_prefix -- Path and file prefix for creating the output files.  For example, /home/lichtens/my_acnv_output/sample1</li>
</ul><h6>Outputs</h6>

<ul><li>acnv_segments -- TSV file with name ending with <code class="code codeInline" spellcheck="false">-sim-final.seg</code> containing posterior summary statistics for log_2 copy ratio and minor-allele fraction in each segment.  Using the above output_prefix,  /home/lichtens/my_acnv_output/sample1-sim-final.seg</li>
<li>acnv_cr_parameters -- TSV file with name ending with <code class="code codeInline" spellcheck="false">-sim-final.cr.param</code> containing posterior summary statistics for global parameters of the copy-ratio model.  Using the above output_prefix,  /home/lichtens/my_acnv_output/sample1-sim-final.cr.param</li>
<li>acnv_af_parameters -- TSV file with name ending with <code class="code codeInline" spellcheck="false">-sim-final.af.param</code> containing posterior summary statistics for global parameters of the allele-fraction model.  Using the above output_prefix,  /home/lichtens/my_acnv_output/sample1-sim-final.af.param</li>
</ul><p>Other files containing intermediate results of the calculation are also generated.</p>

<h6>Invocation</h6>

<pre class="code codeBlock" spellcheck="false"> java -Xmx8g -jar &lt;path_to_gatk_protected_jar&gt; AllelicCNV  --tumorHets &lt;tumor_het_pulldown&gt;
    --tangentNormalized &lt;coverage_profile&gt; --segments &lt;called_segments&gt; --outputPrefix &lt;output_prefix&gt;
</pre>

<h5>Step 3. Call CNLoH and Balanced Segments</h5>

<p>** WARNING: This tool is experimental and exists primarily for internal Broad use. **</p>

<h6>Inputs</h6>

<ul><li>tumor_het_pulldown -- Generated in step 1.</li>
<li>acnv_segments -- Generated in step 2 (*-sim-final.seg).</li>
<li>coverage_profile -- Tangent-normalized coverage TSV file obtained in the GATK CNV case workflow</li>
<li>output_dir -- Directory for creating the output files.  For example, /home/lichtens/my_acnv_cnlohcalls_output/</li>
</ul><h6>Outputs</h6>

<ul><li>GATK-CNV-formatted seg file -- TSV file ending with <code class="code codeInline" spellcheck="false">-sim-final.cnv.seg</code>.  This file is formatted identically as the output of GATK CNV.  Note that this implies that the allelic fraction values are not captured in this file.</li>
<li>AllelicCapSeg-formatted seg file -- TSV file ending with <code class="code codeInline" spellcheck="false">-sim-final.acs.seg</code>.  This file is formatted identically as the output of Broad CGA AllelicCapSeg.  Note that this file can be used as input to Broad-internal versions of ABSOLUTE.</li>
<li>TITAN-compatible het file --TSV file ending with <code class="code codeInline" spellcheck="false">-sim-final.titan.het.tsv</code>.  This file can be used as the input to TITAN for the het read counts.</li>
<li>TITAN-compatible copy-ratio file -- TSV file ending with <code class="code codeInline" spellcheck="false">-sim-final.titan.tn.tsv</code>.  This file can be used as the input to TITAN for the per-target copy-ratio estimates.</li>
</ul><h6>Invocation</h6>

<pre class="code codeBlock" spellcheck="false"> java -Xmx8g -jar &lt;path_to_gatk_protected_jar&gt; CallCNLoHAndSplits  --tumorHets &lt;tumor_het_pulldown&gt;
    --segments &lt;acnv_segments&gt; --tangentNormalized &lt;coverage_profile&gt; --outputDir &lt;output_dir&gt;
    --rhoThreshold 0.2 --numIterations 10  --sparkMaster local[*]  
</pre>

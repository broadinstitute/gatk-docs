## HLA Caller

By delangel

<h3>Please note that this article has not been updated in a very long time and may no longer be applicable. Use at your own risk.</h3>

<hr></hr><p><strong>WARNING: unfortunately we do not have the resources to directly support the HLA typer at this time.  As such this tool is no longer under active development or supported by our group.   The source code is available in the GATK <em>as is</em>.  This tool may or may not work without substantial experimentation by an analyst.</strong></p>

<h3>Introduction</h3>

<p>Inherited DNA sequence variation in the major histocompatibilty complex (MHC) on human chromosome 6 significantly influences the inherited risk for autoimmune diseases and the host response to pathogenic infections. Collecting allelic sequence information at the classical human leukocyte antigen (HLA) genes is critical for matching in organ transplantation and for genetic association studies, but is complicated due to the high degree of polymorphism across the MHC. Next-generation sequencing offers a cost-effective alternative to Sanger-based sequencing, which has been the standard for classical HLA typing. To bridge the gap between traditional typing and newer sequencing technologies, we developed a generic algorithm to call HLA alleles at 4-digit resolution from next-generation sequence data.</p>

<h3>Downloading the HLA tools</h3>

<p>The HLA-specific walkers/tools (FindClosestHLA, CalculateBaseLikelihoods, and HLACaller) are available as a separate download from our FTP site and as source code only.  Instructions for obtaining and compiling them are as follows:</p>

<p>Download the source code (in a tar ball):</p>

<p>location: <a href="ftp://gsapubftp-anonymous@ftp.broadinstitute.org" rel="nofollow">ftp://gsapubftp-anonymous@ftp.broadinstitute.org</a><br>
   password: &lt;blank&gt;<br>
   subdirectory: HLA/</p>

<p>Untar the file, 'cd' into the untar'ed directory and compile with 'ant'</p>

<p>Remember that we no longer support this tool, so if you encounter issues with any of these steps please do <em>NOT</em> post them to our support forum.</p>

<h3>The algorithm</h3>

<p>Algorithmic components of the HLA caller:</p>

<p><img src="http://www.broadinstitute.org/gatk/media/pics/HLAcaller.jpg" alt="HLA caller" class="embedImage-img importedEmbed-img"></img></p>

<p>The HLA caller algorithm, developed as part of the open-source GATK, examines sequence reads aligned to the classical HLA loci taking SAM/BAM formatted files as input and calculates, for each locus, the posterior probabilities for all pairs of classical alleles based on three key considerations: (1) genotype calls at each base position,  (2) phase information of nearby variants, and (3) population-specific allele frequencies. See the diagram below for a visualization of the heuristic. The output of the algorithm is a list of HLA allele pairs with the highest posterior probabilities.</p>

<p>Functionally, the HLA caller was designed to run in three steps:</p>

<ol><li><p>The "FindClosestAllele" walker detects misaligned reads by comparing each read to the dictionary of HLA alleles (reads with &lt; 75% SNP homology to the closest matching allele are removed)</p></li>
<li><p>The "CalculateBaseLikelihoods" walker calculates the likelihoods for each genotype at each position within the HLA loci and finds the polymorphic sites in relation to the reference</p></li>
<li><p>The "HLAcaller" walker reads the output of the previous steps, and makes the likelihood / probability calculations based on base genotypes, phase information, and allele frequencies.</p></li>
</ol><h3>Required inputs</h3>

<p>These inputs are required:</p>

<ul><li>Aligned sequence (.bam) file - input data</li>
<li>Genomic reference (.bam) file - human genome build 36.</li>
<li>HLA exons (HLA.intervals) file - list of HLA loci / exons to examine.</li>
<li>HLA dictionary - list of HLA alleles, DNA sequences, and genomic positions.</li>
<li>HLA allele frequencies - allele frequencies for HLA alleles across multiple populations.</li>
<li>HLA polymorphic sites - list of polymorphic sites (used by FindClosestHLA walker)</li>
</ul><p>You can download the latter four <a rel="nofollow" href="http://www.broadinstitute.org/gatk/media/docs/HLA_REFERENCE.zip">here</a>.</p>

<h3>Usage and Arguments</h3>

<h4>Standard GATK arguments (applies to subsequent functions)</h4>

<p>The GATK contains a wealth of tools for analysis of sequencing data. Required inputs include an aligned bam file and reference fasta file. The following example shows how to calculate depth of coverage.</p>

<p>Usage:</p>

<pre class="code codeBlock" spellcheck="false">java -jar GenomeAnalysisTK.jar -T DepthOfCoverage -I input.bam -R ref.fasta -L input.intervals &gt; output.doc
</pre>

<p>Arguments:</p>

<ul><li><code class="code codeInline" spellcheck="false">-T</code> (required) name of walker/function</li>
<li><code class="code codeInline" spellcheck="false">-I</code> (required) Input (.bam) file.</li>
<li><code class="code codeInline" spellcheck="false">-R</code> (required) Genomic reference (.fasta) file.</li>
<li><code class="code codeInline" spellcheck="false">-L</code> (optional) Interval or list of genomic intervals to run the genotyper on.</li>
</ul><h4>FindClosestHLA</h4>

<p>The FindClosestHLA walker traverses each read and compares it to all overlapping HLA alleles (at specific polymorphic sites), and identifies the closest matching alleles. This is useful for detecting misalignments (low concordance with best-matching alleles), and helps narrow the list of candidate alleles (narrowing the search space reduces computational speed) for subsequent analysis by the HLACaller walker. Inputs include the HLA dictionary, a list of polymorphic sites in the HLA, and the exons of interest. Output is a file (output.filter) that includes the closest matching alleles and statistics for each read.</p>

<p>Usage:</p>

<pre class="code codeBlock" spellcheck="false">java -jar GenomeAnalysisTK.jar -T FindClosestHLA -I input.bam -R ref.fasta -L HLA_EXONS.intervals -HLAdictionary HLA_DICTIONARY.txt \ 
    -PolymorphicSites HLA_POLYMORPHIC_SITES.txt -useInterval HLA_EXONS.intervals | grep -v INFO &gt; output.filter
</pre>

<p>Arguments:</p>

<ul><li><code class="code codeInline" spellcheck="false">-HLAdictionary</code> (required) HLA_DICTIONARY.txt file</li>
<li><code class="code codeInline" spellcheck="false">-PolymorphicSites</code> (required) HLA_POLYMORPHIC_SITES.txt file</li>
<li><code class="code codeInline" spellcheck="false">-useInterval</code> (required) HLA_EXONS.intervals file</li>
</ul><h4>CalculateBaseLikelihoods</h4>

<p>CalculateBestLikelihoods walker traverses each base position to determine the likelihood for each of the 10 diploid genotypes. These calculations are used later by HLACaller to determine likelihoods for HLA allele pairs based on genotypes, as well as determining the polymorphic sites used in the phasing algorithm. Inputs include aligned bam input, (optional) results from FindClosestHLA (to remove misalignments), and cutoff values for inclusion or exclusion of specific reads. Output is a file (output.baselikelihoods) that contains base likelihoods at each position.</p>

<p>Usage:</p>

<pre class="code codeBlock" spellcheck="false">java -jar GenomeAnalysisTK.jar -T CalculateBaseLikelihoods -I input.bam -R ref.fasta -L HLA_EXONS.intervals -filter output.filter \
-maxAllowedMismatches 6 -minRequiredMatches 0  | grep -v "INFO"  | grep -v "MISALIGNED" &gt; output.baselikelihoods
</pre>

<p>Arguments:</p>

<ul><li><code class="code codeInline" spellcheck="false">-filter</code> (optional) file = output of FindClosestHLA walker (output.filter - to exclude misaligned reads in genotype calculations)</li>
<li><code class="code codeInline" spellcheck="false">-maxAllowedMismatches</code> (optional) max number of mismatches tolerated between a read and the closest allele (default = 6)</li>
<li><code class="code codeInline" spellcheck="false">-minRequiredMatches</code> (optional) min number of base matches required between a read and the closest allele (default = 0)</li>
</ul><h4>HLACaller</h4>

<p>The HLACaller walker calculates the likelihoods for observing pairs of HLA alleles given the data based on genotype, phasing, and allele frequency information. It traverses through each read as part of the phasing algorithm to determine likelihoods based on phase information. The inputs include an aligned bam files, the outputs from FindClosestHLA and CalculateBaseLikelihoods, the HLA dictionary and allele frequencies, and optional cutoffs for excluding specific reads due to misalignment (maxAllowedMismatches and minRequiredMatches).</p>

<p>Usage:</p>

<pre class="code codeBlock" spellcheck="false">java -jar GenomeAnalysisTK.jar -T HLACaller -I input.bam -R ref.fasta -L HLA_EXONS.intervals -filter output.filter -baselikelihoods output.baselikelihoods\
-maxAllowedMismatches 6 -minRequiredMatches 5  -HLAdictionary HLA_DICTIONARY.txt -HLAfrequencies HLA_FREQUENCIES.txt | grep -v "INFO"  &gt; output.calls
</pre>

<p>Arguments:</p>

<ul><li><code class="code codeInline" spellcheck="false">-baseLikelihoods</code> (required) output of CalculateBaseLikelihoods walker (output.baselikelihoods - genotype likelihoods / list of polymorphic sites from the data)</li>
<li><code class="code codeInline" spellcheck="false">-HLAdictionary</code> (required) HLA_DICTIONARY.txt file</li>
<li><code class="code codeInline" spellcheck="false">-HLAfrequencies</code> (required) HLA_FREQUENCIES.txt file</li>
<li><code class="code codeInline" spellcheck="false">-useInterval</code> (required) HLA_EXONS.intervals file</li>
<li><code class="code codeInline" spellcheck="false">-filter</code> (optional) file = output of FindClosestAllele walker (to exclude misaligned reads in genotype calculations)</li>
<li><code class="code codeInline" spellcheck="false">-maxAllowedMismatches</code> (optional) max number of mismatched bases tolerated between a read and the closest allele (default = 6)</li>
<li><code class="code codeInline" spellcheck="false">-minRequiredMatches</code> (optional) min number of base matches required between a read and the closest allele (default = 5)</li>
<li><code class="code codeInline" spellcheck="false">-minFreq</code> (option) minimum allele frequency required to consider the HLA allele (default = 0.0).</li>
</ul><h4>Example workflow (genome-wide HiSeq data in NA12878 from HapMap; computations were performed on the Broad servers)</h4>

<p>Extract sequences from the HLA loci and make a new bam file:</p>

<pre class="code codeBlock" spellcheck="false">use Java-1.6

set HLA=/seq/NKseq/sjia/HLA_CALLER
set GATK=/seq/NKseq/sjia/Sting/dist/GenomeAnalysisTK.jar
set REF=/humgen/1kg/reference/human_b36_both.fasta

cp $HLA/samheader NA12878.HLA.sam

java -jar $GATK -T PrintReads \
-I /seq/dirseq/ftp/NA12878_exome/NA12878.bam -R /seq/references/Homo_sapiens_assembly18/v0/Homo_sapiens_assembly18.fasta \
-L $HLA/HLA.intervals | grep -v RESULT | sed 's/chr6/6/g' &gt;&gt; NA12878.HLA.sam

/home/radon01/sjia/bin/SamToBam.csh NA12878.HLA
</pre>

<p>Use FindClosestHLA to find closest matching HLA alleles and to detect possible misalignments:</p>

<pre class="code codeBlock" spellcheck="false">java -jar $GATK -T FindClosestHLA -I NA12878.HLA.bam -R $REF -L $HLA/HLA_EXONS.intervals -useInterval $HLA/HLA_EXONS.intervals \
-HLAdictionary $HLA/HLA_DICTIONARY.txt -PolymorphicSites $HLA/HLA_POLYMORPHIC_SITES.txt | grep -v INFO &gt; NA12878.HLA.filter

READ_NAME   START-END       S   %Match  Matches Discord Alleles
20FUKAAXX100202:7:63:8309:75917 30018423-30018523   1.0 1.000   1   0   HLA_A*01010101,HLA_A*01010102N,HLA_A*010102,HLA_A*010103,HLA_A*010104,...
20GAVAAXX100126:3:24:13495:18608    30018441-30018541   1.0 1.000   3   0   HLA_A*0312,HLA_A*110101,HLA_A*110102,HLA_A*110103,HLA_A*110104,...
20FUKAAXX100202:8:44:16857:92134    30018442-30018517   1.0 1.000   1   0   HLA_A*01010101,HLA_A*01010102N,HLA_A*010102,HLA_A*010103,HLA_A*010104,HLA_A*010105,...
20FUKAAXX100202:8:5:4309:85338  30018452-30018552   1.0 1.000   3   0   HLA_A*0312,HLA_A*110101,HLA_A*110102,HLA_A*110103,HLA_A*110104,HLA_A*110105,...
20GAVAAXX100126:3:28:7925:160832    30018453-30018553   1.0 1.000   3   0   HLA_A*0312,HLA_A*110101,HLA_A*110102,HLA_A*110103,HLA_A*110104,HLA_A*110105,...
20FUKAAXX100202:1:2:10539:169258    30018459-30018530   1.0 1.000   1   0   HLA_A*01010101,HLA_A*01010102N,HLA_A*010102,HLA_A*010103,.
20FUKAAXX100202:8:43:18611:44456    30018460-30018560   1.0 1.000   3   0   HLA_A*01010101,HLA_A*01010102N,HLA_A*010102,HLA_A*010103,HLA_A*010104,...
</pre>

<p>Use CalculateBaseLikelihoods to determine genotype likelihoods at every base position:</p>

<pre class="code codeBlock" spellcheck="false">java -jar $GATK -T CalculateBaseLikelihoods -I NA12878.HLA.bam -R $REF -L $HLA/HLA_EXONS.intervals \
-filter NA12878.HLA.filter -maxAllowedMismatches 6 -minRequiredMatches 0 | grep -v INFO  | grep -v MISALIGNED &gt; NA12878.HLA.baselikelihoods

chr:pos     Ref Counts          AA  AC  AG  AT  CC  CG  CT  GG  GT  TT
6:30018513  G   A[0]C[0]T[1]G[39]   -113.58 -113.58 -13.80  -113.29 -113.58 -13.80  -113.29 -3.09   -13.50  -113.11
6:30018514  C   A[0]C[39]T[0]G[0]   -119.91 -13.00  -119.91 -119.91 -2.28   -13.00  -13.00  -119.91 -119.91 -119.91
6:30018515  T   A[0]C[0]T[39]G[0]   -118.21 -118.21 -118.21 -13.04  -118.21 -118.21 -13.04  -118.21 -13.04  -2.35
6:30018516  C   A[0]C[38]T[1]G[0]   -106.91 -13.44  -106.91 -106.77 -3.05   -13.44  -13.30  -106.91 -106.77 -106.66
6:30018517  C   A[0]C[38]T[0]G[0]   -103.13 -13.45  -103.13 -103.13 -3.64   -13.45  -13.45  -103.13 -103.13 -103.13
6:30018518  C   A[0]C[38]T[0]G[0]   -112.23 -12.93  -112.23 -112.23 -2.71   -12.93  -12.93  -112.23 -112.23 -112.23
...
</pre>

<p>Run HLACaller using outputs from previous steps to determine the most likely alleles at each locus:</p>

<pre class="code codeBlock" spellcheck="false">java -jar $GATK -T HLACaller -I NA12878.HLA.bam -R $REF -L $HLA/HLA_EXONS.intervals -useInterval $HLA/HLA_EXONS.intervals \
-bl NA12878.HLA.baselikelihoods -filter NA12878.HLA.filter -maxAllowedMismatches 6 -minRequiredMatches 5 \
-HLAdictionary $HLA/HLA_DICTIONARY.txt -HLAfrequencies $HLA/HLA_FREQUENCIES.txt &gt; NA12878.HLA.info

grep -v INFO NA12878.HLA.info &gt; NA12878.HLA.calls

Locus   A1  A2  Geno    Phase   Frq1    Frq2    L   Prob    Reads1  Reads2  Locus   EXP White   Black   Asian
A   0101    1101    -1229.5 -15.2   -0.82   -0.73   -1244.7 1.00    180 191 229 1.62    -1.99   -3.13   -2.07
B   0801    5601    -832.3  -37.3   -1.01   -2.15   -872.1  1.00    58  59  100 1.17    -3.31   -4.10   -3.95
C   0102    0701    -1344.8 -37.5   -0.87   -0.86   -1384.2 1.00    91  139 228 1.01    -2.35   -2.95   -2.31
DPA1    0103    0201    -842.1  -1.8    -0.12   -0.79   -846.7  1.00    72  48  120 1.00    -0.90   -INF    -1.27
DPB1    0401    1401    -991.5  -18.4   -0.45   -1.55   -1010.7 1.00    64  48  113 0.99    -2.24   -3.14   -2.64
DQA1    0101    0501    -1077.5 -15.9   -0.90   -0.62   -1095.4 1.00    160 77  247 0.96    -1.53   -1.60   -1.87
DQB1    0201    0501    -709.6  -18.6   -0.77   -0.76   -729.7  0.95    50  87  137 1.00    -1.76   -1.54   -2.23
DRB1    0101    0301    -1513.8 -317.3  -1.06   -0.94   -1832.6 1.00    52  32  101 0.83    -1.99   -2.83   -2.34
</pre>

<p>Make a SAM/BAM file of the called alleles:</p>

<pre class="code codeBlock" spellcheck="false">awk '{if (NR &gt; 1){print $1 "*" $2 "\n" $1 "*" $3}}' NA12878.HLA.calls | sort -u &gt; NA12878.HLA.calls.unique
cp $HLA/samheader NA12878.HLA.calls.sam
awk '{split($1,a,"*"); print "grep \"" a[1] "[*]" a[2] "\" '$HLA/HLA_DICTIONARY.sam' &gt;&gt; 'NA12878.HLA'.tmp";}' NA12878.HLA.calls.unique | sh
sort -k4 -n NA12878.HLA.tmp &gt;&gt; NA12878.HLA.calls.sam
/home/radon01/sjia/bin/SamToBam.csh NA12878.HLA.calls
rm NA12878.HLA.tmp
</pre>

<h3>Performance Considerations / Tradeoffs</h3>

<p>There exist a few performance / accuracy tradeoffs in the HLA caller, as in any algorithm. The following are a few key considerations that the user should keep in mind when using the software for HLA typing.</p>

<h4>Robustness to sequencing/alignment artifact vs. Ability to recognize rare alleles</h4>

<p>In polymorphic regions of the genome like the HLA, misaligned reads (presence of erroneous reads or lack of proper sequences) and sequencing errors (indels, systematic PCR errors) may cause the HLA caller to call rare alleles with polymorphisms at the affected bases. The user can manually spot these errors when the algorithm calls a rare allele (Frq1 and Frq2 columns in the output of HLACaller indicate log10 of the allele frequencies). Alternatively, the user can choose to consider only non-rare alleles (use the "-minFreq 0.001" option in HLACaller) to make the algorithm (faster and) more robust against sequencing or alignment errors. The drawback to this approach is that the algorithm may not be able to correctly identifying rare alleles when they are truly present. We recommend using the -minFreq option for genome-wide sequencing datasets, but not for high-quality (targeted PCR 454) data specifically captured for HLA typing in large cohorts.</p>

<h4>Misalignment Detection and Data Pre-Processing</h4>

<p>The FindClosestAllele walker (optional step) is recommended for two reasons:</p>

<ol><li><p>The ability to detect misalignments for reads that don't match very well to the closest appearing HLA allele - removing these misaligned reads improves calling accuracy.</p></li>
<li><p>Creating a list of closest-matching HLA alleles reduces the search space (over 3,000 HLA alleles across the class I and class II loci) that HLACaller has to iterate through, reducing computational burdon.</p></li>
</ol><p>However, using this pre-processing step is not without costs:</p>

<ol><li><p>Any cutoff chosen for %concordance, min base matches, or max base mismatches will not distinguish between correctly aligned and misaligned reads 100% of the time - there is a chance that correctly aligned reads may be removed, and misaligned reads not removed.</p></li>
<li><p>The list of closest-matching alleles in some cases may not contain the true allele if there is sufficient sequencing error, in which case the true allele will not be considered by the HLACaller walker. In our experience, the advantages of using this pre-processing FindClosestAllele walker greatly outweighs the disadvantages, as recommend including it in the pipeline long as the user understands the possible risks of using this function.</p></li>
</ol><h3>Contributions </h3>

<p>The HLA caller algorithm was was developed by Xiaoming (Sherman) Jia with generous support of the GATK team (especially Mark Depristo, Eric Banks), and Paul de Bakker.</p>

<ul><li>xiaomingjia at gmail dot com</li>
<li>depristo at broadinstitute dot org</li>
<li>ebanks at broadinstitute dot org</li>
<li>pdebakker at rics dot bwh dot harvard dot edu<br></li>
</ul>
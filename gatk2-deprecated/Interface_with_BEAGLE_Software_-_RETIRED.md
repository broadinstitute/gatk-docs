## Interface with BEAGLE Software - RETIRED

By delangel

<h3>Note: As of version 4, BEAGLE reads and outputs VCF files directly, and can handle multiallelic sites. We have not yet evaluated what this means for the GATK-BEAGLE interface but we believe it renders these tools unnecessary.</h3>

<hr></hr><h3>Introduction</h3>

<p><a rel="nofollow" href="http://www.stat.auckland.ac.nz/~bbrowning/beagle/beagle.html">BEAGLE</a> is a state of the art software package for analysis of large-scale genetic data sets with hundreds of thousands of markers genotyped on thousands of samples. BEAGLE can</p>

<ul><li>phase genotype data (i.e. infer haplotypes) for unrelated individuals, parent-offspring pairs, and parent-offspring trios.</li>
<li>infer sporadic missing genotype data.</li>
<li>impute ungenotyped markers that have been genotyped in a reference panel.</li>
<li>perform single marker and haplotypic association analysis.</li>
<li>detect genetic regions that are homozygous-by-descent in an individual or identical-by-descent in pairs of individuals.</li>
</ul><p>The GATK provides an experimental interface to BEAGLE. Currently, the only use cases supported by this interface are a) inferring missing genotype data from call sets (e.g. for lack of coverage in low-pass data), b) Genotype inference for unrelated individuals.</p>

<h3>Workflow</h3>

<p>The basic workflow for this interface is as follows:</p>

<p>After variants are called and possibly filtered, the GATK walker ProduceBeagleInput will take the resulting VCF as input, and will produce a likelihood file in BEAGLE format.</p>

<ul><li>User needs to run BEAGLE with this likelihood file specified as input.</li>
<li>After Beagle runs, user must unzip resulting output files (.gprobs, .phased) containing posterior genotype probabilities and phased haplotypes.</li>
<li>User can then run GATK walker BeagleOutputToVCF to produce a new VCF with updated data. The new VCF will contain updated genotypes as well as updated annotations.</li>
</ul><h3>Producing Beagle input likelihoods file</h3>

<p>Before running BEAGLE, we need to first take an input VCF file with genotype likelihoods and produce the BEAGLE likelihoods file using walker ProduceBealgeInput, as described in detail in its <a rel="nofollow" href="http://www.broadinstitute.org/gatk/gatkdocs/org_broadinstitute_sting_gatk_walkers_beagle_ProduceBeagleInput.html">documentation page</a>.</p>

<p>For each variant in inputvcf.vcf, ProduceBeagleInput will extract the genotype likelihoods, convert from log to linear space, and produce a BEAGLE input file in Genotype likelihoods file format (see <a rel="nofollow" href="http://www.stat.auckland.ac.nz/~bbrowning/beagle/beagle.html">BEAGLE documentation</a> for more details). Essentially, this file is a text file in tabular format, a snippet of which is pasted below:</p>

<pre class="code codeBlock" spellcheck="false">marker    alleleA alleleB NA07056 NA07056 NA07056 NA11892 NA11892 NA11892 
20:60251    T        C     10.00   1.26    0.00     9.77   2.45    0.00 
20:60321    G        T     10.00   5.01    0.01    10.00   0.31    0.00 
20:60467    G        C      9.55   2.40    0.00     9.55   1.20    0.00 
</pre>

<p>Note that BEAGLE only supports biallelic sites. Markers can have an arbitrary label, but they need to be in chromosomal order. Sites that are not genotyped in the input VCF (i.e. which are annotated with a "./." string and have no Genotype Likelihood annotation) are assigned a likelihood value of (0.33, 0.33, 0.33).</p>

<p><strong>IMPORTANT:</strong> Due to BEAGLE memory restrictions, it's strongly recommended that BEAGLE be run on a separate chromosome-by-chromosome basis. In the current use case, BEAGLE uses RAM in a manner approximately proportional to the number of input markers. After BEAGLE is run and an output VCF is produced as described below, CombineVariants can be used to combine resulting VCF's, using the "-variantMergeOptions UNION" argument.</p>

<h3>Running Beagle</h3>

<p>We currently only support a subset of BEAGLE functionality - only unphased, unrelated input likelihood data is supported. To run imputation analysis, run for example</p>

<pre class="code codeBlock" spellcheck="false">java -Xmx4000m -jar path_to_beagle/beagle.jar like=path_to_beagle_output/beagle_output out=myrun
</pre>

<p>Extra BEAGLE arguments can be added as required.</p>

<h4>About Beagle memory usage </h4>

<p>Empirically, Beagle can run up to about ~800,000 markers with 4 GB of RAM. Larger chromosomes require additional memory.</p>

<h3>Processing BEAGLE output files</h3>

<p>BEAGLE will produce several output files. The following shell commands unzip the output files in preparation for their being processed, and put them all in the same place:</p>

<pre class="code codeBlock" spellcheck="false"># unzip gzip'd files, force overwrite if existing
gunzip -f path_to_beagle_output/myrun.beagle_output.gprobs.gz
gunzip -f path_to_beagle_output/myrun.beagle_output.phased.gz
#rename also Beagle likelihood file to mantain consistency
mv path_to_beagle_output/beagle_output path_to_beagle_output/myrun.beagle_output.like 
</pre>

<h3>Creating a new VCF from BEAGLE data with BeagleOutputToVCF</h3>

<p>Once BEAGLE files are produced, we can update our original VCF with BEAGLE's data. This is done using the <a rel="nofollow" href="http://www.broadinstitute.org/gatk/gatkdocs/org_broadinstitute_sting_gatk_walkers_beagle_BeagleOutputToVCF.html">BeagleOutputToVCF</a> tool.</p>

<p>The walker looks for the files specified with the -B(type,BEAGLE,file) triplets as above for the output posterior genotype probabilities, the output r^2 values and the output phased genotypes. The order in which these are given in the command line is arbitrary, but all three must be present for correct operation.</p>

<p>The output VCF has the new genotypes that Beagle produced, and several annotations are also updated. By default, the walker will update the per-genotype annotations GQ (Genotype Quality), the genotypes themselves, as well as the per-site annotations AF (Allele Frequency), AC (Allele Count) and AN (Allele Number).</p>

<p>The resulting VCF can now be used for further downstream analysis.</p>

<h3>Merging VCFs broken up by chromosome into a single genome-wide file</h3>

<p>Assuming you have broken up your calls into Beagle by chromosome (as recommended above), you can use the CombineVariants tool to merge the resulting VCFs into a single callset.</p>

<pre class="code codeBlock" spellcheck="false">java -jar /path/to/dist/GenomeAnalysisTK.jar \
  -T CombineVariants \
  -R reffile.fasta \
  --out genome_wide_output.vcf \
  -V:input1 beagle_output_chr1.vcf \
  -V:input2 beagle_output_chr2.vcf \
  .
  .
  .
  -V:inputX beagle_output_chrX.vcf \
  -type UNION -priority input1,input2,...,inputX
</pre>

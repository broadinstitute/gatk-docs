## What do the VariantEval modules do?

By Geraldine_VdAuwera

<p>VariantEval accepts two types of modules: stratification and evaluation modules.</p>

<ul><li>Stratification modules will stratify (group) the variants based on certain properties.</li>
<li>Evaluation modules will compute certain metrics for the variants</li>
</ul><h3>CpG</h3>

<p>CpG is a three-state stratification:</p>

<ul><li>The locus is a CpG site ("CpG")</li>
<li>The locus is not a CpG site ("non_CpG")</li>
<li>The locus is either a CpG or not a CpG site ("all")</li>
</ul><p>A CpG site is defined as a site where the reference base at a locus is a C and the adjacent reference base in the 3' direction is a G.</p>

<h3>EvalRod</h3>

<p>EvalRod is an N-state stratification, where N is the number of eval rods bound to VariantEval.</p>

<h3>Sample</h3>

<p>Sample is an N-state stratification, where N is the number of samples in the eval files.</p>

<h3>Filter</h3>

<p>Filter is a three-state stratification:</p>

<ul><li>The locus passes QC filters ("called")</li>
<li>The locus fails QC filters ("filtered")</li>
<li>The locus either passes or fails QC filters ("raw")</li>
</ul><h3>FunctionalClass</h3>

<p>FunctionalClass is a four-state stratification:</p>

<ul><li>The locus is a synonymous site ("silent")</li>
<li>The locus is a missense site ("missense")</li>
<li>The locus is a nonsense site ("nonsense")</li>
<li>The locus is of any functional class ("any")</li>
</ul><h3>CompRod</h3>

<p>CompRod is an N-state stratification, where N is the number of comp tracks bound to VariantEval.</p>

<h3>Degeneracy</h3>

<p>Degeneracy is a six-state stratification:</p>

<ul><li>The underlying base position in the codon is 1-fold degenerate ("1-fold")</li>
<li>The underlying base position in the codon is 2-fold degenerate ("2-fold")</li>
<li>The underlying base position in the codon is 3-fold degenerate ("3-fold")</li>
<li>The underlying base position in the codon is 4-fold degenerate ("4-fold")</li>
<li>The underlying base position in the codon is 6-fold degenerate ("6-fold")</li>
<li>The underlying base position in the codon is degenerate at any level ("all")</li>
</ul><p>See the [<a href="http://en.wikipedia.org/wiki/Genetic_code#Degeneracy" rel="nofollow">http://en.wikipedia.org/wiki/Genetic_code#Degeneracy</a> Wikipedia page on degeneracy] for more information.</p>

<h3>JexlExpression</h3>

<p>JexlExpression is an N-state stratification, where N is the number of JEXL expressions supplied to VariantEval.  See [[Using JEXL expressions]]</p>

<h3>Novelty</h3>

<p>Novelty is a three-state stratification:</p>

<ul><li>The locus overlaps the knowns comp track (usually the dbSNP track) ("known")</li>
<li>The locus does not overlap the knowns comp track ("novel")</li>
<li>The locus either overlaps or does not overlap the knowns comp track ("all")</li>
</ul><h3>CountVariants</h3>

<p>CountVariants is an evaluation module that computes the following metrics:</p>

<table><thead><tr><th align="left">Metric</th>
  <th align="left">Definition</th>
</tr></thead><tbody><tr><td align="left">nProcessedLoci</td>
  <td align="left">Number of processed loci</td>
</tr><tr><td align="left">nCalledLoci</td>
  <td align="left">Number of called loci</td>
</tr><tr><td align="left">nRefLoci</td>
  <td align="left">Number of reference loci</td>
</tr><tr><td align="left">nVariantLoci</td>
  <td align="left">Number of variant loci</td>
</tr><tr><td align="left">variantRate</td>
  <td align="left">Variants per loci rate</td>
</tr><tr><td align="left">variantRatePerBp</td>
  <td align="left">Number of variants per base</td>
</tr><tr><td align="left">nSNPs</td>
  <td align="left">Number of snp loci</td>
</tr><tr><td align="left">nInsertions</td>
  <td align="left">Number of insertion</td>
</tr><tr><td align="left">nDeletions</td>
  <td align="left">Number of deletions</td>
</tr><tr><td align="left">nComplex</td>
  <td align="left">Number of complex loci</td>
</tr><tr><td align="left">nNoCalls</td>
  <td align="left">Number of no calls loci</td>
</tr><tr><td align="left">nHets</td>
  <td align="left">Number of het loci</td>
</tr><tr><td align="left">nHomRef</td>
  <td align="left">Number of hom ref loci</td>
</tr><tr><td align="left">nHomVar</td>
  <td align="left">Number of hom var loci</td>
</tr><tr><td align="left">nSingletons</td>
  <td align="left">Number of singletons</td>
</tr><tr><td align="left">heterozygosity</td>
  <td align="left">heterozygosity per locus rate</td>
</tr><tr><td align="left">heterozygosityPerBp</td>
  <td align="left">heterozygosity per base pair</td>
</tr><tr><td align="left">hetHomRatio</td>
  <td align="left">heterozygosity to homozygosity ratio</td>
</tr><tr><td align="left">indelRate</td>
  <td align="left">indel rate (insertion count + deletion count)</td>
</tr><tr><td align="left">indelRatePerBp</td>
  <td align="left">indel rate per base pair</td>
</tr><tr><td align="left">deletionInsertionRatio</td>
  <td align="left">deletion to insertion ratio</td>
</tr></tbody></table><h3>CompOverlap</h3>

<p>CompOverlap is an evaluation module that computes the following metrics:</p>

<table><thead><tr><th align="left">Metric</th>
  <th align="left">Definition</th>
</tr></thead><tbody><tr><td align="left">nEvalSNPs</td>
  <td align="left">number of eval SNP sites</td>
</tr><tr><td align="left">nCompSNPs</td>
  <td align="left">number of comp SNP sites</td>
</tr><tr><td align="left">novelSites</td>
  <td align="left">number of eval sites outside of comp sites</td>
</tr><tr><td align="left">nVariantsAtComp</td>
  <td align="left">number of eval sites at comp sites (that is, sharing the same locus as a variant in the comp track, regardless of whether the alternate allele is the same)</td>
</tr><tr><td align="left">compRate</td>
  <td align="left">percentage of eval sites at comp sites</td>
</tr><tr><td align="left">nConcordant</td>
  <td align="left">number of concordant sites (that is, for the sites that share the same locus as a variant in the comp track, those that have the same alternate allele)</td>
</tr><tr><td align="left">concordantRate</td>
  <td align="left">the concordance rate</td>
</tr></tbody></table><h4>Understanding the output of CompOverlap</h4>

<p>A SNP in the detection set is said to be 'concordant' if the position exactly matches an entry in dbSNP and the allele is the same.  To understand this and other output of CompOverlap, we shall examine a detailed example.  First, consider a fake dbSNP file (headers are suppressed so that one can see the important things):</p>

<pre class="code codeBlock" spellcheck="false"> $ grep -v '##' dbsnp.vcf
 #CHROM  POS     ID      REF     ALT     QUAL    FILTER  INFO
 1       10327   rs112750067     T       C       .       .       ASP;R5;VC=SNP;VP=050000020005000000000100;WGT=1;dbSNPBuildID=132
</pre>

<p>Now, a detection set file with a single sample, where the variant allele is the same as listed in dbSNP:</p>

<pre class="code codeBlock" spellcheck="false"> $ grep -v '##' eval_correct_allele.vcf
 #CHROM  POS     ID      REF     ALT     QUAL    FILTER  INFO    FORMAT            001-6
 1       10327   .       T       C       5168.52 PASS    ...     GT:AD:DP:GQ:PL    0/1:357,238:373:99:3959,0,4059
</pre>

<p>Finally, a detection set file with a single sample, but the alternate allele differs from that in dbSNP:</p>

<pre class="code codeBlock" spellcheck="false"> $ grep -v '##' eval_incorrect_allele.vcf
 #CHROM  POS     ID      REF     ALT     QUAL    FILTER  INFO    FORMAT            001-6
 1       10327   .       T       A       5168.52 PASS    ...     GT:AD:DP:GQ:PL    0/1:357,238:373:99:3959,0,4059
</pre>

<p>Running VariantEval with just the CompOverlap module:</p>

<pre class="code codeBlock" spellcheck="false"> $ java -jar $STING_DIR/dist/GenomeAnalysisTK.jar -T VariantEval \
        -R /seq/references/Homo_sapiens_assembly19/v1/Homo_sapiens_assembly19.fasta \
        -L 1:10327 \
        -B:dbsnp,VCF dbsnp.vcf \
        -B:eval_correct_allele,VCF eval_correct_allele.vcf \
        -B:eval_incorrect_allele,VCF eval_incorrect_allele.vcf \
        -noEV \
        -EV CompOverlap \
        -o eval.table
</pre>

<p>We find that the eval.table file contains the following:</p>

<pre class="code codeBlock" spellcheck="false"> $ grep -v '##' eval.table | column -t 
 CompOverlap  CompRod  EvalRod                JexlExpression  Novelty  nEvalVariants  nCompVariants  novelSites  nVariantsAtComp  compRate      nConcordant  concordantRate
 CompOverlap  dbsnp    eval_correct_allele    none            all      1              1              0           1                100.00000000  1            100.00000000
 CompOverlap  dbsnp    eval_correct_allele    none            known    1              1              0           1                100.00000000  1            100.00000000
 CompOverlap  dbsnp    eval_correct_allele    none            novel    0              0              0           0                0.00000000    0            0.00000000
 CompOverlap  dbsnp    eval_incorrect_allele  none            all      1              1              0           1                100.00000000  0            0.00000000
 CompOverlap  dbsnp    eval_incorrect_allele  none            known    1              1              0           1                100.00000000  0            0.00000000
 CompOverlap  dbsnp    eval_incorrect_allele  none            novel    0              0              0           0                0.00000000    0            0.00000000
</pre>

<p>As you can see, the detection set variant was listed under nVariantsAtComp (meaning the variant was seen at a position listed in dbSNP), but only the eval_correct_allele dataset is shown to be concordant at that site, because the allele listed in this dataset and dbSNP match.</p>

<h3>TiTvVariantEvaluator</h3>

<p>TiTvVariantEvaluator is an evaluation module that computes the following metrics:</p>

<table><thead><tr><th align="left">Metric</th>
  <th align="left">Definition</th>
</tr></thead><tbody><tr><td align="left">nTi</td>
  <td align="left">number of transition loci</td>
</tr><tr><td align="left">nTv</td>
  <td align="left">number of transversion loci</td>
</tr><tr><td align="left">tiTvRatio</td>
  <td align="left">the transition to transversion ratio</td>
</tr><tr><td align="left">nTiInComp</td>
  <td align="left">number of comp transition sites</td>
</tr><tr><td align="left">nTvInComp</td>
  <td align="left">number of comp transversion sites</td>
</tr><tr><td align="left">TiTvRatioStandard</td>
  <td align="left">the transition to transversion ratio for comp sites</td>
</tr></tbody></table>
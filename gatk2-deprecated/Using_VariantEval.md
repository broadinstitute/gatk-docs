## Using VariantEval

By delangel

<p><strong>This article has been deprecated in favor of a new method article which you can view <a rel="nofollow" href="http://gatkforums.broadinstitute.org/discussion/6308/evaluating-the-quality-of-a-variant-callset">here</a>.</strong></p>

<hr></hr><p>For a complete, detailed argument reference, refer to the <a rel="nofollow" href="http://www.broadinstitute.org/gatk/gatkdocs/org_broadinstitute_sting_gatk_walkers_varianteval_VariantEval.html">technical documentation page</a>.</p>

<h2>Modules</h2>

<p>You can find detailed information about the various modules <a rel="nofollow" href="http://www.broadinstitute.org/gatk/guide/article?id=2361">here</a>.</p>

<h3>Stratification modules</h3>

<ul><li>AlleleFrequency</li>
<li>AlleleCount</li>
<li>CompRod</li>
<li>Contig</li>
<li>CpG</li>
<li>Degeneracy</li>
<li>EvalRod</li>
<li>Filter</li>
<li>FunctionalClass</li>
<li>JexlExpression</li>
<li>Novelty</li>
<li>Sample</li>
</ul><h3>Evaluation modules</h3>

<ul><li>CompOverlap</li>
<li>CountVariants</li>
</ul><p>Note that the GenotypeConcordance module has been rewritten as a separate walker tool (see its <a rel="nofollow" href="http://www.broadinstitute.org/gatk/gatkdocs/org_broadinstitute_sting_gatk_walkers_variantutils_GenotypeConcordance.html">Technical Documentation</a> page).</p>

<h2>A useful analysis using VariantEval</h2>

<p><img src="http://www.broadinstitute.org/gatk/media/pics/ExampleDiagram.png" alt="image" class="embedImage-img importedEmbed-img"></img></p>

<p>We in GSA often find ourselves performing an analysis of 2 different call sets.  For SNPs, we often show the overlap of the sets (their "venn") and the relative dbSNP rates and/or transition-transversion ratios.  The picture provided is an example of such a slide and is easy to create using VariantEval.  Assuming you have 2 filtered VCF callsets named 'foo.vcf' and 'bar.vcf', there are 2 quick steps.</p>

<h4>Combine the VCFs</h4>

<pre class="code codeBlock" spellcheck="false">java -jar GenomeAnalysisTK.jar \
    -R ref.fasta \
    -T CombineVariants \
    -V:FOO foo.vcf \
    -V:BAR bar.vcf \
    -priority FOO,BAR \
    -o merged.vcf
</pre>

<h4>Run VariantEval</h4>

<pre class="code codeBlock" spellcheck="false">java -jar GenomeAnalysisTK.jar \
     -T VariantEval \
     -R ref.fasta \
     -D dbsnp.vcf \
     -select 'set=="Intersection"' -selectName Intersection \
     -select 'set=="FOO"' -selectName FOO \
     -select 'set=="FOO-filterInBAR"' -selectName InFOO-FilteredInBAR \
     -select 'set=="BAR"' -selectName BAR \
     -select 'set=="filterInFOO-BAR"' -selectName InBAR-FilteredInFOO \
     -select 'set=="FilteredInAll"' -selectName FilteredInAll \
     -o merged.eval.gatkreport \
     -eval merged.vcf \
     -l INFO
</pre>

<h3>Checking the possible values of 'set'</h3>

<p>It is wise to check the actual values for the set names present in your file before writing complex VariantEval commands.  An easy way to do this is to extract the value of the set fields and then reduce that to the unique entries, like so:</p>

<pre class="code codeBlock" spellcheck="false">java -jar GenomeAnalysisTK.jar -T VariantsToTable -R ref.fasta -V merged.vcf -F set -o fields.txt
grep -v 'set' fields.txt | sort | uniq -c
</pre>

<p>This will provide you with a list of all of the possible values for 'set' in your VCF so that you can be sure to supply the correct select statements to VariantEval.</p>

<h2>Reading the VariantEval output file</h2>

<p>The VariantEval output is formatted as a <a rel="nofollow" href="http://www.broadinstitute.org/gatk/guide/article?id=1244">GATKReport</a>.</p>

<h3>Understanding Genotype Concordance values from Variant Eval</h3>

<p>The VariantEval genotype concordance module emits information the relationship between the eval calls and genotypes and the comp calls and genotypes.  The following three slides provide some insight into three key metrics to assess call sensitivity and concordance between genotypes.</p>

<pre class="code codeBlock" spellcheck="false">##:GATKReport.v0.1 GenotypeConcordance.sampleSummaryStats&amp;#160;: the concordance statistics summary for each sample
GenotypeConcordance.sampleSummaryStats  CompRod   CpG      EvalRod  JexlExpression  Novelty  percent_comp_ref_called_var  percent_comp_het_called_het  percent_comp_het_called_var  percent_comp_hom_called_hom  percent_comp_hom_called_var  percent_non-reference_sensitivity  percent_overall_genotype_concordance  percent_non-reference_discrepancy_rate
GenotypeConcordance.sampleSummaryStats  compOMNI  all      eval     none            all      0.78                         97.65                        98.39                        99.13                        99.44                        98.80                              99.09                                 3.60
</pre>

<p>The key outputs:</p>

<ul><li><code class="code codeInline" spellcheck="false">percent_overall_genotype_concordance</code></li>
<li><code class="code codeInline" spellcheck="false">percent_non_ref_sensitivity_rate</code></li>
<li><code class="code codeInline" spellcheck="false">percent_non_ref_discrepancy_rate</code></li>
</ul><p>All defined below.</p>

<p><img src="http://www.broadinstitute.org/gatk/media/pics/GenotypeConcordanceVarSens.png" alt="image" class="embedImage-img importedEmbed-img"></img></p>

<p><img src="http://www.broadinstitute.org/gatk/media/pics/GenotypeConcordanceGenotypeErrorRate.png" alt="image" class="embedImage-img importedEmbed-img"></img></p>

<p><img src="http://www.broadinstitute.org/gatk/media/pics/GenotypeConcordanceTraditionalGenotypeConcordance.png" alt="image" class="embedImage-img importedEmbed-img"></img></p>

<p><img src="http://www.broadinstitute.org/gatk/media/pics/GenotypeExample.png" alt="image" class="embedImage-img importedEmbed-img"></img></p>

## Allele-specific annotation and filtering

By Geraldine_VdAuwera

<h2>Introduction and FAQs</h2>

<p>The current recalibration paradigm evaluates each position, and passes or filters all alleles at that position, regardless of how many alternate alleles occur there. This has major disadvantages in cases where a real variant allele occurs at the same position as an error that has sufficient evidence to be called as a variant. The goal of the Allele-Specific Filtering Workflow is to treat each allele separately in the annotation, recalibration and filtering phases.</p>

<h3>What studies can benefit from the Allele-Specific Filtering Workflow?</h3>

<p>Multi-allelic sites benefit the most from the Allele-Specific Filtering Workflow because each allele will be evaluated more accurately than if its data was lumped together with other alleles. Large callsets will benefit more than small callsets because multi-allelics will occur more frequently as the number of samples in a cohort increases.  One callset with 42 samples that was used for development contains 3% multi-allelic sites, while the ExAC callset [<a href="http://biorxiv.org/content/early/2015/10/30/030338]" rel="nofollow">http://biorxiv.org/content/early/2015/10/30/030338]</a> with approximately 60,000 samples contains nearly 8% multi-allelic sites. Recalibrating each allele separately will also greatly benefit rare disease studies, in which rare alleles may not be shared by other members of the callset, but could still occur at the same positions as common alleles or errors.</p>

<h3>What additional data do I need to run the Allele-Specific Filtering Workflow?</h3>

<p>No additional resource files are necessary, but this workflow does require the sample bam files. Annotations cannot be calculated from VCF or gVCF files alone.</p>

<h3>Is the Allele-Specific Filtering Workflow going to change my data? Can I still use my old analysis pipeline?</h3>

<p>After running the Allele-Specific Filtering Workflow, several new annotations will be added to the INFO field for your variants (see below), and VQSR results will be based on those new annotations, though using SNP and INDEL tranche sensitivity cutoffs equivalent to the non-allele-specific best practices. If after analyzing your recalibrated data, you’re not convinced that this workflow is for you, you can still run the classic VQSR on your genotyped VCF because the standard annotations for VQSR are still included in the genotyped VCF.</p>

<h3>Can I run the Allele-Specific Filtering Workflow not in reference confidence mode?</h3>

<p>Nope.  Sorry.  The way we generate and combine the allele-specific data depends on having raw data for each sample in the gVCF.</p>

<h3>Is the Allele-Specific Filtering Workflow part of the GATK Best Practices?</h3>

<p>Not yet. Although we are happy with the performance of this workflow, our own production pipelines have not yet been updated to include this, so it should still be considered experimental. However, we do encourage you to try this out on your own data and let us know what you find, as this helps us refine the tools and catch bugs.</p>

<hr></hr><h2>Allele-Specific Workflow</h2>

<h3>Input</h3>

<p>Begin with a post-BQSR bam file for each sample.  The read data in the bam are necessary to generate the allele-specific annotations.</p>

<h3>Step 1: HaplotypeCaller</h3>

<p>Using the locally-realigned reads, HaplotypeCaller will generate gVCFs with all of its usual standard annotations, plus raw data to calculate allele-specific versions of the standard annotations. That means each alternate allele in each VariantContext will get its own data used by downstream tools to generate allele-specific QualByDepth, RMSMappingQuality, FisherStrand and allele-specific versions of the other standard annotations. For example, this will help us sort out good alleles that only occur in a few samples and have a good balance of forward and reverse reads but occur at the same position as another allele that has bad strand bias because it’s probably a mapping error.</p>

<pre class="code codeBlock" spellcheck="false">java -jar $GATKjar -T HaplotypeCaller -R $reference \
    -I mySample.bam \
    -o mySample.AS.g.vcf \
    -ERC GVCF \
    -G StandardAnnotation -G AS_StandardAnnotation -G StandardHCAnnotation
</pre>

<h3>(Optional) Step 2a: CombineGVCFs</h3>

<p>Here the allele-specific data for each sample is combined per allele, but is not yet in its final annotation form. We only do this for computational efficiency reasons when we have &gt;200 samples.</p>

<pre class="code codeBlock" spellcheck="false">java -jar $GATKjar -T CombineGVCFs -R $reference \
    -V mySample1.AS.g.vcf -V mySample2.AS.g.vcf -V mySample3.AS.g.vcf \
    -o allSamples.g.AS.vcf \
    -G StandardAnnotation -G AS_StandardAnnotation
</pre>

<p><em>Note that if you run this, you need to modify the <code class="code codeInline" spellcheck="false">-V</code> input in the next step to just the combined gVCF file.</em></p>

<h3>Step 2: GenotypeGVCFs</h3>

<p>Raw allele-specific data for each sample is used to calculate the finalized annotation values. In GATK 3.6, non-allele-specific rank sum annotations are still combined using the median across all samples. (See below for details on more accurate MQ calculations.)</p>

<pre class="code codeBlock" spellcheck="false">java -jar $GATKjar -T GenotypeGVCFs -R $reference \
    -V mySample1.AS.g.vcf -V mySample2.AS.g.vcf -V mySample3.AS.g.vcf \
    -o allSamples.AS.vcf \
    -G StandardAnnotation -G AS_StandardAnnotation
</pre>

<h3>Step 3: VariantRecalibrator</h3>

<p>In allele-specific mode, the VariantRecalibrator builds the statistical model based on data for each allele, rather than each site.  This has the added benefit of being able to recalibrate the SNPs in mixed sites according to the appropriate model, rather than lumping them in with INDELs as had been done previously. It will also provide better results by matching the exact allele in the training and truth data rather than just the position.</p>

<pre class="code codeBlock" spellcheck="false"># SNP modeling pass

java -jar $GATKjar -T VariantRecalibrator -R $reference -mode SNP -AS \
    -an AS_QD -an AS_FS -an AS_ReadPosRankSum -an AS_MQ -an AS_MQRankSum -an AS_SOR \
    -input allSamples.AS.vcf \
    -resource:known=false,training=true,truth=true,prior=15.0 $hapmap_sites \
    -resource:known=false,training=true,truth=true,prior=12.0 $omni_sites \
    -resource:known=false,training=true,truth=false,prior=10.0 $training_1000G_sites \
    -resource:known=true,training=false,truth=false,prior=2.0 $dbSNP_129 \
    -tranche 100.0 -tranche 99.9 -tranche 99.8 -tranche 99.7 -tranche 99.5 -tranche 99.3 -tranche 99.0 -tranche 90.0 \
    -recalFile allSamples.AS.snps.recal \
    -tranchesFile allSamples.AS.snps.tranches \
    -modelFile allSamples.AS.snps.report \
    -rscriptFile allSamples.AS.snps.R

# INDEL modeling pass

java -jar $GATKjar -T VariantRecalibrator -R $reference -mode INDEL -AS \
    -an AS_QD -an AS_FS -an AS_ReadPosRankSum -an AS_MQRankSum -an AS_SOR \
    -input allSamples.AS.vcf \
    -resource:known=false,training=true,truth=true,prior=12.0 $indelGoldStandardCallset \
    -resource:known=true,training=false,truth=false,prior=2.0 $dbSNP_129 \
    -tranche 100.0 -tranche 99.9 -tranche 99.8 -tranche 99.7 -tranche 99.5 -tranche 99.3 -tranche 99.0 -tranche 90.0 \
    --maxGaussians 4 \
    -recalFile allSamples.AS.indels.recal \
    -tranchesFile allSamples.AS.indels.tranches \
    -modelFile allSamples.AS.indels.report \
    -rscriptFile allSamples.AS.indels.R

</pre>

<p><em>Note that these commands are for exomes.  For whole genomes, the classic <code class="code codeInline" spellcheck="false">-DP</code> annotation will still be used for SNP recalibration, as in the Best Practices.</em></p>

<h3>Step 4: ApplyRecalibration</h3>

<p>Allele-specific filters are calculated and stored in the AS_FilterStatus INFO annotation. A site-level filter is applied to each site based on the most lenient filter across all alleles. For example, if any allele passes, the entire site passes.  If no alleles pass, then the filter will be applied corresponding to the allele with the lowest tranche (best VQSLOD).</p>

<p>The two ApplyRecalibration modes should be run in series, as in our Best Practices recommendations. If SNP and INDEL ApplyRecalibration modes are run in parallel and combined with CombineVariants (which will work for the standard VQSR arguments), in allele-specific mode any mixed sites will fail to be processed correctly.</p>

<pre class="code codeBlock" spellcheck="false"># SNP filtering pass

java -jar $GATKjar -T ApplyRecalibration -R $reference \
    -input allSamples.AS.vcf \
    -mode SNP --ts_filter_level 99.70 -AS \
    --recal_file allSamples.AS.snps.recal \
    --tranches_file allSamples.AS.snps.tranches \
    -o allSamples.AS.snp_recalibrated.vcf

# INDEL filtering pass

java -jar $GATKjar -T ApplyRecalibration -R $reference \
    -input allSamples.AS.snp_recalibrated.vcf \
    -mode INDEL --ts_filter_level 99.3 -AS \
    --recal_file allSamples.AS.indels.recal \
    --tranches_file allSamples.AS.indels.tranches \
    -o allSamples.AS.snp_indel_recalibrated.vcf

</pre>

<hr></hr><h2>Output of the workflow</h2>

<p>The Allele-Specific Filtration Workflow adds new allele-specific info-level annotations to the VCFs and produces a final output with allele-specific filters based on the VQSR SNP and INDEL tranches.</p>

<h3>Allele-specific annotations</h3>

<p>The AS_Standard annotation set will produce allele-specific versions of our standard annotations.  For AS_MQ, this means that the root-mean-squared mapping quality will be given for all of the reads that support each allele, respectively.  For rank sum and strand bias tests, the annotation for each allele will compare that alternative allele’s values against the reference allele.</p>

<h3>Recalibration files from allele-specific VariantRecalibrator</h3>

<p>Each allele will be described in a separate line in the output recalibration (.recal) files. For the advanced analyst, this is a good way to check which allele has the worst data and is responsible for a NEGATIVE_TRAIN_SITE classification.</p>

<h3>Allele-specific filters</h3>

<p>After both ApplyRecalibration modes are run, the INFO field will contain an annotation called AS_FilterStatus, which will list the filter corresponding to each alternate allele.  Allele-specific culprit and VQSLOD scores will also be added to the final VCF in the AS_culprit and AS_VQSLOD annotations, respectively.</p>

<h3>Sample output</h3>

<p>3       195507036       .       C       G,CCT 6672.42 VQSRTrancheINDEL99.80to99.90    AC=7,2;AF=0.106,0.030;AN=66;AS_BaseQRankSum=-0.144,1.554;AS_FS=127.421,52.461;AS_FilterStatus=VQSRTrancheSNP99.90to100.00,VQSRTrancheINDEL99.80to99.90;AS_MQ=29.70,28.99;AS_MQRankSum=1.094,0.045;AS_ReadPosRankSum=1.120,-7.743;AS_SOR=9.981,7.523;AS_VQSLOD=-48.3935,-7.8306;AS_culprit=AS_FS,AS_FS;BaseQRankSum=0.028;DP=2137;ExcessHet=1.6952;FS=145.982;GQ_MEAN=200.21;GQ_STDDEV=247.32;InbreedingCoeff=0.0744;MLEAC=7,2;MLEAF=0.106,0.030;MQ=29.93;MQRankSum=0.860;NCC=9;NEGATIVE_TRAIN_SITE;QD=10.94;ReadPosRankSum=-7.820e-01;SOR=10.484</p>

<p>3       153842181       .       CT      TT,CTTTT,CTTTTTTTTTT,C  4392.82 PASS    AC=15,1,1,1;AF=0.192,0.013,0.013,0.013;AN=78;AS_BaseQRankSum=-11.667,-3.884,-2.223,0.972;AS_FS=204.035,22.282,16.930,2.406;AS_FilterStatus=VQSRTrancheSNP99.90to100.00,VQSRTrancheINDEL99.50to99.70,VQSRTrancheINDEL99.70to99.80,PASS;AS_MQ=58.44,59.93,54.79,59.72;AS_MQRankSum=2.753,0.123,0.157,0.744;AS_ReadPosRankSum=-9.318,-5.429,-5.578,1.336;AS_SOR=6.924,3.473,5.131,1.399;AS_VQSLOD=-79.9547,-2.0208,-3.4051,0.7975;AS_culprit=AS_FS,AS_ReadPosRankSum,AS_ReadPosRankSum,QD;BaseQRankSum=-2.828e+00;DP=1725;ExcessHet=26.1737;FS=168.440;GQ_MEAN=117.51;GQ_STDDEV=141.53;InbreedingCoeff=-0.1776;MLEAC=16,1,1,1;MLEAF=0.205,0.013,0.013,0.013;MQ=54.35;MQRankSum=0.967;NCC=3;NEGATIVE_TRAIN_SITE;QD=4.42;ReadPosRankSum=-2.515e+00;SOR=4.740</p>

<hr></hr><h2>Caveats</h2>

<h3>Spanning deletions</h3>

<p>Since GATK3.4, GenotypeGVCFs has had the ability to output a “spanning deletion allele” (now represented with *) to indicate that a position in the VCF is contained within an upstream deletion and may have “missing data” in samples that contain that deletion.  While the upstream deletions will continue to be recalibrated and filtered by VQSR similar to the way they always have been, these spanning deletion alleles that occur downstream (and represent the same event) will be skipped.</p>

<h3>gVCF size increase</h3>

<p>Using the default gVCF bands ([1:60,70,80,90,99]), the raw allele-specific data makes a minimal size increase, which was less than a 1% increase on the NA12878 exome used for development.</p>

<h3>MQ calculation change</h3>

<p>If you ran the same callset through GATK 3.4 or earlier and GATK 3.5 or later, you may notice that the MQ annotation values for your variants changed slightly. That’s because with or without allele-specific annotation and filtering, MQ is being calculated in a new more accurate way. The GenotypeGVCFs used to combine each sample’s annotations by taking the median, but new MQ annotation calculation code now combine’s each sample’s data in a more mathematically correct way.</p>

<h3>Potential usage errors</h3>

<h4>Problem: WARN  08:35:26,273 ReferenceConfidenceVariantContextMerger - WARNING: remaining (non-reducible) annotations are assumed to be ints or doubles or booleans, but 0.00|15005.00|14400.00|0.00 doesn't parse and will not be annotated in the final VC.</h4>

<p>Solution: Remember to add <code class="code codeInline" spellcheck="false">-G Standard -G AS_Standard</code> to the GenotypeGVCFs command</p>

<h4>Problem: Standard (non-allele-specific) annotations are missing</h4>

<p>Solution: HaplotypeCaller and GenotypeGVCFs need -G Standard specified if -G AS_Standard is also specified.</p>

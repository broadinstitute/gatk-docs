## (How to) Call somatic SNVs and indels using MuTect2

By shlee

<h4>This tutorial covers the GATK3 version of the MuTect2 tool which is in a <code class="code codeInline" spellcheck="false">BETA</code>state and will not be developed further. A newer and much-improved version of this tool is included in GATK4; we recommend you use that tool rather than this one. Accordingly, this document is provided as is and with no guarantees that everything will work correctly.</h4>

<hr></hr><p><a rel="nofollow" href="https://us.v-cdn.net/5019796/uploads/editor/kg/gc2dug7nqng3.png"><img src="https://us.v-cdn.net/5019796/uploads/editor/kg/gc2dug7nqng3.png" width="450" alt="image" style="float: right;" class="embedImage-img importedEmbed-img"></img></a> In this hands-on tutorial, we will call somatic mutations, both single nucleotide and indels, using GATK v3.7 MuTect2. The calling is done for a tumor sample against a matched normal sample, both of which are aligned to GRCh38, and uses a panel of normals to filter additional artifactual calls. We then subset the calls to indels to focus our manual review on a new feature of MuTect2--<em>MuTect2 calls indels whereas MuTect1 does not</em>.</p>

<blockquote class="UserQuote blockquote"><div class="blockquote-content">
  <p class="blockquote-line">MuTect1 is a somatic pileup caller that requires joint indel realignment of the tumor and normal sample. Because MuTect2 uses reassembly and pairHMM in calling, it is possible to skip indel realignment during preprocessing.</p>
</div></blockquote>

<p>The tutorial gives a broad overview of the steps and touches lightly on some considerations to provide a feeling of what somatic variant calling entails. Because MuTect2 was under active development at the time of this writing, results from this tutorial pertain only to a specific version of MuTect2. <strong>Sections 1</strong>, <strong>4</strong>, <strong>5</strong> and <strong>6</strong> are essential to the MuTect2 workflow and includes instructions on how to create a panel of normals (PoN). <strong>Sections 2</strong> and <strong>3</strong> are optional and collect metrics that measure cross-sample contamination and sequence-context related sequencing artifacts, respectively.</p>

<p>Originally, <a rel="nofollow" href="https://gatkforums.broadinstitute.org/gatk/profile">@shlee</a> developed the tutorial and its data in January of 2017 for presentation at the 2017 Belgium workshop. The workshop folder is labeled <a rel="nofollow" href="https://drive.google.com/open?id=1y7q0gJ-ohNDhKG85UTRTwW1Jkq4HJ5M3">1702 in the GATK Workshops folder</a>. The data preprocessing deviated from standard practices to match and enable the use of publically available germline data for the panel of normals.</p>

<p>► For differences between GATK3 MuTect2 and GATK4 Mutect2, see <a rel="nofollow" href="https://software.broadinstitute.org/gatk/blog?id=10911">Blog#10911</a>.<br>
► For a qualitatively different and pipelined workflow using both MuTect1 and MuTect2, see the FireCloud Mutation Calling workflow described in <a rel="nofollow" href="https://gatkforums.broadinstitute.org/firecloud/discussion/7512">FireCloud Article#7512</a>. The FireCloud tutorial uses the same sample data as this tutorial, HCC1143, but aligned to GRCh37. It takes the SNV calls from MuTect1 and the indel calls from MuTect2 for a composite somatic callset.</p>

<p><a name="top" id="top"></a></p>

<hr></hr><h3>Jump to a section</h3>

<ol><li><a rel="nofollow" href="#1">Create a sites-only panel of normals (PoN) with MuTect2 and CombineVariants</a></li>
<li>(Optional) <a rel="nofollow" href="#2">Estimate cross-sample contamination with ContEst</a><br>
    ☞ <a rel="nofollow" href="#2.1"><em>How does MuTect use the contamination estimate?</em></a></li>
<li>(Optional) <a rel="nofollow" href="#3">Estimate extent of FFPE and OxoG artifacts with CollectSequencingArtifactMetrics </a><br>
    ☞ <a rel="nofollow" href="#3.1"><em>How do I interpret the TOTAL_QSCORE?</em></a><br>
    ☞ <a rel="nofollow" href="#3.2"><em>What is FFPE? What is OxoG?</em></a></li>
<li><a rel="nofollow" href="#4">Call somatic SNVs and indels  with MuTect2</a></li>
<li><a rel="nofollow" href="#5">Subset PASS calls plus suspect filtered calls with SelectVariants</a></li>
<li><a rel="nofollow" href="#6">Generate BAMOUTs  with MuTect2 and manually review alignments with IGV</a></li>
<li><a rel="nofollow" href="#7">Debrief and related resources</a></li>
</ol><h3>Tools involved</h3>

<ul><li>The tutorial uses MuTect2 v3.7, which is in beta. What this means is tool parameters and recommended workflows are currently under active development. As of this writing, going forward, GATK v4 will reflect updates to MuTect2.</li>
<li><a rel="nofollow" href="https://broadinstitute.github.io/picard/">Picard v2.8.1</a></li>
<li><a rel="nofollow" href="http://software.broadinstitute.org/software/igv/">IGV v2.3</a></li>
<li>(Optional) Samtools v1.3.1</li>
</ul><h3>Download example data</h3>

<p>Download tutorial_9183.tar.gz, either from the the <a rel="nofollow" href="denied:ftp://gsapubftp-anonymous@ftp.broadinstitute.org/tutorials/datasets">ftp site</a> or from the <a rel="nofollow" href="https://drive.google.com/open?id=1QdtVEronIzs04L37BFkw29TLjNWcyOpf">GoogleDrive</a>. To access the ftp site, leave the password field blank. If the GoogleDrive link is broken, please let us know. The tutorial also requires the GRCh38 reference FASTA, dictionary, and index. These are available from the <a rel="nofollow" href="https://software.broadinstitute.org/gatk/download/bundle">GATK Resource Bundle</a>.</p>

<p>The data tarball contains 23 files plus a folder of 24 precomputed files. Within the file names, 1kg refers to the <em>1000 Genomes Project</em>, pon to <em>panel of normals</em>, T to <em>tumor</em> and N to the tumor matched <em>normal</em>. HCC1143 refers to a <em>Harvard Cancer Center</em> patient.</p>

<p>Example data are based on a breast cancer cell line and its matched normal cell line derived from blood. Both cell lines are consented and known as HCC1143 and HCC1143_BL, respectively. The Broad Cancer Genome Analysis (CGA) group has graciously provided 2x76 paired-end whole exome sequence data from the two cell lines (C835.HCC1143_2 and C835.HCC1143_BL.4), and we have reverted and aligned these to GRCh38 using alt-aware alignment and post-alt processing as described in <a rel="nofollow" href="https://software.broadinstitute.org/gatk/documentation/article?id=8017">Tutorial#8017</a>. During preprocessing, we omitted the MergeBamAlignment step and included indel realignment, to match the toolchain of the PoN normals.</p>

<blockquote class="UserQuote blockquote"><div class="blockquote-content">
  <p class="blockquote-line">Tutorial example data are just that--examples we use to illustrate tool features. Data are inappropriate for deriving biological significance. Although we have aligned and post-alt processed reads to GRCh38, in this tutorial we focus only on results from the primary assembly.</p>
</div></blockquote>

<p><a name="1" id="1"></a></p>

<hr></hr><h2>1. Create a sites-only panel of normals (PoN) with MuTect2 and CombineVariants</h2>

<p>The PoN allows additional filtering of calls, e.g. those that arise from technical artifacts. Therefore, it is important that the PoN consist of samples that are technically similar to and representative of the tumor samples, e.g. that were sequenced on the same platform using the same chemistry and analyzed using the same toolchain. For the tutorial, <strong>we have already created a PoN using forty 1000 Genomes Project exome samples</strong> aligned to GRCh38. We chose these randomly, downloaded CRAM files from the <em>1000 Genomes Project</em> GRCh38 FTP site, converted to BAMs and indexed, and used the data directly with MuTect2 without further modification.</p>

<p>Within the tutorial bundle, the <em>1kg_40_m2pon_subset50k.vcf.gz</em> and <em>1kg_40_m2pon_sitesonly_subset50k.vcf.gz</em> files represent the 40-sample PoN that we will use in <strong>Sections 5</strong>.</p>

<p>We emulate the PoN creation steps below but with small substitute data.</p>

<h3>1.1. First, per sample BAM, invoke MuTect2’s <code class="code codeInline" spellcheck="false">--artifact_detection_mode</code> to generate a per-normal-sample callset.</h3>

<pre class="code codeBlock" spellcheck="false"> java -jar $GATK \
        -T MuTect2 \
        -I:tumor hcc1143_N_subset50k.bam \
        --dbsnp dbSNP142_GRCh38_subset50k.vcf.gz \
        --artifact_detection_mode \
        -o 1_normalforpon.vcf.gz \
        -L chr6:33,413,000-118,315,000 \
        -R ~/Documents/ref/hg38/Homo_sapiens_assembly38.fasta
</pre>

<p>We include the dbSNP resource so as to annotate sites already present in the database. Here we make the motions of this step using our matched normal. In the real world, you would perform this step on other germline samples that are NOT your tumor's matched normal.</p>

<h3>1.2. Second, we use CombineVariants on all the per-normal-sample VCFs at once.</h3>

<pre class="code codeBlock" spellcheck="false">  java -jar $GATK \
     -T CombineVariants \
     --arg_file inputs.list \
     -minN 2 \
     --setKey "null" \
     --filteredAreUncalled \
     --filteredrecordsmergetype KEEP_IF_ANY_UNFILTERED \
     -o 2_pon_combinevariants.vcf.gz \
     -R ~/Documents/ref/hg38/Homo_sapiens_assembly38.fasta \
     --genotypemergeoption UNIQUIFY
</pre>

<p>We keep only the variant sites that are in at least two samples, using the <code class="code codeInline" spellcheck="false">-minN 2</code> option. We omit filtered calls with the <code class="code codeInline" spellcheck="false">--filteredAreUncalled</code> option but ensure a site is kept if at least two samples pass the site (<code class="code codeInline" spellcheck="false">KEEP_IF_ANY_UNFILTERED</code>, <code class="code codeInline" spellcheck="false">-minN 2</code>). Also, instead of supplying each of the forty VCFs with <code class="code codeInline" spellcheck="false">-V</code>, we pass in an argument file ending in <code class="code codeInline" spellcheck="false">.list</code> with the <code class="code codeInline" spellcheck="false">--arg_file</code> option. This file lists all the input arguments in the same line, e.g. “-V file1.vcf -V file2.vcf …”.</p>

<p>In the real world, you would NOT use the last option<code class="code codeInline" spellcheck="false">--genotypemergeoption UNIQUIFY</code>. The <code class="code codeInline" spellcheck="false">--genotypemergeoption UNIQUIFY</code> allows us to use the same normal VCF twice, as different samples, to illustrate this step with the data at hand. Without it, the command will error because the read group sample <code class="code codeInline" spellcheck="false">RGSM</code> tags are identical for our two VCFs. This generates a PoN with 13 records.</p>

<h3>1.3. Finally, generate a sites-only VCF with Picard's MakeSitesOnlyVcf.</h3>

<p>In the command in section 1.2, we could have used the <code class="code codeInline" spellcheck="false">--minimalVCF</code> option to generate a simplified sites-only VCF. However, in this tutorial we deviate from the standard practice because we are in learning-mode, and so we care to retain information when possible. Using the <code class="code codeInline" spellcheck="false">--minimalVCF</code> option with CombineVariants is faster but produces a qualitatively different VCF that removes TLOD scores. Here we generate a sites-only VCF that retains TLOD scores by removing the sample columns with Picard's MakeSitesOnlyVcf.</p>

<pre class="code codeBlock" spellcheck="false">java -jar $PICARD MakeSitesOnlyVcf \
    I=2_pon_combinevariants.vcf.gz \
    O=3_pon_siteonly.vcf.gz
</pre>

<p>The command also generates an index--<code class="code codeInline" spellcheck="false">3_pon_siteonly.vcf.gz.tbi</code>. This produces records that have only eight columns. Compare the before and after using <code class="code codeInline" spellcheck="false">gzcat</code>.</p>

<pre class="code codeBlock" spellcheck="false">gzcat 3_pon_siteonly.vcf.gz | grep -v '##' | less
</pre>

<p><a rel="nofollow" href="https://us.v-cdn.net/5019796/uploads/FileUpload/23/37a44fb1a63bef0c5dcba6fbdc415a.png"><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/23/37a44fb1a63bef0c5dcba6fbdc415a.png" width="" alt="image" style="text-align: ;" class="embedImage-img importedEmbed-img"></img></a></p>

<p><a name="2" id="2"></a><br><a rel="nofollow" href="#top">back to top</a></p>

<hr></hr><h2>2. (Optional) Estimate cross-sample contamination with ContEst</h2>

<p>ContEst detects cross-sample contamination and to some extent sample swaps. Even if our sample data are from cultured cell lines and we need not factor for tumor purity and tumor heterogeneity, we still need to consider contamination from other human sources and whether the extent of contamination is prohibitive or is in an acceptable range that allows us to proceed in analyzing a sample. We need to estimate contamination for both the tumor and the normal.</p>

<blockquote class="UserQuote blockquote"><div class="blockquote-content">
  <p class="blockquote-line">ContEst informs your downstream filtering. Consider tumor types in which you expect low purity. It would be good to know whether you have 0–2% or 2–5% contamination, as, depending on expected mutation rates, one result allows you to progress in analysis and the other requires planning manual review or even tossing the data.</p>
</div></blockquote>

<p>For on-the-fly genotyping of the normal, ContEst will call any site with &gt;80% bases showing an alternate allele <strong>with at least 50x coverage</strong> homozygous variant. If your normal sample has lower coverage overall, then you can alternately provide ContEst with a genotyped VCF using the <code class="code codeInline" spellcheck="false">--genotypes</code> option.</p>

<pre class="code codeBlock" spellcheck="false">java -jar $GATK -T ContEst \
        -I:eval hcc1143_T_subset50k.bam \
        -I:genotype hcc1143_N_subset50k.bam \
        --popfile hapmap_3.3_grch38_pop_stratified_af_subset50k.vcf.gz \
        --interval_set_rule INTERSECTION \
        -o 4_T_contest.txt \
        -R ~/Documents/ref/hg38/Homo_sapiens_assembly38.fasta
</pre>

<p>For the normal, use a genotyped VCF of the normal. Our genotypes are from HaplotypeCaller.</p>

<pre class="code codeBlock" spellcheck="false">java -jar $GATK -T ContEst \
        -I:eval hcc1143_N_subset50k.bam \
        --genotypes hcc1143_N_haplotypecaller50k.vcf.gz \
        --popfile hapmap_3.3_grch38_pop_stratified_af_subset50k.vcf.gz \
        --interval_set_rule INTERSECTION \
        -o 5_N_contest.txt \
        -R ~/Documents/ref/hg38/Homo_sapiens_assembly38.fasta
</pre>

<p>If there is insufficient data to make a meaningful estimate, the resulting file will contain a warning. Take a look at each result using <code class="code codeInline" spellcheck="false">cat</code>. Of the eight columns, the fourth column, labeled <em>contamination</em>, gives the percent contamination.</p>

<p><a rel="nofollow" href="https://us.v-cdn.net/5019796/uploads/FileUpload/be/36824920fc56cd0a3bd6f1d6039d65.png"><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/be/36824920fc56cd0a3bd6f1d6039d65.png" width="" alt="image" style="text-align: ;" class="embedImage-img importedEmbed-img"></img></a></p>

<p><a rel="nofollow" href="https://us.v-cdn.net/5019796/uploads/FileUpload/4e/1c6c7bb5c16967628a338e8723e771.png"><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/4e/1c6c7bb5c16967628a338e8723e771.png" width="" alt="image" style="text-align: ;" class="embedImage-img importedEmbed-img"></img></a></p>

<p>Converting 1.9% to a fraction gives us 0.019. So we know going forward that any calls with less than ~0.02 alternate allele fraction are suspect. For the tutorial, we omit the contamination estimate from the MuTect2 analysis.</p>

<blockquote class="UserQuote blockquote"><div class="blockquote-content">
  <p class="blockquote-line">Could this represent the fraction of a tumor subpopulation? If we estimate contamination for the normal sample using the tumor BAM to genotype on-the-fly, then ContEst estimates 42.2% contamination using 2,070 sites. Why does this happen?</p>
</div></blockquote>

<hr></hr><p><a name="2.1"></a></p>

<h3>☞ How does MuTect use the contamination estimate?</h3>

<p>The handling differs for the different versions of MuTect. MuTect1 incorporates the contamination estimate with the <code class="code codeInline" spellcheck="false">--fraction_contamination</code> option as a hard cutoff for calling in all samples. For the same parameter, v3.7 MuTect2 uses the fraction to downsample reads for each alternate allele. For example, if a site contains X coverage depth, then MuTect2 will remove X times the contamination fraction of reads for each alternate allele.</p>

<p><a name="3" id="3"></a><br><a rel="nofollow" href="#top">back to top</a></p>

<hr></hr><h2>3. (Optional) Estimate extent of FFPE and OxoG artifacts with CollectSequencingArtifactMetrics</h2>

<p>Picard’s CollectSequencingArtifactMetrics provides us with an estimate of the extent of FFPE and OxoG artifacts as well as other potential artifacts related to sequence context. These the tool categorizes as those that occur before hybrid selection (<em>preadapter</em>) and those that occur during hybrid selection (<em>baitbias</em>). The tool provides a global view of the genome that empowers decision making in ways that site-specific analyses cannot. For example, the metrics can help you decide whether you should consider downstream filtering.</p>

<pre class="code codeBlock" spellcheck="false">java -jar $PICARD CollectSequencingArtifactMetrics \
     I=hcc1143_T_subset50k.bam  \
     O=6_T_artifact \
     R=~/Documents/ref/hg38/Homo_sapiens_assembly38.fasta
</pre>

<pre class="code codeBlock" spellcheck="false">java -jar $PICARD CollectSequencingArtifactMetrics \
     I=hcc1143_N_subset50k.bam  \
     O=7_N_artifact \
     R=~/Documents/ref/hg38/Homo_sapiens_assembly38.fasta
</pre>

<p>Of the multiple metrics files (five each), examine the <em>pre_adapter_summary_metrics</em> using <code class="code codeInline" spellcheck="false">cat</code>. Here are the metrics for the full callsets that show similar results to that of our small data.</p>

<p><a rel="nofollow" href="https://us.v-cdn.net/5019796/uploads/FileUpload/3b/8bb1c8bdfd02f3617f154971fd2690.png"><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/3b/8bb1c8bdfd02f3617f154971fd2690.png" width="" alt="image" style="text-align: ;" class="embedImage-img importedEmbed-img"></img></a></p>

<p><a rel="nofollow" href="https://us.v-cdn.net/5019796/uploads/FileUpload/28/0b89043b78f6a5798056f68d8afdfa.png"><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/28/0b89043b78f6a5798056f68d8afdfa.png" width="" alt="image" style="text-align: ;" class="embedImage-img importedEmbed-img"></img></a></p>

<hr></hr><p><a name="3.1"></a></p>

<h3>☞ How do I interpret the TOTAL_QSCORE?</h3>

<p>The TOTAL_QSCORE is Phred-scaled such that lower scores equate to a higher probability the change is artifactual. E.g. 40 translates to 1 in 10,000 probability. For OxoG, a rough cutoff for concern is 30. If numbers are less than 30, then plan to filter your somatic calls further.</p>

<hr></hr><p><a name="3.2"></a></p>

<h3>☞ What is FFPE? What is OxoG?</h3>

<p><strong>FFPE</strong> stands for formalin-fixed, paraffin-embedded. Formaldehyde deaminates cytosines and thereby results in C→T transition mutations.</p>

<p>For an explanation of <strong>OxoG</strong> see <a rel="nofollow" href="https://software.broadinstitute.org/gatk/documentation/article?id=6328">its Dictionary entry</a>. Breifly, oxidation of guanine to 8-oxoguanine results in G→T transversion mutations during library preparation. Another Picard tool, CollectOxoGMetrics, similarly gives Phred-scaled scores for the 16 three-base extended sequence contexts. For even more details on this artifact, see the <a rel="nofollow" href="https://doi.org/10.1093/nar/gks1443"><em>2013 Costello et al, article in Nucleic Acids Research</em></a>. For SNVs in our MuTect2 callset, the FOXOG annotation refers to fraction oxoG. Downstream tools such as Broad CGA’s D-ToxoG use this information. For more discussion, see <a rel="nofollow" href="http://gatkforums.broadinstitute.org/gatk/discussion/8771">Article#8771</a> and <a rel="nofollow" href="http://gatkforums.broadinstitute.org/gatk/discussion/8183">Article#8183</a>.</p>

<p><a name="4" id="4"></a><br><a rel="nofollow" href="#top">back to top</a></p>

<hr></hr><h2>4. Call somatic SNVs and indels with MuTect2</h2>

<p><a rel="nofollow" href="https://us.v-cdn.net/5019796/uploads/FileUpload/df/953f9242333afc2771386b0aefc3e1.png"><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/df/953f9242333afc2771386b0aefc3e1.png" width="360" alt="image" style="float: right;" class="embedImage-img importedEmbed-img"></img></a> Our data is targeted capture exomes that have coverage depth variability, e.g. with tapering depths at the ends of target regions as shown in the IGV screenshot.</p>

<p>Consider MuTect2’s <code class="code codeInline" spellcheck="false">-minPruning</code> option. The default is set to two. For our exome data, where coverage occurs in mountainous peaks and falls off steeply around the edges, what this means is that at least two reads must support a path in the assembly graph for consideration, whether in the normal or tumor. Thus, the depth of coverage at a site has implications for calling contaminant alleles as well as low fraction alleles.</p>

<p>For the large dataset, we call on the entirety of the primary assembly regions, without an intervals file. For our tutorial, because calling is compute-intensive, we make somatic calls for a small region only. We provide all three resource files--dbSNP, COSMIC and the PoN. The PoN is made from forty <em>1000 Genomes Project</em> exomes.</p>

<pre class="code codeBlock" spellcheck="false">java -jar $GATK -T MuTect2 \
     -I:tumor hcc1143_T_subset50k.bam \
     -I:normal hcc1143_N_subset50k.bam \
     --dbsnp dbSNP142_GRCh38_subset50k.vcf.gz \
     --cosmic CosmicCodingMuts_grch38_subset50k.vcf.gz \
     --normal_panel 1kg_40_m2pon_sitesonly_subset50k.vcf.gz \
     --output_mode EMIT_VARIANTS_ONLY \
     -o 8_mutect2.vcf.gz \
     -L chr6:33,413,000-118,315,000 \
     -R ~/Documents/ref/hg38/Homo_sapiens_assembly38.fasta  
</pre>

<p>This produces a VCF with four records. Let’s examine the callset with <code class="code codeInline" spellcheck="false">gzcat</code>.</p>

<pre class="code codeBlock" spellcheck="false">gzcat 8_mutect2.vcf.gz | grep -v ‘##’ 
</pre>

<p><a rel="nofollow" href="https://us.v-cdn.net/5019796/uploads/editor/kg/gc2dug7nqng3.png"><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/ae/d060b6817653cda53dbc647d46bc22.png" width="" alt="image" style="text-align: ;" class="embedImage-img importedEmbed-img"></img></a></p>

<p>We see three passing sites (PASS) and one filtered site (alt_allele_in_normal). Three of the sites are deletions and one is a SNV. Each of the tumor and normal samples have genotype calls in the format:</p>

<pre class="code codeBlock" spellcheck="false">GT:AD:AF:ALT_F1R2:ALT_F2R1:FOXOG:QSS:REF_F1R2:REF_F2R1
</pre>

<blockquote class="UserQuote blockquote"><div class="blockquote-content">
  <p class="blockquote-line">What do each of these metrics represent?</p>
</div></blockquote>

<p><a name="5" id="5"></a><br><a rel="nofollow" href="#top">back to top</a></p>

<hr></hr><h2>5. Subset PASS calls plus suspect filtered calls with SelectVariants</h2>

<p>The callset contains both passing and filtered SNVs and indels. Let us subset out just indel calls for further scrutiny, using the full callset (n=4152).</p>

<pre class="code codeBlock" spellcheck="false">java -jar $GATK -T SelectVariants \
    -V hcc1143_cloud.vcf.gz \
    -o 9_mutect2_indels.vcf.gz \
    -selectType INDEL \
    -R ~/Documents/ref/hg38/Homo_sapiens_assembly38.fasta
</pre>

<p>This gives us 581 variant records. We count the passing calls with:</p>

<pre class="code codeBlock" spellcheck="false">gzcat 9_mutect2_indels.vcf.gz | grep -v '#' | grep 'PASS' | wc -l
</pre>

<blockquote class="UserQuote blockquote"><div class="blockquote-content">
  <p class="blockquote-line">How many somatic indel calls do we have? How many bases is the largest indel?</p>
</div></blockquote>

<p><a rel="nofollow" href="https://us.v-cdn.net/5019796/uploads/FileUpload/8a/56b37a909a4663b920aa0173cd5afe.png"><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/8a/56b37a909a4663b920aa0173cd5afe.png" width="360" alt="image" style="float: right;" class="embedImage-img importedEmbed-img"></img></a> The table lists the nine filters MuTect2 emits to the FILTER column. The table also counts the instances each filter occurs for indel calls, as well as the instances a filter is the sole reason for an indel site not passing.</p>

<blockquote class="UserQuote blockquote"><div class="blockquote-content">
  <p class="blockquote-line">Which filter(s) appear to have the greatest impact? Is this what you expect? What type of filtering do you trust the most? What types of calls do you think compels manual review?</p>
</div></blockquote>

<p>Examine the passing records and pay attention to the AD and AF fields.</p>

<pre class="code codeBlock" spellcheck="false">gzcat 9_mutect2_indels.vcf.gz | grep -v '#' | grep 'PASS' | less
</pre>

<blockquote class="UserQuote blockquote"><div class="blockquote-content">
  <p class="blockquote-line">What can you say about the sensitivity of the caller? For ~200Mb genome-wide coverage, how many germline SNPs and indels do you expect? Somatic SNVs and indels?</p>
</div></blockquote>

<p>At first glance, we see additional calls in the GRCh38 callset compared to an older GRCh37 callset. However, the GRCh37 analysis restricted MuTect2 calling to target capture intervals whereas our GRCh38 calling does not. If we confine the counts of calls to the capture regions, we see MuTect2 gives similar number of passing calls for both references (<strong>Table</strong>).</p>

<p><a rel="nofollow" href="https://us.v-cdn.net/5019796/uploads/FileUpload/21/c9546e02346040c01253398e1ece82.png"><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/21/c9546e02346040c01253398e1ece82.png" width="" alt="image" style="text-align: ;" class="embedImage-img importedEmbed-img"></img></a></p>

<blockquote class="UserQuote blockquote"><div class="blockquote-content">
  <p class="blockquote-line">The above comparison is limited. How would you compare calling on GRCh37 versus on GRCh38? <br>
  Given what you know about alt-aware alignment and post-alt processing, is there an additional step that this analysis is missing?<br>
  Below are the six GRCh37 passing indel calls. Do they differ from the six GRCh38 passing indel calls? If so, how?</p>
</div></blockquote>

<p><a rel="nofollow" href="https://us.v-cdn.net/5019796/uploads/FileUpload/58/b9dc4925affb1080734e82a754842e.png"><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/58/b9dc4925affb1080734e82a754842e.png" width="" alt="image" style="text-align: ;" class="embedImage-img importedEmbed-img"></img></a><br><a rel="nofollow" href="https://us.v-cdn.net/5019796/uploads/FileUpload/59/ec53dc2cac58fabf4e5c9d251ac495.png"><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/59/ec53dc2cac58fabf4e5c9d251ac495.png" width="" alt="image" style="text-align: ;" class="embedImage-img importedEmbed-img"></img></a></p>

<p>Examining the records, we find the GRCh38 calls differ qualitatively from that of GRCh37. This simplistic comparison is meant to show how deriving a good somatic callset involves comparing callsets, e.g. from different callers, manually reviewing passing and filtered calls and, as alluded to, additional filtering.</p>

<p><a name="6" id="6"></a><br><a rel="nofollow" href="#top">back to top</a></p>

<hr></hr><h2>6. Generate BAMOUTs  with MuTect2 and manually review alignments with IGV</h2>

<p>Manual review extends from deciphering call record annotations to the nitty-gritty of reviewing read alignments using a visualizer.</p>

<h3>6.1. Generate the BAMOUT with MuTect2</h3>

<p>To review read alignments, we must generate those that reflect MuTect2’s graph-assembly results. We call these bamouts for the parameter we invoke. To demonstrate, we use a small interval.</p>

<pre class="code codeBlock" spellcheck="false">java -jar $GATK -T MuTect2 \
    -I:tumor hcc1143_T_subset50k.bam \
    -I:normal hcc1143_N_subset50k.bam \
    --dbsnp dbSNP142_GRCh38_subset50k.vcf.gz \
    --cosmic CosmicCodingMuts_grch38_subset50k.vcf.gz \
    --normal_panel 1kg_40_m2pon_sitesonly_subset50k.vcf.gz \
    --disableOptimizations --dontTrimActiveRegions --forceActive \
    -L 8_mutect2.vcf.gz \
    -ip 1000 \
    -bamout 10_m2_bamout.bam \
    -R ~/Documents/ref/hg38/Homo_sapiens_assembly38.fasta
</pre>

<p>Notice the special options <code class="code codeInline" spellcheck="false">--disableOptimizations</code>, <code class="code codeInline" spellcheck="false">--dontTrimActiveRegions</code> and <code class="code codeInline" spellcheck="false">--forceActive</code> (many thanks to <a href="https://gatkforums.broadinstitute.org/gatk/profile/Sheila" rel="nofollow">@Sheila</a> for these). To easily generate bamouts for regions surrounding call sites, we use the four-record VCF from step 4 with the <code class="code codeInline" spellcheck="false">-L</code> option and add padding around the sites, e.g. 1000 bases before and after each site, with the <code class="code codeInline" spellcheck="false">-ip</code> option.</p>

<p>Similarly, we have pre-generated a larger bamout for you using a VCF of the 76 solo-filtered indel sites and 22 passing indel sites.</p>

<h3>6.2. Setup on IGV for manual review</h3>

<p>In the remaining exercise, we will use data subset around the 76-solo-filtered and 22 passing indel sites. We will review data in IGV. First, load GRCh38 as the reference in IGV. Then load these files in order:</p>

<p>      i.   98_m2_indels.vcf.gz</p>

<p>      ii.  98_m2_bamout.bam</p>

<p>      iii. hcc1143_T_subset50k.bam</p>

<p>      iv. hcc1143_N_subset50k.bam</p>

<p>Go to the SLC35F1 locus at <strong>chr6:118314029</strong> by typing in the genomic coordinates into IGV. If these alignments seem hard to decipher, it is because we need to tweak some settings.</p>

<p>      a. In the View&gt;Preferences&gt;Alignments panel, turn off downsampling and turn on the center line.</p>

<p>      b. Center the track on the deletion you see by dragging the track so that the center line is at the leftmost edge of the deletion. You may have to scroll to find the deletion. </p>

<p>      c. In the tracks-view, right-click on the bamout track and select the following:</p>

<p>         - Group by sample</p>

<p>         - Color by tag: HC</p>

<p>         - Sort by base</p>

<p><a rel="nofollow" href="https://us.v-cdn.net/5019796/uploads/editor/0o/m07unw6x4zpp.png"><div style="text-align: center;"><img src="https://us.v-cdn.net/5019796/uploads/editor/0o/m07unw6x4zpp.png" width="550" alt="image" style="text-align: ;" class="embedImage-img importedEmbed-img"></img></div></a></p>

<blockquote class="UserQuote blockquote"><div class="blockquote-content">
  <p class="blockquote-line">What are the three grouped tracks for the bamout? How do you feel about this indel call?</p>
</div></blockquote>

<h3>6.3. Now it’s your turn to review some sites.</h3>

<p>Here are ten variant records to start.</p>

<table><thead><tr><th><strong>CHROM</strong></th>
  <th><strong>POS</strong></th>
  <th><strong>ID</strong></th>
  <th><strong>REF</strong></th>
  <th><strong>ALT</strong></th>
  <th><strong>QUAL</strong></th>
  <th><strong>FILTER</strong></th>
  <th><strong>INFO</strong></th>
  <th><strong>FORMAT</strong></th>
  <th><strong>NORMAL</strong></th>
  <th><strong>TUMOR</strong></th>
</tr></thead><tbody><tr><td>chr1</td>
  <td>150577260</td>
  <td>.</td>
  <td>GGCTAGGTT</td>
  <td>G</td>
  <td>.</td>
  <td>clustered_events</td>
  <td>ECNT=2;HCNT=6;MAX_ED=64;MIN_ED=64;NLOD=36.90;TLOD=12.00</td>
  <td>GT:AD:AF:ALT_F1R2:ALT_F2R1:QSS:REF_F1R2:REF_F2R1</td>
  <td>0/0:125,0:0.00:0:0:4175,0:53:72</td>
  <td>0/1:237,5:0.021:4:0:7808,166:133:102</td>
</tr><tr><td>chr4</td>
  <td>39973442</td>
  <td>.</td>
  <td>C</td>
  <td>CAG</td>
  <td>.</td>
  <td>PASS</td>
  <td>ECNT=2;HCNT=4;MAX_ED=2;MIN_ED=2;NLOD=24.69;TLOD=9.71</td>
  <td>GT:AD:AF:ALT_F1R2:ALT_F2R1:PGT:PID:QSS:REF_F1R2:REF_F2R1</td>
  <td>0/0:80,0:0.00:0:0:0</td>
  <td>1:39973442_C_CAG:2523,0:39:41|0/1:83,4:0.044:1:3:0|1:39973442_C_CAG:2636,132:46:31</td>
</tr><tr><td>chr6</td>
  <td>29944450</td>
  <td>.</td>
  <td>TGGA</td>
  <td>T</td>
  <td>.</td>
  <td>panel_of_normals</td>
  <td>ECNT=1;HCNT=3;MAX_ED=.;MIN_ED=.;NLOD=8.74;TLOD=14.98</td>
  <td>GT:AD:AF:ALT_F1R2:ALT_F2R1:QSS:REF_F1R2:REF_F2R1</td>
  <td>0/0:48,1:0.023:0:1:1513,31:25:23</td>
  <td>0/1:31,5:0.143:1:3:981,152:12:18</td>
</tr><tr><td>chr8</td>
  <td>47819519</td>
  <td>.</td>
  <td>A</td>
  <td>ACATTTAGTATAGGTTAGTGACATTTAGCATAG</td>
  <td>.</td>
  <td>germline_risk</td>
  <td>ECNT=1;HCNT=8;MAX_ED=.;MIN_ED=.;NLOD=1.94;TLOD=79.27</td>
  <td>GT:AD:AF:ALT_F1R2:ALT_F2R1:QSS:REF_F1R2:REF_F2R1</td>
  <td>0/0:7,0:0.00:0:0:220,0:4:3</td>
  <td>0/1:13,21:0.667:8:12:401,592:6:7</td>
</tr><tr><td>chr10</td>
  <td>103350983</td>
  <td>.</td>
  <td>A</td>
  <td>AGGAGGC</td>
  <td>.</td>
  <td>panel_of_normals</td>
  <td>ECNT=1;HCNT=1;MAX_ED=.;MIN_ED=.;NLOD=2.44;RPA=2,3;RU=GGAGGC;STR;TLOD=7.17</td>
  <td>GT:AD:AF:ALT_F1R2:ALT_F2R1:QSS:REF_F1R2:REF_F2R1</td>
  <td>0/0:8,0:0.00:0:0:248,0:6:2</td>
  <td>0/1:6,2:0.286:1:1:183,60:3:3</td>
</tr><tr><td>chr11</td>
  <td>66295655</td>
  <td>.</td>
  <td>AC</td>
  <td>A</td>
  <td>.</td>
  <td>germline_risk</td>
  <td>ECNT=1;HCNT=1;MAX_ED=.;MIN_ED=.;NLOD=1.20;RPA=6,5;RU=C;STR;TLOD=37.86</td>
  <td>GT:AD:AF:ALT_F1R2:ALT_F2R1:QSS:REF_F1R2:REF_F2R1</td>
  <td>0/0:4,0:0.00:0:0:98,0:1:3</td>
  <td>0/1:0,15:1.00:6:9:0,364:0:0</td>
</tr><tr><td>chr12</td>
  <td>6885222</td>
  <td>.</td>
  <td>G</td>
  <td>GA</td>
  <td>.</td>
  <td>clustered_events</td>
  <td>ECNT=2;HCNT=2;MAX_ED=36;MIN_ED=36;NLOD=11.43;RPA=4,5;RU=A;STR;TLOD=8.14</td>
  <td>GT:AD:AF:ALT_F1R2:ALT_F2R1:QSS:REF_F1R2:REF_F2R1</td>
  <td>0/0:38,0:0.00:0:0:1291,0:17:21</td>
  <td>0/1:43,5:0.111:5:0:1421,160:12:28</td>
</tr><tr><td>chr15</td>
  <td>89633008</td>
  <td>.</td>
  <td>T</td>
  <td>TAGGGAGGGAGGGAGGG</td>
  <td>.</td>
  <td>panel_of_normals</td>
  <td>ECNT=1;HCNT=1;MAX_ED=.;MIN_ED=.;NLOD=3.30;RPA=4,8;RU=AGGG;STR;TLOD=33.19</td>
  <td>GT:AD:AF:ALT_F1R2:ALT_F2R1:QSS:REF_F1R2:REF_F2R1</td>
  <td>0/0:16,0:0.00:0:0:464,0:11:5</td>
  <td>0/1:13,10:0.474:8:1:294,284:6:6</td>
</tr><tr><td>chr16</td>
  <td>67651997</td>
  <td>.</td>
  <td>C</td>
  <td>CACCATGTTGGCCAGGT</td>
  <td>.</td>
  <td>PASS</td>
  <td>ECNT=1;HCNT=4;MAX_ED=.;MIN_ED=.;NLOD=28.98;TLOD=13.81</td>
  <td>GT:AD:AF:ALT_F1R2:ALT_F2R1:QSS:REF_F1R2:REF_F2R1</td>
  <td>0/0:105,0:0.00:0:0:2912,0:51:54</td>
  <td>0/1:105,6:0.049:2:3:2880,182:49:54</td>
</tr><tr><td>chr20</td>
  <td>19665792</td>
  <td>.</td>
  <td>CGTGTGTGT</td>
  <td>C</td>
  <td>.</td>
  <td>germline_risk</td>
  <td>ECNT=1;HCNT=2;MAX_ED=.;MIN_ED=.;NLOD=0.617;RPA=20,16;RU=GT;STR;TLOD=12.30</td>
  <td>GT:AD:AF:ALT_F1R2:ALT_F2R1:QSS:REF_F1R2:REF_F2R1</td>
  <td>0/0:3,0:NaN:0:0:0,0:0:0</td>
  <td>0/1:9,9:0.778:3:3:55,234:0:2</td>
</tr></tbody></table><blockquote class="UserQuote blockquote"><div class="blockquote-content">
  <p class="blockquote-line">For any of these sites, would you reverse a PASS or filter call? Why?</p>
</div></blockquote>

<p>You can scroll through variant calls in IGV using a keyboard shortcut. Select the <em>98_m2_indels.vcf.gz</em> track and press  <code class="code codeInline" spellcheck="false">CTRL</code> + <code class="code codeInline" spellcheck="false">F</code> to jump forward to the next variant record or <code class="code codeInline" spellcheck="false">CTRL</code> + <code class="code codeInline" spellcheck="false">B</code> to jump back to the previous variant record.</p>

<p><a name="7" id="7"></a><br><a rel="nofollow" href="#top">back to top</a></p>

<hr></hr><h2>7. Debrief and related resources</h2>

<p>As you experienced, understanding sequencing technology and its artifact modes, as well as tools and their quirks, are crucial to discerning good calls from bad calls. This is ever more so important in somatic calling due to confounding factors of (i) tumor purity, (ii) tumor heterogeneity and (iii) the needle-in-a-haystack rate of somatic mutations compared to the inherent noisiness of sequencing artifacts. We have yet to develop machine-learning algorithms that can help us in this process.</p>

<p>What is the next step after you generate a carefully manicured somatic callset?</p>

<ul><li>Annotate with VEP <a rel="nofollow" href="http://useast.ensembl.org/info/docs/tools/vep/index.html">http://useast.ensembl.org/info/docs/tools/vep/index.html</a> to predict phenotypic changes and confirm or hypothesize biochemical effects.</li>
<li>For cohorts, use MutSig to discover driver mutations. Available on GenePattern <a rel="nofollow" href="http://software.broadinstitute.org/cancer/software/genepattern/modules/docs/MutSigCV">http://software.broadinstitute.org/cancer/software/genepattern/modules/docs/MutSigCV</a>.</li>
<li>Combine with other data, e.g. RNA expression, for integrative analyses.</li>
</ul><p>Take a look at the dSKY plot at <a rel="nofollow" href="https://figshare.com/articles/D_SKY_for_HCC1143/2056665">https://figshare.com/articles/D_SKY_for_HCC1143/2056665</a>. It shows somatic copy number alterations for our HCC1143 sample and its colorful results remind us that calling SNVs and indels is only one part of cancer genome analyses. We cover calling somatic copy number alterations in <a rel="nofollow" href="https://software.broadinstitute.org/gatk/documentation/article?id=9143">Tutorial#9143</a>.</p>

<p><a rel="nofollow" href="#top">back to top</a></p>

<hr></hr><p>Many thanks to <a href="https://gatkforums.broadinstitute.org/gatk/profile/Geraldine_VdAuwera" rel="nofollow">@Geraldine_VdAuwera</a> for the workflow diagram at top.</p>

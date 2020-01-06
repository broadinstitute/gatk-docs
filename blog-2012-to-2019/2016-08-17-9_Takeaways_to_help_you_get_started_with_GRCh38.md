## 9 Takeaways to help you get started with GRCh38

By shlee

<h3>We are starting official support of GRCh38, a reference genome with alternate contigs.</h3>

<p>In fact, going forward all of our new projects will use GRCh38. During this transition over the coming year, we will keep supporting GRCh37/hg19. Here are nine takeaways to help you get started in using the latest reference.</p>

<hr></hr><h3>1. GRCh38 is special because it has alternate contigs that represent population haplotypes.</h3>

<p><a rel="nofollow" href="https://us.v-cdn.net/5019796/uploads/FileUpload/e4/444371f8e2b21f4ab7a3ec308b5d2e.png"><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/e4/444371f8e2b21f4ab7a3ec308b5d2e.png" height="180" alt="image" style="float: right;" class="embedImage-img importedEmbed-img"></img></a> Don’t know alternate contig from alternate dimension? Spend five minutes now to review terminology in our <em>Dictionary</em> entry <a rel="nofollow" href="https://software.broadinstitute.org/gatk/guide/article?id=7857">Reference Genome Components</a>. At the least, you should understand the distinction between the primary assembly and alternate contigs.</p>

<p>Long BAM headers notwithstanding, GRCh38 alternate contig sequences are only ~3.6% of the primary assembly length (see table). They encompass alternate haplotypes for which we cannot easily represent variants on the primary assembly. According to my estimation, roughly a tenth of a percent (101,845 basepairs) of the alternate sequence appears highly divergent.</p>

<hr></hr><h3>2. The GRCh38 <em>analysis set</em> hard-masks regions and provides decoy contigs for optimal read mapping.</h3>

<p>Download your own analysis reference set from the <a rel="nofollow" href="https://software.broadinstitute.org/gatk/download/bundle">GATK resource bundle</a>. Be certain you are mapping to a version of the genome that hard-masks--replaces with Ns--Y chromosome PARs. Imagine the <a rel="nofollow" href="http://www.genenames.org/cgi-bin/genefamilies/set/715">SHOX</a> of not being able to call variants for pseudoautosomal regions.</p>

<hr></hr><h3>3. The challenge alternate contigs presents is a familiar one.</h3>

<p>Conceptually it rewraps and regifts the challenge of calling variants for paralogous regions of the genome. The difference is that alternate contigs encompass sequence that is homologous as well as highly divergent for loci across a population instead of across a genome. By definition, we cannot easily represent the variants alternate haplotypes generate against the primary assembly. And so GRCh38 arms us with named alternate contigs that beg to be used when we call their variants. How folks choose to do this with the leeway given by <a rel="nofollow" href="https://samtools.github.io/hts-specs">VCF specifications</a> will depend on research aims.</p>

<hr></hr><h3>4. Latest versions of BWA-MEM handle GRCh38 alternate contig mappings.</h3>

<p><a rel="nofollow" href="https://us.v-cdn.net/5019796/uploads/FileUpload/f2/753c2408344c21309fe081276430ef.png"><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/f2/753c2408344c21309fe081276430ef.png" height="300" alt="image" style="float: left;" class="embedImage-img importedEmbed-img"></img></a> You want to map in an alt-aware manner, i.e. you want your alts handled. Without the handling, you’ll just get a bunch of MAPQ zero ghost reads mapping to both (i) the primary assembly regions that have alternate contigs and (ii) the homologous alternate contig regions. Just as you cannot eat ghost chips, GATK tools refuse to consider zero (and low) MAPQ alignments. No. You. Do. Not. Want. This. Make sure to <a rel="nofollow" href="http://sourceforge.net/projects/bio-bwa/files/">update to BWA-MEM version 0.7.13+</a> to be able to map with alt-handling. I’m partial to calling it ghost-busting. This enables two things. First, because it prioritizes alignments on the primary assembly by <em>disappearing</em> alignments from the alternate contigs, it effectively lets you avoid redundantly calling variants on homologous regions of alternate loci. Second, it allows for an additional postalt-processing step that populates multiple alt loci contig(s) with nonzero MAPQ alignments. This enables super-charged variant calling on all the alt contigs. For details, read BWA’s alt-specific <a rel="nofollow" href="https://github.com/lh3/bwa/blob/master/README-alt.md">README-alt</a>. Although the README currently is marked for an earlier version of the tool, its concepts still apply.</p>

<hr></hr><h3>5. Alt-handling requires the SAM format ALT index file.</h3>

<p>Special handling requires a special index file. Alt-handling requires that an ALT index is available with the other BWA indexes. Heng Li provides the ALT index for GRCh38 in the <a rel="nofollow" href="https://sourceforge.net/projects/bio-bwa/files/bwakit/">linux bwa.kit v0.7.15</a>. Find the <em>hs38DH.fa.alt</em> file in the <em>resource-GRCh38</em> folder and explore it using Samtools to confirm the following.</p>

<ul><li>3,177 total records</li>
<li>792 mapped, of which six are supplementary, that correspond to alternate contigs

<ul><li>528 HLA contigs (3 supplementary)</li>
<li>264 non-HLA alt contigs (3 supplementary)</li>
</ul></li>
</ul><p>Each alternate contig record lists a CIGAR string, some of which are rather convoluted, that aligns the alternate contig back to its primary assembly locus. For six of the alternate contigs, we have two alignments each.</p>

<ul><li>Leaving us 2,385 unmapped records corresponding to decoy contigs. These exclude the EBV contig, which the index considers a part of the primary assembly.</li>
</ul><p>The decoys contain transposable and alpha satellite elements including diverged variants. Why are they represented in the ALT index? See the next takeaway.</p>

<hr></hr><h3>6. New Tutorial#8017 shows how to map to GRCh38 with alt-handling and then some.</h3>

<p><a rel="nofollow" href="https://software.broadinstitute.org/gatk/guide/article?id=8017">Tutorial#8017</a> starts with indexing the reference, reiterates the essentiality of the ALT index and then maps in an alt-aware manner using simulated reads to a miniature-reference. It then goes on to show how to postalt-process alignments using the bwa-postalt.js script. The tutorial does not tell you what to do per se, but rather shows what happens when you use certain options. You definitely want to read <a rel="nofollow" href="https://software.broadinstitute.org/gatk/guide/article?id=8017#5">sections 5–6</a> if you plan on calling variants on alternate contigs.</p>

<p>During postalt-processing, two reshufflings take place. First, alignments that can map to both a primary locus and an alternate locus are mapped to both with non-zero MAPQ alignments. These multimappers are supplementary on the alt. Second, if an alignment on the primary assembly aligns better on a decoy contig, then its alignment on the primary assembly is deprioritized with a zero MAPQ score. The tutorial gives an example of the first reshuffle. For those interested in seeing the second reshuffle, I have a suggestion. Change the mini-reference’s single ALT index record to mimic that of a decoy, i.e. change it to an unmapped record, then see what happens when you postalt-process.</p>

<p>If your research aims require one of the reshufflings but not the other, or selective handling for particular loci, then one approach could be to modify the ALT index for the selective postalt-processing.</p>

<hr></hr><h3>7. Simulate read mapping for your favorite alternate haplotype.</h3>

<p><a rel="nofollow" href="https://software.broadinstitute.org/gatk/guide/article?id=7859">Tutorial#7859</a> shows how to generate simulated reads so you can see results akin to those in Tutorial#8017 for <em>your</em> favorite alternate contig. For both tutorials, I use the <a rel="nofollow" href="http://www.genenames.org/cgi-bin/gene_symbol_report?hgnc_id=HGNC:4458">GPI gene</a>’s singular alternate contig as the example.</p>

<p>Using the liberty the blog format provides, I will digress here. The GPI locus encodes for <a rel="nofollow" href="https://en.wikipedia.org/wiki/Glucose-6-phosphate_isomerase">glucose-6-phosphate isomerase</a>, a protein that has an intracellular role in sugar metabolism and also moonlights extracellularly as Neuroleukin, a factor involved in nerve tissue growth. I chose this locus because (i) it is one of the smallest alternate contigs not near a telomere, (ii) I used to study metabolism and (iii) I worked on an <a rel="nofollow" href="https://en.wikipedia.org/wiki/Glycophosphatidylinositol">identically named, unrelated molecule</a>. Yes, really.</p>

<p>So, how significant are the alternate contigs? To start answering this question, I asked another. What story can I find for the GPI locus?</p>

<p><a rel="nofollow" href="https://us.v-cdn.net/5019796/uploads/FileUpload/cd/bc3c30c80c0ea420e7abce0117a692.png"><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/cd/bc3c30c80c0ea420e7abce0117a692.png" height="240" alt="image" style="float: right;" class="embedImage-img importedEmbed-img"></img></a> I did a little digging last Saturday afternoon for evidence of the alternate haplotype in data resources. In <a rel="nofollow" href="http://www.gtexportal.org/home/">GTex</a>, a project that measures healthy tissue-specific RNA isoform expression, I found that the GPI locus provides cis-eQTLs for <a rel="nofollow" href="http://www.gtexportal.org/home/browseEqtls?location=WTIP&amp;tissueName=Lung">WTIP in lung tissue</a>. WTIP encodes for <a rel="nofollow" href="http://www.jbc.org/content/279/14/14398.abstract">Wilms tumor 1 interacting protein</a> and is three genes down from the GPI locus. Eight of the 11 eQTL sites on the GPI gene match SNPs that my simulated reads, representing the alternate haplotype, generate on the primary assembly. These sites, when I <a rel="nofollow" href="http://www.ncbi.nlm.nih.gov/snp?term=rs10402604%5BAll%20Fields%5D%20OR%20rs4806015%5BAll%20Fields%5D%20OR%20rs2278266%5BAll%20Fields%5D%20OR%20rs7408956%5BAll%20Fields%5D%20OR%20rs8191393%5BAll%20Fields%5D%20OR%20rs7248411%5BAll%20Fields%5D%20OR%20rs1135465%5BAll%20Fields%5D%20OR%20rs10412410%5BAll%20Fields%5D&amp;cmd=DetailsSearch">look them up in dbSNP</a>, are all listed as minor alleles and intronic variants. The average global minor allele frequency for the eight SNPs is 38.7% (+/- 0.90%), with 1936 (+/- 45.0) observations in the <em>1000 Genomes Project</em> phase 3 data. It looks like the GPI locus alternate haplotype is not uncommon and it already has some observed associations.</p>

<hr></hr><h3>8. Our production workflow for single sample variant calling on GRCh38 is public and uses shiny new features.</h3>

<p>Check it out in our <a rel="nofollow" href="https://github.com/broadinstitute/wdl/tree/develop/scripts/broad_pipelines">Broad pipelines WDL scripts repository</a>. The document describing the workflow has the <code class="code codeInline" spellcheck="false">.md</code> extension in the set named <strong>PairedEndSingleSampleWf</strong>. Even if you are unfamiliar with <a rel="nofollow" href="https://software.broadinstitute.org/gatk/blog?id=7349">what is a WDL</a>, no worries. The document focuses on explaining the data transformation steps from alignment to single-sample SNP and indel variant calling. The workflow maps paired reads in an alt-aware manner to GRCh38 and then uses HaplotypeCaller to generate a GVCF callset for the primary assembly. New features the workflow uses include <a rel="nofollow" href="https://broadinstitute.github.io/picard/command-line-overview.html#MarkDuplicates">query-grouped alignments through duplicate marking</a> and addition of NM and UQ tags with <a rel="nofollow" href="https://broadinstitute.github.io/picard/">SetNmAndUqTags</a>.</p>

<hr></hr><h3>9. Finally, there is no better time than now to start learning WDL.</h3>

<p>It’s pretty straightforward. Using instructions provided by our <a rel="nofollow" href="https://software.broadinstitute.org/wdl">WDL documentation</a>, even yours truly has written her first three scripts for Tutorial#8017’s workflows. These we share via our new <a rel="nofollow" href="https://github.com/broadinstitute/wdl/tree/develop/scripts/tutorials/gatk">GATK Tutorials WDL scripts repo</a>. WDL scripts will become more prevalent going forward. In conjunction with <a rel="nofollow" href="https://en.wikipedia.org/wiki/Docker_(software)">Docker</a>, these process-centric pipeline scripts enable better provenance and reproducibility in research. If you are a complete newb to WDL, e.g. don’t know how to pronounce the acronym, then start with <a rel="nofollow" href="https://software.broadinstitute.org/gatk/blog?id=7349">Blog#7349</a>.</p>

<hr></hr><p>Want to help build our GRCh38 resources? Share your findings by posting a comment.</p>

<hr></hr>
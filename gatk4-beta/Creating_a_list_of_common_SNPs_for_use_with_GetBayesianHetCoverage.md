## Creating a list of common SNPs for use with GetBayesianHetCoverage

By slee

<p>The first step in the GATK ACNV workflow (<a href="http://gatkforums.broadinstitute.org/gatk/discussion/7387/description-and-examples-of-the-steps-in-the-acnv-case-workflow" rel="nofollow">http://gatkforums.broadinstitute.org/gatk/discussion/7387/description-and-examples-of-the-steps-in-the-acnv-case-workflow</a>) is to naively call heterozygous SNPs in a case sample from the pileups at common SNP sites.  These SNP sites are specified by a Picard interval list and can be constructed from any suitable database of SNPs.  We outline below one possible method of building such a list with the GATK and Picard from the filtered 1000 Genomes Project Phase 3 markers used by BEAGLE 4.x.</p>

<p>First, download and extract the BEAGLE 1000G VCFs to a working folder:</p>

<pre class="code codeBlock" spellcheck="false">wget -r -nH -nd -np -R index.html -A 'chr*.1kg.phase3.v5a.vcf.zip' http://bochet.gcc.biostat.washington.edu/beagle/1000_Genomes_phase3_v5a/
unzip 'chr*.1kg.phase3.v5a.vcf.zip'
</pre>

<p>Structural variants have already been filtered from these VCFs.  We further filter out indels, multiallelic sites, and sites with minor-allele frequency less than a given threshold (we choose 10% here) using SelectVariants.  We then use CatVariants to merge the per-chromosome VCFs and produce a single VCF, which is finally converted to the desired interval list using Picard.  Note that sex chromosomes are excluded as they are not currently supported by the GATK CNV/ACNV workflows.</p>

<pre class="code codeBlock" spellcheck="false">PICARD_JAR=/seq/software/picard/current/bin/picard.jar
GATK_JAR=/humgen/gsa-hpprojects/GATK/bin/current/GenomeAnalysisTK.jar
HG19_FASTA=/seq/references/Homo_sapiens_assembly19/v1/Homo_sapiens_assembly19.fasta
CATVARIANTS_COMMAND="java -cp $GATK_JAR org.broadinstitute.gatk.tools.CatVariants -R $HG19_FASTA"

for chr in {1..22}
do 
    # Index the per-chromosome vcf.gz files using tabix.
    tabix -p vcf chr$chr.1kg.phase3.v5a.vcf.gz
    # Structural variants have already been filtered from these VCFs.  We further filter out indels, multiallelic sites, and sites with minor-allele frequency less than 10%.
    java -jar $GATK_JAR -T SelectVariants -R $HG19_FASTA -V chr$chr.1kg.phase3.v5a.vcf.gz -selectType SNP --restrictAllelesTo BIALLELIC -select 'vc.getCalledChrCount(vc.altAlleleWithHighestAlleleCount) * 1.0 / vc.calledChrCount &gt;= 0.10 &amp;&amp; vc.getCalledChrCount(vc.altAlleleWithHighestAlleleCount) * 1.0 / vc.calledChrCount &lt; 0.90' -o chr$chr.1kg.phase3.v5a.snp.maf10.biallelic.hg19.vcf.gz
    # Add filtered VCF for this chromosome to the list of inputs for CatVariants.
    CATVARIANTS_COMMAND="$CATVARIANTS_COMMAND -V chr$chr.1kg.phase3.v5a.snp.maf10.biallelic.hg19.vcf.gz"
done

# Finish constructing the CatVariants command and execute it.
CATVARIANTS_COMMAND="$CATVARIANTS_COMMAND -out allchr.1kg.phase3.v5a.snp.maf10.biallelic.hg19.vcf.gz --assumeSorted"
$CATVARIANTS_COMMAND

# Convert to interval list.
java -jar $PICARD_JAR VcfToIntervalList I=allchr.1kg.phase3.v5a.snp.maf10.biallelic.hg19.vcf.gz O=allchr.1kg.phase3.v5a.snp.maf10.biallelic.hg19.interval_list
</pre>

<p>Depending on the amount of free space in the working folder, commands to remove intermediate files can be added to the appropriate places above.</p>

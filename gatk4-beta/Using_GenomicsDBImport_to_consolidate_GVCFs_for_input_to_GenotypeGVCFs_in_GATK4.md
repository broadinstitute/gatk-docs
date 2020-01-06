## Using GenomicsDBImport to consolidate GVCFs for input to GenotypeGVCFs in GATK4

By Geraldine_VdAuwera

<p><strong>This article is out of date and has been replaced by <a href="https://software.broadinstitute.org/gatk/documentation/article?id=11813" rel="nofollow">https://software.broadinstitute.org/gatk/documentation/article?id=11813</a></strong></p>

<hr></hr><p>In GATK4, the <code class="code codeInline" spellcheck="false">GenotypeGVCFs</code> tool can only take a single input, so if you have GVCFs from multiple samples (which is usually the case) you will need to combine them before feeding them to <code class="code codeInline" spellcheck="false">GenotypeGVCFs</code>. Although there are several tools in the GATK and Picard toolkits that provide some type of VCF or GVCF merging functionality, for this use case there is only one valid way to do it: with <code class="code codeInline" spellcheck="false">GenomicsDBImport</code>.</p>

<p>The <code class="code codeInline" spellcheck="false">GenomicsDBImport</code> tool takes in one or more single-sample GVCFs and imports data over a single interval, and outputs a directory containing a GenomicsDB datastore with combined multi-sample data. <code class="code codeInline" spellcheck="false">GenotypeGVCFs</code> can then read from the created GenomicsDB directly and output a VCF. Note that GenomicsDBImport does not take two or more same sample GVCFs. You will need to create one GVCF per-sample before running the tool.</p>

<p>Here are example commands to use it:</p>

<pre class="code codeBlock" spellcheck="false">gatk-launch GenomicsDBImport \
    -V data/gvcfs/mother.g.vcf \
    -V data/gvcfs/father.g.vcf \
    -V data/gvcfs/son.g.vcf \
    --genomicsDBWorkspace my_database \
    --intervals 20
</pre>

<p>That generates a directory called <code class="code codeInline" spellcheck="false">my_database</code> containing the combined gvcf data.</p>

<p>Then you run joint genotyping; note the <code class="code codeInline" spellcheck="false">gendb://</code> prefix to the database input directory path.</p>

<pre class="code codeBlock" spellcheck="false">gatk-launch GenotypeGVCFs \
    -R data/ref/ref.fasta \
    -V gendb://my_database \
    -G StandardAnnotation -newQual \
    -O test_output.vcf 
</pre>

<p>And that's all there is to it.</p>

<p>There are three caveats:</p>

<ol><li><p>You can't add data to an existing database; you have to keep the original GVCFs around and reimport them all together when you get new samples. For very large numbers of samples, there are some batching options.</p></li>
<li><p>At the moment you can only run <code class="code codeInline" spellcheck="false">GenomicsDBImport</code> on a single genomic interval (ie max one contig) at a time. Down the road this will change (the work is tentatively scheduled for the second quarter of 2018), because we want to make it possible to run on one multiple intervals in one go. But for now you need to run on each interval separately. We recommend scripting this of course.</p></li>
<li><p>At the moment GenomicsDB only supports diploid data. The developers of GenomicsDB are working on implementing support for non-diploid data.</p></li>
</ol><p><strong>If you can't use GenomicsDB for whatever reason, fall back to CombineGVCFs instead.</strong> It is slower but will allow you to combine GVCFs the old-fashioned way.</p>

<hr></hr><h4>Addendum: extracting data from the GenomicsDB</h4>

<p>If you want to generate a flat multisample GVCF file from the GenomicsDB you created, you can do so with SelectVariants as follows:</p>

<pre class="code codeBlock" spellcheck="false">gatk-launch SelectVariants \
    -R data/ref/ref.fasta \
    -V gendb://my_database \
    -O combined.g.vcf
</pre>

<hr></hr><h4>Caveat: cannot move database after creation</h4>

<p>Currently the GenomicsDB internal code uses the absolute path of the location of the database as part of the data encoding. As a consequence, you cannot move the database to a different location before running GenotypeGVCFs on it. If you do, it will no longer work. This is obviously not desirable, and the development team is looking at options to remediate this.</p>

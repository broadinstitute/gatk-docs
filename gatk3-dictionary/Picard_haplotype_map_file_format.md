## Picard haplotype map file format

By shlee

<p>The <strong>haplotype map</strong> that certain Picard tools require is a file that maps SNPs to LD (linkage disequilibrium) blocks. These tools include Picard <a rel="nofollow" href="https://broadinstitute.github.io/picard/command-line-overview.html#CrosscheckReadGroupFingerprints">CrosscheckReadGroupFingerprints</a> and <a rel="nofollow" href="https://broadinstitute.github.io/picard/command-line-overview.html#CheckFingerprint">CheckFingerprint</a>. For these tools, the HAPLOTYPE_MAP parameter defines the file.</p>

<ul><li>For details on what the tools do and their parameters, see <a rel="nofollow" href="https://broadinstitute.github.io/picard/">https://broadinstitute.github.io/picard/</a>.</li>
<li>For an overview of fingerprinting math and comparative results for different data types, see the related poster. You can find a link to posters on <a rel="nofollow" href="https://software.broadinstitute.org/gatk/documentation/presentations.php">this page</a>.</li>
<li><p>To view the javadoc documentation for tools within the Picard Jar, type</p>

<pre class="code codeBlock" spellcheck="false">java -jar picard.jar &lt;tool name&gt; -h
</pre></li>
</ul><p>As of this writing (5/5/2017, Picard v2.9.0), the HAPLOTYPE_MAP file is a text-based file that tab-separates fields. In a <em>future release</em> of Picard, this field will also accept VCF formats ending in <code class="code codeInline" spellcheck="false">.vcf</code>, <code class="code codeInline" spellcheck="false">.vcf.gz</code> or <code class="code codeInline" spellcheck="false">.bcf</code>. At that time, tools will interpret all other file extensions for this parameter as the original text-based format.</p>

<p>These two formats differ in their requirements as we outline below.</p>

<hr></hr><h2>The original haplotype map file format</h2>

<p>It has a header and a body as shown.</p>

<p><a rel="nofollow" href="https://us.v-cdn.net/5019796/uploads/FileUpload/9b/d61db4713af62c25daa8da3da27e79.png"><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/9b/d61db4713af62c25daa8da3da27e79.png" alt="image" class="embedImage-img importedEmbed-img"></img></a></p>

<p>The header is a standard SAM header, with an <a href="https://gatkforums.broadinstitute.org/gatk/profile/HD" rel="nofollow">@HD</a> line to define the file type and <a href="https://gatkforums.broadinstitute.org/gatk/profile/SQ" rel="nofollow">@SQ</a> lines to define the reference contigs. You can easily derive such a header from your reference dictionary file.</p>

<p>The body contains a column header line starting with a <code class="code codeInline" spellcheck="false">#</code> hash followed by lines that annotate SNPs and blocks in high LD.</p>

<ul><li>NAME is a SNP identifier, e.g. dbSNP rsID</li>
<li>MAF is minor allele frequency</li>
<li>ANCHOR_SNP refers to the NAME of a SNP that groups SNPs in high LD with each other. The tool counts all of the SNPs with the same ANCHOR_SNP as one group.</li>
<li>Although the column header requires the PANELS label, the PANELS column field value is optional.</li>
</ul><p>Again, the SNPs listed with the same ANCHOR_SNP will be in the same haplotype. If there is a discrepancy between the MAFs within a block, the tool considers the MAF of the first SNP, i.e. that with the smallest genomic position, the MAF of the block. Again, MAF stands for <em>minor allele frequency</em>.</p>

<hr></hr><h2>The VCF-based haplotype map</h2>

<p>Picard v2.10.1+ (released 2017/7/11) accepts this format. Tools will recognize a VCF format if the file extension ends in <code class="code codeInline" spellcheck="false">.vcf</code>, <code class="code codeInline" spellcheck="false">.vcf.gz</code> or <code class="code codeInline" spellcheck="false">.bcf</code>. Tools will interpret all other file extensions fas the original text-based format we describe above.</p>

<p>Click <a rel="nofollow" href="https://us.v-cdn.net/5019796/uploads/FileUpload/75/d3d40553ba45102118a48d7de5f799.txt">here</a> to download an example file. Here is the body portion of this example file.</p>

<p><a rel="nofollow" href="https://us.v-cdn.net/5019796/uploads/FileUpload/d1/2d4a793b3e06d0dae4f853cb8c12be.png"><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/d1/2d4a793b3e06d0dae4f853cb8c12be.png" alt="image" class="embedImage-img importedEmbed-img"></img></a></p>

<ul><li>The VCF format haplotype map contains exactly one sample whose genotype calls are all heterozygous, e.g. <code class="code codeInline" spellcheck="false">0/1</code> or <code class="code codeInline" spellcheck="false">0|1</code>.</li>
<li>The tool determines haplotype block grouping using phased genotypes (with a pipe <code class="code codeInline" spellcheck="false">|</code>) and the PS (phase set) format field annotation.</li>
<li>The INFO field's AF annotation refers to the <em>alternate allele frequency</em>. This is not necessarily the minor allele frequency. This differs from the original haplotype map file format's requirement.</li>
</ul><p>Finally, the <a rel="nofollow" href="https://samtools.github.io/hts-specs/VCFv4.2.pdf">VCF specification (v4.2)</a> defines the PS field as follows.</p>

<blockquote class="UserQuote blockquote"><div class="blockquote-content">
  <p class="blockquote-line">PS : phase set. A phase set is defined as a set of phased genotypes to which this genotype belongs. Phased genotypes for an individual that are on the same chromosome and have the same PS value are in the same phased set. A phase set specifies multi-marker haplotypes for the phased genotypes in the set. All phased genotypes that do not contain a PS subfield are assumed to belong to the same phased set. If the genotype in the GT field is unphased, the corresponding PS field is ignored. The recommended convention is to use the position of the first variant in the set as the PS identifier (although this is not required). (Non-negative 32-bit Integer)</p>
</div></blockquote>

<hr></hr>
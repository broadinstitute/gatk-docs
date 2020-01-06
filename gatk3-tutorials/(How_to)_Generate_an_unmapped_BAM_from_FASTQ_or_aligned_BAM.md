## (How to) Generate an unmapped BAM from FASTQ or aligned BAM

By shlee

<p><a name="top" id="top"></a><br><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/31/992f952c8e9819d57bf74b0a4ac308.png" height="180" border="9" alt="image" style="float: right;" class="embedImage-img importedEmbed-img"></img> Here we outline how to generate an unmapped BAM (uBAM) from either a FASTQ or aligned BAM file. We use Picard's FastqToSam to convert a FASTQ (<strong>Option A</strong>) or Picard's RevertSam to convert an aligned BAM (<strong>Option B</strong>).</p>

<h4>Jump to a section on this page</h4>

<p>(A) <a rel="nofollow" href="#optionA">Convert FASTQ to uBAM</a> and add read group information using FastqToSam<br>
(B) <a rel="nofollow" href="#optionB">Convert aligned BAM to uBAM</a> and discard problematic records using RevertSam</p>

<h4>Tools involved</h4>

<ul><li><a rel="nofollow" href="https://broadinstitute.github.io/picard/command-line-overview.html#FastqToSam">FastqToSam</a></li>
<li><a rel="nofollow" href="https://broadinstitute.github.io/picard/command-line-overview.html#RevertSam">RevertSam</a></li>
</ul><h4>Prerequisites</h4>

<ul><li>Installed Picard tools</li>
</ul><h4>Download example data</h4>

<ul><li><a rel="nofollow" href="https://drive.google.com/open?id=0BzI1CyccGsZiUXVNT3hsNldvUFk">tutorial_6484_FastqToSam.tar.gz</a></li>
<li><a rel="nofollow" href="https://drive.google.com/open?id=0BzI1CyccGsZiMWZacmVWWnV2VFE">tutorial_6484_RevertSam.tar.gz</a></li>
</ul><p>Tutorial data reads were originally aligned to the <a rel="nofollow" href="http://gatkforums.broadinstitute.org/discussion/4610/">advanced tutorial bundle</a>'s human_g1k_v37_decoy.fasta reference and to 10:91,000,000-92,000,000.</p>

<h4>Related resources</h4>

<ul><li>Our <a rel="nofollow" href="http://gatkforums.broadinstitute.org/discussion/6472/read-groups#latest">dictionary entry on read groups</a> discusses the importance of assigning appropriate read group fields that differentiate samples and factors that contribute to batch effects, e.g. flow cell lane. Be sure your read group fields conform to the recommendations.</li>
<li><a rel="nofollow" href="http://gatkforums.broadinstitute.org/discussion/2909#addRG">This post</a> provides an example command for AddOrReplaceReadGroups.</li>
<li>This <em>How to</em> is part of a larger workflow and tutorial on <a rel="nofollow" href="http://gatkforums.broadinstitute.org/discussion/6483">(How to) Efficiently map and clean up short read sequence data</a>.</li>
<li>To extract reads in a genomic interval from the aligned BAM, <a rel="nofollow" href="http://gatkforums.broadinstitute.org/discussion/6517/">use GATK's PrintReads</a>.</li>
</ul><hr></hr><p><a name="optionA" id="optionA"></a></p>

<h3>(A)  Convert FASTQ to uBAM and add read group information using FastqToSam</h3>

<p>Picard's <a rel="nofollow" href="https://broadinstitute.github.io/picard/command-line-overview.html#FastqToSam">FastqToSam</a> transforms a FASTQ file to an unmapped BAM, requires two read group fields and makes optional specification of other read group fields. In the command below we note which fields are required for GATK Best Practices Workflows. All other read group fields are optional.</p>

<pre class="code codeBlock" spellcheck="false">java -Xmx8G -jar picard.jar FastqToSam \
    FASTQ=6484_snippet_1.fastq \ #first read file of pair
    FASTQ2=6484_snippet_2.fastq \ #second read file of pair
    OUTPUT=6484_snippet_fastqtosam.bam \
    READ_GROUP_NAME=H0164.2 \ #required; changed from default of A
    SAMPLE_NAME=NA12878 \ #required
    LIBRARY_NAME=Solexa-272222 \ #required 
    PLATFORM_UNIT=H0164ALXX140820.2 \ 
    PLATFORM=illumina \ #recommended
    SEQUENCING_CENTER=BI \ 
    RUN_DATE=2014-08-20T00:00:00-0400
</pre>

<p>Some details on select parameters:</p>

<ul><li>For paired reads, specify each FASTQ file with <code class="code codeInline" spellcheck="false">FASTQ</code> and <code class="code codeInline" spellcheck="false">FASTQ2</code> for the first read file and the second read file, respectively. Records in each file must be queryname sorted as the tool assumes identical ordering for pairs. The tool automatically strips the <code class="code codeInline" spellcheck="false">/1</code> and <code class="code codeInline" spellcheck="false">/2</code> read name suffixes and adds SAM flag values to indicate reads are paired. Do not provide a single interleaved fastq file, as the tool will assume reads are unpaired and the SAM flag values will reflect single ended reads.</li>
<li>For single ended reads, specify the input file with <code class="code codeInline" spellcheck="false">FASTQ</code>.</li>
<li><code class="code codeInline" spellcheck="false">QUALITY_FORMAT</code> is detected automatically if unspecified.</li>
<li><code class="code codeInline" spellcheck="false">SORT_ORDER</code> by default is queryname.</li>
<li><code class="code codeInline" spellcheck="false">PLATFORM_UNIT</code> is often in run_barcode.lane format. Include if sample is multiplexed.</li>
<li><code class="code codeInline" spellcheck="false">RUN_DATE</code> is in <a rel="nofollow" href="https://en.wikipedia.org/wiki/ISO_8601">Iso8601 date format</a>.</li>
</ul><p>Paired reads will have <a rel="nofollow" href="https://broadinstitute.github.io/picard/explain-flags.html">SAM flag</a> values that reflect pairing and the fact that the reads are unmapped as shown in the example read pair below.</p>

<p><strong>Original first read</strong></p>

<pre class="code codeBlock" spellcheck="false">@H0164ALXX140820:2:1101:10003:49022/1
ACTTTAGAAATTTACTTTTAAGGACTTTTGGTTATGCTGCAGATAAGAAATATTCTTTTTTTCTCCTATGTCAGTATCCCCCATTGAAATGACAATAACCTAATTATAAATAAGAATTAGGCTTTTTTTTGAACAGTTACTAGCCTATAGA
+
-FFFFFJJJJFFAFFJFJJFJJJFJFJFJJJ&lt;&lt;FJJJJFJFJFJJJJ&lt;JAJFJJFJJJJJFJJJAJJJJJJFFJFJFJJFJJFFJJJFJJJFJJFJJFJAJJJJAJFJJJJJFFJJ&lt;&lt;&lt;JFJJAFJAAJJJFFFFFJJJAJJJF&lt;AJFFFJ
</pre>

<p><strong>Original second read</strong></p>

<pre class="code codeBlock" spellcheck="false">@H0164ALXX140820:2:1101:10003:49022/2
TGAGGATCACTAGATGGGGGAGGGAGAGAAGAGATGTGGGCTGAAGAACCATCTGTTGGGTAATATGTTTACTGTCAGTGTGATGGAATAGCTGGGACCCCAAGCGTCAGTGTTACACAACTTACATCTGTTGATCGACTGTCTATGACAG
+
AA&lt;FFJJJAJFJFAFJJJJFAJJJJJ7FFJJ&lt;F-FJFJJJFJJFJJFJJF&lt;FJJA&lt;JF-AFJFAJFJJJJJAAAFJJJJJFJJF-FF&lt;7FJJJJJJ-JA&lt;&lt;J&lt;F7-&lt;FJFJJ7AJAF-AFFFJA--J-F######################
</pre>

<p><strong>After FastqToSam</strong></p>

<pre class="code codeBlock" spellcheck="false">H0164ALXX140820:2:1101:10003:49022      77      *       0       0       *       *       0       0       ACTTTAGAAATTTACTTTTAAGGACTTTTGGTTATGCTGCAGATAAGAAATATTCTTTTTTTCTCCTATGTCAGTATCCCCCATTGAAATGACAATAACCTAATTATAAATAAGAATTAGGCTTTTTTTTGAACAGTTACTAGCCTATAGA -FFFFFJJJJFFAFFJFJJFJJJFJFJFJJJ&lt;&lt;FJJJJFJFJFJJJJ&lt;JAJFJJFJJJJJFJJJAJJJJJJFFJFJFJJFJJFFJJJFJJJFJJFJJFJAJJJJAJFJJJJJFFJJ&lt;&lt;&lt;JFJJAFJAAJJJFFFFFJJJAJJJF&lt;AJFFFJ RG:Z:H0164.2
H0164ALXX140820:2:1101:10003:49022      141     *       0       0       *       *       0       0       TGAGGATCACTAGATGGGGGAGGGAGAGAAGAGATGTGGGCTGAAGAACCATCTGTTGGGTAATATGTTTACTGTCAGTGTGATGGAATAGCTGGGACCCCAAGCGTCAGTGTTACACAACTTACATCTGTTGATCGACTGTCTATGACAG AA&lt;FFJJJAJFJFAFJJJJFAJJJJJ7FFJJ&lt;F-FJFJJJFJJFJJFJJF&lt;FJJA&lt;JF-AFJFAJFJJJJJAAAFJJJJJFJJF-FF&lt;7FJJJJJJ-JA&lt;&lt;J&lt;F7-&lt;FJFJJ7AJAF-AFFFJA--J-F###################### RG:Z:H0164.2
</pre>

<p><a rel="nofollow" href="#top">back to top</a></p>

<hr></hr><p><a name="optionB" id="optionB"></a></p>

<h3>(B) Convert aligned BAM to uBAM and discard problematic records using RevertSam</h3>

<p>We use Picard's <a rel="nofollow" href="https://broadinstitute.github.io/picard/command-line-overview.html#RevertSam">RevertSam</a> to remove alignment information and generate an unmapped BAM (uBAM). For our tutorial file we have to call on some additional parameters that we explain below. This illustrates the need to cater the tool's parameters to each dataset. As such, it is a good idea to test the reversion process on a subset of reads before committing to reverting the entirety of a large BAM. Follow the directions in <a rel="nofollow" href="http://gatkforums.broadinstitute.org/discussion/6517/">this <em>How to</em></a> to create a snippet of aligned reads corresponding to a genomic interval.</p>

<p>We use the following parameters.</p>

<pre class="code codeBlock" spellcheck="false">java -Xmx8G -jar /path/picard.jar RevertSam \
    I=6484_snippet.bam \
    O=6484_snippet_revertsam.bam \
    SANITIZE=true \ 
    MAX_DISCARD_FRACTION=0.005 \ #informational; does not affect processing
    ATTRIBUTE_TO_CLEAR=XT \
    ATTRIBUTE_TO_CLEAR=XN \
    ATTRIBUTE_TO_CLEAR=AS \ #Picard release of 9/2015 clears AS by default
    ATTRIBUTE_TO_CLEAR=OC \
    ATTRIBUTE_TO_CLEAR=OP \
    SORT_ORDER=queryname \ #default
    RESTORE_ORIGINAL_QUALITIES=true \ #default
    REMOVE_DUPLICATE_INFORMATION=true \ #default
    REMOVE_ALIGNMENT_INFORMATION=true #default
</pre>

<p>To process large files, also designate a temporary directory.</p>

<pre class="code codeBlock" spellcheck="false">    TMP_DIR=/path/shlee #sets environmental variable for temporary directory
</pre>

<p><strong>We invoke or change multiple RevertSam parameters to generate an unmapped BAM</strong></p>

<ul><li><p>We remove nonstandard alignment tags with the <code class="code codeInline" spellcheck="false">ATTRIBUTE_TO_CLEAR</code> option. Standard tags cleared by default are NM, UQ, PG, MD, MQ, SA, MC, and AS tags (AS for Picard releases starting 9/2015). Additionally, the OQ tag is removed by the default <code class="code codeInline" spellcheck="false">RESTORE_ORIGINAL_QUALITIES</code> parameter. Remove all other nonstandard tags by specifying each with the <code class="code codeInline" spellcheck="false">ATTRIBUTE_TO_CLEAR</code> option. For example, we clear the <code class="code codeInline" spellcheck="false">XT</code> tag using this option for our tutorial file so that it is free for use by other tools, e.g. MarkIlluminaAdapters. To list all tags within a BAM, use the command below.</p>

<pre class="code codeBlock" spellcheck="false">samtools view input.bam | cut -f 12- | tr '\t' '\n' | cut -d ':' -f 1 | awk '{ if(!x[$1]++) { print }}' 
</pre>

<p>For the tutorial file, this gives RG, OC, XN, OP and XT tags as well as those removed by default. We remove all of these except the RG tag. See your aligner's documentation and the <a rel="nofollow" href="http://samtools.sourceforge.net/SAM1.pdf">Sequence Alignment/Map Format Specification</a> for descriptions of tags.</p></li>
<li><p>Additionally, we invoke the <code class="code codeInline" spellcheck="false">SANITIZE</code> option to remove reads that cause problems for certain tools, e.g. MarkIlluminaAdapters. Downstream tools will have problems with paired reads with missing mates, duplicated records, and records with mismatches in length of bases and qualities. Any paired reads file subset for a genomic interval requires sanitizing to remove reads with lost mates that align outside of the interval.</p></li>
<li><p>In this command, we've set <code class="code codeInline" spellcheck="false">MAX_DISCARD_FRACTION</code> to a more strict threshold of 0.005 instead of the default 0.01. Whether or not this fraction is reached, the tool informs you of the number and fraction of reads it discards. This parameter asks the tool to additionally inform you of the discarded fraction via an exception as it finishes processing.</p>

<pre class="code codeBlock" spellcheck="false">Exception in thread "main" picard.PicardException: Discarded 0.787% which is above MAX_DISCARD_FRACTION of 0.500%  
</pre></li>
</ul><p><strong>Some comments on options kept at default:</strong></p>

<ul><li><p><code class="code codeInline" spellcheck="false">SORT_ORDER</code>=queryname<br>
For paired read files, because each read in a pair has the same query name, sorting results in interleaved reads. This means that reads in a pair are listed consecutively within the same file. We make sure to alter the previous sort order. Coordinate sorted reads result in the aligner incorrectly estimating insert size from blocks of paired reads as they are not randomly distributed.</p></li>
<li><p><code class="code codeInline" spellcheck="false">RESTORE_ORIGINAL_QUALITIES</code>=true<br>
Restoring original base qualities to the QUAL field requires OQ tags listing original qualities. The OQ tag uses the same encoding as the QUAL field, e.g. ASCII Phred-scaled base quality+33 for tutorial data. After restoring the QUAL field, RevertSam removes the tag.</p></li>
<li><p><code class="code codeInline" spellcheck="false">REMOVE_ALIGNMENT_INFORMATION</code>=true will remove program group records and alignment flag and tag information. For example, <a rel="nofollow" href="https://broadinstitute.github.io/picard/explain-flags.html">flags</a> reset to unmapped values, e.g. 77 and 141 for paired reads. The parameter also invokes the default <code class="code codeInline" spellcheck="false">ATTRIBUTE_TO_CLEAR</code> parameter which removes standard alignment tags. RevertSam ignores <code class="code codeInline" spellcheck="false">ATTRIBUTE_TO_CLEAR</code> when <code class="code codeInline" spellcheck="false">REMOVE_ALIGNMENT_INFORMATION</code>=false.</p></li>
</ul><p>Below we show below a read pair before and after RevertSam from the tutorial data. Notice the first listed read in the pair becomes <strong>reverse-complemented</strong> after RevertSam. This restores how reads are represented when they come off the sequencer--5' to 3' of the read being sequenced.</p>

<p>For 6484_snippet.bam, <code class="code codeInline" spellcheck="false">SANITIZE</code> removes 2,202 out of 279,796 (0.787%) reads, leaving us with 277,594 reads.</p>

<p><strong>Original BAM</strong></p>

<pre class="code codeBlock" spellcheck="false">H0164ALXX140820:2:1101:10003:23460  83  10  91515318    60  151M    =   91515130    -339    CCCATCCCCTTCCCCTTCCCTTTCCCTTTCCCTTTTCTTTCCTCTTTTAAAGAGACAAGGTCTTGTTCTGTCACCCAGGCTGGAATGCAGTGGTGCAGTCATGGCTCACTGCCGCCTCAGACTTCAGGGCAAAAGCAATCTTTCCAGCTCA :&lt;&lt;=&gt;@AAB@AA@AA&gt;6@@A:&gt;,*@A@&lt;@??@8?9&gt;@==8?:?@?;?:&gt;&lt;??@&gt;==9?&gt;8&gt;@:?&gt;&gt;=&gt;;&lt;==&gt;&gt;;&gt;?=?&gt;&gt;=&lt;==&gt;&gt;=&gt;9&lt;=&gt;??&gt;?&gt;;8&gt;?&gt;&lt;?&lt;=:&gt;&gt;&gt;;4&gt;=&gt;7=6&gt;=&gt;&gt;=&gt;&lt;;=;&gt;===?=&gt;=&gt;&gt;?9&gt;&gt;&gt;&gt;??==== MC:Z:60M91S MD:Z:151    PG:Z:MarkDuplicates RG:Z:H0164.2    NM:i:0  MQ:i:0  OQ:Z:&lt;FJFFJJJJFJJJJJF7JJJ&lt;F--JJJFJJJJ&lt;J&lt;FJFF&lt;JAJJJAJAJFFJJJFJAFJAJJAJJJJJFJJJJJFJJFJJJJFJFJJJJFFJJJJJJJFAJJJFJFJFJJJFFJJJ&lt;J7JJJJFJ&lt;AFAJJJJJFJJJJJAJFJJAFFFFA    UQ:i:0  AS:i:151

H0164ALXX140820:2:1101:10003:23460  163 10  91515130    0   60M91S  =   91515318    339 TCTTTCCTTCCTTCCTTCCTTGCTCCCTCCCTCCCTCCTTTCCTTCCCCCCCCCCCCCCCCCTCCCCCCCCCCCCCCCCCTCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCTTCCCCTCTCCCACCCCTCTCTCCCCCCCTCCCACCC :0;.=;8?7==?794&lt;&lt;;:&gt;769=,&lt;;0:=&lt;0=:9===/,:-==29&gt;;,5,98=599;&lt;=########################################################################################### SA:Z:2,33141573,-,37S69M45S,0,1;    MC:Z:151M   MD:Z:48T4T6 PG:Z:MarkDuplicates RG:Z:H0164.2    NM:i:2  MQ:i:60 OQ:Z:&lt;-&lt;-FA&lt;F&lt;FJF&lt;A7AFAAJ&lt;&lt;AA-FF-AJF-FA&lt;AFF--A-FA7AJA-7-A&lt;F7&lt;&lt;AFF###########################################################################################    UQ:i:49 AS:i:50
</pre>

<p><strong>After RevertSam</strong></p>

<pre class="code codeBlock" spellcheck="false">H0164ALXX140820:2:1101:10003:23460  77  *   0   0   *   *   0   0   TGAGCTGGAAAGATTGCTTTTGCCCTGAAGTCTGAGGCGGCAGTGAGCCATGACTGCACCACTGCATTCCAGCCTGGGTGACAGAACAAGACCTTGTCTCTTTAAAAGAGGAAAGAAAAGGGAAAGGGAAAGGGAAGGGGAAGGGGATGGG AFFFFAJJFJAJJJJJFJJJJJAFA&lt;JFJJJJ7J&lt;JJJFFJJJFJFJFJJJAFJJJJJJJFFJJJJFJFJJJJFJJFJJJJJFJJJJJAJJAJFAJFJJJFFJAJAJJJAJ&lt;FFJF&lt;J&lt;JJJJFJJJ--F&lt;JJJ7FJJJJJFJJJJFFJF&lt; RG:Z:H0164.2

H0164ALXX140820:2:1101:10003:23460  141 *   0   0   *   *   0   0   TCTTTCCTTCCTTCCTTCCTTGCTCCCTCCCTCCCTCCTTTCCTTCCCCCCCCCCCCCCCCCTCCCCCCCCCCCCCCCCCTCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCTTCCCCTCTCCCACCCCTCTCTCCCCCCCTCCCACCC &lt;-&lt;-FA&lt;F&lt;FJF&lt;A7AFAAJ&lt;&lt;AA-FF-AJF-FA&lt;AFF--A-FA7AJA-7-A&lt;F7&lt;&lt;AFF########################################################################################### RG:Z:H0164.2
</pre>

<p><a rel="nofollow" href="#top">back to top</a></p>

<hr></hr>
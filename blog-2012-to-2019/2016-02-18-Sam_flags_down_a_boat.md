## Sam flags down a boat

By shlee

<p><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/3c/8c38d75b924b7056aaa648fca8c5fe.png" width="390" alt="image" style="float: right;" class="embedImage-img importedEmbed-img"></img>Let's talk about SAM flags. We'll breeze over some background (skipping details better left to CS majors), filter alignment records via their flag bits using <a rel="nofollow" href="http://www.htslib.org/doc/samtools.html">Samtools</a>, and end with how to create a valid BAM containing read pairs or sets where at least one record has the specified flag bit.</p>

<p>SAM flags summarize many properties of reads, represented by flag bits, into a single number. Think of each flag bit as one layer of watercolor paint on a canvas. Each SAM flag is then the distinct <a rel="nofollow" href="http://www.merriam-webster.com/dictionary/chroma">chroma</a> resulting from multiple layers of overlapping pigment.</p>

<hr></hr><p><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/73/895cc2a6fe17d08d4d66fd7bf64cf4.png" width="420" alt="image" style="float: left;" class="embedImage-img importedEmbed-img"></img></p>

<h3>Cats pPuUrR12; Sam flags down a boat</h3>

<p>That's the mnemonic I created to memorize the twelve characters in <code class="code codeInline" spellcheck="false">pPuUrR12sfdb</code>. Typically, I use the <a rel="nofollow" href="https://broadinstitute.github.io/picard/explain-flags.html">Explain flags</a> page on the Picard website to decipher SAM flag values. To do better, I made a summary chart and mnemonic for the twelve SAM flag bits. The chart's rows 1–8 refer to cats purring (<code class="code codeInline" spellcheck="false">pur1</code> and <code class="code codeInline" spellcheck="false">PUR2</code>), and rows 9–12 refer to the title of this blogpost <code class="code codeInline" spellcheck="false">sfdb</code>.</p>

<p>If you are an <a rel="nofollow" href="https://en.wikipedia.org/wiki/American_football">American football</a> fan and/or watched the Super Bowl last week, you may know the Carolina Panthers' mascot <a rel="nofollow" href="http://www.panthers.com/fanzone/mascot.html">Sir Purr</a>. Sir Purr's team lost 24:10 and gives us another mnemonic: <strong>Sir pPuUrR12 loses Superbowl Fifty to Denver Broncos</strong>.</p>

<hr></hr><p><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/47/007bc6d689950ea777b4f2e9809526.png" width="420" alt="image" style="float: right;" class="embedImage-img importedEmbed-img"></img></p>

<h3>Dive deeper into bits with this exercise</h3>

<p>If you're interested, this exercise uses Mac OS's <em>Calculator</em> app. Switch to <em>Calculator</em>'s <em>Programmer</em> mode and play around with the features (<strong>A–E</strong>) I've highlighted in the figure.</p>

<p>In the example in (<strong>F</strong>), SAM flag <code class="code codeInline" spellcheck="false">99</code> displays in binary as <code class="code codeInline" spellcheck="false">0000 0110 0011</code>. This is read right-to-left, where 0=no and 1=yes, giving us <em>yes</em> values for positions 1, 2, 6 and 7. Using our mnemonic, this translates to  <code class="code codeInline" spellcheck="false">pPR1</code> or paired, properly paired, mate reverse and read1.</p>

<p>If you forget what a particular bit represents, use the <code class="code codeInline" spellcheck="false">samtools flags</code> command as shown below.</p>

<pre class="code codeBlock" spellcheck="false">samtools flags 256
</pre>

<p>Replacing <code class="code codeInline" spellcheck="false">256</code> with <code class="code codeInline" spellcheck="false">SECONDARY</code> or <code class="code codeInline" spellcheck="false">0x100</code> returns the same answer.</p>

<pre class="code codeBlock" spellcheck="false">0x100   256 SECONDARY
</pre>

<hr></hr><h3>Filter reads by their flag bits using <a rel="nofollow" href="http://www.htslib.org/doc/samtools.html">Samtools</a></h3>

<p>The <code class="code codeInline" spellcheck="false">flagstat</code> option gives a useful summary count for the SAM flag categories.</p>

<pre class="code codeBlock" spellcheck="false">samtools flagstat xyz.bam 
</pre>

<p>The next set of commands either display actual records or return counts <code class="code codeInline" spellcheck="false">-c</code> of records. Depending on capitalization, <code class="code codeInline" spellcheck="false">-f</code> and <code class="code codeInline" spellcheck="false">-F</code> either include or exclude records with the specified flag bits, respectively. Listing two flag values, e.g. <code class="code codeInline" spellcheck="false">-F 2 -f 256</code>, returns records that satisfy both conditions, e.g. not proper pair <em>and</em> secondary.</p>

<pre class="code codeBlock" spellcheck="false">samtools view -f 0x63 xyz.bam | more #display records with all bits represented by 99 
samtools view -c -f 99 xyz.bam #count records with all bits represented by 99
samtools view -c -f 1 -f 2 -f 32 -f 64 xyz.bam #same as above
samtools view -c -F 2 xyz.bam #count records that do not have the 2 bit in flag
</pre>

<hr></hr><h3>Subset reads by flag bit into a valid BAM</h3>

<p>For transforming BAMs, I stick to using <a rel="nofollow" href="http://broadinstitute.github.io/picard/">Picard tools</a> for downstream GATK compatibility.</p>

<p>At one point I wanted to visualize in isolation only secondary alignments but in the context of their read sets. To create such a subsetted BAM, I used three commands. The first uses the <code class="code codeInline" spellcheck="false">256</code> SAM flag to extract the read names, the second sorts reads by queryname, and the third command uses <a rel="nofollow" href="http://broadinstitute.github.io/picard/command-line-overview.html#FilterSamReads">FilterSamReads</a> and the list of read names to generate a valid BAM containing our reads of interest.</p>

<pre class="code codeBlock" spellcheck="false"># Generate a list of unique read names of secondary alignments
samtools view -f 256 xyz.bam | cut -f1 | sort | uniq &gt; xyz_f256.txt 

# Queryname sort the input BAM
java -jar picard.jar SortSam INPUT=xyz.bam OUTPUT=xyz_querynamesort.bam SORT_ORDER=queryname

# Create a new BAM containing read sets
java -jar picard.jar FilterSamReads INPUT=xyz_querynamesort.bam OUTPUT=xyz_f256.bam \
FILTER=includeReadList READ_LIST_FILE=xyz_f256.txt SORT_ORDER=coordinate CREATE_INDEX=true TMP_DIR=/tmp
</pre>

<p>Since forward and reverse reads in a pair and multiple alignments for a given read are all identically named in the BAM, the list of read names pulls out the alignment set for a given read name. You can also set the <code class="code codeInline" spellcheck="false">FILTER</code> parameter to <code class="code codeInline" spellcheck="false">excludeReadList</code>.</p>

<hr></hr><p><em>Nautical flags at top spell SAM FLAGS. If I've gone overboard with sailing references, je m'excuse. I've had a great year racing <a rel="nofollow" href="https://en.wikipedia.org/wiki/Soling">solings</a>.</em></p>

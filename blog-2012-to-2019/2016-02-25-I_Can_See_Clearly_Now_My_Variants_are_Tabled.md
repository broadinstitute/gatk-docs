## I Can See Clearly Now My Variants are Tabled

By KateN

<p>The Variant Call Format, or VCF: an admirable effort to strike an appropriate balance between human readability and machine readability. Too bad it manages to fail in both aspects. For those of you who have ever run into a VCF file (and if you're following this blog, we're betting you have) you'll know that the tab-separated values don't always align perfectly. And that <code class="code codeInline" spellcheck="false">INFO</code> field? It's just a jumble of annotations! You shouldn't need to play pin-the-annotation-on-the-value every time you open a VCF. Similarly, trying to parse a VCF to collect annotations for each variant is a real pain, especially from the FORMAT fields.</p>

<p>Well, good news: GATK has a nifty tool called <a rel="nofollow" href="https://www.broadinstitute.org/gatk/guide/tooldocs/org_broadinstitute_gatk_tools_walkers_variantutils_VariantsToTable.php">VariantsToTable</a> that can export any information you want from a VCF to a handy table format! With all the extra time you'll save on trying to read or parse your VCF file, you can learn better party games than pin-the-annotation, like <a rel="nofollow" href="http://www.keeptalkinggame.com/">Keep Talking</a>.</p>

<hr></hr><p></p><div style="text-align: center;"><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/2b/84b1b759dafa5b251af9e61716f0c0.jpg" alt="image" class="embedImage-img importedEmbed-img"></img></div>

<p>To the left, we see your typical VCF read out. It's messy, and there are a lot of fields for each variant call. But just look at that table to the right! It's nicely aligned, and you can tell right away what the values are. Don't worry though, it's not that much work to get your messy VCFs to look this nice. The only thing you need to figure out is <em>which fields do you want to look at</em>. You can even look at all of them if you're not sure. Specify fields of interest with the <code class="code codeInline" spellcheck="false">-F</code> (for <code class="code codeInline" spellcheck="false">INFO</code> annotations and VCF column headers) or <code class="code codeInline" spellcheck="false">-GF</code> (for genotype fields like PL and GQ) inputs in the command line. When you open your VCF, you can browse through to see which annotations and fields are present in your files.</p>

<p>In my case, I want to compare the QUAL, the GQ (genotype quality), and DP (read depth) for my file. To keep track of what variant I'm looking at, I've included variant-identifying data (CHROM and POS) and then specified the 3 annotations I want included in the generated table.</p>

<pre class="code codeBlock" spellcheck="false"> java -jar GenomeAnalysisTK.jar \
     -R reference.fasta \
     -T VariantsToTable \
     -V file.vcf \
     -F CHROM -F POS -F QUAL -GF GQ  -F DP \
     -o results.table
</pre>

<p>You may also wish to add the <code class="code codeInline" spellcheck="false">--allowMissingData</code> argument to your command, if some of your variant records are missing values for any of the fields you want to display. This is particularly useful when not all of your variants are marked with the same annotations across the board.</p>

<p>"But wait," you say, "there are still variants missing in my table! I counted!" Fear not, those are just variants that failed a filter at some point along the way. By default, the tool ignores them (as do many other GATK tools). To export <em>all</em> the variants in your VCF (yes, even the no-good filter-failing ones), simply add <code class="code codeInline" spellcheck="false">--showFiltered</code> to your command line.</p>

<p>After generating your spiffy new table, you can open it in any number of programs. My program of choice is RStudio, where you can simply <strong>Import Dataset</strong> &gt; <strong>From Text File</strong> &gt; Check 'Yes' under Heading. However, you can also import <code class="code codeInline" spellcheck="false">.table</code> files into Matlab (<strong>Import Data</strong> &gt; Select File &gt; Select "Table" data type &gt; <strong>Import Selection</strong>) or Excel (<strong>File</strong> &gt; <strong>Open File</strong>). Once you have your data opened, there are all sorts of analyses you can do, ranging from generating distribution plots to comparing different sets of variants.<br><sub>* Please note, Matlab and Excel will not recognize table files by default, but they can open them</sub></p>

<p>Now go out and make some tables!</p>

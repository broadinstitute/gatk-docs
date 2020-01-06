## Ahoy mates, be ye unmapped?

By shlee

<p><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/ae/f269ec958cc661f0f5595711924b75.gif" height="200" border="9" alt="image" style="float: right;" class="embedImage-img importedEmbed-img"></img> If you follow along the recently published <a rel="nofollow" href="https://www.broadinstitute.org/gatk/guide/article?id=6483">Tutorial#6483</a> on mapping and cleaning short reads, you find a passage noting two types of mate-unmapped records. We've now defined these more officially in our <a rel="nofollow" href="https://www.broadinstitute.org/gatk/guide/article?id=6976">Dictionary</a>.</p>

<p>Unofficially, I've been calling these two classes of mate-unmapped sets <strong>wendy &amp; peterpan</strong> and <strong>divorced</strong> to further distinguish them from <a rel="nofollow" href="http://www.htslib.org/doc/samtools.html">orphan reads</a>. If we are going to anthropomorphize read records, then I say it's open seas and to each their own ship.</p>

<hr></hr><h3>Wendy &amp; Peterpan</h3>

<p><strong>The original mate-unmapped records: mapped reads with mates that are indeed unmapped.</strong> These arise from one-end anchored inserts. The unmapped mates in these sets were marked as such at alignment. They were lost to begin with just like the Lost Boys of <a rel="nofollow" href="https://en.wikipedia.org/wiki/Peter_Pan">Peter Pan</a> and exist in an alternate plane in how they populate the BAM file. If you look at these records, the alignment columns 2 and 3 indicate they align, in fact identically to the mapped mate. However, they have a MAPQ of zero and a Tinkerbell-esque asterisk <code class="code codeInline" spellcheck="false">*</code> in the CIGAR field that together indicates the record is unmapped. In sorted BAMs, these <strong>peterpan</strong> records sort alongside their mapped mates, or <strong>wendys</strong>. This has the intended effect of keeping the pairs together through the thick and thin of file manipulation.</p>

<p> </p>

<h3>Divorced</h3>

<p><strong>Mapped reads marked as mate-unmapped records whose mates are actually mapped.</strong> This second type of mate unmapped records have to do with multimapping read sets. In our pipeline, reads aligned using BWA-MEM's <code class="code codeInline" spellcheck="false">-M</code> option are passed through MergeBamAlignment, which officiates the creation of divorced reads: MergeBamAlignment marks secondary records as <em>mate-unmapped</em>. This effectively minimizes the association between secondary records from their previous mates, much like in a divorce.</p>

<hr></hr><h3>How do tools treat them differently?</h3>

<p>Let's take duplicate marking (published in <a rel="nofollow" href="https://www.broadinstitute.org/gatk/guide/article?id=6747">Tutorial#6747</a>) as an example. Because consideration for duplicate status requires mapping, duplicate marking tools altogether ignore peterpans. These tools also skip divorced records from consideration in that they are secondary records. However, duplicate marking tools consider wendys as single ended reads, and the tools will mark them if duplicate.</p>

<p><em>Peterpan clipart is from <a rel="nofollow" href="http://www.disneyclips.com/imagesnewb/peterpan4.html">http://www.disneyclips.com/imagesnewb/peterpan4.html</a>.</em></p>

## Intervals and interval lists

By Geraldine_VdAuwera

<p>Many of our workflow recommendations and example commands involve intervals or lists of intervals, which you can specify in your command line using <code class="code codeInline" spellcheck="false">-L</code> (or <code class="code codeInline" spellcheck="false">-XL</code> to <em>exclude</em> specific intervals).</p>

<p>So where do those intervals come from? It depends a lot on what you're working with (everyone's least favorite answer, I know). The most important distinction is the sequencing experiment type: is it whole genome, or targeted sequencing of some sort?</p>

<hr></hr><h3>Targeted sequencing (exomes etc.)</h3>

<p>For exomes and similarly targeted data types, the interval list should correspond to the capture targets used for the library prep, and is typically provided by the prep kit manufacturer (with versions for each ref genome build of course).</p>

<p>We make our exome  interval lists available, but be aware that they are specific to the custom exome targeting kits used at the Broad. If you got your sequencing done somewhere else, you should seek to get the appropriate intervals list from the sequencing provider.</p>

<hr></hr><h3>Whole genomes (WGS)</h3>

<p>For whole genome sequence, the intervals lists don’t depend on the prep (since in principle you captured the “whole genome”) so instead it depends on what regions of the genome you want to blacklist (eg centromeric regions that waste your time for nothing) and how the reference genome build enables you to cut up regions (separated by Ns) for scatter-gather parallelizing.</p>

<p>We make our WGS interval lists available, and the good news is that you can use them with your own data even if it comes from somewhere else -- assuming you agree with our decisions about which regions to blacklist! Which you can examine by looking at the intervals themselves (we don't currently have documentation on their provenance, sorry -- baby steps).</p>

<hr></hr><h3>Further reading</h3>

<p>For more information, see the <a rel="nofollow" href="https://software.broadinstitute.org/gatk/documentation/article?id=4133">FAQ article on using interval lists</a>.</p>

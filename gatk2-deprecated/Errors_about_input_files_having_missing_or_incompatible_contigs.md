## Errors about input files having missing or incompatible contigs

By GATK_Team

<p><strong>This doc has been replaced by <a href="https://software.broadinstitute.org/gatk/documentation/article?id=11012" rel="nofollow">https://software.broadinstitute.org/gatk/documentation/article?id=11012</a></strong></p>

<hr></hr><p>These errors occur when the names or sizes of contigs don't match between input files. This is a classic problem that typically happens when you get some files from collaborators, you try to use them with your own data, and GATK fails with a big fat error saying that the contigs don't match.</p>

<p>The first thing you need to do is find out which files are mismatched, because that will affect how you can fix the problem. This information is included in the error message, as shown in the examples below. You'll notice that GATK  always evaluates everything relative to the reference if one is provided.</p>

<hr></hr><h4>Contents</h4>

<ol><li>BAM file contigs not matching the reference</li>
<li>VCF file contigs not matching the reference</li>
</ol><hr></hr><h2>1. BAM file contigs not matching the reference</h2>

<p>A very common case we see looks like this:</p>

<pre class="code codeBlock" spellcheck="false">##### ERROR MESSAGE: Input files reads and reference have incompatible contigs: Found contigs with the same name but different lengths:
##### ERROR   contig reads = chrM / 16569
##### ERROR   contig reference = chrM / 16571.
##### ERROR   reads contigs = [chr1, chr2, chr3, chr4, chr5, chr6, chr7, chr8, chr9, chr10, chr11, chr12, chr13, chr14, chr15, chr16, chr17, chr18, chr19, chr20, chr21, chr22, chrX, chrY, chrM]
##### ERROR   reference contigs = [chrM, chr1, chr2, chr3, chr4, chr5, chr6, chr7, chr8, chr9, chr10, chr11, chr12, chr13, chr14, chr15, chr16, chr17, chr18, chr19, chr20, chr21, chr22, chrX, chrY, chr1_gl000191_random, chr1_gl000192_random, chr4_ctg9_hap1, chr4_gl000193_random, chr4_gl000194_random, chr6_apd_hap1, chr6_cox_hap2, chr6_dbb_hap3, chr6_mann_hap4, chr6_mcf_hap5, chr6_qbl_hap6, chr6_ssto_hap7, chr7_gl000195_random, chr8_gl000196_random, chr8_gl000197_random, chr9_gl000198_random, chr9_gl000199_random, chr9_gl000200_random, chr9_gl000201_random, chr11_gl000202_random, chr17_ctg5_hap1, chr17_gl000203_random, chr17_gl000204_random, chr17_gl000205_random, chr17_gl000206_random, chr18_gl000207_random, chr19_gl000208_random, chr19_gl000209_random, chr21_gl000210_random, chrUn_gl000211, chrUn_gl000212, chrUn_gl000213, chrUn_gl000214, chrUn_gl000215, chrUn_gl000216, chrUn_gl000217, chrUn_gl000218, chrUn_gl000219, chrUn_gl000220, chrUn_gl000221, chrUn_gl000222, chrUn_gl000223, chrUn_gl000224, chrUn_gl000225, chrUn_gl000226, chrUn_gl000227, chrUn_gl000228, chrUn_gl000229, chrUn_gl000230, chrUn_gl000231, chrUn_gl000232, chrUn_gl000233, chrUn_gl000234, chrUn_gl000235, chrUn_gl000236, chrUn_gl000237, chrUn_gl000238, chrUn_gl000239, chrUn_gl000240, chrUn_gl000241, chrUn_gl000242, chrUn_gl000243, chrUn_gl000244, chrUn_gl000245, chrUn_gl000246, chrUn_gl000247, chrUn_gl000248, chrUn_gl000249]
</pre>

<p>First, the error tells us that the mismatch is between the file containing <strong>reads</strong>, i.e. our BAM file, and the reference:</p>

<pre class="code codeBlock" spellcheck="false">Input files reads and reference have incompatible contigs
</pre>

<p>It further tells us that the contig length doesn't match for the chrM contig:</p>

<pre class="code codeBlock" spellcheck="false">Found contigs with the same name but different lengths:
##### ERROR   contig reads = chrM / 16569
##### ERROR   contig reference = chrM / 16571.
</pre>

<p>This can be caused either by using the wrong genome build version entirely, or using a reference that was hacked from a build that's very close but not identical, like b37 vs hg19, as detailed a bit more below.</p>

<p>We sometimes also see cases where people are using a very different reference; this is especially the case for non-model organisms where there is not yet a widely-accepted standard genome reference build.</p>

<p>Note that the error message also lists the content of the sequence dictionaries that it found for each file, and we see that some contigs in our reference dictionary are not listed in the BAM dictionary, but that's not a problem. If it was the opposite, with extra contigs in the BAM (or VCF), then GATK wouldn't know what to do with the reads from these extra contigs and would error out (even if we try restricting analysis using <code class="code codeInline" spellcheck="false">-L</code>) with something like this:</p>

<pre class="code codeBlock" spellcheck="false">#### ERROR MESSAGE: BAM file(s) do not have the contig: chrM. You are probably using a different reference than the one this file was aligned with.
</pre>

<h3>Solution</h3>

<p>If you can, simply switch to the correct reference. Note that file names may be misleading, as people will sometimes rename files willy-nilly. Sometimes you'll need to do some detective work to identify the correct reference if you inherited someone else's sequence data.</p>

<p>If that's not an option because you either can't find the correct reference or you absolutely MUST use a particular reference build, then you will need to redo the alignment altogether. Sadly there is no liftover procedure for reads. If you don't have access to the original unaligned sequence files, you can use Picard tools to revert your BAM file back to an unaligned state (either unaligned BAM or FASTQ depending on the workflow you wish to follow).</p>

<h4>Special case of b37 vs. hg19</h4>

<p>The b37 and hg19 human genome builds are very similar, and the canonical chromosomes (1 through 22, X and Y) only differ by their names (no prefix vs. chr prefix, respectively). If you only care about those, and don't give a flying fig about the decoys or the mitochondrial genome, you could just rename the contigs throughout your mismatching file and call it done, right?</p>

<p>Well... This can work if you do it carefully and cleanly -- but many things can go wrong during the editing process that can screw up your files even more, and it only applies to the canonical chromosomes. The mitochondrial contig is a slightly different length (see error above) in addition to having a different naming convention, and all the other contigs (decoys, herpes virus etc) don't have direct equivalents.</p>

<p>So only try that if you know what you're doing. YMMV.</p>

<hr></hr><h2>2. VCF file contigs not matching the reference</h2>

<pre class="code codeBlock" spellcheck="false">ERROR MESSAGE: Input files known and reference have incompatible contigs: Found contigs with the same name but different lengths:
ERROR contig known = chrM / 16569
ERROR contig reference = chrM / 16571.
</pre>

<p>Yep, it's just like the error we had with the BAM file above. Looks like we're using the wrong genome build again and a contig length doesn't match. But this time the error tells us that the mismatch is between the file identified as <strong>known</strong> and the reference:</p>

<pre class="code codeBlock" spellcheck="false">Input files known and reference have incompatible contigs
</pre>

<p>In this case the error was output by a tool that takes a VCF file of known variants provided through the <code class="code codeInline" spellcheck="false">known</code> argument, so this makes sense and tells us which file is at fault. Depending on the tool, the way the file is identified may vary, but the logic should be fairly obvious.</p>

<h3>Solution</h3>

<p>If you can, you find a version of the VCF file that is derived from the right reference. If you're working with human data and the VCF in question is just a common resource like dbsnp, you're in luck -- we provide versions of dbsnp and similar resources derived from the major human reference builds in our <a rel="nofollow" href="https://software.broadinstitute.org/gatk/documentation/article?id=11017">resource bundle</a>.</p>

<p>If that's not an option, then you'll have to "liftover" -- specifically, liftover the mismatching VCF to the reference you need to work with. The best tool for liftover is Picard's <a rel="nofollow" href="https://broadinstitute.github.io/picard/command-line-overview.html#LiftoverVcf">LiftoverVCF</a>.</p>

<p>We provide several chain files to liftover between the major human reference builds, also in our <a rel="nofollow" href="https://software.broadinstitute.org/gatk/documentation/article?id=11017">resource bundle</a> in the <code class="code codeInline" spellcheck="false">Liftover_Chain_Files</code> directory. If you are working with non-human organisms, we can't help you -- but others may have chain files, so ask around in your field.</p>

## The GATK reference model pipeline for incremental joint discovery, in full detail

By Geraldine_VdAuwera

<p>Okay, we realize the name's a bit of a mouthful, and we're willing to tweak it if anyone has any good ideas. But never mind that. It's difficult to overstate the importance of this new approach to joint variant discovery (but I'll give it a shot) so we're really stoked to finally be able to share the details of how it's is going to work in practice.</p>

<p>You're probably going to be surprised at how simple it is in practice (not that it was particularly easy to actually build, mind you). The gory details are in <a rel="nofollow" href="http://www.broadinstitute.org/gatk/guide/article?id=3893">the new document here</a>, but here's an overview of how it looks within the Best Practices workflow you all know and (hopefully) love:</p>

<p><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/cd/c16b93f652d57c8986a538dd72e4d9.png" alt="image" class="embedImage-img importedEmbed-img"></img></p>

<hr></hr><p>The first surprise is that instead of calling variants on multiple samples, you now just run HaplotypeCaller on each sample individually. "Oh no," I hear you cry, "but the results were so much better when I called multiple samples together!". Well yeah, but it took forever. Bear with me for a minute.</p>

<p>The key here is that you run HaplotypeCaller <em>in gVCF mode</em>. This outputs a so-called <a rel="nofollow" href="http://www.broadinstitute.org/gatk/guide/article?id=2940">genomic VCF</a>, which contains a record of the genotype likelihoods and annotations for every single site in the genome (or exome), whether or not there is evidence of variation. This essentially boils down all the useful information that can be gleaned from the BAM files, and makes it unnecessary to refer back to the BAM in later steps.</p>

<p>So you repeat that for all your samples (which goes reasonably fast since per-sample calling is pretty tractable nowadays). Optionally, you can add in a step to combine gVCF files if you're working on a really large cohort. Then in the next step, you just run a separate genotyping tool on all the gVCFs (or combined gVCFs) together, which gives you the same output (raw SNPs and indel calls) that you would have got from one-step multisample calling.</p>

<p>See, that's the beauty of the new workflow. A lot less work (for the computer) for equivalent results. And the ability to process samples incrementally and perform joint discovery on cohort sizes that would have previously got you hauled off to the funny farm.</p>

<p>Let us know what you think!</p>

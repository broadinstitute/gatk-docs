## Short Variant Caller Roundup

By gauthier

<h2>Short Variant Callers Roundup: Two Sisters</h2>

<p><strong><em>Authors of the blog: Laura Gauthier and David Benjamin</em></strong></p>

<p>I like to think of Mutect2 and HaplotypeCaller as two sisters. One is a little older, but they live in the same house and share a fair amount of genetic material. One is gregarious and surrounds herself with friends. The other prefers the intimate company of a few special people. But they both have a formidable intellect, a determined work ethic, and a selfless instinct to help.<br>
Mutect2 and HaplotypeCaller both follow the same basic recipe: determine the active region, assemble the haplotypes, evaluate per-read allele likelihoods, and calculate variant likelihoods. Because their code is fundamentally alike for the first three steps, many changes in one tool are likely to impact the other. Recently Mutect2 development has been much more active than HaplotypeCaller development, but both tools are able to benefit. In his blog post (see <a href="https://software.broadinstitute.org/gatk/blog?id=23400" rel="nofollow">https://software.broadinstitute.org/gatk/blog?id=23400</a>), David Benjamin outlined the new Mutect2 features in more detail. Below you’ll see how many of them have consequences for HaplotypeCaller as well. <br><img src="https://us.v-cdn.net/5019796/uploads/editor/ei/czkikj0hu1ch.png" alt="" title="" class="embedImage-img importedEmbed-img"></img></p>

<hr></hr><h3>Improved active region logic: more accurate filtering</h3>

<p>Previous algorithms did not count bases that were hardclipped during local assembly because they fell outside the active region. Overfiltering variants at the ends of reads made Mutect2 less sensitive, and penalized a number of germline variants as well. We’ve improved the active region logic by clarifying how the position of a variant within a read is measured, an update that benefits Mutect2 via the ReadPosition annotation, and HaplotypeCaller via ReadPosRankSum. Mutect2 has already demonstrated improved sensitivity and the current implementation should improve filtering accuracy in germline cases like these as well.</p>

<h3>Improved graph assembly: better accuracy at high depth</h3>

<p>Inspired by Mutect2 work on mitochondrial data, David improved the tendency to assemble low quality haplotypes by introducing “adaptive pruning” into the assembly of candidate haplotypes (see <a href="https://github.com/broadinstitute/gatk/docs/local_assembly.pdf" rel="nofollow">https://github.com/broadinstitute/gatk/docs/local_assembly.pdf</a> for more information about the local assembly used). Mitochondrial data typically have very high depth (thousands of reads per base), and many positions will contain at least three matching sequencing errors by chance. Because the standard threshold for removing errors from the graph is two reads, the result is many more “variants” in the graph. <br>
The new solution uses local coverage to determine the evidence for “pruning” likely erroneous paths from the graph. This improved the sensitivity for low allele fraction variants in mitochondrial genome data and has proven critical for mitochondrial exome analysis. This feature is off by default for HaplotypeCaller, but can be turned on in high depth regions with dense variants. Samples with ploidy higher than two or pooled experiments should benefit greatly as well.</p>

<h3>Improved per-read likelihoods: better local assembly visualization</h3>

<p>Read visualization - based on per-read likelihood calculations - was improved in both tools by David’s change in how “uninformative” reads are handled. The likelihoods calculated by the PairHMM algorithm favor deletions because of a slight bias towards shorter haplotypes. If reads could be explained by either a deletion or a SNP, the shorter allele will have a slightly higher likelihood, introducing a deletion into a read in a repetitive reference context when the more parsimonious representation would be a SNP. The result: read realignments from the bamout mode showing spurious deletions in some cases. GATK 4.1’s version prefers the alignment with the simpler CIGAR, which improves the bamout visualization for both tools, most noticeably in regions with STRs.</p>

<p><img src="https://us.v-cdn.net/5019796/uploads/editor/60/qtkfxs4bwrz7.png" alt="" title="" class="embedImage-img importedEmbed-img"></img></p>

<p>Caption: top view is GATKv4.0, bottom is GATKv4.1: the read with the 5bp deletion in the top becomes the read with the low quality G on the left in the bottom.</p>

<h3>Improved genotyping: GVCF mode in beta for somatic and germline mitochondria research</h3>

<p>The GATK 4.1.0.0 release introduces a beta version of GVCF mode for Mutect2 designed for mitochondrial variant calling -- a use case at the interface between germline and somatic research. This feature leverages the tried and true reference confidence model used by HaplotypeCaller. Future updates and fixes to that code will benefit both callers now that Mutect2 and HaplotypeCaller are sharing code for non-reference likelihood calculation and GVCF generation.</p>

<h3>Sum it up: common code and many common benefits</h3>

<p>While probably only a minority of GATK users run both HaplotypeCaller and Mutect2 in the course of their work, because of the two tools’ shared “ancestry”, improvements in either tool are able to benefit even more users. In the coming months we’re returning to development on the newest addition to the short variant calling family -- the Spark version of HaplotypeCaller. Initial optimization efforts have already yielded improvements by removing bottlenecks in shared code. We expect all users to see improvements in HaplotypeCaller (and likely Mutect2) performance, whether running the Spark version or not.</p>

## Why do joint calling rather than single-sample calling? [RETIRED]

By Geraldine_VdAuwera

<h3>Since the release of GATK 3.0, multisample calling has been replaced by the reference model (gVCF-based) workflow for joint analysis in our Best Practices recommendations. This new workflow provides all the benefits of the joint calling strategy detailed below, without any of the drawbacks. See <a rel="nofollow" href="http://www.broadinstitute.org/gatk/guide/article?id=4150">this FAQ article</a> and <a rel="nofollow" href="http://www.broadinstitute.org/gatk/guide/article?id=3893">this method document</a> for more details.</h3>

<h3>Overview</h3>

<p>There are three potential strategies for calling genetic variants on multiple samples:</p>

<ul><li>single sample calling: samples are analyzed individually</li>
<li>batch calling: samples are analyzed in separate batches, with call sets being merged in a downstream processing step</li>
<li>joint calling: variants are analyzed simultaneously across all samples</li>
</ul><h3>Our recommendation: joint calling</h3>

<p>We recommend joint calling because it can dramatically improve consistency across batches and cause fewer artefacts due to three key advantages:</p>

<h4>1. Clearer distinction between homozygous reference sites and sites with missing data</h4>

<p>Batch-calling does not output a genotype call at sites where no member in the batch has evidence for a variant; it is thus impossible to distinguish such sites from locations missing data. In contrast, joint calling emits genotype calls at every site where any individual in the call set has evidence for variation.</p>

<h4>2. Greater sensitivity for low-frequency variants</h4>

<p>By sharing information across all samples, joint calling makes it possible to “rescue” genotype calls at sites where a carrier has low coverage but other samples within the call set have a confident variant at that location.</p>

<h4>3. Greater ability to filter out false positives</h4>

<p>The current approaches to variant filtering (such as VQSR) use statistical models that work better with large amounts of data. Of the three calling strategies, only joint calling provides enough data for accurate error modeling and ensures that filtering is applied uniformly across all samples.</p>

<p><a rel="nofollow" href="https://us.v-cdn.net/5019796/uploads/FileUpload/40/3d322e97441f1918626854d56c2574.png"><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/40/3d322e97441f1918626854d56c2574.png" alt="image" class="embedImage-img importedEmbed-img"></img></a></p>

<p>Figure 1: Power of joint calling in finding mutations at low coverage sites. The variant allele is present in only two of the N samples, in both cases with such low coverage that the variant is not callable when processed separately. Joint calling allows evidence to be accumulated over all samples and renders the variant callable. (right) Importance of joint calling to square off the genotype matrix, using an example of two disease-relevant variants. Neither sample will have records in a variants-only output file, for different reasons: the first sample is homozygous reference while the second sample has no data. However, merging the results from single sample calling will incorrectly treat both of these samples identically as being non-informative.</p>

<h3>Some numbers and lessons from a large-scale joint calling project</h3>

<p>We recently participated in a large-scale project in which we applied joint calling approaches to raw sequencing data from approximately 57,000 human exomes representing a wide range of human population diversity. This was done in collaboration with other groups studying the genetic basis of complex and Mendelian diseases.</p>

<p>We performed two pilot studies as part of this project, one focused on sample QC and one focused specifically on evaluating the joint calling approach. In that pilot, we performed complete joint calling across chromosomes 11, 20, 21 and 22, which represents approximately 11.4% of the human exome. In a nutshell, we found that large-scale joint calling results in greater sensitivity to low-frequency variants, an increased ability to remove systematic false positives such as mapping errors, and greater consistency of variant calls across projects (Figure 2).</p>

<p><a rel="nofollow" href="https://us.v-cdn.net/5019796/uploads/FileUpload/5e/3d41cee593dd50c9bb35d56ee4f2e2.png"><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/5e/3d41cee593dd50c9bb35d56ee4f2e2.png" alt="image" class="embedImage-img importedEmbed-img"></img></a></p>

<p>So, should you call your samples jointly? Yes! But there are a few issues you should be aware of before you start.</p>

<h3>Outstanding issues with joint calling</h3>

<h4>- Scaling &amp; infrastructure</h4>

<p>Most of the problems we experienced in our joint calling experiments were scaling problems -- we managed to do joint analysis on 50K+ exomes, but that was already pushing the bounds of what our fairly heavyweight infrastructure can support. Anyone with less hardware is going to struggle to reach those numbers. Not to mention the logistical headache of managing access to the data, if it originates from multiple separate projects. But this only really applies to people who are dealing with seriously large projects, involving tens of thousands of samples.</p>

<h4>- The “N+1” problem</h4>

<p>This one is probably more widely applicable. When you’re getting a large-ish number of samples sequenced (especially clinical samples), you typically get them in small batches over an extended period of time, and you analyze each batch as it comes in (whether it’s because the analysis is time-sensitive or your PI is breathing down your back). But that’s not joint calling, that’s batch calling, and it doesn’t give you the same significant gains that joint calling can give you. Unfortunately the current joint calling approaches don’t allow for incremental analysis -- every time you get even one new sample sequence, you have to re-call all samples from scratch.</p>

<p>The good news is that we’re working on a new joint calling procedure that will address these issues (Figure 3) , so stay tuned for updates!</p>

<p><a rel="nofollow" href="https://us.v-cdn.net/5019796/uploads/FileUpload/e1/6518b7f7be6327c70a5e09c246716b.png"><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/e1/6518b7f7be6327c70a5e09c246716b.png" alt="image" class="embedImage-img importedEmbed-img"></img></a></p>

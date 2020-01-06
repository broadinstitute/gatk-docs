## Copy Number Variation pipelines out of beta!

By slee

<h3>Production-ready tools to call copy-number variants</h3>

<p>Early adopters of GATK4 will recall that somatic and germline copy-number variation (CNV) pipelines were among the first to be developed. The current generation of these pipelines still bear traits that reflect their evolutionary beginnings, but  have also acquired adaptations that take them far beyond their predecessors’ limitations. With the release of GATK4.1, we are excited to bring the latest and greatest versions of these pipelines out of beta and to officially add CNV calling to GATK’s ever-growing set of capabilities.</p>

<p><strong>Evolution of the CNV pipelines</strong><br>
Beta versions of the GATK CNV pipelines were heavily influenced by methods previously developed at the Broad. For example, the GATK4(beta) CNV/AllelicCNV pipeline bore strong resemblances to the exome <a rel="nofollow" href="https://gatkforums.broadinstitute.org/gatk/discussion/5640/recapseg-overview#latest" title="ReCapSeg">ReCapSeg</a>/AllelicCapSeg pipeline developed by the Cancer Genome Analysis Group. The germline GATK4(beta) XHMMSegmentCaller pipeline was a near-direct port of the <a rel="nofollow" href="https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4065038/" title="XHMM">XHMM</a> (eXome-Hidden Markov Model) tool. Vestiges of these venerable ancestors still remain in GATK4.1’s ModelSegments and GermlineCNVCaller pipelines; however, new innovations yield dramatically improved performance and enable scalability from exomes to genomes.</p>

<hr></hr><p><strong>CNV calling in a nutshell</strong><br>
To appreciate these innovations, let’s review the problem of calling CNVs from sequencing read-depth data---which can be a tough nut to crack! Like Darwin’s finches, different CNV tools have evolved a variety of different ways to crack this nut, but their overall function is largely the same. CNV tools typically break down the problem into more manageable tasks:</p>

<ol><li><p><strong>Denoising</strong>:  Distinguishing the signal from CNV events from systematic sequencing noise can be quite a challenge. Many CNV tools employ denoising strategies to learn patterns of noise from a panel of control samples and remove them. For example, both ReCapSeg and XHMM use principal components analysis (PCA) denoising.</p></li>
<li><p><strong>Segmentation</strong>:  The signal from CNV events can vary both in genomic length and amplitude. Algorithms like the circular binary segmentation (CBS) method used by ReCapSeg can identify genomic segments that contain somatic CNV signal. For germline calling, where the signal appears at amplitudes corresponding to integer copy-number states, a Hidden Markov Model (HMM) like the one used by XHMM can work well.</p></li>
</ol><p><strong>GATK4.1 ModelSegments: A next-generation CNV caller</strong><br>
GATK4.1’s ModelSegments pipeline is a streamlined, modernized, and highly evolved version of the ReCapSeg pipeline from which it descended. Like its ancestor, the ModelSegments pipeline uses PCA denoising and a panel of control samples to remove systematic sequencing noise. However, we’ve optimized our denoising code to drastically reduce both runtime and memory requirements. Panels that used to take upwards of an entire day to build using ReCapSeg can now be built in under an hour---and at ~100x higher resolution, to boot!</p>

<p>We’ve also developed a new kernel-segmentation method to replace the workhorse algorithm CBS. This method enables scaling to high-resolution whole genome data as well as segmentation of multidimensional data. Combined with the improvements to denoising, the new segmentation method allows ModelSegments to run well on both exomes and genomes.</p>

<p><strong>GATK4.1 GermlineCNVCaller: A new species of CNV caller</strong><br>
GATK4.1’s GermlineCNVCaller pipeline introduces even more novel methods---representing a saltational step in the evolution of CNV tools.</p>

<p>Taking advantage of computational frameworks from the world of probabilistic programming, (i.e., <a rel="nofollow" href="https://github.com/pymc-devs/pymc3" title="PyMC3">PyMC3</a> and <a rel="nofollow" href="https://github.com/Theano/Theano" title="Theano">Theano</a>), GermlineCNVCaller is able to simultaneously model both systematic biases and CNV events. More naive approaches to denoising (such as PCA) cannot always distinguish between signal and noise, and sometimes inadvertently subtract the signal. In contrast, our new modeling approach yields high sensitivity---especially in genomic regions of common CNV activity.</p>

<p>GermlineCNVCaller also introduces a hierarchical HMM method for segmentation, which learns these regions of common CNV activity across multiple samples while simultaneously calling CNVs in each sample. GermlineCNVCaller’s abilities shine on noisy exome data, but can scale to genomes by harnessing the power of Cromwell and WDL.</p>

<p><a rel="nofollow" href="https://www.youtube.com/watch?v=HxgDy2fowTg&amp;feature=youtu.be" title="An animation of GermlineCNVCaller inference performed on a cohort of simulated exome samples">An animation of GermlineCNVCaller inference performed on a cohort of simulated exome samples</a>. <br>
Video by: Mehrtash Babadi</p>

<p>The sample-by-target heatmaps in the center column show 1) count data generated from 2) underlying copy-number (CN) events; GermlineCNVCaller infers 3) CN calls in each sample, while also identifying 4) regions of common CNV activity (indicated in yellow). Counts and inferred CN calls are plotted for a single sample on the right, while various quantities which determine model convergence are tracked over learning iterations on the left.</p>

<p>Though they owe a lot to their prototypical predecessors, GATK4.1’s CNV pipelines have evolved substantially to yield dramatically improved performance and augmented capabilities. GATK CNV tool development is ongoing, so stay tuned for the next stage of evolution!</p>

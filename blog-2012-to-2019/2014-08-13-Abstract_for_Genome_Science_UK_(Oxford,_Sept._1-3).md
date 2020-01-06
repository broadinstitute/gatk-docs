## Abstract for Genome Science UK (Oxford, Sept. 1-3)

By Geraldine_VdAuwera

<p>Here's my abstract for the upcoming Genome Science UK meeting in Oxford, where I'll be talking about our hot new workflow for variant discovery. The slide deck will be posted in the <a rel="nofollow" href="http://www.broadinstitute.org/gatk/guide/events">Presentations</a> section as usual after the conference.</p>

<hr></hr><h3>Analyzing large cohorts without losing your mind: GATK's new reference model pipeline for variant discovery</h3>

<p>Variant discovery is greatly empowered by the ability to analyse large cohorts of samples rather than single samples taken in isolation, but doing so presents considerable challenges. Variant callers that operate per-locus (such as Samtools and GATK’s UnifiedGenotyper) can handle fairly large cohorts (thousands of samples) and produce good results for SNPs, but they perform poorly on indels. More recently developed callers that operate using assembly graphs (such as Platypus and GATK’s HaplotypeCaller) perform much better on indels, but their runtime and computational requirements tend to increase exponentially with cohort size, limiting their application to cohorts of hundreds at most. In addition, traditional multisample calling workflows suffer from the so-called “N+1 problem”, where full cohort analysis must be repeated each time new samples are added.</p>

<p>To overcome these challenges, we developed an innovative workflow that decouples the two steps in the multisample variant discovery process: identifying evidence of variation in each sample, and interpreting that evidence in light of the evidence gathered for the entire cohort. Only the second step needs to be done jointly on all samples, while the first step can be done just as well (and much faster) on one sample at a time. This decoupling hinges on the use of a novel method for reference confidence estimation that produces a genomic VCF (gVCF) intermediate for each sample.</p>

<p>The new workflow enables fast, highly accurate and computationally cheap variant discovery in cohort sizes that were previously intractable: it has already been applied successful to a cohort of nearly one hundred thousand samples. This replaces previous brute-force approaches and lowers the threshold of accessibility of sophisticated cohort analysis methods for all, including researchers who do not have access to large amounts of computing power.</p>

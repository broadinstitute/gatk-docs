## Conference Talk (UKGS) 2014 Sep 2: Analyzing cohorts without losing your mind

By Geraldine_VdAuwera

<p>This talk was presented by Geraldine Van der Auwera at Genome Science UK at Oxford on September 2, 2014. <a rel="nofollow" href="https://www.dropbox.com/s/mjk8gnndu5hs92m/GSUK14-RefModelPipeline.pdf?dl=0">Get the slide deck here</a>; abstract below.</p>

<h3>Abstract</h3>

<p>Variant discovery is greatly empowered by the ability to analyse large cohorts of samples rather than single samples taken in isolation, but doing so presents considerable challenges. Variant callers that operate per-locus (such as Samtools and GATK’s UnifiedGenotyper) can handle fairly large cohorts (thousands of samples) and produce good results for SNPs, but they perform poorly on indels. More recently developed callers that operate using assembly graphs (such as Platypus and GATK’s HaplotypeCaller) perform much better on indels, but their runtime and computational requirements tend to increase exponentially with cohort size, limiting their application to cohorts of hundreds at most. In addition, traditional multisample calling workflows suffer from the so-called “N+1 problem”, where full cohort analysis must be repeated each time new samples are added.</p>

<p>To overcome these challenges, we developed an innovative workflow that decouples the two steps in the multisample variant discovery process: identifying evidence of variation in each sample, and interpreting that evidence in light of the evidence gathered for the entire cohort. Only the second step needs to be done jointly on all samples, while the first step can be done just as well (and much faster) on one sample at a time. This decoupling hinges on the use of a novel method for reference confidence estimation that produces a genomic VCF (gVCF) intermediate for each sample.</p>

<p>The new workflow enables fast, highly accurate and computationally cheap variant discovery in cohort sizes that were previously intractable: it has already been applied successfully to a cohort of nearly one hundred thousand samples. This replaces previous brute-force approaches and lowers the threshold of accessibility of sophisticated cohort analysis methods for all, including researchers who do not have access to large amounts of computing power.</p>

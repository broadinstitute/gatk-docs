## What's new with Mutect2 since v4.1.1.0 ?

By davidben

<p>GATKv4.1.1.0 introduces streamlined somatic calling with fewer errors, fewer false-negatives and optimized sensitivity and precision due to several major advances in the Mutect2 pipeline. We hope the changes will help make your work more efficient, more accurate and less expensive, benefits that will be worth the slight annoyance of the occasional command line change to the workflow. Read to the bottom for what you need to know to run and take advantage of the new pipeline.</p>

<h2>Reducing errors with key bug fixes</h2>

<p>We fixed several bugs that were responsible for error messages about invalid log probabilities, infinities, NaNs etc. We also resolved an issue where CalculateContamination worked poorly on very small gene panels.</p>

<h2>Maximizing sensitivity and precision with a streamlined filtering strategy</h2>

<p>FilterMutectCalls now filters based on a single quantity, the probability that a variant is not a somatic mutation, regardless of cause. Previously, each had its own threshold. We have removed parameters such as -normal-artifact-lod, -max-germline-posterior, -max-strand-artifact-probability, -max-contamination-probability, and even -tumor-lod. FilterMutectCalls automatically determines the probability threshold that optimizes the "F score," the harmonic mean of sensitivity and precision. Users can tweak results in favor of more or less sensitivity by modifying a single parameter, the variable beta (the relative weight of sensitivity versus precision in the harmonic mean). Setting beta to a value greater than its default filters for greater sensitivity and setting it lower filters for greater precision.</p>

<h2>Reducing false-positives with a Bayesian somatic clustering model</h2>

<p>We had long suspected that modeling the spectrum of subclonal allele fractions would help distinguish somatic variants from errors. For example, if every somatic variant in a tumor occurred in 40% of cells, we would know to reject anything with an allele fraction significantly different from 20%. In the Bayesian framework of Mutect2 this means that we can model the read counts of somatic variants with binomial distributions. We account for an unknown number of subclones with a Dirichlet process binomial mixture model. Because CNVs, small subclones, and genetic drift of passenger mutations all contribute allele fractions that don’t match a few discrete values, this is still an oversimplification. Therefore, we include a couple of beta-binomials in the mixture to account for a background spread of allele fractions while still benefiting from clustering. Finally, we use these binomial and beta-binomial likelihoods to refine the tumor log odds calculated by Mutect2, which assume a uniform distribution of allele fractions.</p>

<h4>For more details refer to our step-by-step tutorial for somatic variant calling with Mutect2 v4.1.1.0 and higher <a rel="nofollow" href="https://gatkforums.broadinstitute.org/gatk/discussion/24057/how-to-call-somatic-mutations-using-gatk4-mutect2#latest" title="here">here</a>. Also, refer to the latest Mutect2 tool documentation <a rel="nofollow" href="https://software.broadinstitute.org/gatk/documentation/tooldocs/current/org_broadinstitute_hellbender_tools_walkers_mutect_Mutect2.php" title="here">here</a>.</h4>

<hr></hr>
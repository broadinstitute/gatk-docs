## Creating Variant Validation Sets - RETIRED

By delangel

<h3>Please note that this article has not been updated in a very long time and may no longer be applicable. Use at your own risk.</h3>

<hr></hr><table id="toc"><tr><td><div id="toctitle"><h2>Contents</h2></div>
<ul><li><a rel="nofollow" href="#Introduction">1 Introduction</a></li>
<li><a rel="nofollow" href="#GATK_Documentation">2 GATK Documentation</a></li>
<li><a rel="nofollow" href="#Sample_and_Frequency_Restrictions">3 Sample and Frequency Restrictions</a>
<ul><li><a rel="nofollow" href="#-sampleMode">3.1 -sampleMode</a></li>
<li><a rel="nofollow" href="#-samplePNonref">3.2 -samplePNonref</a></li>
<li><a rel="nofollow" href="#-frequencySelectionMode">3.3 -frequencySelectionMode</a></li>
</ul></li>
</ul></td></tr></table><h2><span id="Introduction"> Introduction </span></h2>

<p>ValidationSiteSelectorWalker is intended for use in experiments where we sample data randomly from a set of variants, for example in order to choose sites for a follow-up validation study. Sites are selected randomly but within certain restrictions. There are two main sources of restrictions: Sample restrictions and Frequency restrictions. Sample restrictions alter the polymorphic/monomorphic status of sites by restricting the sample set to a given number of samples. Frequency restrictions bias the site sampling method to sample either uniformly, or in accordance with the allele frequency spectrum of the input VCF.
</p>

<h2><span id="GATK_Documentation"> GATK Documentation </span></h2>

<p>For example command lines and a full list of arguments, please see the GATK documentation for this tool at <a rel="nofollow" href="http://www.broadinstitute.org/gatk/gatkdocs/org_broadinstitute_sting_gatk_walkers_validation_validationsiteselector_ValidationSiteSelector.html">Validation Site Selector</a>.
</p>

<h2><span id="Sample_and_Frequency_Restrictions"> Sample and Frequency Restrictions </span></h2>

<h3><span id="-sampleMode"> -sampleMode </span></h3>

<p>The -sampleMode argument controls the mode of sample-based site consideration. The options are:
</p>

<ul><li> None: All sites are included for consideration, including reference sites
</li><li> Poly_based_on_gt: Site is included if it has a variant genotype in at least one of the selected samples
</li><li> Poly_based_on_gl: Site is included if it is likely to be variant based on the genotype likelihoods of the selected samples
</li></ul><h3><span id="-samplePNonref"> -samplePNonref </span></h3>

<p>Note that Poly_based_on_gl uses the exact allele frequency calculation model to estimate P[site is nonref]. The site is considered for validation if P[site is nonref] &gt; [this argument]. So if you want to validate sites that are &gt;95% confidently nonref (based on the likelihoods), you would set -sampleMode POLY_BASED_ON_GL -samplePNonref 0.95
</p>

<h3><span id="-frequencySelectionMode"> -frequencySelectionMode </span></h3>

<p>The -frequencySelectionMode argument controls the mode of frequency matching for site selection. The options are:
</p>

<ul><li> Uniform: Choose variants uniformly, without regard to their allele frequency.
</li><li> Keep AF Spectrum: Choose variants so that the resulting allele frequency matches as closely as possible to that of the input VCF.
</li></ul>
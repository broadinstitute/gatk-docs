## Fake your data -- for Science!

By Geraldine_VdAuwera

<p><strong><em>Part 1 of a series on the theme of synthetic data</em></strong></p>

<p>Don't get me wrong, I'm not suddenly advocating for fraudulent research. What I'm talking about is creating <strong>synthetic sequence data</strong> for testing pipelines, sharing tools and generally increasing the <a rel="nofollow" href="https://medium.com/voices-from-the-open-science-movement/reproducibility-in-computational-analysis-53c0d227d6ca">computational reproducibility</a> of published studies, so that we can all more easily build on each other's work.</p>

<p>The majority of the effort around computational reproducibility has so far focused on better ways to share and run code, as far as I can tell. With great results -- it's been transformative to see the community adopt tooling like version control, containers and Jupyter notebooks. Yet you can give me all the containers and notebooks in the world; if I don't have appropriate data to run that code on, none of it helps me.</p>

<p>Most of the genomic data that gets generated for human biomedical research is subject to very strict access restrictions. These protections exist for good reason, but on the downside, they make it much harder to train researchers in key methodologies until after they have been granted access to specific datasets — if they can get access at all. There are certainly open datasets like 1000 Genomes and ENCODE that can be used beyond their original research purposes for some types of training and testing. However they don't cover the full range of what is needed in the field in terms of technical characteristics (eg exome vs WGS, amount of coverage, number of samples for scale testing etc); not by a long way.</p>

<p>That's where fake data comes in -- we can create synthetic datasets to use as proxies for the real data. This is not a new idea of course; people have been using synthetic data for some time, as in the <a rel="nofollow" href="https://www.synapse.org/#!Synapse:syn312572/wiki/58893">ICGC-TCGA DREAM Mutation challenges</a>, and there is already a <a rel="nofollow" href="https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5224698/">rather impressive range</a> of command-line software packages available for generating synthetic genomic data. It's even possible to introduce (or "spike in") variants into sequencing data, real or fake, on demand. So that's all pretty cool. But in practice these packages tend to be mostly used by savvy tool developers for small-scale testing and benchmarking purposes, and rarely (if ever? send me links!) by biomedical researchers for providing reproducible research supplements.</p>

<p>And frankly, it's no surprise. It's actually kinda hard.</p>

<p><strong><em>Next up: <a rel="nofollow" href="https://software.broadinstitute.org/gatk/blog?id=23949">An exercise in reproducibility (and frustration)</a></em></strong></p>

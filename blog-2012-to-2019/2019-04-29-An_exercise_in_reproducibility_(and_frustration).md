## An exercise in reproducibility (and frustration)

By Geraldine_VdAuwera

<p><strong><em>Part 2 of a series on the theme of synthetic data | Start with part 1: <a rel="nofollow" href="https://software.broadinstitute.org/gatk/blog?id=23939">Fake your data -- for Science!</a></em></strong></p>

<p>Last year my team and I collaborated with a genetics researcher, <a rel="nofollow" href="https://scholar.google.cl/citations?user=wP82vyoAAAAJ">Dr. Matthieu Miossec</a>, to reproduce an analysis from a paper he had co-authored, as a case study for a workshop. The <a rel="nofollow" href="https://www.ahajournals.org/doi/10.1161/CIRCRESAHA.118.313250">paper</a> reported variants associated with a particular type of congenital heart disease called Tetralogy of Fallot; a pretty classic example of a germline analysis that uses GATK for variant calling. Between the information provided in the preprint and Matthieu's generous cooperation, we had everything we needed to reimplement the analysis in a more portable, fully shareable form… Except the data, because as with many human genetics studies, the original exome data was private.</p>

<p><em>In illo tempore</em>, with the exuberance of youth filling my sails, I thought it should be simple enough to create some synthetic data to stand in for the original. I am wiser now, and grayer.</p>

<hr></hr><p>We originally explored using exome BAMs from the 1000 Genomes project, and simply spiking in the variants of interest in a subset to create semi-synthetic case samples. That proved more difficult than expected, in part because of the heterogeneity (wrt coverage, target territory, quality etc) of the 1000 Genomes data. We ran into a lot of validation errors, and the mutation spiking package we wanted to use (<a rel="nofollow" href="https://github.com/adamewing/bamsurgeon">Bamsurgeon</a>) was clearly not happy with that data.</p>

<p>After banging our heads against that wall for a bit we decided to just synthesize all the data from scratch using a read simulator, <a rel="nofollow" href="https://github.com/zstephens/neat-genreads">NEAT-genreads</a>. Or nearly from scratch; NEAT-genreads can use a VCF as starting point for generating the data, so we chose to use the VCFs from the 1000 Genomes. Which if you think about it amounts to creating fake BAMs representing real people's exomes... Then we spiked in the variants of interest into a random subset of the fake exomes to create synthetic case samples. We also spiked a synonymous mutation into the controls to ensure all samples went through the same processing steps and software.</p>

<p>We didn't make quite as many synthetic exomes as we'd originally planned; the largest set we made was 500 exomes in total (cases + controls), out of the 2,000 intended to match the paper. But that was enough to build a working proof of concept for reproducing the paper, which was all we needed for the workshop. And yes, we were able to show that the reimplemented analysis was able to pull out the expected mutations that we had spiked in. Success!</p>

<p>So it worked. What am I whining about? Well, all in all this took a lot more effort than I had originally envisioned. We had to chain together multiple tools, and unsurprisingly (this is bioinformatics) some of them had "creative differences" with each other regarding format requirements and what they deemed reasonable to produce as output. And once we had the basic commands working end to end, we had to write workflows to automate the process; since we wanted to generate hundreds of samples, there was no way running individual command lines manually was going to cut it.</p>

<p>Needless to say (and I do say this with the utmost respect) none of the development work that was involved in that project would be reasonable to expect from your average biomedical researcher.</p>

<p><strong><em>Next up: <a rel="nofollow" href="https://software.broadinstitute.org/gatk/blog?id=23976">Hacking a community resource (prototype) for synthetic data</a></em></strong></p>

<hr></hr><p>You can read more about the original project <a rel="nofollow" href="https://broad.io/ASHG2018">here</a>; we presented it at the ASHG meeting in October and have used it in several other workshops since. It's been quite useful for teaching purposes, so we make it available in an <a rel="nofollow" href="https://app.terra.bio/#workspaces/help-gatk/Reproducibility_Case_Study_Tetralogy_of_Fallot">example workspace in Terra</a> that anyone can use.</p>

<p><em>Terra is a cloud analysis platform developed at the Broad Institute, née FireCloud. It is currently in open beta development phase; it is freely accessible to everyone, and all new accounts are provided $300 free credits to spend on Google compute and storage.</em></p>

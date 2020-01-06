## Run the germline GATK Best Practices Pipeline for $5 per genome

By ebanks

<p><em>By Eric Banks, Director, Data Sciences Platform at the Broad Institute</em></p>

<p><a rel="nofollow" href="https://software.broadinstitute.org/gatk/blog?id=11398">Last week</a> I wrote about our efforts to develop a data processing pipeline specification that would eliminate batch effects, in collaboration with other major sequencing centers. Today I want to share our implementation of the resulting "Functional Equivalence" pipeline spec, and highlight the cost-centric optimizations we've made that make it incredibly cheap to run on Google Cloud.</p>

<p>For a little background, we started transitioning our analysis pipelines to Google Cloud Platform in 2016. Throughout that process we focused most of our engineering efforts on bringing down compute cost, which is the most important factor for our production operation. It's been a long road, but all that hard work really paid off: we managed to get the cost of our main Best Practices analysis pipeline down from about $45 to $5 per genome! As you can imagine that kind of cost reduction has a huge impact on our ability to do more great science per research dollar -- and now, we’re making this same pipeline available to everyone.</p>

<hr></hr><p>The Best Practices pipeline I'm talking about is the most common type of analysis done on a 30x WGS: germline short variant discovery (SNPs and indels). This pipeline covers taking the data from unmapped reads all the way to an analysis-ready BAM or CRAM (<em>i.e.</em> the part covered by the Functional Equivalence spec), then either a single-sample VCF or an intermediate GVCF, plus 15 steps of quality control metrics collected at various points in the pipeline, totalling $5 in compute cost on Google Cloud. As far as I know this is the most comprehensive pipeline available for whole-genome data processing and germline short variant discovery (without skimping on QC and important cleanup steps like base recalibration).</p>

<p><img src="https://us.v-cdn.net/5019796/uploads/editor/86/wegjk3uey5da.png" alt="" title="" class="embedImage-img importedEmbed-img"></img></p>

<p>Let me give you a real-world example of what this means for an actual project. In February 2017, our production team processed a cohort of about 900 30x WGS samples through our Best Practices germline variant discovery pipeline; the compute costs totalled $12,150 or $13.50 per sample. If we had run the version of this pipeline we had just one year prior (before the main optimizations were made), it would have cost $45 per sample; a whopping $40,500 total! Meanwhile we've made further improvements since February, and if we were to run this same pipeline today, the cohort would cost only $4,500 to analyze.</p>

<table><thead><tr><th align="left"></th>
  <th align="left">2016</th>
  <th align="left">2017</th>
  <th align="left">Today</th>
</tr></thead><tbody><tr><td align="left"># of Whole Genomes Analyzed</td>
  <td align="left">900</td>
  <td align="left">900</td>
  <td align="left">900</td>
</tr><tr><td align="left">Total Compute Cost</td>
  <td align="left">$40,500</td>
  <td align="left">$12,150</td>
  <td align="left">$4,500</td>
</tr><tr><td align="left">Cost per Genome Analyzed</td>
  <td align="left">$45</td>
  <td align="left">$13.50</td>
  <td align="left">$5</td>
</tr></tbody></table><p>For the curious, the most dramatic reductions we saw came from using different machine types for each of the various tasks (rather than piping data between tasks), leveraging GCP’s preemptible VMs, and most recently incorporating NIO to minimize the amount of data localization involved. You can read more about these approaches on <a rel="nofollow" href="https://www.blog.google/topics/google-cloud/our-genes-how-google-cloud-helps-broad-institute-slash-cost-research/">Google's blog</a>. At this point the single biggest culprit for cost in the pipeline is BWA (the genome mapper), a problem which its author Heng Li is actively working to address through a much faster (but equivalently accurate) mapper. Once Heng's new mapper is available, we anticipate the cost per genome analyzed to drop below $3.</p>

<p>On top of the low cost of operating the pipeline, the other huge bonus we get from running this pipeline on the cloud is that we can get any number of samples done in the time it takes to do just one, due to the staggeringly elastic scalability of the cloud environment. Even though it takes a single genome 30 hours to run through the pipeline (and we're still working on speeding that up), we're able to process genomes at a rate of one every 3.6 minutes, and we've been averaging about 500 genomes completed per day.</p>

<p>We're making the workflow script for this pipeline available <a rel="nofollow" href="https://github.com/gatk-workflows/five-dollar-genome-analysis-pipeline">in Github</a> under an open-source license so anyone can use it, and we're also providing it as a <a rel="nofollow" href="https://portal.firecloud.org/#workspaces/help-gatk/five-dollar-genome-analysis-pipeline">preconfigured pipeline in FireCloud</a>, the pipelining service we run on Google Cloud. Anyone can access FireCloud for free, you just need to pay Google for any compute and storage costs you incur when running the pipelines. So to be clear, when you run this pipeline on your data in FireCloud, all $5 of compute costs will go directly to the cloud provider; we won't make any money off of it. <em>And there are no licensing fees involved at any point!</em></p>

<p>As a cherry on the cake, our friends at Google Cloud Platform are sponsoring free credits to help first-time users get started with FireCloud: the first 1,000 applicants can get $250 worth of credits to cover compute and storage costs. You can learn more <a rel="nofollow" href="https://software.broadinstitute.org/firecloud/documentation/freecredits">here on the FireCloud website</a> if you're interested.</p>

<p>Of course, we understand that not everyone is on Google Cloud, so we are actively collaborating with other cloud vendors and technology partners to expand the range of options for taking advantage of our optimized pipelines. For example, the Chinese cloud giant Alibaba Cloud is developing a backend for Cromwell, the execution engine we use to run our pipelines. And it's not all cloud-centric either; we are also collaborating with our long-time partners at Intel to ensure our pipelines can be run optimally on on-premises infrastructure without compromising on quality.</p>

<p>In conclusion, this pipeline is the result of two years' worth of hard work by a lot of people, both on our team and on the teams of the institutions and companies we collaborate with. We're all really excited to finally share it with the world, and we hope it will make it easier for everyone in the community to get more mileage out of their research dollars, just like we do.</p>

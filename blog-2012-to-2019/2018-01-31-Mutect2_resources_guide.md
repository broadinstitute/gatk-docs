## Mutect2 resources guide

By shlee

<h3>A new tutorial for somatic calling</h3>

<p>We have a new tutorial, <a rel="nofollow" href="https://software.broadinstitute.org/gatk/documentation/article?id=11136">Tutorial#11136</a>, that outlines how to call somatic short variants, i.e. SNVs and indels, with GATK4 Mutect2. The tutorial provides small example data to follow along with.</p>

<h3>Mutect2-compatible germline resources</h3>

<p>Full-length Mutect2-compatible human germline resources are available on our <a rel="nofollow" href="https://software.broadinstitute.org/gatk/download/bundle">FTP server</a> and at <a rel="nofollow" href="https://console.cloud.google.com/storage/browser/gatk-best-practices/somatic-hg38?project=broad-dsde-outreach">gs://gatk-best-practices/</a>. The resources are simplified from the <a rel="nofollow" href="http://gnomad.broadinstitute.org/downloads">gnomAD resource</a> and retain population allele frequencies. Mutect2 and GetPileupSummaries are the two tools in the workflow that each require a germline resource.</p>

<h3>Working WDL scripts</h3>

<p>If you want to run the <em>Somatic Short Variant Discovery</em> Best Practices workflow using WDL, be sure to check out the official <a rel="nofollow" href="https://github.com/gatk-workflows/gatk4-somatic-snvs-indels">Mutect2 WDL script</a> in the <em>gatk-workflows</em> repository. <a href="https://gatkforums.broadinstitute.org/gatk/profile/bshifaw" rel="nofollow">@bshifaw</a> and other engineers optimize the scripts in the repository to run efficiently in the cloud. Furthermore, the scripts come with example JSON format inputs files filled out with publically-accessible cloud data.</p>

<p>For other Mutect2-related scripts, e.g. towards panel of normals generation, check out the <em>gatk</em> repository's <a rel="nofollow" href="https://github.com/broadinstitute/gatk/tree/master/scripts/mutect2_wdl">scripts/mutect2_wdl</a> directory. Our developers update these scripts on a continual basis.</p>

<h3>For background information</h3>

<p>If you are new to somatic calling, be sure to read <a rel="nofollow" href="https://software.broadinstitute.org/gatk/documentation/article?id=11127">Article#11127</a>. It gives an overview of what traditional somatic calling entails. For one, somatic calling is NOT just a difference between two callsets in that germline variant sites are excluded from consideration.</p>

<p>For those switching from GATK3 MuTect2, <a rel="nofollow" href="https://software.broadinstitute.org/gatk/blog?id=10911">Blog#10911</a> will bring you up to speed on the differences.</p>

<h3>An off-label tutorial for simple difference calling</h3>

<p>If you are interested in simply calling differences between two samples, <a rel="nofollow" href="https://software.broadinstitute.org/gatk/blog?id=11315">Blog#11315</a> outlines an <em>off-label</em> two-pass Mutect2 workflow. <a rel="nofollow" href="https://en.wikipedia.org/wiki/Off-label_use"><em>Off-label</em></a> means the workflow is not a part of the Best Practices and is therefore unsupported. However, if given enough community interest, we may be convinced to further flesh out the workflow. Please do post to the forum to express interest.</p>

<hr></hr>
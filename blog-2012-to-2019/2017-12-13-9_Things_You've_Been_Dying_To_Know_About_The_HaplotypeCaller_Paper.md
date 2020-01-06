## 9 Things You've Been Dying To Know About The HaplotypeCaller Paper

By gauthier

<p><em>By Laura Gauthier, lead GATK developer for germline short variant discovery</em></p>

<h3>Q: What, there's a HaplotypeCaller paper?</h3>

<p><strong>A:</strong> Yes! We are super excited to announce the long-awaited release of The HaplotypeCaller Paper -- or rather, the <a rel="nofollow" href="https://www.biorxiv.org/content/early/2017/11/14/201178.1">preprint in bioRxiv</a>. (Actually we announced it on Twitter a while back but we understand not everyone enjoys such an old-school way of keeping up with the news). Hopefully you’re as excited as we are, if not more so, but we understand that this probably raises a few questions for some of you, so we tried to address some of those below.</p>

<hr></hr><h3>Q: Why did it take so long?!</h3>

<p><strong>A:</strong> Our mission is to develop the tools that get used by others to do groundbreaking scientific research.  Benchmarking and validation are important parts of our prototyping and development cycle, but given that we’re not subject to the “publish or perish” culture of a research lab, submitting manuscripts presenting those results wasn’t a high priority for us.</p>

<h3>Q: Are you going to submit it to a peer-reviewed journal?</h3>

<p><strong>A:</strong> Probably not.</p>

<h3>Q: Why not?</h3>

<p><strong>A:</strong> Our main motivation for posting the HaplotypeCaller manuscript to bioRxiv was to provide something recent/reasonable to cite and to make more details of the methods public. Submitting to a peer-reviewed journal usually involves a lot of time working on revisions that we’d rather put towards working on further improvements to the tools.</p>

<h3>Q: Is it still a preprint if it's never intended to go to print?</h3>

<p><strong>A:</strong> You tell us.</p>

<h3>Q: What version of HaplotypeCaller does the paper describe?</h3>

<p><strong>A:</strong> The paper describes the GATK 3.4 version of the HaplotypeCaller (yes we started this a while back) but the HaplotypeCaller has not changed significantly in later 3.x versions so it's fair to say the paper covers up to version 3.8 completely.</p>

<h3>Q: How do these results compare to GATK4?</h3>

<p><strong>A:</strong> At time of writing, the GATK4 version of HaplotypeCaller is still considered a beta version.  The team is actively working on validating the GATK4 version to make sure that it’s guaranteed to be as good as or better than the GATK3 version described in the paper.</p>

<h3>Q: How does the methodology compare to GATK4?</h3>

<p><strong>A:</strong> The GATK engine that parses the BAM and “shards” the data to pass to the tools has been rewritten for improved efficiency over GATK3, and the HaplotypeCaller code has been refactored for better organization and readability. So there's a lot that is different in terms of software implementation. However the algorithms and equations presented in the manuscript remain the same, so overall the paper's description of how the HaplotypeCaller operates also applies to the GATK4 beta version, and it is appropriate to use it as a citation for results derived from versions up to the current beta (4.beta.6).</p>

<h3>Q: Does the release of this paper hint at a change in how the team prioritizes publication?</h3>

<p><strong>A:</strong> To some extent. The developers of the somatic variant caller Mutect2 and related tools have put in a lot of effort to prepare white papers on the methods involved (<a rel="nofollow" href="https://github.com/broadinstitute/gatk/blob/master/docs/mutect/mutect.pdf">Mutect2 itself</a>, the <a rel="nofollow" href="https://github.com/broadinstitute/gatk/blob/master/docs/local_assembly.pdf">assembly process</a> and the <a rel="nofollow" href="https://github.com/broadinstitute/gatk/blob/master/docs/pair_hmm.pdf">pairHMM algorithm</a>), some of which are shared with the HaplotypeCaller. They hope to release a manuscript featuring Mutect2 somatic SNV and INDEL variant calling results in the near future. Additionally, the GATK development team as a whole aims to make more of our internal benchmarking and validation efforts more transparent and available to other tool developers; an effort that our colleague Yossi Farjoun kicked off in style in his <a rel="nofollow" href="https://software.broadinstitute.org/gatk/blog?id=10912">blog post about the new "SynDip" benchmark</a> last week.</p>

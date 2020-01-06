## Springtime of GATK4: pipelines and machine learning at Bio-IT

By Geraldine_VdAuwera

<p>It's finally Spring in Boston; the trees are sprouting leaves again, everything is turning green and gloriously alive -- and Bio-IT World is starting, which makes it official! Many of you may not know or care about Bio-IT, since it's more a biotech trade show than a scientific meeting, but for us it has become a springtime tradition to announce important developments there. These announcements have often focused on strategic/roadmap level plans -- for example that's where we broke the news last year that GATK4 would be fully open-source to a standing ovation (whoo!) --  but this year we're in a position to talk about the new capabilities we're actually delivering, and that feels really good. To quote the inevitable Steve Jobs, real artists ship, and boy are we shipping.</p>

<p>We have two major themes that we're developing this year: (1) democratization of the Best Practices pipelines, which includes everything from increasing access to ease of deployment, standardization and optimization for cost and speed; and (2) application of machine learning to improve accuracy and scalability in established pipelines as well as tackle new areas like germline CNV discovery.</p>

<hr></hr><p>I'm including a schedule of GATK-related talks and demos below for the tiny minority of you who will actually be there in person; for the rest, we plan to post summaries of the key information over the next few days, right here on the GATK blog, so I promise you won't miss out on the important stuff!</p>

<p>Speaking of which, you might have seen <a rel="nofollow" href="https://www.illumina.com/company/news-center/press-releases/press-release-details.html?newsid=2349147">this little item</a> in the news today about Illumina acquiring Edico Genome, with a quote from our very own Anthony Philippakis, who directs our mothership, the Broad's Data Sciences Platform (emphasis mine):</p>

<blockquote class="UserQuote blockquote"><div class="blockquote-content">
  <p class="blockquote-line">“The scientific community should align around standards to maximize the impact of genomics in health,” said Anthony Philippakis, MD, PhD, Chief Data Officer of the Broad Institute of MIT and Harvard. “We are excited to collaborate with Illumina on approaches and pipelines for the analysis of NGS data. The Genome Analysis Toolkit (GATK) has been adopted by a diverse set of researchers, and <strong>we look forward to integrating these methods with Illumina sequencers to improve the overall efficiency of data analysis</strong> —enabling the community to more easily share and collaborate.”</p>
</div></blockquote>

<p>You can watch a short video clip of Anthony Philippakis talking about what this means for GATK <a rel="nofollow" href="https://twitter.com/illumina/status/996492913000570880">here</a>; or if you're down at the Seaport today I'm happy to meet up and discuss in person <img class="emoji" src="https://gatkforums.broadinstitute.org/resources/emoji/smile.png" title=":)" alt=":)" height="20"></img></p>

<hr></hr><h3>Bio-IT World schedule for GATK &amp; friends</h3>

<table><thead><tr><th align="left"></th>
  <th align="left"></th>
  <th align="left"></th>
  <th align="left"></th>
</tr></thead><tbody><tr><td align="left"><strong>Tuesday 15 May</strong></td>
  <td align="left"></td>
  <td align="left"></td>
  <td align="left"></td>
</tr><tr><td align="left">GATK4 pipelines &amp; machine learning</td>
  <td align="left">Geraldine VdA</td>
  <td align="left">5:00-5:30</td>
  <td align="left">Google Cloud booth</td>
</tr><tr><td align="left">Pipelining on FireCloud</td>
  <td align="left">Geraldine VdA</td>
  <td align="left">5:30-6:00</td>
  <td align="left">Google Cloud booth</td>
</tr><tr><td align="left"><strong>Wednesday 16 May</strong></td>
  <td align="left"></td>
  <td align="left"></td>
  <td align="left"></td>
</tr><tr><td align="left">Pipelining with WDL and Cromwell</td>
  <td align="left">Jeff Gentry</td>
  <td align="left">11:00-11:30</td>
  <td align="left">Google Cloud booth</td>
</tr><tr><td align="left">Machine learning in GATK4</td>
  <td align="left">Lee Lichtenstein</td>
  <td align="left">12:00-12:30</td>
  <td align="left">Skyline</td>
</tr><tr><td align="left">Democratizing the GATK4 pipelines</td>
  <td align="left">Geraldine VdA</td>
  <td align="left">12:40-1:10</td>
  <td align="left">Cityview 2</td>
</tr><tr><td align="left">Pipelining with WDL and Cromwell</td>
  <td align="left">Jeff Gentry</td>
  <td align="left">3:00-3:30</td>
  <td align="left">Google Cloud booth</td>
</tr><tr><td align="left">Pipelining on FireCloud</td>
  <td align="left">Geraldine VdA</td>
  <td align="left">3:30-4:00</td>
  <td align="left">Google Cloud booth</td>
</tr><tr><td align="left">GATK4 pipelines &amp; machine learning</td>
  <td align="left">Geraldine VdA</td>
  <td align="left">4:00-4:30</td>
  <td align="left">Google Cloud booth</td>
</tr><tr><td align="left">Pipelining on FireCloud</td>
  <td align="left">Geraldine VdA</td>
  <td align="left">5:00-5:30</td>
  <td align="left">Google Cloud booth</td>
</tr><tr><td align="left"><strong>Thursday 17 May</strong></td>
  <td align="left"></td>
  <td align="left"></td>
  <td align="left"></td>
</tr><tr><td align="left">FAIR data on the cloud</td>
  <td align="left">Geraldine VdA</td>
  <td align="left">11:40-12:10</td>
  <td align="left">Waterfront 3</td>
</tr></tbody></table><p><em>The Google Cloud booth is in the middle lane of the exhibition hall close to the registration desk.</em></p>

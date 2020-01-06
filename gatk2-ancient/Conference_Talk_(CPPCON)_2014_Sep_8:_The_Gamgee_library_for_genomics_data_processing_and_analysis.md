## Conference Talk (CPPCON) 2014 Sep 8: The Gamgee library for genomics data processing and analysis

By Geraldine_VdAuwera

<p>Mauricio Carneiro presented this talk at CPPCON (C++ conference) in Bellevue, WA on September 8, 2014. His slide deck and a link to the video are available <a rel="nofollow" href="http://www.broadinstitute.org/gatk/guide/presentations?id=4767">at this link</a> if you're viewing this post in the forum, or below if you are viewing the presentation page already.</p>

<h3>Abstract</h3>

<p>Our group has defined the standards for DNA and RNA sequencing data processing and analysis for disease research and clinical applications. In the last 5 years we have published our tools in the GATK (genome analysis toolkit) which is completely written in java. With the scaling of next generation sequencing and the immense amount of that needs to be processed we hit a performance wall and found ourselves limited by the language to make optimizations and rewrite the algorithms in a way that would conform better to modern hardware.</p>

<p>Enter Gamgee. A free and open source C++14 library that offers much of the functionality of the GATK framework with the performance necessary to scale to the hundreds of petabytes of todays complex diseases projects. We will show how the tools developed using the Gamgee library replaced legacy java GATK tools in the production pipeline of the Broad Institute. We will also talk about how the algorithms have changed to take advantage of the native libraries and modern hardware features such as SSE/AVX and GPUs.</p>

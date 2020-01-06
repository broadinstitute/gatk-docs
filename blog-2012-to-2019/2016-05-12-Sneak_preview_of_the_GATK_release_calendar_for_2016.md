## Sneak preview of the GATK release calendar for 2016

By Geraldine_VdAuwera

<p>Since the GATK first started gaining traction in the research community ca. 2010, its development has sustained a fairly rapid pace, with a new major version (1, 2 and 3 so far) coming out about every 2 years. Each major version was a straight continuation of the same codebase, distinguished by significantly new tools and capabilities (e.g. HaplotypeCaller in 2.0, the GVCF workflow in 3.0).</p>

<p><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/5f/2addb75417cf5dbe7a7075c8b027d2.png" width="300px" border="none" alt="image" style="float: right;" class="embedImage-img importedEmbed-img"></img></p>

<p>This year, we're on track for a new major version, but we're breaking the mold of the classic GATK codebase. Over the past 18 months, in parallel to the ongoing 3.x development effort, we built a brand new GATK engine that is faster, more scalable and can support new types of analysis that weren't possible in the original GATK framework. Now we're hard at work porting the classic GATK tools over to the new framework, as well as developing some new ones (copy number!). The resulting toolkit will be formally released as GATK 4 later this year. If you're keen to try it out, it's already available as an alpha preview; I'll follow up on this with more details soon.</p>

<p>But that's not all. At the same time, we kept working on the GATK 3.x package in order to continue delivering improvements to the research community. Now we're just about ready to release version 3.6 -- nothing <em>yuuugely</em> different, but quite a few bug fixes and feature enhancements (especially in the GVCF workflow tools) that have been widely requested. Again, details to follow. Oh, and it supports Java 8! Which previous versions <em>do not</em> -- it may look like they do because they run on it without crashing, but there could be silent correctness errors. Which are the worst; I prefer a good honest in-your-face run-busting error any day.</p>

<p>So there you have it; version 3.6 coming out sometime next week (-ish), and GATK 4 coming out later this year, probably Fall timeframe. In between, we'll have one last 3.x release, either a patch release (3.6-x) or a proper minor release (3.7) depending on how substantial are the changes involved, to immortalize the last state of the classic GATK before it gets encased in amber.</p>

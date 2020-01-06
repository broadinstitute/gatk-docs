## Service note: Important website updates

By Geraldine_VdAuwera

<p>First, I hope those of you in the USA had a relaxing and/or exciting holiday weekend (happy birthday, 'Murica!). For the rest, we thank you for your patience as we recover from the festivities and work our way through the backlog of forum questions.</p>

<p>Now, I wanted to let you know that over the next few weeks, we're going to push out a variety of improvements to the GATK website and documentation contents. We start today with a main push that involves some structural changes that we think will improve the user experience overall and make it easier for new users in particular. Much of this is based on feedback we've received over the years, so hopefully we're following the will of the people!</p>

<p>We've done our best to avoid causing any disruptions for those of you who have been using our website for a long time, but we did have to move a few things around. Here are the highlights; if you have strong feelings about any of this (good or bad) let us know in the comments. Also let us know if you stumble across anything that looks broken and we'll fix it double quick.</p>

<hr></hr><h3>Guide -&gt; Documentation</h3>

<p>We found that the section name "Guide" (short for User Guide) did not indicate clearly enough that it held the user documentation. So we're changing the name of that section to "Documentation" to be more explicit. We've set up some internal links so that old bookmarks and links should still work, but if you find any links broken by this change, let us know.</p>

<p>In addition, the newly-renamed Documentation section also gets a proper <a rel="nofollow" href="https://www.broadinstitute.org/gatk/documentation/">index page</a> that breaks down how the documentation is organized, <em>and</em> a <a rel="nofollow" href="https://www.broadinstitute.org/gatk/documentation/quickstart">Getting Started</a> page that summarizes how to, well, get started.</p>

<p>Note that we switched off the tag-related functionality (tag search and summary of section or article tags) because it was adding to the page load time yet did not seem to be popular. If you feel this is a huge loss for humanity (really?) let us know; if enough people complain we'll turn them back on.</p>

<hr></hr><h3>Best Practices</h3>

<p>We wanted to give the <a rel="nofollow" href="https://www.broadinstitute.org/gatk/best-practices">Best Practices</a> documentation more visibility, so we made it a top-level section in its own right. There's still a link within "Documentation" that points to "Best Practices" (since it is technically a subset of our documentation) but now you can also find it in the menu at the top of every page.</p>

<p>This new section is designed to make it clear that there are multiple Best Practices workflows, each tailored for specific types of variants and experimental designs. Each one is presented through a dedicated page that offers an overview of the workflow as well as interactive browsing of the steps involved, with both high-level descriptions and direct links to more detailed documentation (e.g. tool docs, tutorials, FAQs etc).</p>

<p>As it takes a lot of work to assemble this per-workflow content, we currently have only one workflow that is fully documented in this way (<a rel="nofollow" href="https://www.broadinstitute.org/gatk/best-practices/bp_3step.php?case=GermShortWGS">Best Practices for Germline SNP &amp; Indel Discovery in Whole Genome and Exome Sequence</a>). However, we have summary information out for each of the others, and are actively working on assembling the full documentation for all of them. We'll push each out as it reaches completion.</p>

<hr></hr><h3>Download</h3>

<p>We simplified the page that offers the <a rel="nofollow" href="https://www.broadinstitute.org/gatk/download/">current GATK package</a> for download, and added some dedicated download pages for other packages: <a rel="nofollow" href="https://www.broadinstitute.org/gatk/download/alpha">GATK 4 Alpha</a>, <a rel="nofollow" href="https://www.broadinstitute.org/gatk/download/queue">Queue + GATK</a> and <a rel="nofollow" href="https://www.broadinstitute.org/gatk/download/mutect">MuTect 1</a>. We also added a page for the <a rel="nofollow" href="https://www.broadinstitute.org/gatk/download/bundle">resource bundle information</a> that previously lived in FAQs, and a page for the <a rel="nofollow" href="https://www.broadinstitute.org/gatk/download/licensing">licensing information</a>. Nightly builds and archives are still in the same place.</p>

<p><em>Note that we also added an option in the clickthrough screen for license holders, who previously had no choice but to click that they accepted the academic license, which does not apply to them.</em></p>

<hr></hr><h3>Home page and About</h3>

<p>The <a rel="nofollow" href="https://www.broadinstitute.org/gatk/">Home page</a> itself got a facelift, with a new picture and a bit less blather.</p>

<p>Finally, we removed the infrequently-visited "About" section and recycled the key information it contained in a "Learn More" button on the Home page that shows/hide it on demand (requires javascript).</p>

<hr></hr><h3>Coming soon</h3>

<p>Next up, we plan to add links to reference implementation scripts for the Best Practices pipelines as they are implemented in production at Broad; make a few updates to the Best Practices workflow for germline short variant discovery; produce full docs for the RNAseq workflow and the somatic short variants workflows; and update the presentations section with the latest crop of workshop materials.</p>

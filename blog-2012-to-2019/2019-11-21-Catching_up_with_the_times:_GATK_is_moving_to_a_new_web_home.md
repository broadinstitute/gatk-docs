## Catching up with the times: GATK is moving to a new web home

By Geraldine_VdAuwera

<p><strong><a rel="nofollow" href="https://www.dictionary.com/e/acronyms/tldr/">TL,DR:</a> In a few weeks, we're going to move the website and forum to a new online platform that will scale better with the needs of the research community.</strong> The website will still live at <a href="https://software.broadinstitute.org/gatk/" rel="nofollow">https://software.broadinstitute.org/gatk/</a> but there will be some important changes at the level of the user guide and the support forum in particular. Read on to get the lowdown on where this is coming from, where we're heading and how you can prepare for the upcoming migration.</p>

<hr></hr><h3>A brief history</h3>

<p>When GATK was first released around 2010, its documentation lived in a rather primitive wiki that was half-public, half-private, and almost entirely aimed at developers. The wiki was supplemented by a proto-forum, hosted by Get Satisfaction and run by Eric Banks, one of the original authors of GATK who has since risen to the lofty position of Senior Director in the Data Sciences Platform at the Broad (a heartwarming rags-to-riches story for another time). Despite being an absolutely lovely human being in person, Eric was notoriously mean to the unfortunate few who dared ask questions on the old forum. So, in 2012, I was hired to be a human filter for his snark, plus, you know, build a proper user guide. Something that would enable researchers to use GATK without needing to be physically in the room with the developers for the darn thing to work. Coming out of a wetlab microbiology postdoc, I was uniquely unqualified for the job, but that too is a story for another time... Point is, that's how a little over seven years ago, Roger the summer intern and I built and launched the GATK website, which included a more formally structured user guide and the community forum hosted by Vanilla Forums that we have been using ever since.</p>

<p>Our little hand-rolled artisanal website has had a good run, with over 20 million page views to date and about two to three thousand unique visitors on any given weekday. But it's time to face the facts: we've outgrown it. In that time, and especially since the release of GATK4 two years ago (OMG has it really been two years already), the toolkit has expanded dramatically. It currently includes more than 200 tools and multiple Best Practices pipelines covering the major variant classes, plus use cases like mitochondrial analysis. We're aware that many of you find it difficult to find the information you need in our sprawling documentation. And there's more new stuff coming out soon that we haven't yet had a chance to talk about... So it's clear we're going to need both a better structure and way more elbow room than the current system can support.</p>

<h3>Long story short: it's time to reboot and rebuild</h3>

<p>For the past few months, we've been crafting a new web home for GATK documentation and support. This time, instead of building a traditional website, we're using a customer service system called Zendesk that includes a knowledge base module for documentation and a community forum for Q&amp;A. Part of our support team has already been using this system for the <a rel="nofollow" href="https://support.terra.bio/">Terra helpdesk</a>. Although the Terra knowledge base itself is still a work in progress, it’s been a positive experience so far. That gives us confidence that adopting Zendesk for GATK will help us improve the usability of the GATK documentation. We're also looking forward to being able to streamline our support operation by consolidating across the multiple software products and services offered by the Data Sciences Platform. That's good for everyone — not just those of you who use WDL and Terra as well as GATK — because if our support team spends less time wrangling different internal systems, they can spend more time improving the docs and answering your questions.</p>

<h3>What's going to change?</h3>

<p>Overall we're trying to minimize disruption but there will be a few important changes. Here's a breakdown of what you're most likely to care about.</p>

<h4>The user guide will be organized a little differently and the search box will work better</h4>

<p>We're taking this opportunity to update how the documentation content is organized to make it easier to find information. Hopefully this will be all upside, but if you get lost at any point, try the search box — it should work better in the new system.</p>

<h4>Some links may break</h4>

<p>This is perhaps the most important consequence of the fact that we're moving to a new content management system: all links to individual articles will change. We're going to set up an automated redirection system to map old URLs to the new ones, so that your bookmarks and links that people have previously posted online stay functional, but we can't guarantee that we'll be able to capture absolutely everything. We'll do our best to make the system handle missing content as gracefully as possible.</p>

<h4>GATK3 documentation will be archived in GitHub</h4>

<p>We're aware that the awkward coexistence of docs from the GATK3 era and the newer GATK4 versions is one of the major sources of confusion in the current user guide. We've wrestled with this problem and ultimately decided to move the GATK3 content to <a rel="nofollow" href="https://github.com/broadgsa/gatk-protected/tree/master/doc_archive">the documentation archive</a> in the <a rel="nofollow" href="https://github.com/broadgsa/gatk-protected">old GATK repository in GitHub</a>, where versions 1.x through 3.x of the code live. This way the old content will always remain available for anyone still working with older versions of the software, yet it will be more clear that it only applies to those versions, and it will be out of the way of anyone using the current versions of the tools. And of course, we'll do our best to include all those articles in the redirect mapping to keep links functional.</p>

<h4>You'll need a new account for the community forum</h4>

<p>Unfortunately we're not able to move existing forum accounts to the new platform. So to ask questions, start discussions or add comments in the new Community forum, you’ll need to create a new account. The good news is that this new account will work for all the other products we support, like Terra and Cromwell/WDL. And if you already have a Terra community forum account, you’re all set.</p>

<p><em>The new system will support single sign-on (SSO) with Google, Microsoft, Twitter and Facebook.</em></p>

<h4>Old forum discussions will eventually be taken offline</h4>

<p>I'll admit it: this is the part that makes me hyperventilate a little. We have over 17,000 discussion threads in the "Ask a question" section of the forum, and it's just not feasible to migrate them all over to the new platform. Most of them are out of date anyway, referencing old tools and methods that we no longer recommend, command syntax that no longer works, and my favorite, bugs that no longer occur! But there is still plenty of useful information in there that's not in the docs, from explanations of weird errors to strategies for customizing methods for non-standard use cases. So we're going to keep the old forum online in read-only mode for the next few months, and during that time we'll comb through the most frequently visited threads to capture the good stuff and turn it into documentation articles. We're also open to suggestions if there are any discussions that you have found particularly useful in your own work.</p>

<p>However, at some point we're going to have to shut down the old forum. The plan right now is to <strong>shut it down on February 1st, 2020</strong>, but we'll re-evaluate that timing if we feel that we need more time for the knowledge capture process. Your opinion on this matters a lot to us, so don't hesitate to nominate threads that you think would be useful to preserve in the knowledge base. We also recommend you save any threads that are important to you personally as a PDF or HTML page on your computer just in case. If all else fails, the Internet Archive's "Way Back Machine" does preserve <a rel="nofollow" href="https://web.archive.org/web/*/gatk%20forum">snapshots of the forum</a>, so it's very likely that those old forum discussions will actually outlive us all.</p>

<h3>Talk to us</h3>

<p>Ultimately the purpose of these resources is to help you use GATK effectively in your work, so we'd really like to hear from you, especially if you have concerns about how any of this is going to affect how you normally use the documentation and forum. We're very open to making amendments based on your feedback, both before the migration happens but also during the months that follow. I have no doubt that as the dust settles and we put some mileage on the new platform, we'll see opportunities emerge to tweak it for the better. Don't be shy about volunteering your thoughts and suggestions!</p>

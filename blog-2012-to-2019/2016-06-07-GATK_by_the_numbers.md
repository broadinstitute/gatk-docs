## GATK by the numbers

By Geraldine_VdAuwera

<p>For my 10,000th posting on this forum, I thought I'd pull together a few numbers.</p>

<p>First, yes I did just say this is my 10,000th post. That breaks downs to 464 new discussions (documentation articles and blog posts, including this one) and 9536 comments in various threads. I've also posted over 1,000 tweets as <a rel="nofollow" href="https://twitter.com/gatk_dev">@gatk_dev</a> on behalf of the development team. When people ask what I do I can say with a straight face that I'm a scientist and I tweet for a living, it's awesome.</p>

<p>But hey, here are some more important numbers.</p>

<ul><li><strong>Forum and website (since 2012):</strong> 35,000 registered users; 3,000 active participants; 6,000 discussions; 20,000 comments; 50,000 page views weekly; 8,000,000 page views total.</li>
<li><strong>Codebase:</strong> 23 version; 59 contributors; 14,000 commits ; 500,000 lines.</li>
<li><strong>Usage:</strong> 5,000,000 CPU days; 800,000,000 jobs; 30,000 distinct users.</li>
</ul><p>Details below.</p>

<hr></hr><h3>Forum and website (since 2012)</h3>

<p>The forum community includes just shy of 35,000 registered users. Among these, an active subset of about 3,000 have posted over 6,000 discussions and over 20,000 comments. That's <em>not</em> counting mine; and soon I'll have to start subtracting <a href="https://gatkforums.broadinstitute.org/gatk/profile/Sheila" rel="nofollow">@Sheila</a>'s since she has taken over my day-to-day forum duties and is racking up quite a post count herself.</p>

<p>Between the forum and the documentation, we typically get about 50,000 page views per week (showing a neat Monday-Friday hill pattern -- good on you for having a life on the weekends, people!) totaling over 8,000,000 page views since the launch of the website in 2012.</p>

<h3>Codebase</h3>

<p>Now let's talk about development activity. Looking at just the "classic" GATK codebase (not GATK4), there have been 23 released versions of GATK (1.x through 3.x, not counting point releases such as 3.4-46). We've had 59 contributors (mostly internal but some external) who made over 14,000 code commits. Number of lines of code is a reaaaally controversial metric, but if you must know, we estimate that the GATK3 codebase has about 500,000 lines of code, excluding license text lines but including code comments <em>which are important and should totally count</em>.</p>

<h3>Usage</h3>

<p>The GATK has been run for at least 5,000,000 days' worth of of runtime (that's over 13,000 years -- and it's not just HaplotypeCaller being a bit slow) over 800,000,000 separate jobs by 30,000 distinct users, as reported by the GATK's <a rel="nofollow" href="https://www.broadinstitute.org/gatk/guide/article?id=1250">Phone Home</a> system. I believe that's not counting the Broad's own usage on <a rel="nofollow" href="https://twitter.com/BroadGenomics/status/733412184894537731">250,000 genomes and exomes</a> -- and it's certainly not counting anyone running GATK offline, behind a firewall or with a NO_ET key.</p>

<p>Now, as I mentioned in the <a rel="nofollow" href="https://www.broadinstitute.org/gatk/blog?id=7712">GATK 3.6 version highlights</a>, we removed Phone Home in the latest version, so we'll no longer be getting usage information from it or any future versions -- but I am holding out hope that enough people will be running older versions for a little while to get us to clocking a billion jobs. Because a billion, well, that's a cool number right there. But don't take this as an encouragement <em>not</em> to update to 3.6 as soon as possible!</p>

## Version highlights for GATK release 2.8

By Geraldine_VdAuwera

<p>Better late than never, here are the highlights of the most recent version release, GATK 2.8. This should be short and sweet because as releases go, 2.8 is light on new features, and is best described as a collection of bug fixes, which are all* dutifully listed in the corresponding release notes document. That said, two of the changes we've made deserve some additional explanation.</p>

<p><small>* Up to now (this release included) we have not listed updates/patches to Queue in the release notes, but will start doing so from the next version onward.</small></p>

<hr></hr><h3>VQSR &amp; bad variants: no more guessing games</h3>

<p>In the last release (2.7, for those of you keeping score at home) we trumpeted that the old <code class="code codeInline" spellcheck="false">-percentBad</code> argument of VariantRecalibrator had been replaced by the shiny new <code class="code codeInline" spellcheck="false">-numBad</code> argument, and that this was going to be awesome for all sorts of good reasons, improve stability and whatnot. Weeeeeeell it turned out that wasn't quite the case. It worked really well on the subset of analyses that we tested it on initially, but once we expanded to different datasets (and the complaints started rolling in on the forum) we realized that it actually made things worse in some cases because the default value was less appropriate than what <code class="code codeInline" spellcheck="false">-percentBad</code> would have produced. This left people guessing as to what value would work for their particular dataset, with a great big range to choose from and very little useful information to assist in the choice.</p>

<p>So, long story short, we (and by "we" I mean Ryan) built in a new function that allows the VariantRecalibrator to determine for itself the amount of variants that is appropriate to use for the "bad" model depending on the data. So the short-lived <code class="code codeInline" spellcheck="false">-numBad</code> argument is gone too, replaced by... nothing. No new argument to specify; just let the VariantRecalibrator do its thing.</p>

<p>Of course if you really want to, you can override the default behavior and tweak the internal thresholds. See the tool doc <a rel="nofollow" href="http://www.broadinstitute.org/gatk/gatkdocs/org_broadinstitute_sting_gatk_walkers_variantrecalibration_VariantRecalibrator.html">here</a>; and remember that a good rule of thumb is that if you can't figure out which arguments are involved based on that doc, you probably shouldn't be messing with this advanced functionality.</p>

<hr></hr><h3>Reference calculation model</h3>

<p>This is still a rather experimental feature, so we're still making changes as we go. The two big changes worth mentioning here are that you can now run this on reduced reads, and that we've changed the indexing routine to optimize the compression level. The latter shouldn't have any immediate impact on normal users, but it was necessary for a new feature project we've been working on behind the scenes (the single-sample-to-joint-discovery pipeline we have been alluding to in recent forum discussions). The reason we're mentioning it now is that if you use <code class="code codeInline" spellcheck="false">-ERC GVCF</code> output, you'll need to specify a couple of new arguments as well (<code class="code codeInline" spellcheck="false">-variant_index_type LINEAR</code> and <code class="code codeInline" spellcheck="false">-variant_index_parameter 128000</code>, with those exact values). This useful little fact didn't quite make it into the documentation before we released, and not specifying them leads to an error message, so... there you go. No error message for you!</p>

<hr></hr><h3>What's up, doc?</h3>

<p>That's all for tool changes. In addition to those, we have made a number of corrections in the tool documentation pages, updated the Best Practices (mostly layout, tiny bit of content update related to the VQSR -numBad deprecation) and made some minor changes to the website, e.g. updated the list of publications that cite the GATK and improved the Guide index somewhat (but that's still a work in progress).</p>

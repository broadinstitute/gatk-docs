## Version highlights for GATK release 2.5

By Geraldine_VdAuwera

<h3>Overview</h3>

<p>This is going to be a short one, folks. The 2.5 release is pretty much all about bug fixes, with a couple of exceptions that we'll cover below.</p>

<h3>Bug fixes</h3>

<p>Remember how we said that version 2.4 was going to be the least buggy ever? Well, that might have been a bit optimistic. We had a couple of stumpers in there -- and a flurry of little ones that were probably not novel (i.e. not specific to version 2.5) but finally bubbled up to the surface. We're not going to go over the bug fixes in detail, since the <a rel="nofollow" href="http://www.broadinstitute.org/gatk/guide/article?id=2572">release notes</a> include a comprehensive list. Basically, those are all fixed.</p>

<h3>Actual features!</h3>

<p>Well, not exactly new features, but noteworthy improvements to existing tools.</p>

<h4>- ReduceReads turns the squeeze dial up to eleven</h4>

<p>In addition to countless bug fixes, we've made drastic improvements to ReduceReads' compression algorithm, so you can now achieve much better compression rates without compromising on the retention of informative data. Keep in mind of course that as always, you'll see much bigger gains on certain types of data sets -- the higher the coverage in your original BAM files, the bigger the savings in file size and performance of the downstream tools.</p>

<h4>- HaplotypeCaller is faster and more accurate! No, really!</h4>

<p>We say this every time, and every time it's true: we've made some more improvements to the HaplotypeCaller that make it faster and more accurate. Well, it's still slower than the UnifiedGenotyper, in case you were going to ask (of course you were). But on the accuracy front, we say this without reservation or caveat: HC is now just as accurate as the UG for calling SNPs, and it is in a league of its own for calling indels. If you are even remotely interested in indels you should absolutely take it out for a spin. Go. Now.</p>

<h4>- DiagnoseTargets, all grown up</h4>

<p>Say goodbye to the mood swings and the pimples; it looks like this tool's awkward teenager phase is finally over. We've entirely reworked how DiagnoseTargets functions so it now uses a plugin system, which we think is much more convenient. This plugin system will be explained in detail in a forthcoming documentation article.</p>

<h4>- Functional annotation recovers some functionality</h4>

<p>You may be aware that we had imposed a freeze of sorts on the annotation database version that could be used with the snpEff annotation. Well, we're happy to report that the author of the snpEff software package has made some significant upgrades, including a feature called <em>GATK compatibility mode</em>. As a result there is no longer any version constraint. We'll be updating our documentation on using snpEff with GATK soon (-ish), but in the meantime, feel free to go forth and annotate away. Just make sure to consult the <a rel="nofollow" href="http://snpeff.sourceforge.net/SnpEff_manual.html#gatk">snpEff manual</a> for relevant information on using it with GATK.</p>

<h3>Deprecation alerts</h3>

<p>Even as the dev team giveth, the dev team taketh away.</p>

<p>A few annotations were removed from the VariantAnnotator stables (as listed in the release notes), mainly because they didn't work properly. With all the caveats about how GATK is research software, we're still committed to providing quality tools that do something close to what they're advertised to do, at the bare minimum. If something doesn't fulfill that requirement, it's out.</p>

<p>We've also disabled the auto-generation of fai/dict files for fasta references. I can hear some of you groaning all the way from here. Yes, it was convenient -- but far too buggy. Come on people, it's a one-liner using Picard. Oh, and we're no longer allowing the use of compressed (.gz) references either -- also too buggy. The space savings were simply not worth the headaches.</p>

## Why is HaplotypeCaller slower in the most recent GATK4 beta versions?

By Geraldine_VdAuwera

<p>Because it's saving its strength for the 4.0 general release <img class="emoji" src="https://gatkforums.broadinstitute.org/resources/emoji/wink.png" title=";)" alt=";)" height="20"></img></p>

<p>Many of the "early adopters" who have been testing out the GATK4 during its beta phase have pointed out that they saw significant speed improvements in early beta versions (yay!), but then when they upgraded to more recent betas (starting with 4.beta.4), they observed a return to the  slowness seen in GATK3 versions (boo!). This has understandably caused some concern to those who were attracted to the GATK4 beta version of HaplotypeCaller because of its promised speed improvements -- so, basically everyone.</p>

<p>The good news is that this is only a temporary artifact of some of our development and evaluation constraints, which forced us to remove some key improvements while we refine and evaluate the equivalence of results with the older version. We should be able to restore the HaplotypeCaller's speed improvements in the very near future -- in time for the GATK 4.0 planned for January 9, 2018.</p>

<p>If you're interested in understanding why we had to hobble the HaplotypeCaller in this way, please read on! Otherwise feel free to take our word for it.</p>

<hr></hr><p>There are two opposing forces in play when we migrate tools from the older GATK to the new 4.x framework. One is that we want to streamline the program's operation to make it run faster and cheaper. The other is that we have been asked by our internal stakeholders to produce an exact "tie-out" for the germline variant discovery pipeline that we run in production at the Broad (<em>i.e.</em> for a subset of tools including HaplotypeCaller). This means that the HaplotypeCaller we release in GATK 4.0 needs to produce exactly the same output (modulo some margins) as the one from version 3.8, to minimize disruption when the pipelines are migrated. That's a very high standard, and it's the right thing to do both from an operations standpoint and from a software engineering standpoint.</p>

<p>However, these two directives came into conflict because we realized, somewhere in the early beta stages, that some of the optimizations that were introduced to make HaplotypeCaller faster also created output differences that were outside of the acceptable margins. We believe that those differences may actually be improvements on the "old" results, but for the sake of the tie-outs we had to take them out temporarily -- hence the HaplotypeCaller went back to being slower than we'd like in the later beta releases.</p>

<p>We're confident we have a solution that will allow us to put the efficiency optimizations back in as soon as the final tie-out test results have been approved, which appears to be imminent. So by the time GATK4 is released into general availability in January, the new HaplotypeCaller should have all its superpowers back.</p>

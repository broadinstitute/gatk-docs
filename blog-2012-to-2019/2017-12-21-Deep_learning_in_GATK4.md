## Deep learning in GATK4

By samwell

<p><em>By Sam Friedman, deep learning developer in GATK4</em></p>

<p>Over the past couple of weeks, there's been a lot of chatter online --and in the press!-- about the applicability of deep learning to variant calling. This is exciting to me because I've been working on developing a deep learning approach to variant filtering based on Convolutional Neural Networks (CNNs), with the goal of replacing the VQSR filtering step in the GATK germline short variants pipeline. In fact, multiple groups have been picking up on the promise of deep learning and applying it to genomics.</p>

<p>As far as I'm aware, the first group to publish a deep learning-based approach for variant calling was the <a rel="nofollow" href="http://campagnelab.org">Campagne lab</a> at Cornell, who released their <a rel="nofollow" href="https://github.com/CampagneLaboratory/variationanalysis">variationanalysis</a> software in December 2016. There's also a group at Illumina that has been doing some interesting work with deep learning for predicting functional effects of variants, and some of my colleagues are currently working with researchers at Microsoft to see if CNNs can be used to discover complex Structural Variations (SVs) from short sequencing reads.</p>

<p>When the Google team made the source for DeepVariant public last week, I ran a few tests to compare it to the tool that I've been working on for the last six months (GATK4 CNN). The results are summarized in the table below.</p>

<p><img src="https://us.v-cdn.net/5019796/uploads/editor/c8/bvb8f6olfgm0.png" alt="" title="Comparison of VQSR, DeepVariant and GATK4 CNN" class="embedImage-img importedEmbed-img"></img></p>

<p>Both my tool and DeepVariant outperform VQSR (our "traditional" variant filtering algorithm), especially when VQSR is run on a single sample rather than on a cohort according to our current best practices. The delta isn't all that large on SNPs, but that's expected because germline SNPs are largely a solved problem, so all tools tend to do great there. The harder problem is indel calling, and that's where we see more separation between the tools. The good news is we're all doing better than VQSR on calling indels, which means progress for the research community, whatever else happens. It doesn't hurt my mood that these preliminary findings suggest GATK4 CNN is doing even better than DeepVariant <img class="emoji" src="https://gatkforums.broadinstitute.org/resources/emoji/smile.png" title=":)" alt=":)" height="20"></img></p>

<p>But keep in mind it's early days yet for deep learning in genomics, so a lot could still change as we all figure out how best to take advantage of these methods. Read on if you want to know more about how these results were generated and how my tool works under the hood.</p>

<hr></hr><p>The table above was created from a held out test set of variants enriched for difficult calls.  We compute the ROC curves from the VQSLOD scores from VQSR, QUAL scores from deep variant, and the scores from our CNN. SynDip, if you're not familiar with it, is the new truth set we're using as recently described by my colleague Yossi Farjoun <a rel="nofollow" href="https://software.broadinstitute.org/gatk/blog?id=10912">here</a>, and gnomAD is the <a rel="nofollow" href="http://gnomad.broadinstitute.org/">Genome Aggregation Database</a> produced by Daniel MacArthur and colleagues.</p>

<p>I'll be giving a talk about this work at the AGBT conference in Orlando in February 2018, and I'm aiming to have the tools ready for general release at that time. If you'd like to know more in the meantime, read on below, and feel free to check out the code on the <a rel="nofollow" href="https://github.com/broadinstitute/gatk/blob/master/src/main/java/org/broadinstitute/hellbender/tools/walkers/vqsr/CNNScoreVariants.java">GATK repo on Github</a>.</p>

<hr></hr><h3>How it works under the hood</h3>

<p>Deep learning is a pretty rich field of research that comprises many different approaches, and there's still a lot to be figured out in terms of what's going to transfer well to any particular problem. For example, the Campagne lab's approach starts from reads and, in <a rel="nofollow" href="http://campagnelab.org/interested-in-calling-genotypes-with-deep-learning/">their own words</a>, "maps structured data (alignment) to a small number of informative features, which can be used for efficient DL model training". In my work I've chosen to treat the problem as a variant <em>filtering</em> question (or <em>classification</em> in machine learning terms), where I take the output of the caller and use a deep learning process to filter that callset. It seems the Google team's approach is similar in that their software involves a first step that looks a lot like the HaplotypeCaller (which makes sense since Mark and Ryan wrote most of the HC's original code), then applies the deep learning classifier to the variant candidates.</p>

<p>My approach uses variants and annotations encoded as <em>tensors</em>, which carry the precise read and reference sequences, read flags, as well as base and mapping qualities. The modeling architecture I use is specifically designed to process these <em>read tensors</em>, and so avoids the extensive use of <a rel="nofollow" href="https://en.wikipedia.org/wiki/Convolutional_neural_network#Pooling">maxpooling</a> found in many image classification CNNs (maxpooling is motivated by the smoothness prior of natural images, where neighboring pixels tend to be the same, but DNA sequences are not smooth, so we reserve maxpooling for the deeper layers of the net). Because the tensor dimensions contain different types of data, I convolve each separately so as to allow deep layers in the net to see this meaningful difference. This architecture also integrates annotations seamlessly into the classification, which accelerates learning times dramatically and opens the door to unsupervised training down the road.</p>

<p>The animations below shows two read tensors one containing a heterozygous SNP the other with a homozygous deletion of a short tandem repeat.  You can see the reads with the individual base qualities, the reference sequence, the read flags and the mapping quality each in their own channel.</p>

<p><img src="https://us.v-cdn.net/5019796/uploads/editor/mm/ta0dz9pwctre.gif" alt="" title="" class="embedImage-img importedEmbed-img"></img></p>

<p><img src="https://us.v-cdn.net/5019796/uploads/editor/nj/1a5nahsm30yq.gif" alt="" title="" class="embedImage-img importedEmbed-img"></img></p>

<p>If you'd like to know more or if you're a developer yourself and want to discuss the pros and cons of various approaches, please feel free to comment in the discussion thread below.</p>

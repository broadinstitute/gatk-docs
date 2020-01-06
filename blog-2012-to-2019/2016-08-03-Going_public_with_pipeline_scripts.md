## Going public with pipeline scripts

By Geraldine_VdAuwera

<p>Folks, it really makes my day when I get to announce some good news that has been cooking for a long time. So this is going to be a very happy Humpday indeed.</p>

<p>The good news <small>(which I may have <a rel="nofollow" href="https://software.broadinstitute.org/gatk/blog?id=7392">hinted at</a> previously)</small> is that we are making our production pipeline scripts public, starting with the one that implements our Best Practices for data pre-processing and initial variant calling (aka GVCF generation) in whole genomes. Not only that, all Grch38/Hg38 resource files needed to run it, plus test data, are in a Google Cloud bucket. In time the bucket will replace our not-so-reliable FTP server as bundle sharing mechanism.</p>

<p><strong>Details below the fold, in FAQ format (sort of).</strong></p>

<p><strong>TL;DR:</strong> Take this script and run it, for it is our WGS processing production workflow (uBAMs -&gt; GVCF per-sample).</p>

<hr></hr><h4><em>Wait, what? The GATK dev team is sharing a pipeline script?</em></h4>

<p>Yup. I know some old-timers out there will be shaking their head in disbelief. In the past we were very reluctant to share scripts because our internal scripts were very infrastructure-specific and difficult to provide support for <small>(because, well, Scala)</small>. But now that we have a beautifully simple workflow language, <a rel="nofollow" href="https://software.broadinstitute.org/wdl/">WDL</a>, that can run pretty much anywhere <small>(<em>cough</em> cluster support coming soon <em>cough</em>)</small> we're a lot more comfortable sharing our scripts.</p>

<h4><em>What are you hoping to achieve with this?</em></h4>

<p>The scientific equivalent of peace, love and understanding: reproducibility, economy of effort and, uh, understanding. That last one applies equally, it turns out.</p>

<h4><em>No but really, what's the hidden agenda?</em></h4>

<p>Frankly? From our support team's point of view, it's all about reducing support burden. There's a big gap between our Best Practices recommendations, which were always meant to be a generic, <em>platonically ideal</em> representation of The Scientifically Correct Way to do variant calling with GATK, and an <em>actual pipeline implementation</em> that represents A Technically Valid Way to run the Best Practices in practice. That gap causes a lot of head-scratching, and so much energy being spent on reimplementing the same wheel over and over again <a rel="nofollow" href="https://software.broadinstitute.org/gatk/usermap.php">across the globe</a>. And in the process, so many forum questions.</p>

<p>So we hope that sharing this script <small>(and others to come)</small> will help fill the gap by providing researchers with either a fully-baked solution <small>(if their use case is the same as ours)</small> or at least a solid blueprint that they can tweak without too much difficulty. In theory that should lead to fewer questions about how to run Best Practices, and more time for everyone to do more interesting things, like cure cancer and/or watch wacky cat videos on YouTube.</p>

<h4><em>That sounds reasonable. Now get to the point --the script?</em></h4>

<p>Alright, alright, I'm getting to the interesting stuff.</p>

<p>What we're sharing today is the workflow that we use in production to process the Broad's whole genomes, from unaligned BAMs (uBAMs) all the way to HaplotypeCaller GVCFs. Its official name is PublicPairedSingleSampleWf because it's designed to run per sample on paired-end reads; though we may sometimes refer to it as just "the single-sample pipeline". <small>Don't look at me, the engineers named it. I just added "Public" at the front.</small></p>

<p>Note that it's really only half of the pipeline; the second half, which runs the GVCFs through joint discovery and filtering, is done by a second script. We're comfortable sharing the first script because it's mature enough that we don't anticipate it changing much. In contrast, the second one is still being actively worked on <small>(as we port our local pipelines <a rel="nofollow" href="https://software.broadinstitute.org/gatk/blog?id=7372">to the cloud</a>)</small> and we can't commit to releasing and supporting it quite yet. But we will do so as soon as we can.</p>

<h4><em>SHOW ME THE SCRIPT!</em></h4>

<p>That's not a question, but okay. The PublicPairedSingleSampleWf script is written in <a rel="nofollow" href="https://software.broadinstitute.org/wdl/">WDL</a> and lives <a rel="nofollow" href="https://github.com/broadinstitute/wdl/tree/develop/scripts/broad_pipelines">here</a>, in a directory of the WDL repository dedicated to hosting all the pipeline scripts that we will make public. Crucially, the script comes with <strong>all the things</strong> that you need to run it:</p>

<ul><li>The header specifying scope of application, expectations and input requirements;</li>
<li>The DockerHub identifier of the docker image containing all the software that is used in the pipeline (but note that the usual <a rel="nofollow" href="https://software.broadinstitute.org/gatk/download/licensing">GATK licensing</a> rules apply);</li>
<li>An example JSON file that specifies inputs, including resources and test data, which are all available in a Google Cloud bucket;</li>
<li>An example JSON file that specifies cloud-based runtime options;</li>
<li>A document that explains everything that happens in the pipeline <small>(what tools are run, in what order and with what parameters)</small> with particular focus on how the implementation relates to the theoretical Best Practices.</li>
</ul><h4><em>Why no direct links to the various files?</em></h4>

<p>I'm not linking to the files directly because they are date-stamped <small>(pending a more formal versioning system TBD by the engineering team)</small> and the current versions will eventually be supplanted by newer ones -- but past versions remain accessible in the <a rel="nofollow" href="https://github.com/broadinstitute/wdl/tree/develop/scripts/broad_pipelines/archive">archive</a>.</p>

<p>In the near future, and as we add more scripts, we will add versioned links on the corresponding Best Practices pages to make it super easy to find these reference implementation scripts and accompanying resources.</p>

<h4><em>So does it actually work out of the box? How do I run it?</em></h4>

<p>Well, yes, with caveats.</p>

<p>It works out of the box, no questions asked, if you have access to a service on Google Cloud that runs the Cromwell execution engine. Like we do. <small>(What do you mean, "that's not very helpful"?)</small></p>

<p>If you don't <small>(hello, majority of the world)</small>, there is a light at the end of the tunnel, in two forms:</p>

<ul><li><p>It will run on the <a rel="nofollow" href="https://software.broadinstitute.org/firecloud/">FireCloud</a> platform <small>(which, although presented largely as a cancer genome analysis platform, is not technically restricted to cancer work)</small> pending a few tweaks to the FireCloud backend. Not sure what is the ETA but it's on the engineering group's roadmap. We'll announce widely when this is ready.</p></li>
<li><p>Our friendly collaborators at Google are putting the final touches to a Cromwell execution service that will run this WDL on the <a rel="nofollow" href="https://cloud.google.com/genomics/">Google Genomics</a> platform. We'll announce that when it's ready as well.</p></li>
</ul><p>The cloud-free alternative for now is to run it on your local machine <small>(cluster support coming soon)</small>. You'll need to download all the files from the cloud bucket or substitute your own. The <a rel="nofollow" href="https://software.broadinstitute.org/wdl/userguide/index">Getting Started</a> section of the WDL documentation includes <a rel="nofollow" href="https://software.broadinstitute.org/wdl/userguide/article?id=6671">installation</a> and <a rel="nofollow" href="https://software.broadinstitute.org/wdl/userguide/execution">execution</a> instructions. The Cromwell engine will ignore the cloud-specific settings that are included in the WDL when you run locally, so the script should run seamlessly on the local backend -- but if you run into any trouble at the Crom/WDL level, you're welcome to ask for help in the <a rel="nofollow" href="http://gatkforums.broadinstitute.org/wdl">WDL forum</a>. Conveniently, it is a sister forum to the GATK one, and does not require a separate registration. This is also the case of the <a rel="nofollow" href="http://gatkforums.broadinstitute.org/firecloud">FireCloud forum</a>, by the way.</p>

<h4><em>Can anyone modify and redistribute the script?</em></h4>

<p>Sure, feel free! But if you do so, please also modify the filenames and header of the script accordingly, to avoid any misunderstandings about the identity and origin of the script you redistribute. Specifically, we'd like to avoid situations where someone asks us for help troubleshooting a script that they think is ours but that turns out to have been modified by someone else. We'll still try to help, but will know to look for differences that could explain any unexpected behavior. Time saved dealing with that sort of problem translates to more/better documentation and support of other things.</p>

<h4><em>How can I get notified when scripts are added or updated?</em></h4>

<p>Subscribe to <a rel="nofollow" href="https://software.broadinstitute.org/gatk/documentation/article?id=27">forum notifications</a>, to the GATK blog <a rel="nofollow" href="http://gatkforums.broadinstitute.org/gatk/categories/announcements/feed.rss">RSS feed</a>, or follow <a rel="nofollow" href="https://twitter.com/gatk_dev">@GATK_dev</a> and/or <a rel="nofollow" href="https://twitter.com/wdl_dev">@WDL_dev</a> on Twitter.</p>

## Test drive GATK Best Practices workflows on Terra

By Geraldine_VdAuwera

<p>Last week, I wrote about a new initiative we're kicking off to <a rel="nofollow" href="https://software.broadinstitute.org/gatk/blog?id=24102">make it easier to get started with GATK</a>. Part of that involves making it easier for anyone to try out the Best Practices workflows without having to do a ton of work up front. That's a pretty big can of worms, because for a long time the Best Practices were really meant to describe at a high level the key GATK (and related) tools/steps you need to run for a particular type of analysis (e.g. germline short variant discovery). They weren't intended to provide an exact recipe of commands and parameters… Yet that's what many of you have told us you want.</p>

<p>For the past couple of years we've been providing actual reference implementations in the form of workflows written in the <a rel="nofollow" href="http://www.openwdl.org/">Workflow Description Language</a>, but that still leaves you with a big old learning curve to overcome before you can actually run them. And we know that for many of you, that learning curve can feel both overwhelming and unwarranted - especially when you're in the exploratory phase of a project and you're not even sure yet that you'll end up using GATK.</p>

<p>To address that problem, we've set up all the GATK Best Practices workflows in public workspaces on our cloud platform, <a rel="nofollow" href="https://app.terra.bio">Terra</a>. These workspaces feature  workflows that are fully configured with all commands and parameters, as well as resource files and example data you need to run them right out of the box. All it takes is a click of a button! (Almost. There's like three clicks involved, for real).</p>

<p></p><div style="text-align: center;"><img src="https://us.v-cdn.net/5019796/uploads/editor/zc/8cscvu21fken.gif" alt="Homer Simpson computer meme" class="embedImage-img importedEmbed-img"></img></div>

<p>Let me show you  one of these workspaces, and how you would use it to try out Best Practices pipelines. It should take about 15 mins if you follow along and actually click all the things. Or you can just read through to get a sense of what's involved.</p>

<hr></hr><h2>GATK Best Practices workspaces live in the Terra Showcase library</h2>

<p>Terra has a growing <a rel="nofollow" href="https://app.terra.bio/#library/showcase">library of workspaces</a> showcasing a variety of analysis use cases and tools, including GATK. You can get to it by clicking the "View Examples" button on the <a rel="nofollow" href="https://app.terra.bio">Terra landing page</a> or selecting "Terra Library" then "Showcase" in the dropdown menu (top left icon, horizontal stripes) from any page.</p>

<p><em>If you go there now (go on, we'll wait for you) you'll be asked to log in with a Google identity. If you don't have one already you can create one, and choose to either create a new Gmail account for it or associate your new Google identity with your existing email address. See <a rel="nofollow" href="https://support.terra.bio/hc/en-us/articles/360028235911">this article</a> for step-by-step instructions on how to register. Once you've logged in, look for the big green banner at the top of the screen and click "Start trial" to take advantage of the free credits program. As a reminder, access to Terra is free but Google charges you for compute and storage; the credits (a $300 value!) will allow you to try out the Best Practices for free.</em></p>

<p><img src="https://us.v-cdn.net/5019796/uploads/editor/ex/y0n8fens6r15.gif" alt="" title="" class="embedImage-img importedEmbed-img"></img></p>

<h2>Let's try out the germline short variants pipeline</h2>

<p>The <a rel="nofollow" href="https://app.terra.bio/#library/showcase">Terra Showcase</a> is organized in two major categories: "GATK4 Examples" are all the Best Practices workspaces, and "Featured Workspaces" are various others (including GATK workshop materials -- I'll cover that in an upcoming blog post in this series). Find the <a rel="nofollow" href="https://app.terra.bio/#workspaces/help-gatk/Germline-SNPs-Indels-GATK4-hg38">"Germline-SNPs-Indels-GATK4-hg38"</a> card and click on it to access a read-only version of the workspace. If you want to be able to actually run things, you need to clone it. To do that, expand the workspace action menu (three-dot icon, top right) and select the "Clone" option. The resulting workspace clone belongs to you. See the animation below or <a rel="nofollow" href="https://support.terra.bio/hc/en-us/articles/360026130851">this article</a> for an exact step-by-step walkthrough.</p>

<p><img src="https://us.v-cdn.net/5019796/uploads/editor/38/i2hin3zxl7ld.gif" alt="" title="" class="embedImage-img importedEmbed-img"></img></p>

<p>You can find a detailed description of the workspace contents in the <strong>Dashboard</strong> tab, including instructions and links to relevant documentation. There's a lot of interesting info in there that we could go into, but let's zip straight over to the Data tab to look at the example data that we're providing as input for testing the pipeline.</p>

<h2>Example data</h2>

<p>Go to the <strong>Data</strong> tab of the workspace and click on "sample" in the left hand menu to see the table of input samples we provide. This is all metadata; the actual data files live in Google Cloud Storage. Later I'll point you to docs where you can learn more about how that works and how you can import your own data securely (it stays private unless you choose to share it) but for now, I just want to point out that in this workspace, we provide a full whole genome (WGS) input dataset in CRAM format for full-scale testing as well as a "small" downsampled dataset in BAM format for running faster tests, typically as sanity checks.</p>

<p></p><div style="text-align: center;"><img src="https://us.v-cdn.net/5019796/uploads/editor/q7/lvqs54dacxqq.png" alt="" title="" class="embedImage-img importedEmbed-img"></img></div>

<p>There's also a table called "Workspace Data" that lists resource files like the reference genome, known variants files, interval lists and so on -- everything you need to run the pipeline. So let's do that now.</p>

<h2>Pre-configured Best Practices workflows</h2>

<p>Finally, we get to the good stuff! The workflows are set up in the <strong>Tools</strong> tab of your workspace. In this particular one, you should see three workflows corresponding to the pre-processing, single-sample calling and joint variant discovery portions of the Best Practices for germline SNP &amp; Indel discovery, respectively:</p>

<ul><li><strong>1-Processing-For-Variant-Discovery</strong> takes the raw data in unmapped BAM format to analysis-ready BAMs (we have <a rel="nofollow" href="https://app.terra.bio/#workspaces/help-gatk/Sequence-Format-Conversion">conversion utilities</a> for dealing with FASTQ data);</li>
<li><strong>2-Haplotypecaller-GVCF</strong> takes the output from the first WDL and does the variant calling per sample, producing a GVCF;</li>
<li><strong>3-Joint-Discovery</strong> implements the joint calling and VQSR filtering portion to return a VCF file and its index.</li>
</ul><p>The three workflows are designed to be run back-to-back. Each workflow's outputs will get added to the data table in the appropriate columns, so that the next workflow will find the right inputs automatically.</p>

<p>Click on the first tool to load up the details; the page will open at the inputs definition form, which is pre-filled for you. To launch the workflow, select some data to run it on, hit the "Run analysis" button then click "Launch" in the popup dialog, as shown in the animation below.</p>

<p><img src="https://us.v-cdn.net/5019796/uploads/editor/pg/it3dv5ivcri3.gif" alt="" title="" class="embedImage-img importedEmbed-img"></img></p>

<p>That's all it takes! Congratulations, especially if this is the first GATK pipeline you've ever run.</p>

<p>You can check its status in the Job History tab; as the system processes your request, the status label will change from “Queued” to “Submitted” to “Done” (remember to refresh the page to see the current status). Behind the scenes, Terra will interpret the workflow script, dispatch jobs for execution on Google Cloud virtual machines (with parallelization in all the right places), move data around as needed, and eventually write the results to your workspace storage bucket. The best part of all that? You don't have to worry about any of it :-)</p>

<p><em>The Dashboard lists expected runtime and costs of each workflow for each input dataset provided for testing. For example, you see that you can run the complete pipeline on the 3GB sample NA12878_24RG_small in about six hours, for less than the cost of a medium Dunkin's coffee.</em></p>

<p></p><div style="text-align: center;"><img src="https://us.v-cdn.net/5019796/uploads/editor/x4/z4mybzqt1mcr.jpg" alt="Dunkin Donuts meme" class="embedImage-img importedEmbed-img"></img></div>

<h2>What's next?</h2>

<p>At this point you should have a sense of what it's like to test drive GATK workflows on Terra. If you'd like to learn more about how you can take further advantage of these resources, e.g. by uploading your own data to evaluate how our pipelines behave on that, have a look at this <a rel="nofollow" href="https://support.terra.bio/hc/en-us">quick start guide</a>. You may also want to check out <a rel="nofollow" href="https://app.terra.bio/#workspaces/help-gatk/Sequence-Format-Conversion">this handy utility workspace</a> that contains preconfigured tools for converting between various input formats, or look at the other GATK Best Practices workspaces in the <a rel="nofollow" href="https://app.terra.bio/#library/showcase">Terra Showcase</a>.</p>

<p>Next week I'll walk you through using the workspaces that we use in workshops to teach the component steps of each pipeline within Jupyter notebooks. If you want a sneak peek, have a look at <a rel="nofollow" href="https://app.terra.bio/#workspaces/help-gatk/GATKTutorials-Germline-June2019">this tutorial workspace</a>, where all the action is in the Notebooks tab.</p>

<p><img src="https://us.v-cdn.net/5019796/uploads/editor/bg/8dbc5qb5c44j.png" alt="" title="" class="embedImage-img importedEmbed-img"></img></p>

<h2>And of course, we're always here to help</h2>

<p>It's the same crack team that provides frontline support for both Terra and GATK, so whenever you're using Terra, you can expect the same speedy and caring support you're used to getting on the GATK forum. In fact, you can even write to the support team privately through the Terra Support helpdesk, which you can't do in the GATK forum.</p>

<p>Let us know how it goes; we'd love to hear from you on how we could make this even more useful.</p>

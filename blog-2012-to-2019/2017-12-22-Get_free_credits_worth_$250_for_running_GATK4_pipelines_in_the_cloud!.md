## Get free credits worth $250 for running GATK4 pipelines in the cloud!

By Tiffany_at_Broad

<p>With the GATK4 release just around the corner, we wanted to make it easy for everyone to try out the new pipelines without going through a whole lot of setup. So we're setting them all up in ready-to-run workspaces on <a rel="nofollow" href="https://software.broadinstitute.org/firecloud/">FireCloud</a>, which is a secure, freely-accessible, open-source analysis portal we built on Google Cloud (think Galaxy but more scalable). The pipelines are preconfigured according to our Best Practices, so it'll be just a matter of a few clicks to run any pipeline you like on the preloaded example datasets -- or, with a few more (simple) steps, to run them on your own data. All this without ever touching a command line, unless you're the CLI-over-GUI type, in which case you're welcome to use the FireCloud APIs vis <a rel="nofollow" href="https://api.firecloud.org/">Swagger</a> or the <a rel="nofollow" href="https://github.com/broadinstitute/fiss">FISS Python bindings</a> to do all this programmatically.</p>

<p>But that's not all -- we're super excited to announce that we're giving out free credits for running the pipelines! Normally you would have to pay Google for the compute and storage costs -- we make the portal and tools available for free, but Google runs the machines, and they charge you for what you use. However, if you apply ASAP, you can get $250 worth of credits for free! That should be more than enough to test out the new pipelines; with that amount of credits you should be able to get real work done toward your research. And you can run any pipelines you want as long as they're written in WDL, so you can run other tools besides GATK.</p>

<p>The FireCloud free credits program starts January 9th, 2018, when GATK4 is released and the new pipelines are made available in FireCloud. We have secured funding to give out $250 worth of credits each to 1,000 people. Credits will be allocated on a first come, first serve basis, so the sooner you sign up, the more likely you are to receive credits.</p>

<p>To take advantage of this unique opportunity, all you need to do is register for an account on the FireCloud portal (which is itself always free and open to all) and sign up for the free credits program. Read on below for details about signing up and which pipelines will be featured.</p>

<hr></hr><h3>Instructions for signing up</h3>

<p>Follow these three steps:</p>

<ol><li><p><a rel="nofollow" href="https://portal.firecloud.org/">Register</a> for an account in FireCloud. This requires a Google identity, but don't worry -- if you don’t have a Gmail account to use, you can link your current email address to a Google identity by following <a rel="nofollow" href="https://software.broadinstitute.org/firecloud/documentation/article?id=9846">these instructions</a>.</p></li>
<li><p>Apply <a rel="nofollow" href="https://docs.google.com/forms/d/e/1FAIpQLSeJu66dmqkz4DYK1R8Vy4PG_pm6JAGUEWqw-MmRj9Y1bhGlzQ/viewform?usp=sf_link#responses">here</a> with the email address you used to register for FireCloud, so we can assign you the free credits. You will receive an email when the free credits are available in FireCloud.</p></li>
<li><p>When you log into FireCloud on or after January 9, you will see a banner inviting you to start your free trial. Simply click the "Start" button in the banner to get started.</p></li>
</ol><p>Full terms and conditions will be posted in the FireCloud documentation and will be repeated to you before you commit to anything.</p>

<p>Note that we reserve the right to accept or reject applications for credits at our discretion. This is intended to protect against any attempts to abuse the credits program, since our goal here is to give as many people as possible the opportunity to try out the pipelines.</p>

<h3>Do the credits expire? What happens next?</h3>

<p>You will have two months (60 days) to use the free credits from the date you click the “Start” button in FireCloud. We encourage you to begin by checking out the GATK4 workflows in the preloaded workspaces, which will be linked individually on the GATK4 launch page and featured prominently within FireCloud itself. Documentation for the pipelines will include cost estimates for each of the preloaded examples so you can gauge how far your credits will go depending on what you choose to run.</p>

<p>There is no obligation to continue using FireCloud after your free credits expire, and you will be presented with options to save any work you got done during that time.</p>

<h3>Which GATK4 pipelines will be featured?</h3>

<p>We are preparing individual workspaces for each of the major use cases that will be supported in GATK 4.0:</p>

<p><strong>- Germline short variants:</strong> the HaplotypeCaller / joint calling workflows for germline SNPs and indels<br><strong>- Somatic short variants:</strong> the Mutect2 workflow for somatic SNPs and indels<br><strong>- Somatic copy number:</strong> the GATK4 CNV and ACNV workflows for somatic CNV and allelic CNV discovery (including PON creation)</p>

<p>We foresee additional workflows being released in the first quarter of 2018 to cover germline copy number variation (GATK4 gCNV), pathogen sequence detection (PathSeq) and deep learning applied to short variant filtering (GATK4 CNN). These will be added to FireCloud when the corresponding GATK software is released.</p>

<p>Each of these pipelines will be set up in a dedicated workspace in FireCloud, along with complementary workflows (mapping and pre-processing plus commonly-requested format conversions) and appropriate publicly-accessible example data for running pipeline tests. All workflow scripts (WDL) will be viewable and exportable, with inputs and outputs fully configured according to our Best Practices recommendations.</p>

<h3>Getting started with GATK4 in FireCloud</h3>

<p>Instructions will be provided for getting started quickly and painlessly, including <a rel="nofollow" href="https://www.youtube.com/watch?v=G_HTuXYZayU&amp;list=PLbhQIHW7hn4jt-_gw8FN-ax6W2sVPYdcQ" title="short videos">short videos</a> showing step by step how to run the workflows and how to bring in your own data. We're still putting the finishing touches on the Quick Start guide, which will go live shortly before the GATK4 release on Jan 9, but in the meantime you can check out <a rel="nofollow" href="https://www.youtube.com/watch?v=G_HTuXYZayU" title="this video">this video </a>for a preview.</p>

<p>FireCloud has its own <a rel="nofollow" href="https://software.broadinstitute.org/firecloud/documentation/">documentation website</a> and <a rel="nofollow" href="https://gatkforums.broadinstitute.org/firecloud">forum</a>, but the support is done by the same team that supports GATK, so you can rest assured we'll be there to help you every step of the way.</p>

<p>We hope you will take advantage of these credits to take GATK4 out for a spin! If you have any questions about the free credits, please comment in the discussion thread below.</p>

<hr></hr><h3>Who is paying for the credits? What does Broad get out of it?</h3>

<p>Our friends at Google Cloud Platform are generously footing the bill for this credits program. We at the Broad Institute are not getting any share of any revenue that may be generated by GCP as a result of this program. By that I mean that if you continue using Google Cloud for your work on your own dime after you have exhausted your credits, we will <strong>not</strong> get a cut of the money you pay to Google.</p>

<p>For us (the GATK team), the FireCloud portal and cloud-based platforms in general present an unparalleled opportunity to make our tools available in a format that is much easier to support, since it removes a lot of the complexity involved with dealing with lots of different local infrastructures. The more people use this kind of platform to run our pipelines, the easier it becomes for us to help ensure that the pipelines are running smoothly and correctly for everyone. We are very aware that to many of you, moving your work to the cloud is a big logistical and cultural shift, so we hope that this program will grease the wheels and make it easier for you to try the cloud (and GATK4 itself) on for size. If you find it doesn't suit you, you'll still be able to go back to the traditional method of downloading the software and deploying it on your own infrastructure.</p>

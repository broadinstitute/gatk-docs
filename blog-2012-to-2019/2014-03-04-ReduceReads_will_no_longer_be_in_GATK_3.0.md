## ReduceReads will no longer be in GATK 3.0

By Geraldine_VdAuwera

<h4>This is an important heads-up regarding the GATK 3.0 release.</h4>

<p>The purpose of the ReduceReads algorithm was to enable joint analysis of large cohorts by the UnifiedGenotyper. The new workflow for joint discovery, which involves doing a single-sample pass with the HaplotypeCaller in gVCF mode followed by a joint analysis on multiple sample gVCFs, renders the compression step obsolete.</p>

<p>In addition, based on our most recent analyses, we have come to the conclusion that the quality of variant calls made on BAMs compressed with ReduceReads is inferior to the standard targeted by GATK tools. In comparison, the results obtained with the new workflow are far superior.</p>

<p>For these reasons, we have made the difficult decision to remove the ReduceReads tool from version 3.0 of the toolkit. To be clear, reduced BAMs will NOT be supported in GATK 3.0.</p>

<p>We realize that this may cause some disruption to your existing workflows, and for that we apologize. Please understand that we are driven to provide tools that produce the best possible results. Now that all the data is in, we have found that the best results cannot be achieved with reduced BAMs, so we feel that the best thing to do is to remove this inferior tool from the toolkit, and promote the new tools.</p>

<p>As always we welcome your comments, and we look forward to showing you how the new calling workflow will yield superior results.</p>

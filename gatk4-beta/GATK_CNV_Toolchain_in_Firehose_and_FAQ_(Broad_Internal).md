## GATK CNV Toolchain in Firehose and FAQ (Broad Internal)

By LeeTL1220

<p>We have put the GATK4 Somatic CNV Toolchain into Firehose.  Please copy the below workflows from <code class="code codeInline" spellcheck="false">Algorithm_Commons</code></p>

<pre class="code codeBlock" spellcheck="false">GATK_Somatic_CNV_Toolchain_Capture
GATK_Somatic_CNV_Toolchain_WGS
</pre>

<p>For questions and discussions, not just specific to Firehose, please see the <em>GATK 4 forum</em>:  <a href="http://gatkforums.broadinstitute.org/gatk/categories/gatk-4-alpha" rel="nofollow">http://gatkforums.broadinstitute.org/gatk/categories/gatk-4-alpha</a></p>

<p><strong>Who do I contact with an issue?</strong></p>

<p>First, make sure that your question is not here or in another forum post.<br><em>If it is a Firehose issue or you are not sure</em>, email <code class="code codeInline" spellcheck="false">pipeline-help@broadinstitute.org</code>.<br>
If you are sure that it is an issue with GATK CNV, ACNV, or GetBayesianHetPulldown, post to the forum.</p>

<p><strong>What is GATK CNV vs. ACNV and which are run in the workflows above?</strong></p>

<ul><li>GATK CNV estimates total copy ratio and performs segmentation and (basic) event calling.  This tool works very similarly to ReCapSeg (for now).</li>
<li>GATK ACNV creates credible intervals for copy ratio and minor allelic fraction (MAF).  Under the hood, this tool is very different from Allelic CapSeg, but it can produce a file that can be ingested by ABSOLUTE (i.e. file is in same format produced by Allelic CapSeg)</li>
<li>Both GATK CNV and ACNV are in the workflows above.</li>
</ul><p><strong>Are the results (e.g. sensitivity and precision) better than ReCapSeg in the GATK CNV toolchain?</strong></p>

<p>If you talk about running without the allelic integration, then the results are equivalent.  If you want more details, ask in the forum or invite us to talk to you -- we have a presentation or two about this topic.</p>

<p><strong>Do I run these workflows on Pair Sets or Individual Sets?</strong></p>

<p>Individual Sets</p>

<p><strong>What entity types do the tasks run on?</strong></p>

<p>Samples and Pairs.  I realize that the above question says to run the workflow on Individual Sets.  This is to work around a Firehose issue.</p>

<p><strong>What are the caveats around WGS?</strong></p>

<ul><li>The total copy number tasks (similar to ReCapSeg) take about a tenth of the time as ReCapSeg, assuming good NFS performance.  This is a good thing.</li>
<li>The allelic tasks (GetBayesianPulldown and Allelic CNV) take a <em>very</em> long time to run.  Over a day of runtime is not uncommon.  In the next version of the GATK4 CNV Toolchain, we will have addressed this issue, but due to dispatch limitations, Firehose may not be able to fully capitalize on these improvements.</li>
<li>The runtimes in general are very very sensitive to the filesystem performance.</li>
<li>The results still have the same oversegmentation issues that you will see in ReCapSeg.  There is a GC correction tool, but this has not been integrated into the Firehose workflow.</li>
<li>There is a bug in a third-party library that limits the size of a PoN.  This is unlikely to be an issue for capture, but can become a problem for WGS.  For more details, please see gatkforums.broadinstitute.org/gatk/discussion/7594/limits-on-the-size-of-a-pon</li>
</ul><p><strong>What about the future of ReCapSeg?</strong></p>

<p>We are phasing out ReCapSeg, for many reasons, everywhere -- not just Firehose.  If you would like more details, post to the forum and we'll respond.</p>

<p><strong>What about the future of Allelic CapSeg?</strong></p>

<p>We have never supported (and never will support) Allelic CapSeg and cannot answer that question.  We have some results comparing Allelic CapSeg and GATK ACNV.  We can show you if you are interested (internal to Broad only).</p>

<p><strong>Why are there fewer plots than in ReCapSeg?</strong></p>

<p>We did not include plots that we did not believe were being used.  If you would like to include additional plots, please post to the forum.</p>

<p><strong>How is the GATK 4 CNV toolchain workflow better than the ReCapSeg workflow?</strong></p>

<p>1)  Faster.  On exome, ReCapSeg takes ~105 minutes per case sample.  GATK CNV takes &lt; 30 minutes.  Both time estimates assume good performance of NFS filesystem.<br>
2)  The workflows above include allelic integration results, from the tool GATK ACNV.  These results are analogous to what Allelic CapSeg produces.<br>
3)  The workflow above produces results compatible with ABSOLUTE and TITAN.  I.e. the results can be used as input to ABSOLUTE or TITAN.<br>
4)  All future improvements and bugfixes are going into GATK, not ReCapSeg.  And many improvements are coming....<br>
5)  The workflows produce germline heterzygous SNP call files.<br>
6)  The ReCapSeg WGS workflow no longer works.</p>

<p><strong>Are there new PoNs for these workflows?</strong></p>

<p>Yes, but the PoN locations are already populated, if you run the workflows properly.  Users do not need to do anything.</p>

<p><strong>Is the correct PoN automatically selected for ICE vs. Agilent samples?</strong></p>

<p>Yes, if you run the workflow.</p>

<p><strong>Is there a PoN creation workflow in Firehose?</strong></p>

<p>No.  Never going to happen.  Don't ask.  See the forum for instructions (and a Queue workflow) to create PoNs.</p>

<p><strong>Can I run ABSOLUTE from the output of GATK ACNV?</strong></p>

<p>Yes.  The annotations are <code class="code codeInline" spellcheck="false">gatk4cnv_acnv_acs_seg_file_capture</code> (capture) and <code class="code codeInline" spellcheck="false">gatk4cnv_acnv_acs_seg_file_wgs</code> (WGS).</p>

<p><strong>Can I run TITAN from the output of GATK ACNV?</strong></p>

<p>Yes, though there has been little testing.  The annotations are <code class="code codeInline" spellcheck="false">gatk4cnv_acnv_acs_seg_file_capture</code> and <code class="code codeInline" spellcheck="false">gatk4cnv_acnv_acs_seg_file_wgs</code>.</p>

<p><strong>Do the workflows above include Oncotator gene lists?</strong></p>

<p>Yes.</p>

<p><strong>Is the GATK4 CNV Toolchain in alpha?</strong></p>

<p>Technically, the whole GATK4 is in alpha, but that includes more than just the GATK CNV toolchain.  We are confident that the version in the workflows above produce high quality results.  Please tell us if you find otherwise!</p>

<p><strong>These workflows have Picard Target Mapper.  Isn't that going to cause me to have to rerun all of my jobs (e.g. MuTect)?</strong></p>

<p>The workflows above will rerun Picard Target Mapper, but only new annotations are added.  All previous output annotations of Picard Target Mapper should be populated with the same values.  This will look as if it outdated mutation calling (MuTect) and other tasks, but the rerunning will be job avoided.</p>

<p><strong>Can I do the tumor-only GATK ACNV workflow?</strong></p>

<p>For exome that is working well, but is not available in Firehose.  If you would like to see evaluation data for tumor-only on exome, we can show you (internal to Broad only).  Please contact us if you need this in Firehose and we will work with you to set it up.</p>

<p><strong>What are all of the annotations produced?</strong></p>

<p>Where applicable, each of the list below also has a <code class="code codeInline" spellcheck="false">*_wgs</code> counterpart...<br>
Sample annotations:</p>

<ul><li>gatk4cnv_seg_file_capture -- seg file of GATK CNV.  This file is analogous to the ReCapSeg seg file.</li>
<li>gatk4cnv_tn_file_capture -- tangent normalized (denoised) target copy ratio estimates of GATK CNV.  This file is analogous to the ReCapSeg tn file.</li>
<li>gatk4cnv_pre_tn_file_capture -- coverage profile (i.e. target copy ratio estimates without denoising) of GATK CNV.  This file is analogous to the ReCapSeg tn file.</li>
<li>gatk4cnv_betahats_capture -- Tangent normalization coefficients used in the projection.  This is in the weeds.</li>
<li>gatk4cnv_called_seg_file_capture  -- output called seg file of GATK CNV.  This file is analogous to the ReCapSeg called seg file.</li>
<li><p>gatk4cnv_oncotated_called_seg_file_capture -- gene list file generated from the GATK CNV segments</p></li>
<li><p>gatk4cnv_dqc_capture (coming later) -- measure of noise reduction in the tangent normalization process.  Lower is better.</p></li>
<li>gatk4cnv_preqc_capture (coming later) -- measure of noise before tangent normalization</li>
<li>gatk4cnv_postqc_capture (coming later) -- measure of noise after tangent normalization</li>
<li>gatk4cnv_num_seg_capture (coming later) -- number of segments in the GATK CNV output</li>
</ul><p>Pair annotations:</p>

<ul><li>gatk4cnv_case_het_file_capture -- het pulldown file for the tumor sample in the pair.</li>
<li>gatk4cnv_control_het_file_capture -- het pulldown file for the normal sample in the pair.</li>
<li><p>gatk4cnv_acnv_seg_file_capture -- ACNV seg file with confidence intervals for copy ratio and minor allelic fraction.</p></li>
<li><p>gatk4cnv_acnv_acs_seg_file_capture -- ACNV seg file in a format that looks as if it was produced by AllelicCapSeg.  Any segments called as "balanced" will be pegged to a MAF of 0.5.  This file is ready for ingestion by ABSOLUTE.</p></li>
<li>gatk4cnv_acnv_cnv_seg_file_capture -- ACNV seg file in a format that looks as if it was produced by GATK CNV</li>
<li>gatk4cnv_acnv_titan_het_file_capture -- het file in a format that can be ingested by TITAN</li>
<li>gatk4cnv_acnv_titan_cr_file_capture -- target copy ratio estimates file in a format that can be ingested by TITAN</li>
<li>gatk4cnv_acnv_cnloh_balanced_file_capture -- ACNV seg file with calls for whether a segment is balanced or CNLoH (or neither).</li>
</ul><p><strong>Do the workflows also run on the normals?</strong></p>

<p>GATK CNV, yes.<br>
GATK ACNV, no.<br>
There is a het pulldown generated for the normal, as a side effect, when doing the het pulldown for the tumor.</p>

<p><strong>What about array data?</strong></p>

<p>The GATK4 CNV tools do not run on array data.  Sequencing data only.</p>

<p><strong>Do we still need separate PoNs if we want to run on X and Y?</strong></p>

<p>Yes.</p>

<p><strong>Can I run both the ReCapSeg workflow and the GATK CNV toolchain workflow?</strong></p>

<p>Yes.  All results are written to separate annotations.</p>

<p><strong>Are the new workflows part of my PrAn?</strong></p>

<p>No, not yet.  You will need to copy (and run) these manually from <code class="code codeInline" spellcheck="false">Algorithm_Commons</code> before you begin analysis.  As a reminder, <em>copy into your analysis workspace.</em></p>

<p><strong>Does GATK CNV require matched (tumor-normal) samples?</strong></p>

<p>No.</p>

<p><strong>Does GATK ACNV require matched (tumor-normal) samples?</strong></p>

<p>In Firehose, yes.  Out of Firehose, no.</p>

<p><strong>How do I modify the ABSOLUTE tasks in FH to accept the new GATK ACNV annotations?</strong></p>

<p>There are two changes you need to make to the ABSOLUTE_v1.5_WES configuration to make it accept the new outputs.</p>

<p>1) replace alleliccapseg_tsv with gatk4cnv_acnv_acs_seg_file_capture in the inputs<br>
2) replace alleliccapseg_skew with 0.9883274, and change the annotation type to "Literal" instead of "Simple Expression"</p>

<p>This answer thanks to Dimitri Livitz, Daniel Rosebrock, and David Kwiatkowski.</p>

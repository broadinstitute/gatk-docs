## Documentation error in RNAseq workflow

By Geraldine_VdAuwera

<p>We discovered today that we made an error in the <a rel="nofollow" href="http://www.broadinstitute.org/gatk/guide/article?id=3891">documentation article that describes the RNAseq Best Practices workflow</a>. The error is not critical but is likely to cause an increased rate of False Positive calls in your dataset.</p>

<p>The error was made in the description of the "Split &amp; Trim" pre-processing step. We originally wrote that you need to reassign mapping qualities to 60 using the ReassignMappingQuality read filter. However, this causes <strong>all</strong> MAPQs in the file to be reassigned to 60, whereas what you want to do is reassign MAPQs <strong>only for good alignments</strong> which STAR identifies with MAPQ 255. This is done with a different read filter, called <a rel="nofollow" href="http://www.broadinstitute.org/gatk/gatkdocs/org_broadinstitute_sting_gatk_filters_ReassignOneMappingQualityFilter.html"><strong>ReassignOneMappingQuality</strong></a>. The correct command is therefore:</p>

<pre class="code codeBlock" spellcheck="false">java -jar GenomeAnalysisTK.jar -T SplitNCigarReads -R ref.fasta -I dedupped.bam -o split.bam -rf ReassignOneMappingQuality -RMQF 255 -RMQT 60 -U ALLOW_N_CIGAR_READS
</pre>

<p>In our hands we see a bump in the rate of FP calls from 4% to 8% when the wrong filter is used. We don't see any significant amount of false negatives (lost true positives) with the bad command, although we do see a few more true positives show up in the results of the bad command. So basically the effect is to excessively increase sensitivity, at the expense of specificity, because poorly mapped reads are taken into account with a "good" mapping quality, where they would normally be discarded.</p>

<p>This effect will be stronger in datasets with lower overall quality, so your results may vary. Let us know if you observe any really dramatic effects, but we don't expect that to happen.</p>

<p>To be clear, we do recommend re-processing your data if you can, but if that is not an option, keep in mind how this affects the rate of false positive discovery in your data.</p>

<p>We apologize for this error (which has now been corrected in the documentation) and for the inconvenience it may cause you.</p>

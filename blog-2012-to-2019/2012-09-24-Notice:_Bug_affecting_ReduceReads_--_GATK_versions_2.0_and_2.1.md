## Notice: Bug affecting ReduceReads -- GATK versions 2.0 and 2.1

By Geraldine_VdAuwera

<p>We have identified a major bug in ReduceReads -- GATK versions 2.0 and 2.1. The effect of the bug is that variant regions with more than 100 reads and fewer than 250 reads get downsampled to 0 reads.</p>

<p>This has now been fixed in the most recent release.</p>

<p>To check if you are using a buggy version, run the following:</p>

<pre class="code codeBlock" spellcheck="false">    samtools view -H $BAM
</pre>

<p>This will produce the following output:</p>

<pre class="code codeBlock" spellcheck="false">    @PG ID:GATK ReduceReads VN:XXX
</pre>

<p>If XXX is 2.0 or 2.1, any results obtained with your current version are suspect, and you will need to upgrade to the most recent version then rerun your processing.</p>

<p>Our most sincere apologies for the inconvenience.</p>

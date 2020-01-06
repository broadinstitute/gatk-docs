## Bug in various tools, 2.4-7 : "ArrayIndexOutOfBoundsException"

By Geraldine_VdAuwera

<p>As reported here:</p>

<ul><li><a href="http://gatkforums.broadinstitute.org/discussion/2342/unifiedgenotyper-causes-arrayindexoutofboundsexception-3-how-to-fix-it" rel="nofollow">http://gatkforums.broadinstitute.org/discussion/2342/unifiedgenotyper-causes-arrayindexoutofboundsexception-3-how-to-fix-it</a></li>
<li><a href="http://gatkforums.broadinstitute.org/discussion/2343/printreads-yet-another-arrayindexoutofboundsexception" rel="nofollow">http://gatkforums.broadinstitute.org/discussion/2343/printreads-yet-another-arrayindexoutofboundsexception</a></li>
<li><a href="http://gatkforums.broadinstitute.org/discussion/2345/arrayindexoutofboundsexception-in-haplotypecaller" rel="nofollow">http://gatkforums.broadinstitute.org/discussion/2345/arrayindexoutofboundsexception-in-haplotypecaller</a></li>
</ul><p>If you encounter this bug too, please don't post a new question about it. Feel free to comment in this thread to let us know you have also had the same problem. Tell us what version of the GATK you were using and post your command line.</p>

<p>Thank you for your patience while we work to fix this issue.</p>

<h3>Latest update: we found that the three tools (PrintRead, HaplotypeCaller and UnifiedGenotyper) had different issues that only produced the same symptom (the ArrayIndexOutOfBoundsException error). The underlying issues have all been fixed in version 2.5. If you encounter an  ArrayIndexOutOfBoundsException in later versions, it is certainly caused by a different problem, so please open a new thread to report the issue.</h3>

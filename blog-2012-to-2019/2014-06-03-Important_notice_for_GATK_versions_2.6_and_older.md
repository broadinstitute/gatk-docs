## Important notice for GATK versions 2.6 and older

By Geraldine_VdAuwera

<p>In a nutshell: if you're using a version of GATK older than 2.7, you need to <a rel="nofollow" href="http://www.broadinstitute.org/gatk/request-key">request a key</a> to disable Phone Home (if you don't already have one). See below for a full explanation.</p>

<hr></hr><p>As you may know, the GATK includes a reporting mechanism called Phone Home that sends us runtime statistics about usage and bugs. These statistics (which you can read more about <a rel="nofollow" href="http://www.broadinstitute.org/gatk/guide/article?id=1250">here</a>) help us make development decisions, e.g. prioritize bug fixes and new features, as well as track adoption of new versions and tools.</p>

<p>The system uses an AWS cloud service as a data repository, called a "bucket", which GATK accesses using an encryption key. We currently have two active AWS keys for the Phone Home bucket. GATK versions 2.6 and older use one AWS key, and versions 2.7 and later use another AWS key.</p>

<p>For practical reasons, we need to deactivate the old AWS key, which means that GATK jobs run using a version older than 2.7 may end with a Phone Home failure unless the system is deactivated. To be clear, the GATK analysis itself will complete successfully, but the GATK may exit with a fail code. This may cause issues in pipelines and potentially fill up error logs.</p>

<p>The best way to avoid these problems is to upgrade to the latest version of GATK, which you should seriously consider anyway in order to get the best possible results out of your analysis. However, if you are unable to upgrade to a recent version, we recommend that you disable the Phone Home system. To do so, you will need to follow these two simple steps:</p>

<ol><li><p>Request a GATK key using <a rel="nofollow" href="http://www.broadinstitute.org/gatk/request-key">this online form</a>. In the "justification" field, please write "AWS key deprecation", indicate which version of GATK you are using, and if possible let us know why you are unable to upgrade to a recent version. You can expect to receive your key within 1 business day.</p></li>
<li><p>Once you have the key, apply it to all GATK jobs by adding <code class="code codeInline" spellcheck="false">-et NO_ET -K your.key</code> (where <code class="code codeInline" spellcheck="false">your.key</code> is the path to the key file you obtained from us) to every GATK command line.</p></li>
</ol><p>We expect this workaround will suppress any issues EXCEPT in versions prior to 1.5, in which the Phone Home system cannot be deactivated. For versions 1.4 and older, there is nothing we can do, and you will have to either put up with the errors, or upgrade to a newer version (which you should really really do anyway!).</p>

<p>Let us know in the comments if you have any trouble or questions.</p>

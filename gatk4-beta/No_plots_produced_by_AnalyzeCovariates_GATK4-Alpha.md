## No plots produced by AnalyzeCovariates GATK4-Alpha

By AmandaChamberlain

<p>I'm using GATK4-Alpha AnalyzeCovariates</p>

<pre class="code codeBlock" spellcheck="false">java -Xmx80G -jar $GATK AnalyzeCovariates -before recal.table -after after_recal.table -plots recal_plots.pdf
</pre>

<p>The tool appears to finish however I then get an error referring to an R script</p>

<pre class="code codeBlock" spellcheck="false">java.lang.IllegalArgumentException: Resource not found relative to class org.broadinstitute.hellbender.utils.recalibration.RecalUtils: BQSR.R
</pre>

<p>and plots are not produced.</p>

<p>Should this script have been a part of the install?</p>

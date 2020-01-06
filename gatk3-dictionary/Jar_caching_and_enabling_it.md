## Jar caching and enabling it

By shlee

<p>When running GATK4 Spark jobs, we see in the standard output a message about caching the jar file.</p>

<pre class="code codeBlock" spellcheck="false">Using GATK jar /Applications/genomicstools/gatk/gatk-4.latest/gatk-package-4.beta.2-spark.jar
jar caching is disabled because GATK_GCS_STAGING is not set

please set GATK_GCS_STAGING to a bucket you have write access too in order to enable jar caching
add the following line to you .bashrc or equivalent startup script

    export GATK_GCS_STAGING=gs://&lt;my_bucket&gt;/
</pre>

<p>Instead of uploading the local jar each time you run a command, it is possible to cache the jar in a run to a cloud bucket. To enable this, you will have to export, i.e. add to your bashrc, <code class="code codeInline" spellcheck="false">GATK_GCS_STAGING=gs://your_bucket_name/some_folder_name/</code>. Gatk-launch will check if the jar you are invoking matches the jar in this folder and, if they match, will copy this jar from google storage to the google cluster instead of your local system. Here is an example export command.</p>

<pre class="code codeBlock" spellcheck="false">export GATK_GCS_STAGING=gs://spacecade7/gatk4/
</pre>

<p>Uploads to Google cloud buckets are free though. So this feature is advantageous for situations in which you have limited or slow network connections. And we know uploads are generally much slower than downloads.</p>

<p>Remember to include the forward slash at the end of the bucket path and use a dedicated directory, e.g. <em>gatk4</em>. Because each GATK4 release’s jar will have a different identifying hash, as you upgrade to each latest release, different versioned jars will start to accumulate and otherwise litter your bucket.</p>

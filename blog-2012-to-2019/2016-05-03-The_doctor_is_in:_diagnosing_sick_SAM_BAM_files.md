## The doctor is in: diagnosing sick SAM/BAM files

By dekling

<p>Ever find yourself happily going about your day, using <a rel="nofollow" href="http://broadinstitute.github.io/picard/">Picard</a> and/or <a rel="nofollow" href="https://www.broadinstitute.org/gatk/">GATK</a> when all of sudden a dreaded <code class="code codeInline" spellcheck="false">ERROR</code> message is output instead of your tidy SAM/BAM or VCF file?  Even worse, you try to diagnose the errors using Picard's <a rel="nofollow" href="http://broadinstitute.github.io/picard/command-line-overview.html#ValidateSamFile">ValidateSamFile</a> tool and the command-line output is... shall we say, "incomplete"?  Well, take comfort because we have just the right medicine for you... new <a rel="nofollow" href="https://www.broadinstitute.org/gatk/guide/article?id=7571">documentation</a> for Picard's <a rel="nofollow" href="http://broadinstitute.github.io/picard/command-line-overview.html#ValidateSamFile">ValidateSamFile</a> tool!</p>

<div>
<div style="text-align: center;">
<img src="https://us.v-cdn.net/5019796/uploads/FileUpload/ed/c754b23a9b48cf9f53dbaaf601de91.png" width="714.4" height="361.6" alt="image" class="embedImage-img importedEmbed-img"></img></div>
</div>

<p>This <a rel="nofollow" href="https://www.broadinstitute.org/gatk/guide/article?id=7571">document</a> will guide even the most novice of users through the steps of diagnosing problems lurking within their SAM/BAM files.  Not only that, for the first time in the history of the world, we present tables listing and explaining all of the  <code class="code codeInline" spellcheck="false">WARNING</code> and  <code class="code codeInline" spellcheck="false">ERROR</code> message outputs of this program!  Woohoo!!!  Now you can effectively troubleshoot your <code class="code codeInline" spellcheck="false">WARNING</code> and  <code class="code codeInline" spellcheck="false">ERROR</code> messages by running <a rel="nofollow" href="http://broadinstitute.github.io/picard/command-line-overview.html#ValidateSamFile">ValidateSamFile</a> prior to feeding your SAM/BAM file into <a rel="nofollow" href="http://broadinstitute.github.io/picard/">Picard</a> and/or <a rel="nofollow" href="https://www.broadinstitute.org/gatk/">GATK</a>.</p>

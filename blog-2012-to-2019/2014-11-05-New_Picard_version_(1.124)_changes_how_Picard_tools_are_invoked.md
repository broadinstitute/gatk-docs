## New Picard version (1.124) changes how Picard tools are invoked

By Geraldine_VdAuwera

<p>Consider this a public service announcement, since most GATK users probably also use Picard tools routinely. The recently released version 1.124 of the Picard tools includes many lovely improvements, bug fixes and even a few new tools (see <a rel="nofollow" href="http://sourceforge.net/p/samtools/mailman/message/32999384/">release notes</a> for full details) -- but you'll want to pay attention to one major change in particular.</p>

<p>From this version onward, the Picard release will contain a single JAR file containing all the tools, instead of one JAR file per tool as it was before. This means that  when you invoke a Picar tool, you'll invoke a single JAR, then specify which tool (which they call CLP for Command Line Program) you want to run. This should feel completely familiar if you already use GATK regularly, but it does mean you'll need to update any scripts that use Picard tools to the new paradigm. Other than that, there's no change of syntax; Picard will still use e.g. <code class="code codeInline" spellcheck="false">I=input.bam</code> where GATK would use <code class="code codeInline" spellcheck="false">-I input.bam</code>.</p>

<p>We will need to update some of our own documentation accordingly over the near future; please bear with us as we go through this process, and let us know by commenting in this thread if you find any docs that have yet to be updated.</p>

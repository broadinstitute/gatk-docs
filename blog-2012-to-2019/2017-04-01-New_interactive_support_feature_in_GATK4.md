## New interactive support feature in GATK4

By Geraldine_VdAuwera

<p>You may have heard that we've been working on a major new release of GATK that we call GATK4. As we are getting closer to the scheduled transition of GATK4 into beta status (from its current lowly alpha state), we are putting a lot work into fine-tuning the user-facing aspects of the program. We realize that many of our users struggle to make sense of the variety of tools and their numerous options and parameters, and that when something goes wrong, the error messages can seem cryptic and/or overwhelming.</p>

<p>So one of the things we're experimenting with is an interactive support feature that you can invoke directly from the command line, and that  should help you figure out solutions to most problems that you might encounter while using GATK. It's not quite fully-featured yet but we'd like to get some feedback to evaluate whether it is helpful to real users, and determine how we can further improve it.</p>

<p>You can download a precompiled jar (fully open source under a BSD license) where this feature is enabled by default, from this page: <a href="https://software.broadinstitute.org/gatk/download/gatk4_1" rel="nofollow">https://software.broadinstitute.org/gatk/download/gatk4_1</a>. The command syntax is essentially the same as for the current version of GATK, except you no longer provide <code class="code codeInline" spellcheck="false">-T</code> to specify the tool, and <code class="code codeInline" spellcheck="false">-o</code> is all grown up and is now <code class="code codeInline" spellcheck="false">-O</code>. You can get usage information for any tool by doing e.g. <code class="code codeInline" spellcheck="false">java -jar GenomeAnalysisTk-4_1.jar PrintReads -h</code> the same way as you would with the current GATK.</p>

<p>Please try it out and let us know what you think!</p>

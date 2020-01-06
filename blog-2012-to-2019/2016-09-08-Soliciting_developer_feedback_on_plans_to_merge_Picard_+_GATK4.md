## Soliciting developer feedback on plans to merge Picard + GATK4

By Geraldine_VdAuwera

<p><em>Cross-posted from <a href="https://github.com/broadinstitute/picard/issues/647" rel="nofollow">https://github.com/broadinstitute/picard/issues/647</a></em></p>

<p>For many years now we’ve been hearing from users of both GATK and Picard about how they’d love to see the two projects <strong>unite into a single "toolkit-to-rule-them-all"</strong>, for the sake of <strong>user convenience</strong>, to promote <strong>consistency across tools</strong>, and to <strong>minimize duplication of effort</strong>.</p>

<p>With the advent of <strong>GATK 4</strong> this suddenly became <strong>a real possibility</strong>, as the decision was made to start the new GATK codebase from the Picard base classes rather than the old GATK 3.x base classes. This allows for <strong>free-form Picard-style tools and GATK “walkers”</strong> built upon an engine traversal to <strong>peacefully co-exist</strong> within the same framework. Last year, a Picard engineer successfully <strong>ported all Picard tools to the GATK 4 codebase</strong> with only minor changes to the tools themselves. More recently, efforts have been made to <strong>harmonize the build systems</strong> of the two projects, resulting in Picard’s recent move to gradle.</p>

<p>Importantly, the <strong>core GATK 4 codebase</strong> at <a href="https://github.com/broadinstitute/gatk" rel="nofollow">https://github.com/broadinstitute/gatk</a> is released entirely under the <strong>BSD 3-clause license</strong>, a big improvement over the confusing licensing situation in GATK 3.x, with its mix of open-source and proprietary licenses within the same repository -- and that is where any Picard tools moved to the GATK 4 codebase would live, <strong>remaining fully open-sourced and free for all</strong>.</p>

<p>As <strong>all of the technical pieces are now in place</strong> to allow for a merger of the two projects (with the guarantee that the open-source nature of Picard code will be preserved) we are <strong>soliciting feedback from the Picard developer community</strong> about the prospect of a union with GATK. Would people here be generally in favor of such a move? Are there any strong objections to this idea? Any concerns that should be addressed <em>before</em> we head any further down this path?</p>

<hr></hr><p>To help start the discussion, <strong>here is the case in favor of a GATK4 + Picard merger</strong> as we see it:</p>

<ul><li>It would greatly <strong>cut down on duplication of effort</strong> and re-invention of functionality already existing in one project or the other -- Picard devs could directly use open-source GATK functionality/utilities, and vice versa.</li>
<li><strong>Bug fixes and new features</strong> such as CRAM support could <strong>more easily propagate</strong> to all the tools in the GATK+Picard ecosystem, and the tools overall could be made <strong>more consistent and easier to use</strong>.</li>
<li>Picard and GATK devs could better <strong>share knowledge and give each other feedback</strong>, and with more eyes looking at the code and more shared developer resources available the <strong>overall quality of the tools should increase</strong>.</li>
<li><strong>End-users’ lives</strong> would be greatly simplified by having <strong>all of the tools in one place</strong>, with a unified interface, a common set of conventions and a single place to go to for documentation, help and updates.</li>
<li>Along similar lines, the ability of the GATK/Picard support team to <strong>provide in-depth support for the tools</strong> would be greatly enhanced. Among other things this would speed up plans to provide <strong>developer support and API documentation</strong>.</li>
<li>Only <strong>minimal changes to the Picard tools</strong> would be required. The biggest change would be a switch to standard POSIX-style command-line arguments (eg., <code class="code codeInline" spellcheck="false">--argName value</code> instead of <code class="code codeInline" spellcheck="false">argName=value</code>).</li>
<li>The <strong>work of porting has already largely been done</strong>, and in the process, test coverage of the ported versions of the Picard tools has been improved and bugs have been fixed.</li>
</ul><p>In short: we’re stronger together than apart, right? <img class="emoji" src="https://gatkforums.broadinstitute.org/resources/emoji/smile.png" title=":)" alt=":)" height="20"></img></p>

<p>We realize that such a move is never trivial, but we're prepared to put resources into making the migration as painless as possible for everyone, and we believe that it would take us all to a better situation than what we have right now.</p>

<p>What do you think? Let the <a rel="nofollow" href="https://github.com/broadinstitute/picard/issues/647">discussion</a> begin!</p>

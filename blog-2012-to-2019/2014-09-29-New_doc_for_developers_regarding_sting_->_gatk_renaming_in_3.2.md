## New doc for developers regarding sting -&gt; gatk renaming in 3.2

By Geraldine_VdAuwera

<p>If you're a developer who uses the GATK framework, you may have noticed that the 3.2 release broke existing third-party walkers. This happened because we had to do some codebase-wide refactoring/renaming that changes package names and classpaths (most obviously, renaming all instances of "sting" to "gatk"). We're sorry for the inconvenience but this was necessary as a preparation step on the road to providing GATK as a maven artifact.</p>

<p>We have posted a document that details the changes and explains how to update third-party code to work with GATK framework versions 3.2 onward <a rel="nofollow" href="http://gatkforums.broadinstitute.org/discussion/4173/sting-to-gatk-renaming">here</a>.</p>

<p>While we're on this topic, it seems that the <a rel="nofollow" href="http://gatkforums.broadinstitute.org/discussion/4023/setting-up-your-dev-environment-maven-and-intellij-for-gatk-3">IntelliJ set up instructions</a> are a little outdated (in part due to the renaming), so we'll be updating that as well in the near future.</p>

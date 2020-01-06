## (howto) Get started with GATK4 beta

By Geraldine_VdAuwera

<h3>Download the software</h3>

<p>The GATK4 beta version command-line tools are provided as a single executable jar file. You can download a zipped package containing the jar file from <a rel="nofollow" href="https://github.com/broadinstitute/gatk/releases/download/4.beta.1/gatk-4.beta.1.zip">this Github link</a> (GATK4 Download page coming soon). Once you unzip the package, you will find four files inside the resulting directory:</p>

<pre class="code codeBlock" spellcheck="false">gatk-launch
gatk-package-4.beta.x-local.jar
gatk-package-4.beta.x-spark.jar
README.md 
</pre>

<p>where <code class="code codeInline" spellcheck="false">x</code> is the minor release version in the jar file names.</p>

<p>Now you may ask, why are there two jars? As the names suggest, <code class="code codeInline" spellcheck="false">gatk-package-4.beta.x-spark.jar</code> is the jar for running Spark tools on a Spark cluster, while <code class="code codeInline" spellcheck="false">gatk-package-4.beta.x-local.jar</code> is the jar that is used for everything else (including running Spark tools "locally", ie on a regular server or cluster).</p>

<p>So does that mean you have to specify which one you want to run each time? Nope! See the <code class="code codeInline" spellcheck="false">gatk-launch</code> file in there? That's an executable wrapper script that you invoke and that will choose the appropriate jar for you based on the rest of your command line. You can still invoke a specific jar if you want, but using <code class="code codeInline" spellcheck="false">gatk-launch</code> is easier, and it will also take care of setting some parameters that you would otherwise have to specify manually. We'll talk about that in a minute.</p>

<hr></hr><h3>Install it</h3>

<p>There is no installation necessary in the traditional sense, since the precompiled jar files should work on any POSIX platform (NOT Microsoft Windows!) equipped with the appropriate version of Java (see below). You'll simply need to open the downloaded package and place the folder containing the jar files in a convenient directory on your hard drive (or server). Although the jars themselves cannot simply be added to your PATH, you can do so with the <code class="code codeInline" spellcheck="false">gatk-launch</code> wrapper script. Please look up instructions depending on the terminal shell you use; in <code class="code codeInline" spellcheck="false">bash</code> the typical syntax is <code class="code codeInline" spellcheck="false">export PATH=$PATH:/path/to/gatk/gatk-launch</code> where <code class="code codeInline" spellcheck="false">path/to/</code> is the path to the location of the <code class="code codeInline" spellcheck="false">gatk-launch</code> executable. Note that the jars must remain in the same directory as <code class="code codeInline" spellcheck="false">gatk-launch</code> for it to work.</p>

<h4>Important note about Java version</h4>

<p>For the tools to run properly, you must have Java 8 / JDK or JRE 1.8 installed. To check your java version, open your terminal application and run the following command:</p>

<pre class="code codeBlock" spellcheck="false">java -version
</pre>

<p>If the output looks something like <code class="code codeInline" spellcheck="false">java version "1.8.x_y"</code>, you are good to go. If not, you may need to change your version. You can download a suitable upgrade either from <a rel="nofollow" href="http://www.oracle.com/technetwork/java/javase/downloads/index.html">Oracle</a> or from <a rel="nofollow" href="http://openjdk.java.net/install/">OpenJDK</a>. To be clear, OpenJDK is now fully supported.</p>

<hr></hr><h3>Test that it works</h3>

<p>To test that you can run GATK tools, run the following command in your terminal application (we assume that you have added <code class="code codeInline" spellcheck="false">gatk-launch</code> to your PATH):</p>

<pre class="code codeBlock" spellcheck="false">./gatk-launch --help
</pre>

<p>This will output a summary of the GATK4 invocation syntax, options for listing tools and invoking a specific tool's help documentation, and main Spark options.</p>

<hr></hr><h3>Use GATK tools</h3>

<p>Tools are invoked as follows:</p>

<pre class="code codeBlock" spellcheck="false">./gatk-launch ToolName -OPTION1 value1 -OPTION2 value2 
</pre>

<p>If you have previous used older GATK versions, you'll notice that <code class="code codeInline" spellcheck="false">ToolName</code> is no longer passed with <code class="code codeInline" spellcheck="false">-T</code> and that it is now positional: the tool name must always be the first thing you write after the <code class="code codeInline" spellcheck="false">./gatk-launch</code> part (or the jar file if you're invoking the jar directly).</p>

<p>Available tools are all listed in the <a rel="nofollow" href="https://software.broadinstitute.org/gatk/documentation/tooldocs">Tool Documentation</a> section, which is versioned; on the website, use the orange dropdown menu button to switch between versions. This provides a complete list of tools with usage recommendations, options, and example commands.</p>

<hr></hr><h3>Docker image</h3>

<p>Docker images for GATK4 releases can be found at <a href="https://hub.docker.com/r/broadinstitute/gatk/" rel="nofollow">https://hub.docker.com/r/broadinstitute/gatk/</a></p>

## (howto) Run a WDL script via Cromwell on a local machine (e.g. your laptop)

By Geraldine_VdAuwera

<p>Running on a local machine is the simplest thing in the world. Assuming that you have a WDL script that you've <a rel="nofollow" href="https://software.broadinstitute.org/wdl/userguide/validation.php">validated</a> called <code class="code codeInline" spellcheck="false">myWorkflow.wdl</code>, and a <a rel="nofollow" href="https://software.broadinstitute.org/wdl/userguide/inputs.php">JSON file of inputs</a> called <code class="code codeInline" spellcheck="false">myWorkflow_inputs.json</code>, you just call Cromwell’s <code class="code codeInline" spellcheck="false">run</code> function, like so:</p>

<pre class="code codeBlock" spellcheck="false">java -jar Cromwell.jar run myWorkflow.wdl --inputs myWorkflow_inputs.json
</pre>

<p>This will run your workflow. You will see text from the Cromwell engine updating you as it walks through the steps.</p>

<p><em>Note that any messages that are normally output to the terminal by the tools themselves will not actually be shown in the terminal where you're running the script. Instead, Cromwell saves this output in a log file called <code class="code codeInline" spellcheck="false">stderr</code> located within the execution folder.</em></p>

<p>By default, you can find all generated files (outputs and logs) in this folder:</p>

<p><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/5d/35596768fbf3f484c3ba95f05cc9ed.png" alt="image" class="embedImage-img importedEmbed-img"></img></p>

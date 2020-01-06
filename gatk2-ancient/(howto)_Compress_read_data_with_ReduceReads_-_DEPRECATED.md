## (howto) Compress read data with ReduceReads - DEPRECATED

By Geraldine_VdAuwera

<h3>Please note that this article refers to a method that is no longer recommended as part of the Best Practices!</h3>

<h4>Objective</h4>

<p>Compress the read data in order to minimize file sizes, which facilitates massively multisample processing.</p>

<h4>Prerequisites</h4>

<ul><li>TBD</li>
</ul><h4>Steps</h4>

<ol><li>Compress your sequence data</li>
</ol><hr></hr><h3>1. Compress your sequence data</h3>

<h4>Action</h4>

<p>Run the following GATK command:</p>

<pre class="code codeBlock" spellcheck="false">java -jar GenomeAnalysisTK.jar \ 
    -T ReduceReads \ 
    -R reference.fa \ 
    -I recal_reads.bam \ 
    -L 20 \ 
    -o reduced_reads.bam 
</pre>

<h4>Expected Result</h4>

<p>This creates a file called <code class="code codeInline" spellcheck="false">reduced_reads.bam</code> containing only the sequence information that is essential for calling variants.</p>

<p>Note that ReduceReads is not meant to be run on multiple samples at once. If you plan on merging your sample bam files, you should run ReduceReads on individual samples before doing so.</p>

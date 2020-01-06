## (howto) Perform local realignment around indels

By Geraldine_VdAuwera

<h3>NOTE: This tutorial has been replaced by a more recent and much improved version that you can find <a rel="nofollow" href="https://www.broadinstitute.org/gatk/guide/article?id=7156">here</a>.</h3>

<h4>Objective</h4>

<p>Perform local realignment around indels to correct mapping-related artifacts.</p>

<h4>Prerequisites</h4>

<ul><li>TBD</li>
</ul><h4>Steps</h4>

<ol><li>Create a target list of intervals to be realigned</li>
<li>Perform realignment of the target intervals</li>
</ol><hr></hr><h3>1. Create a target list of intervals to be realigned</h3>

<h4>Action</h4>

<p>Run the following GATK command:</p>

<pre class="code codeBlock" spellcheck="false">java -jar GenomeAnalysisTK.jar \ 
    -T RealignerTargetCreator \ 
    -R reference.fa \ 
    -I dedup_reads.bam \ 
    -L 20 \ 
    -known gold_indels.vcf \ 
    -o realignment_targets.list
</pre>

<h4>Expected Result</h4>

<p>This creates a file called <code class="code codeInline" spellcheck="false">realignment_targets.list</code> containing the list of intervals that the program identified as needing realignment within our target, chromosome 20.</p>

<p>The list of known indel sites (<code class="code codeInline" spellcheck="false">gold_indels.vcf</code>) are used as targets for realignment. Only use it if there is such a list for your organism.</p>

<hr></hr><h3>2. Perform realignment of the target intervals</h3>

<h4>Action</h4>

<p>Run the following GATK command:</p>

<pre class="code codeBlock" spellcheck="false">java -jar GenomeAnalysisTK.jar \ 
    -T IndelRealigner \ 
    -R reference.fa \ 
    -I dedup_reads.bam \ 
    -targetIntervals realignment_targets.list \ 
    -known gold_indels.vcf \ 
    -o realigned_reads.bam 
</pre>

<h4>Expected Result</h4>

<p>This creates a file called <code class="code codeInline" spellcheck="false">realigned_reads.bam</code> containing all the original reads, but with better local alignments in the regions that were realigned.</p>

<p>Note that here, we didn’t include the <code class="code codeInline" spellcheck="false">-L 20</code> argument. It's not necessary since the program will only run on the target intervals we are providing.</p>

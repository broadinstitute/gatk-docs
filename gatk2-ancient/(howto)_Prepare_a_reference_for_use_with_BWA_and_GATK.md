## (howto) Prepare a reference for use with BWA and GATK

By Geraldine_VdAuwera

<h3>NOTE: This tutorial has been replaced by a more recent version that uses GRCh38 that you can find <a rel="nofollow" href="https://www.broadinstitute.org/gatk/guide/article?id=8017">here</a>.</h3>

<hr></hr><h4>Objective</h4>

<p>Prepare a reference sequence so that it is suitable for use with BWA and GATK.</p>

<h4>Prerequisites</h4>

<ul><li>Installed BWA</li>
<li>Installed SAMTools</li>
<li>Installed Picard</li>
</ul><h4>Steps</h4>

<ol><li>Generate the BWA index</li>
<li>Generate the Fasta file index</li>
<li>Generate the sequence dictionary</li>
</ol><hr></hr><h3>1. Generate the BWA index</h3>

<h4>Action</h4>

<p>Run the following BWA command:</p>

<pre class="code codeBlock" spellcheck="false">bwa index -a bwtsw reference.fa 
</pre>

<p>where <code class="code codeInline" spellcheck="false">-a bwtsw</code> specifies that we want to use the indexing algorithm that is capable of handling the whole human genome.</p>

<h4>Expected Result</h4>

<p>This creates a collection of files used by BWA to perform the alignment.</p>

<hr></hr><h3>2. Generate the fasta file index</h3>

<h4>Action</h4>

<p>Run the following SAMtools command:</p>

<pre class="code codeBlock" spellcheck="false">samtools faidx reference.fa 
</pre>

<h4>Expected Result</h4>

<p>This creates a file called <code class="code codeInline" spellcheck="false">reference.fa.fai</code>, with one record per line for each of the contigs in the FASTA reference file. Each record is composed of the contig name, size, location, basesPerLine and bytesPerLine.</p>

<hr></hr><h3>3. Generate the sequence dictionary</h3>

<h4>Action</h4>

<p>Run the following Picard command:</p>

<pre class="code codeBlock" spellcheck="false">java -jar picard.jar CreateSequenceDictionary \
    REFERENCE=reference.fa \ 
    OUTPUT=reference.dict 
</pre>

<p>Note that this is the new syntax for use with the latest version of Picard. Older versions used a slightly different syntax because all the tools were in separate jars, so you'd call e.g. <code class="code codeInline" spellcheck="false">java -jar CreateSequenceDictionary.jar</code> directly.</p>

<h4>Expected Result</h4>

<p>This creates a file called <code class="code codeInline" spellcheck="false">reference.dict</code> formatted like a SAM header, describing the contents of your reference FASTA file.</p>

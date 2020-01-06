## BWA/C Bindings - RETIRED

By delangel

<h3>Please note that this article has not been updated in a very long time and may no longer be applicable. Use at your own risk.</h3>

<hr></hr><h3>Sting BWA/C Bindings</h3>

<p><b><span style="color:red">WARNING: This tool was experimental and unsupported and never made it beyond a beta version. Use at your own risk.</span></b>
</p>

<p>The GSA group has made bindings available for Heng Li's <a rel="nofollow" href="http://bio-bwa.sourceforge.net/">Burrows-Wheeler Aligner (BWA)</a>.  Our aligner bindings present additional functionality to the user not traditionally available with BWA.  BWA standalone is optimized to do fast, low-memory alignments from <a rel="nofollow" href="http://maq.sourceforge.net/fastq.shtml">Fastq</a> to <a rel="nofollow" href="http://samtools.sourceforge.net/SAM1.pdf">BAM</a>.  While our bindings aim to provide support for reasonably fast, reasonably low memory alignment, we add the capacity to do exploratory data analyses.  The bindings can provide all alignments for a given read, allowing a user to walk over the alignments and see information not typically provided in the BAM format.  Users of the bindings can 'go deep', selectively relaxing alignment parameters one read at a time, looking for the best alignments at a site.
</p>

<p>The BWA/C bindings should be thought of as alpha release quality.  However, we aim to be particularly responsive to issues in the bindings as they arise.  Because of the bindings' alpha state, some functionality is limited; see the Limitations section below for more details on what features are currently supported.
</p>

<table id="toc"><tr><td><div id="toctitle"><h2>Contents</h2></div>
<ul><li><a rel="nofollow" href="#A_note_about_using_the_bindings">1 A note about using the bindings</a>
<ul><li><a rel="nofollow" href="#bash">1.1 bash</a></li>
<li><a rel="nofollow" href="#csh">1.2 csh</a></li>
</ul></li>
<li><a rel="nofollow" href="#Preparing_to_use_the_aligner">2 Preparing to use the aligner</a>
<ul><li><a rel="nofollow" href="#Within_the_Broad_Institute">2.1 Within the Broad Institute</a></li>
<li><a rel="nofollow" href="#Outside_of_the_Broad_Institute">2.2 Outside of the Broad Institute</a></li>
</ul></li>
<li><a rel="nofollow" href="#Using_the_existing_GATK_alignment_walkers">3 Using the existing GATK alignment walkers</a></li>
<li><a rel="nofollow" href="#Writing_new_GATK_walkers_utilizing_alignment_bindings">4 Writing new GATK walkers utilizing alignment bindings</a></li>
<li><a rel="nofollow" href="#Running_the_aligner_outside_of_the_GATK">5 Running the aligner outside of the GATK</a></li>
<li><a rel="nofollow" href="#Limitations">6 Limitations</a></li>
<li><a rel="nofollow" href="#Example:_analysis_of_alignments_with_the_BWA_bindings">7 Example: analysis of alignments with the BWA bindings</a></li>
<li><a rel="nofollow" href="#Validation_methods">8 Validation methods</a></li>
<li><a rel="nofollow" href="#Unsupported:_using_the_BWA.2FC_bindings_from_within_Matlab">9 Unsupported: using the BWA/C bindings from within Matlab</a></li>
</ul></td></tr></table><h2><span id="A_note_about_using_the_bindings"> A note about using the bindings </span></h2>

<p>Whenever native code is called from Java, the user must assist Java in finding the proper shared library.  Java looks for shared libraries in two places, on the system-wide library search path and through Java properties invoked on the command line.  To add libbwa.so to the global library search path, add the following to your .my.bashrc, .my.cshrc, or other startup file:
</p>

<h5><span id="bash"> bash </span></h5>

<pre class="code codeBlock" spellcheck="false">
export LD_LIBRARY_PATH=/humgen/gsa-scr1/GATK_Data/bwa/stable:$LD_LIBRARY_PATH
</pre>

<h5><span id="csh"> csh </span></h5>

<pre class="code codeBlock" spellcheck="false">
setenv LD_LIBRARY_PATH /humgen/gsa-scr1/GATK_Data/bwa/stable:$LD_LIBRARY_PATH
</pre>

<p>To specify the location of libbwa.so directly on the command-line, use the java.library.path system property as follows:
</p>

<pre class="code codeBlock" spellcheck="false">
java -Djava.library.path=/humgen/gsa-scr1/GATK_Data/bwa/stable \
    -jar dist/GenomeAnalysisTK.jar \
    -T AlignmentValidation \
    -I /humgen/gsa-hphome1/hanna/reference/1kg/NA12878_Pilot1_20.bwa.bam \
    -R /humgen/gsa-scr1/GATK_Data/bwa/human_b36_both.fasta
</pre>

<h2><span id="Preparing_to_use_the_aligner"> Preparing to use the aligner </span></h2>

<h3><span id="Within_the_Broad_Institute"> Within the Broad Institute </span></h3>

<p>We provide internally accessible versions of both the BWA shared library and precomputed BWA indices for two commonly used human references at the Broad (Homo_sapiens_assembly18.fasta and human_b36_both.fasta).  These files live in the following directory:
</p>

<pre class="code codeBlock" spellcheck="false">
/humgen/gsa-scr1/GATK_Data/bwa/stable
</pre>

<h3><span id="Outside_of_the_Broad_Institute"> Outside of the Broad Institute </span></h3>

<p>Two steps are required in preparing to use the aligner: building the shared library and using BWA/C to generate an index of the reference sequence.
</p>

<p>The Java bindings to the aligner are available through the <a rel="nofollow" href="https://github.com/broadgsa/gatk">Sting</a> repository.  A precompiled version of the bindings are available for Linux;
these bindings are available in c/bwa/libbwa.so.1.  To build the aligner from source:
</p>

<ul><li> Fetch the latest svn of BWA from <a rel="nofollow" href="https://bio-bwa.svn.sourceforge.net/svnroot/bio-bwa">SourceForge</a>.  Configure and build BWA.
</li></ul><pre class="code codeBlock" spellcheck="false">
sh autogen.sh
./configure
make
</pre>

<ul><li> Download the latest version of Sting from our <a rel="nofollow" href="https://github.com/broadgsa/gatk">Github repository</a>. 
</li><li> Customize the variables at the top one of the build scripts (c/bwa/build_linux.sh,c/bwa/build_mac.sh) based on your environment.  Run the build script.
</li></ul><p>To build a reference sequence, use the BWA C executable directly:
</p>

<pre class="code codeBlock" spellcheck="false">
bwa index -a bwtsw &lt;your reference sequence&gt;.fasta
</pre>

<h2><span id="Using_the_existing_GATK_alignment_walkers"> Using the existing GATK alignment walkers </span></h2>

<p>Two walkers are provided for end users of the GATK.  The first of the stock walkers is Align, which can align an unmapped BAM file or realign a mapped BAM file.
</p>

<pre class="code codeBlock" spellcheck="false">
java \
-Djava.library.path=/humgen/gsa-scr1/GATK_Data/bwa/stable \
    -jar dist/GenomeAnalysisTK.jar \
    -T Align \
    -I NA12878_Pilot1_20.unmapped.bam \
    -R /humgen/gsa-scr1/GATK_Data/bwa/human_b36_both.fasta \
    -U \
    -ob human.unsorted.bam
</pre>

<p>Most of the available parameters here are standard GATK.  -T specifies that the alignment analysis should be used; -I specifies the unmapped BAM file to align, and the -R specifies the reference to which to align.  By default, this walker assumes that the bwa index support files will live alongside the reference.  If these files are stored elsewhere, the optional -BWT argument can be used to specify their location.  By defaults, alignments will be emitted to the console in SAM format.  Alignments can be spooled to disk in SAM format using the -o option or spooled to disk in BAM format using the -ob option.
</p>

<p>The other stock walker is AlignmentValidation, which computes all possible alignments based on the BWA default configuration settings and makes sure at least 
one of the top alignments matches the alignment stored in the read.  
</p>

<pre class="code codeBlock" spellcheck="false">
java \
-Djava.library.path=/humgen/gsa-scr1/GATK_Data/bwa/stable \
    -jar dist/GenomeAnalysisTK.jar \
    -T AlignmentValidation \
    -I /humgen/gsa-hphome1/hanna/reference/1kg/NA12878_Pilot1_20.bwa.bam \
    -R /humgen/gsa-scr1/GATK_Data/bwa/human_b36_both.fasta
</pre>

<p>Options for the AlignmentValidation walker are identical to the Alignment walker, except the AlignmentValidation walker's only output is a exception if validation fails.
</p>

<p>Another sample walker of limited scope, CountBestAlignmentsWalker, is available for review; it is discussed in the example section below.
</p>

<h2><span id="Writing_new_GATK_walkers_utilizing_alignment_bindings"> Writing new GATK walkers utilizing alignment bindings </span></h2>

<p>BWA/C can be created on-the-fly using the org.broadinstitute.sting.alignment.bwa.c.BWACAligner constructor.  The bindings have two sets of interfaces: an interface which returns all possible alignments 
and an interface which randomly selects an alignment from a list of the top scoring alignments as selected by BWA.
</p>

<p>To iterate through all functions, use the following method:
</p>

<pre class="code codeBlock" spellcheck="false">
    /**
     * Get a iterator of alignments, batched by mapping quality.
     * <a href="https://gatkforums.broadinstitute.org/gatk/profile/param" rel="nofollow">@param</a> bases List of bases.
     * <a href="https://gatkforums.broadinstitute.org/gatk/profile/return" rel="nofollow">@return</a> Iterator to alignments.
     */
    public Iterable&lt;Alignment[]&gt; getAllAlignments(final byte[] bases);
</pre>

<p>The call will return an Iterable which batches alignments by score.  Each call to next() on the provided iterator will return all Alignments of a given score, ordered in
best to worst.  For example, given a read sequence with at least one match on the genome, the first call to next() will supply all exact matches, and subsequent calls
to next() will give alignments judged to be inferior by BWA (alignments containing mismatches, gap opens, or gap extensions).
</p>

<p>Alignments can be transformed to reads using the following static method in org.broadinstitute.sting.alignment.Alignment:
</p>

<pre class="code codeBlock" spellcheck="false">
    /**
     * Creates a read directly from an alignment.
     * <a href="https://gatkforums.broadinstitute.org/gatk/profile/param" rel="nofollow">@param</a> alignment The alignment to convert to a read.
     * <a href="https://gatkforums.broadinstitute.org/gatk/profile/param" rel="nofollow">@param</a> unmappedRead Source of the unmapped read.  Should have bases, quality scores, and flags.
     * <a href="https://gatkforums.broadinstitute.org/gatk/profile/param" rel="nofollow">@param</a> newSAMHeader The new SAM header to use in creating this read.  Can be null, but if so, the sequence
     *                     dictionary in the
     * <a href="https://gatkforums.broadinstitute.org/gatk/profile/return" rel="nofollow">@return</a> A mapped alignment.
     */
    public static SAMRecord convertToRead(Alignment alignment, SAMRecord unmappedRead, SAMFileHeader newSAMHeader);
</pre>

<p>A convenience method is available which allows the user to get SAMRecords directly from the aligner.
</p>

<pre class="code codeBlock" spellcheck="false">
    /**
     * Get a iterator of aligned reads, batched by mapping quality.
     * <a href="https://gatkforums.broadinstitute.org/gatk/profile/param" rel="nofollow">@param</a> read Read to align.
     * <a href="https://gatkforums.broadinstitute.org/gatk/profile/param" rel="nofollow">@param</a> newHeader Optional new header to use when aligning the read.  If present, it must be null.
     * <a href="https://gatkforums.broadinstitute.org/gatk/profile/return" rel="nofollow">@return</a> Iterator to alignments.
     */
    public Iterable&lt;SAMRecord[]&gt; alignAll(final SAMRecord read, final SAMFileHeader newHeader);
</pre>

<p>To return a single read randomly selected by the bindings, use one of the following methods:
</p>

<pre class="code codeBlock" spellcheck="false">
    /**
     * Allow the aligner to choose one alignment randomly from the pile of best alignments.
     * <a href="https://gatkforums.broadinstitute.org/gatk/profile/param" rel="nofollow">@param</a> bases Bases to align.
     * <a href="https://gatkforums.broadinstitute.org/gatk/profile/return" rel="nofollow">@return</a> An align
     */
    public Alignment getBestAlignment(final byte[] bases);

    /**
     * Align the read to the reference.
     * <a href="https://gatkforums.broadinstitute.org/gatk/profile/param" rel="nofollow">@param</a> read Read to align.
     * <a href="https://gatkforums.broadinstitute.org/gatk/profile/param" rel="nofollow">@param</a> header Optional header to drop in place.
     * <a href="https://gatkforums.broadinstitute.org/gatk/profile/return" rel="nofollow">@return</a> A list of the alignments.
     */
    public SAMRecord align(final SAMRecord read, final SAMFileHeader header);
</pre>

<p>The org.broadinstitute.sting.alignment.bwa.BWAConfiguration argument allows the user to specify parameters normally specified to 'bwt aln'.  Available parameters are:
</p>

<ul><li> Maximum edit distance (-n)
</li><li> Maximum gap opens (-o)
</li><li> Maximum gap extensions (-e)
</li><li> Disallow an indel within INT bp towards the ends (-i)
</li><li> Mismatch penalty (-M)
</li><li> Gap open penalty (-O)
</li><li> Gap extension penalty (-E)
</li></ul><p>Settings must be supplied to the constructor; leaving any BWAConfiguration field unset means that BWA should use its default value for that argument.  Configuration
settings can be updated at any time using the BWACAligner updateConfiguration method. 
</p>

<pre class="code codeBlock" spellcheck="false">
    public void updateConfiguration(BWAConfiguration configuration);
</pre>

<h2><span id="Running_the_aligner_outside_of_the_GATK"> Running the aligner outside of the GATK </span></h2>

<p>The BWA/C bindings were written with running outside of the GATK in mind, but this workflow has never been tested.  If you would like to run the bindings outside of the
GATK, you will need:
</p>

<ul><li> The BWA shared object, libbwa.so.1
</li><li> The packaged version of Aligner.jar
</li></ul><p>To build the packaged version of the aligner, run the following command
</p>

<pre class="code codeBlock" spellcheck="false">
cp $STING_HOME/lib/bcel-*.jar ~/.ant/lib
ant package -Dexecutable=Aligner
</pre>

<p>This command will extract all classes required to run the aligner and place them in $STING_HOME/dist/packages/Aligner/Aligner.jar.  You can then specify this one jar in your project's dependencies.
</p>

<h2> <span id="Limitations"> Limitations </span></h2>

<p>The BWA/C bindings are currently in an alpha state, but they are extensively supported.  Because of the bindings' alpha state, some functionality is limited.  The limitations of these bindings include:
</p>

<ul><li> Only single-end alignment is supported.  However, a paired end module could be implemented as a simple extension that finds the jointly optimal placement of both singly-aligned ends.
</li><li> Color space alignments are not currently supported.
</li><li> Only a limited number of parameters BWA's extensive parameter list are supported.  The current list of supported parameters is specified in the 'Writing new GATK walkers utilizing alignment bindings' section below.
</li><li> The system is not as heavily memory-optimized as the BWA/C implementation standalone.  The JVM, by default, uses slightly over 4G of resident memory when running BWA on human.  We have not done extensive testing on the behavior of the BWA/C bindings under memory pressure.
</li><li> There is a slight negative impact on performance when using the BWA/C bindings.  BWA/C standalone on 6.9M reads of human data takes roughly 45min to run 'bwa aln', 5min to run 'bwa samse', and another 1.5min to convert the resulting SAM file to a BAM.  Aligning the same dataset using the Java bindings takes approximately 55 minutes.
</li><li> The GATK requires that its input BAMs be sorted and indexed.  Before using the Align or AlignmentValidation walker, you must sort and index your unmapped BAM file.  Note that this is a limitation of the GATK, not the aligner itself.  Using the alignment support files outside of the GATK will eliminate this requirement.
</li></ul><h2><span id="Example:_analysis_of_alignments_with_the_BWA_bindings"> Example: analysis of alignments with the BWA bindings </span></h2>

<p>In order to validate that the Java bindings were computing the same number of reads as BWA/C standalone, we modified the BWA source to gather the number of equally scoring alignments and the frequency of the number of equally scoring alignments.  We then implemented the same using a walker written in the GATK.  We computed this distribution over a set of 36bp human reads and found the distributions to be identical.
</p>

<p>The relevant parts of the walker follow.
</p>

<pre class="code codeBlock" spellcheck="false">
public class CountBestAlignmentsWalker extends ReadWalker&lt;Integer,Integer&gt; {
    /**
     * The supporting BWT index generated using BWT.
     */
    <a href="https://gatkforums.broadinstitute.org/gatk/profile/Argument%28fullName%3D%26quot" rel="nofollow">@Argument(fullName=&amp;quot</a>;BWTPrefix",shortName="BWT",doc="Index files generated by bwa index -d bwtsw",required=false)
    String prefix = null;

    /**
     * The actual aligner.
     */
    private Aligner aligner = null;

    private SortedMap&lt;Integer,Integer&gt; alignmentFrequencies = new TreeMap&lt;Integer,Integer&gt;();

    /**
     * Create an aligner object.  The aligner object will load and hold the BWT until close() is called.
     */
    <a href="https://gatkforums.broadinstitute.org/gatk/profile/Override" rel="nofollow">@Override</a>
    public void initialize() {
        BWTFiles bwtFiles = new BWTFiles(prefix);
        BWAConfiguration configuration = new BWAConfiguration();
        aligner = new BWACAligner(bwtFiles,configuration);
    }

    /**
     * Aligns a read to the given reference.
     * <a href="https://gatkforums.broadinstitute.org/gatk/profile/param" rel="nofollow">@param</a> ref Reference over the read.  Read will most likely be unmapped, so ref will be null.
     * <a href="https://gatkforums.broadinstitute.org/gatk/profile/param" rel="nofollow">@param</a> read Read to align.
     * <a href="https://gatkforums.broadinstitute.org/gatk/profile/return" rel="nofollow">@return</a> Number of alignments found for this read.
     */
    <a href="https://gatkforums.broadinstitute.org/gatk/profile/Override" rel="nofollow">@Override</a>
    public Integer map(char[] ref, SAMRecord read) {
        Iterator&lt;Alignment[]&gt; alignmentIterator = aligner.getAllAlignments(read.getReadBases()).iterator();
        if(alignmentIterator.hasNext()) {
            int numAlignments = alignmentIterator.next().length;
            if(alignmentFrequencies.containsKey(numAlignments))
                alignmentFrequencies.put(numAlignments,alignmentFrequencies.get(numAlignments)+1);
            else
                alignmentFrequencies.put(numAlignments,1);
        }
        return 1;
    }    

    /**
     * Initial value for reduce.  In this case, validated reads will be counted.
     * <a href="https://gatkforums.broadinstitute.org/gatk/profile/return" rel="nofollow">@return</a> 0, indicating no reads yet validated.
     */
    <a href="https://gatkforums.broadinstitute.org/gatk/profile/Override" rel="nofollow">@Override</a>
    public Integer reduceInit() { return 0; }

    /**
     * Calculates the number of reads processed.
     * <a href="https://gatkforums.broadinstitute.org/gatk/profile/param" rel="nofollow">@param</a> value Number of reads processed by this map.
     * <a href="https://gatkforums.broadinstitute.org/gatk/profile/param" rel="nofollow">@param</a> sum Number of reads processed before this map.
     * <a href="https://gatkforums.broadinstitute.org/gatk/profile/return" rel="nofollow">@return</a> Number of reads processed up to and including this map.
     */
    <a href="https://gatkforums.broadinstitute.org/gatk/profile/Override" rel="nofollow">@Override</a>
    public Integer reduce(Integer value, Integer sum) {
        return value + sum;
    }

    /**
     * Cleanup.
     * <a href="https://gatkforums.broadinstitute.org/gatk/profile/param" rel="nofollow">@param</a> result Number of reads processed.
     */
    <a href="https://gatkforums.broadinstitute.org/gatk/profile/Override" rel="nofollow">@Override</a>
    public void onTraversalDone(Integer result) {
        aligner.close();
        for(Map.Entry&lt;Integer,Integer&gt; alignmentFrequency: alignmentFrequencies.entrySet())
            out.printf("%d\t%d%n", alignmentFrequency.getKey(), alignmentFrequency.getValue());
        super.onTraversalDone(result);
    }
}
</pre>

<p>This walker can be run within the svn version of the GATK using -T CountBestAlignments.
</p>

<p>The resulting placement count frequency is shown in the graph below.  The number of placements clearly follows an exponential.
</p>

<p><a rel="nofollow" href="/gsa/wiki/index.php/File:Bwa_dist.png"><img alt="Bwa dist.png" src="/gsa/wiki/images/7/77/Bwa_dist.png" width="640" height="480" class="embedImage-img importedEmbed-img"></img></a>
</p>

<h2><span id="Validation_methods"> Validation methods </span></h2>

<p>Two major techniques were used to validate the Java bindings against the current BWA implementation.
</p>

<ul><li> Fastq files from E coli and from NA12878 chr20 were aligned using BWA standalone with BWA's default settings.  The aligned SAM files were sorted, indexed, and fed into the alignment validation walker.  The alignment validation walker verified that one of the top scoring matches from the BWA bindings matched the alignment produced by BWA standalone.
</li><li> Fastq files from E coli and from NA12878 chr20 were aligned using the GATK Align walker, then fed back into the GATK's alignment validation walker.
</li><li> The distribution of the alignment frequency was compared between BWA standalone and the Java bindings and was found to be identical.
</li></ul><p>As an ongoing validation strategy, we will use the GATK integration test suite to align a small unmapped BAM file with human data.  The contents of the unmapped BAM file will be aligned and written to disk.  The md5 of the resulting file will be calculated and compared to a known good md5.
</p>

<h2><span id="Unsupported:_using_the_BWA.2FC_bindings_from_within_Matlab"> Unsupported: using the BWA/C bindings from within Matlab </span></h2>

<p>Some users are attempting to use the BWA/C bindings from within Matlab.  To run the GATK within Matlab, you'll need to add libbwa.so to your library path through the librarypath.txt file.  The librarypath.txt file normally lives in $matlabroot/toolbox/local.  Within the Broad Institute, the $matlabroot/toolbox/local/librarypath.txt file is shared; therefore, you'll have to create a librarypath.txt file in your working directory from which you execute matlab.
</p>

<pre class="code codeBlock" spellcheck="false">
##
## FILE: librarypath.txt
##
## Entries:
##    o path_to_jnifile
##    o [alpha,glnx86,sol2,unix,win32,mac]=path_to_jnifile
##    o $matlabroot/path_to_jnifile
##    o $jre_home/path_to_jnifile
##
$matlabroot/bin/$arch
/humgen/gsa-scr1/GATK_Data/bwa/stable
</pre>

<p>Once you've edited the library path, you can verify that Matlab has picked up your modified file by running the following command:
</p>

<pre class="code codeBlock" spellcheck="false">
&gt;&gt; java.lang.System.getProperty('java.library.path')
     
ans =
/broad/tools/apps/matlab2009b/bin/glnxa64:/humgen/gsa-scr1/GATK_Data/bwa/stable
</pre>

<p>Once the location of libbwa.so has been added to the library path, you can use the BWACAligner just as you would any other Java class in Matlab:
</p>

<pre class="code codeBlock" spellcheck="false">
&gt;&gt; javaclasspath({'/humgen/gsa-scr1/hanna/src/Sting/dist/packages/Aligner/Aligner.jar'})
&gt;&gt; import org.broadinstitute.sting.alignment.bwa.BWTFiles
&gt;&gt; import org.broadinstitute.sting.alignment.bwa.BWAConfiguration
&gt;&gt; import org.broadinstitute.sting.alignment.bwa.c.BWACAligner
&gt;&gt; x = BWACAligner(BWTFiles('/humgen/gsa-scr1/GATK_Data/bwa/Homo_sapiens_assembly18.fasta'),BWAConfiguration())
&gt;&gt; y=x.getAllAlignments(uint8('CCAATAACCAAGGCTGTTAGGTATTTTATCAGCAATGTGGGATAAGCAC'));
</pre>

<p>We don't have the resources to directly support using the BWA/C bindings from within Matlab, but if you report problems to us, we will try to address them.
</p>

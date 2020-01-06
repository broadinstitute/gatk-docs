## GATK4-Alpha MarkDuplicates crashing

By AmandaChamberlain

<p>I've used Picard2.1 MarkDuplicates for a bunch of bam files (4-40x coverage) generated with Picard2.1 MergeSamFiles and it works fine. I'm trying to get GATK4-Alpha MarkDuplicates working on the bam files generated with GATK4-Alpha MergeSamFiles and it works fine for my lower coverage samples (4-5x) but anything greater than 8x is crashing. I get the following output</p>

<pre class="code codeBlock" spellcheck="false">[May 24, 2017 7:43:27 PM AEST] org.broadinstitute.hellbender.tools.picard.sam.markduplicates.MarkDuplicates done. Elapsed time: 98.36 minutes.
Runtime.totalMemory()=64997556224
Exception in thread "main" java.lang.NoClassDefFoundError: org/xerial/snappy/LoadSnappy
        at htsjdk.samtools.util.SnappyLoader.&lt;init&gt;(SnappyLoader.java:86)
        at htsjdk.samtools.util.SnappyLoader.&lt;init&gt;(SnappyLoader.java:52)
        at htsjdk.samtools.util.TempStreamFactory.getSnappyLoader(TempStreamFactory.java:42)
        at htsjdk.samtools.util.TempStreamFactory.wrapTempOutputStream(TempStreamFactory.java:74)
        at htsjdk.samtools.util.SortingCollection.spillToDisk(SortingCollection.java:223)
        at htsjdk.samtools.util.SortingCollection.add(SortingCollection.java:166)
        at org.broadinstitute.hellbender.tools.picard.sam.markduplicates.MarkDuplicates.buildSortedReadEndLists(MarkDuplicates.java:246)
        at org.broadinstitute.hellbender.tools.picard.sam.markduplicates.MarkDuplicates.doWork(MarkDuplicates.java:83)
        at org.broadinstitute.hellbender.cmdline.CommandLineProgram.runTool(CommandLineProgram.java:102)
        at org.broadinstitute.hellbender.cmdline.CommandLineProgram.instanceMainPostParseArgs(CommandLineProgram.java:155)
        at org.broadinstitute.hellbender.cmdline.PicardCommandLineProgram.instanceMain(PicardCommandLineProgram.java:61)
        at org.broadinstitute.hellbender.Main.instanceMain(Main.java:69)
        at org.broadinstitute.hellbender.Main.main(Main.java:84)
Caused by: java.lang.ClassNotFoundException: org.xerial.snappy.LoadSnappy
        at java.net.URLClassLoader.findClass(URLClassLoader.java:381)
        at java.lang.ClassLoader.loadClass(ClassLoader.java:424)
        at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:331)
        at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
        ... 13 more
</pre>

<p>Can you provide any guidance on what is causing it to crash?</p>

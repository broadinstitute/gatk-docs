## Segment Mean must be finite

By esalinas

<p>I ran GATK4 AllelicCNV program and get an error message saying "Segment Mean must be finite." (see below)</p>

<p>I look at the input files I give the program and no values were listed as "Inf" or "Infinity" or "-Inf" or anything like that.<br>
All the values I saw (sorting numerically on the column a la "sort -g" ) showed values as low as -8 or so and as high as 8 or so with most values near 0.  Because I saw values in the range +/- 8 I'm confused as to the source of the error.  I'm not sure if this is a bug or a known issue?</p>

<p>-eddie</p>

<pre class="code codeBlock" spellcheck="false">14:59:47.184 INFO  AllelicCNV - Shutting down engine
[June 20, 2017 2:59:47 PM UTC] org.broadinstitute.hellbender.tools.exome.AllelicCNV done. Elapsed time: 0.09 minutes.
Runtime.totalMemory()=910688256
java.lang.IllegalArgumentException: Segment Mean must be finite.
        at org.broadinstitute.hellbender.utils.param.ParamUtils.isFinite(ParamUtils.java:180)
        at org.broadinstitute.hellbender.tools.exome.ModeledSegment.&lt;init&gt;(ModeledSegment.java:22)
        at org.broadinstitute.hellbender.tools.exome.SegmentUtils.toModeledSegment(SegmentUtils.java:548)
        at org.broadinstitute.hellbender.tools.exome.SegmentUtils.lambda$readModeledSegmentsFromSegmentFile$721(SegmentUtils.java:185)
        at org.broadinstitute.hellbender.utils.tsv.TableUtils$1.createRecord(TableUtils.java:112)
        at org.broadinstitute.hellbender.utils.tsv.TableReader.fetchNextRecord(TableReader.java:355)
        at org.broadinstitute.hellbender.utils.tsv.TableReader.access$200(TableReader.java:94)
        at org.broadinstitute.hellbender.utils.tsv.TableReader$1.hasNext(TableReader.java:458)
        at java.util.Iterator.forEachRemaining(Iterator.java:115)
        at java.util.Spliterators$IteratorSpliterator.forEachRemaining(Spliterators.java:1801)
        at java.util.stream.AbstractPipeline.copyInto(AbstractPipeline.java:481)
        at java.util.stream.AbstractPipeline.wrapAndCopyInto(AbstractPipeline.java:471)
        at java.util.stream.ReduceOps$ReduceOp.evaluateSequential(ReduceOps.java:708)
        at java.util.stream.AbstractPipeline.evaluate(AbstractPipeline.java:234)
        at java.util.stream.ReferencePipeline.collect(ReferencePipeline.java:499)
        at org.broadinstitute.hellbender.tools.exome.SegmentUtils.readSegmentFile(SegmentUtils.java:421)
        at org.broadinstitute.hellbender.tools.exome.SegmentUtils.readModeledSegmentsFromSegmentFile(SegmentUtils.java:184)
        at org.broadinstitute.hellbender.tools.exome.AllelicCNV.runPipeline(AllelicCNV.java:290)
        at org.broadinstitute.hellbender.engine.spark.SparkCommandLineProgram.doWork(SparkCommandLineProgram.java:38)
        at org.broadinstitute.hellbender.cmdline.CommandLineProgram.runTool(CommandLineProgram.java:115)
        at org.broadinstitute.hellbender.cmdline.CommandLineProgram.instanceMainPostParseArgs(CommandLineProgram.java:170)
        at org.broadinstitute.hellbender.cmdline.CommandLineProgram.instanceMain(CommandLineProgram.java:189)
        at org.broadinstitute.hellbender.Main.runCommandLineProgram(Main.java:122)
        at org.broadinstitute.hellbender.Main.mainEntry(Main.java:143)
        at org.broadinstitute.hellbender.Main.main(Main.java:221)
esalinas@eddie-instance-1:~/fc-9c84e685-79f8-4d84-9e52-640943257a9b/ef545f92-5053-4087-ad54-09fa884d0494/wgs_cnv_work/f2df0580-2445-4ea3-af41-d5a8a3107f5b$ 

</pre>

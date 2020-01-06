## ArrayIndexOutOfBoundsException error in BaseRecalibratorSpark

By joe297

<p>Hi,<br>
Thank you for your time.<br>
I ran BaseRecalibratorSpark with GATK4 and GATK4-protected on Amazon instance. Both of them gave me error <code class="code codeInline" spellcheck="false">java.lang.ArrayIndexOutOfBoundsException: 1073741865</code>. When running small dataset I didn't get this error, but when I used the real dataset, it appears.</p>

<p>The command I used is <code class="code codeInline" spellcheck="false">gatk4 BaseRecalibratorSpark -I xx_markduplicatespark.bam -knownSites /genome/ref/dbsnp_138.b37.vcf -knownSitesb37.vcf -O xx_baserecalibratespark.table -R /curr/tianj/data/humann_g1k_v37.2bit --TMP_DIR tmp</code></p>

<p>And here is the error message,</p>

<hr></hr><p>"<br>
Using GATK wrapper script /curr/tianj/software/gatk/build/install/gatk/bin/gatk<br>
Running:<br>
    /curr/tianj/software/gatk/build/install/gatk/bin/gatk BaseRecalibratorSpark -I A15_markduplicatespark.bam -knownSites ref/Mills_and_1000G_gold_standard.indels.b37.vcf -O A15_baserecalibratespark.table -R /curr/tianj/data/humann_g1k_v37.2bit<br>
17:19:00.338 INFO  NativeLibraryLoader - Loading libgkl_compression.so from jar:file:/curr/tianj/software/gatk/build/instabgkl_compression.so<br>
[May 17, 2017 5:19:00 PM UTC] org.broadinstitute.hellbender.tools.spark.BaseRecalibratorSpark  --knownSites /genome/ref/db_1000G_gold_standard.indels.b37.vcf --output A15_baserecalibratespark.table --reference /curr/tianj/data/humann_g1k_v37.2bp  --joinStrategy BROADCAST --mismatches_context_size 2 --indels_context_size 3 --maximum_cycle_value 500 --mismatches_defdeletions_default_quality 45 --low_quality_tail 2 --quantizing_levels 16 --bqsrBAQGapOpenPenalty 40.0 --preserve_qscores_lles false --useOriginalQualities false --defaultBaseQualities -1 --readShardSize 10000 --readShardPadding 1000 --readValid-interval_padding 0 --interval_exclusion_padding 0 --bamPartitionSize 0 --disableSequenceDictionaryValidation false --sharl[*] --help false --version false --showHidden false --verbosity INFO --QUIET false --use_jdk_deflater false --use_jdk_inf<br>
[May 17, 2017 5:19:00 PM UTC] Executing as tianj@ip-172-31-78-66 on Linux 4.4.41-36.55.amzn1.x86_64 amd64; Java HotSpot(TM:4.alpha.2-261-gb8d32ee-SNAPSHOT<br>
17:19:00.371 INFO  BaseRecalibratorSpark - Defaults.BUFFER_SIZE : 131072<br>
17:19:00.371 INFO  BaseRecalibratorSpark - Defaults.COMPRESSION_LEVEL : 1<br>
17:19:00.371 INFO  BaseRecalibratorSpark - Defaults.CREATE_INDEX : false<br>
17:19:00.371 INFO  BaseRecalibratorSpark - Defaults.CREATE_MD5 : false<br>
17:19:00.371 INFO  BaseRecalibratorSpark - Defaults.CUSTOM_READER_FACTORY :<br>
17:19:00.371 INFO  BaseRecalibratorSpark - Defaults.EBI_REFERENCE_SERVICE_URL_MASK : <a href="http://www.ebi.ac.uk/ena/cram/md5/%s" rel="nofollow">http://www.ebi.ac.uk/ena/cram/md5/%s</a><br>
17:19:00.371 INFO  BaseRecalibratorSpark - Defaults.NON_ZERO_BUFFER_SIZE : 131072<br>
17:19:00.371 INFO  BaseRecalibratorSpark - Defaults.REFERENCE_FASTA : null<br>
17:19:00.371 INFO  BaseRecalibratorSpark - Defaults.SAM_FLAG_FIELD_FORMAT : DECIMAL<br>
17:19:00.371 INFO  BaseRecalibratorSpark - Defaults.USE_ASYNC_IO_READ_FOR_SAMTOOLS : false<br>
17:19:00.371 INFO  BaseRecalibratorSpark - Defaults.USE_ASYNC_IO_WRITE_FOR_SAMTOOLS : true<br>
17:19:00.371 INFO  BaseRecalibratorSpark - Defaults.USE_ASYNC_IO_WRITE_FOR_TRIBBLE : false<br>
17:19:00.371 INFO  BaseRecalibratorSpark - Defaults.USE_CRAM_REF_DOWNLOAD : false<br>
17:19:00.371 INFO  BaseRecalibratorSpark - Deflater IntelDeflater<br>
17:19:00.372 INFO  BaseRecalibratorSpark - Inflater IntelInflater<br>
17:19:00.372 INFO  BaseRecalibratorSpark - Initializing engine<br>
17:19:00.372 INFO  BaseRecalibratorSpark - Done initializing engine<br>
17:19:00.872 WARN  NativeCodeLoader:62 - Unable to load native-hadoop library for your platform... using builtin-java clas<br>
17:22:09.153 INFO  BaseRecalibratorSpark - Shutting down engine<br>
[May 17, 2017 5:22:09 PM UTC] org.broadinstitute.hellbender.tools.spark.BaseRecalibratorSpark done. Elapsed time: 3.15 min<br>
Runtime.totalMemory()=15504244736<br>
java.lang.ArrayIndexOutOfBoundsException: 1073741865<br>
        at com.esotericsoftware.kryo.util.IdentityObjectIntMap.clear(IdentityObjectIntMap.java:382)<br>
        at com.esotericsoftware.kryo.util.MapReferenceResolver.reset(MapReferenceResolver.java:65)<br>
        at com.esotericsoftware.kryo.Kryo.reset(Kryo.java:865)<br>
        at com.esotericsoftware.kryo.Kryo.writeClassAndObject(Kryo.java:630)<br>
        at org.apache.spark.serializer.KryoSerializationStream.writeObject(KryoSerializer.scala:195)<br>
        at org.apache.spark.broadcast.TorrentBroadcast$$anonfun$blockifyObject$2.apply(TorrentBroadcast.scala:236)<br>
        at org.apache.spark.broadcast.TorrentBroadcast$$anonfun$blockifyObject$2.apply(TorrentBroadcast.scala:236)<br>
        at org.apache.spark.util.Utils$.tryWithSafeFinally(Utils.scala:1310)<br>
        at org.apache.spark.broadcast.TorrentBroadcast$.blockifyObject(TorrentBroadcast.scala:237)<br>
        at org.apache.spark.broadcast.TorrentBroadcast.writeBlocks(TorrentBroadcast.scala:107)<br>
        at org.apache.spark.broadcast.TorrentBroadcast.(TorrentBroadcast.scala:86)<br>
        at org.apache.spark.broadcast.TorrentBroadcastFactory.newBroadcast(TorrentBroadcastFactory.scala:34)<br>
        at org.apache.spark.broadcast.BroadcastManager.newBroadcast(BroadcastManager.scala:56)<br>
        at org.apache.spark.SparkContext.broadcast(SparkContext.scala:1387)<br>
        at org.apache.spark.api.java.JavaSparkContext.broadcast(JavaSparkContext.scala:646)<br>
        at org.broadinstitute.hellbender.engine.spark.BroadcastJoinReadsWithVariants.join(BroadcastJoinReadsWithVariants.j<br>
        at org.broadinstitute.hellbender.engine.spark.AddContextDataToReadSpark.add(AddContextDataToReadSpark.java:67)<br>
        at org.broadinstitute.hellbender.tools.spark.BaseRecalibratorSpark.runTool(BaseRecalibratorSpark.java:93)<br>
        at org.broadinstitute.hellbender.engine.spark.GATKSparkTool.runPipeline(GATKSparkTool.java:353)<br>
        at org.broadinstitute.hellbender.engine.spark.SparkCommandLineProgram.doWork(SparkCommandLineProgram.java:38)<br>
        at org.broadinstitute.hellbender.cmdline.CommandLineProgram.runTool(CommandLineProgram.java:116)<br>
        at org.broadinstitute.hellbender.cmdline.CommandLineProgram.instanceMainPostParseArgs(CommandLineProgram.java:179)<br>
        at org.broadinstitute.hellbender.cmdline.CommandLineProgram.instanceMain(CommandLineProgram.java:198)<br>
        at org.broadinstitute.hellbender.Main.runCommandLineProgram(Main.java:121)<br>
        at org.broadinstitute.hellbender.Main.mainEntry(Main.java:142)<br>
        at org.broadinstitute.hellbender.Main.main(Main.java:220)<br>
"</p>

<hr></hr><p>Sorry, but I was wrong before. This error appears also in small dataset.</p>

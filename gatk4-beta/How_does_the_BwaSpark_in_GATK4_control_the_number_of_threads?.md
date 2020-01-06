## How does the BwaSpark in GATK4 control the number of threads?

By cattle

<p>I tried to ERR000589 process data with BwaSpark. The bam file size is 1.3G. The average time spent is about 25 min (5 nodes).<br>
However it would only cost 5 min in processing same data if I tried to use original C bwa with 32 threads.<br>
Base on this observation, I have several questions list as follow:<br>
1. If there is anything wrong with my params?<br>
2. For each Partition, is BwaSpark running in multi-thread mode?<br>
3. How to control the number of the bwa threads inside BwaSpark?</p>

<p>P.S. <br>
The running command is:<br>
./gatk-launch BwaSpark -I hdfs:///user/XX/ERR000589/ERR000589.bam -O hdfs:///user/XX/ERR000589/ERR000589_bwa.bam -R hdfs:///user/xx/refs/ucsc.hg19.fasta --bwamemIndexImage ~/data/ref/ucsc.hg19.img -disableSequenceDictionaryValidation true -- --sparkRunner SPARK --sparkMaster  --executor-cores 1 --total-executor-cores 16 --executor-memory 4G</p>

<p>I tried to further adjust the following parameters,<br>
--executor-cores  --total-executor-cores  --executor-memory   --driver-memory<br>
but none of these took less time than 16 min</p>

<p>Besides, I alsow tried to run it in local mode, while it won't end successfully. It seems that CPU was in endless waiting. I guess it occupied so much memory that the swap space is in use? Pic 1 shows the memory consumed while running <br>
This time, the command is:<br>
./gatk-launch BwaSpark -I hdfs:///user/XX/ERR000589/ERR000589.bam -O hdfs:///user/xx/ERR000589/ERR000589.bwa.bam -R /software/home/xx/data/ref/ucsc.hg19.fasta \ --bwamemIndexImage ~/data/ref/ucsc.hg19.img -disableSequenceDictionaryValidation true -- --sparkRunner SPARK --sparkMaster local[*] --total-executor-cores 8 --executor-memory 20G --driver-memory 30G</p>

<p>BTW, the testing environment is:<br>
CPU 2 X 8 physical core<br>
node: 5<br>
network: GBE<br>
memory: 64G</p>

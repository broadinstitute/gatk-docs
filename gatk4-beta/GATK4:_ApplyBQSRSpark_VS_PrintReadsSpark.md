## GATK4: ApplyBQSRSpark VS PrintReadsSpark

By mgujral

<p>Hello there,</p>

<p>What is the difference between  GATK4 tools ApplyBQSRSpark and PrintReadsSpark?<br>
  java -jar GenomeAnalysisTk-4_1.jar PrintReadsSpark -h suggests that output is String as shown below<br>
--output,-O:String            uri for the output file: a local file path.</p>

<p>While on the website <a href="https://github.com/broadinstitute/gatk" rel="nofollow">https://github.com/broadinstitute/gatk</a>   output is bam for PrintReadsSpark  option as shown below<br>
./gatk-launch PrintReadsSpark -I input.bam -O output.bam</p>

<p>I plan to start testing GATK-4 in coming days. Any thoughts or suggestions will be greatly appreciated.</p>

<p>best<br>
--Madhu</p>

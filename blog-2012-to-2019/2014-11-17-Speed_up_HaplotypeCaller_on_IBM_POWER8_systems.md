## Speed up HaplotypeCaller on IBM POWER8 systems

By Geraldine_VdAuwera

<p>We all know how HaplotypeCaller and Mutect2 analyses can take a long time. IBM is now providing a native implementation of the PairHMM algorithm that leverages the new hardware available in their POWER8 systems. The optimized native library is currently available on POWER8 for the following Linux distributions: Ubuntu 15.10, Ubuntu 16.04 and Red Hat Enterprise Linux 7.1, Red Hat Enterprise Linux 7.2.</p>

<p>To take advantage of the optimized library, you need to do the following:</p>

<ul><li>Download the shared library corresponding to your Linux distribution from <a rel="nofollow" href="https://drive.google.com/folderview?id=0BwTg3aXzGxEDaEpocHVRMkV2RUE&amp;usp=sharing">here</a></li>
<li>Set your java library path to the location of <code class="code codeInline" spellcheck="false">libVectorLoglessPairHMM.so</code> using <code class="code codeInline" spellcheck="false">-Djava.library.path</code></li>
</ul><p>Here is an example for running HaplotypeCaller on a P8 system with Ubuntu:</p>

<pre class="code codeBlock" spellcheck="false">export PHMM_N_THREADS=$Num
java -Xmx32g -Djava.library.path=/path/to/PairHMM_P8_Ubuntu -jar $GATK_PATH/GenomeAnalysisTK.jar \
-T HaplotypeCaller \
-R $REFERENCE -I $INPUT_BAM --dbsnp $SNP_VCF \
-stand_emit_conf 10 -stand_call_conf 50 \
-o $OUTPUT_VCF
</pre>

<p>Here is an example for running Mutect2 on a P8 system with Ubuntu:</p>

<pre class="code codeBlock" spellcheck="false">export PHMM_N_THREADS=$Num
java -Djava.library.path=/path/to/PairHMM_P8_Ubuntu -jar GenomeAnalysisTK.jar \
-T MuTect2 \
-R $REFERENCE -L $GENOME_INTERVALS_FILE \
-I:tumor $TUMOR_BAM -I:normal $NORMAL_BAM \
--cosmic $COSMIC_VCF --dbsnp $SNP_VCF \
-o $OUT_VCF
</pre>

<p>The latest version of the library uses the same floating precision as Java on POWER8, so it generates the same result as without the library. Also, exploiting multithreading along with the SIMD vectorization, it can accelerate HaplotypeCaller and Mutect2 more than the previous version, especially in the single-thread mode (no <code class="code codeInline" spellcheck="false">-nct</code> option specified).</p>

<p>SMT is a processor technology that allows multiple instruction streams (threads) to run concurrently on the same physical processor, improving overall throughput. From the point of view of the operating system, each hardware thread is treated as an independent logical processor. On POWER8 there are SMT8, SMT4, SMT2 and ST mode, each physical processor will have 8, 4, 2 and 1 logical processor, respectively. This pairhmm library uses the number of thread equal to 37% of the available logical processors by default. The number of threads can be tuned by setting the environment variable PHMM_N_THREADS, as shown in above examples.</p>

<p>The library can accelerate HaplotypeCaller, Mutect2 and UnifiedGenotyper of GATK. It can accelerate HaplotypeCaller up to 1.9x and Mutect2 up to 9.26x depending on the test case. For example, if the PairHMM computation consumes about a half of the HaplotypeCaller runtime in single-thread mode, 1.88x speed-up can be expected.</p>

<p>The source code is available <a rel="nofollow" href="https://github.com/t-ogasawara/gatk/tree/vectorPairHMMForPower8">here</a>.<br>
If you have any questions or issues (aside from downloading the file), please contact Yinhue Cheng at IBM (ycheng@us.ibm.com) or Takeshi Ogasawara at IBM Japan (TAKESHI@jp.ibm.com).</p>

<p><em>Disclaimer: Please note that these libraries are not an official IBM product. You use it entirely at your own risk, and neither IBM nor the author assumes any liability whatsoever, nor do they assume responsibility for maintenance. Please report comments and corrections to ycheng@us.ibm.com.</em></p>

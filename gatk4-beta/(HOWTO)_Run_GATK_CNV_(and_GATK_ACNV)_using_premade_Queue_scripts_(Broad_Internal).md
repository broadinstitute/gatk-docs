## (HOWTO) Run GATK CNV (and GATK ACNV) using premade Queue scripts (Broad Internal)

By LeeTL1220

<p><strong>Notice: these workflows are out of date and should no longer be used. See the WDL scripts instead.</strong></p>

<hr></hr><p><em>These workflows are not officially supported, but are used extensively, internally, for our evaluations.</em></p>

<p><em>These instructions assume some familiarity with running Queue.</em></p>

<h4>Download Queue scala scripts</h4>

<p>Download the following files to the same directory.  This step only needs to be done once.</p>

<ul><li><code class="code codeInline" spellcheck="false">CaseSampleHBExomePipeline.scala</code> [<a href="http://www.broadinstitute.org/~lichtens/gatk4cnv/CaseSampleHBExomePipeline.scala]" rel="nofollow">http://www.broadinstitute.org/~lichtens/gatk4cnv/CaseSampleHBExomePipeline.scala]</a></li>
<li><code class="code codeInline" spellcheck="false">CreatePoNPipeline.scala</code> [<a href="http://www.broadinstitute.org/~lichtens/gatk4cnv/CreatePoNPipeline.scala]" rel="nofollow">http://www.broadinstitute.org/~lichtens/gatk4cnv/CreatePoNPipeline.scala]</a></li>
<li><code class="code codeInline" spellcheck="false">recapseg-hb-eval.jar</code> (Queue) [<a href="http://www.broadinstitute.org/~lichtens/gatk4cnv/recapseg-hb-eval.jar]" rel="nofollow">http://www.broadinstitute.org/~lichtens/gatk4cnv/recapseg-hb-eval.jar]</a></li>
<li><code class="code codeInline" spellcheck="false">gatk-protected.jar</code> [<a href="http://www.broadinstitute.org/~lichtens/gatk4cnv/gatk-protected.jar]" rel="nofollow">http://www.broadinstitute.org/~lichtens/gatk4cnv/gatk-protected.jar]</a></li>
</ul><p>Please note:  These locations are temporary and will be updated in the future.</p>

<h4>Test that invoking the help documentation works</h4>

<pre class="code codeBlock" spellcheck="false">java -jar recapseg-hb-eval.jar -S CreatePoNPipeline.scala --help
</pre>

<pre class="code codeBlock" spellcheck="false">java -jar recapseg-hb-eval.jar -S CaseSampleHBExomePipeline.scala --help
</pre>

<h4>Example case sample run with ACNV</h4>

<p><em>Parameters to the recapseg-hb-eval.jar may need to be adjusted depending on your execution environment</em></p>

<pre class="code codeBlock" spellcheck="false">#!/bin/bash -l
. /broad/tools/scripts/useuse
use .hdfview-2.9
use Java-1.8
use .r-3.1.3-gatk-only

### Modify parameters below this line

# Two simple text files listing each bam file on a separate line
#   Note that the lines in each file must correspond to case-control pairs.
CASE_SAMPLES=tumor_bam_files.txt
CONTROL_SAMPLES=normal_bam_files.txt

# Output location (absolute paths recommended).  
#  Seg file will appear in $OUTDIR/tumor_pcov
#  Calls will appear in $OUTDIR/caller
OUTDIR=/home/username/evals/out_case/

# Downloaded gatk-protected.jar
GATK4PJAR=gatk-protected.jar

# Directory of HDF5 JNI shared libraries (.so/.dynlib)
HDFLOC=/broad/software/free/Linux/redhat_6_x86_64/pkgs/hdfview_2.9/HDFView/lib/linux/

# Reference (This is b37 at the Broad Institute)
REF=/seq/references/Homo_sapiens_assembly19/v1/Homo_sapiens_assembly19.fasta

# Target list.  Must be the same as was used to create the PoN.
TARGETS=/home/lichtens/my_target_list.bed

# PoN file must be created with the same target file (above) and settings.
PON=/home/lichtens/evals/out_pon/create_pon/my_blood_normals.pon

# How much memory to allocate to each job, in GB
MEM=8

# List of SNPs in the interval_list format
SNP_LIST=my_list_of_common_het_sites.interval_list

# See --help for descriptions of these parameters.  
#  If `-keepDups` was specified for the PoN, it must be specified for case samples as well.
#  If `-pd 250` was specified for the PoN, it must be specified for case samples as well.
# -rawcov will generate a separate file for the raw counts as a separate process.
PD=250
OTHER_OPTS=" -jobResReq virtual_free=${MEM}G -keepDups -rawcov -jobQueue gsa -pd ${PD} -noWt "
OTHER_OPTS_ACNV=" -acnv  -snp ${SNP_LIST}  -icontrol ${CONTROL_SAMPLES} "

#### Do not modify below this line

# Run the sample(s)
java -jar recapseg-hb-eval.jar -S CaseSampleHBExomePipeline.scala -mem ${MEM} -pon ${PON} -i ${CASE_SAMPLES} -o ${OUTDIR} -gatk4pjar ${HBJAR} -r ${REF} -L ${TARGETS} -qsub -run -logDir ${OUTDIR} -hvl ${HDFLOC} -Z ${Z} ${OTHER_OPTS} ${OTHER_OPTS_ACNV}

</pre>

<h4>Example case sample run without ACNV</h4>

<p><em>Parameters to the recapseg-hb-eval.jar may need to be adjusted depending on your execution environment</em></p>

<pre class="code codeBlock" spellcheck="false">#!/bin/bash -l
. /broad/tools/scripts/useuse
use .hdfview-2.9
use Java-1.8
use .r-3.1.3-gatk-only

### Modify parameters below this line

# A simple text file listing each bam file on a separate line
INPUT_BAMS=case_bam_list.txt

# Output location (absolute paths recommended).  
#  Seg file will appear in $OUTDIR/tumor_pcov
#  Calls will appear in $OUTDIR/caller
OUTDIR=/home/username/evals/out_case/

# Downloaded gatk-protected.jar
GATK4PJAR=gatk-protected.jar

# Directory of HDF5 JNI shared libraries (.so/.dynlib)
HDFLOC=/broad/software/free/Linux/redhat_6_x86_64/pkgs/hdfview_2.9/HDFView/lib/linux/

# Reference (This is b37 at the Broad Institute)
REF=/seq/references/Homo_sapiens_assembly19/v1/Homo_sapiens_assembly19.fasta

# Target list.  Must be the same as was used to create the PoN.
TARGETS=/home/lichtens/my_target_list.bed

# PoN file must be created with the same target file (above) and settings.
PON=/home/lichtens/evals/out_pon/create_pon/my_blood_normals.pon

# How much memory to allocate to each job, in GB
MEM=8

# See --help for descriptions of these parameters.  
#  If `-keepDups` was specified for the PoN, it must be specified for case samples as well.
#  If `-pd 250` was specified for the PoN, it must be specified for case samples as well.
# -rawcov will generate a separate file for the raw counts as a separate process.
PD=250
OTHER_OPTS=" -jobResReq virtual_free=${MEM}G -keepDups -rawcov -jobQueue gsa -pd ${PD} -noWt "

#### Do not modify below this line

# Run the sample(s)
java -jar recapseg-hb-eval.jar -S CaseSampleHBExomePipeline.scala -mem ${MEM} -pon ${PON} -i ${INPUT_BAMS} -o ${OUTDIR} -hbJar ${GATK4PJAR} -r ${REF} -L ${TARGETS} -qsub -run -logDir ${OUTDIR} -hvl ${HDFLOC} ${OTHER_OPTS}

</pre>

<h4>Example create PoN run</h4>

<p><em>Parameters to the Queue jar (recapseg-hb-eval.jar) may need to be adjusted depending on your execution environment</em></p>

<pre class="code codeBlock" spellcheck="false">#!/bin/bash -l
. /broad/tools/scripts/useuse
use .hdfview-2.9
use Java-1.8
use .r-3.1.3-gatk-only

### Modify parameters below this line

# A simple text file listing each bam file on a separate line
INPUT_BAMS=blood_normals_bam_list.txt

# Output location (absolute paths recommended).  
#  PoN file will appear as $OUTDIR/create_pon/$PON_FILENAME
OUTDIR=/home/lichtens/evals/out_pon/

# Downloaded gatk-protected.jar
GATK4PJAR=gatk-protected.jar

# Directory of HDF5 JNI shared libraries (.so/.dynlib)
HDFLOC=/broad/software/free/Linux/redhat_6_x86_64/pkgs/hdfview_2.9/HDFView/lib/linux/

# Reference (This is b37 at the Broad Institute)
REF=/seq/references/Homo_sapiens_assembly19/v1/Homo_sapiens_assembly19.fasta

# Base PoN filename
#  This parameter is only the base filename.  Do not specify a directory.  This file will appear as
#  $OUTDIR/create_pon/$PON_FILENAME
PON_FILENAME=my_blood_normals.pon

# Target list that was used in the capture process.
TARGETS=/home/lichtens/my_target_list.bed

# How much memory to allocate to each coverage job, in GB.  
# 208k targets needs approx. 6GB RAM
MEM=6

#  Larger PoNs can require a lot of RAM (208k targets x 300 samples needs approx. 14GB RAM)
MEM_PON=14

# Number of cores to use when multicore functionality is available.
CORES=4

# See --help for descriptions of these parameters.  
#  If `-keepDups` was specified for the PoN, it must be specified for case samples as well.
#  If `-pd 250` was specified for the PoN, it must be specified for case samples as well.
# `-rawcov` will generate a separate file for the raw counts as a separate process.
OTHER_OPTS=" -jobResReq 'virtual_free=${MEM}G' -keepDups -rawcov -jobQueue gsa -pd 250 -sparkMaster 'local[${CORES}]' "

#### Do not modify below this line

# Run the sample(s)
java -jar recapseg-hb-eval.jar -S CreatePoNPipeline.scala -pon ${PON} -i ${IN} -o ${OUTDIR} -gatk4pjar ${HBJAR} -r ${REF} -L ${TARGETS} -qsub -run -logDir ${OUTDIR} -hvl ${HDFLOC} -mem ${MEM} -mem_pon ${MEM_PON}  ${OTHER_OPTS}

</pre>

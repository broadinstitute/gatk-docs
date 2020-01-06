## Creating Amplicon Sequences - RETIRED

By delangel

<h3>Please note that this article has not been updated in a very long time and may no longer be applicable. Use at your own risk.</h3>

<hr></hr><h3>Creating Amplicon Sequences</h3>

<p>Note that earlier versions of the GATK used a different tool.</p>

<p>For a complete, detailed argument reference, refer to the GATK document page <a rel="nofollow" href="http://www.broadinstitute.org/gatk/gatkdocs/org_broadinstitute_sting_gatk_walkers_validation_ValidationAmplicons.html">here</a><br></p>

<table id="toc"><tr><td><div id="toctitle"><h2>Contents</h2></div>
<ul><li><a rel="nofollow" href="#Introduction">1 Introduction</a>
<ul><li><a rel="nofollow" href="#Lowercase_and_Ns">1.1 Lowercase and Ns</a></li>
<li><a rel="nofollow" href="#BWA_Bindings">1.2 BWA Bindings</a></li>
</ul></li>
<li><a rel="nofollow" href="#Running_Validation_Amplicons">2 Running Validation Amplicons</a></li>
<li><a rel="nofollow" href="#Validation_Amplicons_Output">3 Validation Amplicons Output</a></li>
<li><a rel="nofollow" href="#Warnings_During_Traversal">4 Warnings During Traversal</a></li>
</ul></td></tr></table><h2> <span id="Introduction">Introduction</span></h2>

<p>This tool generates amplicon sequences for use with the Sequenom primer design tool. The output of this tool is fasta-formatted, where the characters [A/B] specify the allele to be probed (see Validation Amplicons Output further below). It can mask nearby variation (either by 'N' or by lower-casing characters), and can try to restrict sequenom design to regions of the amplicon likely to generate a highly specific primer. This tool will also flag sites with properties that could shift the mass-spec peak from its expected value, such as indels in the amplicon sequence, SNPs within 4 bases of the variant attempting to be probed, or multiple variants selected for validation falling into the same amplicon.
</p>

<h4> <span id="Lowercase_and_Ns">Lowercase and Ns</span></h4>

<p>Ns in the amplicon sequence instructs primer design software (such as Sequenom) not to use that base in the primer: any primer will fall entirely before, or entirely after, that base. Lower-case letters instruct the design software to try to avoid using the base (presumably by applying a penalty for doing so), but will not prevent it from doing so if a good primer (i.e. a primer with suitable melting temperature and low probability of hairpin formation) is found.
</p>

<h4><span id="BWA_Bindings">BWA Bindings</span></h4>

<p>ValidationAmplicons relies on the GATK <a rel="nofollow" href="http://www.broadinstitute.org/gatk/guide/topic?name=methods-and-workflows#60" title="Sting BWA/C bindings">Sting BWA/C bindings</a> to assess the specificity of potential primers. The <a rel="nofollow" href="http://www.broadinstitute.org/gatk/guide/topic?name=methods-and-workflows#60" title="Sting BWA/C bindings">wiki page for Sting BWA/C bindings</a> contains <b>required information</b> about how to download the appropriate version of BWA, how to create a BWT reference, and how to set your classpath appropriately to run this tool. <b>If you have not followed the directions to set up the BWA/C bindings, you will not be able to create validation amplicon sequences using the GATK</b>. There is an argument (see below) to disable the use of BWA, and lower repeats within the amplicon only. Use of this argument is not recommended.
</p>

<h2><span id="Running_Validation_Amplicons">Running Validation Amplicons </span></h2>

<p>Validation Amplicons <b>requires</b> three input files: a VCF of alleles you want to validate, a VCF of variants you want to mask, and a Table of intervals around the variants describing the size of the amplicons. For instance:
</p>

<p><b>Alleles to Validate</b>
</p>

<pre class="code codeBlock" spellcheck="false">
##fileformat=VCFv4.0
#CHROM      POS     ID      REF     ALT     QUAL    FILTER  INFO
20          207414  .       G    A       85.09   PASS    .   // SNP to validate
20          792122  .    TCCC    T       22.24   PASS    .   // DEL to validate
20          994145  .       G    GAAG    48.21   PASS    .   // INS to validate
20          1074230 .       C    T       2.29     QD     .   // SNP to validate (but filtered)
20          1084330 .      AC   GT       42.21   PASS    .   // MNP to validate 
</pre>

<p><b>Interval Table</b>
</p>

<pre class="code codeBlock" spellcheck="false">
HEADERpos               name
20:207334-207494        20_207414
20:792042-792202        20_792122
20:994065-994225        20_994145
20:1074150-1074310      20_1074230
20:1084250-1084410      20_1084330
</pre>

<p><b>Alleles to Mask</b>
</p>

<pre class="code codeBlock" spellcheck="false">
##fileformat=VCFv4.1
#CHROM  POS      ID        REF         ALT     QUAL       FILTER         INFO
20      207414   .         G           A       77.12      PASS            .
20      207416   .         A           AGGC    49422.34   PASS            .
20      792076   .         A           G       2637.15    HaplotypeScore  .
20      792080   .         T           G       161.83     PASS            .
20      792087   .         CGGT        C       179.84     ReadPosRankSum  .
20      792106   .         C           G       32.59      PASS            .
20      792140   .         C           G       409.75     PASS            .
20      1084319  .         T          A,C      22.24      PASS            .
20      1084348  .  TACCACCCCACACA     T      482.84      PASS            .
</pre>

<h2><span id="Validation_Amplicons_Output"> Validation Amplicons Output </span></h2>

<p>The output from Validation Amplicons is a fasta-formatted file, with a small adaptation to represent the site being probed. Using the test files above, the output of the command
</p>

<pre class="code codeBlock" spellcheck="false">
java -jar $GATK/dist/GenomeAnalysisTK.jar \
-T ValidationAmplicons \
-R /humgen/1kg/reference/human_g1k_v37.fasta \
-BTI ProbeIntervals \
--ProbeIntervals:table interval_table.table \
--ValidateAlleles:vcf sites_to_validate.vcf \
--MaskAlleles:vcf mask_sites.vcf \
--virtualPrimerSize 30 \
-o probes.fasta \
-l WARN
</pre>

<p>is
</p>

<pre class="code codeBlock" spellcheck="false">
&gt;20:207414 INSERTION=1,VARIANT_TOO_NEAR_PROBE=1, 20_207414
CCAACGTTAAGAAAGAGACATGCGACTGGGTgcggtggctcatgcctggaaccccagcactttgggaggccaaggtgggc[A/G*]gNNcacttgaggtcaggagtttgagaccagcctggccaacatggtgaaaccccgtctctactgaaaatacaaaagttagC
&gt;20:792122 Valid 20_792122
TTTTTTTTTagatggagtctcgctcttatcgcccaggcNggagtgggtggtgtgatcttggctNactgcaacttctgcct[-/CCC*]cccaggttcaagtgattNtcctgcctcagccacctgagtagctgggattacaggcatccgccaccatgcctggctaatTT
&gt;20:994145 Valid 20_994145
TCCATGGCCTCCCCCTGGCCCACGAAGTCCTCAGCCACCTCCTTCCTGGAGGGCTCAGCCAAAATCAGACTGAGGAAGAAG[AAG/-*]TGGTGGGCACCCACCTTCTGGCCTTCCTCAGCCCCTTATTCCTAGGACCAGTCCCCATCTAGGGGTCCTCACTGCCTCCC
&gt;20:1074230 SITE_IS_FILTERED=1, 20_1074230
ACCTGATTACCATCAATCAGAACTCATTTCTGTTCCTATCTTCCACCCACAATTGTAATGCCTTTTCCATTTTAACCAAG[T/C*]ACTTATTATAtactatggccataacttttgcagtttgaggtatgacagcaaaaTTAGCATACATTTCATTTTCCTTCTTC
&gt;20:1084330 DELETION=1, 20_1084330
CACGTTCGGcttgtgcagagcctcaaggtcatccagaggtgatAGTTTAGGGCCCTCTCAAGTCTTTCCNGTGCGCATGG[GT/AC*]CAGCCCTGGGCACCTGTNNNNNNNNNNNNNTGCTCATGGCCTTCTAGATTCCCAGGAAATGTCAGAGCTTTTCAAAGCCC
</pre>

<p>Note that SNPs have been masked with 'N's, filtered 'mask' variants do not appear, the insertion has been flanked by Ns, the unfiltered deletion has been replaced by Ns, and the filtered site in the validation VCF is not marked as valid. In addition, bases that fall inside at least one non-unique 30-mer (meaning no multiple MQ0 alignments using BWA) are lower-cased. The identifier for each sequence is the position of the allele to be probed, a 'validation status' (defined below), and a string representing the amplicon. Validation status values are: 
</p>

<pre class="code codeBlock" spellcheck="false">
Valid                     // amplicon is valid
SITE_IS_FILTERED=1        // validation site is not marked 'PASS' or '.' in its filter field ("you are trying to validate a filtered variant")
VARIANT_TOO_NEAR_PROBE=1  // there is a variant too near to the variant to be validated, potentially shifting the mass-spec peak
MULTIPLE_PROBES=1,        // multiple variants to be validated found inside the same amplicon
DELETION=6,INSERTION=5,   // 6 deletions and 5 insertions found inside the amplicon region (from the "mask" VCF), will be potentially difficult to validate
DELETION=1,               // deletion found inside the amplicon region, could shift mass-spec peak
START_TOO_CLOSE,          // variant is too close to the start of the amplicon region to give sequenom a good chance to find a suitable primer
END_TOO_CLOSE,            // variant is too close to the end of the amplicon region to give sequenom a good chance to find a suitable primer
NO_VARIANTS_FOUND,        // no variants found within the amplicon region
INDEL_OVERLAPS_VALIDATION_SITE, // an insertion or deletion interferes directly with the site to be validated (i.e. insertion directly preceding or postceding, or a deletion that spans the site itself)
</pre>

<h2><span id="Warnings_During_Traversal"> Warnings During Traversal </span></h2>

<p>The files provided to Validation Amplicons should be such that all generated amplicons are valid. That means:
</p>

<pre class="code codeBlock" spellcheck="false">
There are no variants within 4bp of the site to be validated
There are no indels in the amplicon region
Amplicon windows do not include other sites to be probed
Amplicon windows are not too short, and the variant therein is not within 50bp of either edge
All amplicon windows contain a variant to be validated
Variants to be validated are unfiltered or pass filters
</pre>

<p>The tool will warn you each time any of these conditions are not met.
</p>

## Funcotator: Functional annotation out of beta

By jonn

<h2>A production-ready tool to predict variant function</h2>

<p>For the past year Funcotator has been a beta tool in GATK. With this new 4.1 release, Funcotator is (finally) out of beta and ready for use in a production environment. So... what exactly is Funcotator, and why should you care?</p>

<p>Funcotator is a functional annotator (FUNCtional annOTATOR) that reads in variants and adds useful information about their potential effects. It’s uses range from answering the question ‘in which gene (if any) does this variant occur’ to predicting an amino acid change string for variants that result in different protein structures. Accurate functional annotation is critical to turning vast amounts of genomic data into a better understanding of protein function.</p>

<p>Created to be a fast, functional, and accurate annotation tool that supports the hg38 genome, many recent updates have made Funcotator more robust and more correct. Of particular note -  the protein change string algorithm can now account for protein changes that do not occur at the same amino acid position as a variant (such as when deletions occur in short tandem repeats). If you have a set of variants and wish to identify the genes affected and/or the protein amino acid sequence change, or if you simply wish to cross-reference your variants with a list of variants thought to be implicated in disease - Funcotator is the tool for you.</p>

<p>We publish two sets of data sources to go with Funcotator (including Gencode, ClinVar, gnomAD, and more) so it can be used out of the box and with minimal effort to add annotations to either germline or somatic variants. Best of all, it can be updated by you, the user, to include your favorite annotation data sources when you annotate your VCF files (with some caveats).</p>

<hr></hr><h3>“Fun” means improved user experience and data output</h3>

<p>A huge number of bug fixes and accuracy improvements mean output is now much better and more correct than Oncotator. As an example of improved user experience, the new FuncotatorDataSourceDownloader tool enables downloading the data sources from which annotations are created directly from the command-line. It is as simple as running <code class="code codeInline" spellcheck="false">./gatk FuncotatorDataSourceDownloader --somatic</code> to get the somatic data sources (though there are more options for the tool as well).</p>

<h3>“Funcotator” versus “Oncotator” - very different annotator tools</h3>

<p>A savvy user may want to compare Funcotator to the Broad’s previous functional annotation tool Oncotator. Despite similar names and purpose, they are VERY different pieces of software and a direct comparison cannot really be made. Funcotator is not Oncotator. The forum post below details some of the differences between the two tools.</p>

<h3>Future of “Fun”</h3>

<p>There are many features on the horizon for Funcotator (in addition to normal support and bug fixes). In the long-term, we would like greatly increase performance with a Spark version of Funcotator. Adding even more supported data formats for data sources will offer users additional options to add in annotations. Since it is in active development, there are always small features being added and bugs being fixed.</p>

<p>Check for current progress on the GATK Github page here:<br><a href="https://github.com/broadinstitute/gatk/labels/Funcotator" rel="nofollow">https://github.com/broadinstitute/gatk/labels/Funcotator</a></p>

<p>A forum post with a tutorial and some additional data can be found here:<br><a href="https://gatkforums.broadinstitute.org/dsde/discussion/11193/funcotator-information-and-tutorial" rel="nofollow">https://gatkforums.broadinstitute.org/dsde/discussion/11193/funcotator-information-and-tutorial</a></p>

<p>The tool documentation for Funcotator can be found here:<br><a href="https://software.broadinstitute.org/gatk/documentation/tooldocs/current/org_broadinstitute_hellbender_tools_funcotator_Funcotator.php" rel="nofollow">https://software.broadinstitute.org/gatk/documentation/tooldocs/current/org_broadinstitute_hellbender_tools_funcotator_Funcotator.php</a></p>

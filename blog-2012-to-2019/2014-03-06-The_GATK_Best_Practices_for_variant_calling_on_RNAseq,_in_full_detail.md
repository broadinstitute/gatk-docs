## The GATK Best Practices for variant calling on RNAseq, in full detail

By Geraldine_VdAuwera

<p>We’re excited to introduce our Best Practices recommendations for calling variants on RNAseq data. These recommendations are based on our classic DNA-focused Best Practices, with some key differences in the early data processing steps, as well as in the calling step.</p>

<hr></hr><h3>Best Practices workflow for RNAseq</h3>

<p><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/f3/ec6fde05bcd6ae6ed4714d4d023b55.png" alt="image" class="embedImage-img importedEmbed-img"></img></p>

<p>This workflow is intended to be run per-sample; joint calling on RNAseq is not supported yet, though that is on our roadmap.</p>

<p>Please see the <a rel="nofollow" href="http://www.broadinstitute.org/gatk/guide/article?id=3891">new document here</a> for full details about how to run this workflow in practice.</p>

<p>In brief, the key modifications made to the DNAseq Best Practices focus on handling splice junctions correctly, which involves specific mapping and pre-processing procedures, as well as some new functionality in the HaplotypeCaller.</p>

<p>Now, before you try to run this on your data, there are a few important caveats that you need to keep in mind.</p>

<p>Please keep in mind that our DNA-focused Best Practices were developed over several years of thorough experimentation, and are continuously updated as new observations come to light and the analysis methods improve. We have only been working with RNAseq for a few months, so there are many aspects that we still need to examine in more detail before we can be fully confident that we are doing the best possible thing.</p>

<p>For one thing, these recommendations are based on high quality RNA-seq data (30 million 75bp paired-end reads produced on Illumina HiSeq). Other types of data might need slightly different processing. In addition, we have currently worked only on data from one tissue from one individual. Once we’ve had the opportunity to get more experience with different types (and larger amounts) of data, we will update these recommendations to be more comprehensive.</p>

<p>Finally, we know that the current recommended pipeline is producing both false positives (wrong variant calls) and false negatives (missed variants) errors. While some of those errors are inevitable in any pipeline, others are errors that we can and will address in future versions of the pipeline. A few examples of such errors are given in this article as well as our ideas for fixing them in the future.</p>

<p>We will be improving these recommendations progressively as we go, and we hope that the research community will help us by providing feedback of their experiences applying our recommendations to their data. We look forward to hearing your thoughts and observations!</p>

## What do GATK workshops cover?

By Geraldine_VdAuwera

<p>This is a summary description of our standard 3-day workshop, with optional 1-day pipelining add-on at the discretion of the organizer.</p>

<h3>Overview</h3>

<p>This workshop formula focuses on the core steps involved in calling variants with the Broad’s Genome Analysis Toolkit, using the “Best Practices” developed by the GATK team. You will learn why each step is essential to the variant discovery process, what are the operations performed on the data at each step, and how to use the GATK tools to get the most accurate and reliable results out of your dataset. In the course of this workshop, we highlight key functionalities such as the GVCF workflow for joint discovery of germline short variants in cohorts, somatic short variant discovery using Mutect2, and copy number variation discovery using GATK-CNV. Optionally, we can add an extra day to demonstrate the use of pipelining tools to assemble and execute GATK workflows.</p>

<p><strong>Version note:</strong> Starting January 2018, all workshops use GATK4 exclusively (no more GATK3).</p>

<hr></hr><h3>Program outline</h3>

<p>The workshop is composed of one day of lectures (including many opportunities for Q&amp;A) that is compulsory for all participants, and two days of hands-on training in different analysis use cases, which the host may choose to make optional. This formula can be extended with an additional day of hands-on training focused on pipelining, which can also be made optional by the host.</p>

<ul><li><strong>Day 1:</strong> <em>lectures:</em> Rationale, theory and application of the GATK Best Practices for Variant Discovery in high-throughput sequencing data (see details further below).</li>
<li><strong>Day 2 AM:</strong> <em>opt hands-on:</em> Germline short variant discovery (SNPs + Indels)</li>
<li><strong>Day 2 PM:</strong> <em>opt hands-on:</em> Germline short variant filtering (SNPs + Indels)</li>
<li><strong>Day 3 AM:</strong> <em>opt hands-on:</em> Somatic short variant discovery (SNPs + Indels)</li>
<li><strong>Day 3 PM:</strong> <em>opt hands-on:</em> Somatic copy number variant discovery (CNVs)</li>
<li><strong>Day 4 AM:</strong> <em>opt hands-on:</em> Writing pipelines with WDL</li>
<li><strong>Day 4 PM:</strong> <em>opt hands-on:</em> Running WDL pipelines in FireCloud</li>
</ul><p>In the optional hands-on sessions focused on analysis, we walk participants through exercises that teach them how to manipulate the standard data formats involved in variant discovery and how to apply GATK tools appropriately to common use cases and data types. In the course of these exercises, we demonstrate useful tips and tricks for interacting with GATK and Picard tools, dealing with problems, and using third-party tools such as Samtools, IGV, RStudio and RTG Tools.</p>

<p>In the optional hands-on sessions on pipelining, we walk participants through exercises that teach them to write workflow scripts using WDL, the Broad's new Workflow Description Language, and to execute these workflows locally as well as through FireCloud, our publicly available secure cloud-based analysis service.</p>

<p>Participants will perform the exercises on their own laptops. Important note: only MacOS and Linux systems are supported; MS Windows systems are NOT supported.</p>

<p>Please note that this workshop is focused on human data analysis. The majority of the materials presented does apply equally to non-human data, and we will address some questions regarding adaptations that are needed for analysis of non-human data, but we will not go into much detail on those points.</p>

<hr></hr><h3>Target audience</h3>

<p>The lecture-based component of the workshop is aimed at a mixed audience of people who are new to the topic of variant discovery or to GATK, seeking an introductory course into the tools, or who are already GATK users seeking to improve their understanding of and proficiency with the tools. Participants should already be familiar with the basic terms and concepts of genetics and genomics. Workshop hosts are encouraged to open the first day of lectures to as many participants as possible.</p>

<p>The hands-on component is aimed at novice to intermediate users who are seeking detailed guidance with GATK and related tools. Basic familiarity with the command line environment is required. Participants will be expected to bring their own laptops with software preinstalled (detailed instructions will be posted two weeks before the course) unless the workshop host provides a computer lab or cloud-based platform. Supported systems are Mac and Unix/Linux systems; MS Windows is NOT supported. Please note that attendance to the hands-on sessions is limited to 30 participants each.</p>

<hr></hr><h3>Detailed agenda of the Best Practices lectures</h3>

<h4><em>Introductory talks</em></h4>

<ul><li>Introduction to variant discovery analysis</li>
<li>Introduction to GATK Best Practices</li>
<li>Introduction to pipelining with WDL/Cromwell/FireCloud</li>
</ul><h4><em>Data processing algorithms</em></h4>

<ul><li>Mapping</li>
<li>Marking Duplicates</li>
<li>Base Recalibration</li>
</ul><h4><em>Lunch Break</em></h4>

<h4><em>Germline variant discovery</em></h4>

<ul><li>Variant Calling and Joint Genotyping</li>
<li>Filtering variants with VQSR</li>
<li>Genotype Refinement Workflow</li>
</ul><h4><em>Somatic variant discovery</em></h4>

<ul><li>Somatic short variant discovery with Mutect2</li>
<li>Somatic CNV discovery</li>
</ul>
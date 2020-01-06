## Bug in VariantsToBinaryPED

By Geraldine_VdAuwera

<p>As reported <a rel="nofollow" href="http://gatkforums.broadinstitute.org/discussion/3333/probable-serious-bug-in-variantstobinaryped-causing-incorrect-mapping-of-sample-to-genotype">here</a>, a bug was found in VariantsToBinaryPED. Briefly, VariantsToBinaryPed expected the fam file to describe the samples in the same order as the input VCF file: if they were not in the same order, it did not correctly map sample IDs with the genotypes in the output binary PED.</p>

<p>We expect that in most use cases, the order would be the same (because PLINK uses lexicographic order, as does the GATK), so the bug would not impact results. However, as the user report demonstrates, in cases where order was different, the bug would seriously impact results.</p>

<p>We therefore recommend that anyone who has used VariantsToBinaryPED check their results for any inconsistencies in the kinship coefficients. Our apologies for the inconvenience to anyone who is affected by this bug, and big thanks again to user TimHughes for reporting the bug.</p>

<p>Finally, we have fixed the bug in GATK and released the fixed version under version number 2.7-4.</p>

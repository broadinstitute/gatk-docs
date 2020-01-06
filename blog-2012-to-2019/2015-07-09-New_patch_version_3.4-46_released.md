## New patch version 3.4-46 released

By Geraldine_VdAuwera

<p>This patch release fixes several issues that we felt were annoying enough to warrant an interim version release; see detailed list below.</p>

<p>Oh, and in case you're curious about the patch number, the *-46 numeral refers to the number of code commits made since the 3.4-0 release. Out of this, about a dozen are related to the bug fixes listed above; another dozen relate to new work that is not yet publicly available (gotta wait 'til 3.5...) and the rest are automated commits produced when merging completed work into the master codebase.</p>

<hr></hr><h3>Modifications related to the new "spanning deletion" allele</h3>

<p>In version 3.4, we introduced some functionality to handle cases where a site that is variant in one sample is covered by a deletion in another sample (see release notes for details). Some of that functionality involved using a new symbolic <code class="code codeInline" spellcheck="false">&lt;*:DEL&gt;</code> notation to represent this in multi-sample VCFs. Unfortunately we later realized that this was not the best way to represent this, so we have now switched to a more spec-compliant notation, which is simply <code class="code codeInline" spellcheck="false">*</code>. We've also added some refinements to correctly handle cases where alleles need to be remapped, and/or where there are multiple overlapping spanning deletions in the population (which we encountered twice in a population of ~10K samples, to give you an idea of how rarely that happens in humans). The new code is fully backward-compatible, so if you have any files that contain the <code class="code codeInline" spellcheck="false">&lt;*:DEL&gt;</code> notation, these will be understood correctly by GATK. Note however that they will not be converted to the new notation -- they will just be passed through unchanged.</p>

<hr></hr><h3>VectorLoglessPairHMM accelerated library</h3>

<p>Some annoying gcc compiler version issues caused many of you to be unable to utilize Intel's accelerated PairHMM library to speed up HaplotypeCaller. We've sorted those out so now everyone should be able to utilize it provided their computing platform is running reasonably recent (i.e. not Jurassic-era) software.</p>

<hr></hr><h3>MNP merging in ReadBackedPhasing</h3>

<p>There was a minor bug in the ReadBackedPhasing tool that was causing some variants to be merged into MNPs when they shouldn't be; that's now fixed.</p>

<hr></hr><h3>Speed issues in DepthOfCoverage</h3>

<p>A user reported a dramatic slowdown in the DepthOfCoverage and shortly thereafter, provided a code patch that resolved to problem. Self-fixing bug reports are our favorite kind, thank you <a href="https://gatkforums.broadinstitute.org/gatk/profile/mnw21cam" rel="nofollow">@mnw21cam</a>!</p>

<hr></hr><h3>Enhanced variant selection/filtering</h3>

<p>OK, technically not a bug fix, so this shouldn't be in a patch, according to my esteemed software engineer colleagues, but I couldn't wait to share it with y'all. This is something people often ask how to do and until now it was beyond painful. But hark! You can now tell <a rel="nofollow" href="https://www.broadinstitute.org/gatk/guide/tooldocs/org_broadinstitute_gatk_tools_walkers_variantutils_SelectVariants.php">SelectVariants</a> to filter out variants where a given number or proportion of samples have filtered genotypes. See the usage example in the tool doc (toward the end of the list). Now tell me you're not happy that we included it in the patch. C'mon, I dare you.</p>

<hr></hr><h3>New read filter for overly clipped reads</h3>

<p>Again with a not-a-bug-fix thing, sorry -- there's a <a rel="nofollow" href="https://www.broadinstitute.org/gatk/guide/tooldocs/org_broadinstitute_gatk_engine_filters_OverclippedReadFilter.php">new read filter</a> to get rid of reads that have too much soft-clipping going on to be honest. This is a sign that there's something seriously wrong with them, as we recently found out while analyzing a bunch of cheek swab samples that turned out to be heavily contaminated with bacterial DNA. It's always fun to see some seriously messed-up samples go by... and it's nice that now we can do something to rescue them.</p>

<hr></hr><h3>Various small fry</h3>

<p>We made some minor changes to documentation (VQSLOD, QD) and error handling (clarified error message for Contigs Out of Order error, made GVCF indexing parameters not required for gzipped output).</p>

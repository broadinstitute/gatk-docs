## Version highlights for GATK version 3.8

By Geraldine_VdAuwera

<p>One more 3.x version, for the road! That's right, even as we're ramping up our efforts on GATK4 (we're three beta releases in at this point, and getting down to brass tacks writing the migration guide ahead of the 4.0 general release) we still found it worthwhile to cut one last release of GATK3.</p>

<p>Our main motivation here is to introduce the Intel Genomics Kernel Library, which comes bearing the gift of speed improvements for those of you who won't be able to migrate to GATK4 right away.</p>

<p>As a secondary benefit, this version includes a handful of bug fixes, some usability improvements including better error messages, documentation fixes and logging tweaks, and a few improvements to annotation calculations (especially in allele-specific mode), which you'll find described briefly in the <a rel="nofollow" href="https://software.broadinstitute.org/gatk/blog?id=10062">release notes</a>. No big changes though, except perhaps the new default behavior of VariantsToTable with regard to missing annotation values, discussed below. Finally, we've committed a copy of all the peripheral documentation (= the docs that live in the forum and complement the tool documentation) to the now-old GATK codebase.</p>

<p>And thus, the last-ever GATK3 version emerges covered in carbonite.</p>

<hr></hr><h3>Introducing the Intel Genomics Kernel Library</h3>

<p>The <a rel="nofollow" href="https://github.com/Intel-HLS/GKL">Genomics Kernel Library</a> or GKL is an open-source library developed by our collaborators at Intel that provides accelerated versions of algorithms, <em>i.e.</em> "kernels", used in genomics tools. These kernels are optimized to run on Intel Architecture under 64-bit Linux and Mac OSX. They're plugged into the GATK in such a way that they will be automatically used if your computing hardware supports them, but if it doesn't they will remain inactive and the "default" generic Java versions will be used instead.</p>

<p>At the moment there are three main kernels included:</p>

<ul><li><p>Intel inflater/deflater: a file compression/decompression kernel that provides different levels of compression (with correspondingly variable speedups). This replaces the JDK inflater/deflater and is now activated by default. It can be disabled by using the <code class="code codeInline" spellcheck="false">-jdk_deflater</code> and <code class="code codeInline" spellcheck="false">-jdk_inflater</code> flags.</p></li>
<li><p>Intel chip optimization for PairHMM: a version of the PairHMM algorithm used by HaplotypeCaller to calculate genotype likelihoods that runs faster on Intel hardware. It can be disabled by setting <code class="code codeInline" spellcheck="false">-pairHMM LOGLESS_CACHING</code>, for example if you need completely deterministic behavior across different machine types (at the expense, of course, of speed).</p></li>
<li><p>FPGA support for PairHMMM: another version of the PairHMM algorithm, this one designed to run on FPGAs, which are a type of processor that is gaining popularity for computing applications that require extremely high speed. The FPGA support in this version is fairly experimental so we can't guarantee results, but if you have access to this specialized hardware we definitely encourage you to try it out and let us know how it goes.</p></li>
</ul><hr></hr><h3>Attitude adjustment for VariantsToTable</h3>

<p><a rel="nofollow" href="https://software.broadinstitute.org/gatk/documentation/tooldocs/current/org_broadinstitute_gatk_tools_walkers_variantutils_VariantsToTable.php">VariantsToTable</a> is a tool we're quite fond of because it allows us to extract just the information we want from VCFs when we want to probe a callset interactively, typically for <a rel="nofollow" href="https://software.broadinstitute.org/gatk/documentation/article?id=6925">filtering purposes</a>. Previously we had to tell it explicitly not to freak out if it came across any sites or genotypes where an annotation we requested was missing; but realistically, there are always some sites for which we can't calculate some annotations (like ranksum annotations at sites where we don't have any heterozygous samples), so that was annoying. Now we've flipped the behavior so that by default the tool keeps going and just outputs "NA" anywhere it encounters such sites or genotypes, unless you specify that it <em>should</em> freak out by using the <code class="code codeInline" spellcheck="false">--errorIfMissingData</code> flag.</p>

<hr></hr><h3>Documentation archive and deprecation plans</h3>

<p>In preparation for the general release of GATK4 (in the form of a 4.0 version), we made a copy of all the peripheral (forum-based) documentation in its current state and archived it in the codebase itself <a rel="nofollow" href="https://github.com/broadgsa/gatk/tree/master/doc_archive">here</a>. This is intended to be a permanent archive for documentation that we are phasing out in favor of GATK4-focused documentation.</p>

<p>Our ultimate goal is to provide some degree of continuity and support for users who cannot migrate to GATK4 right away and must continue to use older versions, without leaving too much clutter around that might confuse everyone else.</p>

<p>In the immediate future we will delete three sets of documents from the forum (and therefore from the website):</p>

<ul><li>"Developer Zone": replaced in GATK4 by a <a rel="nofollow" href="https://github.com/broadinstitute/gatk/wiki">developer-oriented Wiki</a> in the github repository;</li>
<li>"Queue": superseded for all versions by <a rel="nofollow" href="https://software.broadinstitute.org/wdl/">Cromwell+WDL</a>;</li>
<li>The current contents of "Archive", which have typically been replaced by individual articles linked at the top of the deprecated article.</li>
</ul><p>Within the other documentation sections, articles may get updated in place or moved to the Archive for future removal. Versioned tool documentation going back to 3.5-0 will remain available on the website for the foreseeable future. For older versions, the documentation can be built from source. Finally, the <a rel="nofollow" href="https://software.broadinstitute.org/gatk/best-practices">Best Practices</a> section of the website will be updated to reflect the new world order once GATK 4.0 is released and becomes the officially supported version of GATK. Going forward we'll have versioned Best Practices accompanied by a publicly available WDL script for each major use case. We'll post more details of what this will look like in the coming weeks.</p>

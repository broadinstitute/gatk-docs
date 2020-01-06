## Bug Bulletin: AD swapping by SelectVariants with -nt in 2.x versions

By Geraldine_VdAuwera

<p>This is not exactly new (it was fixed in GATK 3.0) but it's come to our attention that many people are unaware of this bug, so we want to spread the word since it might have some important impacts on people's results.</p>

<p><strong>Affected versions:</strong> 2.x versions up to 2.8 (not sure when it started)</p>

<p><strong>Affected tool:</strong> SelectVariants</p>

<p><strong>Trigger conditions:</strong> Extracting a subset of samples with SelectVariants while using multi-threading (<code class="code codeInline" spellcheck="false">-nt</code>)</p>

<p><strong>Effects:</strong> Genotype-level fields (such as AD) swapped among samples</p>

<p>This bug no longer affects any tools in versions 3.0 and above, but callsets generated with earlier versions may need to be checked for consistency of genotype-level annotations. Our sincere apologies if you have been affected by this bug, and our thanks to the users who reported experiencing this issue.</p>

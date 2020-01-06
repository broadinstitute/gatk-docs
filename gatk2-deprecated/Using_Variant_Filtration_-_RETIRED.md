## Using Variant Filtration - RETIRED

By delangel

<h3>This tool is not deprecated, but this article is now superseded by the method article on JEXL expressions.</h3>

<hr></hr><h2>VariantFiltration</h2>

<p>For a complete, detailed argument reference, refer to the GATK document page <a rel="nofollow" href="http://www.broadinstitute.org/gatk/gatkdocs/org_broadinstitute_sting_gatk_walkers_filters_VariantFiltration.html">here</a>.</p>

<p>The documentation for <a rel="nofollow" href="http://www.broadinstitute.org/gatk/guide/article?id=1255">Using JEXL expressions</a> within the GATK contains very important information about limitations of the filtering that can be done; in particular please note the section on working with complex expressions.</p>

<h2>Filtering Individual Genotypes</h2>

<p>One can now filter individual samples/genotypes in a VCF based on information from the <code class="code codeInline" spellcheck="false">FORMAT</code> field: Variant Filtration will add the sample-level <code class="code codeInline" spellcheck="false">FT</code> tag to the <code class="code codeInline" spellcheck="false">FORMAT</code> field of filtered samples (this does not affect the record's <code class="code codeInline" spellcheck="false">FILTER</code> tag).  This is still a work in progress and isn't quite as flexible and powerful yet as we'd like it to be.  For now, one can filter based on most fields as normal (e.g. <code class="code codeInline" spellcheck="false">GQ &lt; 5.0</code>), but the <code class="code codeInline" spellcheck="false">GT</code> (genotype) field is an exception.  We have put in convenience methods so that one can now filter out hets (<code class="code codeInline" spellcheck="false">isHet == 1</code>), refs (<code class="code codeInline" spellcheck="false">isHomRef == 1</code>), or homs (<code class="code codeInline" spellcheck="false">isHomVar == 1</code>).</p>

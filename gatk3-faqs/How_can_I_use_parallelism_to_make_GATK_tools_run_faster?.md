## How can I use parallelism to make GATK tools run faster?

By Geraldine_VdAuwera

<p><em>This document provides technical details and recommendations on how the parallelism options offered by the GATK can be used to yield optimal performance results.</em></p>

<h3>Overview</h3>

<p>As explained in the <a rel="nofollow" href="http://www.broadinstitute.org/gatk/guide/article?id=1988">primer on parallelism for the GATK</a>, there are two main kinds of parallelism that can be applied to the GATK: multi-threading and scatter-gather (using <a rel="nofollow" href="https://software.broadinstitute.org/gatk/documentation/pipelines">Queue or Crom/WDL</a>).</p>

<h3>Multi-threading options</h3>

<p>There are two options for multi-threading with the GATK, controlled by the arguments <code class="code codeInline" spellcheck="false">-nt</code> and <code class="code codeInline" spellcheck="false">-nct</code>, respectively, which can be combined:</p>

<ul><li><p><code class="code codeInline" spellcheck="false">-nt / --num_threads</code> <br>
controls the number of <strong>data threads</strong> sent to the processor</p></li>
<li><p><code class="code codeInline" spellcheck="false">-nct / --num_cpu_threads_per_data_thread</code><br>
controls the number of <strong>CPU threads</strong> allocated to each data thread</p></li>
</ul><p>For more information on how these multi-threading options work, please read the <a rel="nofollow" href="http://www.broadinstitute.org/gatk/guide/article?id=1988">primer on parallelism for the GATK</a>.</p>

<h4>Memory considerations for multi-threading</h4>

<p>Each data thread needs to be given the full amount of memory you’d normally give a single run. So if you’re running a tool that normally requires 2 Gb of memory to run, if you use <code class="code codeInline" spellcheck="false">-nt 4</code>, the multithreaded run will  use 8 Gb of memory. In contrast, CPU threads will share the memory allocated to their “mother” data thread, so you don’t need to worry about allocating memory based on the number of CPU threads you use.</p>

<h4>Additional consideration when using <code class="code codeInline" spellcheck="false">-nct</code> with versions 2.2 and 2.3</h4>

<p>Because of the way the <code class="code codeInline" spellcheck="false">-nct</code> option was originally implemented, in versions 2.2 and 2.3, there is one CPU thread that is reserved by the system to “manage” the rest. So if you use <code class="code codeInline" spellcheck="false">-nct</code>, you’ll only really start seeing a speedup with <code class="code codeInline" spellcheck="false">-nct 3</code> (which yields two effective "working" threads) and above. This limitation has been resolved in the implementation that will be available in versions 2.4 and up.</p>

<h3>Scatter-gather</h3>

<p>For more details on scatter-gather, see the <a rel="nofollow" href="http://gatkforums.broadinstitute.org/discussion/1988/a-primer-on-parallelism-with-the-gatk">primer on parallelism for the GATK</a> and the documentation on <a rel="nofollow" href="https://software.broadinstitute.org/gatk/documentation/pipelines">pipelining options</a>.</p>

<h3>Applicability of parallelism to the major GATK tools</h3>

<p>Please note that not all tools support all parallelization modes. The parallelization modes that are available for each tool depend partly on the type of traversal that the tool uses to walk through the data, and partly on the nature of the analyses it performs.</p>

<table><thead><tr><th align="left">Tool</th>
  <th align="left">Full name</th>
  <th align="left">Type of traversal</th>
  <th align="center">NT</th>
  <th align="center">NCT</th>
  <th align="center">SG</th>
</tr></thead><tbody><tr><td align="left">RTC</td>
  <td align="left">RealignerTargetCreator</td>
  <td align="left">RodWalker</td>
  <td align="center">+</td>
  <td align="center">-</td>
  <td align="center">-</td>
</tr><tr><td align="left">IR</td>
  <td align="left">IndelRealigner</td>
  <td align="left">ReadWalker</td>
  <td align="center">-</td>
  <td align="center">-</td>
  <td align="center">+</td>
</tr><tr><td align="left">BR</td>
  <td align="left">BaseRecalibrator</td>
  <td align="left">LocusWalker</td>
  <td align="center">-</td>
  <td align="center">+</td>
  <td align="center">+</td>
</tr><tr><td align="left">PR</td>
  <td align="left">PrintReads</td>
  <td align="left">ReadWalker</td>
  <td align="center">-</td>
  <td align="center">+</td>
  <td align="center">-</td>
</tr><tr><td align="left">RR</td>
  <td align="left">ReduceReads</td>
  <td align="left">ReadWalker</td>
  <td align="center">-</td>
  <td align="center">-</td>
  <td align="center">+</td>
</tr><tr><td align="left">HC</td>
  <td align="left">HaplotypeCaller</td>
  <td align="left">ActiveRegionWalker</td>
  <td align="center">-</td>
  <td align="center">(+)</td>
  <td align="center">+</td>
</tr><tr><td align="left">UG</td>
  <td align="left">UnifiedGenotyper</td>
  <td align="left">LocusWalker</td>
  <td align="center">+</td>
  <td align="center">+</td>
  <td align="center">+</td>
</tr></tbody></table><p>Note that while HaplotypeCaller supports <code class="code codeInline" spellcheck="false">-nct</code> in principle, many have reported that it is not very stable (random crashes may occur -- but if there is no crash, results will be correct). We prefer not to use this option with HC; use it at your own risk.</p>

<h3>Recommended configurations</h3>

<p>The table below summarizes configurations that we typically use for our own projects (one per tool, except we give three alternate possibilities for the UnifiedGenotyper). The different values allocated for each tool reflect not only the technical capabilities of these tools (which options are supported), but also our empirical observations of what provides the best tradeoffs between performance gains and commitment of resources. Please note however that this is meant only as a guide, and that we cannot give you any guarantee that these configurations are the best for your own setup. You will probably have to experiment with the settings to find the configuration that is right for you.</p>

<table><thead><tr><th align="left">Tool</th>
  <th align="center">RTC</th>
  <th align="center">IR</th>
  <th align="center">BR</th>
  <th align="center">PR</th>
  <th align="center">RR</th>
  <th align="center">HC</th>
  <th align="center">UG</th>
</tr></thead><tbody><tr><td align="left">Available modes</td>
  <td align="center">NT</td>
  <td align="center">SG</td>
  <td align="center">NCT,SG</td>
  <td align="center">NCT</td>
  <td align="center">SG</td>
  <td align="center">NCT,SG</td>
  <td align="center">NT,NCT,SG</td>
</tr><tr><td align="left">Cluster nodes</td>
  <td align="center">1</td>
  <td align="center">4</td>
  <td align="center">4</td>
  <td align="center">1</td>
  <td align="center">4</td>
  <td align="center">4</td>
  <td align="center">4 / 4 / 4</td>
</tr><tr><td align="left">CPU threads (<code class="code codeInline" spellcheck="false">-nct</code>)</td>
  <td align="center">1</td>
  <td align="center">1</td>
  <td align="center">8</td>
  <td align="center">4-8</td>
  <td align="center">1</td>
  <td align="center">4</td>
  <td align="center">3 / 6 / 24</td>
</tr><tr><td align="left">Data threads (<code class="code codeInline" spellcheck="false">-nt</code>)</td>
  <td align="center">24</td>
  <td align="center">1</td>
  <td align="center">1</td>
  <td align="center">1</td>
  <td align="center">1</td>
  <td align="center">1</td>
  <td align="center">8 / 4 / 1</td>
</tr><tr><td align="left">Memory (Gb)</td>
  <td align="center">48</td>
  <td align="center">4</td>
  <td align="center">4</td>
  <td align="center">4</td>
  <td align="center">4</td>
  <td align="center">16</td>
  <td align="center">32 / 16 / 4</td>
</tr></tbody></table><p>Where NT is data multithreading, NCT is CPU multithreading and SG is scatter-gather using Queue or other data parallelization framework. For more details on scatter-gather, see the <a rel="nofollow" href="http://www.broadinstitute.org/gatk/guide/article?id=1988">primer on parallelism for the GATK</a> and the documentation on <a rel="nofollow" href="https://software.broadinstitute.org/gatk/documentation/pipelines">pipelining options</a>.</p>

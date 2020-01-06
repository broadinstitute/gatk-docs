## Where can I get the GATK source code?

By Geraldine_VdAuwera

<p>We distinguish "Classic GATK" (major versions 1 through 3) and GATK 4, the next generation of GATK tools.</p>

<hr></hr><h2>"Classic GATK" (major versions 1 through 3) (current distribution)</h2>

<p>We provide the current GATK source code through two publicly accessible Github repositories: <a rel="nofollow" href="https://github.com/broadgsa/gatk">broadgsa/gatk</a> and <a rel="nofollow" href="https://github.com/broadgsa/gatk-protected">broadgsa/gatk-protected</a>.</p>

<h3>1. <a rel="nofollow" href="https://github.com/broadgsa/gatk">broadgsa/gatk</a></h3>

<p>This repository contains the code corresponding to the core GATK development framework, including the GATK engine and many utilities, which third-party developers can use to develop their own GATK-based analysis tools. Be advised however that support for development using this framework is being discontinued.</p>

<p>All the code in this repository is open-source under the <a rel="nofollow" href="https://tldrlegal.com/license/mit-license">MIT license</a>. The full text of the license can be viewed <a rel="nofollow" href="https://github.com/broadinstitute/gsa-unstable/blob/master/licensing/public_license.txt">here</a>.</p>

<h3>2. <a rel="nofollow" href="https://github.com/broadgsa/gatk-protected">broadgsa/gatk-protected</a></h3>

<p>This repository contains the code corresponding to the <code class="code codeInline" spellcheck="false">GenomeAnalysisTK.jar</code> file that we distribute to our users, containing the GATK engine and all analysis tools.</p>

<p>This includes the code in <a rel="nofollow" href="https://github.com/broadgsa/gatk">broadgsa/gatk</a> under the MIT license, plus tools and utilities that are under a more restrictive <a rel="nofollow" href="http://www.broadinstitute.org/gatk/gatklicense.htm">license</a> that prohibits commercial/for-profit use. Anyone interested in accessing the protected code for commercial/for-profit purposes should contact our licensing department (softwarelicensing@broadinstitute.org) to inquire about licensing terms.</p>

<hr></hr><h2>GATK 4+</h2>

<p>The code for GATK 4+, currently available as an alpha preview, is accessible through two publicly accessible Github repositories: <a rel="nofollow" href="https://github.com/broadinstitute/gatk">broadinstitute/gatk</a> and <a rel="nofollow" href="https://github.com/broadinstitute/gatk-protected">broadinstitute/gatk-protected</a>. The division is also based on having two different licenses, like Classic GATK, but in this case the repositories are complementary; there is no code shared between them.</p>

<h3>1. <a rel="nofollow" href="https://github.com/broadinstitute/gatk">broadinstitute/gatk</a></h3>

<p>This repository contains the code corresponding to the core GATK 4+ development framework, including the new GATK engine and many utilities, which third-party developers can use to develop their own GATK-based analysis tools. We encourage developers to use this new framework for development and we welcome feedback regarding features and development support.</p>

<p>All the code in this repository is open-source under a <a rel="nofollow" href="https://tldrlegal.com/license/bsd-3-clause-license-(revised)">BSD license</a>. The full text of the license can be viewed <a rel="nofollow" href="https://github.com/broadinstitute/gatk/blob/master/LICENSE.TXT">here</a>.</p>

<h3>2. <a rel="nofollow" href="https://github.com/broadinstitute/gatk-protected">broadinstitute/gatk-protected</a></h3>

<p>This repository contains the code for key analysis tools that are covered under a more restrictive <a rel="nofollow" href="https://github.com/broadinstitute/gatk-protected/blob/master/LICENSE.txt">license</a> that prohibits commercial/for-profit use. Anyone interested in accessing the protected code for commercial/for-profit purposes should contact our licensing department (softwarelicensing@broadinstitute.org) to inquire about licensing terms.</p>

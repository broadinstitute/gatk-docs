## Upcoming changes to the license &amp; the retirement of GATK Lite by v 2.4

By Geraldine_VdAuwera

<p><strong>Note: the license has been updated for GATK 3.x. See details <a rel="nofollow" href="http://www.broadinstitute.org/gatk/blog?id=4565">here</a>.</strong></p>

<hr></hr><p><strong>If you never got the point of GATK Lite and you hated the 2.0 license... Oh, do we have good news for you!</strong></p>

<p>First, a little bit of context. When we released GATK 2.0, the GATK had emerged as the leading research software package in its domain. Public demand for tech support was rising rapidly; not only from the academic research community as it had in the past, but also from researchers using the software in a for-profit context. These latter users have specific needs (quality assurance, process certifications, etc.) that we are ill-equipped to address.</p>

<p>This drove us to seek a partnership with a company called <a rel="nofollow" href="http://www.appistry.com/">Appistry</a> which could release and license the GATK as a commercial software product appropriate for use in a for-profit and regulatory-compliant setting. We knew this solution would better meet customer needs, while alleviating our support burden and allowing us to focus on our core constituency, the academic and non-profit research community. This plan also had the prospective benefit of leveraging the intellectual property of the GATK (much of which results more or less directly from public investments) to fund the continuation of our research and development activities.</p>

<p>However we knew it would take us and our partners at <a rel="nofollow" href="http://www.appistry.com/">Appistry</a> some time to develop a mature commercial product. So as an interim solution, we enacted a more restrictive license, closed part of the source code on the “Full GATK” release, and provided a “Lite” version to enable for-profit users to keep working with an up-to-date version of the GATK (albeit without the cutting-edge tools that were introduced in version 2.0). Of course, the GATK programming framework (the GATK engine, libraries, and basic data management tools) continued to remain open source under the MIT license.</p>

<p>Well, we got a lot of feedback from the user community over these changes. We listened carefully, took the criticism to heart, and realized our interim solution left much to be desired. First, closing part of the source code was a deeply unpopular move. Many of you pointed out that this might restrict academic knowledge and obstruct progress in the field of algorithmic research. Second, we did a poor job of communicating the purpose of Lite and how it differed from the Full version. Even though Lite was always intended as an interim solution, some organizations opted to adopt it instead of the Full version and seem to view it as a viable long-term solution for genetic analysis. Related to this, we found that maintaining the two different distributions gave us our share of headaches in terms of supporting and updating the toolkit.</p>

<p>In light of these considerations, we’re going to change things up again, hopefully for the better!</p>

<p><strong>In a nutshell: no more Lite and a new <a rel="nofollow" href="https://us.v-cdn.net/5019796/uploads/FileUpload/bf/ff9cd826f3f32795d404b34f841204.doc">license</a>  (attached) that restores free access to the source code for those in the community performing academic non-commercial research.</strong> That’s right, free as in beer! You’ll still have the option of downloading the packaged binary (i.e., the “ready-to-run” program) from our website as you did before, but you’ll also be able to get the full source code (programming framework AND all tools including the latest and greatest) straight from the Github repository if you want. You can set it up on a server and provide it as a service to other non-profit users within your organization. You can dig into our deepest secrets to find out what makes ReduceReads and the HaplotypeCaller tick. And feel free to send us patches if you find a way to improve the code!</p>

<p>Licensed users through Appistry, in addition to having access to the full GATK and the added benefits of a fully-fledged commercial solution (less buggy, more help-y), may optionally purchase access to the source code. <a rel="nofollow" href="http://www.appistry.com/">Appistry</a> has been fine-tuning its process for providing the commercial product (including enterprise-grade QA, which we don’t do) as well as training a professional support team. If your use of GATK requires a commercial license, we encourage you to <a rel="nofollow" href="http://www.appistry.com/gatk/contact">reach out to them</a>. Appistry will be able to handle any questions you may have about the commercial release schedule, available support, and of course, licensing and pricing terms (whether for individual or site-wide licenses, companies big and small).</p>

<p>The following figure summarizes the different packages and their corresponding licenses.</p>

<p><img src="https://us.v-cdn.net/5019796/uploads/FileUpload/13/a0b227f1aca1af84aefbe716dbef06.png" alt="image" class="embedImage-img importedEmbed-img"></img></p>

<p>Note that if you are using a version of GATK-Lite, you may continue using it, but we will be making no more updates to Lite after 2.3. Thus, if you choose to stay with Lite, you will be using an outdated version of the toolkit and you won’t benefit from any further improvements made to the GATK with the 2.4 release and in the future.</p>

<p>We welcome any and all comments on these new changes, which are due to take effect with the upcoming release of version 2.4 (tentatively scheduled for early February). There’s still time to tweak the language of the <a rel="nofollow" href="https://us.v-cdn.net/5019796/uploads/FileUpload/bf/ff9cd826f3f32795d404b34f841204.doc">license</a> if you spot any issues we’ve overlooked.</p>

<p>If you are using the GATK in an academic or non-profit research setting and have any questions or concerns about the details of the new license (attached), please join the discussion in the comments below. If you are using the GATK in a for-profit context, please contact our partners at <a rel="nofollow" href="http://www.appistry.com/gatk/contact">Appistry</a> as they will be in a better position to address your questions. If you’re not sure in which category you belong, please contact either <a rel="nofollow" href="http://www.appistry.com/gatk/contact">Appistry</a> or <a rel="nofollow" href="denied:mailto:irozen@broadinstitute.org">Issi Rozen</a> at the Broad Institute.</p>

<p>--</p>

<p>Edits:</p>

<ol><li><p>Removed "free as in speech" since the distribution of modified source is restricted to within academic institutions.</p></li>
<li><p>Add a link in-text so people can see the <a rel="nofollow" href="https://us.v-cdn.net/5019796/uploads/FileUpload/bf/ff9cd826f3f32795d404b34f841204.doc">license</a>  text without registering.</p></li>
<li><p>Added a <a rel="nofollow" href="https://us.v-cdn.net/5019796/uploads/FileUpload/cd/87cd0e9628539680f06a6ffc41511d.txt">TXT format version</a>  for those who don't want to use DOC.</p></li>
<li><p>Corrected the license file version.</p></li>
<li><p>Prepended note and link to the license updates for GATK3 (9/02/14)</p></li>
</ol>
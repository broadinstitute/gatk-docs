## How can I submit a patch to the GATK codebase?

By Carneiro

<h3>This article has been retired. If you would like to contribute a patch to GATK 3.*, please let us know in the forum. You may also consider contributing to the <a rel="nofollow" href="https://github.com/broadinstitute/gatk">GATK4 codebase</a> instead, which is where we are putting most of our development effort from here onward.</h3>

<p>The GATK is an open source project that has greatly benefited from the contributions of outside users. The GATK team welcomes contributions from anyone who produces useful functionality in line with the goals of the toolkit. You are welcome to branch the GATK main repository and develop your own tools. Sometimes these tools may be useful to the GATK user community and you may want to make it part of the main GATK distribution. If so we ask you to follow our guidelines for submission of patches.</p>

<h3>1. Good practices</h3>

<p>There are a few good GIT practices that you should follow to simplify the ultimate goal, which is, adding your changes to the main GATK repository.</p>

<ul><li><dl><dt>Use branches.</dt>
<dd>Every time you start new work that you are going to submit to the GATK team later, do it in a new branch. Make it a habit as this will simplify many of the following procedures and allow your master branch to always be a fresh (up to date) copy of the GATK main repository. Take a look on [[#How to create a new submission|  how to create a new branch for submission]].</dd>
</dl></li>
<li><dl><dt>Never merge.</dt>
<dd>Merging creates a branched history with multiple parent nodes that make history hard to understand, impossible to modify and patches near-impossible to create. Merges are very useful when you need to combine multiple repositories and it should ''only'' be used when it makes sense. This means '''never merge''' and '''never pull''' (if it's not a fast-forward, or you will create a merge).</dd>
</dl></li>
<li><dl><dt>Commit as often as possible.</dt>
<dd>Every change, should be committed to make sure you can go back in time effectively in your own tree. The commit messages don't matter to us as long as they're meaningful to you in this stage. You can essentially do whatever you want in your local tree with your commits, as long as you don't merge.</dd>
</dl></li>
<li><dl><dt>Rebase constantly</dt>
<dd>Your branch is diverging from the master by the minute, so if you keep rebasing as often as you can, you will avoid major conflicts when it's time to send the patches. Take a look at our guide on [[#How to rebase | how to rebase]].</dd>
</dl></li>
<li><dl><dt>Tell a meaningful story</dt>
<dd>When it's time to submit your patches to us, reorder your commits and write meaningful commit messages. Each commit must be (as much as possible) self contained. These commits must tell a meaningful story to us so we can understand what it is you're adding to the codebase. Take a look at an [[#How to make your commits | example commit scenario]].</dd>
</dl></li>
<li><dl><dt>Generate patches and email them to the group</dt>
<dd>This part is super easy, provided you've followed the good practices. You just have to [[#How to generate the patches | generate the patches]] and e-mail them to gsa-patches@broadinstitute.org.</dd>
</dl></li>
</ul><h3>2. How to create a new submission</h3>

<p>You should <em>always</em> start your code by creating a new branch from the most recent version of the main repository with :</p>

<pre class="code codeBlock" spellcheck="false">git checkout master                       (make sure you are in the master branch)
git fetch &amp;&amp; git rebase origin/master     (you can substitute this line for "git pull" if you have no changes in the master branch) 
git checkout -b newtool                   (create a new branch for your new tool)
</pre>

<p>Note: If you have submitted a patch to the group, do not continue development on the same branch as we cannot guarantee that your changes will make it to the main repository unchanged.</p>

<h3>3. How to rebase</h3>

<p><em>Every time</em> before you rebase, you have to update your copy of the main repository. To do this use:</p>

<pre class="code codeBlock" spellcheck="false">git fetch
</pre>

<p>If you are just trying to keep up with the changes in the main repository after a fetch, you can rebase your branch at anytime using (and this should be all you need to do):</p>

<pre class="code codeBlock" spellcheck="false">git rebase origin/master
</pre>

<p>In case there are conflicts, resolve them as you would and do:</p>

<pre class="code codeBlock" spellcheck="false">git rebase --continue
</pre>

<p>If you don't know how to resolve the conflicts, you can always safely abort the whole process and go back to your branch before you started rebasing:</p>

<pre class="code codeBlock" spellcheck="false">git rebase --abort
</pre>

<p>If you are done and want to generate your patches conforming to the latest repository changes, to edit, squash and reorder your commits use :</p>

<pre class="code codeBlock" spellcheck="false">git rebase -i origin/master
</pre>

<p>At the prompt, you can follow the instructions to squash, edit and reorder accordingly. You can also do this step from IntelliJ with a visual editor that allows you to select what to edit/squash/reorder. You can also take a look at <a rel="nofollow" href="http://book.git-scm.com/4_interactive_rebasing.html">this nice tutorial on how to use interactive rebase</a>.</p>

<h3>4. How to make your commits</h3>

<p>It is okay to have a list of commits (numbered) somewhat like this in your local tree:</p>

<ul><li>added function X</li>
<li>fixed a b and c on X</li>
<li>b was actually d</li>
<li>started creating feature Y but had to go to the bathroom</li>
<li>added Y</li>
<li>found bug in X, fixed with e</li>
<li>added Z</li>
<li>fixed bug in Z with f</li>
</ul><p>Before you can send your tools to us, you have to organize these commits so they tell a meaningful history and are self contained. To achieve this you will need to rebase so you can squash, edit and reorder your commits. This tree makes a lot of sense for your development process, but it makes no sense in the main repository history as it becomes hard to pick/revert commits and understand the history at a glance. After rebasing, you should edit your commits to look like this:</p>

<ul><li>added X (including commits 2, 3 and 6)</li>
<li>added Y (including commits 4 and 5)</li>
<li>added Z (including commits 7 and 8)</li>
</ul><p>Use your commit messages wisely to help quick processing of your patches. Make sure the first line of your commit messages have less than 50 characters (title). Add a blank line and write  a paragraph or more explaining what this commit represents (now that it is a package of multiple commits. It is important to have the 50 char title because this is all we see when we look at an extended history to find bugs and it is also our quick access to remember what the commit does to the repository.</p>

<p>A patch should be self contained. Meaning if we decide to adopt feature X and Z but not Y, we should be able to do so by only applying patches 1 and 2. If your patches are co-dependent, you should say so in the commits and justify why you didn't squash the commits together into one tool.</p>

<h3>5. How to generate the patches</h3>

<p>To generate patches, use :</p>

<pre class="code codeBlock" spellcheck="false">git format-patch since  
</pre>

<p>The <em>since</em> parameter is the last commit you want to generate patches from, for example: HEAD^3 will generate patches for HEAD^2, HEAD^1 and HEAD. You can also specify the commit by its id or by using the head of a branch. This is where using branches will make your life easier. If master is always up to date with the main repo with no changes, you can do:</p>

<pre class="code codeBlock" spellcheck="false">git format-patch master   (provided your master is up to date) 
</pre>

<p>This will generate a patch for each commit you've created and you can simply e-mail them as an attachment to us.</p>

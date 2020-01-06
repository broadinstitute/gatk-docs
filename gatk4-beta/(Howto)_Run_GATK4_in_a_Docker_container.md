## (Howto) Run GATK4 in a Docker container

By Geraldine_VdAuwera

<h3>1. Install Docker</h3>

<p>Follow the relevant link below depending on your computer system; on Mac and Windows, select the "Stable channel" download. Run through the installation instructions and initial setup page; they are very straightforward and should only take you a few minutes (not counting download time). <br>
We have included instructions below for all steps after that first page, so you shouldn't need to go to any other pages in the Docker documentation. Frankly their docs are targeted at people who want to do things like run web applications on the cloud and can be quite frustrating to deal with.</p>

<h4><a rel="nofollow" href="https://docs.docker.com/docker-for-mac/install/">Click here for Mac</a></h4>

<h4><a rel="nofollow" href="https://docs.docker.com/docker-for-windows/install/">Click here for Windows</a></h4>

<h4><a rel="nofollow" href="https://docs.docker.com/engine/installation/#supported-platforms">Full list of supported systems and their install pages</a></h4>

<hr></hr><h3>2. Get the GATK4 container image</h3>

<p>Go to your Terminal (it doesn't matter where your working directory is) and run the following command.</p>

<pre class="code codeBlock" spellcheck="false">docker pull broadinstitute/gatk:4.beta.6
</pre>

<p>Note that the last bit after <code class="code codeInline" spellcheck="false">gatk:</code> is the version tag, which you can change to get a different version than what we've specified here.</p>

<p>The GATK4 image is quite large so the download may take a little while if you've never done this before. The good news is that next time you need to pull a GATK4 image (e.g. to get another release), Docker will only pull the components that have been updated, so it will go faster.</p>

<hr></hr><h3>3. Start up the GATK4 container</h3>

<p>There are several different ways to do this in Docker. Here we're going to use the simplest invocation that gets us the functionality we need, i.e. the ability to log into the container once it's running and execute commands from inside it.</p>

<pre class="code codeBlock" spellcheck="false">docker run -it broadinstitute/gatk:4.beta.6
</pre>

<p>If all goes well, this will start up the container in interactive mode, and you will automatically get logged into it. Your terminal prompt will change to something like this:</p>

<pre class="code codeBlock" spellcheck="false">root@ea3a5218f494:/gatk#
</pre>

<p>At this point you can use classic shell commands to explore the container and see what's in there, if you like.</p>

<hr></hr><h3>4. Run a GATK4 command in the container</h3>

<p>The container has the <code class="code codeInline" spellcheck="false">gatk-launch</code> script all set up and ready to go, so you can now run any GATK or Picard command you want. Note that if you want to run a Picard command, you need to use the new syntax, which follows GATK conventions (<code class="code codeInline" spellcheck="false">-I</code> instead of <code class="code codeInline" spellcheck="false">I=</code> and so on). Let's use <code class="code codeInline" spellcheck="false">--list</code> to list all tools available in this version.</p>

<pre class="code codeBlock" spellcheck="false">./gatk-launch --list
</pre>

<p>The output will start with a usage message (shown below) then a full list of tools and their summary descriptions.</p>

<pre class="code codeBlock" spellcheck="false">Using GATK wrapper script /gatk/build/install/gatk/bin/gatk
Running:
    /gatk/build/install/gatk/bin/gatk --help
USAGE:  &lt;program name&gt; [-h]
</pre>

<p>Once you've verified that this works for you, you know you can run any GATK4 commands you want. But before you proceed, there's one more setup thing to go through, which is technically optional but will make your life much easier.</p>

<hr></hr><h3>5. Use a mounted volume to access data that lives outside the container</h3>

<p>This is the final piece of the puzzle. By default, when you're inside the container you can't access any data that lives on the filesystem outside of the container. One way to deal with that is to copy things back and forth, but that's wasteful and tedious. So we're going to follow the better path, which is to <strong>mount a volume</strong> in the container, i.e. establish a link that makes part of the filesystem visible from inside the container.</p>

<p>The hitch is that you can't do this after you started running the container, so you'll have to shut it down and run a new one (not just restart the first one) with an extra part to the command. In case you're wondering why we didn't do this from the get-go, it's because the first command we ran is simpler so there's less chance that something will go wrong, which is nice when you're trying something for the first time.</p>

<p>To shut down your container from <em>inside</em> it, you can just type exit while still inside the container:</p>

<pre class="code codeBlock" spellcheck="false">exit
</pre>

<p>That should stop the container and take you back to your regular prompt. It's also possible to exit the container without stopping it (a move called <em>detaching</em>) but that's a matter for another time since here we do want to to stop it. You'll probably also want to learn how to clean up and delete old instances of containers that you no longer want.</p>

<p>For now, let's focus on starting a new instance of the GATK4 container, specifying in the following command what is your particular container ID and the filesystem location you want to mount.</p>

<pre class="code codeBlock" spellcheck="false">docker run -v ~/my_project:/gatk/my_data -it broadinstitute/gatk:4.beta.6
</pre>

<p>Here I set the external location to be an existing directory called <code class="code codeInline" spellcheck="false">my_project</code> in my home directory (the key requirement is that it has to be an absolute path) and I'm setting the <strong>mount point</strong> inside the container's <code class="code codeInline" spellcheck="false">/gatk</code> directory. The name of the mount point can be the same as the mount directory, or something completely different; the main constraint is that it should not conflict with an existing directory, otherwise that would make the existing directory unattainable.</p>

<p>Assuming your paths are valid, this command starts up the container and logs you into it the same way as before; but now you can see by using <code class="code codeInline" spellcheck="false">ls</code> that you have access to your filesystem. So now you can run GATK commands on any data you have lying around. Have fun!</p>

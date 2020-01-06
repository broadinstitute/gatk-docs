## (How to) Run the GATK4 Docker locally and take a look inside

By shlee

<h4>Document is in <code class="code codeInline" spellcheck="false">BETA</code>. It may be incomplete and/or inaccurate. Post suggestions to the <em>Comments</em> section and be sure to read about updates also within the <em>Comments</em> section.</h4>

<hr></hr><h3>1. Install Docker on your system</h3>

<p>Install Docker for your system from <a href="https://docs.docker.com/engine/installation/" rel="nofollow">https://docs.docker.com/engine/installation/</a>, e.g. for Mac, Windows or Linux servers. There is also a program called Docker Toolbox and I have this installed but I don't think it's necessary for running Docker containers locally or on a server.</p>

<p>On my Mac, I just double-click on the Docker whale icon to start the application. Check that Docker is running in the Mac menu bar at top by clicking on the icon that looks like a whale-container-ship.<br><a rel="nofollow" href="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10172_figures/dockerisrunning.png"><div style="text-align: center;"><img src="https://software.broadinstitute.org/gatk/resources/img_tutorials/tutorial_10172_figures/dockerisrunning.png" width="540" alt="image" style="text-align: ;" class="embedImage-img importedEmbed-img"></img></div></a></p>

<hr></hr><h3>2. Check your Docker software installation</h3>

<p>See the Docker version with <code class="code codeInline" spellcheck="false">docker --version</code>.</p>

<pre class="code codeBlock" spellcheck="false">$ docker --version
Docker version 17.06.0-ce, build 02c1d87
</pre>

<p>If you have trouble, you may need to run one or a number of the following commands.</p>

<pre class="code codeBlock" spellcheck="false">docker-machine restart default
</pre>

<pre class="code codeBlock" spellcheck="false">docker-machine regenerate-certs
</pre>

<pre class="code codeBlock" spellcheck="false">docker-machine env
</pre>

<hr></hr><h3>3. Download a Docker image from Dockerhub</h3>

<p>In Docker, an <em>image</em> is the original from which we launch <em>containers</em>. We pull images from Dockerhub (<a href="https://hub.docker.com/" rel="nofollow">https://hub.docker.com/</a>), using Git like lingo. For example, the following command downloads a GATK4 docker image.</p>

<pre class="code codeBlock" spellcheck="false">docker pull broadinstitute/gatk:4.beta.3
</pre>

<p>The part after the colon is the version of the container we pull.  You can see which images you have locally with <code class="code codeInline" spellcheck="false">docker image ls</code>. Here we see I have two different versions of <code class="code codeInline" spellcheck="false">broadinstitute/gatk</code>, v4.beta.3 and v4.beta.2.</p>

<pre class="code codeBlock" spellcheck="false">$ docker image ls
REPOSITORY                            TAG                    IMAGE ID            CREATED             SIZE
broadinstitute/gatk                   4.beta.3               5c138c493794        2 weeks ago         2.87GB
broadinstitute/gatk                   4.beta.2               507406cb4d85        3 weeks ago         2.88GB
</pre>

<hr></hr><h3>4. Inspect a Docker image by running a container</h3>

<p>There are two ways to inspect an image. One is with <code class="code codeInline" spellcheck="false">docker inspect 5c138c493794</code>. The other is to launch a container off the image and root around within it much like you would a file system.</p>

<ul><li>The <code class="code codeInline" spellcheck="false">broadinstitute/gatk</code> image is built automatically from a script documented at <a href="https://github.com/broadinstitute/gatk/blob/master/scripts/docker/" rel="nofollow">https://github.com/broadinstitute/gatk/blob/master/scripts/docker/</a>. For tools that the script installs, see <a href="https://github.com/broadinstitute/gatk/blob/master/scripts/docker/gatkbase/Dockerfile" rel="nofollow">https://github.com/broadinstitute/gatk/blob/master/scripts/docker/gatkbase/Dockerfile</a>.</li>
</ul><p>Launch a container with its tag or image ID. Whichever you use to launch a container, the tag or image ID, it becomes the image name.</p>

<pre class="code codeBlock" spellcheck="false">docker run -i -t 5c138c493794
</pre>

<p>or</p>

<pre class="code codeBlock" spellcheck="false">docker run -i -t broadinstitute/gatk:4.beta.3
</pre>

<p>We see then our bash opens into a location in the container preset by those who built the image.</p>

<pre class="code codeBlock" spellcheck="false">root@f944f81ff6d7:/gatk#
</pre>

<p>We can check the contents of the current directory and the java version.</p>

<pre class="code codeBlock" spellcheck="false">root@f944f81ff6d7:/gatk# ls -ltrh
total 148K
drwxr-xr-x  4 root root 4.0K Jul 26 15:49 docs
-rw-r--r--  1 root root  428 Jul 26 15:49 codecov.yml
-rwxr-xr-x  1 root root 4.5K Jul 26 15:49 build_docker.sh
-rw-r--r--  1 root root  21K Jul 26 15:49 build.gradle
-rw-r--r--  1 root root  33K Jul 26 15:49 README.md
-rw-r--r--  1 root root 1.5K Jul 26 15:49 LICENSE.TXT
-rw-r--r--  1 root root  690 Jul 26 15:49 Dockerfile
-rw-r--r--  1 root root  775 Jul 26 15:49 AUTHORS
drwxr-xr-x  1 root root 4.0K Jul 26 15:49 src
-rw-r--r--  1 root root   26 Jul 26 15:49 settings.gradle
drwxr-xr-x 10 root root 4.0K Jul 26 15:49 scripts
drwxr-xr-x  2 root root 4.0K Jul 26 15:49 resources_for_CI
-rwxr-xr-x  1 root root 5.2K Jul 26 15:49 gradlew
drwxr-xr-x  3 root root 4.0K Jul 26 15:49 gradle
-rwxr-xr-x  1 root root  19K Jul 26 15:49 gatk-launch
drwxr-xr-x  9 root root 4.0K Jul 26 15:53 build
-rw-r--r--  1 root root   40 Jul 26 15:55 run_unit_tests.sh
lrwxrwxrwx  1 root root   25 Jul 26 15:55 gatk.jar -&gt; /gatk/build/libs/gatk.jar
-rw-r--r--  1 root root 1017 Jul 26 15:55 install_R_packages.R
root@96d91017226e:/gatk# 
</pre>

<pre class="code codeBlock" spellcheck="false">root@f944f81ff6d7:/gatk# java -version
openjdk version "1.8.0_131"
OpenJDK Runtime Environment (build 1.8.0_131-8u131-b11-0ubuntu1.16.04.2-b11)
OpenJDK 64-Bit Server VM (build 25.131-b11, mixed mode)
root@f944f81ff6d7:/gatk# 
</pre>

<p>When we exit out of the container, by typing <code class="code codeInline" spellcheck="false">exit</code>, we exit out of it <em>and</em> also stop it from running.  We can check all the stopped container instances that docker saves automatically with <code class="code codeInline" spellcheck="false">docker ps -a</code>.</p>

<pre class="code codeBlock" spellcheck="false">$ docker ps -a
CONTAINER ID        IMAGE                          COMMAND             CREATED              STATUS                     PORTS               NAMES
28035a3b71f1        broadinstitute/gatk:4.beta.3   "bash"              About a minute ago   Exited (0) 8 seconds ago                       silly_davinci
f944f81ff6d7        5c138c493794                   "bash"              6 minutes ago        Exited (0) 4 minutes ago                       fervent_wing
62fb9991a939        5c138c493794                   "bash"              6 minutes ago        Exited (0) 6 minutes ago                       tender_mirzakhani
96d91017226e        5c138c493794                   "bash"              3 days ago           Exited (0) 2 days ago                          vigilant_montalcini
</pre>

<p>As you can see, I have multiple containers launched from the same image. Notice, however, each container has a unique ID (under <code class="code codeInline" spellcheck="false">CONTAINER ID</code>) and name (under <code class="code codeInline" spellcheck="false">NAMES</code>). Whatever changes I make within a container get saved to that container. We can remove containers with <code class="code codeInline" spellcheck="false">docker container rm</code> using either the container ID or name.</p>

<pre class="code codeBlock" spellcheck="false">$ docker container rm silly_davinci
silly_davinci
$ docker ps -a
CONTAINER ID        IMAGE                      COMMAND             CREATED             STATUS                      PORTS               NAMES
f944f81ff6d7        5c138c493794               "bash"              11 minutes ago      Exited (0) 9 minutes ago                        fervent_wing
62fb9991a939        5c138c493794               "bash"              11 minutes ago      Exited (0) 11 minutes ago                       tender_mirzakhani
96d91017226e        5c138c493794               "bash"              3 days ago          Exited (0) 2 days ago                           vigilant_montalcini
</pre>

<pre class="code codeBlock" spellcheck="false">$ docker container rm f944f81ff6d7
f944f81ff6d7
$ docker ps -a
CONTAINER ID        IMAGE                      COMMAND             CREATED             STATUS                      PORTS               NAMES
62fb9991a939        5c138c493794               "bash"              12 minutes ago      Exited (0) 12 minutes ago                       tender_mirzakhani
96d91017226e        5c138c493794               "bash"              3 days ago          Exited (0) 2 days ago                           vigilant_montalcini
</pre>

<p>We can run one of these containers with <code class="code codeInline" spellcheck="false">docker start</code>.</p>

<pre class="code codeBlock" spellcheck="false">docker start 96d91017226e
</pre>

<p>It may take a minute for a container to start up. We can see the running containers with <code class="code codeInline" spellcheck="false">docker container ls</code>.</p>

<pre class="code codeBlock" spellcheck="false">$ docker container ls
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
96d91017226e        5c138c493794        "bash"              3 days ago          Up About a minute                       vigilant_montalcini
</pre>

<p>Finally, we can reattach to the running container.</p>

<pre class="code codeBlock" spellcheck="false">docker attach vigilant_montalcini
</pre>

<p>On my local Mac, there is a glitch and I must press enter twice to show the docker container's bash prompt. You can also use the container ID instead of the name in the command. To exit out of a running container without stopping it, use <code class="code codeInline" spellcheck="false">Ctrl+P+Q</code>.</p>

<hr></hr><h3>5. Copy files from local system to the running container</h3>

<p>There are two ways to do this, from within the container and from outside the container. I only know how to copy files from outside the container. The container can be stopped or running.</p>

<pre class="code codeBlock" spellcheck="false">docker cp file_you_want_to_copy &lt;container_id&gt;:&lt;file_path_to_target_dirctory&gt;
</pre>

<p>For example,</p>

<pre class="code codeBlock" spellcheck="false">docker cp tumor.seg 96d91017226e:/gatk
</pre>

<p>Copies the file <code class="code codeInline" spellcheck="false">tumor.seg</code> into the container <code class="code codeInline" spellcheck="false">96d91017226e</code>'s <code class="code codeInline" spellcheck="false">/gatk</code> directory.</p>

<hr></hr><h3>6. Save a modified container as an image and upload to Dockerhub</h3>

<p>If you will modify a container to save, then remember that environmental variables, e.g. in bashrc, do not work in Docker containers. However, symlinks work well and you should create these in, e.g. <code class="code codeInline" spellcheck="false">/usr/bin</code> with the <code class="code codeInline" spellcheck="false">ln -s path/to/item short_cut_name</code>.</p>

<p>First, log into your Dockerhub account with <code class="code codeInline" spellcheck="false">docker login</code>. If you don't have one, create one at <a href="https://hub.docker.com" rel="nofollow">https://hub.docker.com</a>. My account is called spacecade7. For the container you have modified and wish to save a snapshot image of, use the following command.</p>

<pre class="code codeBlock" spellcheck="false">docker commit 96d91017226e spacecade7/mygatk:versioning_tag1
</pre>

<p>Where the string that follows <em>commit</em> is the container ID. The last part points to my Dockerhub account followed by what I would like to call the image and an image version tag. This saves the image locally.</p>

<p>To save the image to Dockerhub, use <code class="code codeInline" spellcheck="false">docker push spacecade7/mygatk:versioning_tag1</code>. The image should appear in your Dockerhub account.</p>

<hr></hr>
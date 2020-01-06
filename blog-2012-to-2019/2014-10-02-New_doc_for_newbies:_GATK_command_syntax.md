## New doc for newbies: GATK command syntax

By Geraldine_VdAuwera

<p>I'm not sure why it hadn't occurred to us to do this before, but we've finally done it: an FAQ article that formally explains how GATK commands are structured, what are the basic types of arguments, and how to string them all together.</p>

<p>We realized that command structure requirements can be confusing, if you are new to command line programs, if only because so many toolkits use fairly different ones. For example, Picard tools (which are also developed at the Broad!) have separate jar files for each tool in the toolkit, while GATK has one jar file containing all the tools. The Picard syntax for passing argument values is also different; they use <code class="code codeInline" spellcheck="false">=</code> to join the argument name and value, while GATK commands just take a space.</p>

<p>So if that's something you need help with, <a rel="nofollow" href="https://www.broadinstitute.org/gatk/guide/article?id=4669">check out the doc</a>! We'd love to hear from people who are new to GATK about whether this is helpful and how we can improve it further.</p>

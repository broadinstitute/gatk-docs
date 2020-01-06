## CNN deep learning pipeline out of beta!

By samwell

<h3>A production-ready suite of tools for single-sample variant filtration</h3>

<p>After training on a massive amount of data, the neural network models at the heart of CNNvariant have learned enough to be released from beta.</p>

<p>This suite of tools for single-sample variant filtration uses machine learning to differentiate between good variants and artifacts of the sequencing process, a fairly new approach that is especially effective at correctly calling indels. To enable the models to accurately filter and score variants from VCF files, we trained on validated VCFs (from truth models including <strong>SynDip</strong>, <strong>Genomes in a bottle</strong>, and <strong>Platinum Genomes</strong>) with unvalidated VCFs aligned to different reference builds (<strong>HG19</strong>, <strong>HG38</strong>), sequenced on different machines, using different protocols. The result is a single-sample tool that complements, rather than replaces, VQSR (our "traditional" cohort based variant filtering algorithm).</p>

<p><strong>What’s in the CNN toolbox? Pre-trained and train-your-own</strong><br>
CNNScoreVariant is a pre-trained Convolutional Neural Network ready to score variants. After scoring, you can filter your VCF by applying a sensitivity threshold with the tool FilterVariantTranches. Or you can train your own models with the tools CNNVariantWriteTensors and CNNVariantTrain, as long as you have validated VCFs to use as training data.</p>

<p>While the current release is only recommended for single sample variant filtration, in the future we hope to explore ways a similar model could be applied to joint-called VCFs.</p>

<p><strong>Under the hood</strong><br>
For details on how our deep learning tool works and how it stacks up to other variant filtering tools (both Deep Learning and traditional), as well as a bit of history on Deep Learning in genomics, see our other <a rel="nofollow" href="https://gatkforums.broadinstitute.org/gatk/discussion/10996/deep-learning-in-gatk4" title="blog post">blog post</a>.</p>

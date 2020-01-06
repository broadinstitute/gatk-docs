## Notice: Bug affecting BQSR run with Queue scatter-gather

By Geraldine_VdAuwera

<h3>We have discovered a bug that seriously impacts the results of BQSR/ BaseRecalibrator when it is run with the scatter-gather functionality of Queue. To be clear, the bug does NOT affect BaseRecalibrator runs performed "normally", i.e. WITHOUT Queue's scatter-gather.</h3>

<h3>Consequences/ Solution:</h3>

<p>Please be aware that if you have been using BaseRecalibrator scatter-gathered with Queue (GATK versions 2.0 and 2.1), your results may be wrong. You will need to redo the base recalibration of your data WITHOUT scatter-gathering.</p>

<p>This issue will be fixed in the next release (version 2.2). We apologize for any inconvenience this may cause you!</p>

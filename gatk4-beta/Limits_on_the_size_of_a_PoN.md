## Limits on the size of a PoN

By LeeTL1220

<p>Due to a shortcoming in the Java interface of the underlying storage space (HDF5), a single matrix cannot exceed a number of bytes equal to MAX_INT, which is 2147483647</p>

<p>Therefore, S x T x 8 &lt; 2147483647, where S is the number of samples and T is the number of targets.</p>

<p>In a simpler form:  S x T &lt; 268435455</p>

<p>This is usually only an issue for WGS PoNs.  In this case, we recommend setting larger binsize (e.g. 5000) to avoid a very large number of targets (T).</p>

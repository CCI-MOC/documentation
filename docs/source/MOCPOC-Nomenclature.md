We have a few separable setups in Hariri. It's a bit of a pain to keep
referring to them as e.g. "the one with the head nodes", or "the one the
ebbrt guys are using". This is especially true when trying to write
documentation. To this end, I (Ian) propose the following names for the
clusters:

* mocpoc-head - the cluster of twelve machines, managed via the SPL,
  with head nodes on death-star.
* mocpoc-fuel-lo - the fuel-based cluster composed of machines of the
  same model as mocpoc-head. This is the one the EbbRT project is using.
* mocpoc-fuel-hi - the other fuel-based cluster. This is the one sitting
  on top of mocpoc-fuel-lo (hence the names).

Suggestions for better names (particularly for the fuel-based clusters)
are welcome, but I will be using the above in documentation for the time
being.

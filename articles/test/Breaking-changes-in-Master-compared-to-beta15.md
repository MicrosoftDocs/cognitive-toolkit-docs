After we shipped beta15, we have mad some breaking changes that is currently in master. This breaking changes will make it to our release candidate 1 soon.

Based on the feedbacks that we got, we are changing some APIs, refactor some code, deprecated old APIs and changing namespaces to a more logical hierarchy. The plan is that after the release Candidate there will be very little breaking change.

Here current breaking changes:

- NDArrayView's data() method is now a property 
- Namespaces:
    - Readers are exposed through `cntk.io` namespace,  they no longer imported to the root `CNTK` namespace.
    - Several functionalities that were under `cntk.utils` are now refactored (removed) into their suitable namespace. Some of the popular ones are
	    - Print progress has moved to `cntk.logging`.
        - Print graph or finding nodes by names moved from `cntk.graph` to `CNTK.logging.graph`
	- All debugging APIs have moved to `cntk.debugging`.
	- Move profiler APIs to `cntk.debugging`.
	- Losses and metrics have moved to `cntk.losses` and `cntk.metrics` respectively.
	- Former `cntk_learner` namespace is replaced by `cntk.learners` with all learners moved under `cntk.learners` namespace. 
	- trainer, training_session, and distributed have moved to `cntk.train` namespace.
	- variables module has moved to cntk root namespace.
	- `one_hot` function is now a static method in Value class.
	- `value_to_seq` and `value_variable_to_seq` functions are now a method in Value `class` and renamed to `to_seq`.
	- Layers APIs aren't loaded by default, so you need `from cntk.layers import *`.
 
- Evaluating a node that has only the batch axis and no sequence axis, previously always returned the result containing a sequence dimension. This was fixed, i.e., it will now only output the batch axis.
- adam_sgd is deprecated:
	- If you used adam_sgd with low_memory equal True or didn't specify low_memory, then use fsadagrad.
	- Otherwise, use adam, which matches the published ADAM method.
- upper_pad and lower_pad are gone.


How do I find the support for following NDL LSTM primitives to Python:

**Delay**  

* How to pass argument in delay of a variable defined later in the network? E.g. for peep hole LSTM, cell state variable is defined later, but delay is needed to get t-1 cell state. Python doesn’t allow variables to be used first and defined later. 

* Ans: One needs to use a `placeholder_variable` and later a call to `replace_placeholders`. [Here](https://github.com/Microsoft/CNTK/wiki/Implement-an-attention-mechanism) is a simple example.


**RowStack**, **RowSlice** 

* Are there any substitutes for these primitives? If not how to implement them in python? Can we operate on variables as if they are numpy arrays?

* Ans: Use [splice](https://cntk.ai/pythondocs/cntk.ops.html?highlight=splice#cntk.ops.splice)
  

**DiagTime** vs **ElementTimes** 

* Is there any difference between them for vector element wise multiplication? Also is DiagTimes in supported in python?

* Use [element wise multiplication](https://cntk.ai/pythondocs/cntk.ops.html?highlight=element#cntk.ops.element_times)

**Parameter initialization** 

* How to Initialize parameters from file in python and set `computeGradient` as false.        

* Use [constants](https://cntk.ai/pythondocs/cntk.ops.html?highlight=splice#cntk.ops.splice). You can specify the initial value via a NumPy array. There are many ways to load a text (or other) file into a NumPy array.

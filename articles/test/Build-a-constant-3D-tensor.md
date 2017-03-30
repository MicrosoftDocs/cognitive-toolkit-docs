I want to build a constant 3D tensor in CNTK. I learned how to produce 1D and 2D constant arrays, I can stack (conacenate or combine) them and repeat them. Now I need to stack the 2D Tensors to make a 3D tensor?

Say you have three tensors, e.g.

``` 
A = ParameterTensor {100:200}
B = ParameterTensor {100:200}
C = ParameterTensor {100:200}
``` 

You can now say
 
```
    ABC = Splice (A:B:C, axis=3)
```
 
which will give you a [100 x 200 x 3] tensor.
 
(If, on the other hand, you had says Splice (A:B:C, axis=1), you would get a [300 x 200] tensor, and Splice (A:B:C, axis=2) would get you a [100 x 600] tensor.)
 
Note that to splice in a new dimension, dimensions of all inputs must match. E.g. you cannot stack a ParameterTensor {100:200} and a ParameterTensor {100:50} along axis 3 (but you could stack it along axis 2, which wold give you a [100 x 250] tensor).
 
Also note that axis indices are 1-based, like in math. E.g. the row dimension of a matrix is the first axis, axis=1, and the column dimension is the second axis, axis=2.



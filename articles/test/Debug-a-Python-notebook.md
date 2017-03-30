You will need to install ipdb via
```
pip install ipdb
```
Make sure the above installation happens for the correct python environment (the one used to run the notebook). 
Afterwards, in your notebook you should add this line to import the debugger.
```python
from IPython.core.debugger import Tracer
```
Finally, at the point where you want to debug add this line
```python
Tracer()()
```
When you run the cell with the `Tracer()()` the execution will drop into ipdb at the next line. Then you can start inspecting things with the usual [pdb](https://docs.python.org/2/library/pdb.html) commands. Don't forget to quit the debugger when you are done!

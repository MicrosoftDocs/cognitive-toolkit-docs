Remember that Brainscript is case sensitive. So code like 
```
# Special Nodes
OutputNodes = (ol)
```
will not work as expected. The names of all special nodes are *lower* camelCase
```
# Special Nodes
featureNodes = (features)
labelNodes = (labels)
criterionNodes = (ce)
evaluationNodes = (errs)
outputNodes = (ol)
```
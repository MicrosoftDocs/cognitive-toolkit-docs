---
title:   Plot command
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   01/20/2016
ms.custom:   cognitive-toolkit
ms.topic:   reference
ms.devlang:   brainscript
---

# Plot command

This command loads a given computation network and describes the network topology (usually a DAG) using the DOT (http://www.graphviz.org/doc/info/lang.html) language. It can also optionally call a third-part tool (http://www.graphviz.org/Documentation/dotguide.pdf) to render the network topology. Note that many DOT rendering tools are optimized for DAG, and the rendered graph becomes quite messy if the network structure is non-DAG. The non-DAG structure is usually caused by the use of delay node. In this case, this command will treat all the delay nodes as leaf nodes.

The related parameters of this command are:
* `modelPath`: the path to the computation network.

* `outputDOTFile`: the path to the output DOT file. If user does not specify it, ${modelPath}.dot will be assumed.

Optionally, if users want to convert the dot description to a figure, they need to specify the following parameters: 

* `outputFile`: the path to the output figure.

* `renderCmd`: A string indicates how the third party tool is used to convert from a DOT file to a figure. For example, if graphviz is used, the RenderCmd can be written as `RenderCmd="d:\Tools\graphviz\bin\dot.exe -Tjpg <IN> -o<OUT>"` where the plot command will substitute `“<IN>“` with the `outputDOTFile` command parameters and `“<OUT>”` with the `outputFile` command parameters; `-Tjpg` indicates a JPG file format is selected.

Here is a complete example:

    command = topoplot
    topoplot = [
        action = "plot"
        modelPath = "train\lstm.model.0"
    
        # outputdotFile specifies the dot file to output
        # if the user does not specify this, it will be ${modelPath}.dot
        outputdotFile = "train\lstm.model.dot" 
    
        # outputFile specifies the rendered image
        outputFile="train\lstm.model.jpg" 
    
        # if RenderCmd is specified, CNTK will call the plot command after replacing
        # <IN> with ${outputdotFile} and <OUT> with ${outputfile}
        renderCmd="d:\Tools\graphviz\bin\dot.exe -Tjpg <IN> -o<OUT>"
    ]


Below we describe the CNTK command line parsing rules. CNTK consists of a number of components to complete a task. Most of these components need some configuration information available in order to function, and these configuration parameters are provided through configuration files in CNTK. 

Configuration files are collections of name-value pairs. The configuration data can be one of the following types:
* Simple: a single value is assigned to the configuration parameter. For example, `deviceId = "Auto"`.
* Array: a configuration parameter is assigned an array of values which need not be of a uniform type. `:` is the default separator for arrays. The separator may be changed by enclosing the array values in parenthesis and placing the new separator character immediately following the open parenthesis. The `*` character allows a particular value to be repeated multiple times in the array. For example, `minibatchSize = 256:512:512:512:1024` equals to `minibatchSize = 
256:512*3:1024`.
* Set: parameter sets contain sets of configuration parameters of any type. Parameter sets can be nested. The default separator for parameter sets is `;` if multiple items are included on one line. Line separators also serve as separators for items. For example:

`block1 = [id=1;size=256]`

    block2 = [
        subblock = [string="hi";num=5]
        value = 1e-10
        array = 10:"this is a test":1.25
    ]

In CNTK, configuration files are organized in a hierarchical fashion. The actual data values are not evaluated until a CNTK component requests the value. When a value is requested by a component, CNTK will search inside the component block
first. If the value is not found, it will continue looking in the parent and grandparent parameter set until the parameter is found, or the top level of the configuration hierarchy is reached without a match. This allows the share of the same parameter values easier across different blocks. As we discussed previously, to run CNTK you need to specify the configuration file in the command line as `cntk configFile=yourExp.cntk` This will load the requested configuration file, and execute any command block listed in the command parameters in the configuration file.

## Commands and Actions
There must be a top-level command parameter, which defines the commands (separated with `:`) that will be executed in the configuration file. Each command references a command block in the file, which must contain an action parameter defining the operation that block will perform. For example the following command will execute the `mnistTrain` block, which executes the train action, followed by the `mnistTest` block, which evaluates the model.

    command = mnistTrain:mnistTest
    
    mnistTrain = [
        action = "train"
        ...
    ]
    
    mnistTest = [
        action = "eval"
        ...
    ]

## Configuration Overloads In the Command Line
It is common to have a configuration that can be used as a base configuration, and modify only a few parameters for each experimental run. This can be done in a few different ways, one of which is to override settings on the command line. For
example, to override the model file path, one can simply modify the command line as follows:

`cntk configFile=yourExp.cntk stderr="c:\temp\newpath"`

This will override the current setting for `stderr`, which is defined at the root level of the configuration file, with the new value. If a parameter inside a command block needs to be modified, the block also needs to be specified. For example, one can change the `minibatchSize` for an experiment in the command line as

`cntk configFile=yourExp.cntk mnistTrain=[minibatchSize=256]`

or modify the data file used for an experiment as

`cntk configFile=yourExp.cntk mnistTrain=[reader=[file="mynewfile.txt"]]`

## Layered Configuration Files
Instead of overriding some parts of a configuration file using command line parameters, one can also specify multiple configuration files, where the latter files override the earlier ones. This allows a user to have a master configuration file, and then specify, in a separate configuration file, which parameters of the master they would like to override for a given run of CNTK. This can be accomplished by either specifying a ’+’ separated list of configuration files, or by using the `configFile=` tag multiple times. The following are equivalent.

`cntk configFile=yourExp1.cntk+yourExp2.cntk`

`cntk configFile=yourExp1.cntk configFile=yourExp2.cntk`

If `yourExp2.cntk` only contains the string `mnistTrain=[reader=[file=mynewfile.txt]]`, then both of these commands would be equivalent to:

`cntk configFile=yourExp1.cntk mnistTrain=[reader=[file="mynewfile.txt"]]`

Note that the value of a variable is always determined by the last time it is assigned. It is also possible to mix command-line parameters, and layered configuration files, in arbitrary combinations. For example,

`cntk configFile=yourExp1.cntk+yourExp2.cntk var1=value configFile=yourExp3.cntk`

would process these configuration parameters in the order they appear on the command line and whatever value assigned last is the value used. 

In addition being able to specify multiple configuration files at the command line, a user can include one configuration file within another. For example, if the first line of `yourExp2.cntk` was

`include=yourExp1.cntk`

then simply running 

`cntk configFile=yourExp2.cntk` 

would be equivalent to running

`cntk configFile=yourExp1.cntk+yourExp2.cntk`

where in this latter case, `yourExp2.cntk` doesn’t contain the include statement. Note that these include statements can appear anywhere inside a configuration file; wherever the include statement appears, that is where the specified configuration file will be included. Including a configuration file is equivalent to pasting the contents of that file at the location of the include statement. Include statements are resolved recursively (using a depth-first search), meaning that if `yourExpA.cntk` includes `yourExpB.cntk`, and `yourExpB.cntk` includes `yourExpC.cntk`, then the full chain will be resolved, and `yourExpC.cntk` will effectively be included in `yourExpA.cntk`. If a configuration file is included multiple times (eg, ’A’ includes ’B’ and ’C’, and ’B’ also includes ’C’), then it will effectively only be included the first time it is encountered.

## Stringize Variables
While layered configuration files allow users to reuse configuration files across experiments, this can still be a cumbersome process. For each experiment, a user might have to override several parameters, some of which might be long file paths (e.g., `stderr`, `modelPath`, `file`). The "stringize" functionality can make this process much easier. It allows a user to specify configuration like the following:

    command = SpeechTrain
    stderr = "$Root$\$RunName$.log"
    speechTrain = [
        modelPath = "$Root$\$RunName$.cn"
        SGD = [
            reader = [
                features = [
                    type = "real"
                    dim = "$DataSet1_Dim$"
                    file = "$DataSet1_Features$"
                ]
            ]
        ]
    ]

Here, `Root`,`RunName`, `DataSet1_Dim`, and `DataSet1_Features` are variables specified elsewhere in the configuration (at a scope visible from the point at which they are used). When interpreting this configuration file, the parser would replace every string of the form `$VarName$` with the string `VarValue`, where `VarValue` represents the value of the variable called `VarName`. The variable resolution process is recursive; for example, if A=$B$, B=$C$, and C=HelloWorld.txt, then A would be resolved as "HelloWorld.txt". Please make sure there is no reference loop in your configuration file. Otherwise the parser will go into infinite loop at this moment.

Notice that because it is equivalent for a user to specify the value of a variable in a configuration file vs. at the command line, the values for these variables can be specified in either location. Recall that the value of a variable is determined by the last time it is assigned, whether that happens to be in a configuration file, or on the command line. Thus, if `Root` is defined in config1.txt, but overridden at the command-line, then the value specified at the command-line would be the one used to resolve instances of `$Root$` in configFile1.txt. One useful feature is that if `stderr` or `modelPath` point to directories which do not exist, these directories will be created by CNTK; this allows you to specify something like `stderr = $Root$\$RunName$\$RunName$.log`, even if the directory `$Root$\$RunName$` doesn’t exist.

## Default, Repeated Values, and Comments
Most parameters in configuration files have a default value which will be used if no configuration value is specified. If there is no default value and the value cannot be found in a search, an exception will be displayed and the program will exit. If a parameter name is specified more than once, the last value set to that value is the one that will be maintained. The only exception to this is in parameter sets, which are surrounded by `[` square braces `]`, in these cases the values inside the braces are considered to be a parameter set, and will be added to the currently existing parameter set. For example:

    params=[a=1;b=2;c=3]
    params=[c=5;d=6;e=7]

is effectively equal to:

    params=[a=1;b=2;c=5;d=6;e=7]

Note that this append processing is NOT used for array elements, and the entire array will be replaced if it is set multiple times. The `#` character signifies the beginning of a comment, everything that occurs after the `#` is ignored. The `#` must be preceded by whitespace or be at the beginning of the line to be interpreted as a comment. The following is valid comment

`stderr="c:\cntk\log\cntk" # "_mnistTrain_mnistTest.log"`

The following is an example of a value that will not be interpreted as a comment. It sets a parameter `var` to infinity as the `#` in `1#INF` is not a comment marker

`var=1#INF`

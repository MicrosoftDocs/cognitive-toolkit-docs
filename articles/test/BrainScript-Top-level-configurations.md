Besides commands, there are several top-level configurations that you can set. The following is the taken form the Simple2d [example](Examples):

    rootDir = ".."
    
    configDir = "$rootDir$/Config"
    dataDir   = "$rootDir$/Data"
    outputDir = "$rootDir$/Output"
    modelDir  = "$outputDir$/Models"
    
    # deviceId=-1 for CPU, >=0 for GPU devices, "auto" chooses the best available device
    deviceId = -1
    
    command = Simple_Demo_Train:Simple_Demo_Test
    
    precision = "float"
    traceLevel = 1
    modelPath = "$modelDir$/simple.dnn"

You can add comments or comment out lines using `#`. You can define variables like `rootDir` above and use them later using `$rootDir$`. Variables can be overwritten on the command line by simply specifying them as additional arguments. The following top level configurations are available:

## `stderr`
All logging information in CNTK is sent to standard error, and will appear on the console screen. You can redirect the logging information to a file with the `stderr` parameter. The value of the `stderr` parameter defines the folder and the prefix of the log file. The suffix is determined by the command block executed. For example, if you set `stderr="c:\cntk\log\cntk"` and the command block `mnistTrain` is to be executed then the full log file name will be `c:\cntk\log\cntk_mnistTrain.log`. It is important to note that this file is overwritten on subsequent executions if the `stderr` parameter and the command being run are identical.

## `deviceId`
CNTK supports CPU and GPU computation. Users can determine what device to use by setting the `deviceId` parameter. The possible values are
* `"auto"`: choose the best device available. If multiple GPUs are available, it will choose the fastest, least busy GPU. If no usable GPUs can be found CPU will be used.

* `"cpu"` or `-1`: use the CPU for all computation.

* `0`: use the GPU of your system. If you have multiple, use the first.

* any non-negative integer: use the GPU identified by this number to carry out all computation.

## `excludedDevices`
This option is meant to be used in conjunction with the `deviceId = "auto"` automatic device selection setting. An explicit list of device ids (separated by `:`) can be specified here to exclude from the device selection process. For example, to avoid using the CPU, set `excludedDevices = -1`.

## `precision`
Chooses between `float` (32-bit) and `double` (64-bit) floating-point precision.
The precision of the floating point values determines the trade-off between the numerical accuracy and the training and testing speed. In CNTK, the precision is specified at the top-level as `precision = "float"` or `precision = "double"`. For most experiments you should use `float` since it is significantly faster, especially when running under GPUs. When using the gradient-check feature, `precision` should be set to `double`.

## `numCPUThreads`
The `numCPUThreads` parameter determines the maximum CPU threads used for CPU math operations. In CNTK it is specified at the top-level as `numCPUThreads = 4`. By default the value is `0`, which means using what ever number of threads determined by MKL, or OpenMP, respectively. If a positive number is specified, the number of threads will not be larger than the value specified and the number of hardware concurrency. If a negative number is specified, the actual number of thread will be the sum of the number of hardware concurrency and this value floored at 1. 

## `traceLevel`
The `traceLevel` parameter is uniformly used by the code in CNTK to specify how much extra output (verbosity) is desired as in 

    traceLevel = 0 # larger values mean more output

The default value is `0` and specifies minimal output. The higher the number the more output can be expected. Currently `0` (limited output), `1` (medium output) and `2` (verbose output) are the only values supported.

## `traceGPUMemoryAllocations`
Trace GPU memory allocation. The default value is `0` and specifies no output. Setting it to `1` enables tracing.

## `forceDeterministicAlgorithms`
The default value of this flag is `false`.

Due to high level of parallelism, commands in CNTK are not always deterministic. In order to force use of deterministic algorithms, please use `forceDeterministicAlgorithms` flag in the outermost scope of your configuration. Setting this flag to `true` forces CNTK to use a single tread for MKL/OMP calculations and requires convolutions to use only deterministic algorithms. This can impact performance, so we do not recommend it for performance critical tasks.

In case the deterministic algorithms are not available for a particular network setup, an exception will be thrown with the appropriate error message.

## `hyperCompressMemory`
The default value of this flag is `false`

When this feature is set to `true`, it works to reduce memory usage by using a pool.
The steps for debugging CUDA kernels:

1. Install NVIDIA Nsight following the directions from [here](http://docs.nvidia.com/gameworks/index.html#developertools/desktop/nsight/install_debug_monitor.htm%3FTocPath%3DDeveloper%2520Tools%7CDesktop%2520Developer%2520Tools%7CNVIDIA%2520Nsight%2520Visual%2520Studio%2520Edition%7CNVIDIA%2520Nsight%2520Visual%2520Studio%2520Edition%25205.2%7CInstallation%2520and%2520Setup%2520Essentials%7C_____2)
1. Follow the directions for for “Local debugging”.
1. Set the environment variable NSIGHT_CUDA_DEBUGGER = 1.
1. Run Visual Studio and the Nsight monitor as administrator.
1. In Nsight Monitor->Options->CUDA, set “Use this monitor for CUDA attach” to True. You may have to restart Nsight. Run as admin again.
1. In Visual Studio, go to Nsight->Options and make sure the options match up with your options in Nsight monitor (e.g. the ports are the same). Especially make sure ”Establish secure connection” is the same in both.
1. Right click on the MathCUDA project in the solution explorer and go to Properties.
1. Go to Configuration Properties -> CUDA C/C++ -> Device and set Generate GPU Debug Information to Yes
1. Go to Configuration Properties -> CUDA Linker -> General and set Generate GPU Debug Information to Yes
1. Add your breakpoints in your kernel, rebuild CNTK, and get ready to run whatever you’re trying to debug.
1. In VS, go to Debug -> Attach to Process, set Transport to Nsight GPU Debugger, and set Qualifier to localhost.
1. Start CNTK.
1. Click refresh and find CNTK in the process list, then attach. When it hits a breakpoint you should be able to see all of your local variables from the kernel. If you only see CUDA globals like threadIdx and blockIdx, you haven’t properly set the GPU Debug flags in the MathCUDA properties.

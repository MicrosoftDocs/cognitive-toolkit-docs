---
title:   How  to test
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   10/01/2016
ms.prod: cntk
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.devlang:   python
---

# How to test

The unit tests and end-to-end tests provided in the <CNTK Repo root>\Tests folder are run as part 
of the check-in workflow on the build server on the Windows and Linux platforms. You can run the
tests for your platform locally to verify that your code changes didn't break any test.

## Unit tests

Unit test are currently only supported (and checked) for GPU builds.

### On Windows
Make sure you set up the Boost libraries and environment variables as well as the Boost test runner
for Visual Studio. For instructions see [here](./Setup-CNTK-on-Windows.md#boost). 

Restart Visual Studio and go to to the 'Test --> Test Settings' menu. There set 
the default processor architecture to x64 and uncheck the option 'Keep Test 
Execution Engine Running'. In the Test Explorer window select Group by Traits 
(next to the search field). After a rebuild you should see all unit tests in 
the Test Explorer. You can run and debug using the context menu.

### On Linux
First you need to install the Boost libraries. For instructions see [here](./Setup-CNTK-on-Linux.md#boost-library). 

Then you can build unit test binaries in the same way as the CNTK binary, as specified [here](./Setup-CNTK-on-Linux.md). The binaries are created in the same folder as the CNTK binary.
The following unit tests are currently available:
* mathtests -- unit tests for math operations
* readertests -- unit tests for readers
* networktests -- unit tests for network operations
* brainscripttests -- unit tests for brainscript 
* evaltests -- unit tests for Eval

You can launch each unit test by running the corresponding binary file. You can also use command line parameters described by [Boost Unit Test Framework](http://www.boost.org/doc/libs/1_60_0/libs/test/doc/html/boost_test/utf_reference/rt_param_reference.html) to control running tests and test output. 

## End-to-end tests

The CNTK end-to-end tests can be run on Linux and Windows using the Python 
script 'TestDriver.py' located in <CNTK Repo root>\Tests\EndToEndTests. 
Alternatively they can be run and debugged from Visual Studio. In the following 
we detail:
* How to use the TestDriver.py script.
* Prerequisites to use TestDriver.py on Windows.
* How to run and debug end-to-end tests from Visual studio.

### How to use the TestDriver.py script.

Start a shell (CygWin Bash shell under Windows) and change directory to the Tests\EndToEndTests folder of 
your local CNTK repository (note: c:\src\CNTK in CygWin is /cygdrive/c/src/CNTK/).
Start with one of the following commands to learn the usage and options of the 
TestDriver:

`python TestDriver.py -h`

`python TestDriver.py run -h`

`python TestDriver.py list -h`

To list all available end-to-end tests run

`python TestDriver.py list`

To run a single test, for example Image/QuickE2E, execute

`python TestDriver.py run Image/QuickE2E`

You can add for example '-d gpu' to only run the test using a GPU or '-f debug' 
to  only run the test using the debug build. See `python TestDriver.py run -h` 
for all options.

To run all tests from the nightly builds execute

`python TestDriver.py run -t nightly`

### Prerequisites to use TestDriver.py on Windows.

1. Install Anaconda Python 2.7 (not 3.5) from here: https://www.continuum.io/downloads which contains the most of the popular Python packages. Alternatively install Python 2.7 https://www.python.org/downloads/ and install required additional packages as prompted.

2. Install CygWin from http://cygwin.com/install.html During installation select 
"Install from Internet" (default selection).
IMPORTANT: At Select Packages screen type "yaml" in the search field and expand 
the Python section. Select "python2-yaml: Python Lib YAML bindings" 
(NOT "python3-yaml"). Similarly, type "python2-setuptools" in the search field, expand the Python section, and select "python2-setuptools" for installation. Then finish installation.
3. Make sure you have Microsoft MPI installed (there should be an environment 
variable named MSMPI_BIN. For this you need to download and run the **exe** and not the msi)
4. Launch a Cygwin Bash shell.
5. Run "easy_install-2.7 pip"
6. Run "pip install six"

Note: Some tests require an environment variable named 
CNTK_EXTERNAL_TESTDATA_SOURCE_DIRECTORY pointing to where the data resides. 
If the external data is not available those tests will be skipped.

### How to run and debug end-to-end tests from Visual Studio.

You can generate the Visual Studio debug command arguments using the `-n` options
on the TestDriver for a specific end-to-end test:

`python TestDriver.py run -n Image/QuickE2E`

From the output of the above command you simply copy the 'VS debugging command args' 
to the command arguments of the CNTK project in Visual Studio (Right click on CNTK 
project -> Properties -> Configuration Properties -> Debugging -> Command Arguments). 
Start debugging the CNTK project.


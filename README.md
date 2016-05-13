[![Build Status](https://travis-ci.org/NordicSemiconductor/nrfjprog.svg?branch=master)](https://travis-ci.org/NordicSemiconductor/nrfjprog)
[![PyPI](https://img.shields.io/pypi/l/Django.svg)](https://opensource.org/licenses/BSD-3-Clause)

# nrfjprog
The nrfjprog command-line tool implemented in Python. nrfjprog.exe is a tool to program Nordic Semiconductor's nRF51 and nRF52 series devices. Using a tool such as PyInstaller or py2exe this module can be converted to a stand-alone (cross-platform) executable. This will give the user the option to run nrfjprog without worrying about their python environment or to use this module in their custom scripts (i.e. automated testing) as a higher-level alternative/supplement to pynrfjprog.

# Running the exe
1. Download the zipped 'dist' folder for your operating system and extract it.
2. Either add the path containing 'nrfjprog.exe' to your environment variables or navigate to that directory in ~/dist/.
3. $ nrfjprog -h (nrfjprog.exe -h if path not added to your environment variables).
4. $ nrfjprog program -h
5. $ nrfjprog program -f PATH_TO_APP.hex -e -v -r

# Running in Python
1. $ sudo pip install -r requirements.txt
2. Clone or download this repository.
3. Navigate to the repository's root directory (~/nrfjprog/).
4. $ python nrfjprog --help
5. $ python nrfjprog program --help
5. $ python nrfjprog program --file PATH_TO_APP.hex --eraseall --verify --systemreset

# Structure
```python
nrfjprog\
  # LICENSE, README.md, setup.py and requirements.txt (used to install this module).
  nrfjprog\
    __init__.py # Package marker to make nrfjprog a module.
    __main__.py # This is where the command line interface is implemented. It parses arguments using argparse and calls functions in 
    nrfjprog_version.py # A global variable containing the version of nrfjprog.
      model\
        __init__.py # Package marker to make model a module.
        perform_command.py # Determines if a CMSIS-DAP/DAP-Link or JLink debugger is connected to the PC and fowards the command accordingly.
        perform_command_daplink.py # This is where the functionality of each command is implemented. Relies on the pyOCD module.
        perform_command_jlink.py # This is where the functionality of each command is implemented. Relies on the pynrfjprog module.
        device.py # Implements a class to represent the specs of a specific device (i.e. NRF52_FP1).
tests\
  unit_tests.py # All of the unit tests for nrfjprog.exe. Requires that dist/OS/ to be present on system which contains the built .exe for the system's OS.
```

# Architecture
```python
"""
Detailed below is how our software is stacked. Each layer depends on the layer below.
"""
nrfjprog.exe # Command line tool providing high level programming functionality for nRF5x devices.

pynrfjprog # Imports the nrfjprog DLL into Python and wraps it to be used in applications like this one or directly in scripts.
nrfjprogdll # A DLL that does some error checking and calls SEGGER's JLink API. Wraps JLink API specifically for nRF5x devices.
JLinkARMDLL # A DLL provided by SEGGER that works with SEGGER debuggers. Performs all low level operations with target device.

or, if using a CMSIS-DAP/DAP-Link debugger, than only:
pyOCD # https://github.com/mbedmicro/pyOCD.
```

# Bundling as an executable
```python
"""
PyInstaller bundles a Python application and all its dependencies into a single package and is tested against Windows, Mac OS X, and Linux. http://pythonhosted.org/PyInstaller/.
We will use PyInstaller to create an executable to distribute to the end user from our nrfjprog Python application. It will be multi platform.
Currently we bundle into a single package but we can also bundle into a single executable (one file) using PyInstaller.
"""
```
1. $ sudo pip install pyinstaller
2. Navigate to ~/nrfjprog and run $ pyinstaller __main__.py --clean --name nrfjprog
3. Move the DLL's required by the official nrfjprog.exe into ~/nrfjprog/dist/nrfjprog/
4. Navigate to ~/nrfjprog/dist/nrfjprog and run $ nrfjprog.exe --help
5. Add ~/nrfjprog/dist/nrfjprog to your path and call $ nrfjprog -h from any directory.

# Coding Standard
*  https://google.github.io/styleguide/pyguide.html
*  http://www.clifford.at/style.html
*  http://semver.org/

# Future
We want nrfjprog to be flexible and open. We want it to be an option for our users all the way from development and testing to production programming.

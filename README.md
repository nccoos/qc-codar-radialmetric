# qccodar

This python code applies several quality control (QC) functions based
on CODAR SeaSonde (COS) Radialmetric data currently output in COS
RadialSuite version 7.x. There are two modes to this code: an auto-
and manual-mode.  Auto-mode is for realtime processing. Manual-mode is
for processing all RadialMetric files in a specified folder in
post-processing. qccodar is intended to run beside (in parallel) to
SeaSonde Analysis and not supplant any processing.  In fact, qccodar
uses the LLUVMerger.app provided by SeaSonde to merge the data back
into standard SeaSonde processing methodology.

## Quickstart

## Installation

qccodar is a python package and runs under Python 2. Eventhough Mac
OS X comes with Python 2 installed or you can install Python
directly from
[python.org](https://wiki.python.org/moin/BeginnersGuide/Download), it
is recommended to use the lightweight option from
[Conda](https://conda.io/docs/index.html) called
[miniconda](https://conda.io/miniconda.html).  Miniconda contains only
Python and other libraries needed to run Conda itself; other packages
will be downloaded and installed as requested.  Conda has a package
manager which makes this installation easy.  Conda also supports
virtual environments where qccodar can run independently from the
system-installed Python. 

The following instructions show how to install and configure Miniconda2
and qccodar to provide QC'd Radial data based on RadialMetric output.  

While logged on as user `codar`, open a terminal and issue the following. 

```bash
   $ cd ~/Downloads
   $ curl https://repo.continuum.io/miniconda/Miniconda2-latest-MacOSX-x86_64.sh -o "miniconda.sh"
   $ bash ~/Downloads/miniconda.sh -p $HOME/miniconda
```

Creating a conda environment allows the qccodar module and its
dependencies to run isolated from the installed Python avoiding
potential module version conflicts.  While this is not a requirement
to get qccodar running, it is recommended.

As user `codar`, open a new terminal window to source the .bash_profile.

```bash
   $ conda create --name qccodar python
```

To activate the environment:
```bash
   $ source activate qccodar
   (qccodar) $ which python
   /Users/codar/miniconda/envs/qccodar/bin/python
```

To install qccodar within the conda environment:
```bash
   (qccodar) $ pip install qccodar
```

Or use `git` to retrieve code distribution from the github repo and run the setup.py:
```bash
   (qccodar) $ git clone https://github.com/nccoos/qccodar.git
   (qccodar) $ cd qccodar
   (qccodar) $ python setup.py install
```

Either method will install qccodar together with all the required
dependencies.  After configuring CODAR RadialSuite to ouput
RadialMetric data, you can then run qccodar in either auto- or
manual-mode. 

## Configuration

## Background

### Notes

| QC Function        | Description |
| -----------        | ----------- |
| Threshold Tests    | badflag any values that fall below or above a single threshold |
| Weighted Averaging | average several values with weights based on signal quality parameters |

#### QC Threshold Tests:
1. DOA peak power (MSR1, MDR1, MDR2) < 5 dB default 
1. DOA 1/2 power width (3dB down) (MSW1, MDW1, MDW2) > 50 deg default
1. SNR on monopole (MA3S) < 5 dB default
1. SNR on both loops (MA1S and MA2S) < 5 dB

#### Weighted Averaging:
1. Weighting based on Music Power (MSP1, MDP1, MDP2)
1. Weighting based on SNR on monopole (MA3S)
1. No weight function (None) 
 
### System Requirements

- Python 2.7.x
- Numpy 1.9.x
    - https://pypi.python.org/pypi/numpy
    - Data read into memory are stored in the N-dimensional array datatype (ndarray) for indexing and computation.
- geopy 1.11.0
    - https://pypi.python.org/pypi/geopy
    - geopy.distance.vincenty()
    - Used to compute (LAT, LON) based on range and bearing from site origin in generating RadialShorts file
- watchdog 0.8.2
    - Used to monitor a directory for new files and trigger qc and merge process when new RadialMetric file is created
- CODAR SeaSonde RadialSuite 7.x (version 8 does not support RadialMetric output unless requested from CODAR)
    - /Codar/SeaSonde/Apps/Bin/LLUVMerger.app
    - Used to merge spatial and temporal RadialShorts data to final Radial

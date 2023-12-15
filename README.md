# Introduction and setting up
This document is a brief guide to using the famous Rybicki-Hummer (RH) code during WSSP 2023. We will use the open-source [1.5D](https://github.com/ITA-Solar/rh) code version, which is a massively-parallel radiative transfer code for spectral synthesis in stellar atmospheres. This code was developed by Tiago Pereira and Han Uitenbroek in [2015](https://ui.adsabs.harvard.edu/abs/2015A%26A...574A...3P/abstract) and it also contains a detailed [online documentation](https://rh15d.readthedocs.io/en/latest/index.html). This is a modified version of the original code developed by Han Uitenbroek in [2001](https://ui.adsabs.harvard.edu/abs/2001ApJ...557..389U/abstract). Hopefully, if everything goes as planned, you will be able to run this code yourself on your personal computers during this hands-on session. 

## Installing Python3.x and other necessary packages
Over the next few days, I will be sharing instructions on how to compile this code on your personal computers (in macOS/Linux only!) but before that, I would like you to install Python and other necessary packages as detailed below. If you already have them installed, good! If not, here is a guide using [Miniconda](https://docs.conda.io/projects/miniconda/en/latest/). I prefer (also recommend) using Miniconda over Anaconda as it is lighter on your system and easy to install. Miniconda is especially handy when you are working with virtual systems or Windows sub-systems for Linux [WSL](https://learn.microsoft.com/en-us/windows/wsl/install). Open your terminal and follow the following steps.
```
1. mkdir -p ~/miniconda3
2. wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh (Linux)
                                                      or
   curl https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-arm64.sh -o ~/miniconda3/miniconda.sh (macOS)
3. bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
4. rm -rf ~/miniconda3/miniconda.sh
```
After installing, initialize your newly-installed Miniconda. The following commands initialize for bash and zsh shells:
```
~/miniconda3/bin/conda init bash
~/miniconda3/bin/conda init zsh
```
This should also install Python in your system. You can check `which python` and `python --version` if you are interested in knowing where is Python installed and its version. 

### Installing necessary Python packages all at once using a YAML file through conda

YAML stands for **yet another markup language**. You can use YAML files to list all the necessary packages for a project through Conda in one go! I prefer to do it this way because it creates a separate environment without hampering the existing installation/packages. This should be the way also for those who already have Python or anaconda/miniconda installed in their system beforehand. We don't want to affect your parent installation :)

1. Create the following YAML file and save it as `environment.yml` in your work directory.
   
   ```
   name: rh15d_exercises
   channels:
    - defaults
    - conda-forge
   dependencies:
    - numpy
    - scipy
    - astropy
    - matplotlib
    - xarray
    - cython
    - h5py
    - tqdm
    - specutils
    - numba
    - bqplot
   ```

2. Navigate to the directory containing the YAML file and use `conda env create -f environment.yml` to create the conda environment. This command will also install all the dependencies listed above and will look for them in two channels namely, defaults and conda-forge.
3. If everything goes well, you will have everything installed in the environment called `rh15d_exercises`. But you need to activate it using
   ```
   conda activate rh15d_exercises
   ```
4. And a final step would be to install `Jupyter lab`. We will need it to follow the exercise notebooks and visualize the output of RH 1.5D. To install it simply type `conda install -c conda-forge jupyterlab`

Once you have followed all the above steps and have everything installed you can check them by typing `jupyter lab` in your terminal session and do
```
import numpy
import scipy
import xarray
and so on
```
to check if everything is loaded. If so, you are done with setting up the Python part.

## Install and compiling RH1.5D
The following steps have been tested for macOS Intel and Apple Silicon M1 processors and Linux (Ubuntu) distribution. _These may not work on **Windows**_. If you are using Windows (why?), you can try to install Linux on your machine by using the steps outlined in [WSL](https://learn.microsoft.com/en-us/windows/wsl/install) and then try to follow the Linux steps. 
### For Mac users

RH needs C and Fortran compilers with the OpenMPI and HDF5 (parallel build) libraries. If you are using a Mac and have Homebrew installed (recommended) you can simply install these compilers using `brew install ----`. The first step is to make sure you have C and Fortran compilers installed. Most UNIX machines have this installed (`gcc` and `gfortran`). Simply check if so by typing `which gcc` and `which gfortran`. If not you can simply do `sudo brew install gcc` to install both. HDF5-mpi (HDF5 parallel build) and OpenMPI can be installed together with `sudo brew install hdf5-mpi`. Once you have everything set above, you need to

1. Clone the RH repository.
2. Edit the `Makefile.config`, change `CC` to `/usr/local/opt/openmpi/bin/mpicc`, `LD` to `/usr/local/opt/openmpi/bin/mpicc` and `HDF5_DIR` to `/usr/local/opt/hdf5-mpi`. Note that installing compilers (or any software for that matter) through `brew` installs them in the path `/usr/local/opt/` in your Mac.
3. Compile!

```
git clone https://github.com/ita-solar/rh.git
cd rh
vim Makefile.config
make -j8
cd rh15d
make -j8
```

If everything goes well (FINGERS CROSSED!), the first `make -j8` if successful should produce `librh.a` and `librh_f90.a` library files inside the `rh` directory and the last `make -j8` should produce three executables `rh15d_ray_pool`, `rh15d_ray` and `rh15d_lteray` inside the `rh/rh15d` directory. **If you see these files, Congrats! you have successfully compiled and installed RH1.5D**.

### For Linux users

The steps are very similar to those in the MacOS case. Your machine could have gcc and gfortran compilers installed. If not, you can use `sudo apt-get install gcc` followed by `sudo apt-get install gfortran`. Check their versions by `gcc -v` and `gfortran -v`. Installing HDF5 parallel can be a bit tricky here. There are a variety of ways to do this. The safest bet is to download HDF5 from [source](https://www.hdfgroup.org/downloads/hdf5/source-code/) and follow the instructions similar to what is mentioned [here](https://forum.hdfgroup.org/t/installing-hdf5-ready-version-of-open-mpi/4998). But this is perhaps a bit complex and people with little familiarity with developing packages in Unix might find it non-trivial. The easier way (which I follow :) is to use `conda` to download and install `hdf5-parallel` using `conda install -c clawpack hdf5-parallel`. This should install hdf5 with parallel support. Note that if you are going to do this, you should have your conda environment activated already, otherwise it may not work. Once done follow the steps above as in MacOs and change the `HDF5_DIR` to `HOME/miniconda/` or wherever you have miniconda installed. Compile!  

### Installing the [Helita](https://ita-solar.github.io/helita/) python package.
We will use the Helita python package to load, visualize, and generate model atmospheres needed for RH15D synthesis. You need to go to your home directory under your active conda environment and:
```
git clone https://github.com/ITA-solar/helita.git
cd helita
python setup.py install
```
You will need the C and Fortran compilers to setup helita. Seee above.

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
The following steps have been tested for macOS Intel and Apple Silicon M1 processors and Linux (Ubuntu) distribution. These may not work on Windows. If you are using Windows (why?), you can try to install Linux on your machine by using the steps outlined in [WSL](https://learn.microsoft.com/en-us/windows/wsl/install) and then try to follow the Linux steps.

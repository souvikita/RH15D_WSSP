# Auxillary files

The RH 1.5D distribution you obtained from Github does not contain all the files necessary for this tutorial. Please download an [additional archive](https://github.com/souvikita/RH15D_WSSP/blob/main/rh_wssp.tar.bz2) and put it inside the RH main folder. Unpack the archive as follows:

`tar jxvf rh_wssp.tar.bz2`

This will place all files in their correct directories.

# Quickstart: Running RH1.5D and exploring its output

You should run your code inside the `run` directory. Now, there lies a `rh/rh15d/run_example` directory. Simply copy this directory to `run` on your own so that you can make changes:

```
cp -rp run_example run
cd run
```
Inside the `run` directory there are already some sample input files (e.g. `keyword.input`, `atoms.input` etc) with which you can do a test run by doing:
```
../rh15d_ray
```
By default, it will run forward modeling of the Ca II atom because it is set as active in the `atoms.input` file with only _one_ processor. The output will look like:

```
[barnes-2:~/rh/rh15d/run] souvikb% ../rh15d_ray
Process    0: --- START task   1 [of 1], (xi,yi) = (  0,  0)
Process    0: *** END   task   1 iter, iterations =  22, CONVERGED
Process    0: --- START output
Process    0: *** END output
*** Job ending. Total 1 1-D columns: 1 converged, 0 did not converge, 0 crashed.
*** RH finished gracefully.
```
The last two lines above tell you about the summary of the run. Note that there are two other binaries `rh15d_ray_pool` and `rh15d_lteray`. **Can you check what happens if you run them instead of `../rh15d_ray`? What happens if you run `../h15d_ray_pool` without using `mpiexec` or `mpirun`?**

## Check the notebook named `Explore_RH_output_input.ipynb` for visualization purposes.

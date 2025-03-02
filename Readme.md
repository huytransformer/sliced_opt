
# Sliced optimal partial transport (SOPT)
## Introduction 
This restortion contains example code for sliced optimal partial transport introduced in [Sliced optimal partial transport](https://arxiv.org/abs/2212.08049). All code is written in Python / numpy / scipy /torch without any additional exotic dependencies. The code reproduces all the numerical examples in the paper. 

##  Setup 
To run the code, you must first install [Pytorch](https://pytorch.org/get-started/locally/), 
[numba](https://numba.pydata.org) 
```
conda install numba 
```
and [PythonOT](https://pythonot.github.io) 
```
pip install pot
```

## Optional: Installing C++ 1D OPT solver 
We also provide the C++ implementation of our 1D OPT solver. 
To use the C++ solver, you must first compile the 1D Partial Optimal Transport with pybind11. 

### Installing pybind11

Start by cloning the pybind11 repo:

```
conda install -c conda-forge pybind11
```
Ensure cmake, cython, and pytest are installed: 
```
conda install cmake
conda install pip
pip install cython pytest
```
Then use: 
```
cd pybin11
mkdir build
cd build
cmake -DDOWNLOAD_CATCH=ON ..
cmake --build . --config Release --target check
```

### Installing opt1d
You can use the following to compile the opt1d.cpp:
```
c++ -O3 -Wall -shared -std=c++11 -fPIC $(python3 -m pybind11 --includes) opt1d.cpp -o opt1d$(python3-config --extension-suffix)
```
or (if the first one does not work.)
```
c++ -O3 -Wall -shared -std=c++11 -fPIC $(python3 -m pybind11 --includes) opt1d.cpp -o opt1d.so
```

You will get a compiled c++ file. In my computer, it is


```
opt1d.cpython-310-x86_64-linux-gnu.so
```
or 

```
opt1d.so
```


Please ensure to replace the paths with your relevant paths. 

### Test opt1d

If all has gone well, then we can test the code in Python: 

```
python3 test_opt1d.py
```

this should results in saved plot as follows.
![Results of test_pot1d.py](Lambda.png)
## Examples: 
First please ensure to replace the paths with your relevant paths. 
- Run the file running_time.ipynb to see the comparision of running time between our method, spot, Sinkhorn, and Linear programming (Network simplexity). 
You should comment out the lines which contains "opt1d.solve" if the C++ implementation of our 1D opt solver is not installed. 
- shape_registration.ipynb demonstrates an example of our method and other methods in shape registration problem. 
- color_adaptation.ipynb demonstrates an example of our method and other methods in color adaptation problem. 
## Outline of repository
### OPT solver

The file code/sopt/lib_ot.py contains our 1-D optimal partial transport (OPT problem) solver. In particular, it contains the following functions, most of which are accelarated by numba: 
- "solve_opt" (for data in float64): our 1-D Optimal partial transport (OPT) sovler 
- "opt_lp": linear programming sovler for OPT 
- "sinkhorn_knopp": the Sinkhorn-knopp algorithm for classical OT problem (see [1])
- "sinkhorn_knopp_opt": Sinkhorn-knopp algorithm for OPT problem accelarated by numba 
- "pot": python implementation of the solver for partial optimal transport problem (algorithm 1 in [2])



### Shape registration 
The file code/sopt/lib_shape.py contains our method for shape registration experiment based on our OPT solver. In particular it contains the following functions: 

- sopt_main: our method for shape registration problem based on our 1-D OPT sovler 
- spot_bonneel: one python implementation of "Fast Iterative Sliced Transport algorithm" based on SPOT in [2]) 
- icp_du: Du's ICP algorithm (see [3])
- icp_umeyama: classical ICP algorithm (see [4]) 



### Color adaptation 
The file code/sopt/lib_color.py contains our color adaptation method based on our 1-D OPT solver. In particular, it contains the following functions: 
- sopt_transfer: our color transporation method based on 1-D OPT solver. 
- spot_transfer: one python implementation of the color transportation method based on SPOT (see [2])
- ot_transfer: one python implementation of OT-based color adaptation method (we modify the code of ot.da.EMDTransport in PythonOT to improve the speed) (see [5])
- eot_transfer: one python implemtation of entropic OT-based color adaptation method (we modify the code of ot.da.SinkhornTransport in PythonOT to improve the speed) (see [5])








## References

[1] Marco Cuturi. Sinkhorn distances: Lightspeed computation of optimal transport. Advances in neural information processing systems, 26, 2013

[2] Nicolas Bonneel and David Coeurjolly. SPOT: sliced partial optimal transport. ACM Transactions on Graphics, 38(4):1–13, 2019

[3] haoyi Du, Nanning Zheng, Shihui Ying, Qubo You, and Yang Wu. An extension of the icp algorithm considering
scale factor. In 2007 IEEE International Conference on Image Processing, volume 5, pages V–193. IEEE, 2007

[4] Shinji Umeyama. Least-squares estimation of transformation parameters between two point patterns. IEEE Transactions
on Pattern Analysis & Machine Intelligence, 13(04):376–380, 1991.

[5] Ferradans, S., Papadakis, N., Peyre, G., & Aujol, J. F. (2014). Regularized discrete optimal transport. SIAM Journal on Imaging Sciences, 7(3), 1853-1882.

---
layout: "post"
title: "julia pyplot issue"
date: "2018-12-3 17:51"
---

#### Problems
1. runtime error.
  ```
Runtime Error R6034
Program; C:\Users\c\AppData\Local\Julia-0.6.0\bin\julia.exe 
R6034 
An application has made an attempt to load the C runtime library incorrectly. 
Please contact the application's support team for more information.```
  ```

2.  `Qt` error
	```
The application failed to start because it could not find or load the QT platform plugin "windows" in "",
Available platform plugins are: minimal, offscreen, windows
	```


#### Solution 1

1. do not use `pyplot()`, use `GR()`
```julia
using Plots
GR()
histogram(randn(10000))
```

#### Solution 2

do the following instruction step by step.

1. re-install `matplotlib`
```bash
conda uninstall matplotlib
pip install -U matplotlib    
```

2. test your matplotlib will work in python.
```python
import matplotlib.plot as plt
import numpy as np
x = np.linspace(0,2*pi,1000); y = np.sin(3*x + 4*np.cos(2*x));
plt.plot(x, y, color="red", linewidth=2.0, linestyle="--")
plt.show()   
```

3. config your python path, then rebuild the `PyCall` module.
```julia
using Pkg
ENV["PYTHON"]="D://miniconda//python.exe" # "path to your python"
Pkg.build("PyCall")
```

4. verify you can use `PyCall` to call `Matplotlib` in julia.

```julia
using Pycall
@pyimport numpy as np
@pyimport matplotlib.pyplot as plt
x = np.linspace(0,2*pi,1000); y = np.sin(3*x + 4*np.cos(2*x));
plt.plot(x, y, color="red", linewidth=2.0, linestyle="--")
plt.show()
```

5. reinstall `Plots` and `PyPlot`.
```julia
using Pkg
Pkg.rm("Plots")
Plt.rm("PyPlot")
Pkg.add("Plots")
Pkg.add("PyPlot")
```

6. finally check that you can see the `gui`.
```julia
using PyPlot
# use x = linspace(0,2*pi,1000) in Julia 0.6
x = range(0; stop=2*pi, length=1000); y = sin.(3 * x + 4 * cos.(2 * x));
plot(x, y, color="red", linewidth=2.0, linestyle="--")
title("A sinusoidally modulated sinusoid")
```



### Reference

1. [Pyplot.jl](https://github.com/JuliaPy/PyPlot.jl/issues/334)
2. [stackoverflow](https://stackoverflow.com/questions/46399480/julia-runtime-error-when-using-pyplot)

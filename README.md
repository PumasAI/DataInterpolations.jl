# DataInterpolations.jl

[![Build Status](https://travis-ci.org/PumasAI/DataInterpolations.jl.svg?branch=master)](https://travis-ci.org/PumasAI/DataInterpolations.jl)
[![codecov](https://codecov.io/gh/PumasAI/DataInterpolations.jl/branch/master/graph/badge.svg)](https://codecov.io/gh/PumasAI/DataInterpolations.jl)

DataInterpolations.jl is a library for performing interpolations of one-dimensional data. By
"data interpolations" we mean techniques for interpolating possibly noisy data, and thus
some methods are mixtures of regressions with interpolations (i.e. do not hit the data
points exactly, smoothing out the lines). This library can be used to fill in intermediate
data points in applications like timeseries data.

## Tutorial / Demonstration

A tutorial is included and can be found at [this page](https://htmlpreview.github.io/?https://github.com/UMCTM/DataInterpolations.jl/blob/master/Example/DataInterpolations.html). To run the tutorial yourself
locally, use the following Weave commands:

```julia
using Weave, DataInterpolations
weave(joinpath(dirname(pathof(DataInterpolations)), "../Example", "DataInterpolations.jmd"), out_path=:doc)
```

## API

All interpolation objects act as functions. Thus for example, using an interpolation looks like:

```julia
u = rand(5)
t = 0:4
interp = LinearInterpolation(u,t)
interp(3.5) # Gives the linear interpolation value at t=3.5
```

The indexing retreives the underlying values:

```julia
interp[4] # Gives the 4th value of u
```

## Available Interpolations

In all cases, `u` an `AbstractVector` of values and `t` is an `AbstractVector` of timepoints
corresponding to `(u,t)` pairs.

- `ConstantInterpolation(u,t)` - A piecewise constant interpolation.

- `LinearInterpolation(u,t)` - A linear interpolation.

- `QuadraticInterpolation(u,t)` - A quadratic interpolation.

- `LagrangeInterpolation(u,t,n)` - A Lagrange interpolation of order `n`.

- `QuadraticSpline(u,t)` - A quadratic spline interpolation.

- `CubicSpline(u,t)` - A cubic spline interpolation.

- `BSplineInterpolation(u,t,d,pVec,knotVec)` - An interpolation B-spline. This is a B-spline which hits each of the data points. The argument choices are:
  	- `d` - degree of B-spline  
  	- `pVec` - Symbol to Parameters Vector, `pVec = :Uniform` for uniform spaced parameters and `knotVec = :ArcLen` for parameters generated by chord length method.  
  	- `knotVec` - Symbol to Knot Vector, `knotVec = :Uniform` for uniform knot vector, `knotVec = :Average` for average spaced knot vector.

- `BSplineApprox(u,t,d,h,pVec,knotVec)` - A regression B-spline which smooths the fitting curve. The argument choices are the same as the `BSplineInterpolation`, with the additional parameter `h<length(t)` which is the number of control points to use, with smaller `h` indicating more smoothing.

- `Curvefit(u,t,m,p,alg)` - An interpolation which is done by fitting a user-given functional form `m(t,p)` where `p` is the vector of parameters. The user's input `p` is a an initial value for a least-square fitting, `alg` is the algorithm choice to use for optimize the cost function (sum of squared deviations) via `Optim.jl` and optimal `p`s are used in the interpolation.

## Plotting

DataInterpolations.jl is tied into the Plots.jl ecosystem, by way of RecipesBase.  
Any interpolation can be plotted using the `plot` command (or any other), since they have type recipes associated with them.

For convenience, and to allow keyword arguments to propagate properly, DataInterpolations.jl also defines several series types, corresponding to different interpolations.

The series types defined are:
- `:linear_interp`
- `:quadratic_interp`
- `:lagrange_interp`
- `:quadratic_spline`
- `:cubic_spline`

By and large, these accept the same keywords as their function counterparts.

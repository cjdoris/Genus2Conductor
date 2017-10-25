# EvenConductorExponent

A package for computing the conductor exponent of hyperelliptic curves of genus 2 defined over number fields at primes above 2, for the [MAGMA computer algebra system](http://magma.maths.usyd.edu.au/magma).

This implements the algorithm described in [this paper](https://arxiv.org/abs/1706.06162).

**If you find this package useful, please cite the above paper in any publications.**

Please direct any bugs or comments to the [issues page](https://github.com/cjdoris/EvenConductorExponent/issues).

## User Manual

### Installation

- This package is built on top of the [`ExactpAdics` package](https://cjdoris.github.io/ExactpAdics), so first you need to download and attach that.
- Either [download the latest release of this package](https://github.com/cjdoris/EvenConductorExponent/releases) or [copy the repository](https://github.com/cjdoris/EvenConductorExponent/releases).
- Attach the `spec_ExactpAdics` spec file (e.g. `AttachSpec("/path/to/this/package/spec_ExactpAdics")`; see [the MAGMA documentation](http://magma.maths.usyd.edu.au/magma/handbook/functions_procedures_and_packages)).

### Intrinsics

This package exports two intrinsics:

```
EvenConductorExponent_Genus2(C :: CrvHyp, p)
-> RngIntElt
```

The conductor exponent of `C` at `p`. `C` must be defined over a number field and be of genus 2. `p` must be a prime ideal of the base field of `C` above 2.

**Parameters.**
- `UseRegularModels:=false`: By default, the tame conductor exponent is computed from simple invariants of `C` if possible, but this doesn't always work. In this case, if the parameter is `true` then the tame exponent is computed from a regular model of `C`, but this can be extremely time consuming to compute, hence the default is `false`.
- `MaximumPrecision:=Infinity()`: A limit on the p-adic precision used in the algorithm. This is mainly for debugging purposes.

```
EvenConductorExponentData_Genus2(C :: CrvHyp, p)
-> Rec
```

Data about the conductor exponent of `C` at `p`. The arguments are as in the previous intrinsic.

The returned record includes the following fields:
- `Exponent`: The conductor exponent, the sum of the tame and wild exponents. If `UseRegularModels:=true` was not set, then this may not be assigned.
- `TameExponent`: The tame part of the conductor exponent. If `UseRegularModels:=true` was not set, then this may not be assigned; in this case, the tame exponent is 1, 2 or 3.
- `WildExponent`: The wild part of the conductor exponent.

**Parameters.** As in previous intrinsic.

### Verbosity

The `EvenConductorExponent_Genus2` verbosity flag can be enabled for information about the execution of the algorithm. Level 1 logs each stage of the algorithm. Level 2 provides more information, mainly for debugging.
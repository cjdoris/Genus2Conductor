# Genus2Conductor

A package for computing the conductor exponent of hyperelliptic curves of genus 2 defined over number fields, for the [MAGMA computer algebra system](http://magma.maths.usyd.edu.au/magma).

This implements the algorithm described in [this paper](https://arxiv.org/abs/1706.06162). MAGMA is already able to compute the odd part of the conductor (i.e. at primes not above 2) and the even part thanks to Ogg's formula in some cases, but not all. The even part may be computed assuming a functional equation for the L-function of the curve, but this is both conjectural and slow (its run-time is the square of the *global* conductor). The algoirithm implemented here depends mainly only on the local part of the conductor, and hence can be more practical, and most importantly the algorithm produces *proven* results.

**If you find this package useful, please cite the above paper in any publications.**

Please direct any bugs or comments to the [issues page](https://github.com/cjdoris/Genus2Conductor/issues).

## User Manual

### Installation

- This package is built on top of the [`ExactpAdics` package](https://cjdoris.github.io/ExactpAdics), so first you need to download and attach that.
- Either [download the latest release of this package](https://github.com/cjdoris/Genus2Conductor/releases) or [copy the repository](https://github.com/cjdoris/Genus2Conductor).
- Attach the `spec_ExactpAdics` spec file (e.g. `AttachSpec("/path/to/this/package/spec_ExactpAdics")`; see [the MAGMA documentation](http://magma.maths.usyd.edu.au/magma/handbook/functions_procedures_and_packages)).

### Intrinsics

```
Conductor_Genus2(C :: CrvHyp)
-> .
```

The conductor of `C`, a hyperelliptic curve defined over the rationals or a number field.

**Parameters.**
- `UseRegularModels:=true`: If necessary, a regular model will be computed in order to compute the tame part of the conductor. Occasionally, this step can take forever and so be skipped by setting this parameter to false. This can cause the intrinsic to raise an error if regular models were really needed.
- `UseOgg:=true`: Use Ogg's formula to compute conductor exponents, if possible.
- `MaximumPrecision:=Infinity()`: The underlying algorithm works with p-adic numbers up to some precision determined by this. Setting it to a finite value will sometimes cause the intrinsic to raise an error, but avoids a potential source of infinite loop. Mainly for debugging.

```
EvenConductorExponent_Genus2(C :: CrvHyp, p)
-> RngIntElt
```

The conductor exponent of `C` at `p`. `C` must be defined over the rationals or a number field and be of genus 2. `p` must be a prime ideal of the base field of `C` above 2.

**Parameters.** As in previous intrinsic.

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
- `UseRegularModels:=true`: Unlike the previous intrinsics, setting this to false will not cause an error to be raised. Instead, the `TameExponent` and `Exponent` fields of the returned record may not be set.

```
OddConductor(C :: CrvHyp)
-> .
```

The odd part of the conductor of `C`. This is provided for convenience to work in tandem with `EvenConductorExponentData_Genus2`.

### Verbosity

The `EvenConductorExponent_Genus2` verbosity flag can be enabled for information about the execution of the algorithm. Level 1 logs each stage of the algorithm. Level 2 provides more information, mainly for debugging.
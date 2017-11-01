# Genus2Conductor

A package for computing the conductor exponent of hyperelliptic curves of genus 2 defined over number fields, for the [MAGMA computer algebra system](http://magma.maths.usyd.edu.au/magma).

This implements the algorithm described in [this paper](https://arxiv.org/abs/1706.06162). MAGMA is already able to compute the odd part of the conductor (i.e. at primes not above 2) and the even part thanks to Ogg's formula in some cases, but not all. The even part may be computed assuming a functional equation for the L-function of the curve, but this is both conjectural and slow (its run-time is the square of the *global* conductor). The algoirithm implemented here depends mainly only on the local part of the conductor, and hence can be more practical, and most importantly the algorithm produces *proven* results.

**If you find this package useful, please cite the above paper in any publications.**

Please direct any bugs or comments to the [issues page](https://github.com/cjdoris/Genus2Conductor/issues).

## User Manual

**Contents**
- [Installation](#installation)
- [Example](#example)
- [Intrinsics](#intrinsics)
- [Verbosity](#verbosity)

### Installation

- Download [the `ExactpAdics` package](https://cjdoris.github.com/ExactpAdics/getting-started).
- Download [the latest release of this package](https://github.com/cjdoris/Genus2Conductor/releases/latest) or copy [the repository](https://github.com/cjdoris/Genus2Conductor).
- In MAGMA, attach the `spec` file from the `ExactpAdics` package and the `spec_ExactpAdics` file from this package. See [the example](#example) below or [the MAGMA documentation](http://magma.maths.usyd.edu.au/magma/handbook/text/24#173).

### Example

The following session verifies that the conductor of the [curve 15360.f.983040.2 in the LMFDB](http://www.lmfdb.org/Genus2Curve/Q/15360/f/983040/2) is 15360. It takes about a minute to execute.

```
> // you only need to do the following once per MAGMA session, or put it into your startup file
> AttachSpec("/path/to/ExactpAdics/package/spec");
> AttachSpec("/path/to/this/package/spec_ExactpAdics");
>
> // the following line is copied from the LMFDB entry
> R<x> := PolynomialRing(Rationals()); C := HyperellipticCurve(R![-30, 0, -37, 0, -15, 0, -2], R![]);
>
> // compute the conductor
> Conductor_Genus2(C);
15360
```

### Limitations

- The algorithm requires its input to be defined globally (i.e. over a number field) and therefore we cannot for example compute the conductor exponent of a curve defined over the 2-adics.
- The algorithm will run much slower if the coefficients of the defining polynomial are large. Compare about 1 minute for a curve defined with coefficients of a few digits to hours for a hundred digits per coefficient.

### Intrinsics

```
Conductor_Genus2(C :: CrvHyp)
-> .
```

The conductor of `C`, a hyperelliptic curve defined over the rationals or a number field.

**Parameters.**
- `UseRegularModels := true`: If necessary, a regular model will be computed in order to compute the tame part of the conductor. Occasionally, this step can take forever and so be skipped by setting this parameter to false. This can cause the intrinsic to raise an error if regular models were really needed.
- `UseOgg := true`: Use Ogg's formula to compute conductor exponents, if possible.
- `MaximumPrecision := Infinity()`: The underlying algorithm works with p-adic numbers up to some precision determined by this. Setting it to a finite value will sometimes cause the intrinsic to raise an error, but avoids a potential source of infinite loop. Mainly for debugging.

-----
```
EvenConductorExponent_Genus2(C :: CrvHyp, p)
-> RngIntElt
```

The conductor exponent of `C` at `p`. `C` must be defined over the rationals or a number field and be of genus 2. `p` must be a prime ideal of the base field of `C` above 2.

**Parameters.** As in previous intrinsic.

-----
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
- `UseRegularModels := true`: Unlike the previous intrinsics, setting this to false will not cause an error to be raised. Instead, the `TameExponent` and `Exponent` fields of the returned record may not be set.

-----
```
OddConductor(C :: CrvHyp)
-> .
```

The odd part of the conductor of `C`. This is provided for convenience to work in tandem with `EvenConductorExponentData_Genus2`.

### Verbosity

The `EvenConductorExponent_Genus2` verbosity flag can be enabled for information about the execution of the algorithm. Level 1 logs each stage of the algorithm. Level 2 provides more information, mainly for debugging.

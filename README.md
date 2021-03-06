# Genus2Conductor

A package for computing the conductor exponent of hyperelliptic curves of genus 2 defined over number fields, for the [MAGMA computer algebra system](http://magma.maths.usyd.edu.au/magma).

This implements the algorithm described in [this paper](https://arxiv.org/abs/1706.06162). MAGMA is already able to compute the odd part of the conductor (i.e. at primes not above 2) and the even part thanks to Ogg's formula in some cases, but not all. The even part may be computed assuming a functional equation for the L-function of the curve, but this is both conjectural and slow (its run-time is the square of the *global* conductor). The algoirithm implemented here depends mainly only on the local part of the conductor, and hence can be more practical, and most importantly the algorithm produces *proven* results.

**If you find this package useful, please cite the above paper in any publications.**

Please direct any bugs or comments to the [issues page](https://github.com/cjdoris/Genus2Conductor/issues).

**Contents**
- [User Manual](#user-manual)
  - [Installation](#installation)
  - [Example](#example)
  - [Intrinsics](#intrinsics)
  - [Verbosity](#verbosity)
- [Changelog](#changelog)

## User Manual

### Installation

- Download either (recommended) [the `ExactpAdics2` package](https://cjdoris.github.com/ExactpAdics2/getting-started) or [the `ExactpAdics` package](https://cjdoris.github.com/ExactpAdics/getting-started).
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
-> RngIntElt or RngOrdIdl
```

The conductor of `C`, a hyperelliptic curve defined over the rationals or a number field.

**Parameters.**
- `Proof := true`: By default, this algorithm produces proven results. When `false`, the output is not proven to be correct but it is exceedingly unlikely to be incorrect. Can run significantly quicker as a result. Can specify which proof method to use by passing a string: `"Local"` (the default) or `"Global"`. The global method is faster when the defining polynomial has small coefficients, and can half the run-time for curves over the rationals, but the benefits decrease as the degree of the number field grows; if the coefficients are not small, the local method is significantly quicker.
- `UseRegularModels := true`: If necessary, a regular model will be computed in order to compute the tame part of the conductor. Occasionally, this step can take forever and so be skipped by setting this parameter to false. This can cause the intrinsic to raise an error if regular models were really needed.
- `UseOgg := true`: Use Ogg's formula to compute conductor exponents, if possible (i.e. if the discriminant has valuation less than 12).
- `MaximumPrecision := Infinity()`: The underlying algorithm works with p-adic numbers up to some precision determined by this. Setting it to a finite value will sometimes cause the intrinsic to raise an error, but avoids a potential source of infinite loop. Mainly for debugging.
- `MobiusRandomize := 0`: By default, the algorithm first tries the identity Mobius transformation before trying ones with larger coefficients. Setting this to an integer greater than 0 will skip the identity and start with transformations with coefficients of size up to this. If the algorithm ever appears to be stuck at a factorization step, try setting this to a higher value &mdash; e.g. first try 2, then 3, and so on.

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
- `TameFixedDegree`: The degree of the subspace of the vector space of 3-torsion fixed by the inertia subgroup.
- `RamificationDegreeBound`: A divisor of the ramification degree of the field containing all 3-torsion points.
- `Error`: An error message, if something didn't work.
- `DUEs`: A sequence of triples `<d,u,e>` for each Galois-conjugacy-class of 3-torsion points, where `d` is the degree of (the field defined by) the class, `u` is the highest ramification break in the "upper" numbering of the field, `e` is the ramification degree of the field.
- `ExtraData`: further information about the inner part of the algorithm which computes fields containing each 3-torsion point (up to Galois conjugacy). Currently this is a record with the following fields:
  - `success`: `true` if it succeeded.
  - `err`: if `success` is false, an error message.
  - `seed`: the seed of the random number generator at the start.
  - `moebius`: the coefficients `[a,b,c,d]` of the Moebius transformation applied to the defining polynomial of the curve in order to get a nice Groebner basis.
  - `groebner_basis`: the Groebner basis of the equations defining 3-torsion.
  - `K`: the completion of the base field of the curve at the prime, so an extension of the 2-adic field.
  - `FtoK`: the embedding map from the base field of the curve into `K`.
  - `factors`: a sequence of records, one for each conjugacy class of 3-torsion, with the fields:
    - `b0`, `b1`, `b2`, `b3`, `c0`, `c1`, `c2`: co-ordinates of a 3-torsion point in the class.
    - `M`: the field they generate. The degree of `M` over `K`, summed over `factors`, is 80.
    - `L`: the field generated by `c0`, `c1` and `c2`, either index 1 or 2 in `M`. The degree of `L` over `K`, summed over `factors`, is 40.
    - `b3_fac`: the minimal polynomial of `b3` over `K`.
    - `c2_fac`: the minimal polynomial of `c2` over `L`.
    - `c2_fac_idx`: a unique label for each `c2_fac`, since each one can lead to multiple (1 or 2) factors.

**Parameters.** As in previous intrinsic.
- `UseRegularModels := true`: Unlike the previous intrinsics, setting this to false will not cause an error to be raised. Instead, the `TameExponent` and `Exponent` fields of the returned record may not be set.

-----
```
OddConductor(C :: CrvHyp)
-> RngIntElt or RngOrdIdl
```

The odd part of the conductor of `C`. This is provided for convenience to work in tandem with `EvenConductorExponentData_Genus2`.

### Verbosity

The `EvenConductorExponent_Genus2` verbosity flag can be enabled for information about the execution of the algorithm. Level 1 logs each stage of the algorithm. Level 2 provides more information, including timings, mainly for debugging.

## Changelog

{% include_relative CHANGELOG.md %}

## License

Copyright (C) 2018 Christopher Doris

Genus2Conductor is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

Genus2Conductor is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with Genus2Conductor.  If not, see http://www.gnu.org/licenses/.

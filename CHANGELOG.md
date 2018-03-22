### v1.2.1
- Compatibility fix with `ExactpAdics` package.

### v1.2.0
- Adds the global proof method, so now the `Proof` parameter can take the values `"Local"` (equivalent to `true`) and `"Global"`.
- The `UseOgg` parameter works over number fields now.
- Adds the `MobiusRandomize` parameter.
- We use the `UseNP` option to `Factorization`, since it's quicker.
- Removes sloping from a call to `IsHenselLiftable`, since it's quicker.

### v1.1.0
- Adds the `Proof` parameter.

### v1.0.1
- Removes dependencies on the standard library by copying a few functions out of it. (A better solution would be either a mechanism to `import` functions from the standard library in a platform-independent manner; even better, the functions we use would be intrinsics.)
- `OddConductor` over number fields was not filtering out the even primes.

### v1.0.0
- Initial release.

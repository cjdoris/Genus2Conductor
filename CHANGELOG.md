# Changelog

### v1.1.0

### v1.0.1
- Removes dependencies on the standard library by copying a few functions out of it. (A better solution would be either a mechanism to `import` functions from the standard library in a platform-independent manner; even better, the functions we use would be intrinsics.)
- `OddConductor` over number fields was not filtering out the even primes.

### v1.0.0
- Initial release.
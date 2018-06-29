## When to write an extension method

Make an extension method only when at least one of the following is true:
- [Your operation is very general](only-for-general-operations.md)
- [To promote SRP](promote-srp.md)
- [To hide advanced functionality](hide-advanced.md)
- [You don't have control of the code you wish to extend](no-control.md)
- [To avoid heavy breaking changes](breaking-changes.md)

Unless your use case falls into one of the above then _do not_ write an extension method. Create a standard method on the interface / class itself.
## Use an extension method to avoid breaking changes

If you want to change an interface, but:
 - it has many implementations which will need to be updated, or
 - it will break your consumers,
 it might be more appropriate to add an extension method to the interface.
## Use an extension method to avoid breaking changes

If you want to change an interface, but:
 - it has many implementations which will all need to be updated, or
 - it will break your consumers unacceptably,
 
 it might be more appropriate to add your new functionality as an extension to the interface.
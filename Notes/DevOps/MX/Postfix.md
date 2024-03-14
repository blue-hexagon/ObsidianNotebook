Configuration files are lazy-loaded.
Set a variable: `var = x`
Reference a variable: `newvar = $var`

```docs
Whenever you make a change to the [main.cf](https://www.postfix.org/postconf.5.html) or [master.cf](https://www.postfix.org/master.5.html) file, execute the following command as root in order to refresh a running mail system:

> # postfix reload
```

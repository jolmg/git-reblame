# git-reblame
Repeatedly calls git-blame to see full history of whatever you ask of git-blame

# Quick example

Using the gnupg repo for this example, here is a normal git-blame call:

```
$ git blame -sL '/^iobuf_writestr\b/,/^}/' iobuf.c
9ca4830a5b 2206) iobuf_writestr (iobuf_t a, const char *buf)
9ca4830a5b 2207) {
f2d75ac7dc 2208)   if (a->use == IOBUF_INPUT || a->use == IOBUF_INPUT_TEMP)
c5da750cf3 2209)     {
c5da750cf3 2210)       log_bug ("iobuf_writestr called on an input pipeline!\n");
c5da750cf3 2211)       return -1;
c5da750cf3 2212)     }
c5da750cf3 2213) 
a6d4bca3b5 2214)   return iobuf_write (a, buf, strlen (buf));
9ca4830a5b 2215) }
```

And here is that same call, but using git-**re**blame:

```
$ git reblame -sL '/^iobuf_writestr\b/,/^}/' iobuf.c
from commit: HEAD

9ca4830a5b 2206) iobuf_writestr (iobuf_t a, const char *buf)
9ca4830a5b 2207) {
f2d75ac7dc 2208)   if (a->use == IOBUF_INPUT || a->use == IOBUF_INPUT_TEMP)
c5da750cf3 2209)     {
c5da750cf3 2210)       log_bug ("iobuf_writestr called on an input pipeline!\n");
c5da750cf3 2211)       return -1;
c5da750cf3 2212)     }
c5da750cf3 2213) 
a6d4bca3b5 2214)   return iobuf_write (a, buf, strlen (buf));
9ca4830a5b 2215) }

from commit: f2d75ac7d~

9ca4830a5b 2153) iobuf_writestr (iobuf_t a, const char *buf)
9ca4830a5b 2154) {
c5da750cf3 2155)   if (a->use == IOBUF_INPUT)
c5da750cf3 2156)     {
c5da750cf3 2157)       log_bug ("iobuf_writestr called on an input pipeline!\n");
c5da750cf3 2158)       return -1;
c5da750cf3 2159)     }
c5da750cf3 2160) 
a6d4bca3b5 2161)   return iobuf_write (a, buf, strlen (buf));
9ca4830a5b 2162) }

from commit: c5da750cf~

9ca4830a5b 2109) iobuf_writestr (iobuf_t a, const char *buf)
9ca4830a5b 2110) {
a6d4bca3b5 2111)   return iobuf_write (a, buf, strlen (buf));
9ca4830a5b 2112) }

from commit: a6d4bca3b~

9ca4830a5b 2109) iobuf_writestr (iobuf_t a, const char *buf)
9ca4830a5b 2110) {
9ca4830a5b 2111)   int rc;
9ca4830a5b 2112) 
9ca4830a5b 2113)   for (; *buf; buf++)
9ca4830a5b 2114)     if ((rc=iobuf_writebyte (a, *buf)))
9ca4830a5b 2115)       return rc;
9ca4830a5b 2116)   return 0;
9ca4830a5b 2117) }

```

It doesn't just work from `HEAD`, you can pass it commit references just like how you'd do with git-blame:

```
$ git reblame -sL '/^iobuf_writestr\b/,/^}/' c5da750cf~2 -- iobuf.c
from commit: c5da750cf~2

9ca4830a5b 2109) iobuf_writestr (iobuf_t a, const char *buf)
9ca4830a5b 2110) {
a6d4bca3b5 2111)   return iobuf_write (a, buf, strlen (buf));
9ca4830a5b 2112) }

from commit: a6d4bca3b~

9ca4830a5b 2109) iobuf_writestr (iobuf_t a, const char *buf)
9ca4830a5b 2110) {
9ca4830a5b 2111)   int rc;
9ca4830a5b 2112) 
9ca4830a5b 2113)   for (; *buf; buf++)
9ca4830a5b 2114)     if ((rc=iobuf_writebyte (a, *buf)))
9ca4830a5b 2115)       return rc;
9ca4830a5b 2116)   return 0;
9ca4830a5b 2117) }

```

It provides full transparency of git-blame options, with the exception of `--help`, which it overrides.

Happy coding.

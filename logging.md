## Logging

Log into JSON: it's rich, structured and you can parse messages even if the structure changes somewhat. [Log to the standard output](https://12factor.net/logs) and let the execution environment pick it up.

It is generally discouraged to import the same 3rd party library in many different locations. A good practice to hide the dependency behind a local proxy, so you can change the implementation as you see fit.

The same is valid for logging. Pick a library - if you even need a library at all - and set it up behind your facade, so your modules keep importing `@you/logger`.

Try to use [two log levels](https://dave.cheney.net/2015/11/05/lets-talk-about-logging): debug and info. Debug is for developers only, do not enable it in production. Info is for analytics and auditing.

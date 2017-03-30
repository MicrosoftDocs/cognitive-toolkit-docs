Here's how to do it if you use `vim`; other editors should be similar
```
:set syntax=conf
```
This is not perfect but it's much better than no syntax highlighting. If you know how to write a `.vim` syntax file please send us a pull request.

In Emacs, `conf-mode` is best suited. To switch, press `M-x` to start entering commands, then enter `conf-mode`. On Windows Emacs, there is also `conf-windows-mode`, however that mode appears to sometimes get confused by special characters in comments.

Title: How to install Numba on Mac OS X
Date: 2016-01-25
Category: how-to
Tags: how-to, osx, python, numba, llvm

Hello!! I'm here to guide you guys on how to install [numba](http://numba.pydata.org/) on mac os x. Numba is a tool to speed up python applications with just annotations.

It should be easy as `pip install numba`, but I don't know why it does not solve all the dependencies, and I don't know why I spend so much time on it... :/

Numba depends on llvm [llvm](http://llvm.org/), a umbrella project that includes a bunch of compilers and low-level tools. You can install it with [Homebrew](http://brew.sh/).
```
$ brew install llvm
```

Then you need to link some env vars with `brew link` and create a symbolic link that is necessary to install [llvmlite](http://llvmlite.readthedocs.org/en/latest/) (a numba dependency).
```
$ brew link llvm
$ ln -s /usr/local/Cellar/llvm/<llvm_version>/include/llvm/Config/llvm-config.h config.h
```

You can check the llvm version installed with `brew info llvm`.

Finally, we can install numba. Remember to unlink llvm vars, so that it don't mess up with os x llvm.
```
$ pip install numba
$ brew unlink llvm
```

.o/


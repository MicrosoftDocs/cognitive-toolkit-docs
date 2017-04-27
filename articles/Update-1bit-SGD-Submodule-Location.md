Effective January 25, 2017 CNTK [1-bit Stochastic Gradient Descent (1bit-SGD)](./Enabling-1bit-SGD.md) and [BlockMomentumSGD](./Multiple-GPUs-and-machines#22-block-momentum-sgd) code is moved to a new Repository in GitHub.

If you cloned CNTK Repository with [1bit-SGD enabled](./Enabling-1bit-SGD.md) *prior to January 25, 2017* you need to update git submodule configuration as described below.

If you cloned CNTK Repository after January 25, 2017, or if you do not use 1bit-SGD Repository code, you may safely ignore information in this article.

## Updating 1bit-SGD Submodule information

In the instructions below we assume that the local copy of CNTK Repository is located at:

* `~/Repos/cntk` (Linux)
* `c:\repos\cntk` (Windows)

To update the Submodule information for 1bit-SGD do the following:

* Go to the root of your local Repository:

```cd ~/Repos/cntk``` (Linux)

or

```cd c:\repos\cntk``` (Windows)

* Checkout master branch and ensure it's up-to-date
```
git checkout master
git fetch
git pull
```

* Update Submodule information:

```git submodule sync```

Your code now should be in sync with the new 1bit-SGD location at GitHub.
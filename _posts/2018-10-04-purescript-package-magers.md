---
layout:     post
title:      A quick overview of Purescript package managers as of October 2018
date:       2018-10-04 20:14:00
published:  false
categories: purescript
---

In this post I want to give a veru quick overview of Purescript's package managers and some tools related to them.

I'll go through:

- bower
- psc-package
- spacchetti
- spacchetti-cli

## Bower

Coming from the JavaScript world, [Bower](https://bower.io/) is certainly the most familiar to use. 
It's the default package manager used with `pulp init`, and all the familiar usual commands work, e.g.

```bash
mkdir foo
cd foo
pulp init
bower i
bower i --save purescript-simple-json
bower i --save-dev purescript-quickcheck
```

Sadly, Bower is not the perfect tool to use with Purescript, and I think I realized this mainly during the switch 
from Purescript v0.11 to v0.12.
Figuring out the correct packages versions that have to go with the compiler version currently in use can get annoying 
and a source of many mistakes.


Luckily, the problem was already solved by psc-package!

It's important to note that currenly using bower for dependencies is the only way to publish a package on [Pursuit](https://pursuit.purescript.org/).

## Psc-package

Psc-package uses package sets to manage dependencies. This means that we don't have to worry too much about matching versions. 
We can just pick the right set, and then happily install all the packages we need!

```bash
pulp --psc-package init
psc-package install
psc-package install simple-json
```

There's a nice bonus in that we can drop the prefix `purescript-` from packages names too!

I'm not going to write more about it, as there's already a nice [guide](https://psc-package.readthedocs.io/en/latest/).

Sadly, not even psc-package is perfect: using packages that are not included in the default sets requires some work.
Basically the [package-sets](https://github.com/purescript/package-sets) repository has to be forked and then modified together
with a new tag. Then this new set can be used in our projects.

## Spacchetti

[Spacchetti](https://github.com/justinwoo/spacchetti) brings the power of Dhall (a programmable configuration language) to
improve the psc-package experience.
Two of the problems of extending psc-package's package sets are having to deal with manually editing a large JSON file, 
and some messy and not very useful git diffs.

Spachetti is an alternate psc-package package set, but with super powers. The JSON is generated using Dhall, and there
are a few scripts to automate some tasks, e.g. one is used to add a package automatically from bower.

The workflow to add packages is essentially the same as with psc-package: fork spacchetti's repo, make the changes you need,
add a tag, and then use it in your project.

There's a very nice [guide](https://spacchetti.readthedocs.io/en/latest/why-dhall.html) that you can read to learn more.

## Spacchetti-cli

[Spacchetti-cli](https://github.com/justinwoo/spacchetti-cli) is a new tool which adds some functionalities to spacchetti,
and has the potential to make our life even easier.

Right now it only has two commands: `local-setup` and `insdhall` (ugh).

With these it becomes very easy to add additional packages to a spacchetti set without having to go through all the procedure of
forking, modifying etc.

We can just define the basic set and the eventual changes or additions we want and spacchetti-cli will thing about everything else
(well, almost).

You can read more about it [here](https://spacchetti.readthedocs.io/en/latest/local-setup.html).







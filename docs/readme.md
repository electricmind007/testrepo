# CEDP Documentation

This documentation of the Cognitive Enterprise Data Platform
covers the technical aspects of CEDP including architecture and
infrastructure. It is intended as a guide for developers and engineers
that are working on CEDP itself or CEDP related projects. It describes
both the current state of CEDP as well as planned extensions.
The documentation is not a user manual, although
some information about usage can be found here.

## Dynamic Nature of the Documentation

The documentation evolves together with CEDP and is therefore hosted in the same repository as the source code.

## Documentation Implementation

The documentation uses the [gitbook](https://www.gitbook.com/) approach that is supported by IBM's GitHub infrastructure. Documents are written in [Markdown](https://guides.github.com/features/mastering-markdown/) and compiled into HTML (or PDF) using the [gitbook toolchain](https://www.gitbook.com/book/gitbookio/docs-toolchain/details). The results are checked into the CEDP repository into branch [gh-pages](https://github.ibm.com/cognitive-data-platform/cognitive-data-platform/tree/gh-pages). This branch must not be merged in to the master branch.

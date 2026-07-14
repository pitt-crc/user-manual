# CRCD User Manual

Source code for the official CRCD user manual.

## Quick Start

Clone and enter the repository.

```bash
git clone git@github.com:pitt-crc/user-manual.git && \
cd user-manual
```

Ensure you have [Pixi](https://pixi.prefix.dev/latest/) available and install the documentation dependencies with the following command.

```bash
pixi install
```

To generate a live preview locally, use the `serve` task:

```bash
pixi run serve
```

A new version of the documentation is built and pushed to production every time the main branch is updated.

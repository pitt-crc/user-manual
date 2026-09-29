# CRCD User Manual

Source code for the official CRCD user manual.

## Quick Start

Start by installing the project dependencies:

```bash
pip install -r requirements.txt
```

To generate a live preview locally, use the `serve` command:

```bash
zensical serve
```

To build the site into `site/`:

```bash
zensical build
```

The site is configured in `zensical.toml`. A new version of the documentation is
built and pushed to production every time the main branch is updated.

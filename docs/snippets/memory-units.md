!!! info "Reading the memory size in a partition name"
    A partition's name tells you roughly how much memory each **core** carries. On
    **`smp_8GB`**, for example, every core comes with about **8 GB** of RAM, so a
    4-core job has roughly 32 GB available. Use the number to *choose* a tier that
    fits your memory-per-core needs.

    That number is a base-10 gigabyte (GB) figure, rounded for readability. It is a
    **selection aid — not a value to type into a job request**:

    - **Slurm counts in base-2.** In `--mem`, `--mem-per-cpu`, and in billing, the
      suffixes `K`/`M`/`G`/`T` are multiples of **1024**. Slurm's `G` therefore means
      a *gibibyte* (GiB = 1024 MB), and Slurm has **no base-10 `GB` unit** — a `GB`
      suffix is not valid.
    - **Don't put the tier number in `--mem-per-cpu`.** On `smp_8GB`,
      `--mem-per-cpu=8G` requests 8 GiB (8192 MB) per core — *more* than the node
      provides — so Slurm adds cores to cover it (raising your bill) or leaves the
      job pending.
    - **Easiest, and recommended:** don't set memory at all. Each tier already gives
      every core its share of memory automatically, and your job is billed on cores.
      Just request the cores you need.
    - **If you must cap memory,** give it in megabytes, e.g. `--mem-per-cpu=8000`, or
      take the whole node with `--mem=0`.

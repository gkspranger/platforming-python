# Platforming Python

In today's fast-evolving technical landscape, Python has emerged as a dominant language due to its flexibility, ease of use, and extensive library ecosystem. However, hosting Python in unconventional environments, such as the z/OS mainframe, presents unique challenges and opportunities. This document delves into innovative approaches for platforming Python on z/OS to achieve flexibility, predictability, and isolation in a large, shared environment.

## Hosting the Python Runtime

1. **Idea:** Immutable Runtimes

- Install the runtime, and never touch it again - other than to remove (maybe)

- A runtime patch/PTF will (almost) 100% of the time be a new Python version

  - e.g. Current version `3.11.2` would be patched to `3.11.3`

  - New versions bring new features (bugs) and should be deployed in isolation

2. **Idea:** "Support" Multiple Runtimes

- You've heard `N - 1` ?? How about `N - 10` !!

  - Get use to the phrase "available, but not supported"

- With an interpreted runtime, it is unreasonable to expect all Python applications to be on the same version

  - Everyone would need to "jump" at once

    - Has all of your systems' modules been compiled/linked with an actively supported compiler ??

  - "Abandonware" is a legitimate business decision

    - An opportunity is identified

    - A solution with limited life expectancy is created

    - Any additional cycles beyond the life expectancy continues to add value, but not enough for maintenance

- **Goal:** Highly predictable deployments

  - In a platform, developers explicitly choose the desired runtime

    ```docker
    FROM python:3.10.12
    ```

  - Dynamically changing runtimes are unpredictable

    ```bash
    # 1/1/2025
    $ python --version
    Python 3.10.12 👍

    # 4/1/2025
    $ python --version
    Python 3.11.2 💥
    ```

- **Helper:** Provide (immutable) shortcuts to (immutable) runtimes with expected environment variables pre-baked

  - `/opt/cpy/3.10.12`

  ```bash
  export PATH=/path/to/cpy/3.10.12/bin:$PATH
  export LIBPATH=/path/to/cpy/3.10.12/lib:$LIBPATH
  export _BPXK_AUTOCVT='ON'
  export _CEE_RUNOPTS='FILETAG(AUTOCVT,AUTOTAG) POSIX(ON)'
  export _TAG_REDIR_ERR=txt
  export _TAG_REDIR_IN=txt
  export _TAG_REDIR_OUT=txt

  exec /path/to/cpy/3.10.12/bin/python "$@"
  ```

## Managing Python Packages

1. **Idea:** Never install "global" site packages

- AKA :: system-site packages

- Goal is to NOT dirty the host

2. **Idea:** Only ship packages to the build environment

- Promoting packages across multiple environments is an unmanageable mess

3. **Idea:** `venv` is allowed for development, but discouraged as being part of an "application"

4. **Idea:** We should encourage and support custom Python packages

## Creating Application Artifacts

1. **Idea:** Application artifacts should contain all custom code needed dependencies

## Deploying and Releasing Artifacts

1. **Idea:** Deploying an application artifact, is not the same as releasing an artifact

2. **Idea:** Support manifests as a source of record for releases

  - Allows for declarative state management and continuous reconciliation

  ```
  release:
    dev: 2024.12.13-01
    qa: 2024.12.13-01
    prod: 2024.11.25-07
  ```

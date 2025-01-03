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

- **Goal:** Highly predictable deployments

  - In a platform, developers explicitly choose the desired runtime

- Get use to the phrase "available, but not supported"

- **Admission:** We are in a "battle" with Docker, K8s, Serverless, Cloud Foundry, etc. - whether we admit it or not

  ```docker
  FROM python:3.11.1
  ```

- With an interpreted runtime, it is unreasonable to expect all Python applications to be on the same version

  - Everyone would need to "jump" at once

  - Are all of your systems' compiled/linked COBOL modules using the latest COBOL compiler ??

  - "Abandonware" is a legitimate business decision

- **Helper:** Provide (immutable) shortcuts to (immutable) runtimes with expected environment variables pre-baked

  - `/opt/cpy/3.11.1`

  ```bash
  export PATH=/path/to/cpy/3.11.1/bin:$PATH
  export LIBPATH=/path/to/cpy/3.11.1/lib:$LIBPATH
  export _BPXK_AUTOCVT='ON'
  export _CEE_RUNOPTS='FILETAG(AUTOCVT,AUTOTAG) POSIX(ON)'
  export _TAG_REDIR_ERR=txt
  export _TAG_REDIR_IN=txt
  export _TAG_REDIR_OUT=txt
  ```

## Managing Python Packages

1. **Idea:** Never install system site packages

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

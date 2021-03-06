# [chartpress](https://github.com/jupyterhub/chartpress)

[![PyPI](https://img.shields.io/pypi/v/chartpress.svg)](https://pypi.python.org/pypi/chartpress)
[![Build Status](https://travis-ci.org/jupyterhub/chartpress.svg?branch=master)](https://travis-ci.org/jupyterhub/chartpress)

Chartpress automate basic Helm chart development work. It is tightly used in development of the [JupyterHub](https://github.com/jupyterhub/zero-to-jupyterhub-k8s) and [BinderHub](https://github.com/jupyterhub/binderhub) Helm charts.

## Features

Chartpress can do the following with the help of some configuration.

- Update Chart.yaml's version appropriately
- Build docker images and tag them appropriately
- Push built images to a docker iamge repository
- Update values.yaml to reference the built images
- Publish a chart to a Helm chart registry based on GitHub pages
- Reset changes to Chart.yaml and values.yaml

## Requirements

The following binaries must be in your `PATH`:
- [git](https://www.git-scm.com/downloads)
- [docker](https://docs.docker.com/install/#supported-platforms)
- [helm](https://helm.sh/docs/using_helm/#installing-helm)

If you are publishing a chart to GitHub Pages create a `gh-pages` branch in the
destination repository.

## Installation

```
pip install chartpress
```

## Usage

In a directory containing a `chartpress.yaml`, run:

    chartpress

to build your chart(s) and image(s). Add `--push` to publish images to docker
hub and `--publish-chart` to publish the chart and index to gh-pages.

```
usage: chartpress [-h] [--push] [--force-push] [--publish-chart]
                  [--extra-message EXTRA_MESSAGE] [--tag TAG | --long]
                  [--image-prefix IMAGE_PREFIX] [--reset]
                  [--skip-build | --force-build] [--version]
                  [--commit-range COMMIT_RANGE]

Automate building and publishing helm charts and associated images. This is
used as part of the JupyterHub and Binder projects.

optional arguments:
  -h, --help            show this help message and exit
  --push                Push built images to their image registries, but not
                        if it would replace an existing image.
  --force-push          Push built images to their image registries,
                        regardless if it would replace an existing image.
  --publish-chart       Package a Helm chart and publish it to a Helm chart
                        repository contructed with a GitHub git repository and
                        GitHub pages.
  --extra-message EXTRA_MESSAGE
                        Extra message to add to the commit message when
                        publishing charts.
  --tag TAG             Explicitly set the image tags and chart version.
  --long                Use this to always get a build suffix for the
                        generated tag and chart version, even when the
                        specific commit has a tag.
  --image-prefix IMAGE_PREFIX
                        Override the configured image prefix with this value.
  --reset               Skip image build step and reset Chart.yaml's version
                        field and values.yaml's image tags. What it resets to
                        can be configured in chartpress.yaml with the resetTag
                        and resetVersion configurations.
  --skip-build          Skip the image build step.
  --force-build         Enforce the image build step, regardless of if the
                        image already is available either locally or remotely.
  --version             Print current chartpress version and exit.
  --commit-range COMMIT_RANGE
                        Deprecated: this flag will be ignored. The new logic
                        to determine if an image needs to be rebuilt does not
                        require this. It will find the time in git history
                        where the image was last in need of a rebuild due to
                        changes, and check if that build exists locally or
                        remotely already.
```

## Configuration

A `chartpress.yaml` file contains a specification of charts and images to build
for each chart. Below is an example `chartpress.yaml` file.

```yaml
charts:
  # list of charts by name
  # each name should be a directory containing a helm chart
  - name: binderhub
    # the prefix to use for built images
    imagePrefix: jupyterhub/k8s-
    # tag to use when resetting the chart values
    # with the --reset flag. It defaults to "set-by-chartpress".
    resetTag: latest
    # version to use when resetting the Chart.yaml's version field with the
    # --reset flag. It defaults to "0.0.1-set.by.chartpress". This is a valid
    # SemVer 2 version, which is required for a helm lint command to succeed.
    resetVersion: 1.2.3
    # The git repo whose gh-pages contains the charts. This can be a local
    # path such as "." as well but if matching <organization>/<repo> will be
    # assumed to be a separate GitHub repository.
    repo:
      git: jupyterhub/helm-chart
      published: https://jupyterhub.github.io/helm-chart
    # Additional paths that when modified should lead to an updated Chart.yaml
    # version, other than the chart directory in <chart name> or any path that
    # influence the images of the chart. These paths should be set relative to
    # chartpress.yaml's directory.
    paths:
      - ../setup.py
      - ../binderhub
    # images to build for this chart (optional)
    images:
      binderhub:
        # Template docker build arguments to be passed using docker's
        # --build-arg flag as --build-arg <key>=<value>. Available dynamic
        # values are TAG and LAST_COMMIT.
        buildArgs:
          MY_STATIC_BUILD_ARG: "hello world"
          MY_DYNAMIC_BUILD_ARG: "{TAG}-{LAST_COMMIT}"
        # contextPath is the path to the directory that is to be considered the
        # current working directory during the build process of the Dockerfile.
        # This is by default the folder of the Dockerfile. This path should be
        # set relative to chartpress.yaml.
        contextPath: ..
        # Path to the Dockerfile, relative to chartpress.yaml. Defaults to
        # "images/<image name>/Dockerfile".
        dockerfilePath: images/binderhub/Dockerfile
        # Path(s) in <chart name>/values.yaml to be updated with image name and
        # tag.
        valuesPath:
          - singleuser.image
          - singleuser.profileList.0.kubespawner_override.image
        # Additional paths, relative to chartpress.yaml's directory, that should
        # be used to indicate that a new tag of the image is required, aside
        # from the contextPath and dockerfilePath for building the image itself.
        paths:
          - assets
```

## Caveats

### TravisCI mirror image registry

If you run chartpress on TravisCI, its logic can be fooled by a mirror image
registry to rebuild something that didn't need rebuilding. A workaround for this
can be found in this repo's [.travis.yml](.travis.yml).

### Shallow clones

Chartpress detects the latest commit which changed a directory or file when
determining the version and tag to use for charts and images. This means that
shallow clones should not be used because if the last commit that changed a
relevant file is outside the shallow commit range, the wrong tag will be
assigned.

TravisCI uses a clone depth of 50 by default, which can result in incorrect
image tagging. You can [disable this shallow clone
behavior](https://docs.travis-ci.com/user/customizing-the-build/#Git-Clone-Depth)
in your `.travis.yml`:

```yaml
git:
  depth: false
```

## Development

Testing of this python package can be done using
[`pytest`](https://github.com/pytest-dev/pytest). For more details on the
testing, see [tests/README.md](tests/README.md).

```bash
# install chartpress locally
pip install  -e .

# install dev dependencies
pip install -r dev-requirements.txt

# run tests
pytest --verbose --flakes --exitfirst
```

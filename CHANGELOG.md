# Changelog for chartpress

## Unreleased

## 0.4

### 0.4.3 (Breaking changes)

0.4.3 contains important bug fixes for versions `0.4.0` to `0.4.2`. A big bug
fixed was that charts published using `--publish-chart` replaced previous charts
in the helm chart repositories' `index.yaml` file that only differed by a SemVer
2 compliant build suffix like `+001.asdf123`. The bugfixes introduced in this
release avoid this issue, caused by a bug in helm, by using a build suffix of
`.001.asdf123` instead - a breaking change.

Example versions to expect in this release and onwards are given below where
some commits were made in between git tagged (`0.1.0` and `0.2.0-beta.1`)
commits.

```
# without --long
0.1.0
0.1.0-002.sdfg234
0.2.0-beta.1
0.2.0-beta.1.003.asdf123

# with --long
0.1.0-000.qwer123
0.1.0-002.sdfg234
0.2.0-beta.1.000.wert234
0.2.0-beta.1.003.asdf123
```

- Fix latest tagged commit
  [#66](https://github.com/jupyterhub/chartpress/pull/66)
  ([@minrk](https://github.com/minrk))
- Fix bugs: index merge, image tag, g prefix, ignored tags
  [#64](https://github.com/jupyterhub/chartpress/pull/64)
  ([@consideRatio](https://github.com/consideRatio))
- Support `valuesPath` pointing to a single `image:tag` string in addition to a
  dict with separate `repository` and `tag` keys
  [#63](https://github.com/jupyterhub/chartpress/pull/63)
  ([@minrk](https://github.com/minrk)).
- Support lists in `valuesPath` by using integer indices
  [#65](https://github.com/jupyterhub/chartpress/pull/65)
  ([@minrk](https://github.com/minrk)), e.g. `section.list.1.image` for the
  yaml:
  ```yaml
  section:
    list:
      - first: item
        image: "not set"
      - second: item
        image: "image:tag"  #  <--sets this here
  ```

### 0.4.2 (broken)

- --long flag to always output build information in image tags and chart version [#57](https://github.com/jupyterhub/chartpress/pull/57) ([@consideRatio](https://github.com/consideRatio))
- Refactor publish_pages for comprehensibility [#56](https://github.com/jupyterhub/chartpress/pull/56) ([@consideRatio](https://github.com/consideRatio))

### 0.4.1 (broken)

- Deprecate --commit-range [#55](https://github.com/jupyterhub/chartpress/pull/55) ([@consideRatio](https://github.com/consideRatio))
- Reset Chart.yaml's version to a valid value [#54](https://github.com/jupyterhub/chartpress/pull/54) ([@consideRatio](https://github.com/consideRatio))
- Don't append +build on tagged commits [#53](https://github.com/jupyterhub/chartpress/pull/53) ([@consideRatio](https://github.com/consideRatio))

### 0.4.0 (broken)

- Chart and image versioning, and Chart.yaml's --reset interaction [#52](https://github.com/jupyterhub/chartpress/pull/52) ([@consideRatio](https://github.com/consideRatio))
- Add --version flag [#45](https://github.com/jupyterhub/chartpress/pull/45) ([@consideRatio](https://github.com/consideRatio))

## 0.3

### 0.3.2

- Update chartpress --help output in README.md [#42](https://github.com/jupyterhub/chartpress/pull/42) ([@consideRatio](https://github.com/consideRatio))
- Add initial setup when starting from scratch [#36](https://github.com/jupyterhub/chartpress/pull/36) ([@manics](https://github.com/manics))
- avoid mangling of quotes in rendered charts (#1) [#34](https://github.com/jupyterhub/chartpress/pull/34) ([@rokroskar](https://github.com/rokroskar))
- Add --skip-build and add --reset to reset image tags as well as chart version [#28](https://github.com/jupyterhub/chartpress/pull/28) ([@rokroskar](https://github.com/rokroskar))

### 0.3.1

- Fix conditionals for builds with new tagging scheme,
  by checking if images exist locally or on the registry
  rather than assuming the correct tag was pushed based on commit range.
- Echo shell commands that are executed during the chartpress process

### 0.3.0

- Add chart version as prefix to image tags (e.g. 0.8-abc123)
- Fix requires-python metadata to specify that Python 3.6 is required

## 0.2

### 0.2.2

- Another ruamel.yaml type fix

### 0.2.1

- Add `--image-prefix` option
- Workaround ruamel.yaml bug when strings are all-digits
  and start with 0 and contain an 8 or 9.
- Fix type checking for recent ruamel.yaml

### 0.2.0

- Fix image tagging when building multiple images
- Make image-building optional
- Show changes being made
- Support GITHUB_TOKEN env for pushing to gh-pages
- Include chartpress.yaml when resolving last changed ref
- Update only necessary fields

## 0.1

### 0.1.1

- Add missing dependency on ruamel.yaml

### 0.1.0

first release!

# CI and releases

See discussions about these topics in [filebrowser/filebrowser](https://github.com/filebrowser/filebrowser/) issues [#399](https://github.com/filebrowser/filebrowser/pull/399),
[#450](https://github.com/filebrowser/filebrowser/issues/450), and [#492](https://github.com/filebrowser/filebrowser/pull/492).

Currently, a single CI service is used: [travis-ci.com/filebrowser](https://travis-ci.com/filebrowser).


## Release procedure

- Manually tag the commit that is to be used in [filebrowser/frontend](https://github.com/filebrowser/frontend).

> NOTE: See [semver.org](https://semver.org/).

- Execute `./build/release.sh $semver` in [filebrowser/filebrowser](https://github.com/filebrowser/filebrowser). It will:
  - Check if the tag exists in [filebrowser/frontend](https://github.com/filebrowser/frontend).
  - Update the submodule to the tag.
  - Replace `untracked` with the semver/tag in `filebrowser.go` (through `sed`).
  - Tag [filebrowser/filebrowser](https://github.com/filebrowser/filebrowser).
  - Revert the semver/tag in change in `filebrowser.go` and commit again.

> NOTE: `release.sh` depends on `git` and requires `frontend` to be a submodule of `filebrowser`.

- When the tag in [filebrowser/filebrowser](https://github.com/filebrowser/filebrowser) is pushed, [travis-ci](https://travis-ci.com/filebrowser/filebrowser/builds) detects it and two of the following stages (`0,1` or `0,2`) are automatically executed (see [`.travis.yml`](https://github.com/filebrowser/filebrowser/blob/master/.travis.yml)):
  - STAGE 0: golang linting tools ([gometalinter](https://github.com/alecthomas/gometalinter)) are executed. See [`build/run_gometalinter.sh`](https://github.com/filebrowser/filebrowser/blob/master/build/run_gometalinter.sh) and [`.gometalinter.json`](https://github.com/filebrowser/filebrowser/blob/master/.gometalinter.json).
  - STAGE 1: the frontend and the backend are built, thus `rice-box.go` is created. See [`build/build_all.sh`](https://github.com/filebrowser/filebrowser/blob/master/build/build_all.sh).
    - If the commit is not tagged, docker image `filebrowser/filebrowser` is built and it is pushed to [hub.docker.com/r/filebrowser/filebrowser](https://hub.docker.com/r/filebrowser/filebrowser/). See [`build/build_img.sh`](https://github.com/filebrowser/filebrowser/blob/master/build/build_img.sh).
  - STAGE 2: if the commit is tagged,
    - [goreleaser](https://github.com/goreleaser/goreleaser) is used to build the release artifacts for all the supported platforms and to build a single docker image (tagged/named twice). The artifacts are published to [GitHub Releases](https://github.com/filebrowser/filebrowser/releases) and the image is pushed to [hub.docker.com/r/filebrowser/filebrowser](https://hub.docker.com/r/filebrowser/filebrowser/). See [`.goreleaser.yml`](https://github.com/filebrowser/filebrowser/blob/master/.goreleaser.yml).
    - [`./build/push_ricebox.sh`](https://github.com/filebrowser/filebrowser/blob/master/build/push_ricebox.sh) is executed in order to update `rice-box.go` in [filebrowser/caddy/assets](https://github.com/filebrowser/caddy/tree/master/assets).
      - [filebrowser/caddy](https://github.com/filebrowser/caddy) is cloned.
      - A branch named `update-rice-box` is created from `master`.
      - `assets/rice-box.go` is replaced.
      - A commit is added.
      - The commit is tagged.
      - The branch and tag are pushed to [filebrowser/caddy](https://github.com/filebrowser/caddy).

> NOTE: A maintainer is required to merge/squash/rebase branch `update-rice-box` in [filebrowser/caddy](https://github.com/filebrowser/caddy) to `master`.

> HELP: a PR could be automatically created by using the GitHub REST API: https://developer.github.com/v3/pulls/#create-a-pull-request. However, even though creating the PR is quite easy, we don't know how to properly handle the authentication: https://developer.github.com/v3/auth/. Should be willing to help us, please [let us know](https://github.com/filebrowser/filebrowser/issues/new).

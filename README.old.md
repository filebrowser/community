# File Browser Community

This repository contains guides, snippets, hints, references, etc. to help the users and contributors of [File Browser](filebrowser.github.io).

> If you want to contribute to these docs, please open an [issue](https://github.com/filebrowser/community/issues) or a [Pull Request (PR)](https://github.com/filebrowser/community/compare)!

# [Welcome!](./code_of_conduct.md)

File Browser is a free and open source software brought to you with :heart: by [@hacdias](https://github.com/hacdias) and [contributors](https://github.com/filebrowser/filebrowser/graphs/contributors). So, first off, thanks for taking the time to contribute. We need all the help we can get :thumbsup:

- Found a bug? Open either a [bug report](https://github.com/filebrowser/filebrowser/issues/new?template=bug_report.md) or a [Caddy-related bug report](https://github.com/filebrowser/filebrowser/issues/new?template=caddy_bug_report.md).
- Got a feature idea? Open a [feature request](https://github.com/filebrowser/filebrowser/issues/new?template=feature_request.md).
- Want to contribute modifications to the codebase? Please, read [Project structure](#structure), [Builds](./builds.md) and [Development](./development.md). Then open a [Pull Request (PR)](https://github.com/filebrowser/filebrowser/compare/).
- Spread the word. Talk to your friends and colleagues about how awesome File Browser is!

<a name="structure"></a>
# Project structure

This project is composed of two main repositories:

- The backend hosted at [filebrowser/filebrowser](https://github.com/filebrowser/filebrowser) is entirely written in [go(lang)](https://golang.org/).
- The frontend hosted at [filebrowser/frontend](https://github.com/filebrowser/frontend) is written with the [Vue.js](https://vuejs.org/) framework to produce [JavaScript](https://en.wikipedia.org/wiki/JavaScript), [CSS](https://en.wikipedia.org/wiki/Cascading_Style_Sheets) and [HTML](https://www.w3.org/html/).

This modular approach is meant to make contributing easier for users willing to focus on/reuse only some piece, instead of being forced to analyze the whole project. However, due to the tight coupling required by some features, basic knowledge of both `golang` and `Vue.js` is recommended. See [golang/go/wiki/Learn](https://github.com/golang/go/wiki/Learn) and [vuejs.org/v2/guide](https://vuejs.org/v2/guide/index.html).

This structure is also valuable for third parties to develop alternative implementations of any of the pieces. E.g. a different frontend can be written using Angular/AngularJS, but keeping the same backend. Equally, a different backend can be written using Python or Ruby, but keeping the same frontend.

Furthermore, File Browser can be used as a middleware for an app. Indeed, it is available as a plugin for [Caddy](https://caddyserver.com/). See [caddyserver.com/docs/http.filemanager](https://caddyserver.com/docs/http.filemanager) and [filebrowser/filebrowser/tree/master/caddy](https://github.com/filebrowser/filebrowser/tree/master/caddy). The codebase that plugs/embeds Filebrowser into Caddy is developed in [filebrowser/caddy](https://github.com/filebrowser/caddy). Moreover, the procedure to update `assets/rice-box.go` there is explained in [CI and release](./ci_releases.md).

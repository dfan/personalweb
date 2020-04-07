# davidfan.io
My personal [web site](http://www.davidfan.io), generated using [Hugo](https://gohugo.io/), a modern static site generator written in Go.
Deployment is through ``/docs`` folder in ``master`` branch.

Installation and Development:
1. Install hugo: ``brew install hugo``
2. Run local development server: ``hugo server``
3. Build static site into ``/docs`` folder before pushing new changes: ``hugo`` (directory is configured by default in the yaml)

I use ```mediumexporter```, a [NPM package](https://www.npmjs.com/package/mediumexporter) to convert Medium posts into markdown for the blog. Sample usage: `mediumexporter yoururl > yourfile.md`.

# davidfan.io
My personal [web site](http://www.davidfan.io), generated using [Hugo](https://gohugo.io/), a modern static site generator written in Go.
Deployment is through ``/docs`` folder in ``master`` branch. Credit to [naro143](https://github.com/naro143/hugo-coder-portfolio) for the theme.

Installation and Development:
1. Install hugo:
(We need to use the version of Hugo from December 2018 since the new updates seem to break the website.)

```
wget https://github.com/gohugoio/hugo/releases/download/v0.53/hugo_0.53_macOS-64bit.tar.gz -0 ~/Downloads
tar -xvzf ~/Downloads/hugo_0.53_macOS-64bit.tar.gz -C ~/Downloads
cp ~/Downloads/hugo_0.53_macOS-64bit/hugo /usr/bin/
npm install -g less uglifycss```
2. Run local development server: ``hugo server``
3. Compile `.less` files into CSS: ``make build``
4. Build static site into ``/docs`` folder before pushing new changes: ``hugo`` (directory is configured by default in the yaml)

I use ```mediumexporter```, a [NPM package](https://www.npmjs.com/package/mediumexporter) to convert Medium posts into markdown for the blog. Sample usage: `mediumexporter yoururl > yourfile.md`.

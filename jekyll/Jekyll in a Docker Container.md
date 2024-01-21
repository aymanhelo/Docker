
# Jekyll in a Docker Container

## Docker Images

| Image | Purpose | Example |
| ----- | ------- | ------- |
| [bretfisher/jekyll](https://hub.docker.com/r/bretfisher/jekyll/) | Runs Jekyll by default with no options, good for general CLI commands | `docker run -v $(pwd):/site bretfisher/jekyll new .` |
| [bretfisher/jekyll-serve](https://hub.docker.com/r/bretfisher/jekyll-serve/) | Runs Jekyll serve with sane defaults, good for local Jekyll site dev | `docker run -p 4000:4000 -v $(pwd):/site bretfisher/jekyll-serve` |

## Getting Started

Creating a site:

```shell
cd to empty directory
docker run -v $(pwd):/site bretfisher/jekyll new .
```

Start a local server with sane defaults listening on port 4000:

```shell
cd dir/of/your/jekyll/site
docker run -p 4000:4000 -v $(pwd):/site bretfisher/jekyll-serve
```

That's it!

Details: it will mount your current path into the containers `/site`, `bundle install` before running
`jekyll serve` to , serve it at `http://<docker-host>:4000`.

To make this even easier, copy `docker-compose.yml`
[from this repository](https://github.com/aymanhelo/Docker/blob/main/jekyll/docker-compose.yml)
to your jekyll site root. Then you'll only need to:

```shell
cd dir/of/your/jekyll/site
docker-compose up
```



### Q. What if I want to run other jekyll commands?

just add the jekyll options to the end of the `bretfisher/jekyll`:

```shell
docker run -v $(pwd):/site bretfisher/jekyll doctor
```

### Q. What if I want to build a site with Gemfile dependencies?

As your Jekyll site gets fancier, you'll need to add Jekyll plugins via Ruby's `Gemfile` and `bundle` CLI. You'll need to do a `bundle install` first before building your Jekyll site.

If you were using this repo's image, you could:

`docker run -v $(pwd):/site -it --entrypoint bash bretfisher/jekyll`

Then run your commands interactively:

```bash
bundle install --retry 5 --jobs 20
bundle exec jekyll build
```

Then your bind-mounted `_site` will be there on your host, built by Jekyll using your Gemfile Jekyll dependencies that were installed in that container.

If this is something you do often, you'll want to build your *own* image that already has your Ruby dependencies installed. Then when you run the `jekyll build` command, it'll have all the Gemfile dependencies it needs.


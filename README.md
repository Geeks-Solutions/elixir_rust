# Elixir Rust Docker Image

This repo hosts the Dockerfile to produce an image that is based on Alpine, includes Elixir and Rust (with Cargo).

It is used in a CI pipeline to run code standard right away, with no before_script requirements:  
`mix credo --strict`

to run tests:  
`mix coveralls` 

And also to build a production ready Elixir release.

Adding Rust to this image allows to compile Rust Nifs based on Rustler.

If a release relies on a Rust NIFs, then your release env. should contain libgcc. For instance here is a working example:

```Dockerfile
FROM alpine:3.13 AS app
RUN apk add --no-cache openssl ncurses-libs libgcc

WORKDIR /app

RUN chown nobody:nobody /app

USER nobody:nobody

COPY --from=build --chown=nobody:nobody /app/_build/prod/rel ./

ENV HOME=/app

COPY entrypoint.sh .

# Run the Phoenix app
CMD ["./entrypoint.sh"]
```

## Development
If you want to contribute you can update the versions necessary and then build for multiple plateforms:
```bash
docker buildx build . --platform linux/amd64,linux/arm64 -t geeks5olutions/elixir_rust:{tag}
```

To debug you can add the following parameters to the above command:
`--progress=plain --no-cache`

To push to the registry run the buildx command with the `--push` param
FROM hexpm/elixir:1.19.5-erlang-28.0.4-debian-bookworm-20260824-slim

ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y --no-install-recommends \
      build-essential clang git curl ca-certificates python3 pkg-config libsqlite3-dev \
    && rm -rf /var/lib/apt/lists/*
RUN mix local.hex --force && mix local.rebar --force

COPY . /src
WORKDIR /src
RUN MIX_ENV=dev mix deps.get \
    && MIX_ENV=test mix deps.get \
    && MIX_ENV=dev mix compile \
    && MIX_ENV=test mix compile

# These harnesses compile the actual portable iOS headers. The Objective-C
# frame-registry harness needs Apple's Foundation and is not built on Linux.
RUN make -C test/native CC=clang dist_port_test dist_cookie_test stored_queue_test init_args_test

# All Hex sources and registry records remain available for offline reproducers.
ENV HEX_OFFLINE=1

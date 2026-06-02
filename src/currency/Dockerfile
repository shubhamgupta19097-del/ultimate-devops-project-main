# Copyright The OpenTelemetry Authors
# SPDX-License-Identifier: Apache-2.0
# Copyright 2023 The OpenTelemetry Authors
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License

FROM ubuntu:22.04 AS builder

ARG DEBIAN_FRONTEND=noninteractive
ARG OPENTELEMETRY_CPP_VERSION=1.9.1

RUN apt-get update && apt-get install -y \
    git \
    curl \
    cmake \
    build-essential \
    g++ \
    pkg-config \
    libgrpc++-dev \
    libprotobuf-dev \
    protobuf-compiler \
    linux-libc-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /currency

COPY ./src/currency /currency
COPY ./pb/demo.proto /currency/proto/demo.proto

RUN rm -rf build \
    && mkdir -p build

WORKDIR /currency/build

RUN cmake .. -DCMAKE_POLICY_VERSION_MINIMUM=3.5 \
    || (echo "CMAKE FAILED" && ls -R .. && exit 1)

RUN make -j1 \
    || (echo "MAKE FAILED" && exit 1)

RUN make install

RUN ls -lah /currency || true
RUN ls -lah /currency/build || true
RUN find /currency -type f || true

FROM ubuntu:22.04 AS release

RUN apt-get update && apt-get install -y \
    libgrpc++-dev \
    libprotobuf-dev

COPY --from=builder /usr/local /usr/local
COPY --from=builder /currency /currency

EXPOSE ${CURRENCY_PORT}

ENTRYPOINT ["/usr/local/bin/currency"]

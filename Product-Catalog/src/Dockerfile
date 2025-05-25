# Copyright The OpenTelemetry Authors
# SPDX-License-Identifier: Apache-2.0

FROM golang:1.22-alpine AS builder

WORKDIR /usr/src/app/

RUN --mount=type=cache,target=/go/pkg/mod/ \
    --mount=type=bind,source=./go.sum,target=go.sum \
    --mount=type=bind,source=./go.mod,target=go.mod \
    go mod download

RUN --mount=type=cache,target=/go/pkg/mod/ \
    --mount=type=cache,target=/root/.cache/go-build \
    --mount=type=bind,rw,source=./.,target=. \
    go build -ldflags "-s -w" -o /go/bin/product-catalog/ ./

FROM alpine AS release

WORKDIR /usr/src/app/

COPY --from=builder /go/bin/product-catalog/ ./
COPY products ./products

EXPOSE ${PRODUCT_CATALOG_PORT}
ENTRYPOINT [ "./product-catalog" ]

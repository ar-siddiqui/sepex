# -------------------------------
FROM golang:1.24.1 AS dev

# Build arguments for version information (defaults to unknown)
ARG GIT_TAG=unknown
ARG GIT_COMMIT=unknown

RUN go install github.com/swaggo/swag/cmd/swag@v1.16.6

# Hot-Reloader
RUN go install github.com/githubnemo/CompileDaemon@v1.4.0

COPY ./ /app
WORKDIR /app

RUN swag init && \
    go build -ldflags="-X main.GitTag=${GIT_TAG} -X main.GitCommit=${GIT_COMMIT}" -o main main.go


ENTRYPOINT ["CompileDaemon", "--build=go build main.go", "--command=./main"]
# -------------------------------

# -------------------------------
FROM debian:12.8-slim AS prod

# Install CA certificates and other runtime dependencies
RUN apt-get update && apt-get install -y \
    ca-certificates \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

COPY --from=dev /app/main /app/main
COPY --from=dev /app/public /app/public
COPY --from=dev /app/views /app/views
COPY --from=dev /app/docs /app/docs

ENTRYPOINT ["/app/main"]
# -------------------------------

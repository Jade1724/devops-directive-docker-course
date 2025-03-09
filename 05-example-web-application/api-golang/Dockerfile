# Ubuntu based image buster that contains necessary tools for build command. 
# Although buster image is larger than alpine, the size of the image doesn't matter as build and production images are separate. 
FROM golang:1.19-buster as build

WORKDIR /app

RUN useradd -u 1001 nonroot

# Copy dependencies files first to separate from the source code
COPY go.mod go.sum ./

RUN --mount=type=cache,target=/go/pkg/mod \
    --mount=type=cache,target=root/.cache/go-build \
    go mod download

COPY . .

# Build a binary with an external linker. These options make sure it generates fully static build.
RUN go build \
    -ldflags="-linkmode external -extldflags -static" \
    -tags netgo \
    -o api-golang

# Docker official image, an explicitly empty image with just a user, for copying binary
FROM scratch

ENV GIN_MODE release

# Copy teh password created when adding a non root user in the build image across production image
COPY --from=build /etc/passwd /etc/passwd

# Copy executable in the build image working directory into production image
COPY --from=build /app/api-golang api-golang

USER nonroot

EXPOSE 8080

# Run the executable binary
CMD ["./api-golang"]
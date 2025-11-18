FROM ne0nd0g/merlin-base:v1.8.0

# Build multi-arch Docker image and push tagged version to Docker Hub
# > sudo docker buildx build --push --platform linux/amd64,linux/arm64 --tag ne0nd0g/merlin-mythic:v1.0.2 --tag ne0nd0g/merlin-mythic:latest .

WORKDIR /Mythic

# Copy go.mod and go.sum first for better Docker layer caching
# This allows dependency downloads to be cached separately from source code changes
COPY ["agent/go.mod", "agent/go.sum", "agent/"]
COPY ["container/go.mod", "container/go.sum", "container/"]

# Download all the Go modules ahead of time, before compiling
# This layer will be cached unless go.mod/go.sum changes
WORKDIR /Mythic/agent
RUN go mod download
WORKDIR /Mythic/container
RUN go mod download

# Copy the rest of the source code
# This will invalidate the cache when source files change, forcing a rebuild
WORKDIR /Mythic
COPY [".", "."]

# Build the Merlin container and save in the /usr/local/bin directory because it is in the PATH environment variable
# Do not save it in the Mythic directory because it is temporary while building and will be lost
WORKDIR /Mythic/container
RUN go build -o /usr/local/bin/MerlinContainer

CMD /usr/local/bin/MerlinContainer
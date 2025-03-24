# because of dotnet, we always build on amd64, and target platforms in cli
# dotnet doesn't support QEMU for building or running. 
# (errors common in arm/v7 32bit) https://github.com/dotnet/dotnet-docker/issues/1537
# https://hub.docker.com/_/microsoft-dotnet
# hadolint ignore=DL3029
# to build for a different platform than your host, use --platform=<platform>
# for example, if you were on Intel (amd64) and wanted to build for ARM, you would use:
# docker buildx build --platform "linux/arm64/v8" .

# build compiles the program for the builder's local platform
FROM --platform=${BUILDPLATFORM} mcr.microsoft.com/dotnet/sdk:7.0 AS build
#Base image for .net application taken from microsoft registry.

ARG TARGETPLATFORM
ARG TARGETARCH
ARG BUILDPLATFORM
#Problem: Normally, you would need to build separate Docker images for each platform.
Solution: Docker multi-architecture builds let you create one image that works on multiple CPU architectures (Intel, AMD, ARM) and operating systems (Windows, Linux).

RUN echo "I am running on $BUILDPLATFORM, building for $TARGETPLATFORM"

WORKDIR /source
COPY *.csproj .

#From help with DEV team we should know 'RUN' commands for any new applciation to download the dependencies, to get any packages to deploy applications.
RUN dotnet restore -a $TARGETARCH

COPY . .

RUN dotnet publish -c release -o /app -a $TARGETARCH --self-contained false --no-restore

# app image
FROM mcr.microsoft.com/dotnet/runtime:7.0
WORKDIR /app
COPY --from=build /app .

#From DEV team we should know the ENTRYPOINT to start.
ENTRYPOINT ["dotnet", "Worker.dll"]

#See https://aka.ms/customizecontainer to learn how to customize your debug container and how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/aspnet:11.0@sha256:ca60823900e34e488caed1df187266fa02e3b59d1180c8db728b6cb727212ba2 AS base
WORKDIR /app
EXPOSE 8080
EXPOSE 8081
ENV DEBIAN_FRONTEND=noninteractive
RUN apt update && \
  apt install -y curl ffmpeg && \
  apt clean && \
  rm -rf /var/lib/apt/lists/*
RUN curl -L https://github.com/yt-dlp/yt-dlp/releases/latest/download/yt-dlp -o /usr/local/bin/yt-dlp && \
  chmod a+rx /usr/local/bin/yt-dlp
USER app

FROM mcr.microsoft.com/dotnet/sdk:11.0@sha256:e2438fd800a09d2d761aefe4edd30675c8f1db8958e4423c0844cc9a0240e60f AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["YouTubeDL.Web/YouTubeDL.Web.csproj", "YouTubeDL.Web/"]
RUN dotnet restore "./YouTubeDL.Web/YouTubeDL.Web.csproj"
COPY . .
WORKDIR "/src/YouTubeDL.Web"
RUN dotnet build "./YouTubeDL.Web.csproj" -c $BUILD_CONFIGURATION -o /app/build

FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./YouTubeDL.Web.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "YouTubeDL.Web.dll"]
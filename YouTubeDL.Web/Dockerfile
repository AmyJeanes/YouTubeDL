#See https://aka.ms/customizecontainer to learn how to customize your debug container and how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/aspnet:9.0@sha256:1af4114db9ba87542a3f23dbb5cd9072cad7fcc8505f6e9131d1feb580286a6f AS base
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

FROM mcr.microsoft.com/dotnet/sdk:9.0@sha256:bb42ae2c058609d1746baf24fe6864ecab0686711dfca1f4b7a99e367ab17162 AS build
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
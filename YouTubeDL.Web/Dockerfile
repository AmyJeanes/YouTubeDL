#See https://aka.ms/customizecontainer to learn how to customize your debug container and how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/aspnet:10.0@sha256:9271888dde1f4408dfb7ce75bc0f513c903d20ff2f1287ab153b641d4588ec7d AS base
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

FROM mcr.microsoft.com/dotnet/sdk:10.0@sha256:adc02be8b87957d07208a4a3e51775935b33bad3317de8c45b1e67357b4c073b AS build
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
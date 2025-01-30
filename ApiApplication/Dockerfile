FROM mcr.microsoft.com/dotnet/aspnet:9.0 AS base
WORKDIR /app
EXPOSE 7172

ENV ASPNETCORE_URLS=http://+:7172

# Creates a non-root user with an explicit UID and adds permission to access the /app folder
# For more info, please refer to https://aka.ms/vscode-docker-dotnet-configure-containers
RUN adduser -u 5678 --disabled-password --gecos "" appuser && chown -R appuser /app
USER appuser

FROM --platform=$BUILDPLATFORM mcr.microsoft.com/dotnet/aspnet:9.0 AS build
ARG configuration=Release
WORKDIR /src
COPY ["ApiApplication/ApiApplication.csproj", "ApiApplication/"]
RUN dotnet restore "ApiApplication/ApiApplication.csproj"
COPY . .
WORKDIR "/src/ApiApplication"
RUN dotnet build "ApiApplication.csproj" -c $configuration -o /app/build

FROM build AS publish
ARG configuration=Release
RUN dotnet publish "ApiApplication.csproj" -c $configuration -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "ApiApplication.dll"]

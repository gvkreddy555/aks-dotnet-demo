# Stage 1: Build & Publish
FROM [mcr.microsoft.com/dotnet/sdk:8.0](https://mcr.microsoft.com/dotnet/sdk:8.0) AS build
WORKDIR /src

# Copy project file and restore dependencies (layer caching)
COPY ["MyWebApi.csproj", "./"]
RUN dotnet restore "MyWebApi.csproj"

# Copy remaining source code and publish release binaries
COPY . .
RUN dotnet publish "MyWebApi.csproj" -c Release -o /app/publish /p:UseAppHost=false

# Stage 2: Runtime Image (Lightweight & Hardened)
FROM [mcr.microsoft.com/dotnet/aspnet:8.0](https://mcr.microsoft.com/dotnet/aspnet:8.0) AS final
WORKDIR /app

# Copy compiled binaries from build stage
COPY --from=build /app/publish .

# Run as non-root user for security compliance
USER app

# Set environment variable for HTTP port
ENV ASPNETCORE_HTTP_PORTS=8080
EXPOSE 8080

ENTRYPOINT ["dotnet", "MyWebApi.dll"]
# Stage 1: Base image with ASP.NET runtime
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
WORKDIR /app
EXPOSE 8080
EXPOSE 8081

# Stage 2: Build the application
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src

# Copy the .csproj file and restore dependencies
COPY ["multiapp/multiapp.csproj", "multiapp/"]
RUN dotnet restore "multiapp/multiapp.csproj"

# Copy the entire project and build it
COPY . .
WORKDIR "/src/multiapp"
RUN dotnet build "multiapp.csproj" -c $BUILD_CONFIGURATION -o /app/build

# Stage 3: Publish the application
FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "multiapp.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

# Stage 4: Final image with the application
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .

# Set the entry point to the published DLL
ENTRYPOINT ["dotnet", "multiapp.dll"]

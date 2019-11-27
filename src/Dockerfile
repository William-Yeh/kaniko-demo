FROM mcr.microsoft.com/dotnet/core/sdk:3.0 AS build

# Get around dotnet/core/sdk + Kaniko bug.
# @see https://github.com/GoogleContainerTools/kaniko/issues/391#issuecomment-477472649
ENV COMPlus_EnableDiagnostics=0

WORKDIR /app

# Add *.csproj before rest of repo for caching
COPY *.csproj .
RUN  dotnet restore

# Copy all files in the solution to builder context
COPY . .
RUN  dotnet publish -o out

#---

FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
LABEL maintainer="william.pjyeh@gmail.com"

ENV ASPNETCORE_URLS http://+:80
EXPOSE 80
WORKDIR /app
COPY --from=build /app/out ./
ENTRYPOINT ["dotnet", "TodoApi.dll"]

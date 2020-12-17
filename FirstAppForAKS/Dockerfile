#Dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:3.1-buster AS builder
WORKDIR /src
COPY ./FirstAppForAKS.csproj .
RUN dotnet restore FirstAppForAKS.csproj
COPY . .
RUN dotnet build FirstAppForAKS.csproj -c Debug -o /src/out

FROM mcr.microsoft.com/dotnet/core/aspnet:3.1-buster-slim
WORKDIR /app
COPY --from=builder /src/out .

EXPOSE 80
ENTRYPOINT ["dotnet", "FirstAppForAKS.dll"]

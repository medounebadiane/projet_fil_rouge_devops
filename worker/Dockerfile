# Étape 1 : compilation avec le SDK .NET 8 (contient le compilateur)
FROM --platform=${BUILDPLATFORM} mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG TARGETARCH
WORKDIR /source

# Dépendances d'abord : Docker les garde en cache tant que le .csproj ne change pas
COPY *.csproj .
RUN dotnet restore -a $TARGETARCH

# Puis le code, et la compilation
COPY . .
RUN dotnet publish -c release -o /app -a $TARGETARCH --self-contained false --no-restore

# Étape 2 : image finale légère, avec seulement le runtime .NET 8
FROM mcr.microsoft.com/dotnet/runtime:8.0
WORKDIR /app
COPY --from=build /app .

# Sécurité : le programme ne tourne pas en root
USER $APP_UID

ENTRYPOINT ["dotnet", "Worker.dll"]

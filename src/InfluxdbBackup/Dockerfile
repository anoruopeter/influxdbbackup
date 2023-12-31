# BUILD IMAGE
FROM mcr.microsoft.com/dotnet/sdk AS build-env
WORKDIR /app

# Copy csproj and restore as distinct layers
COPY *.csproj ./
RUN dotnet restore

# Copy everything else and build
COPY . ./
RUN dotnet publish -c Release -o out

FROM influxdb:1.8-alpine as influxdb

FROM mcr.microsoft.com/dotnet/sdk:7.0-alpine3.17
COPY --from=influxdb /usr/bin/influxd /usr/bin/

# RUN apk add --no-cache bash 
# RUN mkdir -p /influxdbbackup/data /influxdbbackup/log 

RUN apk add --no-cache tzdata bash && \
	mkdir -p /influxdbbackup/data /influxdbbackup/log 

# ----------------- Create ENV variables and set default values -------------------- #
ENV INFLUXDB_ACTION="null"
ENV INFLUXDB_ACTION_CRON="null"
ENV INFLUXDB_BACKUPMEDIUM="null"
ENV INFLUXDB_DATABASE="null"
ENV INFLUXDB_HOST="null"
ENV INFLUXDB_PORT="null"

ENV AZURE_STORAGEACCOUNT_NAME="null"
ENV AZURE_STORAGEACCOUNT_KEY="null"
ENV AZURE_STORAGEACCOUNT_CONTAINER="influxdbbackup"

ENV BACKUP_MAXBACKUPS="1"
ENV BACKUP_FILENAME="Influxdb_backup"
# ---------------------------------------------------------------------------------- #

VOLUME ["/influxdbbackup/data", "/influxdbbackup/log"]

WORKDIR /influxdbbackup
COPY --from=build-env /app/out .
ENTRYPOINT [ "dotnet", "InfluxdbBackup.dll" ]

# AutoETL

[![.NET](https://img.shields.io/badge/.NET%20Framework-4.8-512BD4?logo=dotnet)](https://dotnet.microsoft.com/)
[![C#](https://img.shields.io/badge/C%23-Console%20App-239120?logo=c-sharp)](https://docs.microsoft.com/en-us/dotnet/csharp/)

A command-line ETL (Extract, Transform, Load) tool that extracts data from source databases, exports to CSV, compresses to ZIP, transfers via FTP, and loads into a PostgreSQL target.

## 📋 Overview

AutoETL is designed for **edge-to-FDW** data transfer scenarios:

1. **Extract**: Query source databases, export to CSV
2. **Compress**: Zip CSV files for transfer
3. **Transfer**: Upload/download via FTP
4. **Load**: Unzip and load into PostgreSQL

## ⚙️ Supported Databases

| Direction | Databases |
| --- | --- |
| **Extract (Source)** | Oracle, SQL Server, SQL Server V8, PostgreSQL, MySQL (MariaDB) |
| **Load (Target)** | PostgreSQL |

## 🚀 Run Modes

| Mode | Option | Description |
| --- | --- | --- |
| **config** | `-r config -p "path"` | Generate extract configuration files from source schema |
| **update** | `-r update -p "path"` | Update existing config (delta, delta type, etc.) |
| **extract** | `-r extract -p "path"` | Extract data → CSV → Zip → FTP upload |
| **load** | `-r load -p "path"` | FTP download → Unzip → Load into PostgreSQL |

## 📖 Usage

```bash
# Build config from source DB metadata
AutoETL.exe -r config -p ".\extract\_cfg"

# Update config (delta/extract type changes)
AutoETL.exe -r update -p ".\extract\_cfg"

# Extract: source DB → CSV → Zip → FTP upload
AutoETL.exe -r extract -p ".\extract\_cfg"

# Load: FTP download → Unzip → Load into PostgreSQL
AutoETL.exe -r load -p ".\load\_cfg"
```

## 📁 Directory Structure

```text
AutoETL/
├── extract/
│   ├── _cfg/        # Extract config XML files
│   │   ├── success/
│   │   └── failure/
│   ├── _csv/        # Exported CSV output
│   └── _zip/        # Compressed ZIP for transfer
├── load/
│   ├── _cfg/        # Load config
│   ├── _csv/        # Downloaded/unzipped CSV
│   └── _zip/        # Downloaded ZIP from FTP
├── backup/          # Backup storage
├── history.xml      # ETL history (delta tracking)
└── AutoETL.exe.config
```

## 🔧 Configuration

### App.config

| Setting | Description |
| --- | --- |
| `ExtractCfgFolder` | Path to extract config XML files |
| `ExtractCsvFolder` | Output folder for extracted CSV |
| `ExtractZipFolder` | Output folder for ZIP archives |
| `LoadCfgFolder` | Path to load config |
| `LoadCsvFolder` | Input folder for CSV to load |
| `LoadZipFolder` | Input folder for downloaded ZIP |
| `FtpIp`, `FtpPort`, `FtpId`, `FtpPwd` | FTP connection for transfer |
| `FdwServer`, `FdwPort`, `FdwId`, `FdwPwd` | PostgreSQL (FDW) target connection |

### extractSettings (App.config)

Maps source databases to load targets:

```xml
<extractSettings>
  <add key="{key}" value="{LoadDB};{LoadSchema};{DbType};{Ip};{Port};postgres;{id};{ExtractSchema}" />
</extractSettings>
```

## 🛠️ Build

### Prerequisites

- Visual Studio 2019+ or .NET Framework 4.8 SDK
- NuGet package restore

### Build Steps

```bash
# Restore packages
nuget restore AutoETL.sln

# Build
msbuild AutoETL.csproj /p:Configuration=Release
```

Output: `bin\Release\AutoETL.exe`

## 📁 Project Structure

```text
auto-etl/
├── Configs/           # DB-specific config builders (Oracle, SQL Server, PostgreSQL, MySQL)
├── Core/
│   ├── Commands/      # Extract, Load, Zip, Unzip, FTPUpload, FTPDownload
│   ├── Extract/       # DB extractors (IDbServerExtractor implementations)
│   └── Load/          # DB loaders (PgServerLoader)
├── Models/            # DTOs (ExtractorConfig, DbConnInfo, FtpConnInfo, etc.)
├── Utils/             # Helpers (File, Zip, Xml, Directory)
├── Program.cs         # Entry point
├── Options.cs         # Command-line parsing
└── App.config         # Configuration
```

## 📃 License

Copyright © Changsin Inc. All rights reserved.

## 🤝 Contributing

Contributions are welcome. Feel free to open issues or submit pull requests.

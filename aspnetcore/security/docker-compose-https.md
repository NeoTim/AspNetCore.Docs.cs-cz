---
title: Hostování ASP.NET Core image v kontejneru pomocí prostředí Docker – sestavení s protokolem HTTPS
author: ravipal
description: Informace o hostování ASP.NET Core imagí pomocí Docker Compose přes HTTPS
monikerRange: '>= aspnetcore-2.1'
ms.author: ravipal
ms.custom: mvc
ms.date: 03/28/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/docker-compose-https
ms.openlocfilehash: b282af3b9c657bda4432f0d60f100f65fa7cbae9
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408614"
---
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a>Hostování ASP.NET Core imagí pomocí Docker Compose přes HTTPS


ASP.NET Core [ve výchozím nastavení používá protokol HTTPS](/aspnet/core/security/enforcing-ssl). [Protokol HTTPS](https://en.wikipedia.org/wiki/HTTPS) spoléhá na [certifikáty](https://en.wikipedia.org/wiki/Public_key_certificate) pro důvěryhodnost, identitu a šifrování.

Tento dokument vysvětluje, jak spustit předem připravené image kontejnerů pomocí protokolu HTTPS.

Další informace najdete v tématu [vývoj aplikací ASP.NET Core s využitím Docker přes protokol HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) pro vývojové scénáře.

Tato ukázka vyžaduje [docker 17,06](https://docs.docker.com/release-notes/docker-ce) nebo novější z [klienta Docker](https://www.docker.com/products/docker).

## <a name="prerequisites"></a>Požadavky

Některé pokyny v tomto dokumentu vyžadují [sadu SDK .NET Core 2,2](https://dotnet.microsoft.com/download) nebo novější.

## <a name="certificates"></a>Certifikáty

Pro [hostování v provozu](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) v doméně je vyžadován certifikát od [certifikační autority](https://wikipedia.org/wiki/Certificate_authority) . [Let's Encrypt](https://letsencrypt.org/)je certifikační autorita, která nabízí bezplatné certifikáty.

Tento dokument používá [certifikáty pro vývoj podepsaný svým držitelem](https://wikipedia.org/wiki/Self-signed_certificate) pro hostování předem vytvořených imagí `localhost` . Pokyny jsou podobné použití produkčních certifikátů.

Pro produkční certifikáty:

* `dotnet dev-certs`Nástroj není povinný.
* Certifikáty není nutné ukládat v umístění, které jste použili v pokynech. Certifikáty uložte v jakémkoli umístění mimo adresář webu.

Pokyny obsažené v následujícím oddílu připojovat certifikáty do kontejnerů pomocí `volumes` vlastnosti v *Docker-Compose. yml.* Do imagí kontejnerů můžete přidat certifikáty pomocí `COPY` příkazu ve *souboru Dockerfile*, ale nedoporučuje se to. Kopírování certifikátů do bitové kopie se nedoporučuje z následujících důvodů:

* Pro testování pomocí certifikátů pro vývojáře je obtížné použít stejný obrázek.
* Je obtížné použít stejný obrázek pro hostování s provozními certifikáty.
* Existuje významné riziko odhalení certifikátu.

## <a name="starting-a-container-with-https-support-using-docker-compose"></a>Spuštění kontejneru s podporou protokolu HTTPS pomocí sestavení Docker

Pro konfiguraci operačního systému použijte následující pokyny.

### <a name="windows-using-linux-containers"></a>Windows s použitím kontejnerů Linux

Generovat certifikát a nakonfigurovat místní počítač:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

V předchozích příkazech nahraďte `{ password here }` heslo.

Vytvořte soubor _Docker-Compose. Debug. yml_ s následujícím obsahem:

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
Heslo zadané v souboru Docker pro sestavení se musí shodovat s heslem použitým pro certifikát.

Spusťte kontejner s ASP.NET Core nakonfigurovanou pro protokol HTTPS:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a>macOS nebo Linux

Generovat certifikát a nakonfigurovat místní počítač:

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

`dotnet dev-certs https --trust`je podporován pouze v macOS a Windows. Musíte důvěřovat certifikátům na platformě Linux způsobem, který podporuje vaše distribuce. Je možné, že certifikát budete muset důvěřovat v prohlížeči.

V předchozích příkazech nahraďte `{ password here }` heslo.

Vytvořte soubor _Docker-Compose. Debug. yml_ s následujícím obsahem:

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
Heslo zadané v souboru Docker pro sestavení se musí shodovat s heslem použitým pro certifikát.

Spusťte kontejner s ASP.NET Core nakonfigurovanou pro protokol HTTPS:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a>Windows s kontejnery Windows

Generovat certifikát a nakonfigurovat místní počítač:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

V předchozích příkazech nahraďte `{ password here }` heslo.

Vytvořte soubor _Docker-Compose. Debug. yml_ s následujícím obsahem:

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=C:\https\aspnetapp.pfx
    volumes:
      - ${USERPROFILE}\.aspnet\https:C:\https:ro
```
Heslo zadané v souboru Docker pro sestavení se musí shodovat s heslem použitým pro certifikát.

Spusťte kontejner s ASP.NET Core nakonfigurovanou pro protokol HTTPS:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

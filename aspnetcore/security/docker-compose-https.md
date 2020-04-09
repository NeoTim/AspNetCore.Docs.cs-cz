---
title: Hostování image ASP.NET Core v kontejneru pomocí docker compose s HTTPS
author: ravipal
description: Přečtěte si, jak hostovat ASP.NET základní imbliny pomocí Docker Compose přes HTTPS
monikerRange: '>= aspnetcore-2.1'
ms.author: ravipal
ms.custom: mvc
ms.date: 03/28/2020
no-loc:
- Let's Encrypt
uid: security/docker-compose-https
ms.openlocfilehash: 616ccf906e98534ffda08c0c2b6d0a171f063cc1
ms.sourcegitcommit: d03905aadf5ceac39fff17706481af7f6c130411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80381822"
---
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a>Hostování ASP.NET základních iimages s Docker Compose přes HTTPS


ASP.NET Core [ve výchozím nastavení](/aspnet/core/security/enforcing-ssl)používá protokol HTTPS . [Protokol HTTPS](https://en.wikipedia.org/wiki/HTTPS) spoléhá na [certifikáty](https://en.wikipedia.org/wiki/Public_key_certificate) pro důvěryhodnost, identitu a šifrování.

Tento dokument vysvětluje, jak spustit předem vytvořené image kontejneru s protokolem HTTPS.

Viz [Vývoj ASP.NET základních aplikací s Dockerem přes HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) pro scénáře vývoje.

Tato ukázka vyžaduje [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) nebo novější [klienta Dockeru](https://www.docker.com/products/docker).

## <a name="prerequisites"></a>Požadavky

Pro některé pokyny v tomto dokumentu je vyžadována sada [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) nebo novější.

## <a name="certificates"></a>Certifikáty

Pro [produkční hostování](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) pro doménu je vyžadován certifikát od [certifikační autority.](https://wikipedia.org/wiki/Certificate_authority) [Let's Encrypt](https://letsencrypt.org/)je certifikační autorita, která nabízí bezplatné certifikáty.

Tento dokument používá [certifikáty pro vývoj s vlastním podpisem](https://wikipedia.org/wiki/Self-signed_certificate) pro hostování předem vytvořených bitových kopií přes `localhost`. Pokyny jsou podobné použití produkčních certifikátů.

Pro produkční certifikáty:

* Nástroj `dotnet dev-certs` není vyžadován.
* Certifikáty nemusí být uloženy v umístění použitém v pokynech. Certifikáty uklánějte do libovolného umístění mimo adresář webu.

Pokyny obsažené v následující části svazku připojit certifikáty do kontejnerů pomocí `volumes` vlastnosti v *docker-compose.yml.* Můžete přidat certifikáty do imagí kontejneru s příkazem `COPY` v *Dockerfile*, ale není doporučeno. Kopírování certifikátů do obrázku se nedoporučuje z následujících důvodů:

* To ztěžuje použití stejné image pro testování s vývojářcertifikáty.
* To ztěžuje použití stejného obrázku pro hosting s produkčními certifikáty.
* Existuje značné riziko zveřejnění certifikátu.

## <a name="starting-a-container-with-https-support-using-docker-compose"></a>Spuštění kontejneru s podporou https pomocí docker compose

Pro konfiguraci operačního systému postupujte podle následujících pokynů.

### <a name="windows-using-linux-containers"></a>Windows pomocí linuxových kontejnerů

Generovat certifikát a konfigurovat místní počítač:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

V předchozích příkazech `{ password here }` nahraďte heslem.

Vytvořte soubor _docker-compose.debug.yml_ s následujícím obsahem:

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
Heslo zadané v souboru compose dockeru se musí shodovat s heslem použitým pro certifikát.

Spusťte kontejner s ASP.NET Core nakonfigurovaným pro protokol HTTPS:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a>macOS nebo Linux

Generovat certifikát a konfigurovat místní počítač:

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

`dotnet dev-certs https --trust`je podporována pouze v systémech macOS a Windows. Certifikátům na Linuxu musíte důvěřovat způsobem, který je podporován vaší distro. Je pravděpodobné, že budete muset důvěřovat certifikátu ve vašem prohlížeči.

V předchozích příkazech `{ password here }` nahraďte heslem.

Vytvořte soubor _docker-compose.debug.yml_ s následujícím obsahem:

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
Heslo zadané v souboru compose dockeru se musí shodovat s heslem použitým pro certifikát.

Spusťte kontejner s ASP.NET Core nakonfigurovaným pro protokol HTTPS:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a>Windows pomocí kontejnerů Windows

Generovat certifikát a konfigurovat místní počítač:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

V předchozích příkazech `{ password here }` nahraďte heslem.

Vytvořte soubor _docker-compose.debug.yml_ s následujícím obsahem:

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
Heslo zadané v souboru compose dockeru se musí shodovat s heslem použitým pro certifikát.

Spusťte kontejner s ASP.NET Core nakonfigurovaným pro protokol HTTPS:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

---
title: Hostování ASP.NET Core imagí pomocí Docker přes HTTPS
author: rick-anderson
description: Naučte se hostovat image ASP.NET Core pomocí Docker přes HTTPS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
no-loc:
- Let's Encrypt
uid: security/docker-https
ms.openlocfilehash: 07e2791e5b26975c71323f8cb41a4b0fbe0cdf11
ms.sourcegitcommit: 2388c2a7334ce66b6be3ffbab06dd7923df18f60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75952129"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a>Hostování ASP.NET Core imagí pomocí Docker přes HTTPS

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core [ve výchozím nastavení používá protokol HTTPS](/aspnet/core/security/enforcing-ssl). [Protokol HTTPS](https://en.wikipedia.org/wiki/HTTPS) spoléhá na [certifikáty](https://en.wikipedia.org/wiki/Public_key_certificate) pro důvěryhodnost, identitu a šifrování.

Tento dokument vysvětluje, jak spustit předem připravené image kontejnerů pomocí protokolu HTTPS.

Další informace najdete v tématu [vývoj aplikací ASP.NET Core s využitím Docker přes protokol HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/aspnetcore-docker-https-development.md) pro vývojové scénáře.

Tato ukázka vyžaduje [docker 17,06](https://docs.docker.com/release-notes/docker-ce) nebo novější z [klienta Docker](https://www.docker.com/products/docker).

## <a name="prerequisites"></a>Požadavky

Některé pokyny v tomto dokumentu vyžadují [sadu SDK .NET Core 2,2](https://www.microsoft.com/net/download) nebo novější.

## <a name="certificates"></a>Certifikáty

Pro [hostování v provozu](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) v doméně je vyžadován certifikát od [certifikační autority](https://wikipedia.org/wiki/Certificate_authority) . [Let's Encrypt](https://letsencrypt.org/) je certifikační autorita, která nabízí bezplatné certifikáty.

Tento dokument používá [certifikáty pro vývoj podepsaný svým držitelem](https://en.wikipedia.org/wiki/Self-signed_certificate) pro hostování předem sestavených imagí přes `localhost`. Pokyny jsou podobné použití produkčních certifikátů.

Pro produkční certifikáty:

* Nástroj `dotnet dev-certs` není povinný.
* Certifikáty není nutné ukládat v umístění, které jste použili v pokynech. Jakékoli umístění by mělo fungovat, i když ukládání certifikátů v adresáři webu se nedoporučuje.

Pokyny obsažené v následujícím oddílu připojovat certifikáty do kontejnerů pomocí možnosti příkazového řádku Docker `-v`. Certifikáty můžete přidat do imagí kontejneru pomocí příkazu `COPY` v *souboru Dockerfile*, ale nedoporučujeme to. Kopírování certifikátů do bitové kopie se nedoporučuje z následujících důvodů:

* Pro testování pomocí certifikátů pro vývojáře je obtížné použít stejný obrázek.
* Pro hostování s provozními certifikáty je obtížné použít stejný obrázek.
* Existuje významné riziko odhalení certifikátu.

## <a name="running-pre-built-container-images-with-https"></a>Spouštění předem vytvořených imagí kontejneru s protokolem HTTPS

Pro konfiguraci operačního systému použijte následující pokyny.

### <a name="windows-using-linux-containers"></a>Windows s použitím kontejnerů Linux

Generovat certifikát a nakonfigurovat místní počítač:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

V předchozích příkazech nahraďte `{ password here }` heslem.

Spusťte image kontejneru s ASP.NET Core nakonfigurovanou pro protokol HTTPS:

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

Heslo se musí shodovat s heslem použitým pro certifikát.

### <a name="macos-or-linux"></a>macOS nebo Linux

Generovat certifikát a nakonfigurovat místní počítač:

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

`dotnet dev-certs https --trust` se podporuje jenom v macOS a Windows. Musíte důvěřovat certifikátům na platformě Linux způsobem, který podporuje vaše distribuce. Je možné, že certifikát budete muset důvěřovat v prohlížeči.

V předchozích příkazech nahraďte `{ password here }` heslem.

Spusťte image kontejneru s ASP.NET Core nakonfigurovanou pro protokol HTTPS:

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

Heslo se musí shodovat s heslem použitým pro certifikát.

### <a name="windows-using-windows-containers"></a>Windows s kontejnery Windows

Generovat certifikát a nakonfigurovat místní počítač:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

V předchozích příkazech nahraďte `{ password here }` heslem.

Spusťte image kontejneru s ASP.NET Core nakonfigurovanou pro protokol HTTPS:

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

Heslo se musí shodovat s heslem použitým pro certifikát.

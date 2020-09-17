---
title: Hostování ASP.NET Core image v kontejneru pomocí prostředí Docker – sestavení s protokolem HTTPS
author: ravipal
description: Informace o hostování ASP.NET Core imagí pomocí Docker Compose přes HTTPS
monikerRange: '>= aspnetcore-2.1'
ms.author: ravipal
ms.custom: mvc
ms.date: 03/28/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/docker-compose-https
ms.openlocfilehash: 71ead7dcce32dab090a9b0b3573aa855d00fa7f1
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722758"
---
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a><span data-ttu-id="425fb-103">Hostování ASP.NET Core imagí pomocí Docker Compose přes HTTPS</span><span class="sxs-lookup"><span data-stu-id="425fb-103">Hosting ASP.NET Core images with Docker Compose over HTTPS</span></span>


<span data-ttu-id="425fb-104">ASP.NET Core [ve výchozím nastavení používá protokol HTTPS](./enforcing-ssl.md).</span><span class="sxs-lookup"><span data-stu-id="425fb-104">ASP.NET Core uses [HTTPS by default](./enforcing-ssl.md).</span></span> <span data-ttu-id="425fb-105">[Protokol HTTPS](https://en.wikipedia.org/wiki/HTTPS) spoléhá na [certifikáty](https://en.wikipedia.org/wiki/Public_key_certificate) pro důvěryhodnost, identitu a šifrování.</span><span class="sxs-lookup"><span data-stu-id="425fb-105">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="425fb-106">Tento dokument vysvětluje, jak spustit předem připravené image kontejnerů pomocí protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="425fb-106">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="425fb-107">Další informace najdete v tématu [vývoj aplikací ASP.NET Core s využitím Docker přes protokol HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) pro vývojové scénáře.</span><span class="sxs-lookup"><span data-stu-id="425fb-107">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="425fb-108">Tato ukázka vyžaduje [docker 17,06](https://docs.docker.com/release-notes/docker-ce) nebo novější z [klienta Docker](https://www.docker.com/products/docker).</span><span class="sxs-lookup"><span data-stu-id="425fb-108">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="425fb-109">Požadavky</span><span class="sxs-lookup"><span data-stu-id="425fb-109">Prerequisites</span></span>

<span data-ttu-id="425fb-110">Některé pokyny v tomto dokumentu vyžadují [sadu SDK .NET Core 2,2](https://dotnet.microsoft.com/download) nebo novější.</span><span class="sxs-lookup"><span data-stu-id="425fb-110">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="425fb-111">Certifikáty</span><span class="sxs-lookup"><span data-stu-id="425fb-111">Certificates</span></span>

<span data-ttu-id="425fb-112">Pro [hostování v provozu](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) v doméně je vyžadován certifikát od [certifikační autority](https://wikipedia.org/wiki/Certificate_authority) .</span><span class="sxs-lookup"><span data-stu-id="425fb-112">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="425fb-113">[Let's Encrypt](https://letsencrypt.org/) je certifikační autorita, která nabízí bezplatné certifikáty.</span><span class="sxs-lookup"><span data-stu-id="425fb-113">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="425fb-114">Tento dokument používá [certifikáty pro vývoj podepsaný svým držitelem](https://wikipedia.org/wiki/Self-signed_certificate) pro hostování předem vytvořených imagí `localhost` .</span><span class="sxs-lookup"><span data-stu-id="425fb-114">This document uses [self-signed development certificates](https://wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="425fb-115">Pokyny jsou podobné použití produkčních certifikátů.</span><span class="sxs-lookup"><span data-stu-id="425fb-115">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="425fb-116">Pro produkční certifikáty:</span><span class="sxs-lookup"><span data-stu-id="425fb-116">For production certificates:</span></span>

* <span data-ttu-id="425fb-117">`dotnet dev-certs`Nástroj není povinný.</span><span class="sxs-lookup"><span data-stu-id="425fb-117">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="425fb-118">Certifikáty není nutné ukládat v umístění, které jste použili v pokynech.</span><span class="sxs-lookup"><span data-stu-id="425fb-118">Certificates don't need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="425fb-119">Certifikáty uložte v jakémkoli umístění mimo adresář webu.</span><span class="sxs-lookup"><span data-stu-id="425fb-119">Store the certificates in any location outside the site directory.</span></span>

<span data-ttu-id="425fb-120">Pokyny obsažené v následujícím oddílu připojovat certifikáty do kontejnerů pomocí `volumes` vlastnosti v *Docker-Compose. yml.*</span><span class="sxs-lookup"><span data-stu-id="425fb-120">The instructions contained in the following section volume mount certificates into containers using the `volumes` property in *docker-compose.yml.*</span></span> <span data-ttu-id="425fb-121">Do imagí kontejnerů můžete přidat certifikáty pomocí `COPY` příkazu ve *souboru Dockerfile*, ale nedoporučuje se to.</span><span class="sxs-lookup"><span data-stu-id="425fb-121">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="425fb-122">Kopírování certifikátů do bitové kopie se nedoporučuje z následujících důvodů:</span><span class="sxs-lookup"><span data-stu-id="425fb-122">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="425fb-123">Pro testování pomocí certifikátů pro vývojáře je obtížné použít stejný obrázek.</span><span class="sxs-lookup"><span data-stu-id="425fb-123">It makes it difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="425fb-124">Je obtížné použít stejný obrázek pro hostování s provozními certifikáty.</span><span class="sxs-lookup"><span data-stu-id="425fb-124">It makes it difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="425fb-125">Existuje významné riziko odhalení certifikátu.</span><span class="sxs-lookup"><span data-stu-id="425fb-125">There is significant risk of certificate disclosure.</span></span>

## <a name="starting-a-container-with-https-support-using-docker-compose"></a><span data-ttu-id="425fb-126">Spuštění kontejneru s podporou protokolu HTTPS pomocí sestavení Docker</span><span class="sxs-lookup"><span data-stu-id="425fb-126">Starting a container with https support using docker compose</span></span>

<span data-ttu-id="425fb-127">Pro konfiguraci operačního systému použijte následující pokyny.</span><span class="sxs-lookup"><span data-stu-id="425fb-127">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="425fb-128">Windows s použitím kontejnerů Linux</span><span class="sxs-lookup"><span data-stu-id="425fb-128">Windows using Linux containers</span></span>

<span data-ttu-id="425fb-129">Generovat certifikát a nakonfigurovat místní počítač:</span><span class="sxs-lookup"><span data-stu-id="425fb-129">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="425fb-130">V předchozích příkazech nahraďte `{ password here }` heslo.</span><span class="sxs-lookup"><span data-stu-id="425fb-130">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="425fb-131">Vytvořte soubor _Docker-Compose. Debug. yml_ s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="425fb-131">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

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
<span data-ttu-id="425fb-132">Heslo zadané v souboru Docker pro sestavení se musí shodovat s heslem použitým pro certifikát.</span><span class="sxs-lookup"><span data-stu-id="425fb-132">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="425fb-133">Spusťte kontejner s ASP.NET Core nakonfigurovanou pro protokol HTTPS:</span><span class="sxs-lookup"><span data-stu-id="425fb-133">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a><span data-ttu-id="425fb-134">macOS nebo Linux</span><span class="sxs-lookup"><span data-stu-id="425fb-134">macOS or Linux</span></span>

<span data-ttu-id="425fb-135">Generovat certifikát a nakonfigurovat místní počítač:</span><span class="sxs-lookup"><span data-stu-id="425fb-135">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="425fb-136">`dotnet dev-certs https --trust` je podporován pouze v macOS a Windows.</span><span class="sxs-lookup"><span data-stu-id="425fb-136">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="425fb-137">Musíte důvěřovat certifikátům v systému Linux způsobem, který podporuje vaše distribuce.</span><span class="sxs-lookup"><span data-stu-id="425fb-137">You need to trust certificates on Linux in the way that is supported by your distribution.</span></span> <span data-ttu-id="425fb-138">Je možné, že certifikát budete muset důvěřovat v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="425fb-138">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="425fb-139">V předchozích příkazech nahraďte `{ password here }` heslo.</span><span class="sxs-lookup"><span data-stu-id="425fb-139">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="425fb-140">Vytvořte soubor _Docker-Compose. Debug. yml_ s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="425fb-140">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

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
<span data-ttu-id="425fb-141">Heslo zadané v souboru Docker pro sestavení se musí shodovat s heslem použitým pro certifikát.</span><span class="sxs-lookup"><span data-stu-id="425fb-141">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="425fb-142">Spusťte kontejner s ASP.NET Core nakonfigurovanou pro protokol HTTPS:</span><span class="sxs-lookup"><span data-stu-id="425fb-142">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a><span data-ttu-id="425fb-143">Windows s kontejnery Windows</span><span class="sxs-lookup"><span data-stu-id="425fb-143">Windows using Windows containers</span></span>

<span data-ttu-id="425fb-144">Generovat certifikát a nakonfigurovat místní počítač:</span><span class="sxs-lookup"><span data-stu-id="425fb-144">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="425fb-145">V předchozích příkazech nahraďte `{ password here }` heslo.</span><span class="sxs-lookup"><span data-stu-id="425fb-145">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="425fb-146">Vytvořte soubor _Docker-Compose. Debug. yml_ s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="425fb-146">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

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
<span data-ttu-id="425fb-147">Heslo zadané v souboru Docker pro sestavení se musí shodovat s heslem použitým pro certifikát.</span><span class="sxs-lookup"><span data-stu-id="425fb-147">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="425fb-148">Spusťte kontejner s ASP.NET Core nakonfigurovanou pro protokol HTTPS:</span><span class="sxs-lookup"><span data-stu-id="425fb-148">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```
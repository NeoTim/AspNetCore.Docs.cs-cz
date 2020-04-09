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
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a><span data-ttu-id="30a53-103">Hostování ASP.NET základních iimages s Docker Compose přes HTTPS</span><span class="sxs-lookup"><span data-stu-id="30a53-103">Hosting ASP.NET Core images with Docker Compose over HTTPS</span></span>


<span data-ttu-id="30a53-104">ASP.NET Core [ve výchozím nastavení](/aspnet/core/security/enforcing-ssl)používá protokol HTTPS .</span><span class="sxs-lookup"><span data-stu-id="30a53-104">ASP.NET Core uses [HTTPS by default](/aspnet/core/security/enforcing-ssl).</span></span> <span data-ttu-id="30a53-105">[Protokol HTTPS](https://en.wikipedia.org/wiki/HTTPS) spoléhá na [certifikáty](https://en.wikipedia.org/wiki/Public_key_certificate) pro důvěryhodnost, identitu a šifrování.</span><span class="sxs-lookup"><span data-stu-id="30a53-105">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="30a53-106">Tento dokument vysvětluje, jak spustit předem vytvořené image kontejneru s protokolem HTTPS.</span><span class="sxs-lookup"><span data-stu-id="30a53-106">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="30a53-107">Viz [Vývoj ASP.NET základních aplikací s Dockerem přes HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) pro scénáře vývoje.</span><span class="sxs-lookup"><span data-stu-id="30a53-107">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="30a53-108">Tato ukázka vyžaduje [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) nebo novější [klienta Dockeru](https://www.docker.com/products/docker).</span><span class="sxs-lookup"><span data-stu-id="30a53-108">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="30a53-109">Požadavky</span><span class="sxs-lookup"><span data-stu-id="30a53-109">Prerequisites</span></span>

<span data-ttu-id="30a53-110">Pro některé pokyny v tomto dokumentu je vyžadována sada [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) nebo novější.</span><span class="sxs-lookup"><span data-stu-id="30a53-110">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="30a53-111">Certifikáty</span><span class="sxs-lookup"><span data-stu-id="30a53-111">Certificates</span></span>

<span data-ttu-id="30a53-112">Pro [produkční hostování](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) pro doménu je vyžadován certifikát od [certifikační autority.](https://wikipedia.org/wiki/Certificate_authority)</span><span class="sxs-lookup"><span data-stu-id="30a53-112">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="30a53-113">[Let's Encrypt](https://letsencrypt.org/)je certifikační autorita, která nabízí bezplatné certifikáty.</span><span class="sxs-lookup"><span data-stu-id="30a53-113">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="30a53-114">Tento dokument používá [certifikáty pro vývoj s vlastním podpisem](https://wikipedia.org/wiki/Self-signed_certificate) pro hostování předem vytvořených bitových kopií přes `localhost`.</span><span class="sxs-lookup"><span data-stu-id="30a53-114">This document uses [self-signed development certificates](https://wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="30a53-115">Pokyny jsou podobné použití produkčních certifikátů.</span><span class="sxs-lookup"><span data-stu-id="30a53-115">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="30a53-116">Pro produkční certifikáty:</span><span class="sxs-lookup"><span data-stu-id="30a53-116">For production certificates:</span></span>

* <span data-ttu-id="30a53-117">Nástroj `dotnet dev-certs` není vyžadován.</span><span class="sxs-lookup"><span data-stu-id="30a53-117">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="30a53-118">Certifikáty nemusí být uloženy v umístění použitém v pokynech.</span><span class="sxs-lookup"><span data-stu-id="30a53-118">Certificates don't need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="30a53-119">Certifikáty uklánějte do libovolného umístění mimo adresář webu.</span><span class="sxs-lookup"><span data-stu-id="30a53-119">Store the certificates in any location outside the site directory.</span></span>

<span data-ttu-id="30a53-120">Pokyny obsažené v následující části svazku připojit certifikáty do kontejnerů pomocí `volumes` vlastnosti v *docker-compose.yml.*</span><span class="sxs-lookup"><span data-stu-id="30a53-120">The instructions contained in the following section volume mount certificates into containers using the `volumes` property in *docker-compose.yml.*</span></span> <span data-ttu-id="30a53-121">Můžete přidat certifikáty do imagí kontejneru s příkazem `COPY` v *Dockerfile*, ale není doporučeno.</span><span class="sxs-lookup"><span data-stu-id="30a53-121">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="30a53-122">Kopírování certifikátů do obrázku se nedoporučuje z následujících důvodů:</span><span class="sxs-lookup"><span data-stu-id="30a53-122">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="30a53-123">To ztěžuje použití stejné image pro testování s vývojářcertifikáty.</span><span class="sxs-lookup"><span data-stu-id="30a53-123">It makes it difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="30a53-124">To ztěžuje použití stejného obrázku pro hosting s produkčními certifikáty.</span><span class="sxs-lookup"><span data-stu-id="30a53-124">It makes it difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="30a53-125">Existuje značné riziko zveřejnění certifikátu.</span><span class="sxs-lookup"><span data-stu-id="30a53-125">There is significant risk of certificate disclosure.</span></span>

## <a name="starting-a-container-with-https-support-using-docker-compose"></a><span data-ttu-id="30a53-126">Spuštění kontejneru s podporou https pomocí docker compose</span><span class="sxs-lookup"><span data-stu-id="30a53-126">Starting a container with https support using docker compose</span></span>

<span data-ttu-id="30a53-127">Pro konfiguraci operačního systému postupujte podle následujících pokynů.</span><span class="sxs-lookup"><span data-stu-id="30a53-127">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="30a53-128">Windows pomocí linuxových kontejnerů</span><span class="sxs-lookup"><span data-stu-id="30a53-128">Windows using Linux containers</span></span>

<span data-ttu-id="30a53-129">Generovat certifikát a konfigurovat místní počítač:</span><span class="sxs-lookup"><span data-stu-id="30a53-129">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="30a53-130">V předchozích příkazech `{ password here }` nahraďte heslem.</span><span class="sxs-lookup"><span data-stu-id="30a53-130">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="30a53-131">Vytvořte soubor _docker-compose.debug.yml_ s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="30a53-131">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

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
<span data-ttu-id="30a53-132">Heslo zadané v souboru compose dockeru se musí shodovat s heslem použitým pro certifikát.</span><span class="sxs-lookup"><span data-stu-id="30a53-132">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="30a53-133">Spusťte kontejner s ASP.NET Core nakonfigurovaným pro protokol HTTPS:</span><span class="sxs-lookup"><span data-stu-id="30a53-133">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a><span data-ttu-id="30a53-134">macOS nebo Linux</span><span class="sxs-lookup"><span data-stu-id="30a53-134">macOS or Linux</span></span>

<span data-ttu-id="30a53-135">Generovat certifikát a konfigurovat místní počítač:</span><span class="sxs-lookup"><span data-stu-id="30a53-135">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="30a53-136">`dotnet dev-certs https --trust`je podporována pouze v systémech macOS a Windows.</span><span class="sxs-lookup"><span data-stu-id="30a53-136">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="30a53-137">Certifikátům na Linuxu musíte důvěřovat způsobem, který je podporován vaší distro.</span><span class="sxs-lookup"><span data-stu-id="30a53-137">You need to trust certificates on Linux in the way that is supported by your distro.</span></span> <span data-ttu-id="30a53-138">Je pravděpodobné, že budete muset důvěřovat certifikátu ve vašem prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="30a53-138">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="30a53-139">V předchozích příkazech `{ password here }` nahraďte heslem.</span><span class="sxs-lookup"><span data-stu-id="30a53-139">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="30a53-140">Vytvořte soubor _docker-compose.debug.yml_ s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="30a53-140">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

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
<span data-ttu-id="30a53-141">Heslo zadané v souboru compose dockeru se musí shodovat s heslem použitým pro certifikát.</span><span class="sxs-lookup"><span data-stu-id="30a53-141">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="30a53-142">Spusťte kontejner s ASP.NET Core nakonfigurovaným pro protokol HTTPS:</span><span class="sxs-lookup"><span data-stu-id="30a53-142">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a><span data-ttu-id="30a53-143">Windows pomocí kontejnerů Windows</span><span class="sxs-lookup"><span data-stu-id="30a53-143">Windows using Windows containers</span></span>

<span data-ttu-id="30a53-144">Generovat certifikát a konfigurovat místní počítač:</span><span class="sxs-lookup"><span data-stu-id="30a53-144">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="30a53-145">V předchozích příkazech `{ password here }` nahraďte heslem.</span><span class="sxs-lookup"><span data-stu-id="30a53-145">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="30a53-146">Vytvořte soubor _docker-compose.debug.yml_ s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="30a53-146">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

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
<span data-ttu-id="30a53-147">Heslo zadané v souboru compose dockeru se musí shodovat s heslem použitým pro certifikát.</span><span class="sxs-lookup"><span data-stu-id="30a53-147">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="30a53-148">Spusťte kontejner s ASP.NET Core nakonfigurovaným pro protokol HTTPS:</span><span class="sxs-lookup"><span data-stu-id="30a53-148">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

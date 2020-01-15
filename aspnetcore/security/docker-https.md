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
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a><span data-ttu-id="5b310-103">Hostování ASP.NET Core imagí pomocí Docker přes HTTPS</span><span class="sxs-lookup"><span data-stu-id="5b310-103">Hosting ASP.NET Core images with Docker over HTTPS</span></span>

<span data-ttu-id="5b310-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5b310-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5b310-105">ASP.NET Core [ve výchozím nastavení používá protokol HTTPS](/aspnet/core/security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="5b310-105">ASP.NET Core uses [HTTPS by default](/aspnet/core/security/enforcing-ssl).</span></span> <span data-ttu-id="5b310-106">[Protokol HTTPS](https://en.wikipedia.org/wiki/HTTPS) spoléhá na [certifikáty](https://en.wikipedia.org/wiki/Public_key_certificate) pro důvěryhodnost, identitu a šifrování.</span><span class="sxs-lookup"><span data-stu-id="5b310-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="5b310-107">Tento dokument vysvětluje, jak spustit předem připravené image kontejnerů pomocí protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="5b310-107">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="5b310-108">Další informace najdete v tématu [vývoj aplikací ASP.NET Core s využitím Docker přes protokol HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/aspnetcore-docker-https-development.md) pro vývojové scénáře.</span><span class="sxs-lookup"><span data-stu-id="5b310-108">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/aspnetcore-docker-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="5b310-109">Tato ukázka vyžaduje [docker 17,06](https://docs.docker.com/release-notes/docker-ce) nebo novější z [klienta Docker](https://www.docker.com/products/docker).</span><span class="sxs-lookup"><span data-stu-id="5b310-109">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5b310-110">Požadavky</span><span class="sxs-lookup"><span data-stu-id="5b310-110">Prerequisites</span></span>

<span data-ttu-id="5b310-111">Některé pokyny v tomto dokumentu vyžadují [sadu SDK .NET Core 2,2](https://www.microsoft.com/net/download) nebo novější.</span><span class="sxs-lookup"><span data-stu-id="5b310-111">The [.NET Core 2.2 SDK](https://www.microsoft.com/net/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="5b310-112">Certifikáty</span><span class="sxs-lookup"><span data-stu-id="5b310-112">Certificates</span></span>

<span data-ttu-id="5b310-113">Pro [hostování v provozu](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) v doméně je vyžadován certifikát od [certifikační autority](https://wikipedia.org/wiki/Certificate_authority) .</span><span class="sxs-lookup"><span data-stu-id="5b310-113">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="5b310-114">[Let's Encrypt](https://letsencrypt.org/) je certifikační autorita, která nabízí bezplatné certifikáty.</span><span class="sxs-lookup"><span data-stu-id="5b310-114">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="5b310-115">Tento dokument používá [certifikáty pro vývoj podepsaný svým držitelem](https://en.wikipedia.org/wiki/Self-signed_certificate) pro hostování předem sestavených imagí přes `localhost`.</span><span class="sxs-lookup"><span data-stu-id="5b310-115">This document uses [self-signed development certificates](https://en.wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="5b310-116">Pokyny jsou podobné použití produkčních certifikátů.</span><span class="sxs-lookup"><span data-stu-id="5b310-116">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="5b310-117">Pro produkční certifikáty:</span><span class="sxs-lookup"><span data-stu-id="5b310-117">For production certs:</span></span>

* <span data-ttu-id="5b310-118">Nástroj `dotnet dev-certs` není povinný.</span><span class="sxs-lookup"><span data-stu-id="5b310-118">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="5b310-119">Certifikáty není nutné ukládat v umístění, které jste použili v pokynech.</span><span class="sxs-lookup"><span data-stu-id="5b310-119">Certificates do not need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="5b310-120">Jakékoli umístění by mělo fungovat, i když ukládání certifikátů v adresáři webu se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="5b310-120">Any location should work, although storing certs within your site directory is not recommended.</span></span>

<span data-ttu-id="5b310-121">Pokyny obsažené v následujícím oddílu připojovat certifikáty do kontejnerů pomocí možnosti příkazového řádku Docker `-v`.</span><span class="sxs-lookup"><span data-stu-id="5b310-121">The instructions contained in the following section volume mount certificates into containers using Docker's `-v` command-line option.</span></span> <span data-ttu-id="5b310-122">Certifikáty můžete přidat do imagí kontejneru pomocí příkazu `COPY` v *souboru Dockerfile*, ale nedoporučujeme to.</span><span class="sxs-lookup"><span data-stu-id="5b310-122">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="5b310-123">Kopírování certifikátů do bitové kopie se nedoporučuje z následujících důvodů:</span><span class="sxs-lookup"><span data-stu-id="5b310-123">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="5b310-124">Pro testování pomocí certifikátů pro vývojáře je obtížné použít stejný obrázek.</span><span class="sxs-lookup"><span data-stu-id="5b310-124">It makes difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="5b310-125">Pro hostování s provozními certifikáty je obtížné použít stejný obrázek.</span><span class="sxs-lookup"><span data-stu-id="5b310-125">It makes difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="5b310-126">Existuje významné riziko odhalení certifikátu.</span><span class="sxs-lookup"><span data-stu-id="5b310-126">There is significant risk of certificate disclosure.</span></span>

## <a name="running-pre-built-container-images-with-https"></a><span data-ttu-id="5b310-127">Spouštění předem vytvořených imagí kontejneru s protokolem HTTPS</span><span class="sxs-lookup"><span data-stu-id="5b310-127">Running pre-built container images with HTTPS</span></span>

<span data-ttu-id="5b310-128">Pro konfiguraci operačního systému použijte následující pokyny.</span><span class="sxs-lookup"><span data-stu-id="5b310-128">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="5b310-129">Windows s použitím kontejnerů Linux</span><span class="sxs-lookup"><span data-stu-id="5b310-129">Windows using Linux containers</span></span>

<span data-ttu-id="5b310-130">Generovat certifikát a nakonfigurovat místní počítač:</span><span class="sxs-lookup"><span data-stu-id="5b310-130">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="5b310-131">V předchozích příkazech nahraďte `{ password here }` heslem.</span><span class="sxs-lookup"><span data-stu-id="5b310-131">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="5b310-132">Spusťte image kontejneru s ASP.NET Core nakonfigurovanou pro protokol HTTPS:</span><span class="sxs-lookup"><span data-stu-id="5b310-132">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="5b310-133">Heslo se musí shodovat s heslem použitým pro certifikát.</span><span class="sxs-lookup"><span data-stu-id="5b310-133">The password must match the password used for the certificate.</span></span>

### <a name="macos-or-linux"></a><span data-ttu-id="5b310-134">macOS nebo Linux</span><span class="sxs-lookup"><span data-stu-id="5b310-134">macOS or Linux</span></span>

<span data-ttu-id="5b310-135">Generovat certifikát a nakonfigurovat místní počítač:</span><span class="sxs-lookup"><span data-stu-id="5b310-135">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="5b310-136">`dotnet dev-certs https --trust` se podporuje jenom v macOS a Windows.</span><span class="sxs-lookup"><span data-stu-id="5b310-136">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="5b310-137">Musíte důvěřovat certifikátům na platformě Linux způsobem, který podporuje vaše distribuce.</span><span class="sxs-lookup"><span data-stu-id="5b310-137">You need to trust certs on Linux in the way that is supported by your distro.</span></span> <span data-ttu-id="5b310-138">Je možné, že certifikát budete muset důvěřovat v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="5b310-138">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="5b310-139">V předchozích příkazech nahraďte `{ password here }` heslem.</span><span class="sxs-lookup"><span data-stu-id="5b310-139">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="5b310-140">Spusťte image kontejneru s ASP.NET Core nakonfigurovanou pro protokol HTTPS:</span><span class="sxs-lookup"><span data-stu-id="5b310-140">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="5b310-141">Heslo se musí shodovat s heslem použitým pro certifikát.</span><span class="sxs-lookup"><span data-stu-id="5b310-141">The password must match the password used for the certificate.</span></span>

### <a name="windows-using-windows-containers"></a><span data-ttu-id="5b310-142">Windows s kontejnery Windows</span><span class="sxs-lookup"><span data-stu-id="5b310-142">Windows using Windows containers</span></span>

<span data-ttu-id="5b310-143">Generovat certifikát a nakonfigurovat místní počítač:</span><span class="sxs-lookup"><span data-stu-id="5b310-143">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="5b310-144">V předchozích příkazech nahraďte `{ password here }` heslem.</span><span class="sxs-lookup"><span data-stu-id="5b310-144">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="5b310-145">Spusťte image kontejneru s ASP.NET Core nakonfigurovanou pro protokol HTTPS:</span><span class="sxs-lookup"><span data-stu-id="5b310-145">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="5b310-146">Heslo se musí shodovat s heslem použitým pro certifikát.</span><span class="sxs-lookup"><span data-stu-id="5b310-146">The password must match the password used for the certificate.</span></span>

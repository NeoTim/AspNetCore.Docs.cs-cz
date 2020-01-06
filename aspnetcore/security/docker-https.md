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
ms.openlocfilehash: 47027033c0b7130f2d38d22c02a54945b2cc31b3
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75358910"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a><span data-ttu-id="e0923-103">Hostování ASP.NET Core imagí pomocí Docker přes HTTPS</span><span class="sxs-lookup"><span data-stu-id="e0923-103">Hosting ASP.NET Core images with Docker over HTTPS</span></span>

<span data-ttu-id="e0923-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e0923-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e0923-105">ASP.NET Core [ve výchozím nastavení používá protokol HTTPS](/aspnet/core/security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="e0923-105">ASP.NET Core uses [HTTPS by default](/aspnet/core/security/enforcing-ssl).</span></span> <span data-ttu-id="e0923-106">[Protokol HTTPS](https://en.wikipedia.org/wiki/HTTPS) spoléhá na [certifikáty](https://en.wikipedia.org/wiki/Public_key_certificate) pro důvěryhodnost, identitu a šifrování.</span><span class="sxs-lookup"><span data-stu-id="e0923-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="e0923-107">Tento dokument vysvětluje, jak spustit předem připravené image kontejnerů pomocí protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e0923-107">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="e0923-108">Další informace najdete v tématu [vývoj aplikací ASP.NET Core s využitím Docker přes protokol HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/aspnetcore-docker-https-development.md) pro vývojové scénáře.</span><span class="sxs-lookup"><span data-stu-id="e0923-108">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/aspnetcore-docker-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="e0923-109">Tato ukázka vyžaduje [docker 17,06](https://docs.docker.com/release-notes/docker-ce) nebo novější z [klienta Docker](https://www.docker.com/products/docker).</span><span class="sxs-lookup"><span data-stu-id="e0923-109">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e0923-110">Požadavky</span><span class="sxs-lookup"><span data-stu-id="e0923-110">Prerequisites</span></span>

<span data-ttu-id="e0923-111">Některé pokyny v tomto dokumentu vyžadují [sadu SDK .NET Core 2,2](https://www.microsoft.com/net/download) nebo novější.</span><span class="sxs-lookup"><span data-stu-id="e0923-111">The [.NET Core 2.2 SDK](https://www.microsoft.com/net/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="e0923-112">Certifikáty</span><span class="sxs-lookup"><span data-stu-id="e0923-112">Certificates</span></span>

<span data-ttu-id="e0923-113">Pro [hostování v provozu](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) v doméně je vyžadován certifikát od [certifikační autority](https://wikipedia.org/wiki/Certificate_authority) .</span><span class="sxs-lookup"><span data-stu-id="e0923-113">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="e0923-114">[Let's Encrypt](https://letsencrypt.org/) je certifikační autorita, která nabízí bezplatné certifikáty.</span><span class="sxs-lookup"><span data-stu-id="e0923-114">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="e0923-115">Tento dokument používá [certifikáty pro vývoj podepsaný svým držitelem](https://en.wikipedia.org/wiki/Self-signed_certificate) pro hostování předem sestavených imagí přes `localhost`.</span><span class="sxs-lookup"><span data-stu-id="e0923-115">This document uses [self-signed development certificates](https://en.wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="e0923-116">Pokyny jsou podobné použití produkčních certifikátů.</span><span class="sxs-lookup"><span data-stu-id="e0923-116">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="e0923-117">Pro produkční certifikáty:</span><span class="sxs-lookup"><span data-stu-id="e0923-117">For production certs:</span></span>

* <span data-ttu-id="e0923-118">Nástroj `dotnet dev-certs` není povinný.</span><span class="sxs-lookup"><span data-stu-id="e0923-118">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="e0923-119">Certifikáty není nutné ukládat v umístění, které jste použili v pokynech.</span><span class="sxs-lookup"><span data-stu-id="e0923-119">Certificates do not need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="e0923-120">Jakékoli umístění by mělo fungovat, i když ukládání certifikátů v adresáři webu se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="e0923-120">Any location should work, although storing certs within your site directory is not recommended.</span></span>

<span data-ttu-id="e0923-121">Pokyny pro připojení certifikátů do kontejnerů.</span><span class="sxs-lookup"><span data-stu-id="e0923-121">The instructions volume mount certificates into containers.</span></span> <span data-ttu-id="e0923-122">Certifikáty můžete přidat do imagí kontejneru pomocí příkazu `COPY` v *souboru Dockerfile*.</span><span class="sxs-lookup"><span data-stu-id="e0923-122">You can add certificates into container images with a `COPY` command in a *Dockerfile*.</span></span> <span data-ttu-id="e0923-123">Kopírování certifikátů do bitové kopie se nedoporučuje z následujících důvodů:</span><span class="sxs-lookup"><span data-stu-id="e0923-123">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="e0923-124">Pro testování pomocí certifikátů pro vývojáře je obtížné použít stejný obrázek.</span><span class="sxs-lookup"><span data-stu-id="e0923-124">It makes difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="e0923-125">Pro hostování s provozními certifikáty je obtížné použít stejný obrázek.</span><span class="sxs-lookup"><span data-stu-id="e0923-125">It makes difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="e0923-126">Existuje významné riziko odhalení certifikátu.</span><span class="sxs-lookup"><span data-stu-id="e0923-126">There is significant risk of certificate disclosure.</span></span>

## <a name="running-pre-built-container-images-with-https"></a><span data-ttu-id="e0923-127">Spouštění předem vytvořených imagí kontejneru s protokolem HTTPS</span><span class="sxs-lookup"><span data-stu-id="e0923-127">Running pre-built container images with HTTPS</span></span>

<span data-ttu-id="e0923-128">Pro konfiguraci operačního systému použijte následující pokyny.</span><span class="sxs-lookup"><span data-stu-id="e0923-128">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="e0923-129">Windows s použitím kontejnerů Linux</span><span class="sxs-lookup"><span data-stu-id="e0923-129">Windows using Linux containers</span></span>

<span data-ttu-id="e0923-130">Generovat certifikát a nakonfigurovat místní počítač:</span><span class="sxs-lookup"><span data-stu-id="e0923-130">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="e0923-131">V předchozích příkazech nahraďte `{ password here }` heslem.</span><span class="sxs-lookup"><span data-stu-id="e0923-131">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="e0923-132">Spusťte image kontejneru s ASP.NET Core nakonfigurovanou pro protokol HTTPS:</span><span class="sxs-lookup"><span data-stu-id="e0923-132">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="e0923-133">Heslo se musí shodovat s heslem použitým pro certifikát.</span><span class="sxs-lookup"><span data-stu-id="e0923-133">The password must match the password used for the certificate.</span></span>

### <a name="macos-or-linux"></a><span data-ttu-id="e0923-134">macOS nebo Linux</span><span class="sxs-lookup"><span data-stu-id="e0923-134">macOS or Linux</span></span>

<span data-ttu-id="e0923-135">Generovat certifikát a nakonfigurovat místní počítač:</span><span class="sxs-lookup"><span data-stu-id="e0923-135">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="e0923-136">`dotnet dev-certs https --trust` se podporuje jenom v macOS a Windows.</span><span class="sxs-lookup"><span data-stu-id="e0923-136">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="e0923-137">Musíte důvěřovat certifikátům na platformě Linux způsobem, který podporuje vaše distribuce.</span><span class="sxs-lookup"><span data-stu-id="e0923-137">You need to trust certs on Linux in the way that is supported by your distro.</span></span> <span data-ttu-id="e0923-138">Je možné, že certifikát budete muset důvěřovat v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="e0923-138">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="e0923-139">V předchozích příkazech nahraďte `{ password here }` heslem.</span><span class="sxs-lookup"><span data-stu-id="e0923-139">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="e0923-140">Spusťte image kontejneru s ASP.NET Core nakonfigurovanou pro protokol HTTPS:</span><span class="sxs-lookup"><span data-stu-id="e0923-140">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="e0923-141">Heslo se musí shodovat s heslem použitým pro certifikát.</span><span class="sxs-lookup"><span data-stu-id="e0923-141">The password must match the password used for the certificate.</span></span>

### <a name="windows-using-windows-containers"></a><span data-ttu-id="e0923-142">Windows s kontejnery Windows</span><span class="sxs-lookup"><span data-stu-id="e0923-142">Windows using Windows containers</span></span>

<span data-ttu-id="e0923-143">Generovat certifikát a nakonfigurovat místní počítač:</span><span class="sxs-lookup"><span data-stu-id="e0923-143">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="e0923-144">V předchozích příkazech nahraďte `{ password here }` heslem.</span><span class="sxs-lookup"><span data-stu-id="e0923-144">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="e0923-145">Spusťte image kontejneru s ASP.NET Core nakonfigurovanou pro protokol HTTPS:</span><span class="sxs-lookup"><span data-stu-id="e0923-145">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="e0923-146">Heslo se musí shodovat s heslem použitým pro certifikát.</span><span class="sxs-lookup"><span data-stu-id="e0923-146">The password must match the password used for the certificate.</span></span>

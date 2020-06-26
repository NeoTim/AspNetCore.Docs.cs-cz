---
title: Hostování ASP.NET Core imagí pomocí Docker přes HTTPS
author: rick-anderson
description: Naučte se hostovat image ASP.NET Core pomocí Docker přes HTTPS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/docker-https
ms.openlocfilehash: fd2338f433e1651fb974c95b293eaa3fb2009046
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403102"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a><span data-ttu-id="e4c10-103">Hostování ASP.NET Core imagí pomocí Docker přes HTTPS</span><span class="sxs-lookup"><span data-stu-id="e4c10-103">Hosting ASP.NET Core images with Docker over HTTPS</span></span>

<span data-ttu-id="e4c10-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e4c10-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e4c10-105">ASP.NET Core [ve výchozím nastavení používá protokol HTTPS](/aspnet/core/security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="e4c10-105">ASP.NET Core uses [HTTPS by default](/aspnet/core/security/enforcing-ssl).</span></span> <span data-ttu-id="e4c10-106">[Protokol HTTPS](https://en.wikipedia.org/wiki/HTTPS) spoléhá na [certifikáty](https://en.wikipedia.org/wiki/Public_key_certificate) pro důvěryhodnost, identitu a šifrování.</span><span class="sxs-lookup"><span data-stu-id="e4c10-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="e4c10-107">Tento dokument vysvětluje, jak spustit předem připravené image kontejnerů pomocí protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e4c10-107">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="e4c10-108">Další informace najdete v tématu [vývoj aplikací ASP.NET Core s využitím Docker přes protokol HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) pro vývojové scénáře.</span><span class="sxs-lookup"><span data-stu-id="e4c10-108">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="e4c10-109">Tato ukázka vyžaduje [docker 17,06](https://docs.docker.com/release-notes/docker-ce) nebo novější z [klienta Docker](https://www.docker.com/products/docker).</span><span class="sxs-lookup"><span data-stu-id="e4c10-109">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e4c10-110">Požadavky</span><span class="sxs-lookup"><span data-stu-id="e4c10-110">Prerequisites</span></span>

<span data-ttu-id="e4c10-111">Některé pokyny v tomto dokumentu vyžadují [sadu SDK .NET Core 2,2](https://dotnet.microsoft.com/download) nebo novější.</span><span class="sxs-lookup"><span data-stu-id="e4c10-111">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="e4c10-112">Certifikáty</span><span class="sxs-lookup"><span data-stu-id="e4c10-112">Certificates</span></span>

<span data-ttu-id="e4c10-113">Pro [hostování v provozu](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) v doméně je vyžadován certifikát od [certifikační autority](https://wikipedia.org/wiki/Certificate_authority) .</span><span class="sxs-lookup"><span data-stu-id="e4c10-113">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="e4c10-114">[Let's Encrypt](https://letsencrypt.org/)je certifikační autorita, která nabízí bezplatné certifikáty.</span><span class="sxs-lookup"><span data-stu-id="e4c10-114">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="e4c10-115">Tento dokument používá [certifikáty pro vývoj podepsaný svým držitelem](https://en.wikipedia.org/wiki/Self-signed_certificate) pro hostování předem vytvořených imagí `localhost` .</span><span class="sxs-lookup"><span data-stu-id="e4c10-115">This document uses [self-signed development certificates](https://en.wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="e4c10-116">Pokyny jsou podobné použití produkčních certifikátů.</span><span class="sxs-lookup"><span data-stu-id="e4c10-116">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="e4c10-117">Pro produkční certifikáty:</span><span class="sxs-lookup"><span data-stu-id="e4c10-117">For production certs:</span></span>

* <span data-ttu-id="e4c10-118">`dotnet dev-certs`Nástroj není povinný.</span><span class="sxs-lookup"><span data-stu-id="e4c10-118">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="e4c10-119">Certifikáty není nutné ukládat v umístění, které jste použili v pokynech.</span><span class="sxs-lookup"><span data-stu-id="e4c10-119">Certificates do not need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="e4c10-120">Jakékoli umístění by mělo fungovat, i když ukládání certifikátů v adresáři webu se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="e4c10-120">Any location should work, although storing certs within your site directory is not recommended.</span></span>

<span data-ttu-id="e4c10-121">Pokyny obsažené v následujícím oddílu připojovat certifikáty do kontejnerů pomocí `-v` Možnosti příkazového řádku Docker.</span><span class="sxs-lookup"><span data-stu-id="e4c10-121">The instructions contained in the following section volume mount certificates into containers using Docker's `-v` command-line option.</span></span> <span data-ttu-id="e4c10-122">Do imagí kontejnerů můžete přidat certifikáty pomocí `COPY` příkazu ve *souboru Dockerfile*, ale nedoporučuje se to.</span><span class="sxs-lookup"><span data-stu-id="e4c10-122">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="e4c10-123">Kopírování certifikátů do bitové kopie se nedoporučuje z následujících důvodů:</span><span class="sxs-lookup"><span data-stu-id="e4c10-123">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="e4c10-124">Pro testování pomocí certifikátů pro vývojáře je obtížné použít stejný obrázek.</span><span class="sxs-lookup"><span data-stu-id="e4c10-124">It makes difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="e4c10-125">Pro hostování s provozními certifikáty je obtížné použít stejný obrázek.</span><span class="sxs-lookup"><span data-stu-id="e4c10-125">It makes difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="e4c10-126">Existuje významné riziko odhalení certifikátu.</span><span class="sxs-lookup"><span data-stu-id="e4c10-126">There is significant risk of certificate disclosure.</span></span>

## <a name="running-pre-built-container-images-with-https"></a><span data-ttu-id="e4c10-127">Spouštění předem vytvořených imagí kontejneru s protokolem HTTPS</span><span class="sxs-lookup"><span data-stu-id="e4c10-127">Running pre-built container images with HTTPS</span></span>

<span data-ttu-id="e4c10-128">Pro konfiguraci operačního systému použijte následující pokyny.</span><span class="sxs-lookup"><span data-stu-id="e4c10-128">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="e4c10-129">Windows s použitím kontejnerů Linux</span><span class="sxs-lookup"><span data-stu-id="e4c10-129">Windows using Linux containers</span></span>

<span data-ttu-id="e4c10-130">Generovat certifikát a nakonfigurovat místní počítač:</span><span class="sxs-lookup"><span data-stu-id="e4c10-130">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="e4c10-131">V předchozích příkazech nahraďte `{ password here }` heslo.</span><span class="sxs-lookup"><span data-stu-id="e4c10-131">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="e4c10-132">Spusťte image kontejneru s ASP.NET Core nakonfigurovanou pro protokol HTTPS v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="e4c10-132">Run the container image with ASP.NET Core configured for HTTPS in a command shell:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="e4c10-133">Při použití [PowerShellu](/powershell/scripting/overview)nahraďte `%USERPROFILE%` `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="e4c10-133">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="e4c10-134">Heslo se musí shodovat s heslem použitým pro certifikát.</span><span class="sxs-lookup"><span data-stu-id="e4c10-134">The password must match the password used for the certificate.</span></span>

### <a name="macos-or-linux"></a><span data-ttu-id="e4c10-135">macOS nebo Linux</span><span class="sxs-lookup"><span data-stu-id="e4c10-135">macOS or Linux</span></span>

<span data-ttu-id="e4c10-136">Generovat certifikát a nakonfigurovat místní počítač:</span><span class="sxs-lookup"><span data-stu-id="e4c10-136">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="e4c10-137">`dotnet dev-certs https --trust`je podporován pouze v macOS a Windows.</span><span class="sxs-lookup"><span data-stu-id="e4c10-137">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="e4c10-138">Musíte důvěřovat certifikátům na platformě Linux způsobem, který podporuje vaše distribuce.</span><span class="sxs-lookup"><span data-stu-id="e4c10-138">You need to trust certs on Linux in the way that is supported by your distro.</span></span> <span data-ttu-id="e4c10-139">Je možné, že certifikát budete muset důvěřovat v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="e4c10-139">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="e4c10-140">V předchozích příkazech nahraďte `{ password here }` heslo.</span><span class="sxs-lookup"><span data-stu-id="e4c10-140">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="e4c10-141">Spusťte image kontejneru s ASP.NET Core nakonfigurovanou pro protokol HTTPS:</span><span class="sxs-lookup"><span data-stu-id="e4c10-141">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="e4c10-142">Heslo se musí shodovat s heslem použitým pro certifikát.</span><span class="sxs-lookup"><span data-stu-id="e4c10-142">The password must match the password used for the certificate.</span></span>

### <a name="windows-using-windows-containers"></a><span data-ttu-id="e4c10-143">Windows s kontejnery Windows</span><span class="sxs-lookup"><span data-stu-id="e4c10-143">Windows using Windows containers</span></span>

<span data-ttu-id="e4c10-144">Generovat certifikát a nakonfigurovat místní počítač:</span><span class="sxs-lookup"><span data-stu-id="e4c10-144">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="e4c10-145">V předchozích příkazech nahraďte `{ password here }` heslo.</span><span class="sxs-lookup"><span data-stu-id="e4c10-145">In the preceding commands, replace `{ password here }` with a password.</span></span> <span data-ttu-id="e4c10-146">Při použití [PowerShellu](/powershell/scripting/overview)nahraďte `%USERPROFILE%` `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="e4c10-146">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="e4c10-147">Spusťte image kontejneru s ASP.NET Core nakonfigurovanou pro protokol HTTPS:</span><span class="sxs-lookup"><span data-stu-id="e4c10-147">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="e4c10-148">Heslo se musí shodovat s heslem použitým pro certifikát.</span><span class="sxs-lookup"><span data-stu-id="e4c10-148">The password must match the password used for the certificate.</span></span> <span data-ttu-id="e4c10-149">Při použití [PowerShellu](/powershell/scripting/overview)nahraďte `%USERPROFILE%` `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="e4c10-149">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

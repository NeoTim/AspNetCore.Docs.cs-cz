---
title: Image Dockeru pro ASP.NET jádro
author: rick-anderson
description: Zjistěte, jak používat publikované image .NET Core Docker u registru Dockeru. Vytáhněte obrázky a vytvořte si vlastní obrázky.
ms.author: riande
ms.custom: mvc
ms.date: 01/15/2020
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: ced0cb7cbeed1b8811813a70035c2e0b42c3e35a
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440776"
---
# <a name="docker-images-for-aspnet-core"></a><span data-ttu-id="cdc4e-104">Image Dockeru pro ASP.NET jádro</span><span class="sxs-lookup"><span data-stu-id="cdc4e-104">Docker images for ASP.NET Core</span></span>

<span data-ttu-id="cdc4e-105">Tento kurz ukazuje, jak spustit aplikaci ASP.NET Core v kontejnerech Dockeru.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-105">This tutorial shows how to run an ASP.NET Core app in Docker containers.</span></span>

<span data-ttu-id="cdc4e-106">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="cdc4e-106">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="cdc4e-107">Další informace o inicicích Microsoft .NET Core Docker</span><span class="sxs-lookup"><span data-stu-id="cdc4e-107">Learn about Microsoft .NET Core Docker images</span></span>
> * <span data-ttu-id="cdc4e-108">Stažení ukázkové aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cdc4e-108">Download an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="cdc4e-109">Spuštění ukázkové aplikace místně</span><span class="sxs-lookup"><span data-stu-id="cdc4e-109">Run the sample app locally</span></span>
> * <span data-ttu-id="cdc4e-110">Spuštění ukázkové aplikace v kontejnerech Linuxu</span><span class="sxs-lookup"><span data-stu-id="cdc4e-110">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="cdc4e-111">Spuštění ukázkové aplikace v kontejnerech windows</span><span class="sxs-lookup"><span data-stu-id="cdc4e-111">Run the sample app in Windows containers</span></span>
> * <span data-ttu-id="cdc4e-112">Ruční sestavení a nasazení</span><span class="sxs-lookup"><span data-stu-id="cdc4e-112">Build and deploy manually</span></span>

## <a name="aspnet-core-docker-images"></a><span data-ttu-id="cdc4e-113">ASP.NET image Core Dockeru</span><span class="sxs-lookup"><span data-stu-id="cdc4e-113">ASP.NET Core Docker images</span></span>

<span data-ttu-id="cdc4e-114">V tomto kurzu si stáhnete ukázkovou aplikaci ASP.NET Core a spustíte ji v kontejnerech Dockeru.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-114">For this tutorial, you download an ASP.NET Core sample app and run it in Docker containers.</span></span> <span data-ttu-id="cdc4e-115">Ukázka pracuje s linuxovými i windows kontejnery.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-115">The sample works with both Linux and Windows containers.</span></span>

<span data-ttu-id="cdc4e-116">Ukázkový soubor Dockerfile používá [funkci vícefázového sestavení Dockeru](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) k sestavení a spuštění v různých kontejnerech.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-116">The sample Dockerfile uses the [Docker multi-stage build feature](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) to build and run in different containers.</span></span> <span data-ttu-id="cdc4e-117">Kontejnery sestavení a spuštění jsou vytvořeny z bitových kopií, které jsou k dispozici v Docker Hub u Microsoftu:</span><span class="sxs-lookup"><span data-stu-id="cdc4e-117">The build and run containers are created from images that are provided in Docker Hub by Microsoft:</span></span>

* `dotnet/core/sdk`

  <span data-ttu-id="cdc4e-118">Ukázka používá tento obrázek pro vytváření aplikace.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-118">The sample uses this image for building the app.</span></span> <span data-ttu-id="cdc4e-119">Obrázek obsahuje sadku .NET Core SDK, která obsahuje nástroje příkazového řádku (CLI).</span><span class="sxs-lookup"><span data-stu-id="cdc4e-119">The image contains the .NET Core SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="cdc4e-120">Bitová kopie je optimalizována pro místní vývoj, ladění a testování částí.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-120">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="cdc4e-121">Nástroje nainstalované pro vývoj a kompilaci z toho činí poměrně velký obraz.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-121">The tools installed for development and compilation make this a relatively large image.</span></span> 

* `dotnet/core/aspnet`

   <span data-ttu-id="cdc4e-122">Ukázka používá tento obrázek pro spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-122">The sample uses this image for running the app.</span></span> <span data-ttu-id="cdc4e-123">Bitová kopie obsahuje ASP.NET core runtime a knihovny a je optimalizovánpro spouštění aplikací v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-123">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="cdc4e-124">Image je navržena pro rychlost nasazení a spuštění aplikace a je relativně malá, takže je optimalizovaný výkon sítě od registru Dockeru po hostitele Dockeru.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-124">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="cdc4e-125">Do kontejneru se zkopírují pouze binární soubory a obsah potřebný ke spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-125">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="cdc4e-126">Obsah je připraven ke spuštění, což `Docker run` umožňuje nejrychlejší čas od spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-126">The contents are ready to run, enabling the fastest time from `Docker run` to app startup.</span></span> <span data-ttu-id="cdc4e-127">Kompilace dynamického kódu není v modelu Dockeru potřebná.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-127">Dynamic code compilation isn't needed in the Docker model.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cdc4e-128">Požadavky</span><span class="sxs-lookup"><span data-stu-id="cdc4e-128">Prerequisites</span></span>
::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="cdc4e-129">.NET Jádro 2.2 SDK</span><span class="sxs-lookup"><span data-stu-id="cdc4e-129">.NET Core 2.2 SDK</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="cdc4e-130">Sada .NET Core SDK 3.0</span><span class="sxs-lookup"><span data-stu-id="cdc4e-130">.NET Core SDK 3.0</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

* <span data-ttu-id="cdc4e-131">Klient Dockeru 18.03 nebo novější</span><span class="sxs-lookup"><span data-stu-id="cdc4e-131">Docker client 18.03 or later</span></span>

  * <span data-ttu-id="cdc4e-132">Linuxové distribuce</span><span class="sxs-lookup"><span data-stu-id="cdc4e-132">Linux distributions</span></span>
    * [<span data-ttu-id="cdc4e-133">CentOS</span><span class="sxs-lookup"><span data-stu-id="cdc4e-133">CentOS</span></span>](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [<span data-ttu-id="cdc4e-134">Debian</span><span class="sxs-lookup"><span data-stu-id="cdc4e-134">Debian</span></span>](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [<span data-ttu-id="cdc4e-135">Fedora</span><span class="sxs-lookup"><span data-stu-id="cdc4e-135">Fedora</span></span>](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [<span data-ttu-id="cdc4e-136">Ubuntu</span><span class="sxs-lookup"><span data-stu-id="cdc4e-136">Ubuntu</span></span>](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [<span data-ttu-id="cdc4e-137">macOS</span><span class="sxs-lookup"><span data-stu-id="cdc4e-137">macOS</span></span>](https://docs.docker.com/docker-for-mac/install/)
  * [<span data-ttu-id="cdc4e-138">Windows</span><span class="sxs-lookup"><span data-stu-id="cdc4e-138">Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

* [<span data-ttu-id="cdc4e-139">Git</span><span class="sxs-lookup"><span data-stu-id="cdc4e-139">Git</span></span>](https://git-scm.com/download)

## <a name="download-the-sample-app"></a><span data-ttu-id="cdc4e-140">Stažení ukázkové aplikace</span><span class="sxs-lookup"><span data-stu-id="cdc4e-140">Download the sample app</span></span>

* <span data-ttu-id="cdc4e-141">Stáhněte si ukázku klonováním [úložiště .NET Core Docker](https://github.com/dotnet/dotnet-docker):</span><span class="sxs-lookup"><span data-stu-id="cdc4e-141">Download the sample by cloning the [.NET Core Docker repository](https://github.com/dotnet/dotnet-docker):</span></span> 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a><span data-ttu-id="cdc4e-142">Místní spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="cdc4e-142">Run the app locally</span></span>

* <span data-ttu-id="cdc4e-143">Přejděte do složky projektu na *dotnet-docker/samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-143">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="cdc4e-144">Spusťte následující příkaz k sestavení a spuštění aplikace místně:</span><span class="sxs-lookup"><span data-stu-id="cdc4e-144">Run the following command to build and run the app locally:</span></span>

  ```dotnetcli
  dotnet run
  ```

* <span data-ttu-id="cdc4e-145">Přejděte `http://localhost:5000` do prohlížeče a otestujte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-145">Go to `http://localhost:5000` in a browser to test the app.</span></span>

* <span data-ttu-id="cdc4e-146">Stisknutím kláves Ctrl+C na příkazovém řádku aplikaci zastavte.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-146">Press Ctrl+C at the command prompt to stop the app.</span></span>

## <a name="run-in-a-linux-container"></a><span data-ttu-id="cdc4e-147">Spuštění v kontejneru Linuxu</span><span class="sxs-lookup"><span data-stu-id="cdc4e-147">Run in a Linux container</span></span>

* <span data-ttu-id="cdc4e-148">V klientovi Dockeru přepněte do kontejnerů Linuxu.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-148">In the Docker client, switch to Linux containers.</span></span>

* <span data-ttu-id="cdc4e-149">Přejděte do složky Dockerfile na *adrese dotnet-docker/samples/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-149">Navigate to the Dockerfile folder at *dotnet-docker/samples/aspnetapp*.</span></span>

* <span data-ttu-id="cdc4e-150">Spusťte následující příkazy pro sestavení a spuštění ukázky v Dockeru:</span><span class="sxs-lookup"><span data-stu-id="cdc4e-150">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  <span data-ttu-id="cdc4e-151">Argumenty `build` příkazu:</span><span class="sxs-lookup"><span data-stu-id="cdc4e-151">The `build` command arguments:</span></span>
  * <span data-ttu-id="cdc4e-152">Pojmenujte obraz aspnetapp.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-152">Name the image aspnetapp.</span></span>
  * <span data-ttu-id="cdc4e-153">Vyhledejte Dockerfile v aktuální složce (tečka na konci).</span><span class="sxs-lookup"><span data-stu-id="cdc4e-153">Look for the Dockerfile in the current folder (the period at the end).</span></span>

  <span data-ttu-id="cdc4e-154">Argumenty příkazu run:</span><span class="sxs-lookup"><span data-stu-id="cdc4e-154">The run command arguments:</span></span>
  * <span data-ttu-id="cdc4e-155">Přidělit pseudo-TTY a udržet ji otevřenou, i když není připojen.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-155">Allocate a pseudo-TTY and keep it open even if not attached.</span></span> <span data-ttu-id="cdc4e-156">(Stejný efekt `--interactive --tty`jako .)</span><span class="sxs-lookup"><span data-stu-id="cdc4e-156">(Same effect as `--interactive --tty`.)</span></span>
  * <span data-ttu-id="cdc4e-157">Automaticky odeberte kontejner při jeho ukončení.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-157">Automatically remove the container when it exits.</span></span>
  * <span data-ttu-id="cdc4e-158">Mapovat port 5000 v místním počítači na port 80 v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-158">Map port 5000 on the local machine to port 80 in the container.</span></span>
  * <span data-ttu-id="cdc4e-159">Pojmenujte aspnetcore_sample kontejneru.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-159">Name the container aspnetcore_sample.</span></span>
  * <span data-ttu-id="cdc4e-160">Určete obraz aspnetapp.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-160">Specify the aspnetapp image.</span></span>

* <span data-ttu-id="cdc4e-161">Přejděte `http://localhost:5000` do prohlížeče a otestujte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-161">Go to `http://localhost:5000` in a browser to test the app.</span></span>

## <a name="run-in-a-windows-container"></a><span data-ttu-id="cdc4e-162">Spuštění v kontejneru windows</span><span class="sxs-lookup"><span data-stu-id="cdc4e-162">Run in a Windows container</span></span>

* <span data-ttu-id="cdc4e-163">V klientovi Dockeru přepněte do kontejnerů windows.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-163">In the Docker client, switch to Windows containers.</span></span>

<span data-ttu-id="cdc4e-164">Přejděte do složky `dotnet-docker/samples/aspnetapp`souborů dockeru na adrese .</span><span class="sxs-lookup"><span data-stu-id="cdc4e-164">Navigate to the docker file folder at `dotnet-docker/samples/aspnetapp`.</span></span>

* <span data-ttu-id="cdc4e-165">Spusťte následující příkazy pro sestavení a spuštění ukázky v Dockeru:</span><span class="sxs-lookup"><span data-stu-id="cdc4e-165">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* <span data-ttu-id="cdc4e-166">U kontejnerů se systémem Windows potřebujete IP `http://localhost:5000` adresu kontejneru (procházení nebude fungovat):</span><span class="sxs-lookup"><span data-stu-id="cdc4e-166">For Windows containers, you need the IP address of the container (browsing to `http://localhost:5000` won't work):</span></span>
  * <span data-ttu-id="cdc4e-167">Otevřete jiný příkazový řádek.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-167">Open up another command prompt.</span></span>
  * <span data-ttu-id="cdc4e-168">Spuštěním `docker ps` zobrazíte spuštěné kontejnery.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-168">Run `docker ps` to see the running containers.</span></span> <span data-ttu-id="cdc4e-169">Ověřte, zda je kontejner "aspnetcore_sample".</span><span class="sxs-lookup"><span data-stu-id="cdc4e-169">Verify that the "aspnetcore_sample" container is there.</span></span>
  * <span data-ttu-id="cdc4e-170">Spuštěním `docker exec aspnetcore_sample ipconfig` zobrazíte IP adresu kontejneru.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-170">Run `docker exec aspnetcore_sample ipconfig` to display the IP address of the container.</span></span> <span data-ttu-id="cdc4e-171">Výstup z příkazu vypadá takto:</span><span class="sxs-lookup"><span data-stu-id="cdc4e-171">The output from the command looks like this example:</span></span>

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* <span data-ttu-id="cdc4e-172">Zkopírujte adresu IPv4 kontejneru (například 172.29.245.43) a vložte ji do adresního řádku prohlížeče a otestujte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-172">Copy the container IPv4 address (for example, 172.29.245.43) and paste into the browser address bar to test the app.</span></span>

## <a name="build-and-deploy-manually"></a><span data-ttu-id="cdc4e-173">Ruční sestavení a nasazení</span><span class="sxs-lookup"><span data-stu-id="cdc4e-173">Build and deploy manually</span></span>

<span data-ttu-id="cdc4e-174">V některých případech můžete chtít nasadit aplikaci do kontejneru zkopírováním do něj soubory aplikace, které jsou potřebné v době běhu.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-174">In some scenarios, you might want to deploy an app to a container by copying to it the application files that are needed at run time.</span></span> <span data-ttu-id="cdc4e-175">Tato část ukazuje, jak nasadit ručně.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-175">This section shows how to deploy manually.</span></span>

* <span data-ttu-id="cdc4e-176">Přejděte do složky projektu na *dotnet-docker/samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-176">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="cdc4e-177">Spusťte příkaz [publikovat dotnet:](/dotnet/core/tools/dotnet-publish)</span><span class="sxs-lookup"><span data-stu-id="cdc4e-177">Run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  <span data-ttu-id="cdc4e-178">Argumenty příkazu:</span><span class="sxs-lookup"><span data-stu-id="cdc4e-178">The command arguments:</span></span>
  * <span data-ttu-id="cdc4e-179">Vytvořte aplikaci v režimu vydání (výchozí je režim ladění).</span><span class="sxs-lookup"><span data-stu-id="cdc4e-179">Build the application in release mode (the default is debug mode).</span></span>
  * <span data-ttu-id="cdc4e-180">Vytvořte soubory v *publikované* složce.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-180">Create the files in the *published* folder.</span></span>

* <span data-ttu-id="cdc4e-181">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-181">Run the application.</span></span>

  * <span data-ttu-id="cdc4e-182">Windows:</span><span class="sxs-lookup"><span data-stu-id="cdc4e-182">Windows:</span></span>

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * <span data-ttu-id="cdc4e-183">Linux:</span><span class="sxs-lookup"><span data-stu-id="cdc4e-183">Linux:</span></span>

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* <span data-ttu-id="cdc4e-184">Přejděte `http://localhost:5000` na domovskou stránku.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-184">Browse to `http://localhost:5000` to see the home page.</span></span>

<span data-ttu-id="cdc4e-185">Chcete-li použít ručně publikovanou aplikaci v kontejneru Dockeru, vytvořte nový soubor Dockerfile a pomocí příkazu `docker build .` vytvořte kontejner.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-185">To use the manually published application within a Docker container, create a new Dockerfile and use the `docker build .` command to build the container.</span></span>

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="cdc4e-186">Soubor Dockerfile</span><span class="sxs-lookup"><span data-stu-id="cdc4e-186">The Dockerfile</span></span>

<span data-ttu-id="cdc4e-187">Zde je *Dockerfile* používá `docker build` příkaz, který jste spustili dříve.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-187">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="cdc4e-188">Používá `dotnet publish` stejným způsobem jako v této části k sestavení a nasazení.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-188">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out


FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="cdc4e-189">Soubor Dockerfile</span><span class="sxs-lookup"><span data-stu-id="cdc4e-189">The Dockerfile</span></span>

<span data-ttu-id="cdc4e-190">Zde je *Dockerfile* používá `docker build` příkaz, který jste spustili dříve.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-190">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="cdc4e-191">Používá `dotnet publish` stejným způsobem jako v této části k sestavení a nasazení.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-191">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:3.0 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out


FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

::: moniker-end

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

## <a name="additional-resources"></a><span data-ttu-id="cdc4e-192">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="cdc4e-192">Additional resources</span></span>

* [<span data-ttu-id="cdc4e-193">Docker sestavení příkaz</span><span class="sxs-lookup"><span data-stu-id="cdc4e-193">Docker build command</span></span>](https://docs.docker.com/engine/reference/commandline/build)
* [<span data-ttu-id="cdc4e-194">Docker spustit, příkaz</span><span class="sxs-lookup"><span data-stu-id="cdc4e-194">Docker run command</span></span>](https://docs.docker.com/engine/reference/commandline/run)
* <span data-ttu-id="cdc4e-195">[ASP.NET ukázka Core Dockeru](https://github.com/dotnet/dotnet-docker) (ta použitá v tomto kurzu.)</span><span class="sxs-lookup"><span data-stu-id="cdc4e-195">[ASP.NET Core Docker sample](https://github.com/dotnet/dotnet-docker) (The one used in this tutorial.)</span></span>
* [<span data-ttu-id="cdc4e-196">Konfigurace ASP.NET Core pro práci s proxy servery a vyrovnáváním zatížení</span><span class="sxs-lookup"><span data-stu-id="cdc4e-196">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](/aspnet/core/host-and-deploy/proxy-load-balancer)
* [<span data-ttu-id="cdc4e-197">Práce s nástroji Visual Studio Docker</span><span class="sxs-lookup"><span data-stu-id="cdc4e-197">Working with Visual Studio Docker Tools</span></span>](https://docs.microsoft.com/aspnet/core/publishing/visual-studio-tools-for-docker)
* [<span data-ttu-id="cdc4e-198">Ladění pomocí kódu sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cdc4e-198">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)
* [<span data-ttu-id="cdc4e-199">GC pomocí Dockeru a malých kontejnerů</span><span class="sxs-lookup"><span data-stu-id="cdc4e-199">GC using Docker and small containers</span></span>](xref:performance/memory#sc)

## <a name="next-steps"></a><span data-ttu-id="cdc4e-200">Další kroky</span><span class="sxs-lookup"><span data-stu-id="cdc4e-200">Next steps</span></span>

<span data-ttu-id="cdc4e-201">Úložiště Git, které obsahuje ukázkovou aplikaci, obsahuje také dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="cdc4e-201">The Git repository that contains the sample app also includes documentation.</span></span> <span data-ttu-id="cdc4e-202">Přehled prostředků dostupných v úložišti naleznete v [souboru README](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span><span class="sxs-lookup"><span data-stu-id="cdc4e-202">For an overview of the resources available in the repository, see [the README file](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span></span> <span data-ttu-id="cdc4e-203">Zejména se dozvíte, jak implementovat HTTPS:</span><span class="sxs-lookup"><span data-stu-id="cdc4e-203">In particular, learn how to implement HTTPS:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="cdc4e-204">Vývoj základních aplikací ASP.NET s Dockerem přes protokol HTTPS</span><span class="sxs-lookup"><span data-stu-id="cdc4e-204">Developing ASP.NET Core Applications with Docker over HTTPS</span></span>](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)

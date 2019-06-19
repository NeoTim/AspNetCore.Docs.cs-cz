---
title: Image dockeru pro ASP.NET Core
author: tdykstra
description: Další informace o použití publikovaných imagí Dockeru .NET Core z registru Dockeru. Stažení imagí a vytvoření vlastních imagí.
ms.author: tdykstra
ms.custom: mvc
ms.date: 06/18/2019
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: ea96ae6d36c7e8320ea49e666a807ece72645865
ms.sourcegitcommit: a1283d486ac1dcedfc7ea302e1cc882833e2c515
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67207796"
---
# <a name="docker-images-for-aspnet-core"></a><span data-ttu-id="03fd4-104">Image dockeru pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="03fd4-104">Docker images for ASP.NET Core</span></span>

<span data-ttu-id="03fd4-105">Tento kurz ukazuje postupy při spuštění aplikace ASP.NET Core v kontejnerech Dockeru.</span><span class="sxs-lookup"><span data-stu-id="03fd4-105">This tutorial shows how to run an ASP.NET Core app in Docker containers.</span></span>

<span data-ttu-id="03fd4-106">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="03fd4-106">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="03fd4-107">Další informace o imagích Dockeru rozhraní Microsoft .NET Core</span><span class="sxs-lookup"><span data-stu-id="03fd4-107">Learn about Microsoft .NET Core Docker images</span></span> 
> * <span data-ttu-id="03fd4-108">Stažení ukázkové aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="03fd4-108">Download an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="03fd4-109">Místní spuštění ukázkové aplikace</span><span class="sxs-lookup"><span data-stu-id="03fd4-109">Run the sample app locally</span></span>
> * <span data-ttu-id="03fd4-110">Spuštění ukázkové aplikace v Linuxové kontejnery</span><span class="sxs-lookup"><span data-stu-id="03fd4-110">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="03fd4-111">Spuštění ukázkové aplikace v kontejnerech Windows</span><span class="sxs-lookup"><span data-stu-id="03fd4-111">Run the sample app in Windows containers</span></span>
> * <span data-ttu-id="03fd4-112">Sestavování a nasazování ručně</span><span class="sxs-lookup"><span data-stu-id="03fd4-112">Build and deploy manually</span></span>

## <a name="aspnet-core-docker-images"></a><span data-ttu-id="03fd4-113">ASP.NET Core Docker images</span><span class="sxs-lookup"><span data-stu-id="03fd4-113">ASP.NET Core Docker images</span></span>

<span data-ttu-id="03fd4-114">Pro účely tohoto kurzu stáhněte si ukázková aplikace ASP.NET Core a spouštění v kontejnerech Dockeru.</span><span class="sxs-lookup"><span data-stu-id="03fd4-114">For this tutorial, you download an ASP.NET Core sample app and run it in Docker containers.</span></span> <span data-ttu-id="03fd4-115">Ukázka funguje s kontejnery Windows i Linux.</span><span class="sxs-lookup"><span data-stu-id="03fd4-115">The sample works with both Linux and Windows containers.</span></span>

<span data-ttu-id="03fd4-116">Ukázkový soubor Dockerfile použije [Docker vícefázových sestavení funkce](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) sestavte a spusťte v různých kontejnerů.</span><span class="sxs-lookup"><span data-stu-id="03fd4-116">The sample Dockerfile uses the [Docker multi-stage build feature](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) to build and run in different containers.</span></span> <span data-ttu-id="03fd4-117">Sestavení a spuštění kontejnerů jsou vytvořené z imagí, které jsou k dispozici v Docker Hubu společností Microsoft:</span><span class="sxs-lookup"><span data-stu-id="03fd4-117">The build and run containers are created from images that are provided in Docker Hub by Microsoft:</span></span>

* `dotnet/core/sdk`

  <span data-ttu-id="03fd4-118">Ukázka používá tuto bitovou kopii pro sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="03fd4-118">The sample uses this image for building the app.</span></span> <span data-ttu-id="03fd4-119">Bitová kopie obsahuje sady .NET Core SDK, která zahrnuje nástroje příkazového řádku (CLI).</span><span class="sxs-lookup"><span data-stu-id="03fd4-119">The image contains the .NET Core SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="03fd4-120">Na obrázku je optimalizovaná pro místní vývoj, ladění a testování částí.</span><span class="sxs-lookup"><span data-stu-id="03fd4-120">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="03fd4-121">Nástroje nainstalované pro vývoj a sestavování vytvořit poměrně velký obrázek.</span><span class="sxs-lookup"><span data-stu-id="03fd4-121">The tools installed for development and compilation make this a relatively large image.</span></span> 

* `dotnet/core/aspnet` 

   <span data-ttu-id="03fd4-122">Ukázka používá tuto bitovou kopii pro spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="03fd4-122">The sample uses this image for running the app.</span></span> <span data-ttu-id="03fd4-123">Na obrázku obsahuje modul runtime ASP.NET Core a knihovny a je optimalizovaná pro spouštění aplikací v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="03fd4-123">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="03fd4-124">Je určená pro rychlost nasazení a spuštění aplikace a image je poměrně malý, proto je optimalizován výkon sítě z registru Dockeru do Docker hostitele.</span><span class="sxs-lookup"><span data-stu-id="03fd4-124">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="03fd4-125">Binární soubory a obsah potřebný ke spuštění aplikace se zkopírují do kontejneru.</span><span class="sxs-lookup"><span data-stu-id="03fd4-125">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="03fd4-126">Obsah je připraveno ke spuštění, povolení nejrychlejší doba od `Docker run` pro spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="03fd4-126">The contents are ready to run, enabling the fastest time from `Docker run` to app startup.</span></span> <span data-ttu-id="03fd4-127">Dynamický kód kompilace, není nutná v modelu Dockeru.</span><span class="sxs-lookup"><span data-stu-id="03fd4-127">Dynamic code compilation isn't needed in the Docker model.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="03fd4-128">Požadavky</span><span class="sxs-lookup"><span data-stu-id="03fd4-128">Prerequisites</span></span>

* [<span data-ttu-id="03fd4-129">.NET Core 2.2 SDK</span><span class="sxs-lookup"><span data-stu-id="03fd4-129">.NET Core 2.2 SDK</span></span>](https://www.microsoft.com/net/core)

* <span data-ttu-id="03fd4-130">Klient docker 18.03 nebo novější</span><span class="sxs-lookup"><span data-stu-id="03fd4-130">Docker client 18.03 or later</span></span>

  * <span data-ttu-id="03fd4-131">Linuxové distribuce</span><span class="sxs-lookup"><span data-stu-id="03fd4-131">Linux distributions</span></span>
    * [<span data-ttu-id="03fd4-132">CentOS</span><span class="sxs-lookup"><span data-stu-id="03fd4-132">CentOS</span></span>](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [<span data-ttu-id="03fd4-133">Debian</span><span class="sxs-lookup"><span data-stu-id="03fd4-133">Debian</span></span>](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [<span data-ttu-id="03fd4-134">Fedora</span><span class="sxs-lookup"><span data-stu-id="03fd4-134">Fedora</span></span>](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [<span data-ttu-id="03fd4-135">Ubuntu</span><span class="sxs-lookup"><span data-stu-id="03fd4-135">Ubuntu</span></span>](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [<span data-ttu-id="03fd4-136">macOS</span><span class="sxs-lookup"><span data-stu-id="03fd4-136">macOS</span></span>](https://docs.docker.com/docker-for-mac/install/)
  * [<span data-ttu-id="03fd4-137">Windows</span><span class="sxs-lookup"><span data-stu-id="03fd4-137">Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

* [<span data-ttu-id="03fd4-138">Git</span><span class="sxs-lookup"><span data-stu-id="03fd4-138">Git</span></span>](https://git-scm.com/download)

## <a name="download-the-sample-app"></a><span data-ttu-id="03fd4-139">Stáhněte si ukázkovou aplikaci</span><span class="sxs-lookup"><span data-stu-id="03fd4-139">Download the sample app</span></span>

* <span data-ttu-id="03fd4-140">Stáhněte si ukázku naklonováním [úložiště .NET Core Dockeru](https://github.com/dotnet/dotnet-docker):</span><span class="sxs-lookup"><span data-stu-id="03fd4-140">Download the sample by cloning the [.NET Core Docker repository](https://github.com/dotnet/dotnet-docker):</span></span> 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a><span data-ttu-id="03fd4-141">Místní spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="03fd4-141">Run the app locally</span></span>

* <span data-ttu-id="03fd4-142">Přejděte do složky projektu v *dotnet dockeru/samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="03fd4-142">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="03fd4-143">Spuštěním následujícího příkazu sestavte a spusťte aplikaci místně:</span><span class="sxs-lookup"><span data-stu-id="03fd4-143">Run the following command to build and run the app locally:</span></span>

  ```console
  dotnet run
  ```

* <span data-ttu-id="03fd4-144">Přejděte na `http://localhost:5000` v prohlížeči a otestujte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="03fd4-144">Go to `http://localhost:5000` in a browser to test the app.</span></span>

* <span data-ttu-id="03fd4-145">Na příkazovém řádku se zastavit aplikaci stisknutím Ctrl + C.</span><span class="sxs-lookup"><span data-stu-id="03fd4-145">Press Ctrl+C at the command prompt to stop the app.</span></span>

## <a name="run-in-a-linux-container"></a><span data-ttu-id="03fd4-146">Spuštění v kontejneru Linuxu</span><span class="sxs-lookup"><span data-stu-id="03fd4-146">Run in a Linux container</span></span>

* <span data-ttu-id="03fd4-147">V klientovi Docker přepněte na kontejnery Linux.</span><span class="sxs-lookup"><span data-stu-id="03fd4-147">In the Docker client, switch to Linux containers.</span></span>

* <span data-ttu-id="03fd4-148">Přejděte do složky soubor Dockerfile v *dotnet dockeru/samples/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="03fd4-148">Navigate to the Dockerfile folder at *dotnet-docker/samples/aspnetapp*.</span></span>

* <span data-ttu-id="03fd4-149">Spusťte následující příkazy k vytvoření a spuštění ukázky v Dockeru:</span><span class="sxs-lookup"><span data-stu-id="03fd4-149">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  <span data-ttu-id="03fd4-150">`build` Argumenty příkazu:</span><span class="sxs-lookup"><span data-stu-id="03fd4-150">The `build` command arguments:</span></span>
  * <span data-ttu-id="03fd4-151">Název aspnetapp bitové kopie.</span><span class="sxs-lookup"><span data-stu-id="03fd4-151">Name the image aspnetapp.</span></span>
  * <span data-ttu-id="03fd4-152">Vyhledejte soubor Dockerfile v aktuální složce (tečky na konci).</span><span class="sxs-lookup"><span data-stu-id="03fd4-152">Look for the Dockerfile in the current folder (the period at the end).</span></span>

  <span data-ttu-id="03fd4-153">Argumenty spuštění příkazu:</span><span class="sxs-lookup"><span data-stu-id="03fd4-153">The run command arguments:</span></span>
  * <span data-ttu-id="03fd4-154">Přidělit pseudo-TTY a nechte ho otevřený i v případě, že nejsou připojené.</span><span class="sxs-lookup"><span data-stu-id="03fd4-154">Allocate a pseudo-TTY and keep it open even if not attached.</span></span> <span data-ttu-id="03fd4-155">(Stejný vliv jako `--interactive --tty`.)</span><span class="sxs-lookup"><span data-stu-id="03fd4-155">(Same effect as `--interactive --tty`.)</span></span>
  * <span data-ttu-id="03fd4-156">Automaticky odstranit kontejneru při jeho ukončení.</span><span class="sxs-lookup"><span data-stu-id="03fd4-156">Automatically remove the container when it exits.</span></span>
  * <span data-ttu-id="03fd4-157">Mapování portu 5000 místního počítače na port 80 v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="03fd4-157">Map port 5000 on the local machine to port 80 in the container.</span></span>
  * <span data-ttu-id="03fd4-158">Název kontejneru aspnetcore_sample.</span><span class="sxs-lookup"><span data-stu-id="03fd4-158">Name the container aspnetcore_sample.</span></span>
  * <span data-ttu-id="03fd4-159">Zadejte bitovou kopii aspnetapp.</span><span class="sxs-lookup"><span data-stu-id="03fd4-159">Specify the aspnetapp image.</span></span>

* <span data-ttu-id="03fd4-160">Přejděte na `http://localhost:5000` v prohlížeči a otestujte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="03fd4-160">Go to `http://localhost:5000` in a browser to test the app.</span></span>

## <a name="run-in-a-windows-container"></a><span data-ttu-id="03fd4-161">Spuštění v kontejneru Windows</span><span class="sxs-lookup"><span data-stu-id="03fd4-161">Run in a Windows container</span></span>

* <span data-ttu-id="03fd4-162">V klientovi Docker přepněte na kontejnery Windows.</span><span class="sxs-lookup"><span data-stu-id="03fd4-162">In the Docker client, switch to Windows containers.</span></span>

<span data-ttu-id="03fd4-163">Přejděte do složky souboru docker na `dotnet-docker/samples/aspnetapp`.</span><span class="sxs-lookup"><span data-stu-id="03fd4-163">Navigate to the docker file folder at `dotnet-docker/samples/aspnetapp`.</span></span>

* <span data-ttu-id="03fd4-164">Spusťte následující příkazy k vytvoření a spuštění ukázky v Dockeru:</span><span class="sxs-lookup"><span data-stu-id="03fd4-164">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* <span data-ttu-id="03fd4-165">Pro kontejnery Windows, budete potřebovat IP adresu kontejneru (přejdete na adresu `http://localhost:5000` nebude fungovat):</span><span class="sxs-lookup"><span data-stu-id="03fd4-165">For Windows containers, you need the IP address of the container (browsing to `http://localhost:5000` won't work):</span></span>
  * <span data-ttu-id="03fd4-166">Otevřete jiného příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="03fd4-166">Open up another command prompt.</span></span>
  * <span data-ttu-id="03fd4-167">Spustit `docker ps` zobrazte spuštěné kontejnery.</span><span class="sxs-lookup"><span data-stu-id="03fd4-167">Run `docker ps` to see the running containers.</span></span> <span data-ttu-id="03fd4-168">Ověřte, že "aspnetcore_sample" kontejneru existuje.</span><span class="sxs-lookup"><span data-stu-id="03fd4-168">Verify that the "aspnetcore_sample" container is there.</span></span>
  * <span data-ttu-id="03fd4-169">Spustit `docker exec aspnetcore_sample ipconfig` zobrazit IP adresu kontejneru.</span><span class="sxs-lookup"><span data-stu-id="03fd4-169">Run `docker exec aspnetcore_sample ipconfig` to display the IP address of the container.</span></span> <span data-ttu-id="03fd4-170">Výstup příkazu by měl vypadat jako v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="03fd4-170">The output from the command looks like this example:</span></span>

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* <span data-ttu-id="03fd4-171">Kontejner adresa protokolu IPv4 (například 172.29.245.43) zkopírujte a vložte do adresního řádku prohlížeče a otestujte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="03fd4-171">Copy the container IPv4 address (for example, 172.29.245.43) and paste into the browser address bar to test the app.</span></span>

## <a name="build-and-deploy-manually"></a><span data-ttu-id="03fd4-172">Sestavování a nasazování ručně</span><span class="sxs-lookup"><span data-stu-id="03fd4-172">Build and deploy manually</span></span>

<span data-ttu-id="03fd4-173">V některých případech můžete chtít nasadit aplikace do kontejneru tak, že zkopírujete do něj soubory aplikace, které jsou potřebné v době běhu.</span><span class="sxs-lookup"><span data-stu-id="03fd4-173">In some scenarios, you might want to deploy an app to a container by copying to it the application files that are needed at run time.</span></span> <span data-ttu-id="03fd4-174">Tato část ukazuje, jak ručně nasadit.</span><span class="sxs-lookup"><span data-stu-id="03fd4-174">This section shows how to deploy manually.</span></span>

* <span data-ttu-id="03fd4-175">Přejděte do složky projektu v *dotnet dockeru/samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="03fd4-175">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="03fd4-176">Spustit [dotnet publikovat](/dotnet/core/tools/dotnet-publish) příkaz:</span><span class="sxs-lookup"><span data-stu-id="03fd4-176">Run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

  ```console
  dotnet publish -c Release -o published
  ```

  <span data-ttu-id="03fd4-177">Argumenty příkazu:</span><span class="sxs-lookup"><span data-stu-id="03fd4-177">The command arguments:</span></span>
  * <span data-ttu-id="03fd4-178">Vytvoření aplikace v režimu vydání (výchozí hodnota je režim ladění).</span><span class="sxs-lookup"><span data-stu-id="03fd4-178">Build the application in release mode (the default is debug mode).</span></span>
  * <span data-ttu-id="03fd4-179">Vytvoření souborů v *publikované* složky.</span><span class="sxs-lookup"><span data-stu-id="03fd4-179">Create the files in the *published* folder.</span></span>

* <span data-ttu-id="03fd4-180">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="03fd4-180">Run the application.</span></span>

  * <span data-ttu-id="03fd4-181">Windows:</span><span class="sxs-lookup"><span data-stu-id="03fd4-181">Windows:</span></span>

    ```console
    dotnet published\aspnetapp.dll
    ```

  * <span data-ttu-id="03fd4-182">Linux:</span><span class="sxs-lookup"><span data-stu-id="03fd4-182">Linux:</span></span>

    ```bash
    dotnet published/aspnetapp.dll
    ```

* <span data-ttu-id="03fd4-183">Přejděte do `http://localhost:5000` zobrazíte na domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="03fd4-183">Browse to `http://localhost:5000` to see the home page.</span></span>

### <a name="the-dockerfile"></a><span data-ttu-id="03fd4-184">Soubor Dockerfile</span><span class="sxs-lookup"><span data-stu-id="03fd4-184">The Dockerfile</span></span>

<span data-ttu-id="03fd4-185">Tady je soubor Dockerfile používané `docker build` příkaz předtím.</span><span class="sxs-lookup"><span data-stu-id="03fd4-185">Here's the Dockerfile used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="03fd4-186">Používá `dotnet publish` stejně jako jste to udělali v této části sestavíte a nasadíte.</span><span class="sxs-lookup"><span data-stu-id="03fd4-186">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```console
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

## <a name="additional-resources"></a><span data-ttu-id="03fd4-187">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="03fd4-187">Additional resources</span></span>

* [<span data-ttu-id="03fd4-188">Příkaz sestavení dockeru</span><span class="sxs-lookup"><span data-stu-id="03fd4-188">Docker build command</span></span>](https://docs.docker.com/engine/reference/commandline/build)
* [<span data-ttu-id="03fd4-189">Docker, spusťte příkaz</span><span class="sxs-lookup"><span data-stu-id="03fd4-189">Docker run command</span></span>](https://docs.docker.com/engine/reference/commandline/run)
* <span data-ttu-id="03fd4-190">[Ukázka technologie ASP.NET Core Docker](https://github.com/dotnet/dotnet-docker) (použitým v tomto kurzu.)</span><span class="sxs-lookup"><span data-stu-id="03fd4-190">[ASP.NET Core Docker sample](https://github.com/dotnet/dotnet-docker) (The one used in this tutorial.)</span></span>
* [<span data-ttu-id="03fd4-191">Konfigurace ASP.NET Core práci se servery proxy a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="03fd4-191">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](/aspnet/core/host-and-deploy/proxy-load-balancer)
* [<span data-ttu-id="03fd4-192">Práce s nástroji Dockeru pro Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03fd4-192">Working with Visual Studio Docker Tools</span></span>](https://docs.microsoft.com/aspnet/core/publishing/visual-studio-tools-for-docker)
* [<span data-ttu-id="03fd4-193">Ladění ve Visual Studiu Code</span><span class="sxs-lookup"><span data-stu-id="03fd4-193">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers) 

## <a name="next-steps"></a><span data-ttu-id="03fd4-194">Další kroky</span><span class="sxs-lookup"><span data-stu-id="03fd4-194">Next steps</span></span>

<span data-ttu-id="03fd4-195">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="03fd4-195">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="03fd4-196">Dozvěděli jste se o imagích Dockeru rozhraní Microsoft .NET Core</span><span class="sxs-lookup"><span data-stu-id="03fd4-196">Learned about Microsoft .NET Core Docker images</span></span> 
> * <span data-ttu-id="03fd4-197">Stažení ukázkové aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="03fd4-197">Downloaded an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="03fd4-198">Místní spuštění ukázkové aplikace</span><span class="sxs-lookup"><span data-stu-id="03fd4-198">Run the sample app locally</span></span>
> * <span data-ttu-id="03fd4-199">Spuštění ukázkové aplikace v Linuxové kontejnery</span><span class="sxs-lookup"><span data-stu-id="03fd4-199">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="03fd4-200">Spusťte ukázku pomocí v kontejnerech Windows</span><span class="sxs-lookup"><span data-stu-id="03fd4-200">Run the sample with in Windows containers</span></span>
> * <span data-ttu-id="03fd4-201">Sestavíte a nasadíte ručně</span><span class="sxs-lookup"><span data-stu-id="03fd4-201">Built and deployed manually</span></span>

<span data-ttu-id="03fd4-202">Úložiště Git, která obsahuje ukázkovou aplikaci také obsahuje dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="03fd4-202">The Git repository that contains the sample app also includes documentation.</span></span> <span data-ttu-id="03fd4-203">Přehled dostupných prostředků v úložišti, naleznete v tématu [v souboru README](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span><span class="sxs-lookup"><span data-stu-id="03fd4-203">For an overview of the resources available in the repository, see [the README file](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span></span> <span data-ttu-id="03fd4-204">Zejména zjistěte, jak implementovat HTTPS:</span><span class="sxs-lookup"><span data-stu-id="03fd4-204">In particular, learn how to implement HTTPS:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="03fd4-205">Vývoj aplikací ASP.NET Core s využitím Dockeru přes protokol HTTPS</span><span class="sxs-lookup"><span data-stu-id="03fd4-205">Developing ASP.NET Core Applications with Docker over HTTPS</span></span>](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/aspnetcore-docker-https-development.md)

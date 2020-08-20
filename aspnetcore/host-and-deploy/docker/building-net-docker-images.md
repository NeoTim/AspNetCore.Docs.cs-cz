---
title: Image Docker pro ASP.NET Core
author: rick-anderson
description: Naučte se používat publikované image Docker .NET Core z registru Docker. Vyžádání imagí a sestavení vlastních imagí.
ms.author: riande
ms.custom: mvc
ms.date: 05/12/2020
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
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: dfb807d5e8d8b949feb0ea87f7f6bcecd04817e8
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627322"
---
# <a name="docker-images-for-aspnet-core"></a><span data-ttu-id="913dc-104">Image Docker pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="913dc-104">Docker images for ASP.NET Core</span></span>

<span data-ttu-id="913dc-105">V tomto kurzu se dozvíte, jak spustit aplikaci ASP.NET Core v kontejnerech Docker.</span><span class="sxs-lookup"><span data-stu-id="913dc-105">This tutorial shows how to run an ASP.NET Core app in Docker containers.</span></span>

<span data-ttu-id="913dc-106">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="913dc-106">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="913dc-107">Další informace o imagích Docker Microsoft .NET Core</span><span class="sxs-lookup"><span data-stu-id="913dc-107">Learn about Microsoft .NET Core Docker images</span></span>
> * <span data-ttu-id="913dc-108">Stažení ukázkové aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="913dc-108">Download an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="913dc-109">Spustit ukázkovou aplikaci místně</span><span class="sxs-lookup"><span data-stu-id="913dc-109">Run the sample app locally</span></span>
> * <span data-ttu-id="913dc-110">Spuštění ukázkové aplikace v kontejnerech Linux</span><span class="sxs-lookup"><span data-stu-id="913dc-110">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="913dc-111">Spuštění ukázkové aplikace v kontejnerech Windows</span><span class="sxs-lookup"><span data-stu-id="913dc-111">Run the sample app in Windows containers</span></span>
> * <span data-ttu-id="913dc-112">Ruční sestavení a nasazení</span><span class="sxs-lookup"><span data-stu-id="913dc-112">Build and deploy manually</span></span>

## <a name="aspnet-core-docker-images"></a><span data-ttu-id="913dc-113">Image ASP.NET Core Docker</span><span class="sxs-lookup"><span data-stu-id="913dc-113">ASP.NET Core Docker images</span></span>

<span data-ttu-id="913dc-114">Pro tento kurz si stáhnete ukázkovou aplikaci ASP.NET Core a spustíte ji v kontejnerech Docker.</span><span class="sxs-lookup"><span data-stu-id="913dc-114">For this tutorial, you download an ASP.NET Core sample app and run it in Docker containers.</span></span> <span data-ttu-id="913dc-115">Ukázka funguje s kontejnery pro Linux i Windows.</span><span class="sxs-lookup"><span data-stu-id="913dc-115">The sample works with both Linux and Windows containers.</span></span>

<span data-ttu-id="913dc-116">Vzorový souboru Dockerfile využívá [funkci buildu pro více fází](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) pro sestavování a spouštění v různých kontejnerech.</span><span class="sxs-lookup"><span data-stu-id="913dc-116">The sample Dockerfile uses the [Docker multi-stage build feature](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) to build and run in different containers.</span></span> <span data-ttu-id="913dc-117">Kontejnery sestavení a spuštění jsou vytvořeny z imagí, které jsou k dispozici v Docker Hub od společnosti Microsoft:</span><span class="sxs-lookup"><span data-stu-id="913dc-117">The build and run containers are created from images that are provided in Docker Hub by Microsoft:</span></span>

* `dotnet/core/sdk`

  <span data-ttu-id="913dc-118">Ukázka používá tuto image k sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="913dc-118">The sample uses this image for building the app.</span></span> <span data-ttu-id="913dc-119">Obrázek obsahuje .NET Core SDK, která obsahuje nástroje příkazového řádku (CLI).</span><span class="sxs-lookup"><span data-stu-id="913dc-119">The image contains the .NET Core SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="913dc-120">Obrázek je optimalizován pro místní vývoj, ladění a testování částí.</span><span class="sxs-lookup"><span data-stu-id="913dc-120">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="913dc-121">Nástroje nainstalované pro vývoj a kompilaci vytvářejí poměrně velký obrázek.</span><span class="sxs-lookup"><span data-stu-id="913dc-121">The tools installed for development and compilation make this a relatively large image.</span></span> 

* `dotnet/core/aspnet`

   <span data-ttu-id="913dc-122">Ukázka používá tuto image ke spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="913dc-122">The sample uses this image for running the app.</span></span> <span data-ttu-id="913dc-123">Image obsahuje modul runtime a knihovny ASP.NET Core a je optimalizovaný pro spuštěné aplikace v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="913dc-123">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="913dc-124">Bitová kopie je navržena pro rychlost nasazení a spouštění aplikací, takže je optimalizován výkon sítě z registru Docker na hostitele Docker.</span><span class="sxs-lookup"><span data-stu-id="913dc-124">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="913dc-125">Do kontejneru se zkopírují jenom binární soubory a obsah potřebný ke spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="913dc-125">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="913dc-126">Obsah je připravený ke spuštění, což umožňuje nejrychlejší čas od `Docker run` spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="913dc-126">The contents are ready to run, enabling the fastest time from `Docker run` to app startup.</span></span> <span data-ttu-id="913dc-127">Dynamická kompilace kódu není v modelu Docker nutná.</span><span class="sxs-lookup"><span data-stu-id="913dc-127">Dynamic code compilation isn't needed in the Docker model.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="913dc-128">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="913dc-128">Prerequisites</span></span>
::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="913dc-129">Sada .NET Core 2,2 SDK</span><span class="sxs-lookup"><span data-stu-id="913dc-129">.NET Core 2.2 SDK</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="913dc-130">.NET Core SDK 3,0</span><span class="sxs-lookup"><span data-stu-id="913dc-130">.NET Core SDK 3.0</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

* <span data-ttu-id="913dc-131">Klient Docker 18,03 nebo novější</span><span class="sxs-lookup"><span data-stu-id="913dc-131">Docker client 18.03 or later</span></span>

  * <span data-ttu-id="913dc-132">Distribuce systému Linux</span><span class="sxs-lookup"><span data-stu-id="913dc-132">Linux distributions</span></span>
    * [<span data-ttu-id="913dc-133">CentOS</span><span class="sxs-lookup"><span data-stu-id="913dc-133">CentOS</span></span>](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [<span data-ttu-id="913dc-134">Debian</span><span class="sxs-lookup"><span data-stu-id="913dc-134">Debian</span></span>](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [<span data-ttu-id="913dc-135">Fedora</span><span class="sxs-lookup"><span data-stu-id="913dc-135">Fedora</span></span>](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [<span data-ttu-id="913dc-136">Ubuntu</span><span class="sxs-lookup"><span data-stu-id="913dc-136">Ubuntu</span></span>](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [<span data-ttu-id="913dc-137">macOS</span><span class="sxs-lookup"><span data-stu-id="913dc-137">macOS</span></span>](https://docs.docker.com/docker-for-mac/install/)
  * [<span data-ttu-id="913dc-138">Windows</span><span class="sxs-lookup"><span data-stu-id="913dc-138">Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

* [<span data-ttu-id="913dc-139">Git</span><span class="sxs-lookup"><span data-stu-id="913dc-139">Git</span></span>](https://git-scm.com/download)

## <a name="download-the-sample-app"></a><span data-ttu-id="913dc-140">Stažení ukázkové aplikace</span><span class="sxs-lookup"><span data-stu-id="913dc-140">Download the sample app</span></span>

* <span data-ttu-id="913dc-141">Stáhněte si ukázku klonování [úložiště Docker .NET Core](https://github.com/dotnet/dotnet-docker):</span><span class="sxs-lookup"><span data-stu-id="913dc-141">Download the sample by cloning the [.NET Core Docker repository](https://github.com/dotnet/dotnet-docker):</span></span> 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a><span data-ttu-id="913dc-142">Místní spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="913dc-142">Run the app locally</span></span>

* <span data-ttu-id="913dc-143">Přejděte do složky projektu v *dotnet-Docker/Samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="913dc-143">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="913dc-144">Spuštěním následujícího příkazu Sestavte a spusťte aplikaci místně:</span><span class="sxs-lookup"><span data-stu-id="913dc-144">Run the following command to build and run the app locally:</span></span>

  ```dotnetcli
  dotnet run
  ```

* <span data-ttu-id="913dc-145">`http://localhost:5000`V prohlížeči otestujte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="913dc-145">Go to `http://localhost:5000` in a browser to test the app.</span></span>

* <span data-ttu-id="913dc-146">Stisknutím kombinace kláves CTRL + C na příkazovém řádku zastavte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="913dc-146">Press Ctrl+C at the command prompt to stop the app.</span></span>

## <a name="run-in-a-linux-container"></a><span data-ttu-id="913dc-147">Spuštění v kontejneru Linux</span><span class="sxs-lookup"><span data-stu-id="913dc-147">Run in a Linux container</span></span>

* <span data-ttu-id="913dc-148">V klientovi Docker přepněte na kontejnery Linux.</span><span class="sxs-lookup"><span data-stu-id="913dc-148">In the Docker client, switch to Linux containers.</span></span>

* <span data-ttu-id="913dc-149">Přejděte do složky souboru Dockerfile v *dotnet-Docker/Samples/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="913dc-149">Navigate to the Dockerfile folder at *dotnet-docker/samples/aspnetapp*.</span></span>

* <span data-ttu-id="913dc-150">Spuštěním následujících příkazů Sestavte a spusťte ukázku v Docker:</span><span class="sxs-lookup"><span data-stu-id="913dc-150">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  <span data-ttu-id="913dc-151">`build`Argumenty příkazu:</span><span class="sxs-lookup"><span data-stu-id="913dc-151">The `build` command arguments:</span></span>
  * <span data-ttu-id="913dc-152">Pojmenujte bitovou kopii aspnetapp.</span><span class="sxs-lookup"><span data-stu-id="913dc-152">Name the image aspnetapp.</span></span>
  * <span data-ttu-id="913dc-153">Vyhledejte souboru Dockerfile v aktuální složce (tečka na konci).</span><span class="sxs-lookup"><span data-stu-id="913dc-153">Look for the Dockerfile in the current folder (the period at the end).</span></span>

  <span data-ttu-id="913dc-154">Argumenty příkazu Run:</span><span class="sxs-lookup"><span data-stu-id="913dc-154">The run command arguments:</span></span>
  * <span data-ttu-id="913dc-155">Přidělte pseudo-TTY a nechte ho otevřený i v případě, že není připojený.</span><span class="sxs-lookup"><span data-stu-id="913dc-155">Allocate a pseudo-TTY and keep it open even if not attached.</span></span> <span data-ttu-id="913dc-156">(Stejný efekt jako `--interactive --tty` .)</span><span class="sxs-lookup"><span data-stu-id="913dc-156">(Same effect as `--interactive --tty`.)</span></span>
  * <span data-ttu-id="913dc-157">Kontejner se po ukončení automaticky odebere.</span><span class="sxs-lookup"><span data-stu-id="913dc-157">Automatically remove the container when it exits.</span></span>
  * <span data-ttu-id="913dc-158">Namapujte port 5000 na místním počítači na port 80 v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="913dc-158">Map port 5000 on the local machine to port 80 in the container.</span></span>
  * <span data-ttu-id="913dc-159">Pojmenujte aspnetcore_sample kontejneru.</span><span class="sxs-lookup"><span data-stu-id="913dc-159">Name the container aspnetcore_sample.</span></span>
  * <span data-ttu-id="913dc-160">Zadejte bitovou kopii aspnetapp.</span><span class="sxs-lookup"><span data-stu-id="913dc-160">Specify the aspnetapp image.</span></span>

* <span data-ttu-id="913dc-161">`http://localhost:5000`V prohlížeči otestujte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="913dc-161">Go to `http://localhost:5000` in a browser to test the app.</span></span>

## <a name="run-in-a-windows-container"></a><span data-ttu-id="913dc-162">Spuštění v kontejneru Windows</span><span class="sxs-lookup"><span data-stu-id="913dc-162">Run in a Windows container</span></span>

* <span data-ttu-id="913dc-163">V klientovi Docker přepněte do kontejnerů Windows.</span><span class="sxs-lookup"><span data-stu-id="913dc-163">In the Docker client, switch to Windows containers.</span></span>

<span data-ttu-id="913dc-164">Přejděte do složky Docker File v `dotnet-docker/samples/aspnetapp` .</span><span class="sxs-lookup"><span data-stu-id="913dc-164">Navigate to the docker file folder at `dotnet-docker/samples/aspnetapp`.</span></span>

* <span data-ttu-id="913dc-165">Spuštěním následujících příkazů Sestavte a spusťte ukázku v Docker:</span><span class="sxs-lookup"><span data-stu-id="913dc-165">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* <span data-ttu-id="913dc-166">V případě kontejnerů Windows budete potřebovat IP adresu kontejneru (procházení `http://localhost:5000` nebude fungovat):</span><span class="sxs-lookup"><span data-stu-id="913dc-166">For Windows containers, you need the IP address of the container (browsing to `http://localhost:5000` won't work):</span></span>
  * <span data-ttu-id="913dc-167">Otevřete další příkazový řádek.</span><span class="sxs-lookup"><span data-stu-id="913dc-167">Open up another command prompt.</span></span>
  * <span data-ttu-id="913dc-168">Spusťte, `docker ps` aby se zobrazily spuštěné kontejnery.</span><span class="sxs-lookup"><span data-stu-id="913dc-168">Run `docker ps` to see the running containers.</span></span> <span data-ttu-id="913dc-169">Ověřte, že je kontejner aspnetcore_sample.</span><span class="sxs-lookup"><span data-stu-id="913dc-169">Verify that the "aspnetcore_sample" container is there.</span></span>
  * <span data-ttu-id="913dc-170">Spusťte `docker exec aspnetcore_sample ipconfig` pro zobrazení IP adresy kontejneru.</span><span class="sxs-lookup"><span data-stu-id="913dc-170">Run `docker exec aspnetcore_sample ipconfig` to display the IP address of the container.</span></span> <span data-ttu-id="913dc-171">Výstup příkazu vypadá jako v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="913dc-171">The output from the command looks like this example:</span></span>

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* <span data-ttu-id="913dc-172">Zkopírujte adresu IPv4 kontejneru (například 172.29.245.43) a vložte ji do adresního řádku prohlížeče, aby se aplikace otestovala.</span><span class="sxs-lookup"><span data-stu-id="913dc-172">Copy the container IPv4 address (for example, 172.29.245.43) and paste into the browser address bar to test the app.</span></span>

## <a name="build-and-deploy-manually"></a><span data-ttu-id="913dc-173">Ruční sestavení a nasazení</span><span class="sxs-lookup"><span data-stu-id="913dc-173">Build and deploy manually</span></span>

<span data-ttu-id="913dc-174">V některých scénářích můžete chtít nasadit aplikaci do kontejneru tak, že do ní nakopírujete soubory aplikace, které jsou potřeba v době běhu.</span><span class="sxs-lookup"><span data-stu-id="913dc-174">In some scenarios, you might want to deploy an app to a container by copying to it the application files that are needed at run time.</span></span> <span data-ttu-id="913dc-175">V této části se dozvíte, jak ručně nasadit.</span><span class="sxs-lookup"><span data-stu-id="913dc-175">This section shows how to deploy manually.</span></span>

* <span data-ttu-id="913dc-176">Přejděte do složky projektu v *dotnet-Docker/Samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="913dc-176">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="913dc-177">Spusťte příkaz [dotnet Publish](/dotnet/core/tools/dotnet-publish) :</span><span class="sxs-lookup"><span data-stu-id="913dc-177">Run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  <span data-ttu-id="913dc-178">Argumenty příkazu:</span><span class="sxs-lookup"><span data-stu-id="913dc-178">The command arguments:</span></span>
  * <span data-ttu-id="913dc-179">Sestavte aplikaci v režimu vydání (výchozí je režim ladění).</span><span class="sxs-lookup"><span data-stu-id="913dc-179">Build the application in release mode (the default is debug mode).</span></span>
  * <span data-ttu-id="913dc-180">Vytvořte soubory v *publikované* složce.</span><span class="sxs-lookup"><span data-stu-id="913dc-180">Create the files in the *published* folder.</span></span>

* <span data-ttu-id="913dc-181">Aplikaci spusťte.</span><span class="sxs-lookup"><span data-stu-id="913dc-181">Run the application.</span></span>

  * <span data-ttu-id="913dc-182">Windows:</span><span class="sxs-lookup"><span data-stu-id="913dc-182">Windows:</span></span>

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * <span data-ttu-id="913dc-183">Linux:</span><span class="sxs-lookup"><span data-stu-id="913dc-183">Linux:</span></span>

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* <span data-ttu-id="913dc-184">Přejděte k `http://localhost:5000` zobrazení domovské stránky.</span><span class="sxs-lookup"><span data-stu-id="913dc-184">Browse to `http://localhost:5000` to see the home page.</span></span>

<span data-ttu-id="913dc-185">Chcete-li použít manuálně publikovanou aplikaci v kontejneru Docker, vytvořte novou souboru Dockerfile a pomocí `docker build .` příkazu Sestavte kontejner.</span><span class="sxs-lookup"><span data-stu-id="913dc-185">To use the manually published application within a Docker container, create a new Dockerfile and use the `docker build .` command to build the container.</span></span>

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="913dc-186">Souboru Dockerfile</span><span class="sxs-lookup"><span data-stu-id="913dc-186">The Dockerfile</span></span>

<span data-ttu-id="913dc-187">Tady je *souboru Dockerfile* , `docker build` který používá příkaz, který jste spustili dříve.</span><span class="sxs-lookup"><span data-stu-id="913dc-187">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="913dc-188">`dotnet publish`Pro sestavování a nasazování používá stejný způsob jako v tomto oddílu.</span><span class="sxs-lookup"><span data-stu-id="913dc-188">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

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

### <a name="the-dockerfile"></a><span data-ttu-id="913dc-189">Souboru Dockerfile</span><span class="sxs-lookup"><span data-stu-id="913dc-189">The Dockerfile</span></span>

<span data-ttu-id="913dc-190">Tady je *souboru Dockerfile* , `docker build` který používá příkaz, který jste spustili dříve.</span><span class="sxs-lookup"><span data-stu-id="913dc-190">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="913dc-191">`dotnet publish`Pro sestavování a nasazování používá stejný způsob jako v tomto oddílu.</span><span class="sxs-lookup"><span data-stu-id="913dc-191">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

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

<span data-ttu-id="913dc-192">Jak je uvedeno v předchozím souboru Dockerfile, `*.csproj` soubory se zkopírují a obnoví jako samostatné *vrstvy*.</span><span class="sxs-lookup"><span data-stu-id="913dc-192">As noted in the preceding Dockerfile, the `*.csproj` files are copied and restored as distinct *layers*.</span></span> <span data-ttu-id="913dc-193">Když `docker build` příkaz vytvoří image, použije vestavěnou mezipaměť.</span><span class="sxs-lookup"><span data-stu-id="913dc-193">When the `docker build` command builds an image, it uses a built-in cache.</span></span> <span data-ttu-id="913dc-194">Pokud se `*.csproj` soubory od `docker build` posledního spuštění příkazu nezměnily, `dotnet restore` příkaz se nemusí znovu spustit.</span><span class="sxs-lookup"><span data-stu-id="913dc-194">If the `*.csproj` files haven't changed since the `docker build` command last ran, the `dotnet restore` command doesn't need to run again.</span></span> <span data-ttu-id="913dc-195">Místo toho se znovu použije vestavěná mezipaměť pro odpovídající `dotnet restore` vrstvu.</span><span class="sxs-lookup"><span data-stu-id="913dc-195">Instead, the built-in cache for the corresponding `dotnet restore` layer is reused.</span></span> <span data-ttu-id="913dc-196">Další informace najdete v tématu [osvědčené postupy pro psaní fázemi](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span><span class="sxs-lookup"><span data-stu-id="913dc-196">For more information, see [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="913dc-197">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="913dc-197">Additional resources</span></span>

* [<span data-ttu-id="913dc-198">Docker – příkaz buildu</span><span class="sxs-lookup"><span data-stu-id="913dc-198">Docker build command</span></span>](https://docs.docker.com/engine/reference/commandline/build)
* [<span data-ttu-id="913dc-199">Příkaz Spustit jako Docker</span><span class="sxs-lookup"><span data-stu-id="913dc-199">Docker run command</span></span>](https://docs.docker.com/engine/reference/commandline/run)
* <span data-ttu-id="913dc-200">[Ukázka docker ASP.NET Core](https://github.com/dotnet/dotnet-docker) (ten, který jste použili v tomto kurzu.)</span><span class="sxs-lookup"><span data-stu-id="913dc-200">[ASP.NET Core Docker sample](https://github.com/dotnet/dotnet-docker) (The one used in this tutorial.)</span></span>
* [<span data-ttu-id="913dc-201">Konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="913dc-201">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](/aspnet/core/host-and-deploy/proxy-load-balancer)
* [<span data-ttu-id="913dc-202">Práce s nástroji Docker sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="913dc-202">Working with Visual Studio Docker Tools</span></span>](https://docs.microsoft.com/aspnet/core/publishing/visual-studio-tools-for-docker)
* [<span data-ttu-id="913dc-203">Ladění pomocí Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="913dc-203">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)
* [<span data-ttu-id="913dc-204">GC s použitím Docker a malých kontejnerů</span><span class="sxs-lookup"><span data-stu-id="913dc-204">GC using Docker and small containers</span></span>](xref:performance/memory#sc)

## <a name="next-steps"></a><span data-ttu-id="913dc-205">Další kroky</span><span class="sxs-lookup"><span data-stu-id="913dc-205">Next steps</span></span>

<span data-ttu-id="913dc-206">Úložiště Git, které obsahuje ukázkovou aplikaci, také obsahuje dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="913dc-206">The Git repository that contains the sample app also includes documentation.</span></span> <span data-ttu-id="913dc-207">Přehled prostředků, které jsou k dispozici v úložišti, najdete v [souboru Readme](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span><span class="sxs-lookup"><span data-stu-id="913dc-207">For an overview of the resources available in the repository, see [the README file](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span></span> <span data-ttu-id="913dc-208">Konkrétně se dozvíte, jak implementovat protokol HTTPS:</span><span class="sxs-lookup"><span data-stu-id="913dc-208">In particular, learn how to implement HTTPS:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="913dc-209">Vývoj aplikací ASP.NET Core pomocí Docker přes HTTPS</span><span class="sxs-lookup"><span data-stu-id="913dc-209">Developing ASP.NET Core Applications with Docker over HTTPS</span></span>](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)

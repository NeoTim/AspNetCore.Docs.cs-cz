---
title: Nástroje kontejnerů visual studia s jádrem ASP.NET
author: spboyer
description: Přečtěte si, jak pomocí nástrojů Visual Studio a Dockeru pro Windows kontejnerizovat aplikaci ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 09/12/2018
uid: host-and-deploy/docker/visual-studio-tools-for-docker
ms.openlocfilehash: ba79f0af8192ad9e8b263d4304ccc0df36c50f00
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440750"
---
# <a name="visual-studio-container-tools-with-aspnet-core"></a><span data-ttu-id="5d22a-103">Nástroje kontejnerů visual studia s jádrem ASP.NET</span><span class="sxs-lookup"><span data-stu-id="5d22a-103">Visual Studio Container Tools with ASP.NET Core</span></span>

<span data-ttu-id="5d22a-104">Visual Studio 2017 a novější verze podporují vytváření, ladění a spouštění kontejnerizovaných ASP.NET základních aplikací zaměřených na elementační .NET Core.</span><span class="sxs-lookup"><span data-stu-id="5d22a-104">Visual Studio 2017 and later versions support building, debugging, and running containerized ASP.NET Core apps targeting .NET Core.</span></span> <span data-ttu-id="5d22a-105">Jsou podporovány kontejnery Windows i Linux.</span><span class="sxs-lookup"><span data-stu-id="5d22a-105">Both Windows and Linux containers are supported.</span></span>

<span data-ttu-id="5d22a-106">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="5d22a-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5d22a-107">Požadavky</span><span class="sxs-lookup"><span data-stu-id="5d22a-107">Prerequisites</span></span>

* [<span data-ttu-id="5d22a-108">Docker pro Windows</span><span class="sxs-lookup"><span data-stu-id="5d22a-108">Docker for Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)
* <span data-ttu-id="5d22a-109">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s **úlohou pro vývoj napříč platformami .NET Core**</span><span class="sxs-lookup"><span data-stu-id="5d22a-109">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **.NET Core cross-platform development** workload</span></span>

## <a name="installation-and-setup"></a><span data-ttu-id="5d22a-110">Instalace a nastavení</span><span class="sxs-lookup"><span data-stu-id="5d22a-110">Installation and setup</span></span>

<span data-ttu-id="5d22a-111">V případě instalace Dockeru nejprve zkontrolujte informace v [Dockeru pro Windows: Co je třeba vědět před instalací](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).</span><span class="sxs-lookup"><span data-stu-id="5d22a-111">For Docker installation, first review the information at [Docker for Windows: What to know before you install](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).</span></span> <span data-ttu-id="5d22a-112">Dále nainstalujte [Docker pro Windows](https://docs.docker.com/docker-for-windows/install/).</span><span class="sxs-lookup"><span data-stu-id="5d22a-112">Next, install [Docker For Windows](https://docs.docker.com/docker-for-windows/install/).</span></span>

<span data-ttu-id="5d22a-113">**[Sdílené jednotky](https://docs.docker.com/docker-for-windows/#shared-drives)** v Dockeru pro Windows musí být nakonfigurovány tak, aby podporovaly mapování a ladění svazků.</span><span class="sxs-lookup"><span data-stu-id="5d22a-113">**[Shared Drives](https://docs.docker.com/docker-for-windows/#shared-drives)** in Docker for Windows must be configured to support volume mapping and debugging.</span></span> <span data-ttu-id="5d22a-114">Klepněte pravým tlačítkem myši na ikonu Dockeru na hlavním panelu systému, vyberte **nastavení**a vyberte **sdílené jednotky**.</span><span class="sxs-lookup"><span data-stu-id="5d22a-114">Right-click the System Tray's Docker icon, select **Settings**, and select **Shared Drives**.</span></span> <span data-ttu-id="5d22a-115">Vyberte jednotku, na kterou Docker ukládá soubory.</span><span class="sxs-lookup"><span data-stu-id="5d22a-115">Select the drive where Docker stores files.</span></span> <span data-ttu-id="5d22a-116">Klikněte na **Použít**.</span><span class="sxs-lookup"><span data-stu-id="5d22a-116">Click **Apply**.</span></span>

![Dialogové okno pro výběr místního sdílení jednotky C pro kontejnery](visual-studio-tools-for-docker/_static/settings-shared-drives-win.png)

> [!TIP]
> <span data-ttu-id="5d22a-118">Visual Studio 2017 verze 15.6 a novější výzvu, pokud sdílené jednotky nejsou **nakonfigurovány.**</span><span class="sxs-lookup"><span data-stu-id="5d22a-118">Visual Studio 2017 versions 15.6 and later prompt when **Shared Drives** aren't configured.</span></span>

## <a name="add-a-project-to-a-docker-container"></a><span data-ttu-id="5d22a-119">Přidání projektu do kontejneru Dockeru</span><span class="sxs-lookup"><span data-stu-id="5d22a-119">Add a project to a Docker container</span></span>

<span data-ttu-id="5d22a-120">Chcete-li kontejnerizovat ASP.NET projektu Core, musí projekt cílit na jádro .NET Core.</span><span class="sxs-lookup"><span data-stu-id="5d22a-120">To containerize an ASP.NET Core project, the project must target .NET Core.</span></span> <span data-ttu-id="5d22a-121">Jsou podporovány kontejnery Linux i Windows.</span><span class="sxs-lookup"><span data-stu-id="5d22a-121">Both Linux and Windows containers are supported.</span></span>

<span data-ttu-id="5d22a-122">Při přidávání podpory Dockeru do projektu zvolte kontejner Windows nebo Linux.</span><span class="sxs-lookup"><span data-stu-id="5d22a-122">When adding Docker support to a project, choose either a Windows or a Linux container.</span></span> <span data-ttu-id="5d22a-123">Hostitel Dockeru musí mít stejný typ kontejneru.</span><span class="sxs-lookup"><span data-stu-id="5d22a-123">The Docker host must be running the same container type.</span></span> <span data-ttu-id="5d22a-124">Chcete-li změnit typ kontejneru v spuštěné instanci Dockeru, klikněte pravým tlačítkem myši na ikonu Dockeru na systémové majebru a zvolte **Přepnout do kontejnerů Windows...** nebo **Přepnout na linuxové kontejnery...**.</span><span class="sxs-lookup"><span data-stu-id="5d22a-124">To change the container type in the running Docker instance, right-click the System Tray's Docker icon and choose **Switch to Windows containers...** or **Switch to Linux containers...**.</span></span>

### <a name="new-app"></a><span data-ttu-id="5d22a-125">Nová aplikace</span><span class="sxs-lookup"><span data-stu-id="5d22a-125">New app</span></span>

<span data-ttu-id="5d22a-126">Při vytváření nové aplikace s šablonami projektu **ASP.NET Core Web Application** zaškrtněte políčko **Povolit podporu Dockeru:**</span><span class="sxs-lookup"><span data-stu-id="5d22a-126">When creating a new app with the **ASP.NET Core Web Application** project templates, select the **Enable Docker Support** check box:</span></span>

![Zaškrtávací políčko Povolit podporu Dockeru](visual-studio-tools-for-docker/_static/enable-docker-support-check-box.png)

<span data-ttu-id="5d22a-128">Pokud je cílový rámec .NET Core, rozevírací seznam **operačního systému** umožňuje výběr typu kontejneru.</span><span class="sxs-lookup"><span data-stu-id="5d22a-128">If the target framework is .NET Core, the **OS** drop-down allows for the selection of a container type.</span></span>

### <a name="existing-app"></a><span data-ttu-id="5d22a-129">Existující aplikace</span><span class="sxs-lookup"><span data-stu-id="5d22a-129">Existing app</span></span>

<span data-ttu-id="5d22a-130">Pro ASP.NET základní projekty zaměřené na .NET Core existují dvě možnosti pro přidání podpory Dockeru prostřednictvím nástrojů.</span><span class="sxs-lookup"><span data-stu-id="5d22a-130">For ASP.NET Core projects targeting .NET Core, there are two options for adding Docker support via the tooling.</span></span> <span data-ttu-id="5d22a-131">Otevřete projekt v sadě Visual Studio a zvolte jednu z následujících možností:</span><span class="sxs-lookup"><span data-stu-id="5d22a-131">Open the project in Visual Studio, and choose one of the following options:</span></span>

* <span data-ttu-id="5d22a-132">V nabídce **Project** vyberte **Podpora Dockeru.**</span><span class="sxs-lookup"><span data-stu-id="5d22a-132">Select **Docker Support** from the **Project** menu.</span></span>
* <span data-ttu-id="5d22a-133">Klikněte pravým tlačítkem myši na projekt v **Průzkumníku řešení** a vyberte **Přidat** > **podporu Dockeru**.</span><span class="sxs-lookup"><span data-stu-id="5d22a-133">Right-click the project in **Solution Explorer** and select **Add** > **Docker Support**.</span></span>

<span data-ttu-id="5d22a-134">Nástroje kontejneru Visual Studio nepodporují přidávání Dockeru do existujícího ASP.NET základního projektu cílení .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="5d22a-134">The Visual Studio Container Tools don't support adding Docker to an existing ASP.NET Core project targeting .NET Framework.</span></span>

## <a name="dockerfile-overview"></a><span data-ttu-id="5d22a-135">Přehled souborů Dockeru</span><span class="sxs-lookup"><span data-stu-id="5d22a-135">Dockerfile overview</span></span>

<span data-ttu-id="5d22a-136">*Dockerfile*, recept pro vytvoření konečné image Dockeru, je přidán do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="5d22a-136">A *Dockerfile*, the recipe for creating a final Docker image, is added to the project root.</span></span> <span data-ttu-id="5d22a-137">Odkazovat na [odkaz Dockerfile](https://docs.docker.com/engine/reference/builder/) pro pochopení příkazů v něm.</span><span class="sxs-lookup"><span data-stu-id="5d22a-137">Refer to [Dockerfile reference](https://docs.docker.com/engine/reference/builder/) for an understanding of the commands within it.</span></span> <span data-ttu-id="5d22a-138">Tento konkrétní *Dockerfile* používá [vícestupňové sestavení](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) se čtyřmi odlišnými, pojmenovanými fázemi sestavení:</span><span class="sxs-lookup"><span data-stu-id="5d22a-138">This particular *Dockerfile* uses a [multi-stage build](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) with four distinct, named build stages:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile.original?highlight=1,6,14,17)]

<span data-ttu-id="5d22a-139">Předchozí *soubor Dockerfile* je založen na image [microsoft/dotnet.](https://hub.docker.com/r/microsoft/dotnet/)</span><span class="sxs-lookup"><span data-stu-id="5d22a-139">The preceding *Dockerfile* is based on the [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/) image.</span></span> <span data-ttu-id="5d22a-140">Tato základní image obsahuje ASP.NET balíčky Core runtime a NuGet.</span><span class="sxs-lookup"><span data-stu-id="5d22a-140">This base image includes the ASP.NET Core runtime and NuGet packages.</span></span> <span data-ttu-id="5d22a-141">Balíčky jsou just-in-time (JIT) zkompilovány ke zlepšení výkonu při spuštění.</span><span class="sxs-lookup"><span data-stu-id="5d22a-141">The packages are just-in-time (JIT) compiled to improve startup performance.</span></span>

<span data-ttu-id="5d22a-142">Když je zaškrtnuto políčko **Konfigurovat pro protokol HTTPS** nového dialogového okna projektu, *dockerfile* zpřístupní dva porty.</span><span class="sxs-lookup"><span data-stu-id="5d22a-142">When the new project dialog's **Configure for HTTPS** check box is checked, the *Dockerfile* exposes two ports.</span></span> <span data-ttu-id="5d22a-143">Jeden port se používá pro přenosy HTTP; druhý port se používá pro protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="5d22a-143">One port is used for HTTP traffic; the other port is used for HTTPS.</span></span> <span data-ttu-id="5d22a-144">Pokud políčko není zaškrtnuto, jeden port (80) je vystaven pro přenosy HTTP.</span><span class="sxs-lookup"><span data-stu-id="5d22a-144">If the check box isn't checked, a single port (80) is exposed for HTTP traffic.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.0/HelloDockerTools/Dockerfile?highlight=1,5,13,16)]

<span data-ttu-id="5d22a-145">Předchozí *soubor Dockerfile* je založen na image [Microsoft/aspnetcore.](https://hub.docker.com/r/microsoft/aspnetcore/)</span><span class="sxs-lookup"><span data-stu-id="5d22a-145">The preceding *Dockerfile* is based on the [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/) image.</span></span> <span data-ttu-id="5d22a-146">Tato základní bitová kopie obsahuje ASP.NET balíčky Core NuGet, které jsou zkompilovány za čase (JIT) ke zlepšení výkonu při spuštění.</span><span class="sxs-lookup"><span data-stu-id="5d22a-146">This base image includes the ASP.NET Core NuGet packages, which are just-in-time (JIT) compiled to improve startup performance.</span></span>

::: moniker-end

## <a name="add-container-orchestrator-support-to-an-app"></a><span data-ttu-id="5d22a-147">Přidání podpory orchestrátoru kontejnerů do aplikace</span><span class="sxs-lookup"><span data-stu-id="5d22a-147">Add container orchestrator support to an app</span></span>

<span data-ttu-id="5d22a-148">Visual Studio 2017 verze 15.7 nebo starší podporují [Docker Compose](https://docs.docker.com/compose/overview/) jako jediné řešení orchestrace kontejnerů.</span><span class="sxs-lookup"><span data-stu-id="5d22a-148">Visual Studio 2017 versions 15.7 or earlier support [Docker Compose](https://docs.docker.com/compose/overview/) as the sole container orchestration solution.</span></span> <span data-ttu-id="5d22a-149">Artefakty Docker Compose jsou přidány pomocí **přidat** > **podporu Dockeru**.</span><span class="sxs-lookup"><span data-stu-id="5d22a-149">The Docker Compose artifacts are added via **Add** > **Docker Support**.</span></span>

<span data-ttu-id="5d22a-150">Visual Studio 2017 verze 15.8 nebo novější přidat řešení orchestrace pouze v pokynech.</span><span class="sxs-lookup"><span data-stu-id="5d22a-150">Visual Studio 2017 versions 15.8 or later add an orchestration solution only when instructed.</span></span> <span data-ttu-id="5d22a-151">Klepněte pravým tlačítkem myši na projekt v **Průzkumníku řešení** a vyberte **přidat** > **podporu orchestrátoru kontejnerů**.</span><span class="sxs-lookup"><span data-stu-id="5d22a-151">Right-click the project in **Solution Explorer** and select **Add** > **Container Orchestrator Support**.</span></span> <span data-ttu-id="5d22a-152">K dispozici jsou dvě různé možnosti: [Docker Compose](#docker-compose) a [Service Fabric](#service-fabric).</span><span class="sxs-lookup"><span data-stu-id="5d22a-152">Two different choices are offered: [Docker Compose](#docker-compose) and [Service Fabric](#service-fabric).</span></span>

### <a name="docker-compose"></a><span data-ttu-id="5d22a-153">Docker Compose</span><span class="sxs-lookup"><span data-stu-id="5d22a-153">Docker Compose</span></span>

<span data-ttu-id="5d22a-154">Nástroje kontejneru Visual Studio přidat *docker-compose* projekt do řešení s následujícími soubory:</span><span class="sxs-lookup"><span data-stu-id="5d22a-154">The Visual Studio Container Tools add a *docker-compose* project to the solution with the following files:</span></span>

* <span data-ttu-id="5d22a-155">*docker-compose.dcproj* &ndash; Soubor představující projekt.</span><span class="sxs-lookup"><span data-stu-id="5d22a-155">*docker-compose.dcproj* &ndash; The file representing the project.</span></span> <span data-ttu-id="5d22a-156">Obsahuje `<DockerTargetOS>` prvek určující operační ho operačního prvku, který má být použit.</span><span class="sxs-lookup"><span data-stu-id="5d22a-156">Includes a `<DockerTargetOS>` element specifying the OS to be used.</span></span>
* <span data-ttu-id="5d22a-157">*.dockerignore:* &ndash; Seznam vzorů souborů a adresářů, které mají být vyloučeny při generování kontextu sestavení.</span><span class="sxs-lookup"><span data-stu-id="5d22a-157">*.dockerignore* &ndash; Lists the file and directory patterns to exclude when generating a build context.</span></span>
* <span data-ttu-id="5d22a-158">*docker-compose.yml* &ndash; Základní [soubor Docker Compose](https://docs.docker.com/compose/overview/) slouží k definování kolekce `docker-compose build` `docker-compose run`bitových kopií postavených a spustit s a , v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="5d22a-158">*docker-compose.yml* &ndash; The base [Docker Compose](https://docs.docker.com/compose/overview/) file used to define the collection of images built and run with `docker-compose build` and `docker-compose run`, respectively.</span></span>
* <span data-ttu-id="5d22a-159">*docker-compose.override.yml* &ndash; Volitelný soubor, přečtený Docker Empose, s přepsáním konfigurace pro služby.</span><span class="sxs-lookup"><span data-stu-id="5d22a-159">*docker-compose.override.yml* &ndash; An optional file, read by Docker Compose, with configuration overrides for services.</span></span> <span data-ttu-id="5d22a-160">Visual Studio `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` spustí sloučit tyto soubory.</span><span class="sxs-lookup"><span data-stu-id="5d22a-160">Visual Studio executes `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` to merge these files.</span></span>

<span data-ttu-id="5d22a-161">Soubor *docker-compose.yml* odkazuje na název bitové kopie, která je vytvořena při spuštění projektu:</span><span class="sxs-lookup"><span data-stu-id="5d22a-161">The *docker-compose.yml* file references the name of the image that's created when the project runs:</span></span>

[!code-yaml[](visual-studio-tools-for-docker/samples/2.0/docker-compose.yml?highlight=5)]

<span data-ttu-id="5d22a-162">V předchozím příkladu `image: hellodockertools` generuje image `hellodockertools:dev` při spuštění aplikace v režimu **ladění.**</span><span class="sxs-lookup"><span data-stu-id="5d22a-162">In the preceding example, `image: hellodockertools` generates the image `hellodockertools:dev` when the app runs in **Debug** mode.</span></span> <span data-ttu-id="5d22a-163">Obrázek se `hellodockertools:latest` vygeneruje, když se aplikace spustí v režimu **vydání.**</span><span class="sxs-lookup"><span data-stu-id="5d22a-163">The `hellodockertools:latest` image is generated when the app runs in **Release** mode.</span></span>

<span data-ttu-id="5d22a-164">Předpona názvu bitové kopie s uživatelským `dockerhubusername/hellodockertools`jménem Docker [Hub](https://hub.docker.com/) (například), pokud je bitová kopie zasunuta do registru.</span><span class="sxs-lookup"><span data-stu-id="5d22a-164">Prefix the image name with the [Docker Hub](https://hub.docker.com/) username (for example, `dockerhubusername/hellodockertools`) if the image is pushed to the registry.</span></span> <span data-ttu-id="5d22a-165">Případně změňte název obrázku tak, aby zahrnoval `privateregistry.domain.com/hellodockertools`adresu URL soukromého registru (například) v závislosti na konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="5d22a-165">Alternatively, change the image name to include the private registry URL (for example, `privateregistry.domain.com/hellodockertools`) depending on the configuration.</span></span>

<span data-ttu-id="5d22a-166">Pokud chcete různé chování na základě konfigurace sestavení (například Ladění nebo Vydání), přidejte soubory *docker-compose* specifické pro konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="5d22a-166">If you want different behavior based on the build configuration (for example, Debug or Release), add configuration-specific *docker-compose* files.</span></span> <span data-ttu-id="5d22a-167">Soubory by měly být pojmenovány podle konfigurace sestavení (například *docker-compose.vs.debug.yml* a *docker-compose.vs.release.yml*) a umístěny ve stejném umístění jako soubor *docker-compose-override.yml.*</span><span class="sxs-lookup"><span data-stu-id="5d22a-167">The files should be named according to the build configuration (for example, *docker-compose.vs.debug.yml* and *docker-compose.vs.release.yml*) and placed in the same location as the *docker-compose-override.yml* file.</span></span> 

<span data-ttu-id="5d22a-168">Pomocí přepsání souborů specifických pro konfiguraci můžete určit různá nastavení konfigurace (například proměnné prostředí nebo vstupní body) pro konfigurace sestavení ladění a vydání.</span><span class="sxs-lookup"><span data-stu-id="5d22a-168">Using the configuration-specific override files, you can specify different configuration settings (such as environment variables or entry points) for Debug and Release build configurations.</span></span>

<span data-ttu-id="5d22a-169">Pro Docker Compose zobrazit možnost ke spuštění v sadě Visual Studio, projekt dockeru musí být spuštění projektu.</span><span class="sxs-lookup"><span data-stu-id="5d22a-169">For Docker Compose to display an option to run in Visual Studio, the docker project must be the startup project.</span></span>

### <a name="service-fabric"></a><span data-ttu-id="5d22a-170">Service Fabric</span><span class="sxs-lookup"><span data-stu-id="5d22a-170">Service Fabric</span></span>

<span data-ttu-id="5d22a-171">Kromě základních [požadavků](#prerequisites)vyžaduje orchestrace [service fabric](/azure/service-fabric/) následující požadavky:</span><span class="sxs-lookup"><span data-stu-id="5d22a-171">In addition to the base [Prerequisites](#prerequisites), the [Service Fabric](/azure/service-fabric/) orchestration solution demands the following prerequisites:</span></span>

* <span data-ttu-id="5d22a-172">[Sada Microsoft Azure Service Fabric SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK) verze 2.6 nebo novější</span><span class="sxs-lookup"><span data-stu-id="5d22a-172">[Microsoft Azure Service Fabric SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK) version 2.6 or later</span></span>
* <span data-ttu-id="5d22a-173">Úloha **Vývoje Azure** od Visual Studia</span><span class="sxs-lookup"><span data-stu-id="5d22a-173">Visual Studio's **Azure Development** workload</span></span>

<span data-ttu-id="5d22a-174">Service Fabric nepodporuje spouštění linuxových kontejnerů v clusteru místního vývoje v systému Windows.</span><span class="sxs-lookup"><span data-stu-id="5d22a-174">Service Fabric doesn't support running Linux containers in the local development cluster on Windows.</span></span> <span data-ttu-id="5d22a-175">Pokud projekt již používá kontejner Linux, Visual Studio vyzve k přepnutí do kontejnerů systému Windows.</span><span class="sxs-lookup"><span data-stu-id="5d22a-175">If the project is already using a Linux container, Visual Studio prompts to switch to Windows containers.</span></span>

<span data-ttu-id="5d22a-176">Nástroje kontejneru sady Visual Studio plní následující úkoly:</span><span class="sxs-lookup"><span data-stu-id="5d22a-176">The Visual Studio Container Tools do the following tasks:</span></span>

* <span data-ttu-id="5d22a-177">Přidá do řešení projekt aplikace \* &lt;&gt;project_name\* **aplikace.**</span><span class="sxs-lookup"><span data-stu-id="5d22a-177">Adds a *&lt;project_name&gt;Application* **Service Fabric Application** project to the solution.</span></span>
* <span data-ttu-id="5d22a-178">Přidá *dockerfile* a *soubor .dockerignore* do projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5d22a-178">Adds a *Dockerfile* and a *.dockerignore* file to the ASP.NET Core project.</span></span> <span data-ttu-id="5d22a-179">Pokud *soubor Dockerfile* již existuje v projektu ASP.NET Core, je přejmenován na *Dockerfile.original*.</span><span class="sxs-lookup"><span data-stu-id="5d22a-179">If a *Dockerfile* already exists in the ASP.NET Core project, it's renamed to *Dockerfile.original*.</span></span> <span data-ttu-id="5d22a-180">Vytvoří se nový *soubor Dockerfile*, podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="5d22a-180">A new *Dockerfile*, similar to the following, is created:</span></span>

    [!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile)]

* <span data-ttu-id="5d22a-181">Přidá `<IsServiceFabricServiceProject>` prvek do souboru *.csproj* projektu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="5d22a-181">Adds an `<IsServiceFabricServiceProject>` element to the ASP.NET Core project's *.csproj* file:</span></span>

    [!code-xml[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/HelloDockerTools.csproj?name=snippet_IsServiceFabricServiceProject)]

* <span data-ttu-id="5d22a-182">Přidá složku *PackageRoot* do projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5d22a-182">Adds a *PackageRoot* folder to the ASP.NET Core project.</span></span> <span data-ttu-id="5d22a-183">Složka obsahuje manifest služby a nastavení pro novou službu.</span><span class="sxs-lookup"><span data-stu-id="5d22a-183">The folder includes the service manifest and settings for the new service.</span></span>

<span data-ttu-id="5d22a-184">Další informace najdete [v tématu Nasazení aplikace .NET v kontejneru Windows do Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container).</span><span class="sxs-lookup"><span data-stu-id="5d22a-184">For more information, see [Deploy a .NET app in a Windows container to Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container).</span></span>

## <a name="debug"></a><span data-ttu-id="5d22a-185">Ladit</span><span class="sxs-lookup"><span data-stu-id="5d22a-185">Debug</span></span>

<span data-ttu-id="5d22a-186">Vyberte **Docker** z rozevíracího okna ladění na panelu nástrojů a začněte ladit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5d22a-186">Select **Docker** from the debug drop-down in the toolbar, and start debugging the app.</span></span> <span data-ttu-id="5d22a-187">Zobrazení **Dockeru** v okně **Výstup** zobrazuje následující akce:</span><span class="sxs-lookup"><span data-stu-id="5d22a-187">The **Docker** view of the **Output** window shows the following actions taking place:</span></span>

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="5d22a-188">Značka *2.1-aspnetcore-runtime* bitové kopie *microsoft/dotnet* runtime je získána (pokud již není v mezipaměti).</span><span class="sxs-lookup"><span data-stu-id="5d22a-188">The *2.1-aspnetcore-runtime* tag of the *microsoft/dotnet* runtime image is acquired (if not already in the cache).</span></span> <span data-ttu-id="5d22a-189">Bitová kopie nainstaluje ASP.NET core a .NET Core runtimes a přidružené knihovny.</span><span class="sxs-lookup"><span data-stu-id="5d22a-189">The image installs the ASP.NET Core and .NET Core runtimes and associated libraries.</span></span> <span data-ttu-id="5d22a-190">Je optimalizovaný pro spouštění aplikací ASP.NET Core v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="5d22a-190">It's optimized for running ASP.NET Core apps in production.</span></span>
* <span data-ttu-id="5d22a-191">Proměnná `ASPNETCORE_ENVIRONMENT` prostředí je `Development` nastavena na v rámci kontejneru.</span><span class="sxs-lookup"><span data-stu-id="5d22a-191">The `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development` within the container.</span></span>
* <span data-ttu-id="5d22a-192">Jsou vystaveny dva dynamicky přiřazené porty: jeden pro protokol HTTP a jeden pro protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="5d22a-192">Two dynamically assigned ports are exposed: one for HTTP and one for HTTPS.</span></span> <span data-ttu-id="5d22a-193">Port přiřazený localhost může být `docker ps` dotazován pomocí příkazu.</span><span class="sxs-lookup"><span data-stu-id="5d22a-193">The port assigned to localhost can be queried with the `docker ps` command.</span></span>
* <span data-ttu-id="5d22a-194">Aplikace se zkopíruje do kontejneru.</span><span class="sxs-lookup"><span data-stu-id="5d22a-194">The app is copied to the container.</span></span>
* <span data-ttu-id="5d22a-195">Výchozí prohlížeč se spustí s ladicím programem připojeným ke kontejneru pomocí dynamicky přiřazeného portu.</span><span class="sxs-lookup"><span data-stu-id="5d22a-195">The default browser is launched with the debugger attached to the container using the dynamically assigned port.</span></span>

<span data-ttu-id="5d22a-196">Výsledná image Dockeru aplikace je označena jako *dev*.</span><span class="sxs-lookup"><span data-stu-id="5d22a-196">The resulting Docker image of the app is tagged as *dev*.</span></span> <span data-ttu-id="5d22a-197">Obrázek je založen na značce *2.1-aspnetcore-runtime* základní bitové kopie *microsoft/dotnet.*</span><span class="sxs-lookup"><span data-stu-id="5d22a-197">The image is based on the *2.1-aspnetcore-runtime* tag of the *microsoft/dotnet* base image.</span></span> <span data-ttu-id="5d22a-198">Spusťte `docker images` příkaz v okně **Konzola správce balíčků** (PMC).</span><span class="sxs-lookup"><span data-stu-id="5d22a-198">Run the `docker images` command in the **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="5d22a-199">Obrázky na zařízení jsou zobrazeny:</span><span class="sxs-lookup"><span data-stu-id="5d22a-199">The images on the machine are displayed:</span></span>

```console
REPOSITORY        TAG                     IMAGE ID      CREATED         SIZE
hellodockertools  dev                     d72ce0f1dfe7  30 seconds ago  255MB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago      255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* <span data-ttu-id="5d22a-200">*Microsoft/aspnetcore* runtime image je získán (pokud již není v mezipaměti).</span><span class="sxs-lookup"><span data-stu-id="5d22a-200">The *microsoft/aspnetcore* runtime image is acquired (if not already in the cache).</span></span>
* <span data-ttu-id="5d22a-201">Proměnná `ASPNETCORE_ENVIRONMENT` prostředí je `Development` nastavena na v rámci kontejneru.</span><span class="sxs-lookup"><span data-stu-id="5d22a-201">The `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development` within the container.</span></span>
* <span data-ttu-id="5d22a-202">Port 80 je vystavena a mapována na dynamicky přiřazený port pro localhost.</span><span class="sxs-lookup"><span data-stu-id="5d22a-202">Port 80 is exposed and mapped to a dynamically assigned port for localhost.</span></span> <span data-ttu-id="5d22a-203">Port je určen hostitelem Dockeru a může `docker ps` být dotazován pomocí příkazu.</span><span class="sxs-lookup"><span data-stu-id="5d22a-203">The port is determined by the Docker host and can be queried with the `docker ps` command.</span></span>
* <span data-ttu-id="5d22a-204">Aplikace se zkopíruje do kontejneru.</span><span class="sxs-lookup"><span data-stu-id="5d22a-204">The app is copied to the container.</span></span>
* <span data-ttu-id="5d22a-205">Výchozí prohlížeč se spustí s ladicím programem připojeným ke kontejneru pomocí dynamicky přiřazeného portu.</span><span class="sxs-lookup"><span data-stu-id="5d22a-205">The default browser is launched with the debugger attached to the container using the dynamically assigned port.</span></span>

<span data-ttu-id="5d22a-206">Výsledná image Dockeru aplikace je označena jako *dev*.</span><span class="sxs-lookup"><span data-stu-id="5d22a-206">The resulting Docker image of the app is tagged as *dev*.</span></span> <span data-ttu-id="5d22a-207">Obrázek je založen na základní bitové kopii *microsoft/aspnetcore.*</span><span class="sxs-lookup"><span data-stu-id="5d22a-207">The image is based on the *microsoft/aspnetcore* base image.</span></span> <span data-ttu-id="5d22a-208">Spusťte `docker images` příkaz v okně **Konzola správce balíčků** (PMC).</span><span class="sxs-lookup"><span data-stu-id="5d22a-208">Run the `docker images` command in the **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="5d22a-209">Obrázky na zařízení jsou zobrazeny:</span><span class="sxs-lookup"><span data-stu-id="5d22a-209">The images on the machine are displayed:</span></span>

```console
REPOSITORY            TAG  IMAGE ID      CREATED        SIZE
hellodockertools      dev  5fafe5d1ad5b  4 minutes ago  347MB
microsoft/aspnetcore  2.0  c69d39472da9  13 days ago    347MB
```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="5d22a-210">*Obrázek dev* postrádá obsah aplikace, protože konfigurace **ladění** používají připojení svazku k zajištění iterativního prostředí.</span><span class="sxs-lookup"><span data-stu-id="5d22a-210">The *dev* image lacks the app contents, as **Debug** configurations use volume mounting to provide the iterative experience.</span></span> <span data-ttu-id="5d22a-211">Chcete-li obrázek stisknout, použijte konfiguraci **vydání.**</span><span class="sxs-lookup"><span data-stu-id="5d22a-211">To push an image, use the **Release** configuration.</span></span>

<span data-ttu-id="5d22a-212">Spusťte `docker ps` příkaz v PMC.</span><span class="sxs-lookup"><span data-stu-id="5d22a-212">Run the `docker ps` command in PMC.</span></span> <span data-ttu-id="5d22a-213">Všimněte si, že aplikace běží pomocí kontejneru:</span><span class="sxs-lookup"><span data-stu-id="5d22a-213">Notice the app is running using the container:</span></span>

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   21 seconds ago      Up 19 seconds       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="edit-and-continue"></a><span data-ttu-id="5d22a-214">Upravit a pokračovat</span><span class="sxs-lookup"><span data-stu-id="5d22a-214">Edit and continue</span></span>

<span data-ttu-id="5d22a-215">Změny statických souborů a zobrazení Razor jsou automaticky aktualizovány bez nutnosti kroku kompilace.</span><span class="sxs-lookup"><span data-stu-id="5d22a-215">Changes to static files and Razor views are automatically updated without the need for a compilation step.</span></span> <span data-ttu-id="5d22a-216">Proveďte změnu, uložte a aktualizujte prohlížeč, abyste aktualizaci zobrazili.</span><span class="sxs-lookup"><span data-stu-id="5d22a-216">Make the change, save, and refresh the browser to view the update.</span></span>

<span data-ttu-id="5d22a-217">Změny souboru kódu vyžadují kompilaci a restartování Kestrel v rámci kontejneru.</span><span class="sxs-lookup"><span data-stu-id="5d22a-217">Code file modifications require compilation and a restart of Kestrel within the container.</span></span> <span data-ttu-id="5d22a-218">Po provedení změny `CTRL+F5` použijte k provedení procesu a spuštění aplikace v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="5d22a-218">After making the change, use `CTRL+F5` to perform the process and start the app within the container.</span></span> <span data-ttu-id="5d22a-219">Kontejner Dockeru není znovu sestaven nebo zastaven.</span><span class="sxs-lookup"><span data-stu-id="5d22a-219">The Docker container isn't rebuilt or stopped.</span></span> <span data-ttu-id="5d22a-220">Spusťte `docker ps` příkaz v PMC.</span><span class="sxs-lookup"><span data-stu-id="5d22a-220">Run the `docker ps` command in PMC.</span></span> <span data-ttu-id="5d22a-221">Všimněte si, že původní kontejner je stále spuštěn před 10 minutami:</span><span class="sxs-lookup"><span data-stu-id="5d22a-221">Notice the original container is still running as of 10 minutes ago:</span></span>

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   10 minutes ago      Up 10 minutes       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="publish-docker-images"></a><span data-ttu-id="5d22a-222">Publikovat imitace Dockeru</span><span class="sxs-lookup"><span data-stu-id="5d22a-222">Publish Docker images</span></span>

<span data-ttu-id="5d22a-223">Po dokončení cyklu vývoje a ladění aplikace nástroje kontejneru Visual Studio pomáhají při vytváření produkční image aplikace.</span><span class="sxs-lookup"><span data-stu-id="5d22a-223">Once the develop and debug cycle of the app is completed, the Visual Studio Container Tools assist in creating the production image of the app.</span></span> <span data-ttu-id="5d22a-224">Změňte rozbalovací verzi konfigurace na **Uvolnění** a vytvořte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5d22a-224">Change the configuration drop-down to **Release** and build the app.</span></span> <span data-ttu-id="5d22a-225">Nástroj získá image kompilace nebo publikování z Docker Hubu (pokud již není v mezipaměti).</span><span class="sxs-lookup"><span data-stu-id="5d22a-225">The tooling acquires the compile/publish image from Docker Hub (if not already in the cache).</span></span> <span data-ttu-id="5d22a-226">Bitová kopie je vytvořena s *nejnovější* značkou, kterou lze zasunout do soukromého registru nebo Docker Hubu.</span><span class="sxs-lookup"><span data-stu-id="5d22a-226">An image is produced with the *latest* tag, which can be pushed to the private registry or Docker Hub.</span></span>

<span data-ttu-id="5d22a-227">Spusťte `docker images` příkaz v PMC a zobrazte seznam obrázků.</span><span class="sxs-lookup"><span data-stu-id="5d22a-227">Run the `docker images` command in PMC to see the list of images.</span></span> <span data-ttu-id="5d22a-228">Zobrazí se výstup podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="5d22a-228">Output similar to the following is displayed:</span></span>

::: moniker range=">= aspnetcore-2.1"

```console
REPOSITORY        TAG                     IMAGE ID      CREATED             SIZE
hellodockertools  latest                  e3984a64230c  About a minute ago  258MB
hellodockertools  dev                     d72ce0f1dfe7  4 minutes ago       255MB
microsoft/dotnet  2.1-sdk                 9e243db15f91  6 days ago          1.7GB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago          255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

```console
REPOSITORY                  TAG     IMAGE ID      CREATED         SIZE
hellodockertools            latest  cd28f0d4abbd  12 seconds ago  349MB
hellodockertools            dev     5fafe5d1ad5b  23 minutes ago  347MB
microsoft/aspnetcore-build  2.0     7fed40fbb647  13 days ago     2.02GB
microsoft/aspnetcore        2.0     c69d39472da9  13 days ago     347MB
```

<span data-ttu-id="5d22a-229">`microsoft/aspnetcore-build` Obrázky `microsoft/aspnetcore` a uvedené v předchozím výstupu `microsoft/dotnet` jsou nahrazeny obrázky od .NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="5d22a-229">The `microsoft/aspnetcore-build` and `microsoft/aspnetcore` images listed in the preceding output are replaced with `microsoft/dotnet` images as of .NET Core 2.1.</span></span> <span data-ttu-id="5d22a-230">Další informace naleznete [v oznámení o migraci úložišť Dockeru](https://github.com/aspnet/Announcements/issues/298).</span><span class="sxs-lookup"><span data-stu-id="5d22a-230">For more information, see [the Docker repositories migration announcement](https://github.com/aspnet/Announcements/issues/298).</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="5d22a-231">Příkaz `docker images` vrátí zprostředkující bitové kopie s \* \<\* názvy úložišť a značkami označenými jako žádné>(nejsou uvedeny výše).</span><span class="sxs-lookup"><span data-stu-id="5d22a-231">The `docker images` command returns intermediary images with repository names and tags identified as *\<none>* (not listed above).</span></span> <span data-ttu-id="5d22a-232">Tyto nepojmenované bitové kopie jsou vytvářeny [vícestupňové sestavení](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) *Dockerfile*.</span><span class="sxs-lookup"><span data-stu-id="5d22a-232">These unnamed images are produced by the [multi-stage build](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) *Dockerfile*.</span></span> <span data-ttu-id="5d22a-233">Zlepšují účinnost vytváření konečného obrazu&mdash;pouze potřebné vrstvy jsou přestavěny, když dojde ke změnám.</span><span class="sxs-lookup"><span data-stu-id="5d22a-233">They improve the efficiency of building the final image&mdash;only the necessary layers are rebuilt when changes occur.</span></span> <span data-ttu-id="5d22a-234">Pokud již nejsou potřebné mezilehlé bitové kopie, odstraňte je pomocí příkazu [rmi dockeru.](https://docs.docker.com/engine/reference/commandline/rmi/)</span><span class="sxs-lookup"><span data-stu-id="5d22a-234">When the intermediary images are no longer needed, delete them using the [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) command.</span></span>

<span data-ttu-id="5d22a-235">Může existovat očekávání, že produkční nebo release image bude menší ve srovnání s *obrázkem dev.*</span><span class="sxs-lookup"><span data-stu-id="5d22a-235">There may be an expectation for the production or release image to be smaller in size by comparison to the *dev* image.</span></span> <span data-ttu-id="5d22a-236">Z důvodu mapování svazku ladicí program a aplikace byly spuštěny z místního počítače a není v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="5d22a-236">Because of the volume mapping, the debugger and app were running from the local machine and not within the container.</span></span> <span data-ttu-id="5d22a-237">*Nejnovější* obrázek zabalil kód aplikace potřebný ke spuštění aplikace na hostitelském počítači.</span><span class="sxs-lookup"><span data-stu-id="5d22a-237">The *latest* image has packaged the necessary app code to run the app on a host machine.</span></span> <span data-ttu-id="5d22a-238">Delta je tedy velikost kódu aplikace.</span><span class="sxs-lookup"><span data-stu-id="5d22a-238">Therefore, the delta is the size of the app code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5d22a-239">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5d22a-239">Additional resources</span></span>

* [<span data-ttu-id="5d22a-240">Vývoj kontejnerů pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5d22a-240">Container development with Visual Studio</span></span>](/visualstudio/containers)
* [<span data-ttu-id="5d22a-241">Azure Service Fabric: Příprava vývojového prostředí</span><span class="sxs-lookup"><span data-stu-id="5d22a-241">Azure Service Fabric: Prepare your development environment</span></span>](/azure/service-fabric/service-fabric-get-started)
* [<span data-ttu-id="5d22a-242">Nasazení aplikace .NET v kontejneru Windows do Azure Service Fabric</span><span class="sxs-lookup"><span data-stu-id="5d22a-242">Deploy a .NET app in a Windows container to Azure Service Fabric</span></span>](/azure/service-fabric/service-fabric-host-app-in-a-container)
* [<span data-ttu-id="5d22a-243">Řešení potíží při vývoji v sadě Visual Studio pomocí Dockeru</span><span class="sxs-lookup"><span data-stu-id="5d22a-243">Troubleshoot Visual Studio development with Docker</span></span>](/azure/vs-azure-tools-docker-troubleshooting-docker-errors)
* [<span data-ttu-id="5d22a-244">Úložiště GitHub nástrojů kontejneru Visual Studia</span><span class="sxs-lookup"><span data-stu-id="5d22a-244">Visual Studio Container Tools GitHub repository</span></span>](https://github.com/Microsoft/DockerTools)
* [<span data-ttu-id="5d22a-245">GC pomocí Dockeru a malých kontejnerů</span><span class="sxs-lookup"><span data-stu-id="5d22a-245">GC using Docker and small containers</span></span>](xref:performance/memory#sc)

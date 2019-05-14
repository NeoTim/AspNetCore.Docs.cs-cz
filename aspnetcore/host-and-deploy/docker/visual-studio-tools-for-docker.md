---
title: Visual Studio Tools for Docker s ASP.NET Core
author: spboyer
description: Zjistěte, jak kontejnerizovat aplikace ASP.NET Core pomocí nástroje Visual Studio 2017 a Docker pro Windows.
ms.author: scaddie
ms.custom: mvc
ms.date: 09/12/2018
uid: host-and-deploy/docker/visual-studio-tools-for-docker
ms.openlocfilehash: b0d884fe2fe56f267ad70c388a08cd3fe6256364
ms.sourcegitcommit: 6afe57fb8d9055f88fedb92b16470398c4b9b24a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65610376"
---
# <a name="visual-studio-tools-for-docker-with-aspnet-core"></a><span data-ttu-id="b64d5-103">Visual Studio Tools for Docker s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b64d5-103">Visual Studio Tools for Docker with ASP.NET Core</span></span>

<span data-ttu-id="b64d5-104">Visual Studio 2017 a novější verze podporují vytváření, ladění a spouštění kontejnerizovaných ASP.NET Core aplikace cílí na .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b64d5-104">Visual Studio 2017 and later versions support building, debugging, and running containerized ASP.NET Core apps targeting .NET Core.</span></span> <span data-ttu-id="b64d5-105">Jsou podporovány kontejnery Windows i Linuxu.</span><span class="sxs-lookup"><span data-stu-id="b64d5-105">Both Windows and Linux containers are supported.</span></span>

<span data-ttu-id="b64d5-106">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b64d5-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b64d5-107">Požadavky</span><span class="sxs-lookup"><span data-stu-id="b64d5-107">Prerequisites</span></span>

* [<span data-ttu-id="b64d5-108">Docker pro Windows</span><span class="sxs-lookup"><span data-stu-id="b64d5-108">Docker for Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)
* <span data-ttu-id="b64d5-109">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s **vývoj pro různé platformy .NET Core** pracovního vytížení</span><span class="sxs-lookup"><span data-stu-id="b64d5-109">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **.NET Core cross-platform development** workload</span></span>

## <a name="installation-and-setup"></a><span data-ttu-id="b64d5-110">Instalace a nastavení</span><span class="sxs-lookup"><span data-stu-id="b64d5-110">Installation and setup</span></span>

<span data-ttu-id="b64d5-111">Pro instalaci Dockeru, nejprve zkontrolujte informace na [Docker pro Windows: Co potřebujete vědět před instalací](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).</span><span class="sxs-lookup"><span data-stu-id="b64d5-111">For Docker installation, first review the information at [Docker for Windows: What to know before you install](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).</span></span> <span data-ttu-id="b64d5-112">Dále nainstalujte [Docker pro Windows](https://docs.docker.com/docker-for-windows/install/).</span><span class="sxs-lookup"><span data-stu-id="b64d5-112">Next, install [Docker For Windows](https://docs.docker.com/docker-for-windows/install/).</span></span>

<span data-ttu-id="b64d5-113">**[Sdílené jednotky](https://docs.docker.com/docker-for-windows/#shared-drives)**  v Docker pro Windows musí být nakonfigurované pro podporu mapování svazku a ladění.</span><span class="sxs-lookup"><span data-stu-id="b64d5-113">**[Shared Drives](https://docs.docker.com/docker-for-windows/#shared-drives)** in Docker for Windows must be configured to support volume mapping and debugging.</span></span> <span data-ttu-id="b64d5-114">Klikněte pravým tlačítkem na ikonu Dockeru na hlavním panelu, vyberte **nastavení**a vyberte **sdílené jednotky**.</span><span class="sxs-lookup"><span data-stu-id="b64d5-114">Right-click the System Tray's Docker icon, select **Settings**, and select **Shared Drives**.</span></span> <span data-ttu-id="b64d5-115">Vyberte jednotku, kde Docker ukládá soubory.</span><span class="sxs-lookup"><span data-stu-id="b64d5-115">Select the drive where Docker stores files.</span></span> <span data-ttu-id="b64d5-116">Klikněte na tlačítko **Použít**.</span><span class="sxs-lookup"><span data-stu-id="b64d5-116">Click **Apply**.</span></span>

![Dialogové okno pro výběr místní jednotky C sdílení pro kontejnery](visual-studio-tools-for-docker/_static/settings-shared-drives-win.png)

> [!TIP]
> <span data-ttu-id="b64d5-118">Visual Studio 2017 verze 15.6 a vyšší výzvu, při **sdílené jednotky** nejsou nakonfigurované.</span><span class="sxs-lookup"><span data-stu-id="b64d5-118">Visual Studio 2017 versions 15.6 and later prompt when **Shared Drives** aren't configured.</span></span>

## <a name="add-a-project-to-a-docker-container"></a><span data-ttu-id="b64d5-119">Přidat projekt do kontejneru Dockeru</span><span class="sxs-lookup"><span data-stu-id="b64d5-119">Add a project to a Docker container</span></span>

<span data-ttu-id="b64d5-120">Kontejnerizaci projektu aplikace ASP.NET Core, musí projekt cílit na .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b64d5-120">To containerize an ASP.NET Core project, the project must target .NET Core.</span></span> <span data-ttu-id="b64d5-121">Jsou podporovány kontejnery Linux i Windows.</span><span class="sxs-lookup"><span data-stu-id="b64d5-121">Both Linux and Windows containers are supported.</span></span>

<span data-ttu-id="b64d5-122">Při přidávání podpory Docker do projektu, zvolte Windows nebo Linuxem kontejneru.</span><span class="sxs-lookup"><span data-stu-id="b64d5-122">When adding Docker support to a project, choose either a Windows or a Linux container.</span></span> <span data-ttu-id="b64d5-123">Hostitele Docker musí běžet stejný typ kontejneru.</span><span class="sxs-lookup"><span data-stu-id="b64d5-123">The Docker host must be running the same container type.</span></span> <span data-ttu-id="b64d5-124">Chcete-li změnit typ kontejneru v běžící instanci Dockeru, klikněte pravým tlačítkem na ikonu Dockeru na hlavním panelu a vyberte **přepnout na kontejnery Windows...**  nebo **přepnout na kontejnery Linuxu...** .</span><span class="sxs-lookup"><span data-stu-id="b64d5-124">To change the container type in the running Docker instance, right-click the System Tray's Docker icon and choose **Switch to Windows containers...** or **Switch to Linux containers...**.</span></span>

### <a name="new-app"></a><span data-ttu-id="b64d5-125">Nová aplikace</span><span class="sxs-lookup"><span data-stu-id="b64d5-125">New app</span></span>

<span data-ttu-id="b64d5-126">Při vytváření nové aplikace s **webové aplikace ASP.NET Core** šablony projektu, vyberte **povolit podporu Dockeru** zaškrtávací políčko:</span><span class="sxs-lookup"><span data-stu-id="b64d5-126">When creating a new app with the **ASP.NET Core Web Application** project templates, select the **Enable Docker Support** check box:</span></span>

![Povolit podporu Dockeru zaškrtávací políčko](visual-studio-tools-for-docker/_static/enable-docker-support-check-box.png)

<span data-ttu-id="b64d5-128">Pokud je verze cílového rozhraní .NET Core **OS** umožňuje rozevírací seznam pro výběr typu kontejneru.</span><span class="sxs-lookup"><span data-stu-id="b64d5-128">If the target framework is .NET Core, the **OS** drop-down allows for the selection of a container type.</span></span>

### <a name="existing-app"></a><span data-ttu-id="b64d5-129">Existující aplikace</span><span class="sxs-lookup"><span data-stu-id="b64d5-129">Existing app</span></span>

<span data-ttu-id="b64d5-130">Pro projekty ASP.NET Core, jejichž cílem je .NET Core existují dvě možnosti pro přidání podpory Dockeru pomocí nástrojů.</span><span class="sxs-lookup"><span data-stu-id="b64d5-130">For ASP.NET Core projects targeting .NET Core, there are two options for adding Docker support via the tooling.</span></span> <span data-ttu-id="b64d5-131">Otevřete projekt v sadě Visual Studio a zvolte jednu z následujících možností:</span><span class="sxs-lookup"><span data-stu-id="b64d5-131">Open the project in Visual Studio, and choose one of the following options:</span></span>

* <span data-ttu-id="b64d5-132">Vyberte **podporu Dockeru** z **projektu** nabídky.</span><span class="sxs-lookup"><span data-stu-id="b64d5-132">Select **Docker Support** from the **Project** menu.</span></span>
* <span data-ttu-id="b64d5-133">Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **přidat** > **podporu Dockeru**.</span><span class="sxs-lookup"><span data-stu-id="b64d5-133">Right-click the project in **Solution Explorer** and select **Add** > **Docker Support**.</span></span>

<span data-ttu-id="b64d5-134">Visual Studio Tools for Docker nepodporuje přidávání Dockeru do existujícího projektu ASP.NET Core cílí na rozhraní .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="b64d5-134">The Visual Studio Tools for Docker don't support adding Docker to an existing ASP.NET Core project targeting .NET Framework.</span></span>

## <a name="dockerfile-overview"></a><span data-ttu-id="b64d5-135">Přehled souboru Dockerfile</span><span class="sxs-lookup"><span data-stu-id="b64d5-135">Dockerfile overview</span></span>

<span data-ttu-id="b64d5-136">A *soubor Dockerfile*, předpisu pro vytvoření finální image Dockeru, je přidán do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="b64d5-136">A *Dockerfile*, the recipe for creating a final Docker image, is added to the project root.</span></span> <span data-ttu-id="b64d5-137">Odkazovat na [odkaz na soubor Dockerfile](https://docs.docker.com/engine/reference/builder/) pro pochopení příkazy v rámci něj.</span><span class="sxs-lookup"><span data-stu-id="b64d5-137">Refer to [Dockerfile reference](https://docs.docker.com/engine/reference/builder/) for an understanding of the commands within it.</span></span> <span data-ttu-id="b64d5-138">Tento konkrétní *soubor Dockerfile* používá [vícefázových sestavení](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) s čtyřmi distinct, s názvem fáze sestavení umísťují:</span><span class="sxs-lookup"><span data-stu-id="b64d5-138">This particular *Dockerfile* uses a [multi-stage build](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) with four distinct, named build stages:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile.original?highlight=1,6,14,17)]

<span data-ttu-id="b64d5-139">Předchozí *soubor Dockerfile* vychází [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/) bitové kopie.</span><span class="sxs-lookup"><span data-stu-id="b64d5-139">The preceding *Dockerfile* is based on the [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/) image.</span></span> <span data-ttu-id="b64d5-140">Tato základní image obsahuje modul runtime ASP.NET Core a balíčky NuGet.</span><span class="sxs-lookup"><span data-stu-id="b64d5-140">This base image includes the ASP.NET Core runtime and NuGet packages.</span></span> <span data-ttu-id="b64d5-141">Balíčky jsou just-in-time (JIT) zkompilována do zlepšit výkon při spuštění.</span><span class="sxs-lookup"><span data-stu-id="b64d5-141">The packages are just-in-time (JIT) compiled to improve startup performance.</span></span>

<span data-ttu-id="b64d5-142">Při nové dialogu projekt **konfigurace pro protokol HTTPS** zaškrtávací políčko zaškrtnuto, *soubor Dockerfile* poskytuje dva porty.</span><span class="sxs-lookup"><span data-stu-id="b64d5-142">When the new project dialog's **Configure for HTTPS** check box is checked, the *Dockerfile* exposes two ports.</span></span> <span data-ttu-id="b64d5-143">Jeden port se používá pro přenos HTTP; jiný port se používá pro protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b64d5-143">One port is used for HTTP traffic; the other port is used for HTTPS.</span></span> <span data-ttu-id="b64d5-144">Pokud políčko není zaškrtnuto, jeden port (80) je přístupný pro přenosy pomocí protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="b64d5-144">If the check box isn't checked, a single port (80) is exposed for HTTP traffic.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.0/HelloDockerTools/Dockerfile?highlight=1,5,13,16)]

<span data-ttu-id="b64d5-145">Předchozí *soubor Dockerfile* vychází [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/) bitové kopie.</span><span class="sxs-lookup"><span data-stu-id="b64d5-145">The preceding *Dockerfile* is based on the [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/) image.</span></span> <span data-ttu-id="b64d5-146">Tato základní image obsahuje balíčky ASP.NET Core NuGet, které jsou just-in-time (JIT) zkompilována do zlepšit výkon při spuštění.</span><span class="sxs-lookup"><span data-stu-id="b64d5-146">This base image includes the ASP.NET Core NuGet packages, which are just-in-time (JIT) compiled to improve startup performance.</span></span>

::: moniker-end

## <a name="add-container-orchestrator-support-to-an-app"></a><span data-ttu-id="b64d5-147">Přidat podporu orchestrátoru kontejnerů do aplikace</span><span class="sxs-lookup"><span data-stu-id="b64d5-147">Add container orchestrator support to an app</span></span>

<span data-ttu-id="b64d5-148">Visual Studio 2017 verze 15.7 nebo starší podporují [Docker Compose](https://docs.docker.com/compose/overview/) jako řešení Orchestrace kontejnerů jediným.</span><span class="sxs-lookup"><span data-stu-id="b64d5-148">Visual Studio 2017 versions 15.7 or earlier support [Docker Compose](https://docs.docker.com/compose/overview/) as the sole container orchestration solution.</span></span> <span data-ttu-id="b64d5-149">Docker Compose artefakty se přidají přes **přidat** > **podporu Dockeru**.</span><span class="sxs-lookup"><span data-stu-id="b64d5-149">The Docker Compose artifacts are added via **Add** > **Docker Support**.</span></span>

<span data-ttu-id="b64d5-150">Visual Studio 2017 verze 15,8 nebo novější přidat řešení Orchestrace jenom v případě, že vydal pokyn pro nástroje.</span><span class="sxs-lookup"><span data-stu-id="b64d5-150">Visual Studio 2017 versions 15.8 or later add an orchestration solution only when instructed.</span></span> <span data-ttu-id="b64d5-151">Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **přidat** > **podporu Orchestrátoru kontejnerů**.</span><span class="sxs-lookup"><span data-stu-id="b64d5-151">Right-click the project in **Solution Explorer** and select **Add** > **Container Orchestrator Support**.</span></span> <span data-ttu-id="b64d5-152">Nabízíme dvě různé možnosti: [Docker Compose](#docker-compose) a [Service Fabric](#service-fabric).</span><span class="sxs-lookup"><span data-stu-id="b64d5-152">Two different choices are offered: [Docker Compose](#docker-compose) and [Service Fabric](#service-fabric).</span></span>

### <a name="docker-compose"></a><span data-ttu-id="b64d5-153">Docker Compose</span><span class="sxs-lookup"><span data-stu-id="b64d5-153">Docker Compose</span></span>

<span data-ttu-id="b64d5-154">Přidejte Visual Studio Tools for Docker *docker-compose* projektu do řešení se následující soubory:</span><span class="sxs-lookup"><span data-stu-id="b64d5-154">The Visual Studio Tools for Docker add a *docker-compose* project to the solution with the following files:</span></span>

* <span data-ttu-id="b64d5-155">*docker compose.dcproj* &ndash; soubor, který projekt.</span><span class="sxs-lookup"><span data-stu-id="b64d5-155">*docker-compose.dcproj* &ndash; The file representing the project.</span></span> <span data-ttu-id="b64d5-156">Zahrnuje `<DockerTargetOS>` prvek určující operačního systému, který se má použít.</span><span class="sxs-lookup"><span data-stu-id="b64d5-156">Includes a `<DockerTargetOS>` element specifying the OS to be used.</span></span>
* <span data-ttu-id="b64d5-157">*.dockerignore* &ndash; obsahuje seznam vzorů souborů a adresářů pro vyloučení při generování kontext sestavení.</span><span class="sxs-lookup"><span data-stu-id="b64d5-157">*.dockerignore* &ndash; Lists the file and directory patterns to exclude when generating a build context.</span></span>
* <span data-ttu-id="b64d5-158">*docker-compose.yml* &ndash; základní [Docker Compose](https://docs.docker.com/compose/overview/) soubor se používá k definování kolekce imagí vytvořili a spustili pomocí `docker-compose build` a `docker-compose run`v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="b64d5-158">*docker-compose.yml* &ndash; The base [Docker Compose](https://docs.docker.com/compose/overview/) file used to define the collection of images built and run with `docker-compose build` and `docker-compose run`, respectively.</span></span>
* <span data-ttu-id="b64d5-159">*docker-compose.override.yml* &ndash; volitelný soubor ke čtení pomocí Docker Compose, s konfigurací přepsání pro služby.</span><span class="sxs-lookup"><span data-stu-id="b64d5-159">*docker-compose.override.yml* &ndash; An optional file, read by Docker Compose, with configuration overrides for services.</span></span> <span data-ttu-id="b64d5-160">Visual Studio provede `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` sloučit tyto soubory.</span><span class="sxs-lookup"><span data-stu-id="b64d5-160">Visual Studio executes `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` to merge these files.</span></span>

<span data-ttu-id="b64d5-161">*Docker-compose.yml* soubor odkazuje na název obrázku, který je vytvořen při spuštění projektu:</span><span class="sxs-lookup"><span data-stu-id="b64d5-161">The *docker-compose.yml* file references the name of the image that's created when the project runs:</span></span>

[!code-yaml[](visual-studio-tools-for-docker/samples/2.0/docker-compose.yml?highlight=5)]

<span data-ttu-id="b64d5-162">V předchozím příkladu `image: hellodockertools` generuje obrázek `hellodockertools:dev` při spuštění aplikace **ladění** režimu.</span><span class="sxs-lookup"><span data-stu-id="b64d5-162">In the preceding example, `image: hellodockertools` generates the image `hellodockertools:dev` when the app runs in **Debug** mode.</span></span> <span data-ttu-id="b64d5-163">`hellodockertools:latest` Image se vygeneruje, když aplikace běží v **vydání** režimu.</span><span class="sxs-lookup"><span data-stu-id="b64d5-163">The `hellodockertools:latest` image is generated when the app runs in **Release** mode.</span></span>

<span data-ttu-id="b64d5-164">Před názvem obrázku [Docker Hubu](https://hub.docker.com/) uživatelské jméno (třeba `dockerhubusername/hellodockertools`) Pokud je image do registru.</span><span class="sxs-lookup"><span data-stu-id="b64d5-164">Prefix the image name with the [Docker Hub](https://hub.docker.com/) username (for example, `dockerhubusername/hellodockertools`) if the image is pushed to the registry.</span></span> <span data-ttu-id="b64d5-165">Můžete také změnit název image přidat adresu URL privátního registru (například `privateregistry.domain.com/hellodockertools`) v závislosti na konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="b64d5-165">Alternatively, change the image name to include the private registry URL (for example, `privateregistry.domain.com/hellodockertools`) depending on the configuration.</span></span>

<span data-ttu-id="b64d5-166">Pokud chcete různé chování v závislosti na konfiguraci sestavení (například ladění nebo vydání), přidejte určených pro konfigurace *docker-compose* soubory.</span><span class="sxs-lookup"><span data-stu-id="b64d5-166">If you want different behavior based on the build configuration (for example, Debug or Release), add configuration-specific *docker-compose* files.</span></span> <span data-ttu-id="b64d5-167">Soubory by měly být pojmenovány podle konfigurace sestavení (například *docker compose.vs.debug.yml* a *docker compose.vs.release.yml*) a je umístěná ve stejném umístění jako *docker compose override.yml* souboru.</span><span class="sxs-lookup"><span data-stu-id="b64d5-167">The files should be named according to the build configuration (for example, *docker-compose.vs.debug.yml* and *docker-compose.vs.release.yml*) and placed in the same location as the *docker-compose-override.yml* file.</span></span> 

<span data-ttu-id="b64d5-168">Použití souborů určených pro konfigurace přepsání, můžete zadat jinou konfiguraci nastavení (jako jsou proměnné prostředí a vstupní body) u sestavení konfigurace Debug a Release.</span><span class="sxs-lookup"><span data-stu-id="b64d5-168">Using the configuration-specific override files, you can specify different configuration settings (such as environment variables or entry points) for Debug and Release build configurations.</span></span>

### <a name="service-fabric"></a><span data-ttu-id="b64d5-169">Service Fabric</span><span class="sxs-lookup"><span data-stu-id="b64d5-169">Service Fabric</span></span>

<span data-ttu-id="b64d5-170">Kromě základní [požadavky](#prerequisites), [Service Fabric](/azure/service-fabric/) řešení Orchestrace požadavky splněné následující požadavky:</span><span class="sxs-lookup"><span data-stu-id="b64d5-170">In addition to the base [Prerequisites](#prerequisites), the [Service Fabric](/azure/service-fabric/) orchestration solution demands the following prerequisites:</span></span>

* <span data-ttu-id="b64d5-171">[Microsoft Azure Service Fabric SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK) version 2.6 nebo novější</span><span class="sxs-lookup"><span data-stu-id="b64d5-171">[Microsoft Azure Service Fabric SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK) version 2.6 or later</span></span>
* <span data-ttu-id="b64d5-172">Visual Studio **vývoj pro Azure** pracovního vytížení</span><span class="sxs-lookup"><span data-stu-id="b64d5-172">Visual Studio's **Azure Development** workload</span></span>

<span data-ttu-id="b64d5-173">Service Fabric nepodporuje spuštěné kontejnery Linuxu v místním vývojovém clusteru na Windows.</span><span class="sxs-lookup"><span data-stu-id="b64d5-173">Service Fabric doesn't support running Linux containers in the local development cluster on Windows.</span></span> <span data-ttu-id="b64d5-174">Pokud projekt již používá kontejner Linuxu, Visual Studio vyzve k přepnout na kontejnery Windows.</span><span class="sxs-lookup"><span data-stu-id="b64d5-174">If the project is already using a Linux container, Visual Studio prompts to switch to Windows containers.</span></span>

<span data-ttu-id="b64d5-175">Visual Studio Tools for Docker provádět následující úlohy:</span><span class="sxs-lookup"><span data-stu-id="b64d5-175">The Visual Studio Tools for Docker do the following tasks:</span></span>

* <span data-ttu-id="b64d5-176">Přidá  *&lt;project_name&gt;aplikace* **aplikace Service Fabric** projektu do řešení.</span><span class="sxs-lookup"><span data-stu-id="b64d5-176">Adds a *&lt;project_name&gt;Application* **Service Fabric Application** project to the solution.</span></span>
* <span data-ttu-id="b64d5-177">Přidá *soubor Dockerfile* a *.dockerignore* soubor do projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b64d5-177">Adds a *Dockerfile* and a *.dockerignore* file to the ASP.NET Core project.</span></span> <span data-ttu-id="b64d5-178">Pokud *soubor Dockerfile* již existuje v projektu ASP.NET Core se přejmenovalo na *Dockerfile.original*.</span><span class="sxs-lookup"><span data-stu-id="b64d5-178">If a *Dockerfile* already exists in the ASP.NET Core project, it's renamed to *Dockerfile.original*.</span></span> <span data-ttu-id="b64d5-179">Nový *soubor Dockerfile*, podobně jako následujícím vytvoření:</span><span class="sxs-lookup"><span data-stu-id="b64d5-179">A new *Dockerfile*, similar to the following, is created:</span></span>

    [!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile)]

* <span data-ttu-id="b64d5-180">Přidá `<IsServiceFabricServiceProject>` prvek do projektu ASP.NET Core *.csproj* souboru:</span><span class="sxs-lookup"><span data-stu-id="b64d5-180">Adds an `<IsServiceFabricServiceProject>` element to the ASP.NET Core project's *.csproj* file:</span></span>

    [!code-xml[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/HelloDockerTools.csproj?name=snippet_IsServiceFabricServiceProject)]

* <span data-ttu-id="b64d5-181">Přidá *PackageRoot* složku pro projekt ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b64d5-181">Adds a *PackageRoot* folder to the ASP.NET Core project.</span></span> <span data-ttu-id="b64d5-182">Složka obsahuje manifest služby a nastavení pro novou službu.</span><span class="sxs-lookup"><span data-stu-id="b64d5-182">The folder includes the service manifest and settings for the new service.</span></span>

<span data-ttu-id="b64d5-183">Další informace najdete v tématu [nasazení aplikace .NET v kontejneru Windows do Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container).</span><span class="sxs-lookup"><span data-stu-id="b64d5-183">For more information, see [Deploy a .NET app in a Windows container to Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container).</span></span>

## <a name="debug"></a><span data-ttu-id="b64d5-184">Ladění</span><span class="sxs-lookup"><span data-stu-id="b64d5-184">Debug</span></span>

<span data-ttu-id="b64d5-185">Vyberte **Docker** z ladění rozevírací seznam v panelu nástrojů a spusťte ladění aplikace.</span><span class="sxs-lookup"><span data-stu-id="b64d5-185">Select **Docker** from the debug drop-down in the toolbar, and start debugging the app.</span></span> <span data-ttu-id="b64d5-186">**Docker** zobrazení **výstup** okno zobrazuje probíhají následující akce:</span><span class="sxs-lookup"><span data-stu-id="b64d5-186">The **Docker** view of the **Output** window shows the following actions taking place:</span></span>

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="b64d5-187">*Modulu runtime 2.1 aspnetcore* značku *microsoft/dotnet* bitové kopie modulu runtime je získali (pokud ještě není v mezipaměti).</span><span class="sxs-lookup"><span data-stu-id="b64d5-187">The *2.1-aspnetcore-runtime* tag of the *microsoft/dotnet* runtime image is acquired (if not already in the cache).</span></span> <span data-ttu-id="b64d5-188">Na obrázku nainstaluje moduly runtime ASP.NET Core a .NET Core a přidruženými knihovnami.</span><span class="sxs-lookup"><span data-stu-id="b64d5-188">The image installs the ASP.NET Core and .NET Core runtimes and associated libraries.</span></span> <span data-ttu-id="b64d5-189">Je optimalizovaný pro spouštění aplikací ASP.NET Core v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="b64d5-189">It's optimized for running ASP.NET Core apps in production.</span></span>
* <span data-ttu-id="b64d5-190">`ASPNETCORE_ENVIRONMENT` Proměnná prostředí je nastavená na `Development` v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="b64d5-190">The `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development` within the container.</span></span>
* <span data-ttu-id="b64d5-191">Dva porty dynamicky přidělovanou vystaveny: jeden pro HTTP a jeden pro protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b64d5-191">Two dynamically assigned ports are exposed: one for HTTP and one for HTTPS.</span></span> <span data-ttu-id="b64d5-192">Port přiřazen k místnímu hostiteli může být dotázán pomocí `docker ps` příkazu.</span><span class="sxs-lookup"><span data-stu-id="b64d5-192">The port assigned to localhost can be queried with the `docker ps` command.</span></span>
* <span data-ttu-id="b64d5-193">Aplikace se zkopíruje do kontejneru.</span><span class="sxs-lookup"><span data-stu-id="b64d5-193">The app is copied to the container.</span></span>
* <span data-ttu-id="b64d5-194">Spustí výchozí prohlížeč se ladicí program připojený ke kontejneru pomocí dynamicky přiřazeného portu.</span><span class="sxs-lookup"><span data-stu-id="b64d5-194">The default browser is launched with the debugger attached to the container using the dynamically assigned port.</span></span>

<span data-ttu-id="b64d5-195">Výsledný image Dockeru aplikace je označený jako *dev*.</span><span class="sxs-lookup"><span data-stu-id="b64d5-195">The resulting Docker image of the app is tagged as *dev*.</span></span> <span data-ttu-id="b64d5-196">Na obrázku je založen na *modulu runtime 2.1 aspnetcore* značku *microsoft/dotnet* základní image.</span><span class="sxs-lookup"><span data-stu-id="b64d5-196">The image is based on the *2.1-aspnetcore-runtime* tag of the *microsoft/dotnet* base image.</span></span> <span data-ttu-id="b64d5-197">Spustit `docker images` v příkaz **Konzola správce balíčků** okno (PMC).</span><span class="sxs-lookup"><span data-stu-id="b64d5-197">Run the `docker images` command in the **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="b64d5-198">Image na počítači se zobrazí:</span><span class="sxs-lookup"><span data-stu-id="b64d5-198">The images on the machine are displayed:</span></span>

```console
REPOSITORY        TAG                     IMAGE ID      CREATED         SIZE
hellodockertools  dev                     d72ce0f1dfe7  30 seconds ago  255MB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago      255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* <span data-ttu-id="b64d5-199">*Microsoft/aspnetcore* bitové kopie modulu runtime je získali (pokud ještě není v mezipaměti).</span><span class="sxs-lookup"><span data-stu-id="b64d5-199">The *microsoft/aspnetcore* runtime image is acquired (if not already in the cache).</span></span>
* <span data-ttu-id="b64d5-200">`ASPNETCORE_ENVIRONMENT` Proměnná prostředí je nastavená na `Development` v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="b64d5-200">The `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development` within the container.</span></span>
* <span data-ttu-id="b64d5-201">Port 80 je vystavena a mapují na dynamicky přidělovanou port pro místního hostitele.</span><span class="sxs-lookup"><span data-stu-id="b64d5-201">Port 80 is exposed and mapped to a dynamically assigned port for localhost.</span></span> <span data-ttu-id="b64d5-202">Port, který je určený hostitelem Dockeru a může být dotázán pomocí `docker ps` příkazu.</span><span class="sxs-lookup"><span data-stu-id="b64d5-202">The port is determined by the Docker host and can be queried with the `docker ps` command.</span></span>
* <span data-ttu-id="b64d5-203">Aplikace se zkopíruje do kontejneru.</span><span class="sxs-lookup"><span data-stu-id="b64d5-203">The app is copied to the container.</span></span>
* <span data-ttu-id="b64d5-204">Spustí výchozí prohlížeč se ladicí program připojený ke kontejneru pomocí dynamicky přiřazeného portu.</span><span class="sxs-lookup"><span data-stu-id="b64d5-204">The default browser is launched with the debugger attached to the container using the dynamically assigned port.</span></span>

<span data-ttu-id="b64d5-205">Výsledný image Dockeru aplikace je označený jako *dev*.</span><span class="sxs-lookup"><span data-stu-id="b64d5-205">The resulting Docker image of the app is tagged as *dev*.</span></span> <span data-ttu-id="b64d5-206">Na obrázku je založen na *microsoft/aspnetcore* základní image.</span><span class="sxs-lookup"><span data-stu-id="b64d5-206">The image is based on the *microsoft/aspnetcore* base image.</span></span> <span data-ttu-id="b64d5-207">Spustit `docker images` v příkaz **Konzola správce balíčků** okno (PMC).</span><span class="sxs-lookup"><span data-stu-id="b64d5-207">Run the `docker images` command in the **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="b64d5-208">Image na počítači se zobrazí:</span><span class="sxs-lookup"><span data-stu-id="b64d5-208">The images on the machine are displayed:</span></span>

```console
REPOSITORY            TAG  IMAGE ID      CREATED        SIZE
hellodockertools      dev  5fafe5d1ad5b  4 minutes ago  347MB
microsoft/aspnetcore  2.0  c69d39472da9  13 days ago    347MB
```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="b64d5-209">*Dev* image chybí obsah aplikace jako **ladění** konfigurace používají připojení svazku pro zajištění iterativní prostředí.</span><span class="sxs-lookup"><span data-stu-id="b64d5-209">The *dev* image lacks the app contents, as **Debug** configurations use volume mounting to provide the iterative experience.</span></span> <span data-ttu-id="b64d5-210">Chcete-li odeslat image, použijte **vydání** konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b64d5-210">To push an image, use the **Release** configuration.</span></span>

<span data-ttu-id="b64d5-211">Spustit `docker ps` příkazu v konzole PMC.</span><span class="sxs-lookup"><span data-stu-id="b64d5-211">Run the `docker ps` command in PMC.</span></span> <span data-ttu-id="b64d5-212">Všimněte si, že je aplikace spuštěna pomocí kontejneru:</span><span class="sxs-lookup"><span data-stu-id="b64d5-212">Notice the app is running using the container:</span></span>

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   21 seconds ago      Up 19 seconds       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="edit-and-continue"></a><span data-ttu-id="b64d5-213">Upravit a pokračovat</span><span class="sxs-lookup"><span data-stu-id="b64d5-213">Edit and continue</span></span>

<span data-ttu-id="b64d5-214">Změny statických souborů a zobrazeními Razor se automaticky aktualizují bez nutnosti kompilační krok.</span><span class="sxs-lookup"><span data-stu-id="b64d5-214">Changes to static files and Razor views are automatically updated without the need for a compilation step.</span></span> <span data-ttu-id="b64d5-215">Proveďte požadovanou změnu, uložte a aktualizujte prohlížeč, chcete-li zobrazit aktualizace.</span><span class="sxs-lookup"><span data-stu-id="b64d5-215">Make the change, save, and refresh the browser to view the update.</span></span>

<span data-ttu-id="b64d5-216">Úpravy souborů kódu vyžadují kompilace a restartování Kestrel v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="b64d5-216">Code file modifications require compilation and a restart of Kestrel within the container.</span></span> <span data-ttu-id="b64d5-217">Po provedení změn, použijte `CTRL+F5` provést proces a spusťte aplikaci v rámci kontejneru.</span><span class="sxs-lookup"><span data-stu-id="b64d5-217">After making the change, use `CTRL+F5` to perform the process and start the app within the container.</span></span> <span data-ttu-id="b64d5-218">Kontejner Dockeru se znovu sestavit nebo zastavená.</span><span class="sxs-lookup"><span data-stu-id="b64d5-218">The Docker container isn't rebuilt or stopped.</span></span> <span data-ttu-id="b64d5-219">Spustit `docker ps` příkazu v konzole PMC.</span><span class="sxs-lookup"><span data-stu-id="b64d5-219">Run the `docker ps` command in PMC.</span></span> <span data-ttu-id="b64d5-220">Všimněte si, že původní kontejneru je stále spuštěna od 10 minut před:</span><span class="sxs-lookup"><span data-stu-id="b64d5-220">Notice the original container is still running as of 10 minutes ago:</span></span>

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   10 minutes ago      Up 10 minutes       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="publish-docker-images"></a><span data-ttu-id="b64d5-221">Publikování Image Dockeru</span><span class="sxs-lookup"><span data-stu-id="b64d5-221">Publish Docker images</span></span>

<span data-ttu-id="b64d5-222">Po dokončení cyklu vývoje a ladění aplikace Visual Studio Tools for Docker pomáhají při vytváření bitové kopie produkční aplikace.</span><span class="sxs-lookup"><span data-stu-id="b64d5-222">Once the develop and debug cycle of the app is completed, the Visual Studio Tools for Docker assist in creating the production image of the app.</span></span> <span data-ttu-id="b64d5-223">Změna konfigurace rozevíracího seznamu **vydání** a sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="b64d5-223">Change the configuration drop-down to **Release** and build the app.</span></span> <span data-ttu-id="b64d5-224">Nástroje získá kompilace/publikování image z Docker Hubu (pokud to nebude již v mezipaměti).</span><span class="sxs-lookup"><span data-stu-id="b64d5-224">The tooling acquires the compile/publish image from Docker Hub (if not already in the cache).</span></span> <span data-ttu-id="b64d5-225">Image se vytvářejí s *nejnovější* značky, který může doručit bez vyžádání do privátního registru nebo centra Dockeru.</span><span class="sxs-lookup"><span data-stu-id="b64d5-225">An image is produced with the *latest* tag, which can be pushed to the private registry or Docker Hub.</span></span>

<span data-ttu-id="b64d5-226">Spustit `docker images` příkazu v konzole PMC zobrazíte seznam imagí.</span><span class="sxs-lookup"><span data-stu-id="b64d5-226">Run the `docker images` command in PMC to see the list of images.</span></span> <span data-ttu-id="b64d5-227">Zobrazí se výstup podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="b64d5-227">Output similar to the following is displayed:</span></span>

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

<span data-ttu-id="b64d5-228">`microsoft/aspnetcore-build` a `microsoft/aspnetcore` kopiemi uvedenými v předchozím výstupu jsou nahrazeny `microsoft/dotnet` Image od .NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="b64d5-228">The `microsoft/aspnetcore-build` and `microsoft/aspnetcore` images listed in the preceding output are replaced with `microsoft/dotnet` images as of .NET Core 2.1.</span></span> <span data-ttu-id="b64d5-229">Další informace najdete v tématu [oznámení migrace úložiště Dockeru](https://github.com/aspnet/Announcements/issues/298).</span><span class="sxs-lookup"><span data-stu-id="b64d5-229">For more information, see [the Docker repositories migration announcement](https://github.com/aspnet/Announcements/issues/298).</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="b64d5-230">`docker images` Příkaz vrátí zprostředkující obrázků s názvy úložišť a značky identifikována jako  *\<žádný >* (které nejsou uvedené výše).</span><span class="sxs-lookup"><span data-stu-id="b64d5-230">The `docker images` command returns intermediary images with repository names and tags identified as *\<none>* (not listed above).</span></span> <span data-ttu-id="b64d5-231">Tyto nepojmenované bitové kopie jsou vytvářeny [vícefázových sestavení](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) *soubor Dockerfile*.</span><span class="sxs-lookup"><span data-stu-id="b64d5-231">These unnamed images are produced by the [multi-stage build](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) *Dockerfile*.</span></span> <span data-ttu-id="b64d5-232">Pomáhají zvýšit efektivitu vytváření konečném obrazu&mdash;při změnách se znovu sestavit pouze nezbytné vrstvy.</span><span class="sxs-lookup"><span data-stu-id="b64d5-232">They improve the efficiency of building the final image&mdash;only the necessary layers are rebuilt when changes occur.</span></span> <span data-ttu-id="b64d5-233">Pokud zprostředkující bitové kopie jsou už je nepotřebujete, odstraňte je pomocí [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) příkazu.</span><span class="sxs-lookup"><span data-stu-id="b64d5-233">When the intermediary images are no longer needed, delete them using the [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) command.</span></span>

<span data-ttu-id="b64d5-234">Může být očekávání pro produkci nebo verze image, která se má menší velikosti oproti tomu na *dev* bitové kopie.</span><span class="sxs-lookup"><span data-stu-id="b64d5-234">There may be an expectation for the production or release image to be smaller in size by comparison to the *dev* image.</span></span> <span data-ttu-id="b64d5-235">Z důvodu mapování svazku byly spuštěné ladicího programu a aplikace z místního počítače a není v rámci kontejneru.</span><span class="sxs-lookup"><span data-stu-id="b64d5-235">Because of the volume mapping, the debugger and app were running from the local machine and not within the container.</span></span> <span data-ttu-id="b64d5-236">*Nejnovější* image se zabalit kód potřebné aplikace a spusťte tak aplikaci na hostitelském počítači.</span><span class="sxs-lookup"><span data-stu-id="b64d5-236">The *latest* image has packaged the necessary app code to run the app on a host machine.</span></span> <span data-ttu-id="b64d5-237">Delta proto je velikost kódu aplikace.</span><span class="sxs-lookup"><span data-stu-id="b64d5-237">Therefore, the delta is the size of the app code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b64d5-238">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b64d5-238">Additional resources</span></span>

* [<span data-ttu-id="b64d5-239">Kontejner vývoj pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b64d5-239">Container development with Visual Studio</span></span>](/visualstudio/containers)
* [<span data-ttu-id="b64d5-240">Azure Service Fabric: Příprava vývojového prostředí</span><span class="sxs-lookup"><span data-stu-id="b64d5-240">Azure Service Fabric: Prepare your development environment</span></span>](/azure/service-fabric/service-fabric-get-started)
* [<span data-ttu-id="b64d5-241">Nasazení aplikace .NET v kontejneru Windows do Azure Service Fabric</span><span class="sxs-lookup"><span data-stu-id="b64d5-241">Deploy a .NET app in a Windows container to Azure Service Fabric</span></span>](/azure/service-fabric/service-fabric-host-app-in-a-container)
* [<span data-ttu-id="b64d5-242">Řešení potíží s Visual Studio 2017 vývoj aplikací pomocí Dockeru</span><span class="sxs-lookup"><span data-stu-id="b64d5-242">Troubleshoot Visual Studio 2017 development with Docker</span></span>](/azure/vs-azure-tools-docker-troubleshooting-docker-errors)
* [<span data-ttu-id="b64d5-243">Visual Studio Tools pro úložiště GitHub Dockeru</span><span class="sxs-lookup"><span data-stu-id="b64d5-243">Visual Studio Tools for Docker GitHub repository</span></span>](https://github.com/Microsoft/DockerTools)

---
title: Nástroje kontejneru sady Visual Studio s ASP.NET Core
author: spboyer
description: Naučte se používat nástroje a Docker for Windows sady Visual Studio k kontejnerizaceí ASP.NET Core aplikace.
ms.author: scaddie
ms.custom: mvc
ms.date: 09/12/2018
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
uid: host-and-deploy/docker/visual-studio-tools-for-docker
ms.openlocfilehash: 68ecc4961401211f1304ac4a26fff1b31e4ad7eb
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627335"
---
# <a name="visual-studio-container-tools-with-aspnet-core"></a><span data-ttu-id="280eb-103">Nástroje kontejneru sady Visual Studio s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="280eb-103">Visual Studio Container Tools with ASP.NET Core</span></span>

<span data-ttu-id="280eb-104">Visual Studio 2017 a novější verze podporují sestavování, ladění a spouštění kontejnerových ASP.NET Core aplikací cílících na .NET Core.</span><span class="sxs-lookup"><span data-stu-id="280eb-104">Visual Studio 2017 and later versions support building, debugging, and running containerized ASP.NET Core apps targeting .NET Core.</span></span> <span data-ttu-id="280eb-105">Podporují se kontejnery Windows i Linux.</span><span class="sxs-lookup"><span data-stu-id="280eb-105">Both Windows and Linux containers are supported.</span></span>

<span data-ttu-id="280eb-106">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="280eb-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="280eb-107">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="280eb-107">Prerequisites</span></span>

* [<span data-ttu-id="280eb-108">Docker for Windows</span><span class="sxs-lookup"><span data-stu-id="280eb-108">Docker for Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)
* <span data-ttu-id="280eb-109">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s úlohou **vývoje .NET Core pro různé platformy**</span><span class="sxs-lookup"><span data-stu-id="280eb-109">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **.NET Core cross-platform development** workload</span></span>

## <a name="installation-and-setup"></a><span data-ttu-id="280eb-110">Instalace a nastavení</span><span class="sxs-lookup"><span data-stu-id="280eb-110">Installation and setup</span></span>

<span data-ttu-id="280eb-111">V případě instalace Docker si nejdříve přečtěte informace v části [Docker for Windows: co potřebujete znát před instalací](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)nástroje.</span><span class="sxs-lookup"><span data-stu-id="280eb-111">For Docker installation, first review the information at [Docker for Windows: What to know before you install](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).</span></span> <span data-ttu-id="280eb-112">Dále nainstalujte [Docker pro Windows](https://docs.docker.com/docker-for-windows/install/).</span><span class="sxs-lookup"><span data-stu-id="280eb-112">Next, install [Docker For Windows](https://docs.docker.com/docker-for-windows/install/).</span></span>

<span data-ttu-id="280eb-113">**[Sdílené jednotky](https://docs.docker.com/docker-for-windows/#shared-drives)** v Docker for Windows musí být nakonfigurovány tak, aby podporovaly mapování svazků a ladění.</span><span class="sxs-lookup"><span data-stu-id="280eb-113">**[Shared Drives](https://docs.docker.com/docker-for-windows/#shared-drives)** in Docker for Windows must be configured to support volume mapping and debugging.</span></span> <span data-ttu-id="280eb-114">Klikněte pravým tlačítkem myši na ikonu Docker systémového panelu, vyberte **Nastavení**a vyberte **sdílené jednotky**.</span><span class="sxs-lookup"><span data-stu-id="280eb-114">Right-click the System Tray's Docker icon, select **Settings**, and select **Shared Drives**.</span></span> <span data-ttu-id="280eb-115">Vyberte jednotku, kde Docker ukládá soubory.</span><span class="sxs-lookup"><span data-stu-id="280eb-115">Select the drive where Docker stores files.</span></span> <span data-ttu-id="280eb-116">Klikněte na **Použít**.</span><span class="sxs-lookup"><span data-stu-id="280eb-116">Click **Apply**.</span></span>

![Dialog pro výběr místního sdílení jednotky C pro kontejnery](visual-studio-tools-for-docker/_static/settings-shared-drives-win.png)

> [!TIP]
> <span data-ttu-id="280eb-118">Visual Studio 2017 verze 15,6 a pozdější dotaz, pokud nejsou nakonfigurovány **sdílené jednotky** .</span><span class="sxs-lookup"><span data-stu-id="280eb-118">Visual Studio 2017 versions 15.6 and later prompt when **Shared Drives** aren't configured.</span></span>

## <a name="add-a-project-to-a-docker-container"></a><span data-ttu-id="280eb-119">Přidání projektu do kontejneru Docker</span><span class="sxs-lookup"><span data-stu-id="280eb-119">Add a project to a Docker container</span></span>

<span data-ttu-id="280eb-120">Aby bylo možné projekt ASP.NET Core kontejnerizace, musí projekt cílit na .NET Core.</span><span class="sxs-lookup"><span data-stu-id="280eb-120">To containerize an ASP.NET Core project, the project must target .NET Core.</span></span> <span data-ttu-id="280eb-121">Jsou podporovány kontejnery Linux i Windows.</span><span class="sxs-lookup"><span data-stu-id="280eb-121">Both Linux and Windows containers are supported.</span></span>

<span data-ttu-id="280eb-122">Když do projektu přidáte podporu Docker, vyberte buď kontejner Windows, nebo Linux.</span><span class="sxs-lookup"><span data-stu-id="280eb-122">When adding Docker support to a project, choose either a Windows or a Linux container.</span></span> <span data-ttu-id="280eb-123">Hostitel Docker musí používat stejný typ kontejneru.</span><span class="sxs-lookup"><span data-stu-id="280eb-123">The Docker host must be running the same container type.</span></span> <span data-ttu-id="280eb-124">Chcete-li změnit typ kontejneru v běžící instanci Docker, klikněte pravým tlačítkem myši na ikonu Docker panelu systému a vyberte možnost **Přepnout na kontejnery Windows...** nebo **Přepnout na kontejnery platformy Linux..**..</span><span class="sxs-lookup"><span data-stu-id="280eb-124">To change the container type in the running Docker instance, right-click the System Tray's Docker icon and choose **Switch to Windows containers...** or **Switch to Linux containers...**.</span></span>

### <a name="new-app"></a><span data-ttu-id="280eb-125">Nová aplikace</span><span class="sxs-lookup"><span data-stu-id="280eb-125">New app</span></span>

<span data-ttu-id="280eb-126">Při vytváření nové aplikace pomocí šablon projektů **ASP.NET Core webové aplikace** zaškrtněte políčko **Povolit podporu Docker** :</span><span class="sxs-lookup"><span data-stu-id="280eb-126">When creating a new app with the **ASP.NET Core Web Application** project templates, select the **Enable Docker Support** check box:</span></span>

![Zaškrtávací políčko Povolit podporu Docker](visual-studio-tools-for-docker/_static/enable-docker-support-check-box.png)

<span data-ttu-id="280eb-128">Pokud je cílová architektura rozhraní .NET Core, rozevírací seznam **OS** umožňuje výběr typu kontejneru.</span><span class="sxs-lookup"><span data-stu-id="280eb-128">If the target framework is .NET Core, the **OS** drop-down allows for the selection of a container type.</span></span>

### <a name="existing-app"></a><span data-ttu-id="280eb-129">Existující aplikace</span><span class="sxs-lookup"><span data-stu-id="280eb-129">Existing app</span></span>

<span data-ttu-id="280eb-130">Pro ASP.NET Core projekty cílené na .NET Core existují dvě možnosti, jak přidat podporu Docker prostřednictvím nástrojů.</span><span class="sxs-lookup"><span data-stu-id="280eb-130">For ASP.NET Core projects targeting .NET Core, there are two options for adding Docker support via the tooling.</span></span> <span data-ttu-id="280eb-131">Otevřete projekt v aplikaci Visual Studio a vyberte jednu z následujících možností:</span><span class="sxs-lookup"><span data-stu-id="280eb-131">Open the project in Visual Studio, and choose one of the following options:</span></span>

* <span data-ttu-id="280eb-132">V nabídce **projekt** vyberte možnost **Podpora Docker** .</span><span class="sxs-lookup"><span data-stu-id="280eb-132">Select **Docker Support** from the **Project** menu.</span></span>
* <span data-ttu-id="280eb-133">Klikněte pravým tlačítkem na projekt v **Průzkumník řešení** a vyberte **Přidat**  >  **podporu Docker**.</span><span class="sxs-lookup"><span data-stu-id="280eb-133">Right-click the project in **Solution Explorer** and select **Add** > **Docker Support**.</span></span>

<span data-ttu-id="280eb-134">Nástroje kontejnerů sady Visual Studio nepodporují přidávání Docker do existující ASP.NET Core projektu cílících na .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="280eb-134">The Visual Studio Container Tools don't support adding Docker to an existing ASP.NET Core project targeting .NET Framework.</span></span>

## <a name="dockerfile-overview"></a><span data-ttu-id="280eb-135">Souboru Dockerfile – přehled</span><span class="sxs-lookup"><span data-stu-id="280eb-135">Dockerfile overview</span></span>

<span data-ttu-id="280eb-136">Do kořenového adresáře projektu se přidá recept pro vytvoření finální image Docker *souboru Dockerfile*.</span><span class="sxs-lookup"><span data-stu-id="280eb-136">A *Dockerfile*, the recipe for creating a final Docker image, is added to the project root.</span></span> <span data-ttu-id="280eb-137">Porozumění příkazům, které jsou v něm, najdete v [referenčních informacích k souboru Dockerfile](https://docs.docker.com/engine/reference/builder/) .</span><span class="sxs-lookup"><span data-stu-id="280eb-137">Refer to [Dockerfile reference](https://docs.docker.com/engine/reference/builder/) for an understanding of the commands within it.</span></span> <span data-ttu-id="280eb-138">Tento konkrétní *souboru Dockerfile* používá [sestavení s více fázemi](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) se čtyřmi odlišnými pojmenovanými fázemi sestavení:</span><span class="sxs-lookup"><span data-stu-id="280eb-138">This particular *Dockerfile* uses a [multi-stage build](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) with four distinct, named build stages:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile.original?highlight=1,6,14,17)]

<span data-ttu-id="280eb-139">Předchozí *souboru Dockerfile* je založen na obrázku [Microsoft/dotNET](https://hub.docker.com/r/microsoft/dotnet/) .</span><span class="sxs-lookup"><span data-stu-id="280eb-139">The preceding *Dockerfile* is based on the [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/) image.</span></span> <span data-ttu-id="280eb-140">Tato základní image zahrnuje ASP.NET Core Runtime a balíčky NuGet.</span><span class="sxs-lookup"><span data-stu-id="280eb-140">This base image includes the ASP.NET Core runtime and NuGet packages.</span></span> <span data-ttu-id="280eb-141">Balíčky jsou kompilovány JIT (just-in-time), aby se zlepšil výkon při spuštění.</span><span class="sxs-lookup"><span data-stu-id="280eb-141">The packages are just-in-time (JIT) compiled to improve startup performance.</span></span>

<span data-ttu-id="280eb-142">Když je zaškrtnuté políčko **Konfigurovat pro protokol HTTPS** v dialogovém okně Nový projekt, *souboru Dockerfile* zpřístupňuje dva porty.</span><span class="sxs-lookup"><span data-stu-id="280eb-142">When the new project dialog's **Configure for HTTPS** check box is checked, the *Dockerfile* exposes two ports.</span></span> <span data-ttu-id="280eb-143">Pro přenosy HTTP se používá jeden port; druhý port se používá pro protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="280eb-143">One port is used for HTTP traffic; the other port is used for HTTPS.</span></span> <span data-ttu-id="280eb-144">Pokud políčko není zaškrtnuté, bude pro přenosy HTTP vystaven jeden port (80).</span><span class="sxs-lookup"><span data-stu-id="280eb-144">If the check box isn't checked, a single port (80) is exposed for HTTP traffic.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.0/HelloDockerTools/Dockerfile?highlight=1,5,13,16)]

<span data-ttu-id="280eb-145">Předchozí *souboru Dockerfile* vychází z image [Microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/) .</span><span class="sxs-lookup"><span data-stu-id="280eb-145">The preceding *Dockerfile* is based on the [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/) image.</span></span> <span data-ttu-id="280eb-146">Tato základní image obsahuje ASP.NET Core balíčky NuGet, které jsou kompilovány JIT (just-in-time), aby se zlepšil výkon při spuštění.</span><span class="sxs-lookup"><span data-stu-id="280eb-146">This base image includes the ASP.NET Core NuGet packages, which are just-in-time (JIT) compiled to improve startup performance.</span></span>

::: moniker-end

## <a name="add-container-orchestrator-support-to-an-app"></a><span data-ttu-id="280eb-147">Přidat do aplikace podporu nástroje Orchestrator pro kontejner</span><span class="sxs-lookup"><span data-stu-id="280eb-147">Add container orchestrator support to an app</span></span>

<span data-ttu-id="280eb-148">Sady Visual Studio 2017 verze 15,7 nebo novější podporují [Docker Compose](https://docs.docker.com/compose/overview/) jako jediné řešení orchestrace kontejnerů.</span><span class="sxs-lookup"><span data-stu-id="280eb-148">Visual Studio 2017 versions 15.7 or earlier support [Docker Compose](https://docs.docker.com/compose/overview/) as the sole container orchestration solution.</span></span> <span data-ttu-id="280eb-149">Artefakty Docker Compose se přidávají prostřednictvím **Přidat**  >  **podporu Docker**.</span><span class="sxs-lookup"><span data-stu-id="280eb-149">The Docker Compose artifacts are added via **Add** > **Docker Support**.</span></span>

<span data-ttu-id="280eb-150">Visual Studio 2017 verze 15,8 nebo novější přidat řešení orchestrace pouze v případě, že je pokyn.</span><span class="sxs-lookup"><span data-stu-id="280eb-150">Visual Studio 2017 versions 15.8 or later add an orchestration solution only when instructed.</span></span> <span data-ttu-id="280eb-151">Klikněte pravým tlačítkem na projekt v **Průzkumník řešení** a vyberte **Přidat**  >  **kontejner Orchestrator support**.</span><span class="sxs-lookup"><span data-stu-id="280eb-151">Right-click the project in **Solution Explorer** and select **Add** > **Container Orchestrator Support**.</span></span> <span data-ttu-id="280eb-152">K dispozici jsou následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="280eb-152">The following choices are available:</span></span> 

* [<span data-ttu-id="280eb-153">Docker Compose</span><span class="sxs-lookup"><span data-stu-id="280eb-153">Docker Compose</span></span>](#docker-compose)
* [<span data-ttu-id="280eb-154">Service Fabric</span><span class="sxs-lookup"><span data-stu-id="280eb-154">Service Fabric</span></span>](#service-fabric)
* [<span data-ttu-id="280eb-155">Kubernetes/Helm </span><span class="sxs-lookup"><span data-stu-id="280eb-155">Kubernetes/Helm </span></span>](https://helm.sh/)

### <a name="docker-compose"></a><span data-ttu-id="280eb-156">Docker Compose</span><span class="sxs-lookup"><span data-stu-id="280eb-156">Docker Compose</span></span>

<span data-ttu-id="280eb-157">Nástroje kontejnerů sady Visual Studio přidají do řešení projekt *Docker-sestavení* s následujícími soubory:</span><span class="sxs-lookup"><span data-stu-id="280eb-157">The Visual Studio Container Tools add a *docker-compose* project to the solution with the following files:</span></span>

* <span data-ttu-id="280eb-158">*Docker-tváře. dcproj*: soubor představující projekt.</span><span class="sxs-lookup"><span data-stu-id="280eb-158">*docker-compose.dcproj*: The file representing the project.</span></span> <span data-ttu-id="280eb-159">Obsahuje `<DockerTargetOS>` element určující operační systém, který se má použít.</span><span class="sxs-lookup"><span data-stu-id="280eb-159">Includes a `<DockerTargetOS>` element specifying the OS to be used.</span></span>
* <span data-ttu-id="280eb-160">*. dockerignore*: vypíše vzor souborů a adresářů, které mají být vyloučeny při generování kontextu sestavení.</span><span class="sxs-lookup"><span data-stu-id="280eb-160">*.dockerignore*: Lists the file and directory patterns to exclude when generating a build context.</span></span>
* <span data-ttu-id="280eb-161">*Docker-Compose. yml*: základní [Docker Compose](https://docs.docker.com/compose/overview/) soubor, který slouží k definování kolekce imagí sestavených a spuštěných s `docker-compose build` a v `docker-compose run` uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="280eb-161">*docker-compose.yml*: The base [Docker Compose](https://docs.docker.com/compose/overview/) file used to define the collection of images built and run with `docker-compose build` and `docker-compose run`, respectively.</span></span>
* <span data-ttu-id="280eb-162">*Docker-Compose. override. yml*: volitelný soubor, který čte Docker Compose, s přepsáním konfigurace pro služby.</span><span class="sxs-lookup"><span data-stu-id="280eb-162">*docker-compose.override.yml*: An optional file, read by Docker Compose, with configuration overrides for services.</span></span> <span data-ttu-id="280eb-163">Visual Studio provede `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` sloučení těchto souborů.</span><span class="sxs-lookup"><span data-stu-id="280eb-163">Visual Studio executes `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` to merge these files.</span></span>

<span data-ttu-id="280eb-164">Soubor *Docker-Compose. yml* odkazuje na název bitové kopie, která je vytvořena při spuštění projektu:</span><span class="sxs-lookup"><span data-stu-id="280eb-164">The *docker-compose.yml* file references the name of the image that's created when the project runs:</span></span>

[!code-yaml[](visual-studio-tools-for-docker/samples/2.0/docker-compose.yml?highlight=5)]

<span data-ttu-id="280eb-165">V předchozím příkladu `image: hellodockertools` vygeneruje obrázek, `hellodockertools:dev` když aplikace běží v režimu **ladění** .</span><span class="sxs-lookup"><span data-stu-id="280eb-165">In the preceding example, `image: hellodockertools` generates the image `hellodockertools:dev` when the app runs in **Debug** mode.</span></span> <span data-ttu-id="280eb-166">`hellodockertools:latest`Obrázek se vygeneruje, když aplikace běží v režimu **vydání** .</span><span class="sxs-lookup"><span data-stu-id="280eb-166">The `hellodockertools:latest` image is generated when the app runs in **Release** mode.</span></span>

<span data-ttu-id="280eb-167">Pokud je image vložená do registru, zadejte předponu názvu image k uživatelskému jménu [Docker](https://hub.docker.com/) (například `dockerhubusername/hellodockertools` ).</span><span class="sxs-lookup"><span data-stu-id="280eb-167">Prefix the image name with the [Docker Hub](https://hub.docker.com/) username (for example, `dockerhubusername/hellodockertools`) if the image is pushed to the registry.</span></span> <span data-ttu-id="280eb-168">Případně změňte název bitové kopie tak, aby zahrnoval adresu URL privátního registru (například `privateregistry.domain.com/hellodockertools` ) v závislosti na konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="280eb-168">Alternatively, change the image name to include the private registry URL (for example, `privateregistry.domain.com/hellodockertools`) depending on the configuration.</span></span>

<span data-ttu-id="280eb-169">Pokud chcete jiné chování na základě konfigurace sestavení (například ladění nebo vydaná verze), přidejte soubory *Docker-skládání* specifické pro konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="280eb-169">If you want different behavior based on the build configuration (for example, Debug or Release), add configuration-specific *docker-compose* files.</span></span> <span data-ttu-id="280eb-170">Soubory by měly být pojmenovány podle konfigurace sestavení (například *Docker-Compose. vs. Debug. yml* a *Docker-Compose. vs. Release. yml*) a umístěny do stejného umístění jako soubor *Docker-Compose-override. yml* .</span><span class="sxs-lookup"><span data-stu-id="280eb-170">The files should be named according to the build configuration (for example, *docker-compose.vs.debug.yml* and *docker-compose.vs.release.yml*) and placed in the same location as the *docker-compose-override.yml* file.</span></span> 

<span data-ttu-id="280eb-171">Pomocí souborů přepsání specifických pro konfiguraci můžete pro konfigurace ladění a vydání zadat různá nastavení konfigurace (například proměnné prostředí nebo vstupní body).</span><span class="sxs-lookup"><span data-stu-id="280eb-171">Using the configuration-specific override files, you can specify different configuration settings (such as environment variables or entry points) for Debug and Release build configurations.</span></span>

<span data-ttu-id="280eb-172">Aby bylo možné Docker Compose zobrazit možnost spuštění v aplikaci Visual Studio, musí být projekt Docker spouštěn jako projekt po spuštění.</span><span class="sxs-lookup"><span data-stu-id="280eb-172">For Docker Compose to display an option to run in Visual Studio, the docker project must be the startup project.</span></span>

### <a name="service-fabric"></a><span data-ttu-id="280eb-173">Service Fabric</span><span class="sxs-lookup"><span data-stu-id="280eb-173">Service Fabric</span></span>

<span data-ttu-id="280eb-174">Kromě základních [požadavků](#prerequisites)řešení orchestrace [Service Fabric](/azure/service-fabric/) vyžaduje následující požadavky:</span><span class="sxs-lookup"><span data-stu-id="280eb-174">In addition to the base [Prerequisites](#prerequisites), the [Service Fabric](/azure/service-fabric/) orchestration solution demands the following prerequisites:</span></span>

* <span data-ttu-id="280eb-175">[Microsoft Azure Service Fabric SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK) verze 2,6 nebo novější</span><span class="sxs-lookup"><span data-stu-id="280eb-175">[Microsoft Azure Service Fabric SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK) version 2.6 or later</span></span>
* <span data-ttu-id="280eb-176">Úloha **vývoje Azure** v aplikaci Visual Studio</span><span class="sxs-lookup"><span data-stu-id="280eb-176">Visual Studio's **Azure Development** workload</span></span>

<span data-ttu-id="280eb-177">Service Fabric nepodporuje spouštění kontejnerů Linux v místním vývojovém clusteru ve Windows.</span><span class="sxs-lookup"><span data-stu-id="280eb-177">Service Fabric doesn't support running Linux containers in the local development cluster on Windows.</span></span> <span data-ttu-id="280eb-178">Pokud projekt již používá kontejner pro Linux, Visual Studio zobrazí výzvu k přepnutí do kontejnerů Windows.</span><span class="sxs-lookup"><span data-stu-id="280eb-178">If the project is already using a Linux container, Visual Studio prompts to switch to Windows containers.</span></span>

<span data-ttu-id="280eb-179">Nástroje kontejneru sady Visual Studio dělají následující úlohy:</span><span class="sxs-lookup"><span data-stu-id="280eb-179">The Visual Studio Container Tools do the following tasks:</span></span>

* <span data-ttu-id="280eb-180">Přidá do řešení projekt aplikace \* &lt; project_name &gt; aplikace\* **Service Fabric** .</span><span class="sxs-lookup"><span data-stu-id="280eb-180">Adds a *&lt;project_name&gt;Application* **Service Fabric Application** project to the solution.</span></span>
* <span data-ttu-id="280eb-181">Přidá do projektu ASP.NET Core soubor s příponou *souboru Dockerfile* a *. dockerignore* .</span><span class="sxs-lookup"><span data-stu-id="280eb-181">Adds a *Dockerfile* and a *.dockerignore* file to the ASP.NET Core project.</span></span> <span data-ttu-id="280eb-182">Pokud *souboru Dockerfile* už v projektu ASP.NET Core existuje, přejmenuje se na *souboru Dockerfile. originál*.</span><span class="sxs-lookup"><span data-stu-id="280eb-182">If a *Dockerfile* already exists in the ASP.NET Core project, it's renamed to *Dockerfile.original*.</span></span> <span data-ttu-id="280eb-183">Vytvoří se nový *souboru Dockerfile*, který bude vypadat přibližně takto:</span><span class="sxs-lookup"><span data-stu-id="280eb-183">A new *Dockerfile*, similar to the following, is created:</span></span>

    [!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile)]

* <span data-ttu-id="280eb-184">Přidá `<IsServiceFabricServiceProject>` prvek do souboru *. csproj* ASP.NET Coreho projektu:</span><span class="sxs-lookup"><span data-stu-id="280eb-184">Adds an `<IsServiceFabricServiceProject>` element to the ASP.NET Core project's *.csproj* file:</span></span>

    [!code-xml[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/HelloDockerTools.csproj?name=snippet_IsServiceFabricServiceProject)]

* <span data-ttu-id="280eb-185">Přidá složku *PackageRoot* do projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="280eb-185">Adds a *PackageRoot* folder to the ASP.NET Core project.</span></span> <span data-ttu-id="280eb-186">Složka obsahuje manifest a nastavení služby pro novou službu.</span><span class="sxs-lookup"><span data-stu-id="280eb-186">The folder includes the service manifest and settings for the new service.</span></span>

<span data-ttu-id="280eb-187">Další informace najdete v tématu [nasazení aplikace .NET v kontejneru Windows do Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container).</span><span class="sxs-lookup"><span data-stu-id="280eb-187">For more information, see [Deploy a .NET app in a Windows container to Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container).</span></span>

## <a name="debug"></a><span data-ttu-id="280eb-188">Ladění</span><span class="sxs-lookup"><span data-stu-id="280eb-188">Debug</span></span>

<span data-ttu-id="280eb-189">V rozevíracím seznamu ladění na panelu nástrojů vyberte **Docker** a spusťte ladění aplikace.</span><span class="sxs-lookup"><span data-stu-id="280eb-189">Select **Docker** from the debug drop-down in the toolbar, and start debugging the app.</span></span> <span data-ttu-id="280eb-190">Zobrazení **Docker** okna **výstup** zobrazuje následující akce:</span><span class="sxs-lookup"><span data-stu-id="280eb-190">The **Docker** view of the **Output** window shows the following actions taking place:</span></span>

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="280eb-191">Byla získána značka *2,1-aspnetcore-runtime* bitové kopie modulu runtime *aplikace Microsoft/dotNET* (Pokud ještě není v mezipaměti).</span><span class="sxs-lookup"><span data-stu-id="280eb-191">The *2.1-aspnetcore-runtime* tag of the *microsoft/dotnet* runtime image is acquired (if not already in the cache).</span></span> <span data-ttu-id="280eb-192">Bitová kopie nainstaluje ASP.NET Core a moduly runtime .NET Core a přidružené knihovny.</span><span class="sxs-lookup"><span data-stu-id="280eb-192">The image installs the ASP.NET Core and .NET Core runtimes and associated libraries.</span></span> <span data-ttu-id="280eb-193">Je optimalizovaná pro spouštění ASP.NET Corech aplikací v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="280eb-193">It's optimized for running ASP.NET Core apps in production.</span></span>
* <span data-ttu-id="280eb-194">`ASPNETCORE_ENVIRONMENT`Proměnná prostředí je nastavena na hodnotu `Development` v rámci kontejneru.</span><span class="sxs-lookup"><span data-stu-id="280eb-194">The `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development` within the container.</span></span>
* <span data-ttu-id="280eb-195">K dispozici jsou dva dynamicky přiřazené porty: jeden pro HTTP a jeden pro protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="280eb-195">Two dynamically assigned ports are exposed: one for HTTP and one for HTTPS.</span></span> <span data-ttu-id="280eb-196">Pomocí příkazu se dá zadat dotaz na port přiřazený k hostiteli localhost `docker ps` .</span><span class="sxs-lookup"><span data-stu-id="280eb-196">The port assigned to localhost can be queried with the `docker ps` command.</span></span>
* <span data-ttu-id="280eb-197">Aplikace se zkopíruje do kontejneru.</span><span class="sxs-lookup"><span data-stu-id="280eb-197">The app is copied to the container.</span></span>
* <span data-ttu-id="280eb-198">Výchozí prohlížeč se spustí s ladicím programem připojeným ke kontejneru pomocí dynamicky přiřazeného portu.</span><span class="sxs-lookup"><span data-stu-id="280eb-198">The default browser is launched with the debugger attached to the container using the dynamically assigned port.</span></span>

<span data-ttu-id="280eb-199">Výsledná image Docker aplikace je označená jako *vývoj*.</span><span class="sxs-lookup"><span data-stu-id="280eb-199">The resulting Docker image of the app is tagged as *dev*.</span></span> <span data-ttu-id="280eb-200">Obrázek je založen na značce *2,1-aspnetcore-runtime* základní image *Microsoft/dotNET* .</span><span class="sxs-lookup"><span data-stu-id="280eb-200">The image is based on the *2.1-aspnetcore-runtime* tag of the *microsoft/dotnet* base image.</span></span> <span data-ttu-id="280eb-201">Spusťte `docker images` příkaz v okně **konzoly Správce balíčků** (PMC).</span><span class="sxs-lookup"><span data-stu-id="280eb-201">Run the `docker images` command in the **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="280eb-202">Zobrazí se obrázky na počítači:</span><span class="sxs-lookup"><span data-stu-id="280eb-202">The images on the machine are displayed:</span></span>

```console
REPOSITORY        TAG                     IMAGE ID      CREATED         SIZE
hellodockertools  dev                     d72ce0f1dfe7  30 seconds ago  255MB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago      255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* <span data-ttu-id="280eb-203">Byla získána bitová kopie modulu runtime *aplikace Microsoft/aspnetcore* (Pokud ještě není v mezipaměti).</span><span class="sxs-lookup"><span data-stu-id="280eb-203">The *microsoft/aspnetcore* runtime image is acquired (if not already in the cache).</span></span>
* <span data-ttu-id="280eb-204">`ASPNETCORE_ENVIRONMENT`Proměnná prostředí je nastavena na hodnotu `Development` v rámci kontejneru.</span><span class="sxs-lookup"><span data-stu-id="280eb-204">The `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development` within the container.</span></span>
* <span data-ttu-id="280eb-205">Port 80 je vystavený a mapovaný na dynamicky přiřazený port pro localhost.</span><span class="sxs-lookup"><span data-stu-id="280eb-205">Port 80 is exposed and mapped to a dynamically assigned port for localhost.</span></span> <span data-ttu-id="280eb-206">Port je určen hostitelem Docker a lze k němu zadat dotaz pomocí `docker ps` příkazu.</span><span class="sxs-lookup"><span data-stu-id="280eb-206">The port is determined by the Docker host and can be queried with the `docker ps` command.</span></span>
* <span data-ttu-id="280eb-207">Aplikace se zkopíruje do kontejneru.</span><span class="sxs-lookup"><span data-stu-id="280eb-207">The app is copied to the container.</span></span>
* <span data-ttu-id="280eb-208">Výchozí prohlížeč se spustí s ladicím programem připojeným ke kontejneru pomocí dynamicky přiřazeného portu.</span><span class="sxs-lookup"><span data-stu-id="280eb-208">The default browser is launched with the debugger attached to the container using the dynamically assigned port.</span></span>

<span data-ttu-id="280eb-209">Výsledná image Docker aplikace je označená jako *vývoj*.</span><span class="sxs-lookup"><span data-stu-id="280eb-209">The resulting Docker image of the app is tagged as *dev*.</span></span> <span data-ttu-id="280eb-210">Obrázek je založen na základní imagi *Microsoft/aspnetcore* .</span><span class="sxs-lookup"><span data-stu-id="280eb-210">The image is based on the *microsoft/aspnetcore* base image.</span></span> <span data-ttu-id="280eb-211">Spusťte `docker images` příkaz v okně **konzoly Správce balíčků** (PMC).</span><span class="sxs-lookup"><span data-stu-id="280eb-211">Run the `docker images` command in the **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="280eb-212">Zobrazí se obrázky na počítači:</span><span class="sxs-lookup"><span data-stu-id="280eb-212">The images on the machine are displayed:</span></span>

```console
REPOSITORY            TAG  IMAGE ID      CREATED        SIZE
hellodockertools      dev  5fafe5d1ad5b  4 minutes ago  347MB
microsoft/aspnetcore  2.0  c69d39472da9  13 days ago    347MB
```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="280eb-213">K *vývojové* imagi chybí obsah aplikace, protože konfigurace **ladění** používá k zajištění iteračního prostředí připojení svazku.</span><span class="sxs-lookup"><span data-stu-id="280eb-213">The *dev* image lacks the app contents, as **Debug** configurations use volume mounting to provide the iterative experience.</span></span> <span data-ttu-id="280eb-214">Pokud chcete odeslat image, použijte konfiguraci **vydané verze** .</span><span class="sxs-lookup"><span data-stu-id="280eb-214">To push an image, use the **Release** configuration.</span></span>

<span data-ttu-id="280eb-215">Spusťte `docker ps` příkaz v PMC.</span><span class="sxs-lookup"><span data-stu-id="280eb-215">Run the `docker ps` command in PMC.</span></span> <span data-ttu-id="280eb-216">Všimněte si, že aplikace je spuštěná pomocí kontejneru:</span><span class="sxs-lookup"><span data-stu-id="280eb-216">Notice the app is running using the container:</span></span>

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   21 seconds ago      Up 19 seconds       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="edit-and-continue"></a><span data-ttu-id="280eb-217">Upravit a pokračovat</span><span class="sxs-lookup"><span data-stu-id="280eb-217">Edit and continue</span></span>

<span data-ttu-id="280eb-218">Změny statických souborů a Razor zobrazení se automaticky aktualizují, aniž by bylo nutné krok kompilace.</span><span class="sxs-lookup"><span data-stu-id="280eb-218">Changes to static files and Razor views are automatically updated without the need for a compilation step.</span></span> <span data-ttu-id="280eb-219">Pro zobrazení aktualizace proveďte změnu, uložte a aktualizujte prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="280eb-219">Make the change, save, and refresh the browser to view the update.</span></span>

<span data-ttu-id="280eb-220">Změny souborů kódu vyžadují kompilaci a restartování Kestrel v rámci kontejneru.</span><span class="sxs-lookup"><span data-stu-id="280eb-220">Code file modifications require compilation and a restart of Kestrel within the container.</span></span> <span data-ttu-id="280eb-221">Po provedení změny použijte `CTRL+F5` k provedení procesu a spustí aplikaci v rámci kontejneru.</span><span class="sxs-lookup"><span data-stu-id="280eb-221">After making the change, use `CTRL+F5` to perform the process and start the app within the container.</span></span> <span data-ttu-id="280eb-222">Kontejner Docker není znovu sestaven ani zastaven.</span><span class="sxs-lookup"><span data-stu-id="280eb-222">The Docker container isn't rebuilt or stopped.</span></span> <span data-ttu-id="280eb-223">Spusťte `docker ps` příkaz v PMC.</span><span class="sxs-lookup"><span data-stu-id="280eb-223">Run the `docker ps` command in PMC.</span></span> <span data-ttu-id="280eb-224">Všimněte si, že původní kontejner pořád běží před 10 minutami:</span><span class="sxs-lookup"><span data-stu-id="280eb-224">Notice the original container is still running as of 10 minutes ago:</span></span>

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   10 minutes ago      Up 10 minutes       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="publish-docker-images"></a><span data-ttu-id="280eb-225">Publikování imagí Docker</span><span class="sxs-lookup"><span data-stu-id="280eb-225">Publish Docker images</span></span>

<span data-ttu-id="280eb-226">Až se cyklus vývoje a ladění aplikace dokončí, pomůže vám nástroje kontejnerů sady Visual Studio při vytváření produkční image aplikace.</span><span class="sxs-lookup"><span data-stu-id="280eb-226">Once the develop and debug cycle of the app is completed, the Visual Studio Container Tools assist in creating the production image of the app.</span></span> <span data-ttu-id="280eb-227">Změňte rozevírací seznam konfigurace na **vydaná** a sestavte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="280eb-227">Change the configuration drop-down to **Release** and build the app.</span></span> <span data-ttu-id="280eb-228">Nástroj získá obrázek kompilace/publikování z Docker Hub (Pokud ještě není v mezipaměti).</span><span class="sxs-lookup"><span data-stu-id="280eb-228">The tooling acquires the compile/publish image from Docker Hub (if not already in the cache).</span></span> <span data-ttu-id="280eb-229">Obrázek se vytvoří s *nejnovější* značkou, která se dá vložit do privátního registru nebo Dock centra.</span><span class="sxs-lookup"><span data-stu-id="280eb-229">An image is produced with the *latest* tag, which can be pushed to the private registry or Docker Hub.</span></span>

<span data-ttu-id="280eb-230">Pokud `docker images` chcete zobrazit seznam imagí, spusťte příkaz v PMC.</span><span class="sxs-lookup"><span data-stu-id="280eb-230">Run the `docker images` command in PMC to see the list of images.</span></span> <span data-ttu-id="280eb-231">Zobrazí se výstup podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="280eb-231">Output similar to the following is displayed:</span></span>

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

<span data-ttu-id="280eb-232">`microsoft/aspnetcore-build`Image a `microsoft/aspnetcore` uvedené v předchozím výstupu se nahrazují `microsoft/dotnet` obrázky od .NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="280eb-232">The `microsoft/aspnetcore-build` and `microsoft/aspnetcore` images listed in the preceding output are replaced with `microsoft/dotnet` images as of .NET Core 2.1.</span></span> <span data-ttu-id="280eb-233">Další informace najdete v tématu [oznámení o migraci úložiště Docker](https://github.com/aspnet/Announcements/issues/298).</span><span class="sxs-lookup"><span data-stu-id="280eb-233">For more information, see [the Docker repositories migration announcement](https://github.com/aspnet/Announcements/issues/298).</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="280eb-234">`docker images`Příkaz vrátí zprostředkující image s názvy a značkami úložiště, které jsou označeny jako *\<none>* (nejsou uvedeny výše).</span><span class="sxs-lookup"><span data-stu-id="280eb-234">The `docker images` command returns intermediary images with repository names and tags identified as *\<none>* (not listed above).</span></span> <span data-ttu-id="280eb-235">Tyto nepojmenované image jsou vytvářeny pomocí *souboru Dockerfile* [sestavení s více fázemi](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) .</span><span class="sxs-lookup"><span data-stu-id="280eb-235">These unnamed images are produced by the [multi-stage build](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) *Dockerfile*.</span></span> <span data-ttu-id="280eb-236">Zlepšují efektivitu vytváření finálního obrázku. při změně dojde k jejich opětovnému vytvoření &mdash; pouze nezbytných vrstev.</span><span class="sxs-lookup"><span data-stu-id="280eb-236">They improve the efficiency of building the final image&mdash;only the necessary layers are rebuilt when changes occur.</span></span> <span data-ttu-id="280eb-237">Když už nepotřebujete zprostředkující image, odstraňte je pomocí příkazu [Docker RMI](https://docs.docker.com/engine/reference/commandline/rmi/) .</span><span class="sxs-lookup"><span data-stu-id="280eb-237">When the intermediary images are no longer needed, delete them using the [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) command.</span></span>

<span data-ttu-id="280eb-238">Mohlo by se stát, že se v porovnání s *vývojovou* imagí bude zmenšovat velikost produkčního nebo produkčního obrazu.</span><span class="sxs-lookup"><span data-stu-id="280eb-238">There may be an expectation for the production or release image to be smaller in size by comparison to the *dev* image.</span></span> <span data-ttu-id="280eb-239">Z důvodu mapování svazků ladicí program a aplikace byly spuštěny z místního počítače, nikoli v rámci kontejneru.</span><span class="sxs-lookup"><span data-stu-id="280eb-239">Because of the volume mapping, the debugger and app were running from the local machine and not within the container.</span></span> <span data-ttu-id="280eb-240">*Nejnovější* image zabalí potřebný kód aplikace ke spuštění aplikace na hostitelském počítači.</span><span class="sxs-lookup"><span data-stu-id="280eb-240">The *latest* image has packaged the necessary app code to run the app on a host machine.</span></span> <span data-ttu-id="280eb-241">Proto rozdíl je velikost kódu aplikace.</span><span class="sxs-lookup"><span data-stu-id="280eb-241">Therefore, the delta is the size of the app code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="280eb-242">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="280eb-242">Additional resources</span></span>

* [<span data-ttu-id="280eb-243">Vývoj kontejnerů pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="280eb-243">Container development with Visual Studio</span></span>](/visualstudio/containers)
* [<span data-ttu-id="280eb-244">Azure Service Fabric: Příprava vývojového prostředí</span><span class="sxs-lookup"><span data-stu-id="280eb-244">Azure Service Fabric: Prepare your development environment</span></span>](/azure/service-fabric/service-fabric-get-started)
* [<span data-ttu-id="280eb-245">Nasazení aplikace .NET v kontejneru Windows do Azure Service Fabric</span><span class="sxs-lookup"><span data-stu-id="280eb-245">Deploy a .NET app in a Windows container to Azure Service Fabric</span></span>](/azure/service-fabric/service-fabric-host-app-in-a-container)
* [<span data-ttu-id="280eb-246">Řešení potíží při vývoji v sadě Visual Studio pomocí Dockeru</span><span class="sxs-lookup"><span data-stu-id="280eb-246">Troubleshoot Visual Studio development with Docker</span></span>](/azure/vs-azure-tools-docker-troubleshooting-docker-errors)
* [<span data-ttu-id="280eb-247">Úložiště GitHub pro Visual Studio Container Tools</span><span class="sxs-lookup"><span data-stu-id="280eb-247">Visual Studio Container Tools GitHub repository</span></span>](https://github.com/Microsoft/DockerTools)
* [<span data-ttu-id="280eb-248">GC s použitím Docker a malých kontejnerů</span><span class="sxs-lookup"><span data-stu-id="280eb-248">GC using Docker and small containers</span></span>](xref:performance/memory#sc)

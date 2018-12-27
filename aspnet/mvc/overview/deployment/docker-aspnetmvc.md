---
uid: mvc/overview/deployment/docker
title: Migrace aplikací ASP.NET MVC do kontejnerů s Windows
description: Zjistěte, jak využít stávající aplikace ASP.NET MVC a spustíte ji v kontejneru Windows Docker
keywords: Windows Containers,Docker,ASP.NET MVC
author: BillWagner
ms.author: wiwagn
ms.date: 12/14/2018
ms.assetid: c9f1d52c-b4bd-4b5d-b7f9-8f9ceaf778c4
ms.openlocfilehash: ef184f4256c20e2a66de8fd2d4f8e67f07d9a086
ms.sourcegitcommit: 6548c19f345850ee22b50f7ef9fca732895d9e08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53425156"
---
# <a name="migrating-aspnet-mvc-applications-to-windows-containers"></a><span data-ttu-id="ec675-104">Migrace aplikací ASP.NET MVC do kontejnerů s Windows</span><span class="sxs-lookup"><span data-stu-id="ec675-104">Migrating ASP.NET MVC Applications to Windows Containers</span></span>

<span data-ttu-id="ec675-105">Spuštění existující aplikace založené na platformě .NET v kontejneru Windows nevyžaduje žádné změny do vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="ec675-105">Running an existing .NET Framework-based application in a Windows container doesn't require any changes to your app.</span></span> <span data-ttu-id="ec675-106">Ke spuštění vaší aplikace v kontejneru Windows vytvoření image Dockeru obsahující vaši aplikaci a spuštění kontejneru.</span><span class="sxs-lookup"><span data-stu-id="ec675-106">To run your app in a Windows container you create a Docker image containing your app and start the container.</span></span> <span data-ttu-id="ec675-107">Toto téma vysvětluje, jak využít stávající [aplikace ASP.NET MVC](http://www.asp.net/mvc) a nasaďte ji v kontejneru Windows.</span><span class="sxs-lookup"><span data-stu-id="ec675-107">This topic explains how to take an existing [ASP.NET MVC application](http://www.asp.net/mvc) and deploy it in a Windows container.</span></span>

<span data-ttu-id="ec675-108">Začněte s existující aplikaci ASP.NET MVC a potom sestavit publikované prostředky pomocí sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ec675-108">You start with an existing ASP.NET MVC app, then build the published assets using Visual Studio.</span></span> <span data-ttu-id="ec675-109">Chcete-li vytvořit bitovou kopii, která obsahuje a spouští vaše aplikace pomocí Dockeru.</span><span class="sxs-lookup"><span data-stu-id="ec675-109">You use Docker to create the image that contains and runs your app.</span></span> <span data-ttu-id="ec675-110">Budete přejděte na web spuštěn v kontejneru Windows a ověřte, zda že aplikace funguje.</span><span class="sxs-lookup"><span data-stu-id="ec675-110">You'll browse to the site running in a Windows container and verify the app is working.</span></span>

<span data-ttu-id="ec675-111">Tento článek předpokládá základní znalost Dockeru.</span><span class="sxs-lookup"><span data-stu-id="ec675-111">This article assumes a basic understanding of Docker.</span></span> <span data-ttu-id="ec675-112">Informace o Dockeru najdete [přehled Dockeru](https://docs.docker.com/engine/understanding-docker/).</span><span class="sxs-lookup"><span data-stu-id="ec675-112">You can learn about Docker by reading the [Docker Overview](https://docs.docker.com/engine/understanding-docker/).</span></span>

<span data-ttu-id="ec675-113">Aplikace, které spustíte v kontejneru je jednoduchý web, který získáte odpovědi na otázky náhodně.</span><span class="sxs-lookup"><span data-stu-id="ec675-113">The app you'll run in a container is a simple website that answers questions randomly.</span></span> <span data-ttu-id="ec675-114">Tato aplikace je základní aplikace MVC pomocí funkce bez ověření a databáze úložiště; To vám umožní zaměřit se na přesun webové vrstvy do kontejneru.</span><span class="sxs-lookup"><span data-stu-id="ec675-114">This app is a basic MVC application with no authentication or database storage; it lets you focus on moving the web tier to a container.</span></span> <span data-ttu-id="ec675-115">Další témata vám ukáže, jak přesunout a spravovat trvalé úložiště v kontejnerizovaných aplikací.</span><span class="sxs-lookup"><span data-stu-id="ec675-115">Future topics will show how to move and manage persistent storage in containerized applications.</span></span>

<span data-ttu-id="ec675-116">Aplikace zahrnuje tyto kroky:</span><span class="sxs-lookup"><span data-stu-id="ec675-116">Moving your application involves these steps:</span></span>

1. [<span data-ttu-id="ec675-117">Vytvoření úlohy Publikovat k vytváření prostředků pro bitovou kopii.</span><span class="sxs-lookup"><span data-stu-id="ec675-117">Creating a publish task to build the assets for an image.</span></span>](#publish-script)
1. [<span data-ttu-id="ec675-118">Sestavování image Dockeru, která se spustí aplikace.</span><span class="sxs-lookup"><span data-stu-id="ec675-118">Building a Docker image that will run your application.</span></span>](#build-the-image)
1. [<span data-ttu-id="ec675-119">Spuštění, na kterém běží vaše image kontejneru Dockeru.</span><span class="sxs-lookup"><span data-stu-id="ec675-119">Starting a Docker container that runs your image.</span></span>](#start-a-container)
1. [<span data-ttu-id="ec675-120">Ověřuje se aplikace pomocí prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="ec675-120">Verifying the application using your browser.</span></span>](#verify-in-the-browser)

<span data-ttu-id="ec675-121">[Dokončené aplikace](https://github.com/dotnet/samples/tree/master/framework/docker/MVCRandomAnswerGenerator) je na Githubu.</span><span class="sxs-lookup"><span data-stu-id="ec675-121">The [finished application](https://github.com/dotnet/samples/tree/master/framework/docker/MVCRandomAnswerGenerator) is on GitHub.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ec675-122">Požadavky</span><span class="sxs-lookup"><span data-stu-id="ec675-122">Prerequisites</span></span>

<span data-ttu-id="ec675-123">Vývojovém počítači musíte mít následující software:</span><span class="sxs-lookup"><span data-stu-id="ec675-123">The development machine must have the following software:</span></span>

- <span data-ttu-id="ec675-124">[Windows 10 Anniversary Update](https://www.microsoft.com/software-download/windows10/) (nebo vyšší) nebo [Windows serveru 2016](https://www.microsoft.com/cloud-platform/windows-server) (nebo novější)</span><span class="sxs-lookup"><span data-stu-id="ec675-124">[Windows 10 Anniversary Update](https://www.microsoft.com/software-download/windows10/) (or higher) or [Windows Server 2016](https://www.microsoft.com/cloud-platform/windows-server) (or higher)</span></span>
- <span data-ttu-id="ec675-125">[Docker pro Windows](https://docs.docker.com/docker-for-windows/) -Beta 26 verze stabilní 1.13.0 nebo 1.12 (nebo novější verze)</span><span class="sxs-lookup"><span data-stu-id="ec675-125">[Docker for Windows](https://docs.docker.com/docker-for-windows/) - version Stable 1.13.0 or 1.12 Beta 26 (or newer versions)</span></span>
- [<span data-ttu-id="ec675-126">Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="ec675-126">Visual Studio 2017</span></span>](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)

> [!IMPORTANT]
> <span data-ttu-id="ec675-127">Pokud používáte Windows Server 2016, postupujte podle pokynů pro [nasazení hostitele kontejneru – Windows Server](https://msdn.microsoft.com/virtualization/windowscontainers/deployment/deployment).</span><span class="sxs-lookup"><span data-stu-id="ec675-127">If you are using Windows Server 2016, follow the instructions for [Container Host Deployment - Windows Server](https://msdn.microsoft.com/virtualization/windowscontainers/deployment/deployment).</span></span>

<span data-ttu-id="ec675-128">Po instalaci a spuštění Dockeru klikněte pravým tlačítkem myši na ikonu na hlavním panelu a vyberte **přepnout na kontejnery Windows**.</span><span class="sxs-lookup"><span data-stu-id="ec675-128">After installing and starting Docker, right-click on the tray icon and select **Switch to Windows containers**.</span></span> <span data-ttu-id="ec675-129">To se vyžaduje pro spuštění imagí Dockeru založených na Windows.</span><span class="sxs-lookup"><span data-stu-id="ec675-129">This is required to run Docker images based on Windows.</span></span> <span data-ttu-id="ec675-130">Tento příkaz má spustit během několika sekund:</span><span class="sxs-lookup"><span data-stu-id="ec675-130">This command takes a few seconds to execute:</span></span>

<span data-ttu-id="ec675-131">![Kontejner Windows][windows-container]</span><span class="sxs-lookup"><span data-stu-id="ec675-131">![Windows Container][windows-container]</span></span>

## <a name="publish-script"></a><span data-ttu-id="ec675-132">Publikování skriptu</span><span class="sxs-lookup"><span data-stu-id="ec675-132">Publish script</span></span>

<span data-ttu-id="ec675-133">Shromážděte všechny prostředky, které je potřeba načíst do image Dockeru na jednom místě.</span><span class="sxs-lookup"><span data-stu-id="ec675-133">Collect all the assets that you need to load into a Docker image in one place.</span></span> <span data-ttu-id="ec675-134">Můžete použít Visual Studio **publikovat** příkazu vytvořte profil publikování pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ec675-134">You can use the Visual Studio **Publish** command to create a publish profile for your app.</span></span> <span data-ttu-id="ec675-135">Tento profil zařadí všechny prostředky v jednom stromu adresáře, který je zkopírovat do bitové kopie cílové později v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="ec675-135">This profile will put all the assets in one directory tree that you copy to your target image later in this tutorial.</span></span>

<span data-ttu-id="ec675-136">**Postup publikování**</span><span class="sxs-lookup"><span data-stu-id="ec675-136">**Publish Steps**</span></span>

1. <span data-ttu-id="ec675-137">Klikněte pravým tlačítkem na webový projekt v sadě Visual Studio a vyberte **publikovat**.</span><span class="sxs-lookup"><span data-stu-id="ec675-137">Right click on the web project in Visual Studio, and select **Publish**.</span></span>
1. <span data-ttu-id="ec675-138">Klikněte na tlačítko **tlačítko vlastní profil**a pak vyberte **systému souborů** jako metodu.</span><span class="sxs-lookup"><span data-stu-id="ec675-138">Click the **Custom profile button**, and then select **File System** as the method.</span></span>
1. <span data-ttu-id="ec675-139">Vyberte adresář.</span><span class="sxs-lookup"><span data-stu-id="ec675-139">Choose the directory.</span></span> <span data-ttu-id="ec675-140">Podle konvence stažené Ukázka používá `bin\Release\PublishOutput`.</span><span class="sxs-lookup"><span data-stu-id="ec675-140">By convention, the downloaded sample uses `bin\Release\PublishOutput`.</span></span>

<span data-ttu-id="ec675-141">![Publikování připojení][publish-connection]</span><span class="sxs-lookup"><span data-stu-id="ec675-141">![Publish Connection][publish-connection]</span></span>

<span data-ttu-id="ec675-142">Otevřít **možností publikování souboru** část **nastavení** kartu. Vyberte **Precompile během publikování**.</span><span class="sxs-lookup"><span data-stu-id="ec675-142">Open the **File Publish Options** section of the **Settings** tab. Select **Precompile during publishing**.</span></span> <span data-ttu-id="ec675-143">Tyto optimalizace znamená, že budete kompilaci zobrazení v kontejneru Dockeru, kterou kopírujete předkompilované zobrazení.</span><span class="sxs-lookup"><span data-stu-id="ec675-143">This optimization means that you'll be compiling views in the Docker container, you are copying the precompiled views.</span></span>

<span data-ttu-id="ec675-144">![Nastavení publikování][publish-settings]</span><span class="sxs-lookup"><span data-stu-id="ec675-144">![Publish Settings][publish-settings]</span></span>

<span data-ttu-id="ec675-145">Klikněte na tlačítko **publikovat**, a sady Visual Studio zkopíruje všechny potřebné prostředky do cílové složky.</span><span class="sxs-lookup"><span data-stu-id="ec675-145">Click **Publish**, and Visual Studio will copy all the needed assets to the destination folder.</span></span>

## <a name="build-the-image"></a><span data-ttu-id="ec675-146">Sestavení image</span><span class="sxs-lookup"><span data-stu-id="ec675-146">Build the image</span></span>

<span data-ttu-id="ec675-147">Vytvořte nový soubor s názvem *soubor Dockerfile* k definování image Dockeru.</span><span class="sxs-lookup"><span data-stu-id="ec675-147">Create a new file named *Dockerfile* to define your Docker image.</span></span> <span data-ttu-id="ec675-148">*Soubor Dockerfile* obsahuje pokyny pro sestavení finální image a zahrnuje názvy základní image, požadované součásti, aplikace, kterou chcete spustit a další konfigurace Image.</span><span class="sxs-lookup"><span data-stu-id="ec675-148">*Dockerfile* contains instructions to build the final image and includes any base image names, required components, the app you want to run, and other configuration images.</span></span> <span data-ttu-id="ec675-149">*Soubor Dockerfile* je vstupem `docker build` příkaz, který vytvoří bitovou kopii.</span><span class="sxs-lookup"><span data-stu-id="ec675-149">*Dockerfile* is the input to the `docker build` command that creates the image.</span></span>

<span data-ttu-id="ec675-150">Pro účely tohoto cvičení bude sestavte image založenou na `microsoft/aspnet` image uložená na [Docker Hubu](https://hub.docker.com/r/microsoft/aspnet/).</span><span class="sxs-lookup"><span data-stu-id="ec675-150">For this exercise, you will build an image based on the `microsoft/aspnet` image located on [Docker Hub](https://hub.docker.com/r/microsoft/aspnet/).</span></span>
<span data-ttu-id="ec675-151">Základní image `microsoft/aspnet`, je image Windows serveru.</span><span class="sxs-lookup"><span data-stu-id="ec675-151">The base image, `microsoft/aspnet`, is a Windows Server image.</span></span> <span data-ttu-id="ec675-152">Obsahuje jádro systému Windows Server, služba IIS a ASP.NET 4.7.2.</span><span class="sxs-lookup"><span data-stu-id="ec675-152">It contains Windows Server Core, IIS, and ASP.NET 4.7.2.</span></span> <span data-ttu-id="ec675-153">Při spuštění této image ve vašem kontejneru se automaticky spustí službu IIS a nainstalované weby.</span><span class="sxs-lookup"><span data-stu-id="ec675-153">When you run this image in your container, it will automatically start IIS and installed websites.</span></span>

<span data-ttu-id="ec675-154">Soubor Dockerfile, který vytvoří image vypadá takto:</span><span class="sxs-lookup"><span data-stu-id="ec675-154">The Dockerfile that creates your image looks like this:</span></span>

```console
# The `FROM` instruction specifies the base image. You are
# extending the `microsoft/aspnet` image.

FROM microsoft/aspnet

# The final instruction copies the site you published earlier into the container.
COPY ./bin/Release/PublishOutput/ /inetpub/wwwroot
```

<span data-ttu-id="ec675-155">Neexistuje žádná `ENTRYPOINT` příkaz v tomto souboru Dockerfile.</span><span class="sxs-lookup"><span data-stu-id="ec675-155">There is no `ENTRYPOINT` command in this Dockerfile.</span></span> <span data-ttu-id="ec675-156">Žádný nepotřebujete.</span><span class="sxs-lookup"><span data-stu-id="ec675-156">You don't need one.</span></span> <span data-ttu-id="ec675-157">Při použití Windows serveru se službou IIS, proces služby IIS je vstupní bod, který je nakonfigurován ke spuštění v aspnet základní image.</span><span class="sxs-lookup"><span data-stu-id="ec675-157">When running Windows Server with IIS, the IIS process is the entrypoint, which is configured to start in the aspnet base image.</span></span>

<span data-ttu-id="ec675-158">Spusťte příkaz sestavení Dockeru vytvořte image spouštějící vaši aplikaci ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="ec675-158">Run the Docker build command to create the image that runs your ASP.NET app.</span></span> <span data-ttu-id="ec675-159">Chcete-li to provést, v adresáři projektu otevřete okno Powershellu a zadejte následující příkaz v adresáři řešení:</span><span class="sxs-lookup"><span data-stu-id="ec675-159">To do this, open a PowerShell window in the directory of your project and type the following command in the solution directory:</span></span>

```console
docker build -t mvcrandomanswers .
```

<span data-ttu-id="ec675-160">Tento příkaz sestaví novou image podle pokynů ve vašem souboru Dockerfile pojmenování (-t označování) bitové kopie jako mvcrandomanswers.</span><span class="sxs-lookup"><span data-stu-id="ec675-160">This command will build the new image using the instructions in your Dockerfile, naming (-t tagging) the image as mvcrandomanswers.</span></span> <span data-ttu-id="ec675-161">To může zahrnovat stahování základní image z [Docker Hubu](http://hub.docker.com)a potom aplikaci přidáte do této bitové kopie.</span><span class="sxs-lookup"><span data-stu-id="ec675-161">This may include pulling the base image from [Docker Hub](http://hub.docker.com), and then adding your app to that image.</span></span>

<span data-ttu-id="ec675-162">Po dokončení tohoto příkazu můžete spustit `docker images` příkazu zobrazte informace o nové imagi:</span><span class="sxs-lookup"><span data-stu-id="ec675-162">Once that command completes, you can run the `docker images` command to see information on the new image:</span></span>

```console
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
mvcrandomanswers              latest              86838648aab6        2 minutes ago       10.1 GB
```

<span data-ttu-id="ec675-163">ID bitové kopie se liší na svém počítači.</span><span class="sxs-lookup"><span data-stu-id="ec675-163">The IMAGE ID will be different on your machine.</span></span> <span data-ttu-id="ec675-164">Teď aplikaci spustíme.</span><span class="sxs-lookup"><span data-stu-id="ec675-164">Now, let's run the app.</span></span>

## <a name="start-a-container"></a><span data-ttu-id="ec675-165">Spuštění kontejneru</span><span class="sxs-lookup"><span data-stu-id="ec675-165">Start a container</span></span>

<span data-ttu-id="ec675-166">Spuštění kontejneru spuštěním následujícího `docker run` příkaz:</span><span class="sxs-lookup"><span data-stu-id="ec675-166">Start a container by executing the following `docker run` command:</span></span>

```console
docker run -d --name randomanswers mvcrandomanswers
```

<span data-ttu-id="ec675-167">`-d` Argument říká Dockeru, spusťte na obrázku v režimu odpojení.</span><span class="sxs-lookup"><span data-stu-id="ec675-167">The `-d` argument tells Docker to start the image in detached mode.</span></span> <span data-ttu-id="ec675-168">To znamená, že se že Image Dockeru spustíte odpojené z aktuálního prostředí.</span><span class="sxs-lookup"><span data-stu-id="ec675-168">That means the Docker image runs disconnected from the current shell.</span></span>

<span data-ttu-id="ec675-169">V mnoha příkladech dockeru může se zobrazit -p mapování portů kontejneru a hostitele.</span><span class="sxs-lookup"><span data-stu-id="ec675-169">In many docker examples, you may see -p to map the container and host ports.</span></span> <span data-ttu-id="ec675-170">Výchozí aspnet image kontejneru pro naslouchání na portu 80 a zpřístupnit ji již nakonfigurována.</span><span class="sxs-lookup"><span data-stu-id="ec675-170">The default aspnet image has already configured the container to listen on port 80 and expose it.</span></span>

<span data-ttu-id="ec675-171">`--name randomanswers` Udává název spuštěného kontejneru.</span><span class="sxs-lookup"><span data-stu-id="ec675-171">The `--name randomanswers` gives a name to the running container.</span></span> <span data-ttu-id="ec675-172">Tento název je můžete použít namísto ID kontejneru v většinu příkazů.</span><span class="sxs-lookup"><span data-stu-id="ec675-172">You can use this name instead of the container ID in most commands.</span></span>

<span data-ttu-id="ec675-173">`mvcrandomanswers` Je název bitovou kopii k spouštění.</span><span class="sxs-lookup"><span data-stu-id="ec675-173">The `mvcrandomanswers` is the name of the image to start.</span></span>

## <a name="verify-in-the-browser"></a><span data-ttu-id="ec675-174">Ověřte v prohlížeči</span><span class="sxs-lookup"><span data-stu-id="ec675-174">Verify in the browser</span></span>

<span data-ttu-id="ec675-175">Po spuštění kontejneru, připojte se k spuštěného kontejneru pomocí `http://localhost` , jak ukazuje příklad.</span><span class="sxs-lookup"><span data-stu-id="ec675-175">Once the container starts, connect to the running container using `http://localhost` in the example shown.</span></span> <span data-ttu-id="ec675-176">Zadejte tuto adresu URL do prohlížeče a měli byste vidět web spuštěný.</span><span class="sxs-lookup"><span data-stu-id="ec675-176">Type that URL into your browser, and you should see the running site.</span></span>

> [!NOTE]
> <span data-ttu-id="ec675-177">Některé softwary VPN nebo proxy serveru brání přejdete na web.</span><span class="sxs-lookup"><span data-stu-id="ec675-177">Some VPN or proxy software may prevent you from navigating to your site.</span></span>
> <span data-ttu-id="ec675-178">Je to, abyste měli jistotu, že kontejner fungují dočasně zakázat.</span><span class="sxs-lookup"><span data-stu-id="ec675-178">You can temporarily disable it to make sure your container is working.</span></span>

<span data-ttu-id="ec675-179">Obsahuje adresáře ukázka na Githubu [skript prostředí PowerShell](https://github.com/dotnet/samples/blob/master/framework/docker/MVCRandomAnswerGenerator/run.ps1) , který spouští tyto příkazy za vás.</span><span class="sxs-lookup"><span data-stu-id="ec675-179">The sample directory on GitHub contains a [PowerShell script](https://github.com/dotnet/samples/blob/master/framework/docker/MVCRandomAnswerGenerator/run.ps1) that executes these commands for you.</span></span> <span data-ttu-id="ec675-180">Otevřete okno Powershellu a změňte adresář na adresář řešení a typ:</span><span class="sxs-lookup"><span data-stu-id="ec675-180">Open a PowerShell window, change directory to your solution directory, and type:</span></span>

```console
./run.ps1
```

<span data-ttu-id="ec675-181">Výše uvedeného příkazu sestaví image, se zobrazí seznam imagí na svém počítači a spustí kontejner.</span><span class="sxs-lookup"><span data-stu-id="ec675-181">The command above builds the image, displays the list of images on your machine, and starts a container.</span></span>

<span data-ttu-id="ec675-182">Chcete kontejner zastavit, vydávání `docker stop` příkaz:</span><span class="sxs-lookup"><span data-stu-id="ec675-182">To stop your container, issue a `docker stop` command:</span></span>

```console
docker stop randomanswers
```

<span data-ttu-id="ec675-183">Pokud chcete odebrat kontejner, vydávání `docker rm` příkaz:</span><span class="sxs-lookup"><span data-stu-id="ec675-183">To remove the container, issue a `docker rm` command:</span></span>

```console
docker rm randomanswers
```

[windows-container]: media/aspnetmvc/SwitchContainer.png "Přepnout do kontejnerů Windows"
[publish-connection]: media/aspnetmvc/PublishConnection.png "Publikovat do systému souborů"
[publish-settings]: media/aspnetmvc/PublishSettings.png "Nastavení publikování"

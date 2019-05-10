---
title: Publikování aplikace ASP.NET Core do Azure pomocí Visual Studio Code
author: ricardoserradas
description: Zjistěte, jak publikovat aplikace ASP.NET Core do služby Azure App Service pomocí nástroje Visual Studio Code
ms.author: ricardoserradas
ms.custom: mvc
ms.date: 04/16/2019
uid: tutorials/publish-to-azure-webapp-using-vscode
ms.openlocfilehash: 64d82835f6a47a458802692c99658b964c07f807
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64901977"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio-code"></a><span data-ttu-id="1edbb-103">Publikování aplikace ASP.NET Core do Azure pomocí Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1edbb-103">Publish an ASP.NET Core app to Azure with Visual Studio Code</span></span>

<span data-ttu-id="1edbb-104">Podle [Ricardo Serradas](https://twitter.com/ricardoserradas)</span><span class="sxs-lookup"><span data-stu-id="1edbb-104">By [Ricardo Serradas](https://twitter.com/ricardoserradas)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="1edbb-105">Poradce při potížích chybu nasazení služby App Service, najdete v článku <xref:host-and-deploy/azure-apps/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="1edbb-105">To troubleshoot an App Service deployment issue, see <xref:host-and-deploy/azure-apps/troubleshoot>.</span></span>

## <a name="intro"></a><span data-ttu-id="1edbb-106">Intro</span><span class="sxs-lookup"><span data-stu-id="1edbb-106">Intro</span></span>

<span data-ttu-id="1edbb-107">V tomto kurzu se dozvíte, jak vytvořit aplikaci ASP.Net Core MVC a nasadit v rámci Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="1edbb-107">With this tutorial, you'll learn how to create an ASP.Net Core MVC Application and deploy it within Visual Studio Code.</span></span>

## <a name="set-up"></a><span data-ttu-id="1edbb-108">Nastavení</span><span class="sxs-lookup"><span data-stu-id="1edbb-108">Set up</span></span>

- <span data-ttu-id="1edbb-109">Otevřít [bezplatný účet Azure](https://azure.microsoft.com/free/dotnet/) pokud ho nemáte.</span><span class="sxs-lookup"><span data-stu-id="1edbb-109">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span>
- <span data-ttu-id="1edbb-110">Nainstalujte [.NET Core SDK](https://dotnet.microsoft.com/download)</span><span class="sxs-lookup"><span data-stu-id="1edbb-110">Install [.NET Core SDK](https://dotnet.microsoft.com/download)</span></span>
- <span data-ttu-id="1edbb-111">Install [Visual Studio Code](https://code.visualstudio.com/Download)</span><span class="sxs-lookup"><span data-stu-id="1edbb-111">Install [Visual Studio Code](https://code.visualstudio.com/Download)</span></span>
  - <span data-ttu-id="1edbb-112">Nainstalujte [ C# rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) pro Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1edbb-112">Install the [C# Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) to Visual Studio Code</span></span>
  - <span data-ttu-id="1edbb-113">Instalovat [rozšíření Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) pro Visual Studio Code a nakonfigurujte jej před pokračováním</span><span class="sxs-lookup"><span data-stu-id="1edbb-113">Instal the [Azure App Service Extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) to Visual Studio Code and configure it before proceeding</span></span>

## <a name="create-an-aspnet-core-mvc-project"></a><span data-ttu-id="1edbb-114">Vytvoření projektu aplikace ASP.Net Core MVC</span><span class="sxs-lookup"><span data-stu-id="1edbb-114">Create an ASP.Net Core MVC project</span></span>

<span data-ttu-id="1edbb-115">V terminálu přejděte do složky chcete projekt vytvořit v a použijte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="1edbb-115">Using a terminal, navigate to the folder you want the project to be created on and use the following command:</span></span>

```cmd
> dotnet new mvc
```

<span data-ttu-id="1edbb-116">Budete mít strukturu složek podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="1edbb-116">You'll have a folder structure similar to the following:</span></span>

```cmd
      appsettings.Development.json
      appsettings.json
<DIR> Controllers
<DIR> Models
      netcore-vscode.csproj
<DIR> obj
      Program.cs
<DIR> Properties
      Startup.cs
<DIR> Views
<DIR> wwwroot
```

## <a name="open-it-with-visual-studio-code"></a><span data-ttu-id="1edbb-117">Otevřete jej pomocí Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1edbb-117">Open it with Visual Studio Code</span></span>

<span data-ttu-id="1edbb-118">Po vytvoření projektu, lze jej otevřít pomocí editoru Visual Studio Code pomocí jedné z následujících možností:</span><span class="sxs-lookup"><span data-stu-id="1edbb-118">After your project is created, you can open it with Visual Studio Code by using one of the options below:</span></span>

### <a name="through-the-command-line"></a><span data-ttu-id="1edbb-119">Pomocí příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="1edbb-119">Through the command line</span></span>

<span data-ttu-id="1edbb-120">Použijte tento příkaz ve složce, že kterou jste vytvořili projekt:</span><span class="sxs-lookup"><span data-stu-id="1edbb-120">Use the following command within the folder you created the project:</span></span>

```cmd
> code .
```

<span data-ttu-id="1edbb-121">Následující příkaz nefunguje, zkontrolujte, pokud vaše instalace je správně nakonfigurované pomocí odkazu na [tento odkaz](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform).</span><span class="sxs-lookup"><span data-stu-id="1edbb-121">If the command below does not work, check if your installation is configured properly by referencing [this link](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform).</span></span>

### <a name="through-visual-studio-code-interface"></a><span data-ttu-id="1edbb-122">Prostřednictvím rozhraní Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1edbb-122">Through Visual Studio Code interface</span></span>

- <span data-ttu-id="1edbb-123">Otevřít Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1edbb-123">Open Visual Studio Code</span></span>
- <span data-ttu-id="1edbb-124">V nabídce vyberte `File > Open Folder`</span><span class="sxs-lookup"><span data-stu-id="1edbb-124">On the menu, select `File > Open Folder`</span></span>
- <span data-ttu-id="1edbb-125">Vyberte kořenové složky jste vytvořili projekt MVC</span><span class="sxs-lookup"><span data-stu-id="1edbb-125">Select the root of the folder you created the MVC Project</span></span>

<span data-ttu-id="1edbb-126">Při otevření složky projektu, zobrazí se zpráva, že chybí požadované prostředky pro sestavení a ladění.</span><span class="sxs-lookup"><span data-stu-id="1edbb-126">When you open the project folder, you'll receive a message saying that required assets to build and debug are missing.</span></span> <span data-ttu-id="1edbb-127">Přijměte nápovědy je přidat.</span><span class="sxs-lookup"><span data-stu-id="1edbb-127">Accept the help to add them.</span></span>

![Visual Studio Code rozhraní se načetl se projekt](publish-to-azure-webapp-using-vscode/_static/folder-structure-restore-netcore.jpg)

<span data-ttu-id="1edbb-129">A `.vscode` složka se vytvoří v rámci struktury projektu.</span><span class="sxs-lookup"><span data-stu-id="1edbb-129">A `.vscode` folder will be created under the project structure.</span></span> <span data-ttu-id="1edbb-130">Bude obsahovat následující soubory:</span><span class="sxs-lookup"><span data-stu-id="1edbb-130">It will contain the following files:</span></span>

```cmd
launch.json
tasks.json
```

<span data-ttu-id="1edbb-131">Jsou to soubory nástroje umožňují vytvářet a ladit webové aplikace .NET Core.</span><span class="sxs-lookup"><span data-stu-id="1edbb-131">These are utility files to help you build and debug your .NET Core Web App.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="1edbb-132">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="1edbb-132">Run the app</span></span>

<span data-ttu-id="1edbb-133">Než aplikaci nasadit do Azure, ujistěte se, že je správně spuštěna na místním počítači.</span><span class="sxs-lookup"><span data-stu-id="1edbb-133">Before we deploy the app to Azure, make sure it is running properly on your local machine.</span></span>

- <span data-ttu-id="1edbb-134">Stiskněte klávesu F5 ke spuštění projektu</span><span class="sxs-lookup"><span data-stu-id="1edbb-134">Press F5 to run the project</span></span>

<span data-ttu-id="1edbb-135">Webové aplikace se spustí, běží na nové kartě prohlížeče výchozí.</span><span class="sxs-lookup"><span data-stu-id="1edbb-135">Your web app will start running on a new tab of your default browser.</span></span> <span data-ttu-id="1edbb-136">Můžete si všimnout upozornění o ochraně osobních údajů ihned po jeho spuštění.</span><span class="sxs-lookup"><span data-stu-id="1edbb-136">You may notice a privacy warning as soon as it starts.</span></span> <span data-ttu-id="1edbb-137">Důvodem je, že vaše aplikace bude spouštět buď pomocí protokolu HTTP a HTTPS a přejde na koncový bod HTTPS ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="1edbb-137">This is because your app will start either using HTTP and HTTPS, and it navigates to the HTTPS endpoint by default.</span></span>

![Upozornění o ochraně osobních údajů při ladění aplikace místně](publish-to-azure-webapp-using-vscode/_static/run-webapp-https-warning.jpg)

<span data-ttu-id="1edbb-139">Udržovat relaci ladění, klikněte na tlačítko `Advanced` a potom `Continue to localhost (unsafe)`.</span><span class="sxs-lookup"><span data-stu-id="1edbb-139">To keep the debugging session, click `Advanced` and then `Continue to localhost (unsafe)`.</span></span>

## <a name="generate-the-deployment-package-locally"></a><span data-ttu-id="1edbb-140">Vygenerujte balíček pro nasazení místně</span><span class="sxs-lookup"><span data-stu-id="1edbb-140">Generate the deployment package locally</span></span>

- <span data-ttu-id="1edbb-141">Otevřete terminál aplikace Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1edbb-141">Open Visual Studio Code terminal</span></span>
- <span data-ttu-id="1edbb-142">Pomocí následujícího příkazu pro generování `Release` balíček do dílčí složky s názvem `publish`:</span><span class="sxs-lookup"><span data-stu-id="1edbb-142">Use the following command to generate a `Release` package to a sub folder called `publish`:</span></span>
  - `dotnet publish -c Release -o ./publish`
- <span data-ttu-id="1edbb-143">Nový `publish` složka se vytvoří ve složce struktura projektu</span><span class="sxs-lookup"><span data-stu-id="1edbb-143">A new `publish` folder will be created under the project structure</span></span>

![Struktura složky publikování](publish-to-azure-webapp-using-vscode/_static/publish-folder.jpg)

## <a name="publish-to-azure-app-service"></a><span data-ttu-id="1edbb-145">Publikování do Azure App Service</span><span class="sxs-lookup"><span data-stu-id="1edbb-145">Publish to Azure App Service</span></span>

<span data-ttu-id="1edbb-146">Využití rozšíření Azure App Service pro Visual Studio Code, postupujte podle pokynů pro publikování na webu přímo do služby Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="1edbb-146">Leveraging the Azure App Service extension for Visual Studio Code, follow the steps below to publish the website directly to the Azure App Service.</span></span>

### <a name="if-youre-creating-a-new-web-app"></a><span data-ttu-id="1edbb-147">Pokud vytváříte novou webovou aplikaci</span><span class="sxs-lookup"><span data-stu-id="1edbb-147">If you're creating a new Web App</span></span>

- <span data-ttu-id="1edbb-148">Klikněte pravým tlačítkem myši `publish` a pak zvolte položku `Deploy to Web App...`</span><span class="sxs-lookup"><span data-stu-id="1edbb-148">Right click the `publish` folder and select `Deploy to Web App...`</span></span>
- <span data-ttu-id="1edbb-149">Vyberte předplatné, které chcete vytvořit webovou aplikaci</span><span class="sxs-lookup"><span data-stu-id="1edbb-149">Select the subscription you want to create the Web App</span></span>
- <span data-ttu-id="1edbb-150">Vyberte `Create New Web App`</span><span class="sxs-lookup"><span data-stu-id="1edbb-150">Select `Create New Web App`</span></span>
- <span data-ttu-id="1edbb-151">Zadejte název webové aplikace</span><span class="sxs-lookup"><span data-stu-id="1edbb-151">Enter a name for the Web App</span></span>

<span data-ttu-id="1edbb-152">Rozšíření se vytvoří nová webová aplikace a automaticky spustí nasazení balíčku na ni.</span><span class="sxs-lookup"><span data-stu-id="1edbb-152">The extension will create the new Web App and will automatically start deploying the package to it.</span></span> <span data-ttu-id="1edbb-153">Po dokončení nasazení klikněte na tlačítko `Browse Website` k ověření nasazení.</span><span class="sxs-lookup"><span data-stu-id="1edbb-153">Once the deployment is finished, click `Browse Website` to validate the deployment.</span></span>

![Zpráva nasazení bylo úspěšné.](publish-to-azure-webapp-using-vscode/_static/deployment-succeeded-message.jpg)

<span data-ttu-id="1edbb-155">Po kliknutí na `Browse Website`, budete přejít k němu pomocí výchozí prohlížeč:</span><span class="sxs-lookup"><span data-stu-id="1edbb-155">Once you click `Browse Website`, you'll navigate to it using your default browser:</span></span>

![Nová webová aplikace úspěšně nasadil](publish-to-azure-webapp-using-vscode/_static/new-webapp-deployed.jpg)

### <a name="if-youre-deploying-to-an-existing-web-app"></a><span data-ttu-id="1edbb-157">Pokud nasazení provádíte do existující webové aplikace</span><span class="sxs-lookup"><span data-stu-id="1edbb-157">If you're deploying to an existing Web App</span></span>

- <span data-ttu-id="1edbb-158">Klikněte pravým tlačítkem myši `publish` a pak zvolte položku `Deploy to Web App...`</span><span class="sxs-lookup"><span data-stu-id="1edbb-158">Right click the `publish` folder and select `Deploy to Web App...`</span></span>
- <span data-ttu-id="1edbb-159">Vyberte předplatné, které se nachází existující webové aplikace</span><span class="sxs-lookup"><span data-stu-id="1edbb-159">Select the subscription the existing Web App resides</span></span>
- <span data-ttu-id="1edbb-160">Vyberte webovou aplikaci ze seznamu</span><span class="sxs-lookup"><span data-stu-id="1edbb-160">Select the Web App from the list</span></span>
- <span data-ttu-id="1edbb-161">Visual Studio Code se zeptá, jestli chcete přepsat existující obsah.</span><span class="sxs-lookup"><span data-stu-id="1edbb-161">Visual Studio Code will ask you if you want to overwrite the existing content.</span></span> <span data-ttu-id="1edbb-162">Klikněte na tlačítko `Deploy` k potvrzení</span><span class="sxs-lookup"><span data-stu-id="1edbb-162">Click `Deploy` to confirm</span></span>

<span data-ttu-id="1edbb-163">Rozšíření se nasazení aktualizovaného obsahu do webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="1edbb-163">The extension will deploy the updated content to the Web App.</span></span> <span data-ttu-id="1edbb-164">Po dokončení klikněte na tlačítko `Browse Website` k ověření nasazení.</span><span class="sxs-lookup"><span data-stu-id="1edbb-164">Once it's done, click `Browse Website` to validate the deployment.</span></span>

![Úspěšně nasazeno existující webové aplikace](publish-to-azure-webapp-using-vscode/_static/existing-webapp-deployed.jpg)

## <a name="next-steps"></a><span data-ttu-id="1edbb-166">Další kroky</span><span class="sxs-lookup"><span data-stu-id="1edbb-166">Next steps</span></span>

- [<span data-ttu-id="1edbb-167">Vytvoření prvního kanálu Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="1edbb-167">Create your first Azure DevOps pipeline</span></span>](/azure/devops/pipelines/create-first-pipeline)

## <a name="additional-resources"></a><span data-ttu-id="1edbb-168">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="1edbb-168">Additional resources</span></span>

- [<span data-ttu-id="1edbb-169">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="1edbb-169">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
- [<span data-ttu-id="1edbb-170">Skupiny prostředků Azure</span><span class="sxs-lookup"><span data-stu-id="1edbb-170">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
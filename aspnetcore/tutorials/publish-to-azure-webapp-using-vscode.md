---
title: Publikování aplikace ASP.NET Core do Azure pomocí Visual Studio Code
author: ricardoserradas
description: Naučte se publikovat aplikaci ASP.NET Core pro Azure App Service pomocí Visual Studio Code
ms.author: riserrad
ms.custom: mvc
ms.date: 07/10/2019
uid: tutorials/publish-to-azure-webapp-using-vscode
ms.openlocfilehash: 90ba130f13903cd45eca062c0eca8945eff2e0fa
ms.sourcegitcommit: 7a2c820692f04bfba04398641b70f27fa15391f5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2019
ms.locfileid: "72290639"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio-code"></a><span data-ttu-id="2effa-103">Publikování aplikace ASP.NET Core do Azure pomocí Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2effa-103">Publish an ASP.NET Core app to Azure with Visual Studio Code</span></span>

<span data-ttu-id="2effa-104">Od [Ricardo Serradas](https://twitter.com/ricardoserradas)</span><span class="sxs-lookup"><span data-stu-id="2effa-104">By [Ricardo Serradas](https://twitter.com/ricardoserradas)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="2effa-105">Pokud chcete řešit potíže s nasazením App Service, přečtěte si téma <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="2effa-105">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="intro"></a><span data-ttu-id="2effa-106">Úvodní</span><span class="sxs-lookup"><span data-stu-id="2effa-106">Intro</span></span>

<span data-ttu-id="2effa-107">V tomto kurzu se naučíte vytvořit aplikaci MVC ASP.Net Core a nasadit ji do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="2effa-107">With this tutorial, you'll learn how to create an ASP.Net Core MVC Application and deploy it within Visual Studio Code.</span></span>

## <a name="set-up"></a><span data-ttu-id="2effa-108">Nastavit</span><span class="sxs-lookup"><span data-stu-id="2effa-108">Set up</span></span>

- <span data-ttu-id="2effa-109">Pokud ho nemáte, otevřete [bezplatný účet Azure](https://azure.microsoft.com/free/dotnet/) .</span><span class="sxs-lookup"><span data-stu-id="2effa-109">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span>
- <span data-ttu-id="2effa-110">Nainstalovat [.NET Core SDK](https://dotnet.microsoft.com/download)</span><span class="sxs-lookup"><span data-stu-id="2effa-110">Install [.NET Core SDK](https://dotnet.microsoft.com/download)</span></span>
- <span data-ttu-id="2effa-111">Nainstalovat [Visual Studio Code](https://code.visualstudio.com/Download)</span><span class="sxs-lookup"><span data-stu-id="2effa-111">Install [Visual Studio Code](https://code.visualstudio.com/Download)</span></span>
  - <span data-ttu-id="2effa-112">Nainstalovat [ C# rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) na Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2effa-112">Install the [C# Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) to Visual Studio Code</span></span>
  - <span data-ttu-id="2effa-113">Než budete pokračovat, nainstalujte [Azure App Service rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) , které Visual Studio Code a nakonfigurujte.</span><span class="sxs-lookup"><span data-stu-id="2effa-113">Install the [Azure App Service Extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) to Visual Studio Code and configure it before proceeding</span></span>

## <a name="create-an-aspnet-core-mvc-project"></a><span data-ttu-id="2effa-114">Vytvoření projektu ASP.Net Core MVC</span><span class="sxs-lookup"><span data-stu-id="2effa-114">Create an ASP.Net Core MVC project</span></span>

<span data-ttu-id="2effa-115">Pomocí terminálu přejděte do složky, ve které chcete vytvořit projekt, a použijte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="2effa-115">Using a terminal, navigate to the folder you want the project to be created on and use the following command:</span></span>

```dotnetcli
dotnet new mvc
```

<span data-ttu-id="2effa-116">Máte strukturu složek podobnou následující:</span><span class="sxs-lookup"><span data-stu-id="2effa-116">You'll have a folder structure similar to the following:</span></span>

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

## <a name="open-it-with-visual-studio-code"></a><span data-ttu-id="2effa-117">Otevřete ho pomocí Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2effa-117">Open it with Visual Studio Code</span></span>

<span data-ttu-id="2effa-118">Po vytvoření projektu ho můžete otevřít pomocí Visual Studio Code pomocí jedné z následujících možností:</span><span class="sxs-lookup"><span data-stu-id="2effa-118">After your project is created, you can open it with Visual Studio Code by using one of the options below:</span></span>

### <a name="through-the-command-line"></a><span data-ttu-id="2effa-119">Prostřednictvím příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="2effa-119">Through the command line</span></span>

<span data-ttu-id="2effa-120">Ve složce, kterou jste vytvořili projekt, použijte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="2effa-120">Use the following command within the folder you created the project:</span></span>

```cmd
> code .
```

<span data-ttu-id="2effa-121">Pokud příkaz níže nefunguje, ověřte, jestli je instalace správně nakonfigurovaná odkazem na [Tento odkaz](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform).</span><span class="sxs-lookup"><span data-stu-id="2effa-121">If the command below does not work, check if your installation is configured properly by referencing [this link](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform).</span></span>

### <a name="through-visual-studio-code-interface"></a><span data-ttu-id="2effa-122">Přes Visual Studio Code rozhraní</span><span class="sxs-lookup"><span data-stu-id="2effa-122">Through Visual Studio Code interface</span></span>

- <span data-ttu-id="2effa-123">Otevřít Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2effa-123">Open Visual Studio Code</span></span>
- <span data-ttu-id="2effa-124">V nabídce vyberte `File > Open Folder`.</span><span class="sxs-lookup"><span data-stu-id="2effa-124">On the menu, select `File > Open Folder`</span></span>
- <span data-ttu-id="2effa-125">Vyberte kořen složky, ve které jste vytvořili projekt MVC.</span><span class="sxs-lookup"><span data-stu-id="2effa-125">Select the root of the folder you created the MVC Project</span></span>

<span data-ttu-id="2effa-126">Po otevření složky projektu se zobrazí zpráva oznamující, že chybí požadované prostředky k sestavení a ladění.</span><span class="sxs-lookup"><span data-stu-id="2effa-126">When you open the project folder, you'll receive a message saying that required assets to build and debug are missing.</span></span> <span data-ttu-id="2effa-127">Pokud je chcete přidat, přijměte ji.</span><span class="sxs-lookup"><span data-stu-id="2effa-127">Accept the help to add them.</span></span>

![Rozhraní Visual Studio Code s načteným projektem](publish-to-azure-webapp-using-vscode/_static/folder-structure-restore-netcore.jpg)

<span data-ttu-id="2effa-129">V rámci struktury projektu se vytvoří složka `.vscode`.</span><span class="sxs-lookup"><span data-stu-id="2effa-129">A `.vscode` folder will be created under the project structure.</span></span> <span data-ttu-id="2effa-130">Bude obsahovat následující soubory:</span><span class="sxs-lookup"><span data-stu-id="2effa-130">It will contain the following files:</span></span>

```cmd
launch.json
tasks.json
```

<span data-ttu-id="2effa-131">Jedná se o pomocné soubory, které vám pomůžou sestavovat a ladit webovou aplikaci .NET Core.</span><span class="sxs-lookup"><span data-stu-id="2effa-131">These are utility files to help you build and debug your .NET Core Web App.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="2effa-132">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="2effa-132">Run the app</span></span>

<span data-ttu-id="2effa-133">Než aplikaci nasadíme do Azure, ujistěte se, že je správně spuštěná na místním počítači.</span><span class="sxs-lookup"><span data-stu-id="2effa-133">Before we deploy the app to Azure, make sure it is running properly on your local machine.</span></span>

- <span data-ttu-id="2effa-134">Stisknutím klávesy F5 spusťte projekt.</span><span class="sxs-lookup"><span data-stu-id="2effa-134">Press F5 to run the project</span></span>

<span data-ttu-id="2effa-135">Vaše webová aplikace začne běžet na nové kartě výchozího prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="2effa-135">Your web app will start running on a new tab of your default browser.</span></span> <span data-ttu-id="2effa-136">Můžete si všimnout upozornění na ochranu osobních údajů hned po spuštění.</span><span class="sxs-lookup"><span data-stu-id="2effa-136">You may notice a privacy warning as soon as it starts.</span></span> <span data-ttu-id="2effa-137">Důvodem je to, že vaše aplikace začne používat HTTP a HTTPS a ve výchozím nastavení přejde na koncový bod HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2effa-137">This is because your app will start either using HTTP and HTTPS, and it navigates to the HTTPS endpoint by default.</span></span>

![Upozornění na ochranu osobních údajů při místním ladění aplikace](publish-to-azure-webapp-using-vscode/_static/run-webapp-https-warning.jpg)

<span data-ttu-id="2effa-139">Pokud chcete relaci ladění zachovat, klikněte na `Advanced` a pak `Continue to localhost (unsafe)`.</span><span class="sxs-lookup"><span data-stu-id="2effa-139">To keep the debugging session, click `Advanced` and then `Continue to localhost (unsafe)`.</span></span>

## <a name="generate-the-deployment-package-locally"></a><span data-ttu-id="2effa-140">Místní generování balíčku pro nasazení</span><span class="sxs-lookup"><span data-stu-id="2effa-140">Generate the deployment package locally</span></span>

- <span data-ttu-id="2effa-141">Otevřít Visual Studio Code terminálu</span><span class="sxs-lookup"><span data-stu-id="2effa-141">Open Visual Studio Code terminal</span></span>
- <span data-ttu-id="2effa-142">Pomocí následujícího příkazu vygenerujte balíček `Release` do dílčí složky s názvem `publish`:</span><span class="sxs-lookup"><span data-stu-id="2effa-142">Use the following command to generate a `Release` package to a sub folder called `publish`:</span></span>
  - `dotnet publish -c Release -o ./publish`
- <span data-ttu-id="2effa-143">Pod strukturou projektu se vytvoří nová složka `publish`.</span><span class="sxs-lookup"><span data-stu-id="2effa-143">A new `publish` folder will be created under the project structure</span></span>

![Publikovat strukturu složek](publish-to-azure-webapp-using-vscode/_static/publish-folder.jpg)

## <a name="publish-to-azure-app-service"></a><span data-ttu-id="2effa-145">Publikování do Azure App Service</span><span class="sxs-lookup"><span data-stu-id="2effa-145">Publish to Azure App Service</span></span>

<span data-ttu-id="2effa-146">Využívá se rozšíření Azure App Service pro Visual Studio Code. k publikování webu přímo do Azure App Service použijte následující postup.</span><span class="sxs-lookup"><span data-stu-id="2effa-146">Leveraging the Azure App Service extension for Visual Studio Code, follow the steps below to publish the website directly to the Azure App Service.</span></span>

### <a name="if-youre-creating-a-new-web-app"></a><span data-ttu-id="2effa-147">Pokud vytváříte novou webovou aplikaci</span><span class="sxs-lookup"><span data-stu-id="2effa-147">If you're creating a new Web App</span></span>

- <span data-ttu-id="2effa-148">Klikněte pravým tlačítkem na složku `publish` a vyberte `Deploy to Web App...`.</span><span class="sxs-lookup"><span data-stu-id="2effa-148">Right click the `publish` folder and select `Deploy to Web App...`</span></span>
- <span data-ttu-id="2effa-149">Vyberte předplatné, ve kterém chcete vytvořit webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2effa-149">Select the subscription you want to create the Web App</span></span>
- <span data-ttu-id="2effa-150">Vybrat `Create New Web App`</span><span class="sxs-lookup"><span data-stu-id="2effa-150">Select `Create New Web App`</span></span>
- <span data-ttu-id="2effa-151">Zadejte název webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="2effa-151">Enter a name for the Web App</span></span>

<span data-ttu-id="2effa-152">Rozšíření vytvoří novou webovou aplikaci a automaticky se do ní zahájí nasazení balíčku.</span><span class="sxs-lookup"><span data-stu-id="2effa-152">The extension will create the new Web App and will automatically start deploying the package to it.</span></span> <span data-ttu-id="2effa-153">Po dokončení nasazení ověřte nasazení kliknutím na `Browse Website`.</span><span class="sxs-lookup"><span data-stu-id="2effa-153">Once the deployment is finished, click `Browse Website` to validate the deployment.</span></span>

![Zpráva o úspěšném nasazení](publish-to-azure-webapp-using-vscode/_static/deployment-succeeded-message.jpg)

<span data-ttu-id="2effa-155">Po kliknutí na `Browse Website` přejdete na něj pomocí výchozího prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="2effa-155">Once you click `Browse Website`, you'll navigate to it using your default browser:</span></span>

![Nová webová aplikace se úspěšně nasadila.](publish-to-azure-webapp-using-vscode/_static/new-webapp-deployed.jpg)

### <a name="if-youre-deploying-to-an-existing-web-app"></a><span data-ttu-id="2effa-157">Pokud provádíte nasazení do existující webové aplikace</span><span class="sxs-lookup"><span data-stu-id="2effa-157">If you're deploying to an existing Web App</span></span>

- <span data-ttu-id="2effa-158">Klikněte pravým tlačítkem na složku `publish` a vyberte `Deploy to Web App...`.</span><span class="sxs-lookup"><span data-stu-id="2effa-158">Right click the `publish` folder and select `Deploy to Web App...`</span></span>
- <span data-ttu-id="2effa-159">Výběr předplatného, které se nachází v existující webové aplikaci</span><span class="sxs-lookup"><span data-stu-id="2effa-159">Select the subscription the existing Web App resides</span></span>
- <span data-ttu-id="2effa-160">Vyberte webovou aplikaci ze seznamu.</span><span class="sxs-lookup"><span data-stu-id="2effa-160">Select the Web App from the list</span></span>
- <span data-ttu-id="2effa-161">Visual Studio Code se vás zeptá, jestli chcete přepsat existující obsah.</span><span class="sxs-lookup"><span data-stu-id="2effa-161">Visual Studio Code will ask you if you want to overwrite the existing content.</span></span> <span data-ttu-id="2effa-162">Potvrďte kliknutím na `Deploy`.</span><span class="sxs-lookup"><span data-stu-id="2effa-162">Click `Deploy` to confirm</span></span>

<span data-ttu-id="2effa-163">Rozšíření nasadí aktualizovaný obsah do webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="2effa-163">The extension will deploy the updated content to the Web App.</span></span> <span data-ttu-id="2effa-164">Až to bude hotové, kliknutím na `Browse Website` Ověřte nasazení.</span><span class="sxs-lookup"><span data-stu-id="2effa-164">Once it's done, click `Browse Website` to validate the deployment.</span></span>

![Existující webová aplikace byla úspěšně nasazena.](publish-to-azure-webapp-using-vscode/_static/existing-webapp-deployed.jpg)

## <a name="next-steps"></a><span data-ttu-id="2effa-166">Další kroky</span><span class="sxs-lookup"><span data-stu-id="2effa-166">Next steps</span></span>

- [<span data-ttu-id="2effa-167">Vytvoření prvního kanálu Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="2effa-167">Create your first Azure DevOps pipeline</span></span>](/azure/devops/pipelines/create-first-pipeline)

## <a name="additional-resources"></a><span data-ttu-id="2effa-168">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="2effa-168">Additional resources</span></span>

- [<span data-ttu-id="2effa-169">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="2effa-169">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
- [<span data-ttu-id="2effa-170">Skupiny prostředků Azure</span><span class="sxs-lookup"><span data-stu-id="2effa-170">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
---
title: Publikování aplikace ASP.NET Core do Azure pomocí kódu Visual Studia
author: rick-anderson
description: Zjistěte, jak publikovat aplikaci ASP.NET Core do služby Azure App Service pomocí kódu Visual Studia
ms.author: riserrad
ms.custom: mvc
ms.date: 07/10/2019
uid: tutorials/publish-to-azure-webapp-using-vscode
ms.openlocfilehash: 5f117cb2867a6e7b54269ef39abe819256b429ec
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80242675"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio-code"></a><span data-ttu-id="58e26-103">Publikování aplikace ASP.NET Core do Azure pomocí kódu Visual Studia</span><span class="sxs-lookup"><span data-stu-id="58e26-103">Publish an ASP.NET Core app to Azure with Visual Studio Code</span></span>

<span data-ttu-id="58e26-104">Podle [Ricardo Serradas](https://twitter.com/ricardoserradas)</span><span class="sxs-lookup"><span data-stu-id="58e26-104">By [Ricardo Serradas](https://twitter.com/ricardoserradas)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="58e26-105">Informace o řešení problému s <xref:test/troubleshoot-azure-iis>nasazením služby App Service naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="58e26-105">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="intro"></a><span data-ttu-id="58e26-106">Intro</span><span class="sxs-lookup"><span data-stu-id="58e26-106">Intro</span></span>

<span data-ttu-id="58e26-107">V tomto kurzu se dozvíte, jak vytvořit ASP.Net základní mvc aplikace a nasadit ji v rámci kódu sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="58e26-107">With this tutorial, you'll learn how to create an ASP.Net Core MVC Application and deploy it within Visual Studio Code.</span></span>

## <a name="set-up"></a><span data-ttu-id="58e26-108">Nastavit</span><span class="sxs-lookup"><span data-stu-id="58e26-108">Set up</span></span>

- <span data-ttu-id="58e26-109">Pokud ho nemáte, otevřete si [bezplatný účet Azure.](https://azure.microsoft.com/free/dotnet/)</span><span class="sxs-lookup"><span data-stu-id="58e26-109">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span>
- <span data-ttu-id="58e26-110">Instalace [sady .NET Core SDK](https://dotnet.microsoft.com/download)</span><span class="sxs-lookup"><span data-stu-id="58e26-110">Install [.NET Core SDK](https://dotnet.microsoft.com/download)</span></span>
- <span data-ttu-id="58e26-111">Instalace [kódu sady Visual Studio](https://code.visualstudio.com/Download)</span><span class="sxs-lookup"><span data-stu-id="58e26-111">Install [Visual Studio Code](https://code.visualstudio.com/Download)</span></span>
  - <span data-ttu-id="58e26-112">Instalace [rozšíření C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) do kódu sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58e26-112">Install the [C# Extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) to Visual Studio Code</span></span>
  - <span data-ttu-id="58e26-113">Instalace [rozšíření služby Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) do kódu Sady Visual Studio a konfigurace před pokračováním</span><span class="sxs-lookup"><span data-stu-id="58e26-113">Install the [Azure App Service Extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) to Visual Studio Code and configure it before proceeding</span></span>

## <a name="create-an-aspnet-core-mvc-project"></a><span data-ttu-id="58e26-114">Vytvoření ASP.Net projektu Core MVC</span><span class="sxs-lookup"><span data-stu-id="58e26-114">Create an ASP.Net Core MVC project</span></span>

<span data-ttu-id="58e26-115">Pomocí terminálu přejděte do složky, ve které má být projekt vytvořen, a použijte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="58e26-115">Using a terminal, navigate to the folder you want the project to be created on and use the following command:</span></span>

```dotnetcli
dotnet new mvc
```

<span data-ttu-id="58e26-116">Budete mít strukturu složek podobnou následující:</span><span class="sxs-lookup"><span data-stu-id="58e26-116">You'll have a folder structure similar to the following:</span></span>

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

## <a name="open-it-with-visual-studio-code"></a><span data-ttu-id="58e26-117">Otevření pomocí kódu sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58e26-117">Open it with Visual Studio Code</span></span>

<span data-ttu-id="58e26-118">Po vytvoření projektu jej můžete otevřít pomocí kódu sady Visual Studio pomocí jedné z následujících možností:</span><span class="sxs-lookup"><span data-stu-id="58e26-118">After your project is created, you can open it with Visual Studio Code by using one of the options below:</span></span>

### <a name="through-the-command-line"></a><span data-ttu-id="58e26-119">Prostřednictvím příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="58e26-119">Through the command line</span></span>

<span data-ttu-id="58e26-120">Ve složce, kterou jste projekt vytvořili, použijte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="58e26-120">Use the following command within the folder you created the project:</span></span>

```cmd
> code .
```

<span data-ttu-id="58e26-121">Pokud níže uvedený příkaz nefunguje, zkontrolujte, zda je instalace správně nakonfigurována odkazem na [tento odkaz](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform).</span><span class="sxs-lookup"><span data-stu-id="58e26-121">If the command below does not work, check if your installation is configured properly by referencing [this link](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform).</span></span>

### <a name="through-visual-studio-code-interface"></a><span data-ttu-id="58e26-122">Prostřednictvím rozhraní kódu sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58e26-122">Through Visual Studio Code interface</span></span>

- <span data-ttu-id="58e26-123">Otevřít kód visual studia</span><span class="sxs-lookup"><span data-stu-id="58e26-123">Open Visual Studio Code</span></span>
- <span data-ttu-id="58e26-124">V nabídce vyberte`File > Open Folder`</span><span class="sxs-lookup"><span data-stu-id="58e26-124">On the menu, select `File > Open Folder`</span></span>
- <span data-ttu-id="58e26-125">Vyberte kořenovou složku, kterou jste vytvořili v projektu MVC.</span><span class="sxs-lookup"><span data-stu-id="58e26-125">Select the root of the folder you created the MVC Project</span></span>

<span data-ttu-id="58e26-126">Při otevření složky projektu se zobrazí zpráva, že chybí požadované prostředky k sestavení a ladění.</span><span class="sxs-lookup"><span data-stu-id="58e26-126">When you open the project folder, you'll receive a message saying that required assets to build and debug are missing.</span></span> <span data-ttu-id="58e26-127">Přijměte nápovědu k jejich přidání.</span><span class="sxs-lookup"><span data-stu-id="58e26-127">Accept the help to add them.</span></span>

![Rozhraní kódu visual studia s načteným projektem](publish-to-azure-webapp-using-vscode/_static/folder-structure-restore-netcore.jpg)

<span data-ttu-id="58e26-129">Složka `.vscode` bude vytvořena pod strukturou projektu.</span><span class="sxs-lookup"><span data-stu-id="58e26-129">A `.vscode` folder will be created under the project structure.</span></span> <span data-ttu-id="58e26-130">Bude obsahovat následující soubory:</span><span class="sxs-lookup"><span data-stu-id="58e26-130">It will contain the following files:</span></span>

```cmd
launch.json
tasks.json
```

<span data-ttu-id="58e26-131">Jedná se o soubory nástrojů, které vám pomohou vytvořit a ladit webovou aplikaci .NET Core.</span><span class="sxs-lookup"><span data-stu-id="58e26-131">These are utility files to help you build and debug your .NET Core Web App.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="58e26-132">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="58e26-132">Run the app</span></span>

<span data-ttu-id="58e26-133">Než aplikaci nasadíme do Azure, ujistěte se, že v místním počítači funguje správně.</span><span class="sxs-lookup"><span data-stu-id="58e26-133">Before we deploy the app to Azure, make sure it is running properly on your local machine.</span></span>

- <span data-ttu-id="58e26-134">Stisknutím klávesy F5 spusťte projekt.</span><span class="sxs-lookup"><span data-stu-id="58e26-134">Press F5 to run the project</span></span>

<span data-ttu-id="58e26-135">Webová aplikace se spustí na nové kartě výchozího prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="58e26-135">Your web app will start running on a new tab of your default browser.</span></span> <span data-ttu-id="58e26-136">Jakmile začne, můžete si všimnout upozornění na ochranu osobních údajů.</span><span class="sxs-lookup"><span data-stu-id="58e26-136">You may notice a privacy warning as soon as it starts.</span></span> <span data-ttu-id="58e26-137">Důvodem je, že vaše aplikace začne buď pomocí PROTOKOLU HTTP a HTTPS a ve výchozím nastavení přejde do koncového bodu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="58e26-137">This is because your app will start either using HTTP and HTTPS, and it navigates to the HTTPS endpoint by default.</span></span>

![Upozornění na ochranu osobních údajů při místním ladění aplikace](publish-to-azure-webapp-using-vscode/_static/run-webapp-https-warning.jpg)

<span data-ttu-id="58e26-139">Chcete-li zachovat relaci ladění, `Advanced` klepněte na tlačítko a potom `Continue to localhost (unsafe)`.</span><span class="sxs-lookup"><span data-stu-id="58e26-139">To keep the debugging session, click `Advanced` and then `Continue to localhost (unsafe)`.</span></span>

## <a name="generate-the-deployment-package-locally"></a><span data-ttu-id="58e26-140">Vygenerovat balíček nasazení místně</span><span class="sxs-lookup"><span data-stu-id="58e26-140">Generate the deployment package locally</span></span>

- <span data-ttu-id="58e26-141">Otevřít terminál kódu Visual Studia</span><span class="sxs-lookup"><span data-stu-id="58e26-141">Open Visual Studio Code terminal</span></span>
- <span data-ttu-id="58e26-142">Pomocí následujícího příkazu `Release` vygenerujte `publish`balíček do podsložky s názvem :</span><span class="sxs-lookup"><span data-stu-id="58e26-142">Use the following command to generate a `Release` package to a sub folder called `publish`:</span></span>
  - `dotnet publish -c Release -o ./publish`
- <span data-ttu-id="58e26-143">V `publish` rámci struktury projektu bude vytvořena nová složka.</span><span class="sxs-lookup"><span data-stu-id="58e26-143">A new `publish` folder will be created under the project structure</span></span>

![Publikovat strukturu složek](publish-to-azure-webapp-using-vscode/_static/publish-folder.jpg)

## <a name="publish-to-azure-app-service"></a><span data-ttu-id="58e26-145">Publikování do Azure App Service</span><span class="sxs-lookup"><span data-stu-id="58e26-145">Publish to Azure App Service</span></span>

<span data-ttu-id="58e26-146">S využitím rozšíření služby Azure App Service pro kód Visual Studia publikujte web přímo do služby Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="58e26-146">Leveraging the Azure App Service extension for Visual Studio Code, follow the steps below to publish the website directly to the Azure App Service.</span></span>

### <a name="if-youre-creating-a-new-web-app"></a><span data-ttu-id="58e26-147">Pokud vytváříte novou webovou aplikaci</span><span class="sxs-lookup"><span data-stu-id="58e26-147">If you're creating a new Web App</span></span>

- <span data-ttu-id="58e26-148">Klikněte `publish` pravým tlačítkem myši na složku a vyberte`Deploy to Web App...`</span><span class="sxs-lookup"><span data-stu-id="58e26-148">Right click the `publish` folder and select `Deploy to Web App...`</span></span>
- <span data-ttu-id="58e26-149">Vyberte předplatné, které chcete vytvořit webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="58e26-149">Select the subscription you want to create the Web App</span></span>
- <span data-ttu-id="58e26-150">Vyberte `Create New Web App`</span><span class="sxs-lookup"><span data-stu-id="58e26-150">Select `Create New Web App`</span></span>
- <span data-ttu-id="58e26-151">Zadejte název webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="58e26-151">Enter a name for the Web App</span></span>

<span data-ttu-id="58e26-152">Rozšíření vytvoří novou webovou aplikaci a automaticky do ní začne nasazovat balíček.</span><span class="sxs-lookup"><span data-stu-id="58e26-152">The extension will create the new Web App and will automatically start deploying the package to it.</span></span> <span data-ttu-id="58e26-153">Po dokončení nasazení klepnutím `Browse Website` ověřte nasazení.</span><span class="sxs-lookup"><span data-stu-id="58e26-153">Once the deployment is finished, click `Browse Website` to validate the deployment.</span></span>

![Zpráva o nasazení proběhla úspěšně.](publish-to-azure-webapp-using-vscode/_static/deployment-succeeded-message.jpg)

<span data-ttu-id="58e26-155">Jakmile kliknete `Browse Website`, přejdete na něj pomocí výchozího prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="58e26-155">Once you click `Browse Website`, you'll navigate to it using your default browser:</span></span>

![Nová webová aplikace byla úspěšně nasazena.](publish-to-azure-webapp-using-vscode/_static/new-webapp-deployed.jpg)

### <a name="if-youre-deploying-to-an-existing-web-app"></a><span data-ttu-id="58e26-157">Pokud nasazujete do existující webové aplikace</span><span class="sxs-lookup"><span data-stu-id="58e26-157">If you're deploying to an existing Web App</span></span>

- <span data-ttu-id="58e26-158">Klikněte `publish` pravým tlačítkem myši na složku a vyberte`Deploy to Web App...`</span><span class="sxs-lookup"><span data-stu-id="58e26-158">Right click the `publish` folder and select `Deploy to Web App...`</span></span>
- <span data-ttu-id="58e26-159">Vyberte předplatné, které se nachází v existující webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="58e26-159">Select the subscription the existing Web App resides</span></span>
- <span data-ttu-id="58e26-160">Výběr webové aplikace ze seznamu</span><span class="sxs-lookup"><span data-stu-id="58e26-160">Select the Web App from the list</span></span>
- <span data-ttu-id="58e26-161">Visual Studio Code se vás zeptá, jestli chcete přepsat existující obsah.</span><span class="sxs-lookup"><span data-stu-id="58e26-161">Visual Studio Code will ask you if you want to overwrite the existing content.</span></span> <span data-ttu-id="58e26-162">Klikněte `Deploy` pro potvrzení</span><span class="sxs-lookup"><span data-stu-id="58e26-162">Click `Deploy` to confirm</span></span>

<span data-ttu-id="58e26-163">Rozšíření nasadí aktualizovaný obsah do webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="58e26-163">The extension will deploy the updated content to the Web App.</span></span> <span data-ttu-id="58e26-164">Po dokončení kliknutím `Browse Website` ověřte nasazení.</span><span class="sxs-lookup"><span data-stu-id="58e26-164">Once it's done, click `Browse Website` to validate the deployment.</span></span>

![Existující Web App úspěšně nasazen](publish-to-azure-webapp-using-vscode/_static/existing-webapp-deployed.jpg)

## <a name="next-steps"></a><span data-ttu-id="58e26-166">Další kroky</span><span class="sxs-lookup"><span data-stu-id="58e26-166">Next steps</span></span>

- [<span data-ttu-id="58e26-167">Vytvoření prvního kanálu Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="58e26-167">Create your first Azure DevOps pipeline</span></span>](/azure/devops/pipelines/create-first-pipeline)

## <a name="additional-resources"></a><span data-ttu-id="58e26-168">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="58e26-168">Additional resources</span></span>

- [<span data-ttu-id="58e26-169">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="58e26-169">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
- [<span data-ttu-id="58e26-170">Skupiny prostředků Azure</span><span class="sxs-lookup"><span data-stu-id="58e26-170">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)

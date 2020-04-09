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
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio-code"></a>Publikování aplikace ASP.NET Core do Azure pomocí kódu Visual Studia

Podle [Ricardo Serradas](https://twitter.com/ricardoserradas)

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

Informace o řešení problému s <xref:test/troubleshoot-azure-iis>nasazením služby App Service naleznete v tématu .

## <a name="intro"></a>Intro

V tomto kurzu se dozvíte, jak vytvořit ASP.Net základní mvc aplikace a nasadit ji v rámci kódu sady Visual Studio.

## <a name="set-up"></a>Nastavit

- Pokud ho nemáte, otevřete si [bezplatný účet Azure.](https://azure.microsoft.com/free/dotnet/)
- Instalace [sady .NET Core SDK](https://dotnet.microsoft.com/download)
- Instalace [kódu sady Visual Studio](https://code.visualstudio.com/Download)
  - Instalace [rozšíření C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) do kódu sady Visual Studio
  - Instalace [rozšíření služby Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) do kódu Sady Visual Studio a konfigurace před pokračováním

## <a name="create-an-aspnet-core-mvc-project"></a>Vytvoření ASP.Net projektu Core MVC

Pomocí terminálu přejděte do složky, ve které má být projekt vytvořen, a použijte následující příkaz:

```dotnetcli
dotnet new mvc
```

Budete mít strukturu složek podobnou následující:

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

## <a name="open-it-with-visual-studio-code"></a>Otevření pomocí kódu sady Visual Studio

Po vytvoření projektu jej můžete otevřít pomocí kódu sady Visual Studio pomocí jedné z následujících možností:

### <a name="through-the-command-line"></a>Prostřednictvím příkazového řádku

Ve složce, kterou jste projekt vytvořili, použijte následující příkaz:

```cmd
> code .
```

Pokud níže uvedený příkaz nefunguje, zkontrolujte, zda je instalace správně nakonfigurována odkazem na [tento odkaz](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform).

### <a name="through-visual-studio-code-interface"></a>Prostřednictvím rozhraní kódu sady Visual Studio

- Otevřít kód visual studia
- V nabídce vyberte`File > Open Folder`
- Vyberte kořenovou složku, kterou jste vytvořili v projektu MVC.

Při otevření složky projektu se zobrazí zpráva, že chybí požadované prostředky k sestavení a ladění. Přijměte nápovědu k jejich přidání.

![Rozhraní kódu visual studia s načteným projektem](publish-to-azure-webapp-using-vscode/_static/folder-structure-restore-netcore.jpg)

Složka `.vscode` bude vytvořena pod strukturou projektu. Bude obsahovat následující soubory:

```cmd
launch.json
tasks.json
```

Jedná se o soubory nástrojů, které vám pomohou vytvořit a ladit webovou aplikaci .NET Core.

## <a name="run-the-app"></a>Spuštění aplikace

Než aplikaci nasadíme do Azure, ujistěte se, že v místním počítači funguje správně.

- Stisknutím klávesy F5 spusťte projekt.

Webová aplikace se spustí na nové kartě výchozího prohlížeče. Jakmile začne, můžete si všimnout upozornění na ochranu osobních údajů. Důvodem je, že vaše aplikace začne buď pomocí PROTOKOLU HTTP a HTTPS a ve výchozím nastavení přejde do koncového bodu HTTPS.

![Upozornění na ochranu osobních údajů při místním ladění aplikace](publish-to-azure-webapp-using-vscode/_static/run-webapp-https-warning.jpg)

Chcete-li zachovat relaci ladění, `Advanced` klepněte na tlačítko a potom `Continue to localhost (unsafe)`.

## <a name="generate-the-deployment-package-locally"></a>Vygenerovat balíček nasazení místně

- Otevřít terminál kódu Visual Studia
- Pomocí následujícího příkazu `Release` vygenerujte `publish`balíček do podsložky s názvem :
  - `dotnet publish -c Release -o ./publish`
- V `publish` rámci struktury projektu bude vytvořena nová složka.

![Publikovat strukturu složek](publish-to-azure-webapp-using-vscode/_static/publish-folder.jpg)

## <a name="publish-to-azure-app-service"></a>Publikování do Azure App Service

S využitím rozšíření služby Azure App Service pro kód Visual Studia publikujte web přímo do služby Azure App Service.

### <a name="if-youre-creating-a-new-web-app"></a>Pokud vytváříte novou webovou aplikaci

- Klikněte `publish` pravým tlačítkem myši na složku a vyberte`Deploy to Web App...`
- Vyberte předplatné, které chcete vytvořit webovou aplikaci.
- Vyberte `Create New Web App`
- Zadejte název webové aplikace.

Rozšíření vytvoří novou webovou aplikaci a automaticky do ní začne nasazovat balíček. Po dokončení nasazení klepnutím `Browse Website` ověřte nasazení.

![Zpráva o nasazení proběhla úspěšně.](publish-to-azure-webapp-using-vscode/_static/deployment-succeeded-message.jpg)

Jakmile kliknete `Browse Website`, přejdete na něj pomocí výchozího prohlížeče:

![Nová webová aplikace byla úspěšně nasazena.](publish-to-azure-webapp-using-vscode/_static/new-webapp-deployed.jpg)

### <a name="if-youre-deploying-to-an-existing-web-app"></a>Pokud nasazujete do existující webové aplikace

- Klikněte `publish` pravým tlačítkem myši na složku a vyberte`Deploy to Web App...`
- Vyberte předplatné, které se nachází v existující webové aplikaci.
- Výběr webové aplikace ze seznamu
- Visual Studio Code se vás zeptá, jestli chcete přepsat existující obsah. Klikněte `Deploy` pro potvrzení

Rozšíření nasadí aktualizovaný obsah do webové aplikace. Po dokončení kliknutím `Browse Website` ověřte nasazení.

![Existující Web App úspěšně nasazen](publish-to-azure-webapp-using-vscode/_static/existing-webapp-deployed.jpg)

## <a name="next-steps"></a>Další kroky

- [Vytvoření prvního kanálu Azure DevOps](/azure/devops/pipelines/create-first-pipeline)

## <a name="additional-resources"></a>Další zdroje

- [Azure App Service](/azure/app-service/app-service-web-overview)
- [Skupiny prostředků Azure](/azure/azure-resource-manager/resource-group-overview#resource-groups)

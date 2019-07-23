---
title: Publikování aplikace ASP.NET Core do Azure pomocí Visual Studio Code
author: ricardoserradas
description: Naučte se publikovat aplikaci ASP.NET Core pro Azure App Service pomocí Visual Studio Code
ms.author: riserrad
ms.custom: mvc
ms.date: 07/10/2019
uid: tutorials/publish-to-azure-webapp-using-vscode
ms.openlocfilehash: 97e8fcb1e5470245c80fad0875abb5fdace7853c
ms.sourcegitcommit: b40613c603d6f0cc71f3232c16df61550907f550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68308321"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio-code"></a>Publikování aplikace ASP.NET Core do Azure pomocí Visual Studio Code

Od [Ricardo Serradas](https://twitter.com/ricardoserradas)

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

Poradce při potížích chybu nasazení služby App Service, najdete v článku <xref:test/troubleshoot-azure-iis>.

## <a name="intro"></a>Úvodní

V tomto kurzu se naučíte vytvořit aplikaci MVC ASP.Net Core a nasadit ji do Visual Studio Code.

## <a name="set-up"></a>Nastavení

- Otevřít [bezplatný účet Azure](https://azure.microsoft.com/free/dotnet/) pokud ho nemáte.
- Nainstalovat [.NET Core SDK](https://dotnet.microsoft.com/download)
- Nainstalovat [Visual Studio Code](https://code.visualstudio.com/Download)
  - Nainstalovat [ C# rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) na Visual Studio Code
  - Než budete pokračovat, naVisual Studio Code [rozšíření Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) a nakonfigurujte ho.

## <a name="create-an-aspnet-core-mvc-project"></a>Vytvoření projektu ASP.Net Core MVC

Pomocí terminálu přejděte do složky, ve které chcete vytvořit projekt, a použijte následující příkaz:

```cmd
> dotnet new mvc
```

Máte strukturu složek podobnou následující:

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

## <a name="open-it-with-visual-studio-code"></a>Otevřete ho pomocí Visual Studio Code

Po vytvoření projektu ho můžete otevřít pomocí Visual Studio Code pomocí jedné z následujících možností:

### <a name="through-the-command-line"></a>Prostřednictvím příkazového řádku

Ve složce, kterou jste vytvořili projekt, použijte následující příkaz:

```cmd
> code .
```

Pokud příkaz níže nefunguje, ověřte, jestli je instalace správně nakonfigurovaná odkazem na [Tento odkaz](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform).

### <a name="through-visual-studio-code-interface"></a>Přes Visual Studio Code rozhraní

- Otevřít Visual Studio Code
- V nabídce vyberte`File > Open Folder`
- Vyberte kořen složky, ve které jste vytvořili projekt MVC.

Po otevření složky projektu se zobrazí zpráva oznamující, že chybí požadované prostředky k sestavení a ladění. Pokud je chcete přidat, přijměte ji.

![Rozhraní Visual Studio Code s načteným projektem](publish-to-azure-webapp-using-vscode/_static/folder-structure-restore-netcore.jpg)

`.vscode` Složka se vytvoří pod strukturou projektu. Bude obsahovat následující soubory:

```cmd
launch.json
tasks.json
```

Jedná se o pomocné soubory, které vám pomůžou sestavovat a ladit webovou aplikaci .NET Core.

## <a name="run-the-app"></a>Spuštění aplikace

Než aplikaci nasadíme do Azure, ujistěte se, že je správně spuštěná na místním počítači.

- Stisknutím klávesy F5 spusťte projekt.

Vaše webová aplikace začne běžet na nové kartě výchozího prohlížeče. Můžete si všimnout upozornění na ochranu osobních údajů hned po spuštění. Důvodem je to, že vaše aplikace začne používat HTTP a HTTPS a ve výchozím nastavení přejde na koncový bod HTTPS.

![Upozornění na ochranu osobních údajů při místním ladění aplikace](publish-to-azure-webapp-using-vscode/_static/run-webapp-https-warning.jpg)

Pokud chcete relaci ladění zachovat, klikněte `Advanced` na a `Continue to localhost (unsafe)`pak.

## <a name="generate-the-deployment-package-locally"></a>Místní generování balíčku pro nasazení

- Otevřít Visual Studio Code terminálu
- Pomocí následujícího příkazu vygenerujte `Release` balíček do dílčí složky s názvem: `publish`
  - `dotnet publish -c Release -o ./publish`
- V rámci `publish` struktury projektu se vytvoří nová složka.

![Publikovat strukturu složek](publish-to-azure-webapp-using-vscode/_static/publish-folder.jpg)

## <a name="publish-to-azure-app-service"></a>Publikování do Azure App Service

Využívá se rozšíření Azure App Service pro Visual Studio Code. k publikování webu přímo do Azure App Service použijte následující postup.

### <a name="if-youre-creating-a-new-web-app"></a>Pokud vytváříte novou webovou aplikaci

- Klikněte pravým `publish` tlačítkem na složku a vyberte`Deploy to Web App...`
- Vyberte předplatné, ve kterém chcete vytvořit webovou aplikaci.
- Vybrali`Create New Web App`
- Zadejte název webové aplikace.

Rozšíření vytvoří novou webovou aplikaci a automaticky se do ní zahájí nasazení balíčku. Po dokončení nasazení kliknutím `Browse Website` ověřte nasazení.

![Zpráva o úspěšném nasazení](publish-to-azure-webapp-using-vscode/_static/deployment-succeeded-message.jpg)

Po kliknutí `Browse Website`na něj přejdete pomocí výchozího prohlížeče:

![Nová webová aplikace se úspěšně nasadila.](publish-to-azure-webapp-using-vscode/_static/new-webapp-deployed.jpg)

### <a name="if-youre-deploying-to-an-existing-web-app"></a>Pokud provádíte nasazení do existující webové aplikace

- Klikněte pravým `publish` tlačítkem na složku a vyberte`Deploy to Web App...`
- Výběr předplatného, které se nachází v existující webové aplikaci
- Vyberte webovou aplikaci ze seznamu.
- Visual Studio Code se vás zeptá, jestli chcete přepsat existující obsah. Kliknutím `Deploy` potvrďte

Rozšíření nasadí aktualizovaný obsah do webové aplikace. Až to bude hotové, `Browse Website` kliknutím ověřte nasazení.

![Existující webová aplikace byla úspěšně nasazena.](publish-to-azure-webapp-using-vscode/_static/existing-webapp-deployed.jpg)

## <a name="next-steps"></a>Další kroky

- [Vytvoření prvního kanálu Azure DevOps](/azure/devops/pipelines/create-first-pipeline)

## <a name="additional-resources"></a>Další zdroje

- [Azure App Service](/azure/app-service/app-service-web-overview)
- [Skupiny prostředků Azure](/azure/azure-resource-manager/resource-group-overview#resource-groups)
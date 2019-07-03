---
title: Publikování aplikace ASP.NET Core do Azure pomocí Visual Studio Code
author: ricardoserradas
description: Zjistěte, jak publikovat aplikace ASP.NET Core do služby Azure App Service pomocí nástroje Visual Studio Code
ms.author: riserrad
ms.custom: mvc
ms.date: 04/16/2019
uid: tutorials/publish-to-azure-webapp-using-vscode
ms.openlocfilehash: 2eaae62af97927fbe22e7f5d4fadfc2265c5a5cd
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538740"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio-code"></a>Publikování aplikace ASP.NET Core do Azure pomocí Visual Studio Code

Podle [Ricardo Serradas](https://twitter.com/ricardoserradas)

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

Poradce při potížích chybu nasazení služby App Service, najdete v článku <xref:host-and-deploy/azure-apps/troubleshoot>.

## <a name="intro"></a>Intro

V tomto kurzu se dozvíte, jak vytvořit aplikaci ASP.Net Core MVC a nasadit v rámci Visual Studio Code.

## <a name="set-up"></a>Nastavení

- Otevřít [bezplatný účet Azure](https://azure.microsoft.com/free/dotnet/) pokud ho nemáte.
- Nainstalujte [.NET Core SDK](https://dotnet.microsoft.com/download)
- Install [Visual Studio Code](https://code.visualstudio.com/Download)
  - Nainstalujte [ C# rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) pro Visual Studio Code
  - Instalovat [rozšíření Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) pro Visual Studio Code a nakonfigurujte jej před pokračováním

## <a name="create-an-aspnet-core-mvc-project"></a>Vytvoření projektu aplikace ASP.Net Core MVC

V terminálu přejděte do složky chcete projekt vytvořit v a použijte následující příkaz:

```cmd
> dotnet new mvc
```

Budete mít strukturu složek podobný následujícímu:

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

## <a name="open-it-with-visual-studio-code"></a>Otevřete jej pomocí Visual Studio Code

Po vytvoření projektu, lze jej otevřít pomocí editoru Visual Studio Code pomocí jedné z následujících možností:

### <a name="through-the-command-line"></a>Pomocí příkazového řádku

Použijte tento příkaz ve složce, že kterou jste vytvořili projekt:

```cmd
> code .
```

Následující příkaz nefunguje, zkontrolujte, pokud vaše instalace je správně nakonfigurované pomocí odkazu na [tento odkaz](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform).

### <a name="through-visual-studio-code-interface"></a>Prostřednictvím rozhraní Visual Studio Code

- Otevřít Visual Studio Code
- V nabídce vyberte `File > Open Folder`
- Vyberte kořenové složky jste vytvořili projekt MVC

Při otevření složky projektu, zobrazí se zpráva, že chybí požadované prostředky pro sestavení a ladění. Přijměte nápovědy je přidat.

![Visual Studio Code rozhraní se načetl se projekt](publish-to-azure-webapp-using-vscode/_static/folder-structure-restore-netcore.jpg)

A `.vscode` složka se vytvoří v rámci struktury projektu. Bude obsahovat následující soubory:

```cmd
launch.json
tasks.json
```

Jsou to soubory nástroje umožňují vytvářet a ladit webové aplikace .NET Core.

## <a name="run-the-app"></a>Spuštění aplikace

Než aplikaci nasadit do Azure, ujistěte se, že je správně spuštěna na místním počítači.

- Stiskněte klávesu F5 ke spuštění projektu

Webové aplikace se spustí, běží na nové kartě prohlížeče výchozí. Můžete si všimnout upozornění o ochraně osobních údajů ihned po jeho spuštění. Důvodem je, že vaše aplikace bude spouštět buď pomocí protokolu HTTP a HTTPS a přejde na koncový bod HTTPS ve výchozím nastavení.

![Upozornění o ochraně osobních údajů při ladění aplikace místně](publish-to-azure-webapp-using-vscode/_static/run-webapp-https-warning.jpg)

Udržovat relaci ladění, klikněte na tlačítko `Advanced` a potom `Continue to localhost (unsafe)`.

## <a name="generate-the-deployment-package-locally"></a>Vygenerujte balíček pro nasazení místně

- Otevřete terminál aplikace Visual Studio Code
- Pomocí následujícího příkazu pro generování `Release` balíček do dílčí složky s názvem `publish`:
  - `dotnet publish -c Release -o ./publish`
- Nový `publish` složka se vytvoří ve složce struktura projektu

![Struktura složky publikování](publish-to-azure-webapp-using-vscode/_static/publish-folder.jpg)

## <a name="publish-to-azure-app-service"></a>Publikování do Azure App Service

Využití rozšíření Azure App Service pro Visual Studio Code, postupujte podle pokynů pro publikování na webu přímo do služby Azure App Service.

### <a name="if-youre-creating-a-new-web-app"></a>Pokud vytváříte novou webovou aplikaci

- Klikněte pravým tlačítkem myši `publish` a pak zvolte položku `Deploy to Web App...`
- Vyberte předplatné, které chcete vytvořit webovou aplikaci
- Vyberte `Create New Web App`
- Zadejte název webové aplikace

Rozšíření se vytvoří nová webová aplikace a automaticky spustí nasazení balíčku na ni. Po dokončení nasazení klikněte na tlačítko `Browse Website` k ověření nasazení.

![Zpráva nasazení bylo úspěšné.](publish-to-azure-webapp-using-vscode/_static/deployment-succeeded-message.jpg)

Po kliknutí na `Browse Website`, budete přejít k němu pomocí výchozí prohlížeč:

![Nová webová aplikace úspěšně nasadil](publish-to-azure-webapp-using-vscode/_static/new-webapp-deployed.jpg)

### <a name="if-youre-deploying-to-an-existing-web-app"></a>Pokud nasazení provádíte do existující webové aplikace

- Klikněte pravým tlačítkem myši `publish` a pak zvolte položku `Deploy to Web App...`
- Vyberte předplatné, které se nachází existující webové aplikace
- Vyberte webovou aplikaci ze seznamu
- Visual Studio Code se zeptá, jestli chcete přepsat existující obsah. Klikněte na tlačítko `Deploy` k potvrzení

Rozšíření se nasazení aktualizovaného obsahu do webové aplikace. Po dokončení klikněte na tlačítko `Browse Website` k ověření nasazení.

![Úspěšně nasazeno existující webové aplikace](publish-to-azure-webapp-using-vscode/_static/existing-webapp-deployed.jpg)

## <a name="next-steps"></a>Další kroky

- [Vytvoření prvního kanálu Azure DevOps](/azure/devops/pipelines/create-first-pipeline)

## <a name="additional-resources"></a>Další zdroje

- [Azure App Service](/azure/app-service/app-service-web-overview)
- [Skupiny prostředků Azure](/azure/azure-resource-manager/resource-group-overview#resource-groups)
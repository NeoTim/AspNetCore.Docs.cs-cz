---
title: Nasazení aplikací ASP.NET Core do služby Azure App Service
author: bradygaster
description: Tento článek obsahuje odkazy na hostitele Azure a nasadit prostředky.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/16/2019
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: ba9671f68a0faf99ff5232a6d5dd132d0a1d5ac5
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665142"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a>Nasazení aplikací ASP.NET Core do služby Azure App Service

[Azure App Service](https://azure.microsoft.com/services/app-service/) je [platforma pro cloud computing společnosti Microsoft](https://azure.microsoft.com/) pro hostování webových aplikací, včetně ASP.NET Core.

## <a name="useful-resources"></a>Užitečné materiály

[Dokumentace ke službě App Service](/azure/app-service/) je domovpro dokumentaci, kurzy, ukázky, návody a další prostředky pro Azure Apps. Dva pozoruhodné výukové programy, které se týkající se hostování ASP.NET aplikace Core jsou:

[Vytvoření webové aplikace ASP.NET Core v Azure](/azure/app-service/app-service-web-get-started-dotnet)  
Pomocí Visual Studia můžete vytvořit a nasadit webovou aplikaci ASP.NET Core do služby Azure App Service ve Windows.

[Vytvoření aplikace ASP.NET Core ve službě App Service na Linuxu](/azure/app-service/containers/quickstart-dotnetcore)  
Pomocí příkazového řádku můžete vytvořit a nasadit ASP.NET webovou aplikaci Core do služby Azure App Service na Linuxu.

Podívejte se [na ASP.NET Core na panelu služby App Service](https://aspnetcoreon.azurewebsites.net/) pro verzi ASP.NET Core, která je dostupná ve službě Azure App.

Přihlaste se k odběru úložiště [oznámení služby App Service](https://github.com/Azure/app-service-announcements/) a sledujte problémy. Tým služby App Service pravidelně zveřejňuje oznámení a scénáře přicházející do služby App Service.

Následující články jsou k dispozici v dokumentaci ASP.NET Core:

<xref:tutorials/publish-to-azure-webapp-using-vs>  
Zjistěte, jak publikovat aplikaci ASP.NET Core do služby Azure App Service pomocí Visual Studia.

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
Zjistěte, jak pomocí Sady Visual Studio vytvořit ASP.NET webovou aplikaci Core a nasadit ji do služby Azure App Service pomocí Gitu pro průběžné nasazení.

[Vytvoření prvního kanálu](/azure/devops/pipelines/get-started-yaml)  
Nastavte sestavení CI pro aplikaci ASP.NET Core a pak vytvořte verzi průběžného nasazení do služby Azure App Service.

[Izolované ho poli Azure Web Appu](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
Seznamte se s omezeními spuštění služby Azure App Service vynucenými platformou Azure Apps.

<xref:test/troubleshoot>  
Seznamte se s ASP.NET základními projekty a vyřešujte je.

## <a name="application-configuration"></a>Konfigurace aplikací

### <a name="platform"></a>Platforma

Architektura platformy (x86/x64) aplikace App Services se nastaví v nastavení aplikace na Webu Azure Portal pro aplikace, které jsou hostované na výpočetní (základní) nebo vyšší úrovni hostování řady A. Zkontrolujte, zda nastavení publikování aplikace (například v profilu publikování ve Visual Studiu [(.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) odpovídá nastavení v konfiguraci služby aplikace na webu Azure Portal.

::: moniker range=">= aspnetcore-2.2"

Runtimes pro 64bitové (x64) a 32bitové (x86) aplikace jsou k dispozici ve službě Azure App Service. Sada [.NET Core SDK](/dotnet/core/sdk) dostupná ve službě App Service je 32bitová, ale 64bitové aplikace vytvořené místně pomocí konzoly Kudu nebo procespublikování v sadě Visual Studio můžete nasadit místně pomocí [konzoly Kudu](https://github.com/projectkudu/kudu/wiki) nebo procespublikování. Další informace najdete v části [Publikování a nasazení aplikace.](#publish-and-deploy-the-app)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Pro aplikace s nativnízávislosti, runtimes pro 32bitové (x86) aplikace jsou k dispozici na Azure App Service. Sada [.NET Core SDK](/dotnet/core/sdk) dostupná ve službě App Service je 32bitová.

::: moniker-end

Další informace o součástech rozhraní .NET Core framework a metodách distribuce, jako jsou informace o běhu .NET Core a .NET Core SDK, naleznete [v tématu About .NET Core: Composition](/dotnet/core/about#composition).

### <a name="packages"></a>Balíčky

Zahrňte následující balíčky NuGet, které poskytují funkce automatického protokolování pro aplikace nasazené do služby Azure App Service:

* [Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) používá [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) k zajištění ASP.NET základní integrace se službou Azure App Service. Přidané funkce protokolování jsou `Microsoft.AspNetCore.AzureAppServicesIntegration` poskytovány balíčkem.
* [Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) provede [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) pro přidání zprostředkovatelů protokolování `Microsoft.Extensions.Logging.AzureAppServices` diagnostiky služby Azure App Service v balíčku.
* [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) poskytuje implementace protokolů pro podporu protokolů diagnostiky služby Azure App Service a funkcí streamování protokolů.

Předchozí balíčky nejsou k dispozici z [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app). Aplikace, které cílí na `Microsoft.AspNetCore.App` rozhraní .NET Framework nebo odkazují na metabalíček, musí explicitně odkazovat na jednotlivé balíčky v souboru projektu aplikace.

## <a name="override-app-configuration-using-the-azure-portal"></a>Přepsání konfigurace aplikace pomocí portálu Azure Portal

Nastavení aplikací na webu Azure Portal umožňuje nastavit proměnné prostředí pro aplikaci. Proměnné prostředí mohou být spotřebovány [zprostředkovatelem konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).

Když se na webu Azure Portal vytvoří nebo upraví nastavení aplikace a vybere se tlačítko **Uložit,** aplikace Azure se restartuje. Proměnná prostředí je k dispozici aplikaci po restartování služby.

::: moniker range=">= aspnetcore-3.0"

Když aplikace používá [obecný hostitel](xref:fundamentals/host/generic-host), proměnné prostředí se načtou do konfigurace aplikace, když <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> se volá k sestavení hostitele. Další informace naleznete <xref:fundamentals/host/generic-host> v tématu a [zprostředkovatelkonfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Když aplikace používá [webhostingu](xref:fundamentals/host/web-host), proměnné prostředí se načtou do konfigurace aplikace, když <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> se volá k sestavení hostitele. Další informace naleznete <xref:fundamentals/host/web-host> v tématu a [zprostředkovatelkonfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scénáře proxy serveru a vyrovnávání zatížení

[Middleware integrace služby IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), který konfiguruje middleware předávaných záhlaví při hostování [mimo proces,](xref:host-and-deploy/iis/index#out-of-process-hosting-model)a ASP.NET core modul jsou konfigurovány tak, aby předávaly schéma (HTTP/HTTPS) a vzdálenou adresu IP, ze které požadavek pochází. Další konfigurace může být vyžadována pro aplikace hostované za dalšími proxy servery a nástroji pro vyrovnávání zatížení. Další informace naleznete v [tématu Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).

## <a name="monitoring-and-logging"></a>Monitorování a protokolování

::: moniker range=">= aspnetcore-3.0"

ASP.NET aplikace Core nasazené do služby App Service automaticky obdrží rozšíření služby App Service **ASP.NET integrace core protokolování**. Toto rozšíření umožňuje integraci protokolování pro aplikace ASP.NET Core ve službě Azure App Service.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET aplikace Core nasazené do služby App Service automaticky obdrží rozšíření služby App Service **ASP.NET rozšíření core protokolování**. Toto rozšíření umožňuje integraci protokolování pro aplikace ASP.NET Core ve službě Azure App Service.

::: moniker-end

Informace o monitorování, protokolování a odstraňování potíží naleznete v následujících článcích:

[Monitorování aplikací ve službě Azure App Service](/azure/app-service/web-sites-monitor)  
Přečtěte si, jak zkontrolovat kvóty a metriky pro aplikace a plány služby App Service.

[Povolení protokolování diagnostiky aplikací ve službě Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log)  
Zjistěte, jak povolit a získat přístup k diagnostickému protokolování pro stavové kódy HTTP, neúspěšné požadavky a aktivitu webového serveru.

<xref:fundamentals/error-handling>  
Seznamte se s běžnými přístupy ke zpracování chyb v aplikacích ASP.NET Core.

<xref:test/troubleshoot-azure-iis>  
Zjistěte, jak diagnostikovat problémy s nasazením služby Azure App Service pomocí aplikací ASP.NET Core.

<xref:host-and-deploy/azure-iis-errors-reference>  
Podívejte se na běžné chyby konfigurace nasazení pro aplikace hostované službou Azure App Service/IIS s radami pro řešení potíží.

## <a name="data-protection-key-ring-and-deployment-slots"></a>Kroužek klíčů pro ochranu dat a sloty pro nasazení

[Klíče ochrany dat](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) jsou uloženy ve složce *%HOME%\ASP.NET\DataProtection-Keys.* Tato složka je zálohována síťovým úložištěm a je synchronizována ve všech počítačích hostujících aplikaci. Klíče nejsou v klidu chráněny. Tato složka poskytuje vyzváněcí klávesy pro všechny instance aplikace v jednom slotu nasazení. Samostatné sloty nasazení, jako je pracovní a produkční, nesdílejí kroužek klíčů.

Při přepnutí mezi sloty nasazení nebude žádný systém využívající ochranu dat schopen dešifrovat uložená data pomocí kroužku klíče uvnitř předchozího slotu. ASP.NET Cookie Middleware používá ochranu dat k ochraně svých souborů cookie. To vede k tomu, že uživatelé jsou odhlášeni z aplikace, která používá standardní ASP.NET cookie Middleware. Pro řešení kroužku na klíče nezávislé na slotu použijte externího poskytovatele kroužku na klíče, například:

* Azure Blob Storage
* Azure Key Vault
* Úložiště SQL
* Redis Cache

Další informace naleznete v tématu <xref:security/data-protection/implementation/key-storage-providers>.
<a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>

## <a name="deploy-an-aspnet-core-app-that-uses-a-net-core-preview"></a>Nasazení aplikace ASP.NET Core, která používá náhled jádra .NET

Pokud chcete nasadit aplikaci, která používá předběžnou verzi .NET Core, podívejte se na následující prostředky. Tyto přístupy se také používají, když je k dispozici runtime, ale sada SDK nebyla nainstalována ve službě Azure App Service.

* [Určení verze sady .NET Core SDK pomocí azure pipelines](#specify-the-net-core-sdk-version-using-azure-pipelines)
* [Nasazení samostatné aplikace preview](#deploy-a-self-contained-preview-app)
* [Použití Dockeru s webovými aplikacemi pro kontejnery](#use-docker-with-web-apps-for-containers)
* [Instalace rozšíření webu ve verzi Preview](#install-the-preview-site-extension)

Podívejte se [na ASP.NET Core na panelu služby App Service](https://aspnetcoreon.azurewebsites.net/) pro verzi ASP.NET Core, která je dostupná ve službě Azure App.

### <a name="specify-the-net-core-sdk-version-using-azure-pipelines"></a>Určení verze sady .NET Core SDK pomocí azure pipelines

Pomocí [scénářů CI/CD služby Azure App Service](/azure/app-service/deploy-continuous-deployment) můžete nastavit sestavení průběžné integrace s Azure DevOps. Po vytvoření sestavení Azure DevOps volitelně nakonfigurujte sestavení tak, aby používalo konkrétní verzi sady SDK. 

#### <a name="specify-the-net-core-sdk-version"></a>Určení verze sady .NET Core SDK

Při použití Centra nasazení služby App Service k vytvoření sestavení Azure DevOps `Restore`obsahuje výchozí kanál sestavení kroky pro `Build`, , `Test`a `Publish`. Chcete-li určit verzi sady SDK, vyberte tlačítko **Přidat (+)** v seznamu úloh agenta a přidejte nový krok. Vyhledejte na panelu hledání **souboru .NET Core SDK.** 

![Přidání kroku sady .NET Core SDK](index/add-sdk-step.png)

Přesuňte krok do první pozice v sestavení tak, aby následující kroky používaly zadanou verzi sady .NET Core SDK. Zadejte verzi sady .NET Core SDK. V tomto příkladu je sada `3.0.100`SDK nastavena na .

![Dokončení kroku sady SDK](index/sdk-step-first-place.png)

Chcete-li publikovat [samostatné nasazení (SCD),](/dotnet/core/deploying/#self-contained-deployments-scd)nakonfigurujte scd v `Publish` kroku a zadejte identifikátor [runtime (RID).](/dotnet/core/rid-catalog)

![Samostatné publikování](index/self-contained.png)

### <a name="deploy-a-self-contained-preview-app"></a>Nasazení samostatné aplikace preview

[Samostatné nasazení (SCD),](/dotnet/core/deploying/#self-contained-deployments-scd) které se zaměřuje na běhový čas náhledu nese za běhu náhledu v nasazení.

Při nasazování samostatné aplikace:

* Web ve službě Azure App Service nevyžaduje [rozšíření webu ve verzi Preview](#install-the-preview-site-extension).
* Aplikace musí být publikována s jiným přístupem než při publikování pro [nasazení závislé na rámci (FDD).](/dotnet/core/deploying#framework-dependent-deployments-fdd)

Postupujte podle pokynů v části [Nasazení samostatné aplikace.](#deploy-the-app-self-contained)

### <a name="use-docker-with-web-apps-for-containers"></a>Použití Dockeru s webovými aplikacemi pro kontejnery

Docker [Hub](https://hub.docker.com/r/microsoft/aspnetcore/) obsahuje nejnovější náhled imitace Dockeru. Obrázky lze použít jako základní obrázek. Použijte bitovou kopii a nasaďte do webových aplikací pro kontejnery normálně.

### <a name="install-the-preview-site-extension"></a>Instalace rozšíření webu ve verzi Preview

Pokud dojde k potížím pomocí rozšíření webu preview, otevřete [problém dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/issues).

1. Z portálu Azure přejděte na službu App Service.
1. Vyberte webovou aplikaci.
1. Do vyhledávacího pole zadejte "ex", chcete-li filtrovat "Rozšíření" nebo posuňte seznam nástrojů pro správu dolů.
1. Vyberte **Extensions** (Rozšíření).
1. Vyberte **Přidat**.
1. Vyberte rozšíření **prostředí ASP.NET jádra {X.Y} ({x64|x86})** ze seznamu, kde `{X.Y}` je verze náhledu ASP.NET Core a `{x64|x86}` určuje platformu.
1. Chcete-li přijmout zákonné podmínky, vyberte **možnost OK.**
1. Chcete-li nainstalovat rozšíření, vyberte **možnost OK.**

Po dokončení operace je nainstalován nejnovější náhled jádra .NET. Ověřte instalaci:

1. Vyberte **rozšířené nástroje**.
1. V **rozšířeném nástroji**vyberte možnost **Přejít** .
1. Vyberte položku nabídky nabídky **ladění konzoly** > **PowerShell.**
1. Na příkazové masko powershellové matné spusťte následující příkaz. V příkazu nahraďte `{X.Y}` ASP.NET verzi `{PLATFORM}` za běhu Core a platformu:

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   Příkaz se `True` vrátí při instalaci runtime náhledu x64.

> [!NOTE]
> Architektura platformy (x86/x64) aplikace App Services se nastaví v nastavení aplikace na Webu Azure Portal pro aplikace, které jsou hostované na výpočetní (základní) nebo vyšší úrovni hostování řady A. Zkontrolujte, zda nastavení publikování aplikace (například v profilu publikování ve Visual Studiu [(.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)odpovídá nastavení v konfiguraci služby aplikace na webu Azure Portal.
>
> Pokud je aplikace spuštěna v režimu v procesu a architektura platformy je nakonfigurovaná pro 64bitovou (x64), ASP.NET Core Module používá 64bitový modul runtime náhledu, pokud je k dispozici. Nainstalujte rozšíření **ASP.NET core {X.Y} (x64) Runtime** pomocí portálu Azure Portal.
>
> Po instalaci runtime náhledu x64 spusťte následující příkaz v příkazovém okně Azure Kudu PowerShell a ověřte instalaci. V následujícím příkazu nahraďte verzi `{X.Y}` ASP.NET core runtime:
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> Příkaz se `True` vrátí při instalaci runtime náhledu x64.

> [!NOTE]
> **ASP.NET rozšíření** Core umožňuje další funkce pro ASP.NET Core ve službách Azure App Services, jako je povolení protokolování Azure. Rozšíření se nainstaluje automaticky při nasazování z Visual Studia. Pokud rozšíření není nainstalované, nainstalujte ho do aplikace.

**Použití rozšíření webu náhledu se šablonou ARM**

Pokud se k vytváření a nasazování `siteextensions` aplikací používá šablona ARM, typ prostředku se dá použít k přidání rozšíření webu do webové aplikace. Příklad:

[!code-json[](index/sample/arm.json?highlight=2)]

## <a name="publish-and-deploy-the-app"></a>Publikování a nasazení aplikace

::: moniker range=">= aspnetcore-2.2"

Pro 64bitové nasazení:

* K vytvoření 64bitové aplikace použijte 64bitovou sadu .NET Core SDK.
* Nastavte **platformu** na **64 bitů** v**nastavení Obecné** **konfigurace** > služby App Service . Aplikace musí používat základní nebo vyšší plán služeb, aby umožnila výběr bitovosti platformy.

::: moniker-end

### <a name="deploy-the-app-framework-dependent"></a>Nasazení architektury aplikace závislé

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Vyberte **Publikovat** > **{Název aplikace}** na panelu nástrojů Sady Visual Studio nebo klikněte pravým tlačítkem myši na projekt v **Průzkumníku řešení** a vyberte **Publikovat**.
1. V **dialogovém** okně Vybrat cíl publikování zkontrolujte, že je vybraná **služba App Service.**
1. Vyberte **Upřesnit**. Otevře se dialogové okno **Publikovat.**
1. V dialogovém okně **Publikovat:**
   * Zkontrolujte, zda je vybrána konfigurace **vydání.**
   * Otevřete rozevírací seznam **Režim nasazení** a vyberte možnost Závislá **na rámci**.
   * Jako cílový **běh**vyberte možnost **Přenosné** .
   * Pokud potřebujete při nasazení odebrat další soubory, otevřete **možnosti publikování souborů** a zaškrtněte políčko pro odebrání dalších souborů v cílovém umístění.
   * Vyberte **Uložit**.
1. Vytvořte nový web nebo aktualizujte existující web podle zbývajících pokynů průvodce publikováním.

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli/)

1. V souboru projektu nezadávejte [identifikátor runtime (RID).](/dotnet/core/rid-catalog)

1. Z příkazového prostředí publikujte aplikaci v konfiguraci release pomocí příkazu [dotnet publish.](/dotnet/core/tools/dotnet-publish) V následujícím příkladu se aplikace publikuje jako aplikace závislá na rámci:

   ```console
   dotnet publish --configuration Release
   ```

1. Přesuňte obsah adresáře *bin/Release/{TARGET FRAMEWORK}/publish* na web v aplikaci App Service. Pokud přetaženíobsahu složky *publikování* z místního pevného disku nebo síťové sdílené složky přímo `D:\home\site\wwwroot` do služby App Service v konzole [Kudu](https://github.com/projectkudu/kudu/wiki) přetáhněte soubory do složky v konzole Kudu.

---

### <a name="deploy-the-app-self-contained"></a>Nasazení aplikace soběstačné

Použití sady Visual Studio nebo rozhraní CLI jádra .NET pro [samostatné nasazení (SCD).](/dotnet/core/deploying/#self-contained-deployments-scd)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Vyberte **Publikovat** > **{Název aplikace}** na panelu nástrojů Sady Visual Studio nebo klikněte pravým tlačítkem myši na projekt v **Průzkumníku řešení** a vyberte **Publikovat**.
1. V **dialogovém** okně Vybrat cíl publikování zkontrolujte, že je vybraná **služba App Service.**
1. Vyberte **Upřesnit**. Otevře se dialogové okno **Publikovat.**
1. V dialogovém okně **Publikovat:**
   * Zkontrolujte, zda je vybrána konfigurace **vydání.**
   * Otevřete rozevírací seznam **Režim nasazení** a vyberte **Položku Samostatně obsažené**.
   * Vyberte cílový runtime z rozevíracího seznamu **Cílový běh.** Výchozí formát je `win-x86`.
   * Pokud potřebujete při nasazení odebrat další soubory, otevřete **možnosti publikování souborů** a zaškrtněte políčko pro odebrání dalších souborů v cílovém umístění.
   * Vyberte **Uložit**.
1. Vytvořte nový web nebo aktualizujte existující web podle zbývajících pokynů průvodce publikováním.

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli/)

1. V souboru projektu zadejte jeden nebo více [identifikátorů runtime (ID).](/dotnet/core/rid-catalog) Použijte `<RuntimeIdentifier>` (singulární) pro `<RuntimeIdentifiers>` jeden RID nebo použijte (množné číslo) k poskytnutí seznamu ridů oddělených středníkem. V následujícím příkladu `win-x86` je určen rid:

   ```xml
   <PropertyGroup>
     <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. Z příkazového prostředí publikujte aplikaci v konfiguraci vydání pro běh hostitele pomocí příkazu [dotnet publish.](/dotnet/core/tools/dotnet-publish) V následujícím příkladu je aplikace publikována pro `win-x86` RID. Rid dodaný `--runtime` k možnosti musí `<RuntimeIdentifier>` být `<RuntimeIdentifiers>`uveden ve vlastnosti (nebo ) v souboru projektu.

   ```console
   dotnet publish --configuration Release --runtime win-x86 --self-contained
   ```

1. Přesuňte obsah adresáře *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* na web v aplikaci App Service. Pokud přetaženíobsahu složky *publikování* z místního pevného disku nebo síťové sdílené složky přímo `D:\home\site\wwwroot` do služby App Service v konzole Kudu přetáhněte soubory do složky v konzole Kudu.

---

## <a name="protocol-settings-https"></a>Nastavení protokolu (HTTPS)

Vazby zabezpečeného protokolu umožňují zadat certifikát, který se má použít při odpovídání na požadavky přes protokol HTTPS. Vazba vyžaduje platný soukromý certifikát (*.pfx*) vydaný pro konkrétní název hostitele. Další informace najdete [v tématu Kurz: Vazba existující vlastní SSL certifikát na Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).

## <a name="transform-webconfig"></a>Transformace souboru web.config

Pokud potřebujete transformovat *web.config* při publikování (například nastavit proměnné prostředí na základě konfigurace, profilu nebo prostředí), přečtěte si viz <xref:host-and-deploy/iis/transform-webconfig>.

## <a name="additional-resources"></a>Další zdroje

* [Přehled služby App Service](/azure/app-service/app-service-web-overview)
* [Azure App Service: Nejlepší místo pro hostování aplikací .NET (55minutové video s přehledem)](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12minutové video)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [Přehled diagnostiky služby Azure App Service](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

Služba Azure App Service na serveru Windows Server používá [Internetovou informační službu (IIS).](https://www.iis.net/) Následující témata se týkající se základní technologie iIS:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [Windows Server – obsah správce IT pro aktuální a předchozí verze](/windows-server/windows-server-versions)

---
title: Nasazení aplikací ASP.NET Core pro Azure App Service
author: bradygaster
description: Tento článek obsahuje odkazy na hostitele Azure a nasazení prostředků.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 10/11/2019
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: 392868b4fc9105279f8f3b10436a9915123e7070
ms.sourcegitcommit: 032113208bb55ecfb2faeb6d3e9ea44eea827950
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2019
ms.locfileid: "73190653"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a>Nasazení aplikací ASP.NET Core pro Azure App Service

[Azure App Service](https://azure.microsoft.com/services/app-service/) je [platforma cloud computingu od Microsoftu](https://azure.microsoft.com/) pro hostování webových aplikací, včetně ASP.NET Core.

## <a name="useful-resources"></a>Užitečné zdroje

[Dokumentace k App Service](/azure/app-service/) je Domovská stránka pro Azure Apps, kurzy, ukázky, návody a další materiály. Existují dva významné kurzy, které se týkají hostování ASP.NET Corech aplikací:

[Vytvoření webové aplikace v ASP.NET Core v Azure](/azure/app-service/app-service-web-get-started-dotnet)  
Pomocí sady Visual Studio můžete vytvořit a nasadit webovou aplikaci ASP.NET Core pro Azure App Service ve Windows.

[Vytvoření aplikace ASP.NET Core v App Service v systému Linux](/azure/app-service/containers/quickstart-dotnetcore)  
Pomocí příkazového řádku můžete vytvořit a nasadit webovou aplikaci ASP.NET Core pro Azure App Service v systému Linux.

Verzi ASP.NET Core dostupnou ve službě Azure App Service najdete na [App Service řídicím panelu ASP.NET Core](https://aspnetcoreon.azurewebsites.net/) .

Přihlaste se k odběru úložiště [oznámení App Service](https://github.com/Azure/app-service-announcements/) a sledujte problémy. Tým App Service pravidelně publikuje oznámení a scénáře, které dorazí v App Service.

V dokumentaci k ASP.NET Core jsou k dispozici následující články:

<xref:tutorials/publish-to-azure-webapp-using-vs>  
Naučte se publikovat aplikaci ASP.NET Core pro Azure App Service pomocí sady Visual Studio.

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
Naučte se, jak vytvořit webovou aplikaci ASP.NET Core pomocí sady Visual Studio a nasadit ji do Azure App Service pomocí Gitu pro průběžné nasazování.

[Vytvoření prvního kanálu](/azure/devops/pipelines/get-started-yaml)  
Nastavte sestavení CI pro aplikaci ASP.NET Core a pak vytvořte Azure App Service vydanou verzi pro průběžné nasazování.

[Sandbox webové aplikace Azure](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
Zjištění omezení spouštění Azure App Service runtime, která vynutila platforma Azure Apps

<xref:test/troubleshoot>  
Pochopení a řešení potíží s chybami a problémy s ASP.NET Core projekty.

## <a name="application-configuration"></a>Konfigurace aplikace

### <a name="platform"></a>Platforma

::: moniker range=">= aspnetcore-2.2"

V Azure App Service jsou k dispozici moduly runtime pro 64 (x64) a 32-bit (x86) aplikací. [.NET Core SDK](/dotnet/core/sdk) k dispozici v App Service je 32-bit, ale můžete nasadit 64 aplikace sestavené místně pomocí konzoly [Kudu](https://github.com/projectkudu/kudu/wiki) nebo procesu publikování v aplikaci Visual Studio. Další informace najdete v části [publikování a nasazení aplikace](#publish-and-deploy-the-app) .

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Pro aplikace s nativními závislostmi jsou moduly runtime pro 32 aplikace (x86) k dispozici na Azure App Service. [.NET Core SDK](/dotnet/core/sdk) k dispozici v App Service je 32-bit.

::: moniker-end

Další informace o komponentách rozhraní .NET Core Framework a metodách distribuce, jako jsou informace o modulu runtime .NET Core a .NET Core SDK, naleznete v tématu [About .NET Core: kompozice](/dotnet/core/about#composition).

### <a name="packages"></a>Balíčky

Zahrňte následující balíčky NuGet pro poskytování automatických funkcí protokolování pro aplikace nasazené do Azure App Service:

* [Microsoft. AspNetCore. AzureAppServices. HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) využívá [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) k poskytování ASP.NET Coreho urychlené integrace s Azure App Service. Přidané funkce protokolování jsou k dispozici v balíčku `Microsoft.AspNetCore.AzureAppServicesIntegration`.
* [Microsoft. AspNetCore. AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) spustí [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) pro Azure App Service přidání zprostředkovatelů protokolování diagnostiky do balíčku `Microsoft.Extensions.Logging.AzureAppServices`.
* [Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) poskytuje implementace protokolovacích nástrojů pro podporu Azure App Servicech diagnostických protokolů a funkcí streamování protokolů.

Předchozí balíčky nejsou k dispozici ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app). Aplikace, které cílí na .NET Framework nebo odkazují na `Microsoft.AspNetCore.App` Metapackage musí explicitně odkazovat na jednotlivé balíčky v souboru projektu aplikace.

## <a name="override-app-configuration-using-the-azure-portal"></a>Přepsání konfigurace aplikace pomocí webu Azure Portal

Nastavení aplikace na webu Azure Portal umožňuje nastavit proměnné prostředí pro aplikaci. Proměnné prostředí mohou být spotřebovány [poskytovatelem konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).

Když se na webu Azure Portal vytvoří nebo upraví nastavení aplikace a vybere se tlačítko **Save (Uložit** ), aplikace Azure se restartuje. Proměnná prostředí je k dispozici aplikaci po restartování služby.

::: moniker range=">= aspnetcore-3.0"

Když aplikace používá [obecného hostitele](xref:fundamentals/host/generic-host), proměnné prostředí se ve výchozím nastavení načtou do konfigurace aplikace a Poskytovatel konfigurace musí být přidaný vývojářem. Vývojář určí předponu proměnné prostředí při přidání poskytovatele konfigurace. Další informace najdete v tématu <xref:fundamentals/host/generic-host> a ve [zprostředkovateli konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Když aplikace vytvoří hostitele pomocí [webhost. CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder), proměnné prostředí, které konfigurují hostitele, používají předponu `ASPNETCORE_`. Další informace najdete v tématu <xref:fundamentals/host/web-host> a ve [zprostředkovateli konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scénáře proxy serveru a nástroje pro vyrovnávání zatížení

[IIS Integration middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), který konfiguruje předávaný middleware hlaviček při hostování [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model)a modul ASP.NET Core je nakonfigurovaný pro přeposílání schématu (http/https) a vzdálené IP adresy, na které pochází požadavek . Pro aplikace hostované za dalšími proxy servery a nástroji pro vyrovnávání zatížení může být vyžadována další konfigurace. Další informace najdete v tématu [konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).

## <a name="monitoring-and-logging"></a>Monitorování a protokolování

::: moniker range=">= aspnetcore-3.0"

Aplikace ASP.NET Core nasazené pro App Service automaticky dostanou rozšíření App Service **ASP.NET Core Integration Logging**. Rozšíření umožňuje integraci protokolování pro aplikace ASP.NET Core v Azure App Service.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Aplikace ASP.NET Core nasazené pro App Service automaticky dostanou rozšíření App Service **ASP.NET Core rozšíření protokolování**. Rozšíření umožňuje integraci protokolování pro aplikace ASP.NET Core v Azure App Service.

::: moniker-end

Informace o monitorování, protokolování a odstraňování potíží najdete v následujících článcích:

[Monitorování aplikací v Azure App Service](/azure/app-service/web-sites-monitor)  
Naučte se kontrolovat kvóty a metriky pro aplikace a App Service plány.

[Povolit protokolování diagnostiky pro aplikace v Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log)  
Zjistěte, jak povolit a získat přístup k protokolování diagnostiky pro stavové kódy HTTP, neúspěšné žádosti a aktivitu webového serveru.

<xref:fundamentals/error-handling>  
Seznamte se s běžnými přístupy ke zpracování chyb v aplikacích ASP.NET Core.

<xref:test/troubleshoot-azure-iis>  
Naučte se diagnostikovat problémy s Azure App Service nasazeními s ASP.NET Core aplikacemi.

<xref:host-and-deploy/azure-iis-errors-reference>  
Projděte si nejčastější chyby konfigurace nasazení pro aplikace hostované službou Azure App Service/IIS s pokyny k řešení potíží.

## <a name="data-protection-key-ring-and-deployment-slots"></a>Kroužky klíčů pro ochranu dat a sloty nasazení

[Klíče ochrany dat](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) jsou trvale uložené ve složce *%Home%\ASP.NET\DataProtection-Keys* . Tato složka je zajištěná síťovým úložištěm a je synchronizovaná napříč všemi počítači hostujícími aplikaci. Klíče nejsou chráněny v klidovém umístění. Tato složka poskytuje klíčového prstence pro všechny instance aplikace v jednom slotu nasazení. Samostatné sloty nasazení, jako je například pracovní a produkční, nesdílejte klíč Ring.

Při záměně mezi sloty nasazení nebude možné dešifrovat uložená data pomocí nástroje Data Protection, a to pomocí klíčeového kruhu v předchozí pozici. Middleware souborů cookie ASP.NET používá ochranu dat k ochraně souborů cookie. To vede k tomu, že se uživatelé odhlásí z aplikace, která používá standardní middleware souborů cookie ASP.NET. Pro řešení Key Ring nezávislé na slotech použijte externího poskytovatele služby Key Ring, například:

* Blob Storage Azure
* Azure Key Vault
* Úložiště SQL
* Redis Cache

Další informace najdete v tématu <xref:security/data-protection/implementation/key-storage-providers>.
<a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>

## <a name="deploy-aspnet-core-30-to-azure-app-service"></a>Nasazení ASP.NET Core 3,0 do Azure App Service

Azure App Service podporuje ASP.NET Core 3,0. Chcete-li nasadit verzi Preview verze .NET Core, která je novější než .NET Core 3,0, použijte jeden z následujících postupů. Tyto přístupy se používají také v případě, že je modul runtime k dispozici, ale sada SDK nebyla nainstalována na Azure App Service.

* [Určení verze .NET Core SDK pomocí Azure Pipelines](#specify-the-net-core-sdk-version-using-azure-pipelines)
* [Nasaďte samostatně obsaženou aplikaci pro náhled](#deploy-a-self-contained-preview-app).
* [Pro kontejnery použijte Docker s Web Apps](#use-docker-with-web-apps-for-containers).
* [Nainstalujte rozšíření webu verze Preview](#install-the-preview-site-extension).

### <a name="specify-the-net-core-sdk-version-using-azure-pipelines"></a>Určení verze .NET Core SDK pomocí Azure Pipelines

Pomocí [Azure App Service scénářů CI/CD](/azure/app-service/deploy-continuous-deployment) můžete nastavit průběžnou integraci sestavení s využitím Azure DevOps. Po vytvoření sestavení Azure DevOps můžete nakonfigurovat sestavení tak, aby používalo konkrétní verzi sady SDK. 

#### <a name="specify-the-net-core-sdk-version"></a>Zadejte verzi .NET Core SDK

Při použití centra nasazení App Service k vytvoření buildu Azure DevOps obsahuje výchozí kanál sestavení kroky pro `Restore`, `Build`, `Test`a `Publish`. Chcete-li zadat verzi sady SDK, přidejte nový krok kliknutím na tlačítko **Přidat (+)** v seznamu úloh agenta. Na panelu hledání vyhledejte **.NET Core SDK** . 

![Přidat .NET Core SDK krok](index/add-sdk-step.png)

Přesuňte krok do první pozice v sestavení tak, aby následující kroky používaly určenou verzi .NET Core SDK. Zadejte verzi .NET Core SDK. V tomto příkladu je sada SDK nastavena na `3.0.100`.

![Krok dokončené sady SDK](index/sdk-step-first-place.png)

Chcete-li publikovat [samostatné nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd), nakonfigurujte SCD v kroku `Publish` a zadejte [identifikátor modulu runtime (RID)](/dotnet/core/rid-catalog).

![Publikování samostatného kontejneru](index/self-contained.png)

### <a name="deploy-a-self-contained-preview-app"></a>Nasazení samostatné aplikace ve verzi Preview

[Samostatné nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) , které cílí na modul runtime verze Preview, přenáší modul runtime verze Preview do nasazení.

Při nasazení samostatně obsažené aplikace:

* Lokalita v Azure App Service nevyžaduje [rozšíření webu verze Preview](#install-the-preview-site-extension).
* Aplikace musí být publikována při jiném přístupu než při publikování pro [nasazení závislé na rozhraní (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).

Postupujte podle pokynů v části [nasazení samostatně obsaženého oddílu aplikace](#deploy-the-app-self-contained) .

### <a name="use-docker-with-web-apps-for-containers"></a>Použití Docker s Web Apps pro kontejnery

[Dokovací centrum](https://hub.docker.com/r/microsoft/aspnetcore/) obsahuje nejnovější image Docker Preview. Obrázky lze použít jako základní bitovou kopii. Použijte image a nasaďte ji normálně pro Web Apps kontejnerů.

### <a name="install-the-preview-site-extension"></a>Nainstalovat rozšíření webu verze Preview

Pokud dojde k potížím pomocí rozšíření webu verze Preview, otevřete [problém ASPNET/AspNetCore](https://github.com/aspnet/AspNetCore/issues).

1. Na webu Azure Portal přejděte na App Service.
1. Vyberte webovou aplikaci.
1. Do vyhledávacího pole zadejte "ex", chcete-li filtrovat "rozšíření", nebo se posuňte dolů v seznamu nástrojů pro správu.
1. Vyberte **rozšíření**.
1. Vyberte **Přidat**.
1. V seznamu vyberte rozšíření **Runtime ASP.NET Core {X. Y} ({x64 | x86})** , kde `{X.Y}` je ASP.NET Core verze preview a `{x64|x86}` Určuje platformu.
1. Kliknutím na **OK** přijměte právní podmínky.
1. Pro instalaci rozšíření vyberte **OK** .

Po dokončení operace je nainstalovaná nejnovější verze .NET Core Preview. Ověřte instalaci:

1. Vyberte **Rozšířené nástroje**.
1. Vyberte **Přejít** v **pokročilých nástrojích**.
1. Vyberte **konzolu ladění** > položku nabídky **PowerShellu** .
1. Na příkazovém řádku PowerShellu spusťte následující příkaz. Nahraďte ASP.NET Core verze modulu runtime pro `{X.Y}` a platformu pro `{PLATFORM}` v příkazu:

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   Příkaz vrátí `True`, když je nainstalován modul runtime x64 Preview.

> [!NOTE]
> Architektura platformy (x86/x64) aplikace App Services je nastavená v nastavení aplikace na webu Azure Portal pro aplikace, které jsou hostované na výpočetní úrovni řady A-Series nebo lepší úrovně hostování. Pokud je aplikace spuštěná v režimu v procesu a architektura platformy je nakonfigurovaná pro 64-bit (x64), modul ASP.NET Core používá 64 runtime Preview, pokud je k dispozici. Nainstalujte rozšíření **Runtime ASP.NET Core {X. Y} (x64)** .
>
> Po instalaci prostředí runtime x64 Preview spusťte následující příkaz v příkazovém okně prostředí Kudu PowerShell a ověřte instalaci. V příkazu nahraďte verzi modulu runtime ASP.NET Core `{X.Y}`:
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> Příkaz vrátí `True`, když je nainstalován modul runtime x64 Preview.

> [!NOTE]
> **Rozšíření ASP.NET Core** umožňují pro ASP.NET Core v Azure App Services další funkce, jako je například povolení protokolování Azure. Rozšíření se nainstaluje automaticky při nasazení ze sady Visual Studio. Pokud rozšíření není nainstalované, nainstalujte ho pro aplikaci.

**Použití rozšíření webu ve verzi Preview se šablonou ARM**

Pokud se k vytváření a nasazování aplikací používá šablona ARM, dá se `siteextensions` typ prostředku použít k přidání rozšíření webu do webové aplikace. Příklad:

[!code-json[](index/sample/arm.json?highlight=2)]

## <a name="publish-and-deploy-the-app"></a>Publikování a nasazení aplikace

### <a name="deploy-the-app-framework-dependent"></a>Nasazení závislého rozhraní App Framework

::: moniker range=">= aspnetcore-2.2"

Pro 64 [nasazení závislé na rozhraní](/dotnet/core/deploying/#framework-dependent-deployments-fdd):

* K vytvoření 64 aplikace použijte 64 .NET Core SDK.
* Nastavte hodnotu **Platform** na **64 Bit** v **konfiguraci** App Service > **Obecné nastavení**. Aby bylo možné vybrat bitová verze platformy, musí aplikace používat základní nebo vyšší plán služby.

::: moniker-end

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Vyberte **Build** > **Publikovat {název aplikace}** z panelu nástrojů sady Visual Studio nebo klikněte pravým tlačítkem na projekt v **Průzkumník řešení** a vyberte **publikovat**.
1. V dialogovém okně **vybrat cíl publikování** potvrďte, že je vybrána možnost **App Service** .
1. Vyberte **Upřesnit**. Otevře se dialogové okno **publikovat** .
1. V dialogovém okně **publikovat** :
   * Potvrďte, že je vybraná konfigurace **vydané verze** .
   * Otevřete rozevírací seznam **režim nasazení** a vyberte možnost závislé na **rozhraní**.
   * Jako **cílový modul runtime**vyberte **přenosný** .
   * Pokud potřebujete po nasazení odebrat další soubory, otevřete **Možnosti publikování souborů** a zaškrtnutím políčka odeberte další soubory v cílovém umístění.
   * Vyberte **Uložit**.
1. Pomocí zbývajících výzev Průvodce publikováním vytvořte novou lokalitu nebo aktualizujte existující web.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli/)

1. V souboru projektu nezadávejte [identifikátor modulu runtime (RID)](/dotnet/core/rid-catalog).

1. V příkazovém prostředí publikujte aplikaci v konfiguraci vydaných verzí pomocí příkazu [dotnet Publish](/dotnet/core/tools/dotnet-publish) . V následujícím příkladu je aplikace publikována jako aplikace závislá na rozhraní:

   ```console
   dotnet publish --configuration Release
   ```

1. Přesuňte obsah adresáře *bin/Release/{Target Framework}/Publish* do lokality v App Service. Pokud přetáhnete obsah složky pro *publikování* z místního pevného disku nebo sdílené síťové složky přímo do App Service v konzole [Kudu](https://github.com/projectkudu/kudu/wiki) , přetáhněte soubory do složky `D:\home\site\wwwroot` v konzole Kudu.

---

### <a name="deploy-the-app-self-contained"></a>Nasazení samostatně obsažené aplikace

Použijte nástroje sady Visual Studio nebo rozhraní příkazového řádku (CLI) pro [samostatně uzavřené nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Vyberte **Build** > **Publikovat {název aplikace}** z panelu nástrojů sady Visual Studio nebo klikněte pravým tlačítkem na projekt v **Průzkumník řešení** a vyberte **publikovat**.
1. V dialogovém okně **vybrat cíl publikování** potvrďte, že je vybrána možnost **App Service** .
1. Vyberte **Upřesnit**. Otevře se dialogové okno **publikovat** .
1. V dialogovém okně **publikovat** :
   * Potvrďte, že je vybraná konfigurace **vydané verze** .
   * Otevřete rozevírací seznam **režim nasazení** a vyberte možnost **samostatně obsaženo**.
   * Z rozevíracího seznamu **cílový modul runtime** vyberte cílový modul runtime. Výchozí hodnota je `win-x86`.
   * Pokud potřebujete po nasazení odebrat další soubory, otevřete **Možnosti publikování souborů** a zaškrtnutím políčka odeberte další soubory v cílovém umístění.
   * Vyberte **Uložit**.
1. Pomocí zbývajících výzev Průvodce publikováním vytvořte novou lokalitu nebo aktualizujte existující web.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli/)

1. V souboru projektu zadejte jeden nebo více [identifikátorů modulu runtime (identifikátorů RID)](/dotnet/core/rid-catalog). Pro jeden identifikátor RID použijte `<RuntimeIdentifier>` (jednotné číslo), nebo použijte `<RuntimeIdentifiers>` (plural) k poskytnutí seznamu identifikátorů ridů oddělených středníkem. V následujícím příkladu je zadán `win-x86` RID:

   ```xml
   <PropertyGroup>
     <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. Z příkazového prostředí publikujte aplikaci v konfiguraci vydání pro modul runtime hostitele pomocí příkazu [dotnet Publish](/dotnet/core/tools/dotnet-publish) . V následujícím příkladu je aplikace publikovaná pro `win-x86` RID. Ve vlastnosti `<RuntimeIdentifier>` (nebo `<RuntimeIdentifiers>`) v souboru projektu musí být uveden identifikátor RID, který je zadaný pro `--runtime`.

   ```console
   dotnet publish --configuration Release --runtime win-x86 --self-contained
   ```

1. Přesuňte obsah složky *bin/Release/{Target Framework}/{runtime identifikátor}/Publish* do lokality v App Service. Pokud přetáhnete obsah složky pro *publikování* z místního pevného disku nebo sdílené síťové složky přímo do App Service v konzole Kudu, přetáhněte soubory do složky `D:\home\site\wwwroot` v konzole Kudu.

---

## <a name="protocol-settings-https"></a>Nastavení protokolu (HTTPS)

Vazby zabezpečeného protokolu umožňují zadat certifikát, který se má použít při reagování na požadavky přes protokol HTTPS. Vazba vyžaduje platný privátní certifikát ( *. pfx*), který byl vydán pro konkrétní název hostitele. Další informace najdete v tématu [kurz: vytvoření vazby existujícího vlastního certifikátu SSL k Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).

## <a name="transform-webconfig"></a>Transformace souboru web.config

Pokud potřebujete transformovat *Web. config* při publikování (například nastavit proměnné prostředí na základě konfigurace, profilu nebo prostředí), přečtěte si téma <xref:host-and-deploy/iis/transform-webconfig>.

## <a name="additional-resources"></a>Další zdroje

* [Přehled App Service](/azure/app-service/app-service-web-overview)
* [Azure App Service: nejlepší místo pro hostování aplikací .NET (video s přehledem 55-minut)](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [Azure pátek: Azure App Service diagnostické prostředí a řešení potíží (12 minut video)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [Přehled diagnostiky Azure App Service](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

Azure App Service na Windows serveru používá [Internetová informační služba (IIS)](https://www.iis.net/). Následující témata se týkají základní technologie IIS:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [Windows Server – obsah správce IT pro aktuální a předchozí verze](/windows-server/windows-server-versions)

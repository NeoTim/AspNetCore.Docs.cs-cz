---
title: Nasazení aplikace ASP.NET Core do Azure App Service
author: guardrex
description: Tento článek obsahuje odkazy na hostiteli Azure a nasazení prostředků.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/26/2019
uid: host-and-deploy/azure-apps/index
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a>Nasazení aplikace ASP.NET Core do Azure App Service

[Azure App Service](https://azure.microsoft.com/services/app-service/) je [Microsoft cloud computingu platformová služba](https://azure.microsoft.com/) pro hostování webových aplikací, včetně ASP.NET Core.

## <a name="useful-resources"></a>Užitečné zdroje

Azure [dokumentace k Web Apps](/azure/app-service/) je domovská stránka pro aplikace Azure dokumentace, kurzy, ukázky, návody a další prostředky. Jsou dva důležité kurzy, které se vztahují k hostování aplikací ASP.NET Core:

[Vytvoření webové aplikace ASP.NET Core v Azure](/azure/app-service/app-service-web-get-started-dotnet)  
Vytvoření a nasazení webové aplikace ASP.NET Core do služby Azure App Service ve Windows pomocí sady Visual Studio.

[Vytvoření aplikace ASP.NET Core ve službě App Service v Linuxu](/azure/app-service/containers/quickstart-dotnetcore)  
Vytvoření a nasazení webové aplikace ASP.NET Core do služby Azure App Service v Linuxu pomocí příkazového řádku.

Jsou k dispozici v dokumentaci k ASP.NET Core v následujících článcích:

<xref:tutorials/publish-to-azure-webapp-using-vs>  
Zjistěte, jak publikovat aplikace ASP.NET Core do služby Azure App Service pomocí sady Visual Studio.

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
Zjistěte, jak vytvořit webovou aplikaci ASP.NET Core pomocí sady Visual Studio a nasaďte ji do služby Azure App Service pro průběžné nasazování pomocí Gitu.

[Vytvořit svůj první kanál s kanály Azure](/azure/devops/pipelines/get-started-yaml)  
Nastavení sestavení CI pro aplikace ASP.NET Core a potom vytvořte vydání průběžné nasazování do služby Azure App Service.

[Azure sandboxu webové aplikace](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
Objevte omezení spuštění modulu runtime služby Azure App Service vynucovaných příslušnou platformou aplikace Azure.

## <a name="application-configuration"></a>Konfigurace aplikace

### <a name="platform"></a>Platforma

::: moniker range=">= aspnetcore-2.2"

Moduly runtime pro (x64) 64bitovým kompilátorem a 32bitový (x 86) aplikace jsou k dispozici ve službě Azure App Service. [.NET Core SDK](/dotnet/core/sdk) k dispozici ve službě App Service je 32-bit, ale můžete nasadit pomocí aplikace 64-bit [Kudu](https://github.com/projectkudu/kudu/wiki) konzole nebo prostřednictvím [publikování MSDeploy pomocí sady Visual Studio profilu nebo rozhraní příkazového řádku](xref:host-and-deploy/visual-studio-publish-profiles).

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Pro aplikace s nativní závislosti modulů runtime pro aplikace 32bitový (x 86) jsou k dispozici ve službě Azure App Service. [.NET Core SDK](/dotnet/core/sdk) k dispozici ve službě App Service je 32-bit.

::: moniker-end

### <a name="packages"></a>Balíčky

Následující balíčky NuGet a zajistit tak funkce automatického protokolování u aplikací nasazených do služby Azure App Service patří:

* [Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) používá [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) k zajištění integrace světla nahoru ASP.NET Core pomocí služby Azure App Service. Poskytované funkce přidání protokolování `Microsoft.AspNetCore.AzureAppServicesIntegration` balíčku.
* [Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) spustí [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) Přidání poskytovatelů protokolování diagnostiky služby Azure App Service v `Microsoft.Extensions.Logging.AzureAppServices` balíčku.
* [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) poskytuje implementace protokolovací nástroj pro podporu funkcí streamování protokolů a protokoly diagnostiky Azure App Service.

Nejsou k dispozici z předchozích balíčcích [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app). Aplikace, které cílí na rozhraní .NET Framework nebo odkaz `Microsoft.AspNetCore.App` Microsoft.aspnetcore.all musí explicitně odkazovat na jednotlivé balíčky v souboru projektu vaší aplikace.

## <a name="override-app-configuration-using-the-azure-portal"></a>Přepsat konfiguraci aplikace pomocí webu Azure Portal

Nastavení aplikace na webu Azure Portal umožní nastavit proměnné prostředí pro aplikaci. Mohou být spotřebovány proměnné prostředí [poskytovatele konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).

Při vytvoření nebo úpravě na webu Azure Portal nastavení aplikace a **Uložit** se vybere tlačítko, restartování aplikace Azure. Proměnná prostředí je k dispozici pro aplikace, po restartování služby.

Pokud aplikace používá [webového hostitele](xref:fundamentals/host/web-host) a sestavení hostitele pomocí [WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder), použít proměnné prostředí, které konfigurace hostitele `ASPNETCORE_` předponu. Další informace najdete v tématu <xref:fundamentals/host/web-host> a [poskytovatele konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).

Pokud aplikace používá [obecný hostitele](xref:fundamentals/host/generic-host), proměnné prostředí nejsou načtené do konfigurace vaší aplikace ve výchozím nastavení a poskytovatel konfigurace je třeba přidat pomocí vývojáře. Vývojář Určuje předponu proměnné prostředí po přidání zprostředkovatele konfigurace. Další informace najdete v tématu <xref:fundamentals/host/generic-host> a [poskytovatele konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy server a scénáře pro nástroj pro vyrovnávání zatížení

Middleware pro integraci služby IIS, který nakonfiguruje předané Middleware záhlaví a že modul ASP.NET Core jsou konfigurovány pro předávání schématu (HTTP/HTTPS) a Vzdálená IP adresa původu žádosti. Další konfigurace může být nezbytný pro aplikací hostovaných za službou další proxy servery a nástroje pro vyrovnávání zatížení. Další informace najdete v tématu [konfigurace ASP.NET Core práci se servery proxy a nástroje pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).

## <a name="monitoring-and-logging"></a>Monitorování a protokolování

Aplikace ASP.NET Core nasadily do App Service automaticky přijímat rozšíření služby App Service, **ASP.NET Core protokolování rozšíření**. Toto rozšíření aktivuje Azure protokolování.

Sledování, protokolování a informace o odstraňování potíží najdete v následujících článcích:

[Postupy: Monitorování aplikací ve službě Azure App Service](/azure/app-service/web-sites-monitor)  
Zjistěte, jak kontrolovat kvóty a metriky pro aplikace a plány služby App Service.

[Povolení protokolování diagnostiky pro webové aplikace ve službě Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log)  
Zjistěte, jak povolit a přístup k protokolování diagnostiky pro stavové kódy HTTP, neúspěšných požadavků a aktivity webového serveru.

<xref:fundamentals/error-handling>  
Seznamte se s běžné přístupy k zpracování chyb v aplikacích ASP.NET Core.

<xref:host-and-deploy/azure-apps/troubleshoot>  
Zjistěte, jak diagnostikovat problémy s nasazením služby Azure App Service s aplikací ASP.NET Core.

<xref:host-and-deploy/azure-iis-errors-reference>  
V tématu běžné chyby konfigurace nasazení pro aplikace hostované pomocí Azure App Service/IIS s Poradce při potížích.

## <a name="data-protection-key-ring-and-deployment-slots"></a>Aktualizační kanál klíč ochrany dat a sloty nasazení

[Klíče ochrany dat](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) jsou zachované *%HOME%\ASP.NET\DataProtection-Keys* složky. Tato složka je zajištěná síťového úložiště a synchronizují ve všech počítačích, který je hostitelem aplikace. Klíče nejsou chráněné v klidovém stavu. Tato složka poskytuje kanál klíč na všechny instance aplikace ve slotu jedno nasazení. Samostatné nasazovacích slotů, jako je například přípravným a produkčním prostředím, Nesdílejte klíč kanál.

Po záměně mezi sloty nasazení, jakéhokoli systému pomocí ochrany dat nebude možné dešifrovat uloženými daty pracujete pomocí aktualizační kanál, který klíč uvnitř předchozí slot. Middlewaru souboru Cookie. technologie ASP.NET používá ochranu dat k ochraně souborů cookie. To vede k uživatelům se odhlásili z aplikace, která používá standardní middlewaru souboru Cookie. technologie ASP.NET. Pro kanál klíč slotu nezávislé na řešení využívají, jako poskytovatele vnější prstenec klíč:

* Azure Blob Storage
* Azure Key Vault
* Úložiště SQL
* Redis cache

Další informace naleznete v tématu <xref:security/data-protection/implementation/key-storage-providers>.

## <a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>Nasazení ve verzi preview ASP.NET Core do služby Azure App Service

Použijte jeden z následujících přístupů:

* [Nainstalujte rozšíření náhledu webu](#install-the-preview-site-extension).
* [Nasazení samostatné aplikace](#deploy-the-app-self-contained).
* [Pomocí webové aplikace Docker pro kontejnery](#use-docker-with-web-apps-for-containers).

### <a name="install-the-preview-site-extension"></a>Instalace rozšíření webu ve verzi preview

Pokud dojde k potížím s pomocí rozšíření webu ve verzi preview, otevřete problém na [Githubu](https://github.com/aspnet/azureintegration/issues/new).

1. Na webu Azure Portal přejděte do služby App Service.
1. Vyberte webovou aplikaci.
1. Typ "ex" do vyhledávacího pole filtrovat "Rozšíření" nebo se posouvají dolů seznam nástrojů pro správu.
1. Vyberte **rozšíření**.
1. Vyberte **Přidat**.
1. Vyberte **ASP.NET Core {X.Y} ({x64 | x86}) Runtime** rozšíření ze seznamu, kde `{X.Y}` je verze preview ASP.NET Core a `{x64|x86}` Určuje platformu.
1. Vyberte **OK** přijměte právní podmínky.
1. Vyberte **OK** nainstalovat rozšíření.

Po dokončení operace, je nainstalovaná nejnovější verze preview .NET Core. Ověření instalace:

1. Vyberte **Rozšířené nástroje**.
1. Vyberte **Přejít** v **Rozšířené nástroje**.
1. Vyberte **konzolou pro ladění** > **Powershellu** položky nabídky.
1. Na příkazovém řádku Powershellu spusťte následující příkaz. Nahraďte verze modulu runtime ASP.NET Core pro `{X.Y}` a platforma pro `{PLATFORM}` v příkazu:

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```
   Příkaz vrátí `True` při x64 je nainstalován modul runtime ve verzi preview.

> [!NOTE]
> Architektura platformy (x86/x64) aplikace služby App Services je nastavena v nastavení aplikace na webu Azure Portal pro aplikace, které jsou hostované na výpočetní prostředky řady A-series nebo lepší hostování vrstvy. Pokud aplikace běží v režimu v procesu a architektura platformy je nakonfigurovaný pro (x64) 64-bit, používá modul ASP.NET Core runtime 64bitová verze preview, pokud jsou k dispozici. Nainstalujte **{X.Y} (x64) ASP.NET Core Runtime** rozšíření.
>
> Po instalaci x64 ve verzi preview modulu runtime, spusťte následující příkaz v příkazovém okně prostředí PowerShell Kudu k ověření instalace. Nahraďte verze modulu runtime ASP.NET Core pro `{X.Y}` v příkazu:
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
> Příkaz vrátí `True` při x64 je nainstalován modul runtime ve verzi preview.

> [!NOTE]
> **Rozšíření ASP.NET Core** povoluje další funkce pro ASP.NET Core v Azure App Services, jako například povolení protokolování v Azure. Rozšíření se nainstaluje automaticky při nasazení ze sady Visual Studio. Pokud rozšíření není nainstalovaný, nainstalujte ho pro aplikaci.

**Použití rozšíření webu ve verzi preview pomocí šablony ARM**

Pokud šablonu ARM se používá k vytvoření a nasazení aplikace, `siteextensions` typ prostředku je možné přidat rozšíření webu do webové aplikace. Příklad:

[!code-json[](index/sample/arm.json?highlight=2)]

### <a name="deploy-the-app-self-contained"></a>Nasazení samostatné aplikace

A [samostatné nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) , která se zaměřuje náhled runtime vyvolá modul runtime náhled v nasazení.

Při nasazování samostatnou aplikaci:

* Na webu v Azure App Service nevyžaduje [náhled rozšíření webu](#install-the-preview-site-extension).
* Aplikace musí být zveřejněno po použití jiné metody než při publikování [framework závislé nasazení (chyba)](/dotnet/core/deploying#framework-dependent-deployments-fdd).

#### <a name="publish-from-visual-studio"></a>Publikování z aplikace Visual Studio

1. Vyberte **sestavení** > **{název_aplikace} publikovat** z panelu nástrojů sady Visual Studio.
1. V **vyberte cíl publikování** dialogového okna, ujistěte se, že **služby App Service** zaškrtnuto.
1. Vyberte **Upřesnit**. **Publikovat** otevře se dialogové okno.
1. V **publikovat** dialogové okno:
   * Ujistěte se, že **vydání** vybrané konfigurace.
   * Otevřít **režim nasazení** rozevíracího seznamu a vyberte **samostatná**.
   * Vyberte cílový modul runtime z **cílový modul Runtime** rozevíracího seznamu. Výchozí hodnota je `win-x86`.
   * Pokud je potřeba odebrat další soubory při nasazování, otevřete **možností publikování souboru** a zaškrtněte políčko, chcete-li odebrat další soubory v cílovém umístění.
   * Vyberte **Uložit**.
1. Vytvoření nového webu nebo aktualizovat existující lokalitu podle zbývajících výzev Průvodce publikováním.

#### <a name="publish-using-command-line-interface-cli-tools"></a>Publikování pomocí nástrojů rozhraní příkazového řádku (CLI)

1. V souboru projektu, zadejte jeden nebo více [Runtime identifikátorů (RID)](/dotnet/core/rid-catalog). Použít `<RuntimeIdentifier>` (singulární) pro jeden identifikátorů RID, nebo použijte `<RuntimeIdentifiers>` zadejte středníkem oddělený seznam identifikátorů RID (Plurální). V následujícím příkladu `win-x86` zadaný identifikátorů RID:

   ```xml
   <PropertyGroup>
     <TargetFramework>netcoreapp2.1</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```
1. Z příkazového prostředí, publikujte aplikaci v rámci konfigurace verze modulu runtime hostitele s [dotnet publikovat](/dotnet/core/tools/dotnet-publish) příkazu. V následujícím příkladu je aplikace publikována pro `win-x86` identifikátorů RID. Identifikátor RID zadaný pro `--runtime` ve musí být Zadaná možnost `<RuntimeIdentifier>` (nebo `<RuntimeIdentifiers>`) vlastnost v souboru projektu.

   ```console
   dotnet publish --configuration Release --runtime win-x86
   ```
1. Přesunout obsah *bin/Release / {TARGET FRAMEWORK} / {IDENTIFIKÁTOR modulu RUNTIME} / publish* adresář k webu ve službě App Service.

### <a name="use-docker-with-web-apps-for-containers"></a>Použití Docker pro kontejnery s Web Apps

[Docker Hubu](https://hub.docker.com/r/microsoft/aspnetcore/) obsahuje nejnovější Image Dockeru ve verzi preview. Image můžete použít jako základní image. Použít bitovou kopii a nasazení do Web Apps for Containers normálně.

## <a name="protocol-settings-https"></a>Nastavení protokolu (HTTPS)

Zabezpečený protokol vazby umožňují že zadat certifikát má použít při reakci na požadavky přes protokol HTTPS. Vazba vyžaduje platný privátní certifikát (*.pfx*) vydaný pro konkrétní název hostitele. Další informace najdete v tématu [kurzu: Vytvoření vazby existujícího vlastního certifikátu SSL k Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).

## <a name="transform-webconfig"></a>Transformace souboru web.config

Pokud potřebujete transformovat *web.config* při publikování (třeba nastavit proměnné prostředí na základě konfigurace, profil nebo prostředí), viz <xref:host-and-deploy/iis/transform-webconfig>.

## <a name="additional-resources"></a>Další zdroje

* [Přehled Web Apps (5minutové video s přehledem)](/azure/app-service/app-service-web-overview)
* [Azure App Service: Nejlepší umístit do hostitele aplikací .NET (55 minutu video s přehledem)](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [Azure Friday: Azure App Service diagnostiku a řešení potíží s prostředí (12 pětiminutové video)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [Přehled diagnostiky služby Azure App Service](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

Služba Azure App Service v systému Windows Server používá [Internetové informační služby (IIS)](https://www.iis.net/). Následující témata se týkají základní technologie služby IIS:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [V knihovně Microsoft TechNet: Windows Server](/windows-server/windows-server-versions)

---
title: Hostitele ASP.NET Core ve Windows se službou IIS
author: guardrex
description: Zjistěte, jak hostovat aplikace ASP.NET Core na Windows serveru Internetové informační služby (IIS).
ms.author: riande
ms.custom: mvc
ms.date: 09/21/2018
uid: host-and-deploy/iis/index
ms.openlocfilehash: 12075f68dd828680f6bfbd46ea22ebd7bbe52dc7
ms.sourcegitcommit: 4bdf7703aed86ebd56b9b4bae9ad5700002af32d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2018
ms.locfileid: "49326014"
---
# <a name="host-aspnet-core-on-windows-with-iis"></a>Hostitele ASP.NET Core ve Windows se službou IIS

Podle [Luke Latham](https://github.com/guardrex)

## <a name="supported-operating-systems"></a>Podporované operační systémy

Následující operační systémy se podporují:

* Windows 7 nebo novější
* Windows Server 2008 R2 nebo novější

[HTTP.sys server](xref:fundamentals/servers/httpsys) (dříve se označovaly jako [WebListener](xref:fundamentals/servers/weblistener)) nebude fungovat v konfigurace reverzního proxy serveru se službou IIS. Použití [Kestrel server](xref:fundamentals/servers/kestrel).

Informace o hostování v Azure najdete v tématu <xref:host-and-deploy/azure-apps/index>.

## <a name="http2-support"></a>Podpora HTTP/2

::: moniker range=">= aspnetcore-2.2"

[HTTP/2](https://httpwg.org/specs/rfc7540.html) se podporuje s ASP.NET Core v následujících scénářích nasazení služby IIS:

* V procesu
  * Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.
  * Cílová architektura: .NET Core 2.2 nebo vyšší
  * Protokol TLS 1.2 nebo vyšší připojení
* Mimo proces
  * Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.
  * Připojení k serveru edge veřejně přístupných pomocí protokolu HTTP/2, ale reverzní proxy server připojení k [Kestrel server](xref:fundamentals/servers/kestrel) používá HTTP/1.1.
  * Cílová architektura: není k dispozici pro nasazení mimo proces, protože připojení HTTP/2 je zpracována zcela službou IIS.
  * Protokol TLS 1.2 nebo vyšší připojení

V procesu nasazení po vytvoření připojení k protokolu HTTP/2 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/2`. Mimo proces nasazení po vytvoření připojení k protokolu HTTP/2 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/1.1`.

Další informace o modelech hostování a mimo proces, najdete v článku <xref:fundamentals/servers/aspnet-core-module> tématu a <xref:host-and-deploy/aspnet-core-module>.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[HTTP/2](https://httpwg.org/specs/rfc7540.html) se podporuje pro nasazení mimo proces, které splňují základní požadavky na následující:

* Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.
* Připojení k serveru edge veřejně přístupných pomocí protokolu HTTP/2, ale reverzní proxy server připojení k [Kestrel server](xref:fundamentals/servers/kestrel) používá HTTP/1.1.
* Cílová architektura: není k dispozici pro nasazení mimo proces, protože připojení HTTP/2 je zpracována zcela službou IIS.
* Protokol TLS 1.2 nebo vyšší připojení

Pokud se připojení HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/1.1`.

::: moniker-end

HTTP/2 je standardně povolená. Připojení vrátit zpět k protokolu HTTP/1.1, pokud nedojde k připojení k protokolu HTTP/2. Další informace o konfiguraci protokolu HTTP/2 u nasazení ve službě IIS najdete v části [HTTP/2 ve službě IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="application-configuration"></a>Konfigurace aplikace

### <a name="enable-the-iisintegration-components"></a>Povolit IISIntegration součásti

::: moniker range=">= aspnetcore-2.2"

**Model hostingu v procesu**

Typické *Program.cs* volání <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> zahájíte nastavení hostitele. `CreateDefaultBuilder` volání `UseIIS` metoda pro spuštění [CoreCLR](/dotnet/standard/glossary#coreclr) a hostování aplikace v rámci pracovní proces služby IIS (`w3wp.exe`). Testy výkonu zjistí, že hostování .NET Core aplikace v procesu poskytuje vyšší propustnost žádostí ve srovnání s žádostí aplikace na více instancí procesu a využívání proxy serverů k hostování [Kestrel](xref:fundamentals/servers/kestrel).

**Model hostingu mimo proces**

Typické *Program.cs* volání <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> zahájíte nastavení hostitele. Pro hostování mimo proces se službou IIS, `CreateDefaultBuilder` nakonfiguruje [Kestrel](xref:fundamentals/servers/kestrel) jako integrace webového serveru a umožňuje služby IIS tím, že nakonfigurujete základní cesty a port pro [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module):

```csharp
public static IWebHost BuildWebHost(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

Modul ASP.NET Core generuje dynamický port přiřadit back endový proces. `CreateDefaultBuilder` volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> metodu, která převezme dynamický port a nakonfiguruje Kestrel k naslouchání `http://localhost:{dynamicPort}/`. Tím se přepíše ostatní konfigurace adresy URL, jako je například volání `UseUrls` nebo [Kestrel pro naslouchání API](xref:fundamentals/servers/kestrel#endpoint-configuration). Proto volání `UseUrls` nebo jeho Kestrel `Listen` rozhraní API nejsou povinné, když pomocí modulu. Pokud `UseUrls` nebo `Listen` nazývá Kestrel naslouchá jenom na porty zadána při spuštění aplikaci bez služby IIS.

Další informace o modelech hostování a mimo proces, najdete v článku <xref:fundamentals/servers/aspnet-core-module> tématu a <xref:host-and-deploy/aspnet-core-module>.

::: moniker-end

::: moniker range="= aspnetcore-2.0 || aspnetcore-2.1"

Typické *Program.cs* volání <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> zahájíte nastavení hostitele. `CreateDefaultBuilder` nakonfiguruje [Kestrel](xref:fundamentals/servers/kestrel) jako integrace webového serveru a umožňuje služby IIS tím, že nakonfigurujete základní cesty a port pro [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module):

```csharp
public static IWebHost BuildWebHost(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

Modul ASP.NET Core generuje dynamický port přiřadit back endový proces. `CreateDefaultBuilder` volání [UseIISIntegration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions.useiisintegration) metodu, která převezme dynamický port a nakonfiguruje Kestrel k naslouchání `http://localhost:{dynamicPort}/`. Tím se přepíše ostatní konfigurace adresy URL, jako je například volání `UseUrls` nebo [Kestrel pro naslouchání API](xref:fundamentals/servers/kestrel#endpoint-configuration). Proto volání `UseUrls` nebo jeho Kestrel `Listen` rozhraní API nejsou povinné, když pomocí modulu. Pokud `UseUrls` nebo `Listen` nazývá Kestrel naslouchá na portu zadána při spuštění aplikaci bez služby IIS.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Zahrnout závislost [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/) balíčku v závislosti aplikaci. Používat middleware pro integraci služby IIS tak, že přidáte [UseIISIntegration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions.useiisintegration) metodu rozšíření k [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder):

```csharp
var host = new WebHostBuilder()
    .UseKestrel()
    .UseIISIntegration()
    ...
```

Obě [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) a [UseIISIntegration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions.useiisintegration) jsou povinné. Volání kódu `UseIISIntegration` nemá vliv na přenositelnost kódu. Pokud aplikace není spuštěna za služby IIS (například spuštění aplikace přímo na Kestrel), `UseIISIntegration` nepracuje.

Modul ASP.NET Core generuje dynamický port přiřadit back endový proces. `UseIISIntegration` Metoda vybere dynamický port a nakonfiguruje Kestrel k naslouchání `http://locahost:{dynamicPort}/`. Tím se přepíše ostatní konfigurace adresy URL, jako je například volání `UseUrls`. Proto volání `UseUrls` není povinné, při použití modulu. Pokud `UseUrls` nazývá Kestrel naslouchá na portu zadána při spuštění aplikaci bez služby IIS.

Pokud `UseUrls` je volána v aplikaci ASP.NET Core 1.0, volání **před** volání `UseIISIntegration` tak, aby port nakonfigurovaný modul není přepsán. Toto pořadí volání není požadován spolu s ASP.NET Core 1.1, protože modul nastavení přepíše `UseUrls`.

::: moniker-end

Další informace o hostování najdete v tématu [hostitele v ASP.NET Core](xref:fundamentals/host/index).

### <a name="iis-options"></a>Možnosti služby IIS

Ke konfiguraci možností služby IIS, zahrnují konfiguraci služby pro [IISOptions](/dotnet/api/microsoft.aspnetcore.builder.iisoptions) v [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices). V následujícím příkladu, předávání klientské certifikáty k aplikaci pro naplnění `HttpContext.Connection.ClientCertificate` zakázaná:

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Možnost                         | Výchozí | Nastavení |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Pokud `true`, nastaví Middleware pro integraci služby IIS `HttpContext.User` ověřována [ověřování Windows](xref:security/authentication/windowsauth). Pokud `false`, middleware pouze poskytuje identitu `HttpContext.User` a reaguje na problémy při explicitním požadavku `AuthenticationScheme`. Musí být povoleno ověřování Windows ve službě IIS pro `AutomaticAuthentication` na funkci. Další informace najdete v tématu [ověřování Windows](xref:security/authentication/windowsauth) tématu. |
| `AuthenticationDisplayName`    | `null`  | Nastaví zobrazovaný název, který se uživatelům na přihlašovací stránky zobrazí. |
| `ForwardClientCertificate`     | `true`  | Pokud `true` a `MS-ASPNETCORE-CLIENTCERT` hlavička požadavku je k dispozici, `HttpContext.Connection.ClientCertificate` naplnění. |

### <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy server a scénáře pro nástroj pro vyrovnávání zatížení

Middleware pro integraci služby IIS, který nakonfiguruje předané Middleware záhlaví a že modul ASP.NET Core jsou konfigurovány pro předávání schématu (HTTP/HTTPS) a Vzdálená IP adresa původu žádosti. Další konfigurace může být nezbytný pro aplikací hostovaných za službou další proxy servery a nástroje pro vyrovnávání zatížení. Další informace najdete v tématu [konfigurace ASP.NET Core práci se servery proxy a nástroje pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).

### <a name="webconfig-file"></a>soubor Web.config

*Web.config* soubor nastaví [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module). Vytváření, transformace a publikování *web.config* soubor se zpracovává souborem cíl nástroje MSBuild (`_TransformWebConfig`) při publikování projektu. Tento cíl je k dispozici v cílech Web SDK (`Microsoft.NET.Sdk.Web`). V horní části souboru projektu je sada SDK:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Pokud *web.config* soubor není k dispozici v projektu, soubor, vytvoří se správnými *processPath* a *argumenty* ke konfiguraci [ASP.NET Core Modul](xref:fundamentals/servers/aspnet-core-module) a přesunout do [publikovaný výstup](xref:host-and-deploy/directory-structure).

Pokud *web.config* soubor je k dispozici v projektu, soubor se transformuje se správnými *processPath* a *argumenty* nakonfigurovat modul ASP.NET Core a přesunout do publikovaný výstup. Transformace nezmění nastavení konfigurace služby IIS v souboru.

*Web.config* soubor může poskytovat další nastavení konfigurace služby IIS, která řídí aktivní moduly služby IIS. Informace o službě IIS modulů, které dokáže zpracovávat požadavky s aplikací ASP.NET Core, najdete v článku [moduly IIS](xref:host-and-deploy/iis/modules) tématu.

Zabránit transformace na sadu Web SDK *web.config* souboru, použijte  **\<IsTransformWebConfigDisabled >** vlastnost v souboru projektu:

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Při zakazování Web SDK z transformaci souboru *processPath* a *argumenty* by měl být ručně nastavit vývojářem. Další informace najdete v tématu [odkaz Konfigurace modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

### <a name="webconfig-file-location"></a>Umístění souboru Web.config

Chcete-li vytvořit reverzního proxy serveru mezi službou IIS a Kestrel server *web.config* soubor musí být k dispozici v obsahu kořenové cestě (obvykle aplikace základní cesta) nasazené aplikace. Jedná se o stejné umístění jako fyzická cesta webu služby IIS k dispozici. *Web.config* soubor je vyžadován v kořenovém adresáři aplikace, aby se daly publikovat víc aplikací pomocí nasazení webu.

Citlivé soubory existují na fyzická cesta aplikace, jako například  *\<sestavení >. runtimeconfig.json*,  *\<sestavení > .xml* (komentáře dokumentace XML) a  *\<sestavení >. deps.json*. Když *web.config* soubor je k dispozici a lokality spustí běžným způsobem, služba IIS bariéru tyto citlivé soubory, pokud jste požádali. Pokud *web.config* soubor chybí, nesprávně pojmenované, nebo nelze konfigurovat lokalitu pro normální spuštění, služba IIS může poskytovat citlivé soubory veřejně.

***Web.config* soubor musí být k dispozici v nasazení za všech okolností, správně s názvem a nakonfigurovat web pro normální spuštění nahoru. Nikdy odebrat *web.config* soubor z produkčního nasazení.**

## <a name="iis-configuration"></a>Konfigurace služby IIS

**Operační systémy Windows Server**

Povolit **webového serveru (IIS)** role serveru a vytvoření služby rolí.

1. Použití **přidat role a funkce** z průvodce **spravovat** nabídky nebo na odkaz v **správce serveru**. Na **role serveru** krok, zaškrtněte políčko u **webového serveru (IIS)**.

   ![V kroku výběr serveru role je vybrána role webového serveru IIS.](index/_static/server-roles-ws2016.png)

1. Po **funkce** kroku **služeb rolí** krok načte pro webový Server (IIS). Vyberte požadovaných služeb role služby IIS nebo přijměte výchozí nastavení role služby za předpokladu.

   ![Výchozí služby rolí jsou vybrané v kroku vybrat roli služby.](index/_static/role-services-ws2016.png)

   **Ověřování Windows (volitelné)**  
   Pokud chcete povolit ověřování Windows, rozbalte následující uzly: **Webový Server** > **zabezpečení**. Vyberte **ověřování Windows** funkce. Další informace najdete v tématu [ověřování Windows \<windowsAuthentication >](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) a [Windows nakonfigurovat ověřování](xref:security/authentication/windowsauth).

   **Protokoly Websocket (volitelné)**  
   Protokoly Websocket je podporována s ASP.NET Core 1.1 nebo vyšší. Pokud chcete povolit protokoly Websocket, rozbalte následující uzly: **Webový Server** > **vývoj aplikací**. Vyberte **protokol WebSocket** funkce. Další informace najdete v tématu [objekty Websocket](xref:fundamentals/websockets).

1. Pokračujte **potvrzení** krok instalace role webového serveru a služby. Po instalaci se nevyžaduje restartování serveru/IIS **webového serveru (IIS)** role.

**Operační systémy Windows**

Povolit **konzolu pro správu IIS** a **webové služby**.

1. Přejděte do **ovládací panely** > **programy** > **programy a funkce** > **zapnout Windows funkce na nebo vypnout** (levé straně obrazovky).

1. Otevřít **Internetová informační služba** uzlu. Otevřít **nástroje webové správy** uzlu.

1. Zaškrtněte políčko u **konzolu pro správu IIS**.

1. Zaškrtněte políčko u **webové služby**.

1. Přijměte výchozí funkce pro **webové služby** nebo přizpůsobení funkcí služby IIS.

   **Ověřování Windows (volitelné)**  
   Pokud chcete povolit ověřování Windows, rozbalte následující uzly: **webové služby** > **zabezpečení**. Vyberte **ověřování Windows** funkce. Další informace najdete v tématu [ověřování Windows \<windowsAuthentication >](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) a [Windows nakonfigurovat ověřování](xref:security/authentication/windowsauth).

   **Protokoly Websocket (volitelné)**  
   Protokoly Websocket je podporována s ASP.NET Core 1.1 nebo vyšší. Pokud chcete povolit protokoly Websocket, rozbalte následující uzly: **webové služby** > **funkce pro vývoj aplikací**. Vyberte **protokol WebSocket** funkce. Další informace najdete v tématu [objekty Websocket](xref:fundamentals/websockets).

1. Pokud instalace služby IIS vyžaduje restartování, restartujte systém.

![Konzola pro správu služby IIS a webové služby jsou vybrány v funkce Windows.](index/_static/windows-features-win10.png)

---

## <a name="install-the-net-core-hosting-bundle"></a>Instalace .NET Core, který je hostitelem svazku

1. Nainstalujte *sady hostování rozhraní .NET Core* v hostitelském systému. Nainstaluje sady .NET Core Runtime, knihovny .NET Core a [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module). Modul vytváří reverzní proxy server mezi službou IIS a Kestrel server. Pokud systém nemá připojení k Internetu, získejte a nainstalujte [Microsoft Visual C++ 2015 Redistributable](https://www.microsoft.com/download/details.aspx?id=53840) před instalací sady hostování rozhraní .NET Core.

   1. Přejděte [stránky pro stažení rozhraní .NET](https://www.microsoft.com/net/download/windows).
   1. V části **.NET Core**, vyberte **stáhnout .NET Core Runtime** vedle **spuštění aplikace** popisek. Spustitelný soubor instalačního programu obsahuje slovo "hostování" v názvu souboru (například *dotnet hostování-2.1.2-win.exe*).
   1. Spusťte instalační program na serveru.

   **Důležité!** Pokud před službou IIS instalovanou sadou hostování, je nutné opravit instalaci sady. Spusťte instalační program sady hostování znovu po instalaci služby IIS.

   Spusťte instalační program z příkazového řádku správce s jeden nebo více přepínačů lze řídit chování instalačního programu:

   * `OPT_NO_ANCM=1` &ndash; Instalace modulu jádra ASP.NET přeskočit.
   * `OPT_NO_RUNTIME=1` &ndash; Instalace modulu runtime .NET Core přeskočit.
   * `OPT_NO_SHAREDFX=1` &ndash; Přeskočit instalaci rozhraní ASP.NET sdílené (modul runtime technologie ASP.NET).
   * `OPT_NO_X86=1` &ndash; X86 instalace přeskočit runtimes. Tento přepínač použijte, když víte, že vám nebude hostitelem 32bitových aplikací. Pokud je pravděpodobné, že 32-bit a 64-bit aplikací budou v budoucnu umístěny, není tento přepínač a nainstalovat obě runtimes.

1. Restartování systému nebo spuštění **net stop byla /y** následovaný **net start w3svc** z příkazového řádku. Restartování služby IIS příjmem změnu systému provedené CESTU, která je proměnná prostředí, instalační služby.

   Pokud instalační služby systému Windows, který je hostitelem svazku zjistí, že služba IIS dokončení instalace vyžaduje obnovení, obnoví instalační program služby IIS. Pokud instalační program spustí resetování služby IIS, se restartují všechny fondy aplikací IIS a websites.

> [!NOTE]
> Informace o sdílenou konfiguraci aplikaci IIS najdete v tématu [modul ASP.NET Core s sdílenou konfiguraci aplikaci IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Nainstalujte nástroj nasazení webu při publikování pomocí sady Visual Studio

Při nasazování aplikací na servery s [Webdeploy](/iis/publish/using-web-deploy/introduction-to-web-deploy), nainstalujte nejnovější verzi nástroje nasazení webu na serveru. Chcete-li nainstalujte nástroj nasazení webu, použijte [instalačního programu webové platformy (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) nebo získat přímo z instalačního programu [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717). Upřednostňovanou metodou je použití instalace webové platformy. Instalace webové platformy nabízí samostatné instalace a konfigurace pro poskytovatele hostingu.

## <a name="create-the-iis-site"></a>Vytvořte web služby IIS

1. V hostitelském systému vytvořte složku obsahující soubory a složky publikované aplikace. Rozložení nasazení vaší aplikace je popsána v [adresářovou strukturu](xref:host-and-deploy/directory-structure) tématu.

1. V rámci novou složku vytvořit *protokoly* složku pro uložení protokolů stdout modul ASP.NET Core, pokud je povoleno protokolování stdout. Pokud je aplikace nasazena s *protokoly* složek v datové části, tento krok přeskočit. Pokyny o tom, jak povolit MSBuild k vytvoření *protokoly* složku automaticky, pokud je projekt je sestaven místně, najdete v článku [adresářovou strukturu](xref:host-and-deploy/directory-structure) tématu.

   > [!IMPORTANT]
   > Pouze pomocí protokolu stdout řešení potíží s chybami při spuštění aplikace. Nikdy nepoužívejte stdout protokolování pro protokolování běžné aplikace. Neexistuje žádné omezení velikosti souboru protokolu nebo počet souborů protokolů, které jsou vytvořeny. Fond aplikací musí mít oprávnění k zápisu do umístění, kde se zapisují protokoly. Všechny složky na cestu k umístění protokolu musí existovat. Další informace o protokolu stdout najdete v tématu [vytváření a přesměrování protokolu](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection). Informace o protokolování v aplikaci ASP.NET Core, najdete v článku [protokolování](xref:fundamentals/logging/index) tématu.

1. V **Správce služby IIS**, otevřete uzel serveru v **připojení** panelu. Klikněte pravým tlačítkem myši **lokality** složky. Vyberte **přidat web** z kontextové nabídky.

1. Zadejte **název lokality** a nastavit **fyzická cesta** do složky pro nasazení aplikace. Zadejte **vazby** konfigurace a vytvořit na webu výběrem **OK**:

   ![Zadejte název lokality, fyzickou cestu a název hostitele v kroku přidat web.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > Vazby nejvyšší úrovně zástupný znak (`http://*:80/` a `http://+:80`) by měl **není** použít. Vazby nejvyšší úrovně zástupný znak můžete otevřít aplikaci tak, aby slabá místa zabezpečení. To platí pro silné a slabé zástupné znaky. Používejte explicitní hostitele názvy místo zástupných znaků. Vazby zástupný znak subdoménu (například `*.mysub.com`) nemá toto bezpečnostní riziko, pokud celé nadřazené domény (nikoli `*.com`, což je ohrožené). Zobrazit [rfc7230 části-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) Další informace.

1. V uzlu server, vyberte **fondy aplikací**.

1. Klikněte pravým tlačítkem na fond aplikací webu a vyberte **základní nastavení** z kontextové nabídky.

1. V **upravit fond aplikací** okno, nastaveno **verze .NET CLR** k **bez spravovaného kódu**:

   ![Nastavit bez spravovaného kódu pro verze .NET CLR.](index/_static/edit-apppool-ws2016.png)

    ASP.NET Core běží v samostatném procesu a spravuje modulu runtime. ASP.NET Core nemusí spoléhat na načítání desktop CLR. Nastavení **verze .NET CLR** k **bez spravovaného kódu** je volitelný.

1. Potvrďte, že identita model procesu má příslušná oprávnění.

   Pokud výchozí identita fondu aplikací (**Model procesu** > **Identity**) se změní z **ApplicationPoolIdentity** na jinou identitu, ověřte, že nové identity má požadovaná oprávnění pro přístup ke složce aplikace, databáze a ostatní požadované prostředky. Například fond aplikací vyžaduje čtení a zápisu do složky, kde aplikace čte a zapisuje soubory.

**Konfigurace ověřování Windows (volitelné)**  
Další informace najdete v tématu [Windows nakonfigurovat ověřování](xref:security/authentication/windowsauth).

## <a name="deploy-the-app"></a>Nasazení aplikace

Nasazení aplikace do složky vytvořené v hostitelském systému. [Webu nasadit](/iis/publish/using-web-deploy/introduction-to-web-deploy) je doporučené mechanismus pro nasazení.

### <a name="web-deploy-with-visual-studio"></a>Nasazení webu pomocí sady Visual Studio

Zobrazit [profily publikování sady Visual Studio pro nasazení aplikace ASP.NET Core](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) téma a zjistěte, jak vytvořit profil publikování pro použití s nasazením webu. Pokud poskytovatel hostingu poskytuje pro vytvoření nového profilu pro publikování nebo podporu, stáhněte si svůj profil a importujte ho pomocí sady Visual Studio **publikovat** dialogového okna.

![Publikovat stránku dialogového okna](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>Nasazení webu mimo sadu Visual Studio

[Webu nasadit](/iis/publish/using-web-deploy/introduction-to-web-deploy) lze také použít mimo sadu Visual Studio z příkazového řádku. Další informace najdete v tématu [nástroje Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).

### <a name="alternatives-to-web-deploy"></a>Alternativy k webové nasazení

Použijte několik metod pro přesun aplikace k hostování systému, jako je ruční export, příkazu Xcopy, Robocopy nebo prostředí PowerShell.

Další informace o nasazení ASP.NET Core do služby IIS, najdete v článku [materiály pro nasazení pro správce služby IIS](#deployment-resources-for-iis-administrators) oddílu.

## <a name="browse-the-website"></a>Přejděte na web

![Prohlížeč Microsoft Edge načetl úvodní stránka služby IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a>Soubory uzamčené nasazení

Při spuštění aplikace jsou zamknuté soubory ve složce pro nasazení. Uzamčené soubory nemohou být přepsána během nasazení. Uvolnit uzamčené soubory v nasazení, zastavte aplikaci pomocí fondu **jeden** z následujících postupů:

* Pomocí nasazení webu a odkaz `Microsoft.NET.Sdk.Web` v souboru projektu. *App_offline.htm* soubor umístěn v kořenovém adresáři webové aplikace. Pokud soubor existuje, modul ASP.NET Core řádně ukončí aplikaci, slouží *app_offline.htm* souboru během nasazení. Další informace najdete v tématu [odkaz Konfigurace modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).
* Ručně zastavte fond aplikací ve Správci služby IIS na serveru.
* Použití Powershellu k zastavení a restartování fondu aplikací (vyžaduje PowerShell 5 nebo novější):

  ```PowerShell
  $webAppPoolName = 'APP_POOL_NAME'

  # Stop the AppPool
  if((Get-WebAppPoolState $webAppPoolName).Value -ne 'Stopped') {
      Stop-WebAppPool -Name $webAppPoolName
      while((Get-WebAppPoolState $webAppPoolName).Value -ne 'Stopped') {
          Start-Sleep -s 1
      }
      Write-Host `-AppPool Stopped
  }

  # Provide script commands here to deploy the app

  # Restart the AppPool
  if((Get-WebAppPoolState $webAppPoolName).Value -ne 'Started') {
      Start-WebAppPool -Name $webAppPoolName
      while((Get-WebAppPoolState $webAppPoolName).Value -ne 'Started') {
          Start-Sleep -s 1
      }
      Write-Host `-AppPool Started
  }
  ```

## <a name="data-protection"></a>Ochrana dat

[Ochranu dat ASP.NET Core zásobníku](xref:security/data-protection/index) používá několik ASP.NET Core [middlewares](xref:fundamentals/middleware/index), včetně middleware použitý v ověřování. I v případě, že Data Protection API nejsou volané kódem uživatele, se skriptem nasazení nebo v uživatelském kódu k vytvoření trvalé by měl nakonfigurovat ochranu dat kryptografických [úložiště klíčů](xref:security/data-protection/implementation/key-management). Pokud není nakonfigurovaná ochrana dat, jsou klíče uložené v paměti a při restartování aplikace.

Pokud kanál klíče jsou uloženy v paměti, při restartování aplikace:

* Všechny tokeny ověřování na základě souborů cookie nejsou zneplatněny. 
* Uživatelé se musí znovu přihlásit v jejich další požadavek. 
* Všechna data chráněná pomocí aktualizační kanál, který klíč můžete už nebude možné dešifrovat. To může zahrnovat [CSRF tokeny](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) a [soubory cookie v ASP.NET Core MVC TempData](xref:fundamentals/app-state#tempdata).

Chcete-li konfigurovat ochranu dat v rámci služby IIS k uchování aktualizační kanál, který klíč, použijte **jeden** z následujících postupů:

* **Vytvoření klíče registru ochrany dat**

  Používá aplikace ASP.NET Core klíče ochrany dat jsou uložené v registru, které jsou externí vzhledem k aplikacím. Pokud chcete zachovat klíče pro danou aplikaci, vytvořte klíče registru pro fond aplikací.

  Pro samostatnou, instalace služby IIS – webové farmě, [skript prostředí PowerShell AutoGenKeys.ps1 poskytování ochrany dat (2.2 technologie ASP.NET Core)](https://github.com/aspnet/DataProtection/blob/release/2.2/Provision-AutoGenKeys.ps1) lze použít pro každý fond aplikací, které jsou součástí aplikace ASP.NET Core. Tento skript vytvoří klíč registru HKLM registru, který je přístupný pouze pro účet pracovního procesu fondu aplikací aplikaci. Klíče se zašifrují neaktivní uložená data pomocí rozhraní DPAPI klíčem celý počítač.

  Ve webových farem lze nastavit aplikaci pro použití cesty UNC pro ukládání jeho data protection klíč kanál. Ve výchozím nastavení klíče ochrany dat nejsou šifrovány. Zajistěte, aby byly omezené na účet Windows, které aplikace běží pod oprávnění pro sdílené síťové složce. X X509 certifikát můžete použít k ochraně klíčů v klidovém stavu. Vezměte v úvahu mechanismus pro uživatelům umožní nahrát certifikáty: místo certifikátů do důvěryhodného certifikátu uživatele ukládat a ujistěte se, jsou k dispozici na všech počítačích, ve kterém běží aplikace uživatele. Zobrazit [Konfigurace ochrany dat ASP.NET Core](xref:security/data-protection/configuration/overview) podrobnosti.

* **Konfigurace fondu aplikací služby IIS se načíst profil uživatele**

  Toto nastavení je v **Model procesu** části **Upřesnit nastavení** pro fond aplikací. Načíst profil uživatele nastaveno `True`. To ukládá klíče v části adresář profilu uživatele a chrání je pomocí rozhraní DPAPI klíčem specifické pro uživatelský účet je používána ve fondu aplikací.

* **Systém souborů jako kanál klíč úložiště**

  Upravit kód aplikace, který [používat systém souborů jako kanál klíč úložiště](xref:security/data-protection/configuration/overview). Použijte X509 certifikátu k ochraně klíče kanál a zajistit certifikát není důvěryhodný certifikát. Pokud certifikát podepsaný svým držitelem, můžete certifikát umístěte do úložiště důvěryhodných kořenových certifikátů.

  Pokud používáte IIS ve webové farmě:

  * Použití sdílené složky, ke kterému přístup všechny počítače.
  * Nasazení x X509 certifikát pro každý počítač. Konfigurace [ochrany dat v kódu](xref:security/data-protection/configuration/overview).

* **Nastavte zásady pro celý počítač pro ochranu dat**

  Systém ochrany dat má omezenou podporu pro nastavení výchozí [celého zásad](xref:security/data-protection/configuration/machine-wide-policy) pro všechny aplikace, které používání rozhraní Data Protection API. Zobrazit [dokumentace k data protection](xref:security/data-protection/index) podrobnosti.

## <a name="sub-application-configuration"></a>Dílčí aplikace konfigurace

Dílčí aplikace přidány pod kořenové aplikace by neměl obsahovat modul ASP.NET Core jako obslužná rutina. Pokud modul je přidán jako obslužná rutina sub-aplikace *web.config* souboru, *500.19 vnitřní chyba serveru* odkazující na chybného konfiguračního souboru obdržíte, když se pokusíte o procházení podřízeným aplikacím.

Následující příklad ukazuje publikování *web.config* sub aplikace ASP.NET Core v souboru:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
      arguments=".\<assembly_name>.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Při hostování za nástrojem sub aplikace ASP.NET Core pod aplikace ASP.NET Core, explicitně odebrat zděděné obslužné rutiny v aplikaci sub *web.config* souboru:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore" />
    </handlers>
    <aspNetCore processPath="dotnet" 
      arguments=".\<assembly_name>.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Další informace o konfiguraci modul ASP.NET Core, najdete v článku [Úvod do ASP.NET Core modulu](xref:fundamentals/servers/aspnet-core-module) tématu a [odkaz Konfigurace modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

## <a name="configuration-of-iis-with-webconfig"></a>Konfigurace služby IIS pomocí souboru web.config

Konfigurace služby IIS je ovlivněno  **\<system.webServer >** část *web.config* pro tyto funkce služby IIS, které platí pro konfigurace reverzního proxy serveru. Pokud je služba IIS konfigurována na úrovni serveru na použití dynamické komprese  **\<urlCompression >** element ve vaší aplikaci *web.config* souboru ji zakázat.

Další informace najdete v tématu [odkaz Konfigurace pro \<system.webServer >](/iis/configuration/system.webServer/), [odkaz Konfigurace modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module), a [moduly IIS s ASP.NET Základní](xref:host-and-deploy/iis/modules). Chcete-li nastavit proměnné prostředí pro jednotlivé aplikace spuštěných ve fondech izolované aplikace (podporováno pro IIS 10.0 a novější), najdete v článku *AppCmd.exe příkaz* část [proměnné prostředí \< environmentVariables >](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) téma v IIS referenční dokumentaci.

## <a name="configuration-sections-of-webconfig"></a>Konfigurační oddíly souboru Web.config

Konfigurační oddíly funkce ASP.NET 4.x aplikací v *web.config* nejsou používány aplikací ASP.NET Core pro konfiguraci:

* **\<system.web>**
* **\<appSettings>**
* **\<connectionStrings >**
* **\<umístění >**

Aplikace ASP.NET Core jsou nakonfigurované pomocí jiných poskytovatelů konfigurace. Další informace najdete v tématu [konfigurace](xref:fundamentals/configuration/index).

## <a name="application-pools"></a>Fondy aplikací

Při hostování více webů na serveru, doporučujeme izoluje aplikace od sebe navzájem spuštěním každou aplikaci ve vlastním fondu aplikací. IIS **přidat web** výchozí dialogové okno pro tuto konfiguraci. Když **název lokality** je k dispozici text je automaticky převedena do **fond aplikací** textového pole. Nový fond aplikací je vytvořený pomocí názvu serveru po přidání serveru.

## <a name="application-pool-identity"></a>Identita fondu aplikací

Účet identita fondu aplikací umožňuje aplikaci běžet pod účtem jedinečný bez nutnosti vytvářet a spravovat domény nebo místní účty. IIS 8.0 nebo novější procesů pro pracovníka Správce služby IIS (WAS) vytvoří virtuální účet s názvem nového fondu aplikací a aplikace spouští fondu pracovních procesů pod tímto účtem ve výchozím nastavení. V konzole pro správu služby IIS v části **Upřesnit nastavení** pro fond aplikací, ujistěte se, že **Identity** nastaveno pro použití **ApplicationPoolIdentity**:

![Dialogové okno pokročilé nastavení fondu aplikací](index/_static/apppool-identity.png)

Proces správy služby IIS vytvoří zabezpečeného identifikátoru se název fondu aplikací v zabezpečení systému Windows. Pomocí této identity, je možné svázat prostředky. Ale tato identita není skutečný účet a nezobrazí se v konzole pro správu uživatelů Windows.

Pokud pracovní proces služby IIS vyžaduje přístup k aplikaci přes se zvýšenými oprávněními, upravte seznam řízení přístupu (ACL) pro adresář obsahující aplikaci:

1. Otevřete Průzkumníka Windows a přejděte do adresáře.

1. Klikněte pravým tlačítkem na adresář a vyberte **vlastnosti**.

1. V části **zabezpečení** kartu, vyberte **upravit** tlačítko a pak **přidat** tlačítko.

1. Vyberte **umístění** tlačítko a ujistěte se, že je vybraná systému.

1. ENTER **fond aplikací služby IIS\\< app_pool_name >** v **zadejte názvy objektů k výběru** oblasti. Vyberte **Kontrola názvů** tlačítko. Pro *DefaultAppPool* zkontrolovat názvy pomocí **IIS AppPool\DefaultAppPool**. Při **Kontrola názvů** se vybere tlačítko, hodnota **DefaultAppPool** je uveden v oblasti názvy objektu. Není možné zadat název fondu aplikací přímo do oblasti názvy objektu. Použití **fond aplikací služby IIS\\< app_pool_name >** formátovat při kontrole názvu objektu.

   ![Vyberte uživatele nebo skupiny dialogové okno pro složky aplikace: název fondu aplikací "DefaultAppPool" se připojí k "fondu aplikací služby IIS\" v oblasti názvy objektu před výběrem"Zkontrolujte názvy."](index/_static/select-users-or-groups-1.png)

1. Vyberte **OK**.

   ![Vyberte uživatele nebo skupiny dialogové okno pro složky aplikace: Po výběru "Kontrola názvů", "DefaultAppPool" se zobrazí v objektu název objektu názvy oblasti.](index/_static/select-users-or-groups-2.png)

1. Čtení &amp; provést ve výchozím nastavení by měl být udělena oprávnění. Zadejte další oprávnění podle potřeby.

Na příkazovém řádku pomocí můžete také udělit přístup **ICACLS** nástroj. Použití *DefaultAppPool* jako příklad slouží následující příkaz:

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

Další informace najdete v tématu [icacls](/windows-server/administration/windows-commands/icacls) tématu.

## <a name="deployment-resources-for-iis-administrators"></a>Materiály pro nasazení pro správce služby IIS

Další informace o službě IIS hlouběji v dokumentaci služby IIS.  
[Dokumentace ke službě IIS](/iis)

Další informace o modelech nasazení aplikace .NET Core.  
[Nasazení aplikace .NET core](/dotnet/core/deploying/)

Zjistěte, jak modul ASP.NET Core umožňuje webovému serveru Kestrel k použití jako reverzní proxy server služby IIS nebo IIS Express.  
[Modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module)

Zjistěte, jak nakonfigurovat modul ASP.NET Core pro hostování aplikací ASP.NET Core.  
[Referenční dokumentace k modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module)

Další informace o struktuře adresářů publikované aplikace ASP.NET Core.  
[Adresářová struktura](xref:host-and-deploy/directory-structure)

Zjistěte aktivní i neaktivní moduly IIS pro aplikace ASP.NET Core a jak spravovat moduly služby IIS.  
[Moduly služby IIS](xref:host-and-deploy/iis/troubleshoot)

Zjistěte, jak diagnostikovat problémy se službou IIS nasazení aplikace ASP.NET Core.  
[Řešení potíží](xref:host-and-deploy/iis/troubleshoot)

Rozlišení běžných chyb při hostování aplikací ASP.NET Core ve službě IIS.  
[Referenční informace k běžným chybám u služeb Azure App Service a IIS](xref:host-and-deploy/azure-iis-errors-reference)

## <a name="additional-resources"></a>Další zdroje

* [Úvod do ASP.NET Core](xref:index)
* [Lokality oficiální Microsoft IIS](https://www.iis.net/)
* [Technická knihovna obsahu Windows serveru](/windows-server/windows-server)
* [HTTP/2 ve službě IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis)

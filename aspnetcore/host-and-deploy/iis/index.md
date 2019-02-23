---
title: Hostitele ASP.NET Core ve Windows se službou IIS
author: guardrex
description: 'Zjistěte, jak hostovat aplikace ASP.NET Core na Windows serveru Internetové informační služby (IIS).'
ms.author: riande
ms.custom: mvc
ms.date: 02/19/2019
uid: host-and-deploy/iis/index
---
# <a name="host-aspnet-core-on-windows-with-iis"></a>Hostitele ASP.NET Core ve Windows se službou IIS

Podle [Luke Latham](https://github.com/guardrex)

[Instalace .NET Core, který je hostitelem svazku](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a>Podporované operační systémy

Následující operační systémy se podporují:

* Windows 7 nebo novější
* Windows Server 2008 R2 nebo novější

[HTTP.sys server](xref:fundamentals/servers/httpsys) (dříve nazývané WebListener) nebude fungovat v konfigurace reverzního proxy serveru se službou IIS. Použití [Kestrel server](xref:fundamentals/servers/kestrel).

Informace o hostování v Azure najdete v tématu <xref:host-and-deploy/azure-apps/index>.

## <a name="supported-platforms"></a>Podporované platformy

Aplikace publikovaná (x86) 32bitová verze a nasazení 64bitovou (x 64) jsou podporované. Nasazení aplikace 32-bit, není-li aplikace:

* Vyžaduje větší paměť virtuální adresní prostor k dispozici pro 64bitové aplikace.
* Vyžaduje větší velikost zásobníku služby IIS.
* Má závislosti nativní 64bitové.

## <a name="application-configuration"></a>Konfigurace aplikace

### <a name="enable-the-iisintegration-components"></a>Povolit IISIntegration součásti

::: moniker range=">= aspnetcore-2.1"

Typické *Program.cs* volání <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> zahájíte nastavení hostitele:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Typické *Program.cs* volání <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> zahájíte nastavení hostitele:

```csharp
public static IWebHost BuildWebHost(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

**Model hostingu v procesu**

`CreateDefaultBuilder` volání `UseIIS` metoda pro spuštění [CoreCLR](/dotnet/standard/glossary#coreclr) a hostování aplikace v rámci pracovní proces služby IIS (*w3wp.exe* nebo *iisexpress.exe*). Testy výkonu zjistí, že hostování .NET Core aplikace v procesu poskytuje výrazně vyšší propustnost žádostí, které jsou ve srovnání s žádostí aplikace na více instancí procesu a využívání proxy serverů k hostování [Kestrel](xref:fundamentals/servers/kestrel) serveru.

Model hostingu v procesu není podporována pro aplikace ASP.NET Core, které jsou cíleny rozhraní .NET Framework.

**Model hostingu mimo proces**

Pro hostování mimo proces se službou IIS, `CreateDefaultBuilder` nakonfiguruje [Kestrel](xref:fundamentals/servers/kestrel) server jako webový server a umožňuje integraci služby IIS tím, že nakonfigurujete základní cesty a port [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

Modul ASP.NET Core generuje dynamický port přiřadit k procesu back-endu. `CreateDefaultBuilder` volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> metody. `UseIISIntegration` Nakonfiguruje Kestrel k naslouchání na dynamický port na IP adresu místního hostitele (`127.0.0.1`). Pokud dynamický port je 1234, Kestrel naslouchá na `127.0.0.1:1234`. Tato konfigurace nahrazuje jiné konfigurace adresy URL poskytnuté:

* `UseUrls`
* [Rozhraní API od kestrel naslouchání](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Konfigurace](xref:fundamentals/configuration/index) (nebo [možnost příkazového řádku--adresy URL](xref:fundamentals/host/web-host#override-configuration))

Volání `UseUrls` nebo jeho Kestrel `Listen` rozhraní API nejsou povinné, když pomocí modulu. Pokud `UseUrls` nebo `Listen` nazývá Kestrel naslouchá na porty určené pouze při spuštěné aplikaci bez služby IIS.

Další informace o modelech hostování a mimo proces, naleznete v tématu [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) a [odkaz Konfigurace modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

::: moniker-end

::: moniker range="= aspnetcore-2.1"

`CreateDefaultBuilder` nakonfiguruje [Kestrel](xref:fundamentals/servers/kestrel) server jako webový server a umožňuje integraci služby IIS tím, že nakonfigurujete základní cesty a port [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

Modul ASP.NET Core generuje dynamický port přiřadit k procesu back-endu. `CreateDefaultBuilder` volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> metody. `UseIISIntegration` Nakonfiguruje Kestrel k naslouchání na dynamický port na IP adresu místního hostitele (`127.0.0.1`). Pokud dynamický port je 1234, Kestrel naslouchá na `127.0.0.1:1234`. Tato konfigurace nahrazuje jiné konfigurace adresy URL poskytnuté:

* `UseUrls`
* [Rozhraní API od kestrel naslouchání](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Konfigurace](xref:fundamentals/configuration/index) (nebo [možnost příkazového řádku--adresy URL](xref:fundamentals/host/web-host#override-configuration))

Volání `UseUrls` nebo jeho Kestrel `Listen` rozhraní API nejsou povinné, když pomocí modulu. Pokud `UseUrls` nebo `Listen` nazývá Kestrel naslouchá na portu zadat jenom při spuštěné aplikaci bez služby IIS.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

`CreateDefaultBuilder` nakonfiguruje [Kestrel](xref:fundamentals/servers/kestrel) server jako webový server a umožňuje integraci služby IIS tím, že nakonfigurujete základní cesty a port [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

Modul ASP.NET Core generuje dynamický port přiřadit k procesu back-endu. `CreateDefaultBuilder` volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> metody. `UseIISIntegration` Nakonfiguruje Kestrel k naslouchání na dynamický port na IP adresu místního hostitele (`localhost`). Pokud dynamický port je 1234, Kestrel naslouchá na `localhost:1234`. Tato konfigurace nahrazuje jiné konfigurace adresy URL poskytnuté:

* `UseUrls`
* [Rozhraní API od kestrel naslouchání](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Konfigurace](xref:fundamentals/configuration/index) (nebo [možnost příkazového řádku--adresy URL](xref:fundamentals/host/web-host#override-configuration))

Volání `UseUrls` nebo jeho Kestrel `Listen` rozhraní API nejsou povinné, když pomocí modulu. Pokud `UseUrls` nebo `Listen` nazývá Kestrel naslouchá na portu zadat jenom při spuštěné aplikaci bez služby IIS.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Zahrnout závislost [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/) balíčku v závislosti aplikaci. Používat middleware pro integraci služby IIS tak, že přidáte <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> metodu rozšíření k <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>:

```csharp
var host = new WebHostBuilder()
    .UseKestrel()
    .UseIISIntegration()
    ...
```

Obě <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> jsou povinné. Volání kódu `UseIISIntegration` nemá vliv na přenositelnost kódu. Pokud aplikace není spuštěna za služby IIS (například spuštění aplikace přímo na Kestrel), `UseIISIntegration` nepracuje.

Modul ASP.NET Core generuje dynamický port přiřadit k procesu back-endu. `UseIISIntegration` Nakonfiguruje Kestrel k naslouchání na dynamický port na IP adresu místního hostitele (`localhost`). Pokud dynamický port je 1234, Kestrel naslouchá na `localhost:1234`. Tato konfigurace nahrazuje jiné konfigurace adresy URL poskytnuté:

* `UseUrls`
* [Konfigurace](xref:fundamentals/configuration/index) (nebo [možnost příkazového řádku--adresy URL](xref:fundamentals/host/web-host#override-configuration))

Volání `UseUrls` není povinné, při použití modulu. Pokud `UseUrls` nazývá Kestrel naslouchá na portu zadat jenom při spuštěné aplikaci bez služby IIS.

Pokud `UseUrls` je volána v aplikaci ASP.NET Core 1.0, volání **před** volání `UseIISIntegration` tak, aby port nakonfigurovaný modul není přepsán. Toto pořadí volání není požadován spolu s ASP.NET Core 1.1, protože modul nastavení přepíše `UseUrls`.

::: moniker-end

Další informace o hostování najdete v tématu [hostitele v ASP.NET Core](xref:fundamentals/index#host).

### <a name="iis-options"></a>Možnosti služby IIS

::: moniker range=">= aspnetcore-2.2"

**Model hostingu v procesu**

Konfigurace možností serveru služby IIS, zahrnují konfiguraci služby pro <xref:Microsoft.AspNetCore.Builder.IISServerOptions> v <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>. Následující příklad zakazuje AutomaticAuthentication:

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| Možnost                         | Výchozí | Nastavení |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Pokud `true`, nastaví Server služby IIS `HttpContext.User` ověřována [ověřování Windows](xref:security/authentication/windowsauth). Pokud `false`, server pouze poskytuje identitu `HttpContext.User` a reaguje na problémy při explicitním požadavku `AuthenticationScheme`. Musí být povoleno ověřování Windows ve službě IIS pro `AutomaticAuthentication` na funkci. Další informace najdete v tématu [ověřování Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Nastaví zobrazovaný název, který se uživatelům na přihlašovací stránky zobrazí. |

**Model hostingu mimo proces**

::: moniker-end

Ke konfiguraci možností služby IIS, zahrnují konfiguraci služby pro <xref:Microsoft.AspNetCore.Builder.IISOptions> v <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>. Následující příklad brání aplikaci v naplnění `HttpContext.Connection.ClientCertificate`:

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

*Web.config* soubor nastaví [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module). Vytváření, transformace a publikování *web.config* soubor se zpracovává souborem cíl nástroje MSBuild (`_TransformWebConfig`) při publikování projektu. Tento cíl je k dispozici v cílech Web SDK (`Microsoft.NET.Sdk.Web`). V horní části souboru projektu je sada SDK:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Pokud *web.config* soubor není k dispozici v projektu, soubor, vytvoří se správnými *processPath* a *argumenty* ke konfiguraci [ASP.NET Core Modul](xref:host-and-deploy/aspnet-core-module) a přesunout do [publikovaný výstup](xref:host-and-deploy/directory-structure).

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

Pokud chcete nastavit [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) správně, *web.config* soubor musí být k dispozici v obsahu kořenové cestě (obvykle aplikace základní cesta) nasazené aplikace. Jedná se o stejné umístění jako fyzická cesta webu služby IIS k dispozici. *Web.config* soubor je vyžadován v kořenovém adresáři aplikace, aby se daly publikovat víc aplikací pomocí nasazení webu.

Citlivé soubory existují na fyzická cesta aplikace, jako například  *\<sestavení >. runtimeconfig.json*,  *\<sestavení > .xml* (komentáře dokumentace XML) a  *\<sestavení >. deps.json*. Když *web.config* soubor je k dispozici a lokality spustí běžným způsobem, služba IIS bariéru tyto citlivé soubory, pokud jste požádali. Pokud *web.config* soubor chybí, nesprávně pojmenované, nebo nelze konfigurovat lokalitu pro normální spuštění, služba IIS může poskytovat citlivé soubory veřejně.

***Web.config* soubor musí být k dispozici v nasazení za všech okolností, správně s názvem a nakonfigurovat web pro normální spuštění nahoru. Nikdy odebrat *web.config* soubor z produkčního nasazení.**

### <a name="transform-webconfig"></a>Transformace souboru web.config

Pokud potřebujete transformovat *web.config* při publikování (třeba nastavit proměnné prostředí na základě konfigurace, profil nebo prostředí), viz <xref:host-and-deploy/iis/transform-webconfig>.

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
   Pokud chcete povolit ověřování Windows, rozbalte následující uzly: **Webová služba** > **zabezpečení**. Vyberte **ověřování Windows** funkce. Další informace najdete v tématu [ověřování Windows \<windowsAuthentication >](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) a [Windows nakonfigurovat ověřování](xref:security/authentication/windowsauth).

   **Protokoly Websocket (volitelné)**  
   Protokoly Websocket je podporována s ASP.NET Core 1.1 nebo vyšší. Pokud chcete povolit protokoly Websocket, rozbalte následující uzly: **Webová služba** > **funkce pro vývoj aplikací**. Vyberte **protokol WebSocket** funkce. Další informace najdete v tématu [objekty Websocket](xref:fundamentals/websockets).

1. Pokud instalace služby IIS vyžaduje restartování, restartujte systém.

![Konzola pro správu služby IIS a webové služby jsou vybrány v funkce Windows.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a>Instalace .NET Core, který je hostitelem svazku

Nainstalujte *sady hostování rozhraní .NET Core* v hostitelském systému. Nainstaluje sady .NET Core Runtime, knihovny .NET Core a [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module). Povolí modul ASP.NET Core aplikací ke spuštění za služby IIS. Pokud systém nemá připojení k Internetu, získejte a nainstalujte [Microsoft Visual C++ 2015 Redistributable](https://www.microsoft.com/download/details.aspx?id=53840) před instalací sady hostování rozhraní .NET Core.

> [!IMPORTANT]
> Pokud před službou IIS instalovanou sadou hostování, je nutné opravit instalaci sady. Spusťte instalační program sady hostování znovu po instalaci služby IIS.

### <a name="direct-download-current-version"></a>Přímé stažení (aktuální verze)

Stažení instalačního programu pomocí následujícího odkazu:

[Aktuální instalační program sady hostování rozhraní .NET Core (s přímým přístupem ke stažení)](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a>Starší verze instalačního programu

Chcete-li získat starší verzi instalačního programu:

1. Přejděte [.NET stáhnout archivy](https://www.microsoft.com/net/download/archives).
1. V části **.NET Core**, vyberte verzi .NET Core.
1. V **spuštění aplikací – prostředí Runtime** sloupce, vyhledejte řádek požadované verze modulu runtime .NET Core.
1. Stáhněte si instalační program s použitím **Runtime & hostování svazek** odkaz.

> [!WARNING]
> Některé instalační programy obsahují verze, které bylo dosaženo jejich konci životnosti (konce řádku) a již nejsou podporovány společností Microsoft. Další informace najdete v tématu [zásady podpory](https://www.microsoft.com/net/download/dotnet-core/2.0).

### <a name="install-the-hosting-bundle"></a>Instalaci sady hostování

1. Spusťte instalační program na serveru. Při spuštění instalačního programu ze Správce příkazové prostředí jsou k dispozici následující parametry:

   * `OPT_NO_ANCM=1` &ndash; Instalace modulu jádra ASP.NET přeskočit.
   * `OPT_NO_RUNTIME=1` &ndash; Instalace modulu runtime .NET Core přeskočit.
   * `OPT_NO_SHAREDFX=1` &ndash; Přeskočit instalaci rozhraní ASP.NET sdílené (modul runtime technologie ASP.NET).
   * `OPT_NO_X86=1` &ndash; X86 instalace přeskočit runtimes. Tento parametr použijte, když víte, že vám nebude hosting 32bitové aplikace. Pokud je pravděpodobné, že budete hostovat 32bitové a 64bitové aplikace v budoucnu, není tento parametr použijte a nainstalujte oba moduly runtime.
   * `OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; Zakázat kontrolu pro použití sdílené konfigurace služby IIS při sdílenou konfiguraci (*applicationHost.config*) je ve stejném počítači jako instalace služby IIS. *K dispozici je pouze pro ASP.NET Core 2.2 nebo vyšší hostování například položky Bundler instalační programy.* Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.
1. Restartování systému nebo spuštění **net stop byla /y** následovaný **net start w3svc** z příkazové okno. Restartování služby IIS příjmem změnu systému provedené CESTU, která je proměnná prostředí, instalační služby.

Pokud instalační služby systému Windows, který je hostitelem svazku zjistí, že služba IIS dokončení instalace vyžaduje obnovení, obnoví instalační program služby IIS. Pokud instalační program spustí resetování služby IIS, se restartují všechny fondy aplikací IIS a websites.

> [!NOTE]
> Informace o sdílenou konfiguraci aplikaci IIS najdete v tématu [modul ASP.NET Core s sdílenou konfiguraci aplikaci IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Nainstalujte nástroj nasazení webu při publikování pomocí sady Visual Studio

Při nasazování aplikací na servery s [Webdeploy](/iis/publish/using-web-deploy/introduction-to-web-deploy), nainstalujte nejnovější verzi nástroje nasazení webu na serveru. Chcete-li nainstalujte nástroj nasazení webu, použijte [instalačního programu webové platformy (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) nebo získat přímo z instalačního programu [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717). Upřednostňovanou metodou je použití instalace webové platformy. Instalace webové platformy nabízí samostatné instalace a konfigurace pro poskytovatele hostingu.

## <a name="create-the-iis-site"></a>Vytvořte web služby IIS

1. V hostitelském systému vytvořte složku obsahující soubory a složky publikované aplikace. Rozložení nasazení vaší aplikace je popsána v [adresářovou strukturu](xref:host-and-deploy/directory-structure) tématu.

1. Ve Správci služby IIS rozbalte uzel serveru v **připojení** panelu. Klikněte pravým tlačítkem myši **lokality** složky. Vyberte **přidat web** z kontextové nabídky.

1. Zadejte **název lokality** a nastavit **fyzická cesta** do složky pro nasazení aplikace. Zadejte **vazby** konfigurace a vytvořit na webu výběrem **OK**:

   ![Zadejte název lokality, fyzickou cestu a název hostitele v kroku přidat web.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > Vazby nejvyšší úrovně zástupný znak (`http://*:80/` a `http://+:80`) by měl **není** použít. Vazby nejvyšší úrovně zástupný znak můžete otevřít aplikaci tak, aby slabá místa zabezpečení. To platí pro silné a slabé zástupné znaky. Používejte explicitní hostitele názvy místo zástupných znaků. Vazby zástupný znak subdoménu (například `*.mysub.com`) nemá toto bezpečnostní riziko, pokud celé nadřazené domény (nikoli `*.com`, což je ohrožené). Zobrazit [rfc7230 části-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) Další informace.

1. V uzlu server, vyberte **fondy aplikací**.

1. Klikněte pravým tlačítkem na fond aplikací webu a vyberte **základní nastavení** z kontextové nabídky.

1. V **upravit fond aplikací** okno, nastaveno **verze .NET CLR** k **bez spravovaného kódu**:

   ![Nastavit bez spravovaného kódu pro verze .NET CLR.](index/_static/edit-apppool-ws2016.png)

    ASP.NET Core běží v samostatném procesu a spravuje modulu runtime. ASP.NET Core nemusí spoléhat na načítání desktop CLR. Nastavení **verze .NET CLR** k **bez spravovaného kódu** je volitelný.

1. *ASP.NET Core 2.2 nebo vyšší*: Pro 64bitové (x64) [samostatná nasazení](/dotnet/core/deploying/#self-contained-deployments-scd) , která používá [model hostingu v procesu](xref:fundamentals/servers/index#in-process-hosting-model), zakažte fond aplikací pro procesy 32bitový (x 86).

   V **akce** postranního panelu ve Správci služby IIS > **fondy aplikací**vyberte **nastavit výchozí nastavení fondu aplikací** nebo **Upřesnit nastavení**. Vyhledejte **povolit 32bitové aplikace** a nastavte hodnotu na `False`. Toto nastavení nemá vliv na aplikace nasazené pro [mimo proces hostování](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).

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
* Použití Powershellu k vyřadit *app_offline.html* (vyžaduje PowerShell 5 nebo novější):

  ```PowerShell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a>Ochrana dat

[Ochranu dat ASP.NET Core zásobníku](xref:security/data-protection/introduction) používá několik ASP.NET Core [middlewares](xref:fundamentals/middleware/index), včetně middleware použitý v ověřování. I v případě, že Data Protection API nejsou volané kódem uživatele, se skriptem nasazení nebo v uživatelském kódu k vytvoření trvalé by měl nakonfigurovat ochranu dat kryptografických [úložiště klíčů](xref:security/data-protection/implementation/key-management). Pokud není nakonfigurovaná ochrana dat, jsou klíče uložené v paměti a při restartování aplikace.

Pokud kanál klíče jsou uloženy v paměti, při restartování aplikace:

* Všechny tokeny ověřování na základě souborů cookie nejsou zneplatněny. 
* Uživatelé se musí znovu přihlásit v jejich další požadavek. 
* Všechna data chráněná pomocí aktualizační kanál, který klíč můžete už nebude možné dešifrovat. To může zahrnovat [CSRF tokeny](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) a [soubory cookie v ASP.NET Core MVC TempData](xref:fundamentals/app-state#tempdata).

Chcete-li konfigurovat ochranu dat v rámci služby IIS k uchování aktualizační kanál, který klíč, použijte **jeden** z následujících postupů:

* **Vytvoření klíče registru ochrany dat**

  Používá aplikace ASP.NET Core klíče ochrany dat jsou uložené v registru, které jsou externí vzhledem k aplikacím. Pokud chcete zachovat klíče pro danou aplikaci, vytvořte klíče registru pro fond aplikací.

  Pro samostatnou, instalace služby IIS – webové farmě, [skript prostředí PowerShell AutoGenKeys.ps1 poskytování ochrany dat](https://github.com/aspnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) lze použít pro každý fond aplikací, které jsou součástí aplikace ASP.NET Core. Tento skript vytvoří klíč registru HKLM registru, který je přístupný pouze pro účet pracovního procesu fondu aplikací aplikaci. Klíče se zašifrují neaktivní uložená data pomocí rozhraní DPAPI klíčem celý počítač.

  Ve webových farem lze nastavit aplikaci pro použití cesty UNC pro ukládání jeho data protection klíč kanál. Ve výchozím nastavení klíče ochrany dat nejsou šifrovány. Zajistěte, aby byly omezené na účet Windows, které aplikace běží pod oprávnění pro sdílené síťové složce. X X509 certifikát můžete použít k ochraně klíčů v klidovém stavu. Vezměte v úvahu mechanismus pro uživatelům umožní nahrát certifikáty: Místo certifikátů do důvěryhodného certifikátu uživatele ukládat a ujistěte se, že jsou k dispozici na všech počítačích, ve kterém běží aplikace daného uživatele. Zobrazit [Konfigurace ochrany dat ASP.NET Core](xref:security/data-protection/configuration/overview) podrobnosti.

* **Konfigurace fondu aplikací služby IIS se načíst profil uživatele**

  Toto nastavení je v **Model procesu** části **Upřesnit nastavení** pro fond aplikací. Nastavte **načíst profil uživatele** k `True`. Pokud je nastavena na `True`, klíče jsou uložené v adresáři profilu uživatele a chránit pomocí rozhraní DPAPI klíčem specifické pro uživatelský účet. Klíče jsou zachované *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* složky.

  Fond aplikací [setProfileEnvironment atribut](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) také musí být povolené. Výchozí hodnota `setProfileEnvironment` je `true`. V některých případech (například Windows operačního systému) `setProfileEnvironment` je nastavena na `false`. Pokud se klíče nejsou uloženy v adresáři profilu uživatele jako očekávání:

  1. Přejděte *%windir%/system32/inetsrv/config* složky.
  1. Otevřít *applicationHost.config* souboru.
  1. Vyhledejte element `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>`.
  1. Ujistěte se, že `setProfileEnvironment` atribut není k dispozici, která má výchozí hodnotu hodnotu k `true`, nebo explicitně nastavit hodnotu atributu na `true`.

* **Systém souborů jako kanál klíč úložiště**

  Upravit kód aplikace, který [používat systém souborů jako kanál klíč úložiště](xref:security/data-protection/configuration/overview). Použijte X509 certifikátu k ochraně klíče kanál a zajistit certifikát není důvěryhodný certifikát. Pokud certifikát podepsaný svým držitelem, můžete certifikát umístěte do úložiště důvěryhodných kořenových certifikátů.

  Pokud používáte IIS ve webové farmě:

  * Použití sdílené složky, ke kterému přístup všechny počítače.
  * Nasazení x X509 certifikát pro každý počítač. Konfigurace [ochrany dat v kódu](xref:security/data-protection/configuration/overview).

* **Nastavte zásady pro celý počítač pro ochranu dat**

  Systém ochrany dat má omezenou podporu pro nastavení výchozí [celého zásad](xref:security/data-protection/configuration/machine-wide-policy) pro všechny aplikace, které používání rozhraní Data Protection API. Další informace naleznete v tématu <xref:security/data-protection/introduction>.

## <a name="virtual-directories"></a>Virtuální adresáře

[Virtuální složky IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) nejsou podporované s aplikací ASP.NET Core. Aplikace je možné hostovat jako [dílčí aplikace](#sub-applications).

## <a name="sub-applications"></a>Dílčí aplikace

Je možné hostovat aplikace ASP.NET Core jako [dílčí aplikace služby IIS (podřízeným aplikacím)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications). Sub – aplikace cesta stane součástí adresy URL kořenového aplikace.

::: moniker range="< aspnetcore-2.2"

Odběr aplikace by neměl obsahovat modul ASP.NET Core jako obslužná rutina. Pokud modul je přidán jako obslužná rutina sub-aplikace *web.config* souboru, *500.19 vnitřní chyba serveru* odkazující na chybného konfiguračního souboru obdržíte, když se pokusíte o procházení podřízeným aplikacím.

Následující příklad ukazuje publikování *web.config* sub aplikace ASP.NET Core v souboru:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Při hostování za nástrojem sub aplikace ASP.NET Core pod aplikace ASP.NET Core, sub-aplikace explicitně odebrat obslužnou rutinu zděděné *web.config* souboru:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore" />
    </handlers>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

::: moniker-end

Odkazy statických prostředků v rámci podřízeným aplikacím měli použít lomítko tilda (`~/`) notaci. Triggery notation tilda lomítko [pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) pro předřazení pathbase, je-sub aplikace pro vykreslený relativní odkaz. Pro odběr aplikace na `/subapp_path`, bitovou kopii propojené s `src="~/image.png"` se vykreslí jako `src="/subapp_path/image.png"`. Middleware kořenové aplikace statické soubory nelze zpracovat požadavek statický soubor. Žádost zpracovává Middleware sub aplikace statické soubory.

Pokud statický prostředek `src` atribut je nastaven na absolutní cestu (například `src="/image.png"`), odkaz je vykreslen bez pathbase, je-sub aplikace. Middleware kořenové aplikace statické soubory pokusí sloužit asset z kořenové aplikace [kořenový adresář webové](xref:fundamentals/index#web-root), výsledek bude *404 - Nenalezeno* odpovědi Pokud statických prostředků není k dispozici z kořenové aplikace.

K hostování aplikace v ASP.NET Core jako podřízeným aplikacím v rámci jiné aplikace ASP.NET Core:

1. Vytvořte fond aplikací pro aplikaci sub. Nastavte **verze .NET CLR** k **žádný spravovaný kód**.

1. Přidání kořenového webu s podřízeným aplikacím v rámci kořenového webu ve Správci služby IIS.

1. Klikněte pravým tlačítkem na složku podřízeným aplikacím ve Správci služby IIS a vyberte **převést na aplikaci**.

1. V **přidat aplikaci** dialogového okna, použijte **vyberte** tlačítko pro **fond aplikací** přiřadit fondu aplikací, kterou jste vytvořili pro podřízeným aplikacím. Vyberte **OK**.

Přiřazení fondu samostatné aplikace k podřízeným aplikacím je požadavek, pokud používáte model hostingu v procesu.

Další informace o procesu model hostingu a konfigurace modulu ASP.NET Core najdete v tématu <xref:host-and-deploy/aspnet-core-module> a <xref:host-and-deploy/aspnet-core-module>.

## <a name="configuration-of-iis-with-webconfig"></a>Konfigurace služby IIS pomocí souboru web.config

Konfigurace služby IIS je ovlivněno `<system.webServer>` část *web.config* pro scénářů služby IIS, které fungují pro aplikace ASP.NET Core s modul ASP.NET Core. Konfigurace služby IIS je třeba funkční dynamické komprese. Pokud je služba IIS konfigurována na úrovni serveru na použití dynamické komprese `<urlCompression>` element ve vaší aplikaci *web.config* souboru můžete zakázat pro aplikace ASP.NET Core.

Další informace najdete v tématu [odkaz Konfigurace pro \<system.webServer >](/iis/configuration/system.webServer/), [odkaz Konfigurace modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module), a [moduly IIS s ASP.NET Základní](xref:host-and-deploy/iis/modules). Chcete-li nastavit proměnné prostředí pro jednotlivé aplikace spuštěných ve fondech izolované aplikace (podporováno pro IIS 10.0 a novější), najdete v článku *AppCmd.exe příkaz* část [proměnné prostředí \< environmentVariables >](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) téma v IIS referenční dokumentaci.

## <a name="configuration-sections-of-webconfig"></a>Konfigurační oddíly souboru Web.config

Konfigurační oddíly funkce ASP.NET 4.x aplikací v *web.config* nejsou používány aplikací ASP.NET Core pro konfiguraci:

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

Aplikace ASP.NET Core jsou nakonfigurované pomocí jiných poskytovatelů konfigurace. Další informace najdete v tématu [konfigurace](xref:fundamentals/configuration/index).

## <a name="application-pools"></a>Fondy aplikací

::: moniker range=">= aspnetcore-2.2"

Izolace fond aplikací je určeno model hostingu:

* Proces hostování &ndash; aplikace jsou potřeba ke spouštění ve fondech samostatné aplikace.
* Hostování mimo proces &ndash; doporučujeme izoluje aplikace od sebe navzájem spuštěním každou aplikaci ve vlastním fondu aplikací.

IIS **přidat web** výchozí dialogové okno aplikace s jedním fondu na aplikaci. Když **název lokality** je k dispozici text je automaticky převedena do **fond aplikací** textového pole. Nový fond aplikací je vytvořený pomocí názvu serveru po přidání serveru.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Při hostování více webů na serveru, doporučujeme izoluje aplikace od sebe navzájem spuštěním každou aplikaci ve vlastním fondu aplikací. IIS **přidat web** výchozí dialogové okno pro tuto konfiguraci. Když **název lokality** je k dispozici text je automaticky převedena do **fond aplikací** textového pole. Nový fond aplikací je vytvořený pomocí názvu serveru po přidání serveru.

::: moniker-end

## <a name="application-pool-identity"></a>Identita fondu aplikací

Účet identita fondu aplikací umožňuje aplikaci běžet pod účtem jedinečný bez nutnosti vytvářet a spravovat domény nebo místní účty. IIS 8.0 nebo novější procesů pro pracovníka Správce služby IIS (WAS) vytvoří virtuální účet s názvem nového fondu aplikací a aplikace spouští fondu pracovních procesů pod tímto účtem ve výchozím nastavení. V konzole pro správu služby IIS v části **Upřesnit nastavení** pro fond aplikací, ujistěte se, že **Identity** nastaveno pro použití **ApplicationPoolIdentity**:

![Dialogové okno pokročilé nastavení fondu aplikací](index/_static/apppool-identity.png)

Proces správy služby IIS vytvoří zabezpečeného identifikátoru se název fondu aplikací v zabezpečení systému Windows. Pomocí této identity, je možné svázat prostředky. Ale tato identita není skutečný účet a nezobrazí se v konzole pro správu uživatelů Windows.

Pokud pracovní proces služby IIS vyžaduje přístup k aplikaci přes se zvýšenými oprávněními, upravte seznam řízení přístupu (ACL) pro adresář obsahující aplikaci:

1. Otevřete Průzkumníka Windows a přejděte do adresáře.

1. Klikněte pravým tlačítkem na adresář a vyberte **vlastnosti**.

1. V části **zabezpečení** kartu, vyberte **upravit** tlačítko a pak **přidat** tlačítko.

1. Vyberte **umístění** tlačítko a ujistěte se, že je vybraná systému.

1. Zadejte **fondu aplikací služby IIS\\< app_pool_name >** v **zadejte názvy objektů k výběru** oblasti. Vyberte **Kontrola názvů** tlačítko. Pro *DefaultAppPool* zkontrolovat názvy pomocí **IIS AppPool\DefaultAppPool**. Při **Kontrola názvů** se vybere tlačítko, hodnota **DefaultAppPool** je uveden v oblasti názvy objektu. Není možné zadat název fondu aplikací přímo do oblasti názvy objektu. Použití **fondu aplikací služby IIS\\< app_pool_name >** formátování při vyhledávání pro název objektu.

   ![Vyberte uživatele nebo skupiny dialogové okno pro složky aplikace: Název fondu aplikací "DefaultAppPool" se připojí k "fondu aplikací služby IIS\" v oblasti názvy objektu před výběrem"Zkontrolujte názvy."](index/_static/select-users-or-groups-1.png)

1. Vyberte **OK**.

   ![Vyberte uživatele nebo skupiny dialogové okno pro složky aplikace: Po výběru "Kontrola názvů", "DefaultAppPool" název objektu se zobrazí v oblasti názvy objektu.](index/_static/select-users-or-groups-2.png)

1. Čtení &amp; provést ve výchozím nastavení by měl být udělena oprávnění. Zadejte další oprávnění podle potřeby.

Na příkazovém řádku pomocí můžete také udělit přístup **ICACLS** nástroj. Použití *DefaultAppPool* jako příklad slouží následující příkaz:

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

Další informace najdete v tématu [icacls](/windows-server/administration/windows-commands/icacls) tématu.

## <a name="http2-support"></a>Podpora HTTP/2

::: moniker range=">= aspnetcore-2.2"

[HTTP/2](https://httpwg.org/specs/rfc7540.html) se podporuje s ASP.NET Core v následujících scénářích nasazení služby IIS:

* V procesu
  * Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.
  * Protokol TLS 1.2 nebo vyšší připojení
* Mimo proces
  * Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.
  * Připojení k serveru edge veřejně přístupných pomocí protokolu HTTP/2, ale reverzní proxy server připojení k [Kestrel server](xref:fundamentals/servers/kestrel) používá HTTP/1.1.
  * Protokol TLS 1.2 nebo vyšší připojení

V procesu nasazení po vytvoření připojení k protokolu HTTP/2 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/2`. Mimo proces nasazení po vytvoření připojení k protokolu HTTP/2 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/1.1`.

Další informace o modelech hostování a mimo proces, najdete v článku <xref:host-and-deploy/aspnet-core-module> tématu a <xref:host-and-deploy/aspnet-core-module>.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[HTTP/2](https://httpwg.org/specs/rfc7540.html) se podporuje pro nasazení mimo proces, které splňují základní požadavky na následující:

* Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.
* Připojení k serveru edge veřejně přístupných pomocí protokolu HTTP/2, ale reverzní proxy server připojení k [Kestrel server](xref:fundamentals/servers/kestrel) používá HTTP/1.1.
* Cílová architektura: Není k dispozici pro nasazení mimo proces, protože připojení HTTP/2 je zpracována zcela službou IIS.
* Protokol TLS 1.2 nebo vyšší připojení

Pokud se připojení HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/1.1`.

::: moniker-end

HTTP/2 je standardně povolená. Připojení vrátit zpět k protokolu HTTP/1.1, pokud nedojde k připojení k protokolu HTTP/2. Další informace o konfiguraci protokolu HTTP/2 u nasazení ve službě IIS najdete v části [HTTP/2 ve službě IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="cors-preflight-requests"></a>Předběžných požadavků CORS

*Tato část platí jenom pro aplikace ASP.NET Core, které se zaměřují rozhraní .NET Framework.*

Pro aplikace ASP.NET Core, který cílí .NET Framework, možnosti žádosti se předávají do aplikace ve výchozím nastavení ve službě IIS. Další informace o konfiguraci aplikace služby IIS obslužné rutiny v *web.config* předat požadavky OPTIONS, naleznete v tématu [povolení žádostí nepůvodního v ASP.NET Web API 2: Jak funguje CORS](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).

## <a name="deployment-resources-for-iis-administrators"></a>Materiály pro nasazení pro správce služby IIS

Další informace o službě IIS hlouběji v dokumentaci služby IIS.  
[Dokumentace ke službě IIS](/iis)

Další informace o modelech nasazení aplikace .NET Core.  
[Nasazení aplikace .NET core](/dotnet/core/deploying/)

Zjistěte, jak modul ASP.NET Core umožňuje webovému serveru Kestrel k použití jako reverzní proxy server služby IIS nebo IIS Express.  
[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module)

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

* <xref:test/troubleshoot>
* [Úvod do ASP.NET Core](xref:index)
* [Lokality oficiální Microsoft IIS](https://www.iis.net/)
* [Technická knihovna obsahu Windows serveru](/windows-server/windows-server)
* [HTTP/2 ve službě IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

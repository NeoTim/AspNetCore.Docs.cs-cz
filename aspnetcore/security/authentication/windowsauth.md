---
title: Konfigurace ověřování systému Windows v ASP.NET Core
author: scottaddie
description: Přečtěte si, jak nakonfigurovat ověřování systému Windows v ASP.NET Core pro službu IIS a HTTP.sys.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/windowsauth
ms.openlocfilehash: 8f6dc8620df04bcebe996119869ca2e498cffccc
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "87330678"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a>Konfigurace ověřování systému Windows v ASP.NET Core

[Scott Addie](https://twitter.com/Scott_Addie)

::: moniker range=">= aspnetcore-3.0"

Ověřování systému Windows (označované také jako Negotiate, Kerberos nebo ověřování NTLM) lze nakonfigurovat pro ASP.NET Core aplikace hostované službou [IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel)nebo [HTTP.sys](xref:fundamentals/servers/httpsys).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Ověřování systému Windows (označované také jako Negotiate, Kerberos nebo ověřování NTLM) lze nakonfigurovat pro ASP.NET Core aplikace hostované službou [IIS](xref:host-and-deploy/iis/index) nebo [HTTP.sys](xref:fundamentals/servers/httpsys).

::: moniker-end

Ověřování systému Windows využívá operační systém k ověřování uživatelů aplikací ASP.NET Core. Ověřování systému Windows můžete použít, pokud server běží na podnikové síti pomocí identit domény služby Active Directory nebo účtů systému Windows k identifikaci uživatelů. Ověřování systému Windows je nejvhodnější pro intranetová prostředí, kde uživatelé, klientské aplikace a webové servery patří do stejné domény systému Windows.

> [!NOTE]
> Ověřování systému Windows není u protokolu HTTP/2 podporováno. Výzvy k ověřování se dají odesílat na odpovědi HTTP/2, ale klient se musí před ověřením snížit na HTTP/1.1.

## <a name="proxy-and-load-balancer-scenarios"></a>Scénáře proxy a nástroje pro vyrovnávání zatížení

Ověřování systému Windows je stavový scénář primárně používaný v intranetu, kde proxy server nebo nástroj pro vyrovnávání zatížení obvykle nezpracovává přenos mezi klienty a servery. Pokud se používá proxy server nebo nástroj pro vyrovnávání zatížení, funguje ověřování systému Windows pouze v případě, že proxy server nebo nástroj pro vyrovnávání zatížení:

* Zpracovává ověřování.
* Předá do aplikace informace o ověřování uživatele (například v hlavičce požadavku), která funguje na ověřovacích informacích.

Alternativou k ověřování Windows v prostředích, kde se používají proxy a služby Vyrovnávání zatížení, je služba AD FS (Active Directory federovaných služeb) se službou OpenID Connect (OIDC).

## <a name="iisiis-express"></a>Služba IIS/IIS Express

Přidejte ověřovací služby vyvoláním <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ( <xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> namespace) v `Startup.ConfigureServices` :

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a>Nastavení spuštění (ladicí program)

Konfigurace pro nastavení spuštění má vliv pouze na *vlastnosti/launchSettings.jsv* souboru pro IIS Express a NEKONFIGURUJE službu IIS pro ověřování systému Windows. Konfigurace serveru je vysvětlena v části [IIS](#iis) .

Šablona **webové aplikace** , která je k dispozici prostřednictvím sady Visual Studio, nebo .NET Core CLI lze nakonfigurovat pro podporu ověřování systému Windows, která automaticky aktualizuje *vlastnosti/launchSettings.jsv* souboru.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**Nový projekt**

1. Vytvoření nového projektu
1. Vyberte **ASP.NET Core webové aplikace**. Vyberte **Další**.
1. Do pole **název projektu** zadejte název. Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu. Vyberte **Vytvořit**.
1. V části **ověřování**vyberte **změnit** .
1. V okně **změnit ověřování** vyberte **ověřování systému Windows**. Vyberte **OK**.
1. Vyberte **Webová aplikace**.
1. Vyberte **Vytvořit**.

Spusťte aplikaci. Uživatelské jméno se zobrazí v uživatelském rozhraní vykreslené aplikace.

**Existující projekt**

Vlastnosti projektu umožňují ověřování systému Windows a zakázání anonymního ověřování:

1. Klikněte pravým tlačítkem na projekt v **Průzkumník řešení** a vyberte **vlastnosti**.
1. Vyberte kartu **ladit** .
1. Zrušte zaškrtnutí políčka **Povolit anonymní ověřování**.
1. Zaškrtněte políčko **Povolit ověřování systému Windows**.
1. Uložte a zavřete stránku vlastností.

Případně lze vlastnosti nakonfigurovat v `iisSettings` uzlu *launchSettings.jsv* souboru:

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

**Nový projekt**

Spusťte příkaz [dotnet New](/dotnet/core/tools/dotnet-new) s `webapp` argumentem (ASP.NET Core webové aplikace) a `--auth Windows` Přepněte:

```dotnetcli
dotnet new webapp --auth Windows
```

**Existující projekt**

Aktualizovat `iisSettings` uzel *launchSettings.jsv* souboru:

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

Při úpravách existujícího projektu potvrďte, že soubor projektu obsahuje odkaz na balíček pro [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) **nebo** balíček NuGet [Microsoft. AspNetCore. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) .

### <a name="iis"></a>IIS

Služba IIS používá [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) k hostování ASP.NET Corech aplikací. Ověřování systému Windows je konfigurováno pro službu IIS prostřednictvím souboru *web.config* . Následující části ukazují, jak:

* Zadejte místní *web.config* soubor, který aktivuje ověřování systému Windows na serveru při nasazení aplikace.
* Pomocí Správce služby IIS nakonfigurujte *web.config* soubor aplikace ASP.NET Core, která už je nasazená na serveru.

Pokud jste to ještě neudělali, povolte službě IIS hostování aplikací ASP.NET Core. Další informace naleznete v tématu <xref:host-and-deploy/iis/index>.

Povolte službu role IIS pro ověřování systému Windows. Další informace najdete v tématu [Povolení ověřování systému Windows v rolích služby IIS (viz krok 2)](xref:host-and-deploy/iis/index#iis-configuration).

[Služba IIS Integration middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) je nakonfigurována pro automatické ověřování požadavků ve výchozím nastavení. Další informace najdete v tématu [hostitelská ASP.NET Core ve Windows se službou IIS: možnosti služby IIS (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).

Modul ASP.NET Core je nakonfigurován tak, aby ve výchozím nastavení předal token ověřování systému Windows do aplikace. Další informace najdete v tématu [Referenční příručka konfigurace modulu ASP.NET Core: atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

Použijte **některý** z následujících přístupů:

* **Před publikováním a nasazením projektu** přidejte následující soubor *web.config* do kořenového adresáře projektu:

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  Pokud je projekt publikován pomocí .NET Core SDK (bez `<IsTransformWebConfigDisabled>` vlastnosti nastavenou na hodnotu `true` v souboru projektu), publikovaný soubor *web.config* obsahuje `<location><system.webServer><security><authentication>` oddíl. Další informace o této `<IsTransformWebConfigDisabled>` vlastnosti naleznete v tématu <xref:host-and-deploy/iis/index#webconfig-file> .

* **Po publikování a nasazení projektu** proveďte u správce služby IIS konfiguraci na straně serveru:

  1. Ve Správci služby IIS vyberte v uzlu **lokality** na postranním panelu **připojení** web služby IIS.
  1. Dvakrát klikněte na **ověřování** v oblasti **IIS** .
  1. Vyberte **anonymní ověřování**. Na bočním panelu **Akce** vyberte **Zakázat** .
  1. Vyberte **ověřování systému Windows**. Na postranním panelu **Akce** vyberte **Povolit** .

  Při těchto akcích Správce služby IIS upraví soubor *web.config* aplikace. `<system.webServer><security><authentication>`Uzel je přidán s aktualizovaným nastavením pro `anonymousAuthentication` a `windowsAuthentication` :

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  `<system.webServer>`Oddíl přidaný do souboru *web.config* správcem služby IIS je mimo `<location>` oddíl aplikace přidaný .NET Core SDK při publikování aplikace. Vzhledem k tomu, že část je přidána mimo `<location>` uzel, nastavení jsou děděna všemi [podaplikacemi](xref:host-and-deploy/iis/index#sub-applications) do aktuální aplikace. Chcete-li zabránit dědění, přesuňte přidaný `<security>` oddíl do `<location><system.webServer>` oddílu, který .NET Core SDK poskytl.

  Když se k přidání konfigurace služby IIS použije Správce služby IIS, ovlivní to jenom *web.config* souboru aplikace na serveru. Následné nasazení aplikace může přepsat nastavení na serveru, pokud je kopie *web.config* serveru nahrazena *web.configm* souborem projektu. Ke správě nastavení použijte **některý** z následujících přístupů:

  * Pomocí Správce služby IIS obnovte nastavení v souboru *web.config* poté, co je soubor po nasazení přepsán.
  * Do aplikace lokálně přidejte *souborweb.config* s nastavením.

::: moniker range=">= aspnetcore-3.0"

## <a name="kestrel"></a>Kestrel

Balíček NuGet [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) se dá použít s [Kestrel](xref:fundamentals/servers/kestrel) k podpoře ověřování systému Windows pomocí dohadování a protokolu Kerberos v systémech Windows, Linux a MacOS.

> [!WARNING]
> Přihlašovací údaje je možné zachovat v rámci požadavků na připojení. *Ověřování Negotiate se nesmí používat s proxy servery, pokud proxy neudržuje spřažení připojení 1:1 (trvalé připojení) pomocí Kestrel.*

> [!NOTE]
> Obslužná rutina Negotiate detekuje, jestli základní server podporuje ověřování systému Windows nativně a jestli je povolený. Pokud server podporuje ověřování systému Windows, ale je zakázaný, vyvolá se chyba s výzvou, abyste povolili implementaci serveru. Pokud je na serveru povoleno ověřování systému Windows, obslužná rutina Negotiate ji transparentně přepošle.

Přidejte ověřovací služby vyvoláním <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> a <xref:Microsoft.Extensions.DependencyInjection.NegotiateExtensions.AddNegotiate*> v `Startup.ConfigureServices` :

 ```csharp
// using Microsoft.AspNetCore.Authentication.Negotiate;
// using Microsoft.Extensions.DependencyInjection;

services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
    .AddNegotiate();
```

Přidejte middleware ověřování voláním <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> `Startup.Configure` :

 ```csharp
app.UseAuthentication();
```

Další informace o middlewaru naleznete v tématu <xref:fundamentals/middleware/index> .

Anonymní požadavky jsou povoleny. K ověření anonymních žádostí o ověření použijte [ASP.NET Core autorizaci](xref:security/authorization/introduction) .

### <a name="windows-environment-configuration"></a>Konfigurace prostředí Windows

Komponenta [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) provádí ověřování v uživatelském režimu. Hlavní názvy služeb (SPN) musí být přidány do uživatelského účtu, který spouští službu, nikoli z účtu počítače. Spustit `setspn -S HTTP/myservername.mydomain.com myuser` v příkazovém prostředí pro správu.

### <a name="linux-and-macos-environment-configuration"></a>Konfigurace prostředí Linux a macOS

Pokyny pro připojení počítače se systémem Linux nebo macOS k doméně systému Windows jsou k dispozici v tématu [připojení Azure Data Studio k vašemu SQL Server pomocí ověřování systému Windows – Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) . Pokyny vytvoří účet počítače pro počítač se systémem Linux v doméně. Do tohoto účtu počítače se musí přidat hlavní názvy služby (SPN).

> [!NOTE]
> Pokud [budete postupovat podle pokynů v Azure Data Studio připojit k vašemu SQL Server pomocí ověřování systému Windows – Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) , `python-software-properties` v `python3-software-properties` případě potřeby nahraďte.

Jakmile je počítač se systémem Linux nebo macOS připojený k doméně, jsou potřeba další kroky k poskytnutí [souboru keytab](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) s názvy SPN:

* Na řadiči domény přidejte nové hlavní názvy služby webu k účtu počítače:
  * `setspn -S HTTP/mywebservice.mydomain.com mymachine`
  * `setspn -S HTTP/mywebservice@MYDOMAIN.COM mymachine`
* K vygenerování souboru keytab použijte [Ktpass](/windows-server/administration/windows-commands/ktpass) :
  * `ktpass -princ HTTP/mywebservice.mydomain.com@MYDOMAIN.COM -pass myKeyTabFilePassword -mapuser MYDOMAIN\mymachine$ -pType KRB5_NT_PRINCIPAL -out c:\temp\mymachine.HTTP.keytab -crypto AES256-SHA1`
  * Některá pole musí být zadána velkými písmeny, jak je uvedeno.
* Zkopírujte soubor keytab do počítače se systémem Linux nebo macOS.
* Vyberte soubor keytab přes proměnnou prostředí:`export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`
* Invoke `klist` pro zobrazení hlavních názvů služby, které jsou aktuálně k dispozici pro použití.

> [!NOTE]
> Soubor keytab obsahuje přihlašovací údaje pro přístup k doméně a musí být vhodným způsobem chráněn.

::: moniker-end

## <a name="httpsys"></a>HTTP.sys

[HTTP.sys](xref:fundamentals/servers/httpsys) podporuje ověřování systému Windows v režimu jádra pomocí ověřování Negotiate, NTLM nebo Basic.

Přidejte ověřovací služby vyvoláním <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ( <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace) v `Startup.ConfigureServices` :

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

Nakonfigurujte webového hostitele aplikace tak, aby používal HTTP.sys s ověřováním systému Windows (*program.cs*). <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>je v <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> oboru názvů.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> HTTP.sys deleguje ověřování režimu jádra pomocí ověřovacího protokolu Kerberos. Ověřování pomocí uživatelského režimu není pro protokol Kerberos a HTTP.sys podporováno. Účet počítače se musí použít k dešifrování tokenu nebo lístku protokolu Kerberos, který je získaný ze služby Active Directory a který přesměruje klient na server za účelem ověření uživatele. Zaregistrujte hlavní název služby (SPN) pro hostitele, nikoli uživatele aplikace.

> [!NOTE]
> HTTP.sys se na nano serveru verze 1709 nebo novější nepodporují. Pokud chcete používat ověřování Windows a HTTP.sys s Nano serverem, použijte [kontejner jádro serveru (Microsoft/windowsservercore)](https://hub.docker.com/r/microsoft/windowsservercore/). Další informace o jádru serveru najdete v tématu [co je možnost instalace jádra serveru ve Windows serveru](/windows-server/administration/server-core/what-is-server-core).

## <a name="authorize-users"></a>Autorizovat uživatele

Stav konfigurace anonymního přístupu určuje způsob, jakým se v `[Authorize]` `[AllowAnonymous]` aplikaci používají atributy a. Následující dvě části vysvětlují, jak zpracovat nepovolené a povolené stavy Konfigurace anonymního přístupu.

### <a name="disallow-anonymous-access"></a>Zakázat anonymní přístup

Pokud je povoleno ověřování systému Windows a je zakázán anonymní přístup, `[Authorize]` `[AllowAnonymous]` atributy a nemají žádný vliv. Pokud je lokalita IIS nakonfigurovaná tak, aby povolovala anonymní přístup, požadavek nikdy nedosáhne vaší aplikace. Z tohoto důvodu se `[AllowAnonymous]` atribut nedá použít.

### <a name="allow-anonymous-access"></a>Povolení anonymního přístupu

Pokud je povoleno ověřování systému Windows i anonymní přístup, použijte `[Authorize]` atributy a `[AllowAnonymous]` . `[Authorize]`Atribut umožňuje zabezpečit koncové body aplikace, které vyžadují ověření. `[AllowAnonymous]`Atribut Přepisuje `[Authorize]` atribut v aplikacích, které povolují anonymní přístup. Podrobnosti o použití atributů naleznete v tématu <xref:security/authorization/simple> .

> [!NOTE]
> Ve výchozím nastavení se uživatelům, kteří nemají oprávnění k přístupu na stránku, zobrazí prázdná odpověď HTTP 403. [Middleware StatusCodePages](xref:fundamentals/error-handling#usestatuscodepages) se dá nakonfigurovat tak, aby poskytoval uživatelům lepší přístup "přístup byl odepřen".

## <a name="impersonation"></a>Zosobnění

ASP.NET Core neimplementuje zosobnění. Aplikace se spouštějí s identitou aplikace pro všechny žádosti pomocí fondu aplikací nebo identity procesu. Pokud by aplikace měla provést akci jménem uživatele, použijte [WindowsIdentity. RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) v [middlewaru vloženého terminálu](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) v `Startup.Configure` . Spusťte v tomto kontextu jednu akci a pak kontext zavřete.

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

`RunImpersonated`nepodporuje asynchronní operace a neměl by se používat pro složité scénáře. Například zabalení všech požadavků nebo řetězů middleware není podporováno ani doporučeno.

::: moniker range=">= aspnetcore-3.0"

I když balíček [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) umožňuje ověřování v systémech Windows, Linux a MacOS, zosobnění je podporováno pouze v systému Windows.

::: moniker-end

## <a name="claims-transformations"></a>Transformace deklarací identity

::: moniker range=">= aspnetcore-3.0"

Při hostování se službou IIS <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> není volána interně k inicializaci uživatele. Proto <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementace používaná k transformaci deklarací po každém ověření není ve výchozím nastavení aktivována. Další informace a příklad kódu, který aktivuje transformace deklarací identity, najdete v tématu <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Při hostování v režimu vnitroprocesové v rámci služby IIS <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> není volána interně k inicializaci uživatele. Proto <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementace používaná k transformaci deklarací po každém ověření není ve výchozím nastavení aktivována. Další informace a příklad kódu, který aktivuje transformace deklarací při hostování v procesu, naleznete v tématu <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> .

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>

---
title: Konfigurace ověřování Windows v ASP.NET Core
author: scottaddie
description: Zjistěte, jak nakonfigurovat ověřování Windows v ASP.NET Core pro službu IIS a HTTP.sys.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 05/29/2019
uid: security/authentication/windowsauth
ms.openlocfilehash: 9dfff5dcba409ddca7e05c771b864ab121e0ea85
ms.sourcegitcommit: 06c4f2910dd54ded25e1b8750e09c66578748bc9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66395926"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a>Konfigurace ověřování Windows v ASP.NET Core

Podle [Scott Addie](https://twitter.com/Scott_Addie) a [Luke Latham](https://github.com/guardrex)

[Ověřování Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) lze konfigurovat pro aplikace ASP.NET Core s hostitelem [IIS](xref:host-and-deploy/iis/index) nebo [HTTP.sys](xref:fundamentals/servers/httpsys).

Ověřování Windows závisí na operačním systému k ověření uživatelů z aplikací ASP.NET Core. Ověřování Windows můžete použít, pokud váš server běží v podnikové síti pomocí identity služby Active Directory domény nebo účty Windows k identifikaci uživatelů. Ověřování Windows je nejvhodnější pro prostředí intranetu, kde uživatelé klientských aplikací a webové servery patří do stejné domény Windows.

## <a name="launch-settings-debugger"></a>Spusťte nastavení (ladicí program)

Konfigurace nastavení spuštění má vliv pouze *Properties/launchSettings.json* souboru a neprovede konfiguraci serveru služby IIS nebo ovladač HTTP.sys pro ověřování Windows. Konfigurace serveru je podrobně [povolit ověřování služby IIS nebo ovladač HTTP.sys](#authentication-services-for-iis-or-httpsys) oddílu.

**Webovou aplikaci** šablony, které jsou k dispozici prostřednictvím sady Visual Studio nebo rozhraní příkazového řádku .NET Core může být nakonfigurované pro podporu ověřování Windows, která aktualizuje *Properties/launchSettings.json* souboru automaticky.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

**Nový projekt**

1. Vytvořte nový projekt.
1. Vyberte **webová aplikace ASP.NET Core**. Vyberte **Další**.
1. Zadejte název **název projektu** pole. Potvrďte **umístění** položka je správný, a zadejte umístění pro projekt. Vyberte **Vytvořit**.
1. Vyberte **změnu** pod **ověřování**.
1. V **změna ověřování** okně **ověřování Windows**. Vyberte **OK**.
1. Vyberte **webovou aplikaci**.
1. Vyberte **Vytvořit**.

Spusťte aplikaci. Uživatelské jméno se zobrazí v uživatelském rozhraní vygenerované aplikace.

**Existující projekt**

Vlastnosti projektu povolit ověřování Windows a vypnutí anonymního ověřování:

1. Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **vlastnosti**.
1. Vyberte **ladění** kartu.
1. Zrušte zaškrtnutí políčka pro **povolit anonymní ověřování**.
1. Zaškrtněte políčko pro **povolit ověřování Windows**.
1. Uložit a zavřít jeho stránku vlastností.

Alternativně se dá nakonfigurovat vlastnosti v `iisSettings` uzlu *launchSettings.json* souboru:

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[Visual Studio Code / .NET Core CLI](#tab/visual-studio-code+netcore-cli)

**Nový projekt**

Spustit [dotnet nové](/dotnet/core/tools/dotnet-new) příkazů `webapp` argument (webové aplikace ASP.NET Core) a `--auth Windows` přepínače:

```console
dotnet new webapp --auth Windows
```

**Existující projekt**

Aktualizace `iisSettings` uzlu *launchSettings.json* souboru:

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

Při úpravě existujícího projektu, přesvědčte se, že soubor projektu obsahuje odkaz na balíček pro [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) **nebo** [ Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) balíček NuGet.

## <a name="authentication-services-for-iis-or-httpsys"></a>Ověřování služby IIS nebo ovladač HTTP.sys

V závislosti na scénáři hostování, postupujte podle pokynů v **buď** [IIS](#iis) části **nebo** [HTTP.sys](#httpsys) oddílu.

### <a name="iis"></a>IIS

Přidat ověřovací služby vyvoláním <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> oboru názvů) v `Startup.ConfigureServices`:

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

Služba IIS použije [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) pro hostování aplikací ASP.NET Core. Ověřování Windows je nakonfigurovaný pro službu IIS prostřednictvím *web.config* souboru. V následujících částech zobrazit postup:

* Zadejte místní *web.config* soubor, který aktivuje ověřování Windows na serveru při nasazení aplikace.
* Pomocí Správce služby IIS ke konfiguraci *web.config* souboru aplikace v ASP.NET Core, která již byla nasazena na server.

Pokud jste tak již neučinili, povolte službu IIS pro hostování aplikací ASP.NET Core. Další informace naleznete v tématu <xref:host-and-deploy/iis/index>.

Povolte službu Role služby IIS pro ověřování Windows. Další informace najdete v tématu [povolit ověřování Windows služby Role služby IIS (viz krok 2)](xref:host-and-deploy/iis/index#iis-configuration).

[Middleware pro integraci služby IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) je ve výchozím nastavení nakonfigurované k automatickému ověření žádosti. Další informace najdete v tématu [hostitele ASP.NET Core ve Windows se službou IIS: Možnosti služby IIS (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).

Modul ASP.NET Core je ve výchozím nastavení nakonfigurovaný pro předávání Windows ověřovací token do aplikace. Další informace najdete v tématu [odkaz na modul ASP.NET Core konfigurace: Atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

Použití **buď** z následujících postupů:

* **Před publikováním a nasazování projektu,** přidejte následující *web.config* souboru do kořenového adresáře projektu:

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  Když se publikuje projektu .NET Core SDK (bez `<IsTransformWebConfigDisabled>` vlastnost nastavena na hodnotu `true` v souboru projektu), publikovanému *web.config* soubor obsahuje `<location><system.webServer><security><authentication>` části. Další informace o `<IsTransformWebConfigDisabled>` vlastnost, naleznete v tématu <xref:host-and-deploy/iis/index#webconfig-file>.

* **Po publikování a nasazení projektu,** provedení konfigurace na straně serveru pomocí Správce služby IIS:

  1. Ve Správci služby IIS vyberte web služby IIS v části **lokality** uzlu **připojení** bočním panelu.
  1. Dvakrát klikněte na panel **ověřování** v **IIS** oblasti.
  1. Vyberte **anonymní ověřování**. Vyberte **zakázat** v **akce** bočním panelu.
  1. Vyberte **ověřování Windows**. Vyberte **povolit** v **akce** bočním panelu.

  Když se provedou tyto akce, Správce služby IIS upraví aplikace *web.config* souboru. A `<system.webServer><security><authentication>` s aktualizovaným nastavením pro přidání uzlu `anonymousAuthentication` a `windowsAuthentication`:

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  `<system.webServer>` Přidá do části *web.config* soubor pomocí Správce služby IIS je mimo aplikaci prvku `<location>` části přidá .NET Core SDK, když je aplikace publikována. Vzhledem k tomu, že v části Přidání mimo `<location>` uzlu nastavení dědí žádné [dílčí aplikace](xref:host-and-deploy/iis/index#sub-applications) do aktuální aplikace. Chcete-li zabránit dědění, přesuňte přidaného `<security>` části uvnitř `<location><system.webServer>` části, která poskytuje sada .NET Core SDK.

  Když správce služby IIS se používá k přidání konfigurace služby IIS, ovlivní pouze aplikace *web.config* souboru na serveru. Následné nasazení aplikace může přepsat nastavení na serveru, pokud na server kopii *web.config* nahrazuje projektu *web.config* souboru. Použití **buď** z následujících postupů můžete spravovat nastavení:

  * Pomocí Správce služby IIS k resetování nastavení ve *web.config* souboru po souboru se přepíše při nasazení.
  * Přidat *souboru web.config* do aplikace místně s nastavením.

### <a name="httpsys"></a>HTTP.sys

I když [Kestrel](xref:fundamentals/servers/kestrel) nepodporuje ověřování Windows, můžete použít [HTTP.sys](xref:fundamentals/servers/httpsys) pro zajištění podpory scénářů v místním prostředí ve Windows.

Přidat ověřovací služby vyvoláním <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> oboru názvů) v `Startup.ConfigureServices`:

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

Konfigurace hostitele webové aplikace pomocí HTTP.sys ověřování Windows (*Program.cs*). <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> Probíhá <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> oboru názvů.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> Ovladač HTTP.sys delegáty pro ověřování v režimu jádra ověřování protokolem Kerberos. Režim ověřování uživatele nepodporuje protokolů Kerberos a HTTP.sys. Účet počítače musí být použité k dešifrování token/lístek služby Kerberos, která se získá z Active Directory a předá klienta na serveru k ověření uživatele. Zaregistrujte hlavní název služby (SPN) příslušného hostitele není uživatel aplikace.

> [!NOTE]
> Soubor HTTP.sys nepodporuje na Nano Server verze 1709 nebo novější. Chcete-li používat ověřování Windows a HTTP.sys s Nano serverem, použijte [jádra serveru (microsoft/windowsservercore) kontejneru](https://hub.docker.com/r/microsoft/windowsservercore/). Další informace o jádra serveru najdete v tématu [co je možnost instalace jádra serveru v systému Windows Server?](/windows-server/administration/server-core/what-is-server-core).

## <a name="authorize-users"></a>Autorizace uživatelů

Stav konfigurace anonymního přístupu určuje způsob, jakým `[Authorize]` a `[AllowAnonymous]` atributy se používají v aplikaci. Následující dvě části popisují, jak zpracovat stavy zakázaných a povolených Konfigurace anonymního přístupu.

### <a name="disallow-anonymous-access"></a>Zakázat anonymní přístup

Když je povolené ověřování Windows a je zakázán anonymní přístup, `[Authorize]` a `[AllowAnonymous]` atributy nemají žádný účinek. Pokud se k webu služby IIS je konfigurována tak anonymní přístup, požadavek nikdy dosáhne aplikace. Z tohoto důvodu `[AllowAnonymous]` atributu nelze použít.

### <a name="allow-anonymous-access"></a>Povolení anonymního přístupu

Pokud jsou povolené ověřování Windows a anonymní přístup, použijte `[Authorize]` a `[AllowAnonymous]` atributy. `[Authorize]` Atribut umožňuje zabezpečené koncové body aplikace, které vyžadují ověřování. `[AllowAnonymous]` Atribut přepsání `[Authorize]` atribut v aplikacích, které povolí anonymní přístup. Podrobnosti o použití atributu, naleznete v tématu <xref:security/authorization/simple>.

> [!NOTE]
> Ve výchozím nastavení uživatelé, kteří nemají oprávnění k získání přístupu ke stránce se zobrazí prázdnou odpověď HTTP 403. [StatusCodePages Middleware](xref:fundamentals/error-handling#usestatuscodepages) umožňují uživatelům poskytovat lepší prostředí "Přístup byl odepřen".

## <a name="impersonation"></a>Zosobnění

ASP.NET Core neimplementuje zosobnění. Aplikace běží s identitou aplikace pro všechny požadavky pomocí aplikace identity fondu nebo procesu. Pokud aplikace musí provést akce jménem uživatele, použijte [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) v [terminálu vložené middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) v `Startup.Configure`. V tomto kontextu spuštění jedné akce a potom zavřete kontextu.

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

`RunImpersonated` nepodporuje asynchronní operace by se neměl používat pro komplexní scénáře. Například obtékání celý požadavky nebo middleware zřetězen není podporován nebo doporučené.

## <a name="claims-transformations"></a>Transformace deklarací identity

Při hostování za nástrojem s režimem v procesu služby IIS, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> nevolá interně k inicializaci uživatele. Proto <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementace používaném k transformaci deklarací identity po každém ověření není ve výchozím nastavení. Další informace a příklad kódu, který aktivuje transformace deklarací identity, při hostování v procesu najdete v tématu <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.

## <a name="additional-resources"></a>Další zdroje

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>

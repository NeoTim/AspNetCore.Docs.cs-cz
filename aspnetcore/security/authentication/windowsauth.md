---
title: Konfigurace ověřování Windows v ASP.NET Core
author: scottaddie
description: Zjistěte, jak nakonfigurovat ověřování Windows v ASP.NET Core pomocí služby IIS Express, IIS a HTTP.sys.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 01/15/2019
uid: security/authentication/windowsauth
ms.openlocfilehash: c98bdedcf943a9057c96a8e5d62615e400074899
ms.sourcegitcommit: 42a8164b8aba21f322ffefacb92301bdfb4d3c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54341651"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a>Konfigurace ověřování Windows v ASP.NET Core

Podle [Scott Addie](https://twitter.com/Scott_Addie) a [Luke Latham](https://github.com/guardrex)

[Ověřování Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) lze konfigurovat pro aplikace ASP.NET Core s hostitelem [IIS](xref:host-and-deploy/iis/index) nebo [HTTP.sys](xref:fundamentals/servers/httpsys).

Ověřování Windows závisí na operačním systému k ověření uživatelů z aplikací ASP.NET Core. Ověřování Windows můžete použít, pokud váš server běží v podnikové síti pomocí identity služby Active Directory domény nebo účty Windows k identifikaci uživatelů. Ověřování Windows je nejvhodnější pro prostředí intranetu, kde uživatelé klientských aplikací a webové servery patří do stejné domény Windows.

## <a name="enable-windows-authentication-in-an-aspnet-core-app"></a>Povolení ověřování Windows v aplikaci ASP.NET Core

**Webovou aplikaci** šablony, které jsou k dispozici prostřednictvím sady Visual Studio nebo rozhraní příkazového řádku .NET Core může být nakonfigurované pro podporu ověřování Windows.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="use-the-windows-authentication-app-template-for-a-new-project"></a>Použití šablony aplikace ověřování Windows pro nový projekt

V sadě Visual Studio:

1. Vytvořte nový **webové aplikace ASP.NET Core**.
1. Vyberte **webovou aplikaci** ze seznamu šablon.
1. Vyberte **změna ověřování** tlačítko a vyberte **ověřování Windows**.

Spusťte aplikaci. Uživatelské jméno se zobrazí v uživatelském rozhraní vygenerované aplikace.

### <a name="manual-configuration-for-an-existing-project"></a>Ruční konfigurace pro existující projekt

Vlastnosti projektu umožňují povolit ověřování Windows a vypnutí anonymního ověřování:

1. Klikněte pravým tlačítkem na projekt v sadě Visual Studio **Průzkumníka řešení** a vyberte **vlastnosti**.
1. Vyberte **ladění** kartu.
1. Zrušte zaškrtnutí políčka pro **povolit anonymní ověřování**.
1. Zaškrtněte políčko pro **povolit ověřování Windows**.

Alternativně se dá nakonfigurovat vlastnosti v `iisSettings` uzlu *launchSettings.json* souboru:

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Použití **ověřování Windows** šablony aplikace.

Spustit [dotnet nové](/dotnet/core/tools/dotnet-new) příkazů `webapp` argument (webové aplikace ASP.NET Core) a `--auth Windows` přepínače:

```console
dotnet new webapp --auth Windows
```

---

Při úpravě existujícího projektu, přesvědčte se, že soubor projektu obsahuje odkaz na balíček pro [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) **nebo** [ Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) balíček NuGet.

## <a name="enable-windows-authentication-with-iis"></a>Povolení ověřování Windows pomocí služby IIS

Služba IIS použije [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) pro hostování aplikací ASP.NET Core. Ověřování Windows je nakonfigurovaný pro službu IIS prostřednictvím *web.config* souboru. V následujících částech zobrazit postup:

* Zadejte místní *web.config* soubor, který aktivuje ověřování Windows na serveru při nasazení aplikace.
* Pomocí Správce služby IIS ke konfiguraci *web.config* souboru aplikace v ASP.NET Core, která již byla nasazena na server.

### <a name="iis-configuration"></a>Konfigurace služby IIS

Pokud jste tak již neučinili, povolte službu IIS pro hostování aplikací ASP.NET Core. Další informace naleznete v tématu <xref:host-and-deploy/iis/index>.

Povolte službu Role služby IIS pro ověřování Windows. Další informace najdete v tématu [povolit ověřování Windows služby Role služby IIS (viz krok 2)](xref:host-and-deploy/iis/index#iis-configuration).

Middleware pro integraci služby IIS je ve výchozím nastavení nakonfigurované k automatickému ověření žádosti. Další informace najdete v tématu [hostitele ASP.NET Core ve Windows se službou IIS: Možnosti služby IIS (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).

Modul ASP.NET Core je ve výchozím nastavení nakonfigurovaný pro předávání Windows ověřovací token do aplikace. Další informace najdete v tématu [odkaz na modul ASP.NET Core konfigurace: Atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

### <a name="create-a-new-iis-site"></a>Vytvořit nový web služby IIS

Zadejte název a složku a mohla vytvořit nový fond aplikací.

### <a name="enable-windows-authentication-for-the-app-in-iis"></a>Povolení ověřování Windows pro aplikace ve službě IIS

Použití **buď** z následujících postupů:

* [Konfigurace na straně vývoj před publikováním aplikace](#development-side-configuration-with-a-local-webconfig-file) (*doporučená*)
* [Konfigurace na straně serveru po publikování aplikace](#server-side-configuration-with-the-iis-manager)

#### <a name="development-side-configuration-with-a-local-webconfig-file"></a>Konfigurace na straně pro vývoj se souborem web.config místní

Proveďte následující kroky **před** vám [publikujte a nasaďte svůj projekt](#publish-and-deploy-your-project-to-the-iis-site-folder).

Přidejte následující *web.config* souboru do kořenového adresáře projektu:

[!code-xml[](windowsauth/sample_snapshot/web_2.config)]

Při publikování projektu sada SDK (bez `<IsTransformWebConfigDisabled>` vlastnost nastavena na hodnotu `true` v souboru projektu), publikovanému *web.config* soubor obsahuje `<location><system.webServer><security><authentication>` oddílu. Další informace o `<IsTransformWebConfigDisabled>` vlastnost, naleznete v tématu <xref:host-and-deploy/iis/index#webconfig-file>.

#### <a name="server-side-configuration-with-the-iis-manager"></a>Konfigurace na straně serveru pomocí Správce služby IIS

Proveďte následující kroky **po** vám [publikujte a nasaďte svůj projekt](#publish-and-deploy-your-project-to-the-iis-site-folder).

1. Ve Správci služby IIS vyberte web služby IIS v části **lokality** uzlu **připojení** bočním panelu.
1. Dvakrát klikněte na panel **ověřování** v **IIS** oblasti.
1. Vyberte **anonymní ověřování**. Vyberte **zakázat** v **akce** bočním panelu.
1. Vyberte **ověřování Windows**. Vyberte **povolit** v **akce** bočním panelu.

Když se provedou tyto akce, Správce služby IIS upraví aplikace *web.config* souboru. A `<system.webServer><security><authentication>` s aktualizovaným nastavením pro přidání uzlu `anonymousAuthentication` a `windowsAuthentication`:

[!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

`<system.webServer>` Přidá do části *web.config* soubor pomocí Správce služby IIS je mimo aplikaci prvku `<location>` části přidá .NET Core SDK, když je aplikace publikována. Vzhledem k tomu, že v části Přidání mimo `<location>` uzlu nastavení dědí žádné [dílčí aplikace](xref:host-and-deploy/iis/index#sub-applications) do aktuální aplikace. Chcete-li zabránit dědění, přesuňte přidaného `<security>` části uvnitř `<location><system.webServer>` oddíl, který poskytuje sady SDK.

Když správce služby IIS se používá k přidání konfigurace služby IIS, ovlivní pouze aplikace *web.config* souboru na serveru. Následné nasazení aplikace může přepsat nastavení na serveru, pokud na server kopii *web.config* nahrazuje projektu *web.config* souboru. Použití **buď** z následujících postupů můžete spravovat nastavení:

* Pomocí Správce služby IIS k resetování nastavení ve *web.config* souboru po souboru se přepíše při nasazení.
* Přidat *souboru web.config* do aplikace místně s nastavením. Další informace najdete v tématu [konfigurace na straně vývoj](#development-side-configuration-with-a-local-webconfig-file) oddílu.

### <a name="publish-and-deploy-your-project-to-the-iis-site-folder"></a>Publikujte a nasaďte projekt do složky webu služby IIS

Pomocí sady Visual Studio nebo rozhraní příkazového řádku .NET Core, publikujte a nasaďte aplikaci do cílové složky.

Další informace o hostování se službou IIS publikování a nasazení, najdete v následujících tématech:

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>

Spuštění aplikace a zkontrolujte, že funguje ověřování Windows.

## <a name="enable-windows-authentication-with-httpsys"></a>Povolení ověřování Windows pomocí HTTP.sys

Přestože Kestrel nepodporuje ověřování Windows, můžete použít [HTTP.sys](xref:fundamentals/servers/httpsys) pro zajištění podpory scénářů v místním prostředí ve Windows. Následující příklad nastaví hostitel webové aplikace pro použití HTTP.sys s ověřováním Windows:

[!code-csharp[](windowsauth/sample_snapshot/Program.cs?highlight=9-14)]

> [!NOTE]
> Ovladač HTTP.sys delegáty pro ověřování v režimu jádra ověřování protokolem Kerberos. Režim ověřování uživatele nepodporuje protokolů Kerberos a HTTP.sys. Účet počítače musí být použité k dešifrování token/lístek služby Kerberos, která se získá z Active Directory a předá klienta na serveru k ověření uživatele. Zaregistrujte hlavní název služby (SPN) příslušného hostitele není uživatel aplikace.

> [!NOTE]
> Soubor HTTP.sys nepodporuje na Nano Server verze 1709 nebo novější. Chcete-li používat ověřování Windows a HTTP.sys s Nano serverem, použijte [jádra serveru (microsoft/windowsservercore) kontejneru](https://hub.docker.com/r/microsoft/windowsservercore/). Další informace o jádra serveru najdete v tématu [co je možnost instalace jádra serveru v systému Windows Server?](/windows-server/administration/server-core/what-is-server-core).

## <a name="work-with-windows-authentication"></a>Práce s ověřováním Windows

Stav konfigurace anonymního přístupu určuje způsob, jakým `[Authorize]` a `[AllowAnonymous]` atributy se používají v aplikaci. Následující dvě části popisují, jak zpracovat stavy zakázaných a povolených Konfigurace anonymního přístupu.

### <a name="disallow-anonymous-access"></a>Zakázat anonymní přístup

Když je povolené ověřování Windows a je zakázán anonymní přístup, `[Authorize]` a `[AllowAnonymous]` atributy nemají žádný účinek. Pokud chcete zakázat anonymní přístup je nakonfigurovaný web služby IIS (nebo ovladač HTTP.sys), požadavek dosáhne nikdy vaší aplikace. Z tohoto důvodu `[AllowAnonymous]` atributu nelze použít.

### <a name="allow-anonymous-access"></a>Povolení anonymního přístupu

Pokud jsou povolené ověřování Windows a anonymní přístup, použijte `[Authorize]` a `[AllowAnonymous]` atributy. `[Authorize]` Atribut umožňuje zabezpečit části aplikace, které skutečně vyžadují ověřování Windows. `[AllowAnonymous]` Atribut přepsání `[Authorize]` atribut využití v aplikacích, které umožňují anonymní přístup. Zobrazit [jednoduchá autorizace](xref:security/authorization/simple) podrobnosti o použití atributu.

V ASP.NET Core 2.x, `[Authorize]` atribut vyžaduje další konfiguraci ve *Startup.cs* vybízí anonymních požadavků pro ověřování Windows. Doporučená konfigurace mírně závisí na webovém serveru, který je používán.

> [!NOTE]
> Ve výchozím nastavení uživatelé, kteří nemají oprávnění k získání přístupu ke stránce se zobrazí prázdnou odpověď HTTP 403. [StatusCodePages middleware](xref:fundamentals/error-handling#configure-status-code-pages) umožňují uživatelům poskytovat lepší prostředí "Přístup byl odepřen".

#### <a name="iis"></a>IIS

Pokud používáte službu IIS, přidejte následující text do `ConfigureServices` metody:

```csharp
// IISDefaults requires the following import:
// using Microsoft.AspNetCore.Server.IISIntegration;
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

#### <a name="httpsys"></a>HTTP.sys

Pokud používáte HTTP.sys, přidejte následující text do `ConfigureServices` metody:

```csharp
// HttpSysDefaults requires the following import:
// using Microsoft.AspNetCore.Server.HttpSys;
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

### <a name="impersonation"></a>Zosobnění

ASP.NET Core neimplementuje zosobnění. Aplikace běží s identitou aplikace pro všechny požadavky pomocí aplikace identity fondu nebo procesu. Pokud je třeba explicitně provést akce jménem uživatele, použijte [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) v [terminálu vložené middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) v `Startup.Configure`. V tomto kontextu spuštění jedné akce a potom zavřete kontextu.

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

`RunImpersonated` nepodporuje asynchronní operace by se neměl používat pro komplexní scénáře. Například obtékání celý požadavky nebo middleware zřetězen není podporován nebo doporučené.

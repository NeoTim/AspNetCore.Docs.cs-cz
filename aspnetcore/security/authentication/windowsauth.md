---
title: Konfigurace ověřování Windows v ASP.NET Core
author: scottaddie
description: Zjistěte, jak nakonfigurovat ověřování Windows v ASP.NET Core pomocí služby IIS Express, IIS, ovladač HTTP.sys a WebListener.
ms.author: riande
ms.custom: mvc
ms.date: 11/01/2018
uid: security/authentication/windowsauth
ms.openlocfilehash: 87fcab75555c1dae0b2815c30d79fd4615df9660
ms.sourcegitcommit: 85f2939af7a167b9694e1d2093277ffc9a741b23
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50968290"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a>Konfigurace ověřování Windows v ASP.NET Core

Podle [Steve Smith](https://ardalis.com) a [Scott Addie](https://twitter.com/Scott_Addie)

Ověřování Windows se dá nakonfigurovat pro aplikace ASP.NET Core hostované službou IIS, [HTTP.sys](xref:fundamentals/servers/httpsys), nebo [WebListener](xref:fundamentals/servers/weblistener).

## <a name="windows-authentication"></a>Ověřování systému Windows

Ověřování Windows závisí na operačním systému k ověření uživatelů z aplikací ASP.NET Core. Ověřování Windows můžete použít, pokud váš server běží v podnikové síti pomocí identit domény služby Active Directory nebo jiné účty Windows k identifikaci uživatelů. Ověřování Windows je nejvhodnější pro prostředí intranetu, ve kterých uživatelé, klientské aplikace a webové servery patří do stejné domény Windows.

[Další informace o ověřování Windows a nainstalovat ho pro službu IIS](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/).

## <a name="enable-windows-authentication-in-an-aspnet-core-app"></a>Povolení ověřování Windows v aplikaci ASP.NET Core

Šablony Visual Studio webové aplikace mohou být nakonfigurované pro podporu ověřování Windows.

### <a name="use-the-windows-authentication-app-template"></a>Použití šablony aplikace ověřování Windows

V sadě Visual Studio:

1. Vytvořte novou webovou aplikaci ASP.NET Core.
1. Vyberte webovou aplikaci ze seznamu šablon.
1. Vyberte **změna ověřování** tlačítko a vyberte **ověřování Windows**.

Spusťte aplikaci. Uživatelské jméno se zobrazí v horní části přímo z aplikace.

![Snímek obrazovky prohlížeče ověřování Windows](windowsauth/_static/browser-screenshot.png)

Při vývojových pracích pomocí služby IIS Express Šablona nabízí veškeré konfigurace nezbytné pro použití ověřování Windows. Následující část popisuje postup při ruční konfiguraci aplikace ASP.NET Core pro ověřování Windows.

### <a name="visual-studio-settings-for-windows-and-anonymous-authentication"></a>Nastavení sady Visual Studio pro Windows a anonymní ověřování

Projekt aplikace Visual Studio **vlastnosti** stránky **ladění** karta obsahuje zaškrtávací políčka pro ověřování Windows a anonymní ověřování.

![Snímek obrazovky prohlížeče ověřování Windows](windowsauth/_static/vs-auth-property-menu.png)

Alternativně se dá nakonfigurovat tyto dvě vlastnosti v *launchSettings.json* souboru:

[!code-json[](windowsauth/sample/launchSettings.json?highlight=3-4)]

## <a name="enable-windows-authentication-with-iis"></a>Povolení ověřování Windows pomocí služby IIS

Služba IIS použije [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module) pro hostování aplikací ASP.NET Core. Ověřování Windows konfigurován ve službě IIS, ne aplikace. Následující části vysvětlují, jak pomocí Správce služby IIS ke konfiguraci aplikace ASP.NET Core používat ověřování Windows.

### <a name="iis-configuration"></a>Konfigurace služby IIS

Povolte službu Role služby IIS pro ověřování Windows. Další informace najdete v tématu [povolit ověřování Windows služby Role služby IIS (viz krok 2)](xref:host-and-deploy/iis/index#iis-configuration).

Middleware pro integraci služby IIS je ve výchozím nastavení nakonfigurované k automatickému ověření žádosti. Další informace najdete v tématu [hostitele ASP.NET Core ve Windows se službou IIS: IIS možnosti (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).

Modul ASP.NET Core je ve výchozím nastavení nakonfigurovaný pro předávání Windows ověřovací token do aplikace. Další informace najdete v tématu [odkaz Konfigurace modul ASP.NET Core: atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

### <a name="create-a-new-iis-site"></a>Vytvořit nový web služby IIS

Zadejte název a složku a mohla vytvořit nový fond aplikací.

### <a name="customize-authentication"></a>Přizpůsobení ověřování

Otevřete funkcích ověřování pro lokalitu.

![Nabídka ověřování služby IIS](windowsauth/_static/iis-authentication-menu.png)

Zakázat anonymní ověřování a povolit ověřování Windows.

![Nastavení ověřování služby IIS](windowsauth/_static/iis-auth-settings.png)

### <a name="publish-your-project-to-the-iis-site-folder"></a>Publikovat projekt do složky webu služby IIS

Pomocí sady Visual Studio nebo rozhraní příkazového řádku .NET Core, publikujte aplikaci do cílové složky.

![Dialogové okno pro publikování aplikace Visual Studio](windowsauth/_static/vs-publish-app.png)

Další informace o [publikování do služby IIS](xref:host-and-deploy/iis/index).

Spuštění aplikace a zkontrolujte, že funguje ověřování Windows.

::: moniker range=">= aspnetcore-2.0"

## <a name="enable-windows-authentication-with-httpsys"></a>Povolení ověřování Windows pomocí HTTP.sys

Přestože Kestrel nepodporuje ověřování Windows, můžete použít [HTTP.sys](xref:fundamentals/servers/httpsys) pro zajištění podpory scénářů v místním prostředí ve Windows. Následující příklad nastaví hostitel webové aplikace pro použití HTTP.sys s ověřováním Windows:

[!code-csharp[](windowsauth/sample/Program2x.cs?highlight=9-14)]

> [!NOTE]
> Ovladač HTTP.sys delegáty pro ověřování v režimu jádra ověřování protokolem Kerberos. Režim ověřování uživatele nepodporuje protokolů Kerberos a HTTP.sys. Účet počítače musí být použité k dešifrování token/lístek služby Kerberos, která se získá z Active Directory a předá klienta na serveru k ověření uživatele. Zaregistrujte hlavní název služby (SPN) příslušného hostitele není uživatel aplikace.

> [!NOTE]
> Soubor HTTP.sys nepodporuje na Nano Server verze 1709 nebo novější. Chcete-li používat ověřování Windows a HTTP.sys s Nano serverem, použijte [jádra serveru (microsoft/windowsservercore) kontejneru](https://hub.docker.com/r/microsoft/windowsservercore/). Další informace o jádra serveru najdete v tématu [co je možnost instalace jádra serveru v systému Windows Server?](/windows-server/administration/server-core/what-is-server-core).

::: moniker-end

::: moniker range="< aspnetcore-2.0"

## <a name="enable-windows-authentication-with-weblistener"></a>Povolení ověřování Windows pomocí WebListener

Přestože Kestrel nepodporuje ověřování Windows, můžete použít [WebListener](xref:fundamentals/servers/weblistener) pro zajištění podpory scénářů v místním prostředí ve Windows. Následující příklad nastaví hostitel webové aplikace pro použití WebListener s ověřováním Windows:

[!code-csharp[](windowsauth/sample/Program1x.cs?highlight=6-11)]

> [!NOTE]
> WebListener delegáty pro ověřování v režimu jádra ověřování protokolem Kerberos. Režim ověřování uživatele nepodporuje protokolů Kerberos a WebListener. Účet počítače musí být použité k dešifrování token/lístek služby Kerberos, která se získá z Active Directory a předá klienta na serveru k ověření uživatele. Zaregistrujte hlavní název služby (SPN) příslušného hostitele není uživatel aplikace.

::: moniker-end

## <a name="work-with-windows-authentication"></a>Práce s ověřováním Windows

Stav konfigurace anonymního přístupu určuje způsob, jakým `[Authorize]` a `[AllowAnonymous]` atributy se používají v aplikaci. Následující dvě části popisují, jak zpracovat stavy zakázaných a povolených Konfigurace anonymního přístupu.

### <a name="disallow-anonymous-access"></a>Zakázat anonymní přístup

Když je povolené ověřování Windows a je zakázán anonymní přístup, `[Authorize]` a `[AllowAnonymous]` atributy nemají žádný účinek. Pokud web služby IIS (nebo HTTP.sys nebo WebListener server) je konfigurována tak anonymní přístup, požadavek dosáhne nikdy vaší aplikace. Z tohoto důvodu `[AllowAnonymous]` atributu nelze použít.

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

ASP.NET Core neimplementuje zosobnění. Aplikace běží s identitou aplikace pro všechny požadavky pomocí aplikace identity fondu nebo procesu. Pokud je třeba explicitně provést akce jménem uživatele, použijte `WindowsIdentity.RunImpersonated`. V tomto kontextu spuštění jedné akce a potom zavřete kontextu.

[!code-csharp[](windowsauth/sample/Startup.cs?name=snippet_Impersonate&highlight=10-18)]

Všimněte si, že `RunImpersonated` nepodporuje asynchronní operace by se neměl používat pro komplexní scénáře. Například obtékání celý požadavky nebo middleware zřetězen není podporován nebo doporučené.

---
title: Metabalíček Microsoft.AspNetCore.All pro ASP.NET Core 2.0
author: Rick-Anderson
description: Metabalíček Microsoft.AspNetCore.All se nedoporučuje pro ASP.NET Core 2.1 a novější.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/25/2018
uid: fundamentals/metapackage
ms.openlocfilehash: cc00c075909da5c17a4aa2fd252c9e662e5a0fc9
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511064"
---
# <a name="microsoftaspnetcoreall-metapackage-for-aspnet-core-20"></a>Metabalíček Microsoft.AspNetCore.All pro ASP.NET Core 2.0

::: moniker range=">= aspnetcore-3.0"

Metabalíček `Microsoft.AspNetCore.All` není součástí ASP.NET Core 3.0 a novější. Další informace naleznete v [tomto problému GitHub](https://github.com/aspnet/Announcements/issues/314).

::: moniker-end

> [!NOTE]
> Doporučujeme aplikace zaměřené ASP.NET Core 2.1 a novější používat [metabalíček Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) spíše než tento balíček. V tomto článku najdete [téma Migrace z Microsoft.AspNetCore.All na Microsoft.AspNetCore.App.](#migrate)

Tato funkce vyžaduje ASP.NET cílení na jádro 2.x .NET Core 2.x.

[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) je metabalíček, který odkazuje na sdílené rozhraní. *Sdílená architektura* je sada sestavení (*soubory DLL),* které nejsou ve složkách aplikace. Sdílená architektura musí být nainstalována v počítači, aby bylo možné aplikaci spustit. Další informace naleznete [v tématu sdílený rámec](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

Sdílený rámec, `Microsoft.AspNetCore.All` který odkazuje, zahrnuje:

* Všechny podporované balíčky ASP.NET core týmu.
* Všechny podporované balíčky jádrem entity frameworku.
* Interní závislosti a závislosti třetích stran používané ASP.NET jádrem jádra a entity frameworku.

Všechny funkce ASP.NET Core 2.x a Entity Framework Core `Microsoft.AspNetCore.All` 2.x jsou součástí balíčku. Výchozí šablony projektu zaměřené na ASP.NET Core 2.0 používají tento balíček.

Číslo verze `Microsoft.AspNetCore.All` metabalíčku představuje minimální verzi ASP.NET Core a core verze entity frameworku.

Následující soubor *.csproj* odkazuje `Microsoft.AspNetCore.All` na metabalíček pro ASP.NET Core:

[!code-xml[](metapackage/samples/Metapackage.All.Example.csproj?highlight=8)]

::: moniker range=">= aspnetcore-2.1"

## <a name="implicit-versioning"></a>Implicitní správa verzí

V ASP.NET Jádrem 2.1 nebo `Microsoft.AspNetCore.All` novějším můžete zadat odkaz na balíček bez verze. Pokud není zadána verze, implicitní verze je určena`Microsoft.NET.Sdk.Web`sadou SDK ( ). Doporučujeme spoléhat se na implicitní verzi určenou sadou SDK a neexplicitně nastavit číslo verze v odkazu na balíček. Pokud máte dotazy týkající se tohoto přístupu, zanechte komentář GitHub u [implicitní verze Diskuse o microsoft.AspNetCore.App](https://github.com/dotnet/AspNetCore.Docs/issues/6430).

Implicitní verze je `major.minor.0` nastavena na pro přenosné aplikace. Mechanismus přetáčení sdíleného rozhraní spouští aplikaci na nejnovější kompatibilní verzi mezi nainstalovanými sdílenými rámci. Chcete-li zaručit, že stejná verze se používá ve vývoji, testování a výrobě, ujistěte se, že stejná verze sdíleného rozhraní je nainstalována ve všech prostředích. U samostatných aplikací je implicitní číslo verze `major.minor.patch` nastaveno na sdílenou architekturu, která je součástí nainstalované sady SDK.

Zadání čísla verze v `Microsoft.AspNetCore.All` odkazu na balíček **nezaručuje,** že je vybrána verze sdíleného rozhraní. Předpokládejme například, že je zadána verze "2.1.1", ale je nainstalována verze "2.1.3". V takovém případě bude aplikace používat "2.1.3". I když se nedoporučuje, můžete zakázat roll vpřed (patch a / nebo menší). Další informace týkající se dotnet hostitele roll-forward a jak nakonfigurovat jeho chování, naleznete [v tématu dotnet hostitele posunout vpřed](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).

Sada SDK projektu musí být `Microsoft.NET.Sdk.Web` nastavena na v souboru `Microsoft.AspNetCore.All`projektu, aby bylo možné použít implicitní verzi aplikace . Pokud `Microsoft.NET.Sdk` je zadána sada`<Project Sdk="Microsoft.NET.Sdk">` SDK (v horní části souboru projektu), je generováno následující upozornění:

*Upozornění NU1604: Závislost projektu Microsoft.AspNetCore.All neobsahuje včetně dolní mez. Zahrnout dolní mez ve verzi závislosti zajistit konzistentní výsledky obnovení.*

Jedná se o známý problém s sadou .NET Core 2.1 SDK a bude opraven v sada .NET Core 2.2 SDK.

::: moniker-end

<a name="migrate"></a>

## <a name="migrating-from-microsoftaspnetcoreall-to-microsoftaspnetcoreapp"></a>Migrace z Microsoft.AspNetCore.All na Microsoft.AspNetCore.App

Následující balíčky jsou `Microsoft.AspNetCore.All` zahrnuty `Microsoft.AspNetCore.App` v balíčku, ale ne balíček.

* `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`
* `Microsoft.AspNetCore.AzureAppServices.HostingStartup`
* `Microsoft.AspNetCore.AzureAppServicesIntegration`
* `Microsoft.AspNetCore.DataProtection.AzureKeyVault`
* `Microsoft.AspNetCore.DataProtection.AzureStorage`
* `Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`
* `Microsoft.AspNetCore.SignalR.Redis`
* `Microsoft.Data.Sqlite`
* `Microsoft.Data.Sqlite.Core`
* `Microsoft.EntityFrameworkCore.Sqlite`
* `Microsoft.EntityFrameworkCore.Sqlite.Core`
* `Microsoft.Extensions.Caching.Redis`
* `Microsoft.Extensions.Configuration.AzureKeyVault`
* `Microsoft.Extensions.Logging.AzureAppServices`
* `Microsoft.VisualStudio.Web.BrowserLink`

Pokud se `Microsoft.AspNetCore.All` `Microsoft.AspNetCore.App`chcete přesunout z aplikace do aplikace, pokud vaše aplikace používá nějaká api z výše uvedených balíčků nebo balíčky, které tyto balíčky přinesly, přidejte odkazy na tyto balíčky v projektu.

Všechny závislosti předchozí balíčky, které jinak nejsou `Microsoft.AspNetCore.App` závislosti nejsou implicitně zahrnuty. Příklad:

* `StackExchange.Redis`jako závislost`Microsoft.Extensions.Caching.Redis`
* `Microsoft.ApplicationInsights`jako závislost`Microsoft.AspNetCore.ApplicationInsights.HostingStartup`

## <a name="update-aspnet-core-21"></a>Aktualizace ASP.NET jádra 2.1

Doporučujeme migrovat `Microsoft.AspNetCore.App` na metabalíček pro 2.1 a novější. Chcete-li `Microsoft.AspNetCore.All` pokračovat v používání metabalíčku a zajistit nasazení nejnovější verze opravy:

* Na vývojových počítačích a serverech sestavení: Nainstalujte nejnovější [sadu .NET Core SDK](https://dotnet.microsoft.com/download).
* Na serverech nasazení: Nainstalujte nejnovější [runtime jádra .NET](https://dotnet.microsoft.com/download).
 Aplikace se při restartování aplikace posune dopředu na nejnovější nainstalovanou verzi.

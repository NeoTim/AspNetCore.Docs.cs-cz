---
title: Metabalíček Microsoft.AspNetCore.App pro ASP.NET jádro
author: Rick-Anderson
description: Sdílený rámec Microsoft.AspNetCore.App
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/24/2019
uid: fundamentals/metapackage-app
ms.openlocfilehash: b30c90116f5a53ba487f88544514f36e388233d3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511376"
---
# <a name="microsoftaspnetcoreapp-for-aspnet-core"></a>Microsoft.AspNetCore.App pro ASP.NET jádro

::: moniker range=">= aspnetcore-3.0"

 Sdílené rozhraní ASP.NET`Microsoft.AspNetCore.App`Core ( ) obsahuje sestavení, která jsou vyvíjena a podporována společností Microsoft. `Microsoft.AspNetCore.App`je nainstalována při instalaci sady [.NET Core 3.0 nebo novější SDK.](https://dotnet.microsoft.com/download/dotnet-core/3.0) *Sdílená architektura* je sada sestavení (*soubory DLL),* které jsou nainstalovány v počítači a obsahují součást runtime a balíček cílení. Další informace naleznete [v tématu sdílený rámec](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

* Projekty, `Microsoft.NET.Sdk.Web` které se zaměřují na `Microsoft.AspNetCore.App` sdk implicitně odkazovat na rozhraní.

Pro tyto projekty nejsou vyžadovány žádné další odkazy:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>
    ...
</Project>
```

Sdílený rámec ASP.NET Core:

* Nezahrnuje závislosti třetích stran.
* Zahrnuje všechny podporované balíčky ASP.NET týmu Core.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Tato funkce vyžaduje ASP.NET cílení na jádro 2.x .NET Core 2.x.

[Metabalíček](/dotnet/core/packages#metapackages) [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App) pro ASP.NET jádro:

* Nezahrnuje závislosti třetích stran s výjimkou [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/)a [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/). Tyto závislosti třetích stran jsou považovány za nezbytné k zajištění funkce funkce hlavní architektury.
* Zahrnuje všechny podporované balíčky ASP.NET základního týmu s výjimkou těch, které obsahují závislosti třetích stran (jiné než ty, které byly zmíněny).
* Zahrnuje všechny podporované balíčky týmu Entity Framework Core s výjimkou těch, které obsahují závislosti třetích stran (jiné než ty, které byly zmíněny).

Všechny funkce ASP.NET Core 2.x a Entity Framework Core `Microsoft.AspNetCore.App` 2.x jsou součástí balíčku. Výchozí šablony projektu zaměřené na ASP.NET Core 2.x používají tento balíček. Doporučujeme aplikace zaměřené ASP.NET Core 2.x a Entity `Microsoft.AspNetCore.App` Framework Core 2.x použít balíček.

Číslo verze `Microsoft.AspNetCore.App` metabalíčku představuje minimální verzi ASP.NET Core a core verze entity frameworku.

Použití `Microsoft.AspNetCore.App` metabalíčku poskytuje omezení verze, která chrání vaši aplikaci:

* Pokud je součástí balíčku, který má přenosnou (ne přímou) závislost na balíčku v `Microsoft.AspNetCore.App`aplikaci a tato čísla verzí se liší, NuGet vygeneruje chybu.
* Další balíčky přidané do vaší aplikace nemohou `Microsoft.AspNetCore.App`změnit verzi balíčků zahrnutých v aplikaci .
* Konzistence verze zajišťuje spolehlivé prostředí. `Microsoft.AspNetCore.App`byl navržen tak, aby se zabránilo netestované verze kombinace souvisejících bitů se používá společně ve stejné aplikaci.

Aplikace, které `Microsoft.AspNetCore.App` používají metabalíček automaticky využít ASP.NET základní sdílené rozhraní. Při použití `Microsoft.AspNetCore.App` metabalíčku **žádné** prostředky z odkazované ASP.NET balíčky Core NuGet jsou nasazeny s aplikací&mdash;ASP.NET základní sdílené rozhraní obsahuje tyto prostředky. Prostředky ve sdíleném rámci jsou předkompilovány pro zlepšení doby spuštění aplikace. Další informace naleznete [v tématu sdílený rámec](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

Následující soubor projektu odkazuje `Microsoft.AspNetCore.App` na metabalíček pro ASP.NET Core a představuje typickou šablonu ASP.NET Core 2.2:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.2</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

</Project>
```

Předchozí značky představuje typickou šablonu ASP.NET Core 2.x. Neurčuje číslo verze pro odkaz `Microsoft.AspNetCore.App` na balíček. Pokud není zadána verze, [implicitní](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) verze je určena sadou `Microsoft.NET.Sdk.Web`SDK, to znamená . Doporučujeme spoléhat se na implicitní verzi určenou sadou SDK a neexplicitně nastavit číslo verze v odkazu na balíček. Pokud máte dotazy k tomuto přístupu, zanechte komentář GitHub u [implicitní verze Diskuse o microsoft.AspNetCore.App](https://github.com/dotnet/AspNetCore.Docs/issues/6430).

Implicitní verze je `major.minor.0` nastavena na pro přenosné aplikace. Mechanismus pro předávání sdílených architektur spustí aplikaci v nejnovější kompatibilní verzi mezi nainstalovanými sdílenými rámci. Chcete-li zaručit, že stejná verze se používá ve vývoji, testování a výrobě, ujistěte se, že stejná verze sdíleného rozhraní je nainstalována ve všech prostředích. U samostatných aplikací je implicitní číslo `major.minor.patch` verze nastaveno na sdílenou architekturu, která je součástí nainstalované sady SDK.

Zadání čísla verze `Microsoft.AspNetCore.App` v odkazu **nezaručuje,** že bude vybrána verze sdíleného rozhraní. Předpokládejme například, že je zadána verze "2.2.1", ale je nainstalována verze "2.2.3". V takovém případě bude aplikace používat "2.2.3". I když se nedoporučuje, můžete zakázat roll vpřed (patch a / nebo menší). Další informace týkající se dotnet hostitele roll-forward a jak nakonfigurovat jeho chování, naleznete [v tématu dotnet hostitele posunout vpřed](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).

::: moniker-end

::: moniker range="= aspnetcore-2.1"

`<Project Sdk`musí být `Microsoft.NET.Sdk.Web` nastavena tak, `Microsoft.AspNetCore.App`aby používala implicitní verzi . Při `<Project Sdk="Microsoft.NET.Sdk">` použití (bez `.Web`koncového) použití:

* Je generováno následující upozornění:

  *Upozornění NU1604: Závislost projektu Microsoft.AspNetCore.App neobsahuje včetně dolní mez. Zahrnout dolní mez ve verzi závislosti zajistit konzistentní výsledky obnovení.*

* Toto je známý problém s .NET Core 2.1 SDK.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<a name="update"></a>

## <a name="update-aspnet-core"></a>Aktualizovat ASP.NET jádro

`Microsoft.AspNetCore.App` [Metapackage](/dotnet/core/packages#metapackages) není tradiční balíček, který je aktualizován z NuGet. Podobně `Microsoft.NETCore.App`jako `Microsoft.AspNetCore.App` , představuje sdílený runtime, který má speciální sémantiku správu verzí zpracovány mimo NuGet. Další informace naleznete [v tématu Balíčky, metabalíčky a rámce](/dotnet/core/packages).

Aktualizace ASP.NET jádra:

* Na vývojových počítačích a serverech sestavení: Stáhněte a nainstalujte sadu [.NET Core SDK](https://dotnet.microsoft.com/download).
* Na nasazení serverů: Stáhněte a nainstalujte [runtime .NET Core](https://dotnet.microsoft.com/download).

 Aplikace se při restartování aplikace posunou na nejnovější nainstalovanou verzi. Není nutné aktualizovat číslo `Microsoft.AspNetCore.App` verze v souboru projektu. Další informace najdete v [tématu Aplikace závislé na rozhraní framework ustoupit](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).

Pokud vaše aplikace `Microsoft.AspNetCore.All`dříve používala , přečtěte si informace [o migraci z microsoft.AspNetCore.All na Microsoft.AspNetCore.App](xref:fundamentals/metapackage#migrate).

::: moniker-end

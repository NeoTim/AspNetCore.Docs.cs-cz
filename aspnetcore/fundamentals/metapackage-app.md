---
title: Microsoft. AspNetCore. app Metapackage pro ASP.NET Core
author: Rick-Anderson
description: Sdílené rozhraní Microsoft. AspNetCore. app
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/24/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/metapackage-app
ms.openlocfilehash: 89f447699e8219ec4dd48b2342095d86a011860f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407964"
---
# <a name="microsoftaspnetcoreapp-for-aspnet-core"></a>Microsoft. AspNetCore. app pro ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

 Rozhraní ASP.NET Core Shared Framework ( `Microsoft.AspNetCore.App` ) obsahuje sestavení vyvinutá a podporovaná společností Microsoft. `Microsoft.AspNetCore.App`se nainstaluje, když je nainstalovaná [sada .NET Core 3,0 nebo novější SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) . *Sdílené rozhraní* je sada sestavení (soubory *. dll* ), které jsou nainstalovány na počítači a zahrnují komponentu modulu runtime a sadu targeting pack. Další informace najdete v tématu [sdílené rozhraní](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

* Projekty, které cílí na `Microsoft.NET.Sdk.Web` sadu SDK, implicitně odkazují na `Microsoft.AspNetCore.App` rozhraní.

Pro tyto projekty nejsou vyžadovány žádné další odkazy:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>
    ...
</Project>
```

ASP.NET Core sdílené rozhraní:

* Neobsahuje závislosti třetích stran.
* Zahrnuje všechny podporované balíčky ASP.NET Core týmu.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Tato funkce vyžaduje ASP.NET Core 2. x cílící na rozhraní .NET Core 2. x.

[Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App) [Metapackage](/dotnet/core/packages#metapackages) pro ASP.NET Core:

* Nezahrnuje závislosti třetích stran kromě [JSON.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [remotion. Linq](https://www.nuget.org/packages/Remotion.Linq/)a [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/). Tyto závislosti třetích stran se považují za nezbytné, aby se zajistilo, že funkce hlavních architektur funguje.
* Zahrnuje všechny podporované balíčky ASP.NET Core týmu s výjimkou těch, které obsahují závislosti třetích stran (jiné než výše zmíněné).
* Zahrnuje všechny podporované balíčky Entity Framework Core týmu s výjimkou těch, které obsahují závislosti třetích stran (jiné než výše zmíněné).

Do balíčku jsou zahrnuté všechny funkce ASP.NET Core 2. x a Entity Framework Core 2. x. `Microsoft.AspNetCore.App` Výchozí šablony projektu, které cílí na ASP.NET Core 2. x, používají tento balíček. Doporučujeme, aby byly aplikace cílené ASP.NET Core 2. x a Entity Framework Core 2. x použijte `Microsoft.AspNetCore.App` balíček.

Číslo verze `Microsoft.AspNetCore.App` Metapackage představuje minimální verzi ASP.NET Core a Entity Framework Core verzi.

Použití `Microsoft.AspNetCore.App` Metapackage poskytuje omezení verze, která chrání vaši aplikaci:

* Pokud je zahrnut balíček, který má přenositelný (nepřímý) závislý na balíčku v nástroji `Microsoft.AspNetCore.App` , přičemž se tato čísla verzí liší, NuGet vygeneruje chybu.
* Jiné balíčky přidané do vaší aplikace nemůžou měnit verzi balíčků zahrnutých v `Microsoft.AspNetCore.App` .
* Konzistence verzí zajišťuje spolehlivé prostředí. `Microsoft.AspNetCore.App`byla navržena tak, aby bránila netestovým kombinacím verzí souvisejících bitů společně ve stejné aplikaci.

Aplikace, které používají `Microsoft.AspNetCore.App` Metapackage, automaticky využijí ASP.NET Core sdílené rozhraní. Při použití `Microsoft.AspNetCore.App` Metapackage nejsou nasazeny **žádné** prostředky z odkazovaného ASP.NET Core balíčků NuGet spolu s aplikací &mdash; , ASP.NET Core sdílená architektura obsahuje tyto prostředky. Prostředky ve sdíleném rozhraní jsou předkompilovány pro zlepšení času spuštění aplikace. Další informace najdete v tématu [sdílené rozhraní](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

Následující soubor projektu odkazuje na `Microsoft.AspNetCore.App` Metapackage pro ASP.NET Core a představuje typickou šablonu ASP.NET Core 2,2:

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

Předchozí kód představuje typickou šablonu ASP.NET Core 2. x. Neurčuje číslo verze `Microsoft.AspNetCore.App` odkazu na balíček. Není-li zadána verze, je [implicitní](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) verze určena sadou SDK, tedy `Microsoft.NET.Sdk.Web` . Doporučujeme, abyste se spoléhali na implicitní verzi určenou sadou SDK a neexplicitně nastavoval číslo verze na odkaz na balíček. Pokud máte na tento přístup nějaké dotazy, ponechte si komentář GitHubu v [diskuzi o implicitní verzi Microsoft. AspNetCore. app](https://github.com/dotnet/AspNetCore.Docs/issues/6430).

Implicitní verze je nastavená na `major.minor.0` pro přenosné aplikace. Mechanismus pro přeposílání sdíleného rozhraní spustí aplikaci na nejnovější kompatibilní verzi z nainstalovaných sdílených rozhraní. Aby bylo zaručeno, že stejná verze se používá ve vývoji, testování a produkčním prostředí, zajistěte, aby byla stejná verze sdíleného rozhraní nainstalovaná ve všech prostředích. U samostatných aplikací je implicitní číslo verze nastaveno na sdílené rozhraní, které je `major.minor.patch` součástí sady SDK.

Zadáním čísla verze na `Microsoft.AspNetCore.App` odkaz nezaručujete, že bude zvolena verze sdíleného rozhraní. **not** Například Předpokládejme, že je zadána verze "2.2.1", ale je nainstalována "2.2.3". V takovém případě bude aplikace používat "2.2.3". I když se to nedoporučuje, můžete zablokovat přeposlání (oprava nebo podverze). Další informace o tom, jak integrovat hostitele dotnet a jak nakonfigurovat jeho chování, najdete v tématu [dotnet Host – přesměrování](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).

::: moniker-end

::: moniker range="= aspnetcore-2.1"

`<Project Sdk`musí být nastaven na hodnotu `Microsoft.NET.Sdk.Web` pro použití implicitní verze `Microsoft.AspNetCore.App` . When `<Project Sdk="Microsoft.NET.Sdk">` (bez koncového `.Web` ) se používá:

* Vygeneruje se následující upozornění:

  *Upozornění NU1604: závislost projektu Microsoft. AspNetCore. app neobsahuje žádné zahrnutí dolní meze. Zahrňte do verze závislosti dolní mez, aby se zajistilo konzistentní výsledky obnovení.*

* Jedná se o známý problém se sadou .NET Core 2,1 SDK.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<a name="update"></a>

## <a name="update-aspnet-core"></a>Aktualizovat ASP.NET Core

`Microsoft.AspNetCore.App` [Metapackage](/dotnet/core/packages#metapackages) není tradiční balíček, který je aktualizovaný z NuGet. Podobně jako `Microsoft.NETCore.App` , `Microsoft.AspNetCore.App` představuje sdílený modul runtime, který má sémantiku se speciálními verzemi zpracovávanými mimo NuGet. Další informace najdete v tématu [balíčky, metabalíčky a rozhraní](/dotnet/core/packages).

Aktualizace ASP.NET Core:

* Ve vývojových počítačích a serverech sestavení: Stáhněte a nainstalujte [.NET Core SDK](https://dotnet.microsoft.com/download).
* Na serverech nasazení: Stáhněte a nainstalujte [modul runtime .NET Core](https://dotnet.microsoft.com/download).

 Aplikace přestanou až na nejnovější nainstalovanou verzi při restartu aplikace. `Microsoft.AspNetCore.App`V souboru projektu není nutné aktualizovat číslo verze. Další informace najdete v tématu [předejte vám aplikace závislé na rozhraní](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).

Pokud se vaše aplikace dřív používala `Microsoft.AspNetCore.All` , přečtěte si téma [migrace z Microsoft. AspNetCore. All do Microsoft. AspNetCore. app](xref:fundamentals/metapackage#migrate).

::: moniker-end

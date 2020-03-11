---
title: Microsoft. AspNetCore. app Metapackage pro ASP.NET Core
author: Rick-Anderson
description: Sdílené rozhraní Microsoft. AspNetCore. app
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/24/2019
uid: fundamentals/metapackage-app
ms.openlocfilehash: 3ce74bc7329a88ffc6f77baf6b8a311c02951318
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78663140"
---
# <a name="microsoftaspnetcoreapp-for-aspnet-core"></a>Microsoft. AspNetCore. app pro ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

 Rozhraní ASP.NET Core Shared Framework (`Microsoft.AspNetCore.App`) obsahuje sestavení vyvinutá a podporovaná společností Microsoft. `Microsoft.AspNetCore.App` se nainstaluje, když je nainstalovaná [sada .NET Core 3,0 nebo novější SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) . *Sdílené rozhraní* je sada sestavení (soubory *. dll* ), které jsou nainstalovány na počítači a zahrnují komponentu modulu runtime a sadu targeting pack. Další informace najdete v tématu [sdílené rozhraní](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

* Projekty, které cílí na sadu SDK `Microsoft.NET.Sdk.Web` implicitně odkazují na `Microsoft.AspNetCore.App` Framework.

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

Do balíčku `Microsoft.AspNetCore.App` jsou zahrnuté všechny funkce ASP.NET Core 2. x a Entity Framework Core 2. x. Výchozí šablony projektu, které cílí na ASP.NET Core 2. x, používají tento balíček. Doporučujeme, abyste pro aplikace cílí na ASP.NET Core 2. x a Entity Framework Core 2. x použití balíčku `Microsoft.AspNetCore.App`.

Číslo verze `Microsoft.AspNetCore.App` Metapackage představuje minimální verzi ASP.NET Core a Entity Framework Core verzi.

Použití `Microsoft.AspNetCore.App` Metapackage poskytuje omezení verze, která chrání vaši aplikaci:

* Pokud je zahrnut balíček, který má přenositelný (nepřímý) závislost na balíčku v `Microsoft.AspNetCore.App`a liší se tato čísla verzí, NuGet vygeneruje chybu.
* Jiné balíčky přidané do vaší aplikace nemůžou měnit verzi balíčků zahrnutých v `Microsoft.AspNetCore.App`.
* Konzistence verzí zajišťuje spolehlivé prostředí. `Microsoft.AspNetCore.App` byla navržena tak, aby nedocházelo k netestovaným kombinacím verzí souvisejících bitů společně ve stejné aplikaci.

Aplikace, které používají `Microsoft.AspNetCore.App` Metapackage, automaticky využijí ASP.NET Core sdílené architektury. Při použití `Microsoft.AspNetCore.App` Metapackage se v aplikaci nesadí **žádné** prostředky z odkazovaných balíčků NuGet ASP.NET Core,&mdash;ASP.NET Core sdílené rozhraní obsahuje tyto prostředky. Prostředky ve sdíleném rozhraní jsou předkompilovány pro zlepšení času spuštění aplikace. Další informace najdete v tématu [sdílené rozhraní](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

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

Předchozí kód představuje typickou šablonu ASP.NET Core 2. x. Neurčuje číslo verze `Microsoft.AspNetCore.App` odkaz na balíček. Není-li zadána verze, je [implicitní](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) verze určena sadou SDK, to znamená `Microsoft.NET.Sdk.Web`. Doporučujeme, abyste se spoléhali na implicitní verzi určenou sadou SDK a neexplicitně nastavoval číslo verze na odkaz na balíček. Pokud máte na tento přístup nějaké dotazy, ponechte si komentář GitHubu v [diskuzi o implicitní verzi Microsoft. AspNetCore. app](https://github.com/dotnet/AspNetCore.Docs/issues/6430).

Implicitní verze je nastavená na `major.minor.0` pro přenosné aplikace. Mechanismus pro přeposílání sdíleného rozhraní spustí aplikaci na nejnovější kompatibilní verzi z nainstalovaných sdílených rozhraní. Aby bylo zaručeno, že stejná verze se používá ve vývoji, testování a produkčním prostředí, zajistěte, aby byla stejná verze sdíleného rozhraní nainstalovaná ve všech prostředích. U aplikací, které jsou v seznamu, je implicitní číslo verze nastavené na `major.minor.patch` sdílených rozhraní, které jsou v nainstalované sadě SDK.

Zadáním čísla verze na odkaz `Microsoft.AspNetCore.App` **nezaručujete** , že bude zvolena verze sdíleného rozhraní. Například Předpokládejme, že je zadána verze "2.2.1", ale je nainstalována "2.2.3". V takovém případě bude aplikace používat "2.2.3". I když se to nedoporučuje, můžete zablokovat přeposlání (oprava nebo podverze). Další informace o tom, jak integrovat hostitele dotnet a jak nakonfigurovat jeho chování, najdete v tématu [dotnet Host – přesměrování](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).

::: moniker-end

::: moniker range="= aspnetcore-2.1"

aby bylo možné používat `Microsoft.AspNetCore.App`implicitní verze, `<Project Sdk` musí být nastavené na `Microsoft.NET.Sdk.Web`. Při použití `<Project Sdk="Microsoft.NET.Sdk">` (bez koncového `.Web`) se používá:

* Vygeneruje se následující upozornění:

  *Upozornění NU1604: závislost projektu Microsoft. AspNetCore. app neobsahuje žádné zahrnutí dolní meze. Zahrňte do verze závislosti dolní mez, aby se zajistilo konzistentní výsledky obnovení.*

* Jedná se o známý problém se sadou .NET Core 2,1 SDK.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<a name="update"></a>

## <a name="update-aspnet-core"></a>Aktualizovat ASP.NET Core

`Microsoft.AspNetCore.App` [Metapackage](/dotnet/core/packages#metapackages) není tradiční balíček, který je aktualizovaný z NuGet. Podobně jako `Microsoft.NETCore.App``Microsoft.AspNetCore.App` představuje sdílený modul runtime, který má sémantiku se speciálními verzemi zpracovávanými mimo NuGet. Další informace najdete v tématu [balíčky, metabalíčky a rozhraní](/dotnet/core/packages).

Aktualizace ASP.NET Core:

* Ve vývojových počítačích a serverech sestavení: Stáhněte a nainstalujte [.NET Core SDK](https://www.microsoft.com/net/download).
* Na serverech nasazení: Stáhněte a nainstalujte [modul runtime .NET Core](https://www.microsoft.com/net/download).

 Aplikace přestanou až na nejnovější nainstalovanou verzi při restartu aplikace. V souboru projektu není nutné aktualizovat číslo verze `Microsoft.AspNetCore.App`. Další informace najdete v tématu [předejte vám aplikace závislé na rozhraní](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).

Pokud vaše aplikace dřív používala `Microsoft.AspNetCore.All`, přečtěte si téma [migrace z Microsoft. AspNetCore. All do Microsoft. AspNetCore. app](xref:fundamentals/metapackage#migrate).

::: moniker-end

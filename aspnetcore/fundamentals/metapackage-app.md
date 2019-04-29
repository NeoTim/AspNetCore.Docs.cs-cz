---
title: Microsoft.AspNetCore.App Microsoft.aspnetcore.all pro ASP.NET Core 2.1 nebo novější
author: Rick-Anderson
description: Microsoft.aspnetcore.All Microsoft.AspNetCore.App zahrnuje všechny podporované balíčky ASP.NET Core a Entity Framework Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/21/2019
uid: fundamentals/metapackage-app
ms.openlocfilehash: 04cd628e2a2e4650fb4396b352aef008b430550e
ms.sourcegitcommit: 8a84ce880b4c40d6694ba6423038f18fc2eb5746
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60165206"
---
# <a name="microsoftaspnetcoreapp-metapackage-for-aspnet-core-21-or-later"></a>Microsoft.AspNetCore.App Microsoft.aspnetcore.all pro ASP.NET Core 2.1 nebo novější

Tato funkce vyžaduje ASP.NET Core 2.1 nebo novější, cílení na .NET Core 2.1 nebo novější.

[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App) [Microsoft.aspnetcore.all](/dotnet/core/packages#metapackages) pro ASP.NET Core:

* Nezahrnuje závislostí třetích stran, s výjimkou [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/), a [IX asynchronní](https://www.nuget.org/packages/System.Interactive.Async/). Tyto závislosti 3rd třetích stran jsou nezbytné k zajištění funkcí funkce hlavní rozhraní.
* Zahrnuje všechny podporované balíčky týmem ASP.NET Core s výjimkou těch, které obsahují závislostí třetích stran (jiné než uvedené výše).
* Zahrnuje všechny podporované balíčky týmem Entity Framework Core s výjimkou těch, které obsahují závislostí třetích stran (jiné než uvedené výše).

Všechny funkce technologie ASP.NET Core 2.1 nebo novější a Entity Framework Core 2.1 a vyšší jsou součástí `Microsoft.AspNetCore.App` balíčku. Výchozí šablony cílení ASP.NET Core 2.1 projektu a později tento balíček použít. Doporučujeme aplikace ASP.NET Core 2.1 nebo novější a Entity Framework Core 2.1 a později použít `Microsoft.AspNetCore.App` balíčku.

Číslo verze `Microsoft.AspNetCore.App` Microsoft.aspnetcore.all představuje verzi technologie ASP.NET Core a Entity Framework Core verze.

Použití `Microsoft.AspNetCore.App` Microsoft.aspnetcore.all poskytuje omezení verze, které chrání vaše aplikace:

* Pokud je součástí balíčku, který obsahuje přechodné (ne přímým přístupem) závislost na balíčku v `Microsoft.AspNetCore.App`a tato čísla verze se liší, NuGet dojde k chybě.
* Další balíčky, přidá do vaší aplikace nelze změnit verzi balíčky, které jsou součástí `Microsoft.AspNetCore.App`.
* Verze konzistence zajišťuje spolehlivé prostředí. `Microsoft.AspNetCore.App` účelem je zabránit kombinace netestované verze související bitů, které jsou právě používány společně ve stejné aplikaci.

Aplikace, které používají `Microsoft.AspNetCore.App` Microsoft.aspnetcore.all automaticky využít sdílené architektuře ASP.NET Core. Při použití `Microsoft.AspNetCore.App` Microsoft.aspnetcore.all, **žádné** nasazení se aplikace používají prostředky z balíčků odkazovaných ASP.NET Core NuGet&mdash;sdílené architektuře ASP.NET Core obsahuje tyto prostředky. Prostředky v rámci sdílené předkompilovány zlepšit dobu spuštění aplikace. Další informace najdete v tématu "sdílené architektuře" [vytváření distribučních balíčků .NET Core](/dotnet/core/build/distribution-packaging).

Následující projekt odkazy na soubory `Microsoft.AspNetCore.App` Microsoft.aspnetcore.all pro ASP.NET Core a představuje typické ASP.NET Core 2.1 šablony:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.1</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

</Project>
```

Předchozí kód představuje typické ASP.NET Core 2.1 a vyšší šablony. Neurčuje číslo verze `Microsoft.AspNetCore.App` odkaz na balíček. Pokud není zadána verze, [implicitní](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) ale není určená verze sady SDK, tedy `Microsoft.NET.Sdk.Web`. Doporučujeme, abyste spoléhat na implicitní verze určené sady SDK a nastavení nejsou explicitně číslo verze na odkaz na balíček. Pokud máte otázky k tomu, ponechte v Githubu komentář [diskuse implicitní verze Microsoft.AspNetCore.App](https://github.com/aspnet/Docs/issues/6430).

Implicitní verze je nastaveno na `major.minor.0` pro přenosné aplikace. Mechanismus vpřed sdílené architektuře se spustí aplikaci na nejnovější verzi kompatibilní mezi nainstalované rozhraní sdílené. Chcete-li zaručit, že při vývoji se používá stejnou verzi, testovacím i produkčním prostředí, ujistěte se, že stejná verze modulu sdílené framework je nainstalována ve všech prostředích. Pro aplikace uzavřeným číslo implicitní verze nastavena na `major.minor.patch` sdílené Framework dodávat v nainstalované sady SDK.

Určení číslo verze na `Microsoft.AspNetCore.App` nemá odkaz **není** zaručit, že tato verze sdíleného vybere se rozhraní framework. Předpokládejme například, ale není určená verze "2.1.1", ale je nainstalovaná "2.1.3". V takovém případě bude aplikace používat "2.1.3". Však není doporučena, můžete zakázat Posunutí vpřed (opravy a/nebo menší). Další informace týkající se příkaz dotnet hostitele vpřed a jak nakonfigurovat své chování najdete v tématu [dotnet hostitele dopředné posunutí](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).

::: moniker range="= aspnetcore-2.1"

`<Project Sdk` musí být nastaveno na `Microsoft.NET.Sdk.Web` použít implicitní verze `Microsoft.AspNetCore.App`. Když `<Project Sdk="Microsoft.NET.Sdk">` (bez koncových `.Web`) se používá:

* Generují se následující upozornění:

  *Upozornění NU1604: Závislosti projektu Microsoft.AspNetCore.App neobsahuje inkluzivní dolní mez. Dolní mez zahrňte do verze závislosti zajistit výsledky obnovení budou konzistentní.*

* Jde o známý problém s .NET Core 2.1 SDK.

::: moniker-end

<a name="update"></a>

## <a name="update-aspnet-core"></a>Aktualizace ASP.NET Core

`Microsoft.AspNetCore.App` [Microsoft.aspnetcore.all](/dotnet/core/packages#metapackages) není tradiční balíček, který je aktualizována z NuGet. Podobně jako `Microsoft.NETCore.App`, `Microsoft.AspNetCore.App` představuje sdílený modul runtime, který má zpracovat mimo NuGet sémantiku speciální správy verzí. Další informace najdete v tématu [balíčky, metabalíčky a architektury](/dotnet/core/packages).

Chcete-li aktualizovat ASP.NET Core:

* Na počítačích vývojářů a buildovací servery: Stáhněte a nainstalujte [.NET Core SDK](https://www.microsoft.com/net/download).
* Na serverech nasazení: Stáhněte a nainstalujte [.NET Core runtime](https://www.microsoft.com/net/download).

 Aplikace se posunout vpřed byla nejnovější nainstalovaná verze na restartování aplikace. Není potřeba aktualizovat `Microsoft.AspNetCore.App` číslo verze v souboru projektu. Další informace najdete v tématu [aplikace závisí na architektuře posunout vpřed](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).

Pokud vaše aplikace už dřív použili `Microsoft.AspNetCore.All`, naleznete v tématu [migrace z metabalíček na Microsoft.AspNetCore.App](xref:fundamentals/metapackage#migrate).

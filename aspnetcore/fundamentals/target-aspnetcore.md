---
title: Použití ASP.NET základních api v knihovně tříd
author: scottaddie
description: Přečtěte si, jak používat ASP.NET základní api v knihovně tříd.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
no-loc:
- Blazor
uid: fundamentals/target-aspnetcore
ms.openlocfilehash: 5374d7eec4334223a4bba7ee26cb6e2f208ed20b
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977194"
---
# <a name="use-aspnet-core-apis-in-a-class-library"></a>Použití ASP.NET základních api v knihovně tříd

Podle [Scott Addie](https://github.com/scottaddie)

Tento dokument obsahuje pokyny pro použití ASP.NET základní api v knihovně tříd. Všechny ostatní pokyny ke knihovně najdete [v tématu Pokyny k knihovně s otevřeným zdrojovým kódem](/dotnet/standard/library-guidance/).

## <a name="determine-which-aspnet-core-versions-to-support"></a>Určení, které verze ASP.NET Core podporují

ASP.NET Core dodržuje [zásady podpory jádra .NET](https://dotnet.microsoft.com/platform/support/policy/dotnet-core). Při určování, které verze ASP.NET Core pro podporu v knihovně, naleznete v zásadách podpory. Knihovna by měla:

* Vynassnažit se podporovat všechny ASP.NET základní verze klasifikované jako *dlouhodobá podpora* (LTS).
* Necítí povinnost podporovat ASP.NET core verze klasifikované jako *Konec životnosti* (EOL).

Jako náhled verze ASP.NET Core jsou k dispozici, narušující změny jsou zveřejněny v úložišti [GitHub aspnet/Announcements.](https://github.com/aspnet/Announcements/issues) Testování kompatibility knihoven lze provádět při vývoji funkcí architektury.

## <a name="use-the-aspnet-core-shared-framework"></a>Použití sdíleného rámce ASP.NET Core

S vydáním .NET Core 3.0 mnoho ASP.NET sestavení core již nejsou publikovány nuget jako balíčky. Místo toho jsou sestavení zahrnuta do `Microsoft.AspNetCore.App` sdíleného rámce, který je nainstalován s instalačními programy .NET Core SDK a runtime. Seznam balíčků, které již nejsou publikovány, naleznete v tématu [Odebrání zastaralých odkazů na balíčky](xref:migration/22-to-30#remove-obsolete-package-references).

Od rozhraní .NET Core 3.0 `Microsoft.NET.Sdk.Web` projekty pomocí sady MSBuild SDK implicitně odkazují na sdílený rámec. Projekty `Microsoft.NET.Sdk` používající sadu `Microsoft.NET.Sdk.Razor` SDK musí odkazovat ASP.NET core, aby bylo nutné používat rozhraní API ASP.NET jádra ve sdíleném rámci.

Chcete-li odkazovat ASP.NET `<FrameworkReference>` jádro, přidejte do souboru projektu následující prvek:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

Odkazování ASP.NET jádra tímto způsobem je podporováno pouze pro projekty zaměřené na rozhraní .NET Core 3.x.

## <a name="include-blazor-extensibility"></a>Včetně rozšiřitelnosti Blazoru

Blazor podporuje webassembly (WASM) a server [hosting modely](xref:blazor/hosting-models). Pokud neexistuje konkrétní důvod, proč ne, knihovna [komponent Razor](xref:blazor/components) by měla podporovat oba hostitelské modely. Knihovna komponent Razor musí používat sadu [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).

### <a name="support-both-hosting-models"></a>Podpora obou hostingových modelů

Chcete-li podpořit spotřebu komponent Razor z projektů [Blazor Server](xref:blazor/hosting-models#blazor-server) i [Blazor WASM,](xref:blazor/hosting-models#blazor-webassembly) použijte následující pokyny pro váš editor.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Použijte šablonu projektu **Knihovna tříd Razor.** Zaškrtávací políčko **Stránky podpory a zobrazení** šablony by mělo být zaškrtnuto.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

V integrovaném terminálu spusťte následující příkaz:

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Použijte šablonu projektu **Knihovna tříd Razor.**

---

Projekt generovaný ze šablony provádí následující akce:

* Cíle .NET Standard 2.0.
* Nastaví `RazorLangVersion` vlastnost `3.0`na . `3.0`je výchozí hodnota pro rozhraní .NET Core 3.x.
* Přidá následující odkazy na balíček:
  * [Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)
  * [Microsoft.AspNetCore.Components.Web](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)

Příklad:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a>Podpora konkrétního modelu hostování

Je mnohem méně časté podporovat jeden model hostingu Blazor. Jako příklad pro podporu spotřeby komponent Razor z projektů [Blazor Server](xref:blazor/hosting-models#blazor-server) pouze:

* Cíl .NET Core 3.x.
* Přidejte `<FrameworkReference>` prvek pro sdílené rozhraní.

Příklad:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

Další informace o knihovnách obsahujících komponenty Razor naleznete [v tématu ASP.NET knihovny tříd komponent Core Razor](xref:blazor/class-libraries).

## <a name="include-mvc-extensibility"></a>Zahrnout rozšiřitelnost MVC

Tato část popisuje doporučení pro knihovny, které zahrnují:

* [Zobrazení břitva nebo Razor Stránky](#razor-views-or-razor-pages)
* [Pomocné rutiny značek](#tag-helpers)
* [Komponenty zobrazení](#view-components)

Tato část nepopisuje vícenásobné cílení pro podporu více verzí MVC. Pokyny k podpoře více verzí ASP.NET Core naleznete v tématu [Podpora více verzí ASP.NET core](#support-multiple-aspnet-core-versions).

### <a name="razor-views-or-razor-pages"></a>Zobrazení břitva nebo Razor Stránky

Projekt, který obsahuje [zobrazení Razor](xref:mvc/views/overview) nebo [Razor Pages,](xref:razor-pages/index) musí používat sadu [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).

Pokud se projekt zaměřuje na rozhraní .NET Core 3.x, vyžaduje:

* Vlastnost `AddRazorSupportForMvc` MSBuild nastavená na `true`.
* Prvek `<FrameworkReference>` pro sdílené rozhraní.

Šablona projektu **Knihovna tříd y razor** splňuje předchozí požadavky pro projekty zaměřené na rozhraní .NET Core 3.x. Pro editor použijte následující pokyny.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Použijte šablonu projektu **Knihovna tříd Razor.** Je třeba zaškrtnout **políčko Stránky podpory a zobrazení** šablony.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

V integrovaném terminálu spusťte následující příkaz:

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

V tuto chvíli není podpora šablony projektu.

---

Příklad:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

Pokud projekt místo toho cílí na standard .NET, je vyžadován odkaz na balíček [Microsoft.AspNetCore.Mvc.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) Balíček `Microsoft.AspNetCore.Mvc` se přesunul do sdíleného rámce v ASP.NET jádrem 3.0, a proto již není zveřejňován. Příklad:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a>Pomocné rutiny značek

Projekt, který obsahuje [pomocné spoje značek,](xref:mvc/views/tag-helpers/intro) by měl používat sadu `Microsoft.NET.Sdk` SDK. Pokud cílení .NET Core 3.x, přidejte `<FrameworkReference>` prvek pro sdílené rozhraní. Příklad:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

Pokud cílení .NET Standard (pro podporu verzí starších než ASP.NET Core 3.x), přidejte odkaz na balíček [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor). Balíček byl `Microsoft.AspNetCore.Mvc.Razor` přesunut do sdíleného rámce, a proto již není zveřejňován. Příklad:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a>Komponenty zobrazení

Projekt, který obsahuje [komponenty](xref:mvc/views/view-components) `Microsoft.NET.Sdk` Zobrazení by měl používat sadu SDK. Pokud cílení .NET Core 3.x, přidejte `<FrameworkReference>` prvek pro sdílené rozhraní. Příklad:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

Pokud cílení .NET Standard (pro podporu verzí starších než ASP.NET Core 3.x), přidejte odkaz na balíček [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures). Balíček byl `Microsoft.AspNetCore.Mvc.ViewFeatures` přesunut do sdíleného rámce, a proto již není zveřejňován. Příklad:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a>Podpora více verzí ASP.NET Core

K vytvoření knihovny, která podporuje více variant ASP.NET Core, je vyžadováno více násobné cílení. Zvažte scénář, ve kterém musí knihovna pomocníků značek podporovat následující ASP.NET varianty jádra:

* ASP.NET zaměření jádra 2.1 na rozhraní .NET Framework 4.6.1
* ASP.NET Cílení na jádro 2.x .NET Core 2.x
* ASP.NET Cílení na jádro 3.x .NET Core 3.x

Následující soubor projektu podporuje tyto `TargetFrameworks` varianty prostřednictvím vlastnosti:

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

S předchozím souborem projektu:

* Balíček `Markdig` je přidán pro všechny spotřebitele.
* Odkaz na [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) je přidán pro spotřebitele cílení .NET Framework 4.6.1 nebo novější nebo .NET Core 2.x. Verze 2.1.0 balíčku pracuje s ASP.NET Core 2.2 z důvodu zpětné kompatibility.
* Sdílený rámec je odkazován pro spotřebitele cílení .NET Core 3.x. Balíček `Microsoft.AspNetCore.Mvc.Razor` je součástí sdíleného rámce.

Alternativně může být cílená norma .NET 2.0 namísto cílení na rozhraní .NET Core 2.1 a .NET Framework 4.6.1:

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

S předchozím souborem projektu existují následující upozornění:

* Vzhledem k tomu, že knihovna obsahuje pouze pomocné spoje značek, je jednodušší cílit na konkrétní platformy, na kterých ASP.NET Core běží: .NET Core a .NET Framework. Pomocné spoje značek nelze použít jinými cílovými rámci kompatibilními se standardem .NET Standard 2.0, jako je Unity, UPW a Xamarin.
* Použití rozhraní .NET Standard 2.0 z rozhraní .NET Framework má některé problémy, které byly vyřešeny v rozhraní .NET Framework 4.7.2. Můžete zlepšit prostředí pro spotřebitele pomocí rozhraní .NET Framework 4.6.1 až 4.7.1 cílením na rozhraní .NET Framework 4.6.1.

Pokud vaše knihovna potřebuje volat rozhraní API specifická pro platformu, zacilte na konkrétní implementace rozhraní .NET namísto standardu .NET. Další informace naleznete [v tématu Multi-targeting](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).

## <a name="use-an-api-that-hasnt-changed"></a>Použití rozhraní API, které se nezměnilo

Představte si scénář, ve kterém upgradujete middlewarovou knihovnu z .NET Core 2.2 na 3.0. Mezi ASP.NET Core 2.2 a 3.0 se nezměnila ASP.NET middlewarová api, která se používají v knihovně. Chcete-li pokračovat v podpoře knihovny middlewaru v rozhraní .NET Core 3.0, postupujte takto:

* Postupujte podle [standardních pokynů pro knihovnu](/dotnet/standard/library-guidance/).
* Přidejte odkaz na balíček pro každý balíček NuGet každého rozhraní API, pokud odpovídající sestavení neexistuje ve sdíleném rámci.

## <a name="use-an-api-that-changed"></a>Použití rozhraní API, které se změnilo

Představte si scénář, ve kterém upgradujete knihovnu z .NET Core 2.2 na .NET Core 3.0. Rozhraní ASP.NET Core API používané v knihovně má [zásadní změnu](/dotnet/core/compatibility/breaking-changes) v ASP.NET Core 3.0. Zvažte, zda lze knihovnu přepsat tak, aby nepoužívala poškozené rozhraní API ve všech verzích.

Pokud můžete přepsat knihovnu, učiňte tak a pokračujte v cílení na dřívější cílovou architekturu (například .NET Standard 2.0 nebo .NET Framework 4.6.1) s odkazy na balíčky.

Pokud knihovnu nemůžete přepsat, postupujte takto:

* Přidejte cíl pro rozhraní .NET Core 3.0.
* Přidejte `<FrameworkReference>` prvek pro sdílené rozhraní.
* Použijte [direktivu preprocesoru #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) s příslušným symbolem cílového rozhraní k podmíněné kompilaci kódu.

Například synchronní čtení a zápisy na požadavek HTTP a datové proudy odpovědí jsou ve výchozím nastavení zakázány od ASP.NET Jádrem 3.0. ASP.NET Core 2.2 podporuje synchronní chování ve výchozím nastavení. Zvažte middleware knihovnu, ve které synchronní čtení a zápisy by měly být povoleny, kde dochází vstupně-in. Knihovna by měla uzavřete kód povolit synchronní funkce v příslušné direktivě preprocesoru. Příklad:

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a>Použití rozhraní API zavedeného v 3.0

Představte si, že chcete použít rozhraní API ASP.NET Core, které bylo zavedeno v ASP.NET Core 3.0. Zvažte následující otázky:

1. Je knihovna funkčně vyžadovat nové rozhraní API?
1. Může knihovna implementovat tuto funkci jiným způsobem?

Pokud knihovna funkčně vyžaduje rozhraní API a neexistuje žádný způsob, jak implementovat nižší úroveň:

* Cíl .NET Core 3.x pouze.
* Přidejte `<FrameworkReference>` prvek pro sdílené rozhraní.

Pokud knihovna může implementovat funkci jiným způsobem:

* Přidejte rozhraní .NET Core 3.x jako cílovou architekturu.
* Přidejte `<FrameworkReference>` prvek pro sdílené rozhraní.
* Použijte [direktivu preprocesoru #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) s příslušným symbolem cílového rozhraní k podmíněné kompilaci kódu.

Například následující pomocník značek používá <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> rozhraní zavedené v ASP.NET Core 3.0. Spotřebitelé cílení .NET Core 3.0 spustit `NETCOREAPP3_0` cestu kódu definované symbolem cílové ho rozhraní. Typ parametru konstruktoru pomocného <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> objektu značky se změní na pro spotřebitele rozhraní .NET Core 2.1 a .NET Framework 4.6.1. Tato změna byla nutná, protože `IHostingEnvironment` ASP.NET Core `IWebHostEnvironment` 3.0 označeny jako zastaralé a doporučené jako náhrada.

```csharp
[HtmlTargetElement("script", Attributes = "asp-inline")]
public class ScriptInliningTagHelper : TagHelper
{
    private readonly IFileProvider _wwwroot;

#if NETCOREAPP3_0
    public ScriptInliningTagHelper(IWebHostEnvironment env)
#else
    public ScriptInliningTagHelper(IHostingEnvironment env)
#endif
    {
        _wwwroot = env.WebRootFileProvider;
    }

    // code omitted for brevity
}
```

Následující víceúčelový soubor projektu podporuje tento scénář pomocníka pro značky:

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a>Použití rozhraní API odebrané ze sdíleného rozhraní

Chcete-li použít ASP.NET sestavení Core, které bylo odebráno ze sdíleného rozhraní, přidejte příslušný odkaz na balíček. Seznam balíčků odebraných ze sdíleného rozhraní v ASP.NET jádrem 3.0 naleznete v tématu [Odebrání zastaralých odkazů na balíčky](xref:migration/22-to-30#remove-obsolete-package-references).

Chcete-li například přidat klienta webového rozhraní API:

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <FrameworkReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNet.WebApi.Client" Version="5.2.7" />
  </ItemGroup>

</Project>
```

## <a name="additional-resources"></a>Další zdroje

* <xref:razor-pages/ui-class>
* <xref:blazor/class-libraries>
* [Podpora implementace rozhraní .NET](/dotnet/standard/net-standard#net-implementation-support)
* [Zásady podpory rozhraní .NET](https://dotnet.microsoft.com/platform/support/policy)

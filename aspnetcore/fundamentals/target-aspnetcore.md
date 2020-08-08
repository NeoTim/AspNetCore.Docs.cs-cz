---
title: Použití rozhraní API ASP.NET Core v knihovně tříd
author: scottaddie
description: Naučte se používat rozhraní API ASP.NET Core v knihovně tříd.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/target-aspnetcore
ms.openlocfilehash: a6c20c61f7f33706e620fcbc239b2cc4bace15f0
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016489"
---
# <a name="use-aspnet-core-apis-in-a-class-library"></a>Použití rozhraní API ASP.NET Core v knihovně tříd

[Scott Addie](https://github.com/scottaddie)

Tento dokument poskytuje pokyny pro použití rozhraní API ASP.NET Core v knihovně tříd. Všechny ostatní doprovodné materiály ke knihovně naleznete v tématu [Open-Source doprovodné](/dotnet/standard/library-guidance/)materiály.

## <a name="determine-which-aspnet-core-versions-to-support"></a>Určete, které verze ASP.NET Core se mají podporovat.

ASP.NET Core dodržuje [zásady podpory .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core). Při určování, které verze ASP.NET Core se mají podporovat v knihovně, se podívejte na zásady podpory. Knihovna by měla:

* Udělejte úsilí, aby podporovalo všechny ASP.NET Core verze klasifikované jako *Dlouhodobá podpora* (LTS).
* Nejedná se o povinnost podporovat ASP.NET Core verze klasifikované jako *konec života* (konce řádku).

V rámci verze Preview ASP.NET Core k dispozici jsou zásadní změny publikované v úložišti GitHub [ASPNET/oznámení](https://github.com/aspnet/Announcements/issues) . Testování kompatibility knihoven se dá provádět jako vývoj funkcí rozhraní.

## <a name="use-the-aspnet-core-shared-framework"></a>Použití sdíleného rozhraní ASP.NET Core

S vydáním .NET Core 3,0 se už mnoho ASP.NET Corech sestavení nepublikují do NuGet jako balíčky. Místo toho jsou sestavení zahrnuta do `Microsoft.AspNetCore.App` sdíleného rozhraní, které je nainstalováno s instalačními programy .NET Core SDK a modulem runtime. Seznam balíčků, které už nejsou publikované, najdete v tématu [odebrání zastaralých odkazů na balíčky](xref:migration/22-to-30#remove-obsolete-package-references).

Od .NET Core 3,0 projekty, které používají `Microsoft.NET.Sdk.Web` sadu MSBuild SDK, implicitně odkazují na sdílené rozhraní. Projekty, které `Microsoft.NET.Sdk` používají `Microsoft.NET.Sdk.Razor` sadu SDK nebo, musí odkazovat ASP.NET Core, aby používaly rozhraní API ASP.NET Core ve sdíleném rozhraní.

Chcete-li odkazovat ASP.NET Core, přidejte následující `<FrameworkReference>` prvek do souboru projektu:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

Odkazování ASP.NET Core tímto způsobem je podporováno pouze pro projekty cílené na .NET Core 3. x.

## <a name="include-no-locblazor-extensibility"></a>Zahrnout Blazor rozšiřitelnost

Blazorpodporuje WebAssembly (WASM) a [modely hostování](xref:blazor/hosting-models)serverů. Pokud neexistuje konkrétní důvod, knihovna [ Razor komponent](xref:blazor/components/index) by měla podporovat oba modely hostování. RazorKnihovna komponent musí používat [Microsoft. NET. SDK. Razor Sada SDK](xref:razor-pages/sdk).

### <a name="support-both-hosting-models"></a>Podpora obou modelů hostování

Pro podporu Razor spotřeby komponent z [Blazor Server](xref:blazor/hosting-models#blazor-server) projektů i [ Blazor WASM](xref:blazor/hosting-models#blazor-webassembly) použijte následující pokyny pro Editor.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Použijte šablonu projektu ** Razor Knihovna tříd** . Zaškrtávací políčko pro **stránky podpory a zobrazení** šablony je třeba zrušit výběr.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

V integrovaném terminálu spusťte následující příkaz:

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Použijte šablonu projektu ** Razor Knihovna tříd** .

---

Projekt vygenerovaný ze šablony provádí následující akce:

* Cíle .NET Standard 2,0.
* Nastaví `RazorLangVersion` vlastnost na hodnotu `3.0` . `3.0`je výchozí hodnota pro .NET Core 3. x.
* Přidá následující odkazy na balíčky:
  * [Microsoft. AspNetCore. Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)
  * [Microsoft. AspNetCore. Components. Web](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)

Například:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a>Podpora konkrétního modelu hostování

Je mnohem méně běžné podporovat jeden Blazor model hostování. Například pro podporu Razor spotřeby komponent [Blazor Server](xref:blazor/hosting-models#blazor-server) pouze z projektů:

* Cílová platforma .NET Core 3. x.
* Přidejte `<FrameworkReference>` element pro sdílené rozhraní.

Například:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

Další informace o knihovnách, které obsahují Razor komponenty, naleznete v tématu [ASP.NET Core Razor knihovny tříd komponent](xref:blazor/components/class-libraries).

## <a name="include-mvc-extensibility"></a>Zahrnutí rozšiřitelnosti MVC

V této části najdete popis doporučení pro knihovny, které zahrnují:

* [Razorzobrazení nebo Razor stránky](#razor-views-or-razor-pages)
* [Pomocné rutiny značek](#tag-helpers)
* [Komponenty zobrazení](#view-components)

Tato část se nezabývá cílením na podporu více verzí MVC. Pokyny k podpoře více verzí ASP.NET Core najdete v tématu [Podpora více verzí ASP.NET Core](#support-multiple-aspnet-core-versions).

### <a name="no-locrazor-views-or-no-locrazor-pages"></a>Razorzobrazení nebo Razor stránky

Projekt, který obsahuje [ Razor zobrazení](xref:mvc/views/overview) nebo [ Razor stránky](xref:razor-pages/index) , musí používat [Microsoft. NET. SDK. Razor Sada SDK](xref:razor-pages/sdk).

Pokud je projekt cílen na rozhraní .NET Core 3. x, vyžaduje:

* `AddRazorSupportForMvc`Vlastnost MSBuild nastavená na `true` .
* `<FrameworkReference>`Prvek pro sdílenou architekturu.

Šablona projektu ** Razor knihovny tříd** splňuje předchozí požadavky pro projekty cílené na .NET Core 3. x. Pro editor použijte následující pokyny.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Použijte šablonu projektu ** Razor Knihovna tříd** . Je třeba vybrat zaškrtávací políčko **stránky podpory a zobrazení** šablony.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

V integrovaném terminálu spusťte následující příkaz:

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

V tuto chvíli není podporována žádná podpora šablon projektů.

---

Například:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

Pokud se místo toho projekt cílí .NET Standard, je vyžadován odkaz na balíček [Microsoft. AspNetCore. Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) . `Microsoft.AspNetCore.Mvc`Balíček se přesunul do sdíleného rozhraní v ASP.NET Core 3,0, a proto už není publikovaný. Například:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a>Pomocné rutiny značek

Projekt, který obsahuje [pomocníky značek](xref:mvc/views/tag-helpers/intro) , by měl používat `Microsoft.NET.Sdk` sadu SDK. Pokud cílíte na rozhraní .NET Core 3. x, přidejte `<FrameworkReference>` element pro sdílené rozhraní. Například:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

Pokud cílíte na .NET Standard (pro podporu verzí starších než ASP.NET Core 3. x), přidejte odkaz na balíček do [Microsoft. AspNetCore. Razor MVC.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor). `Microsoft.AspNetCore.Mvc.Razor`Balíček se přesunul do sdíleného rozhraní, takže už není publikovaný. Například:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a>Komponenty zobrazení

Projekt, který obsahuje [komponenty zobrazení](xref:mvc/views/view-components) , by měl používat `Microsoft.NET.Sdk` sadu SDK. Pokud cílíte na rozhraní .NET Core 3. x, přidejte `<FrameworkReference>` element pro sdílené rozhraní. Například:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

Pokud cílíte .NET Standard (pro podporu verzí starších než ASP.NET Core 3. x), přidejte odkaz na balíček do [Microsoft. AspNetCore. Mvc. ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures). `Microsoft.AspNetCore.Mvc.ViewFeatures`Balíček se přesunul do sdíleného rozhraní, takže už není publikovaný. Například:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a>Podpora více verzí ASP.NET Core

Aby bylo možné vytvořit knihovnu, která podporuje více variant ASP.NET Core, vyžaduje se cílení na více verzí. Vezměte v úvahu scénář, ve kterém musí knihovna pomocníků značek podporovat následující ASP.NET Core varianty:

* ASP.NET Core 2,1 cílení na .NET Framework 4.6.1
* ASP.NET Core 2. x cílící na .NET Core 2. x
* ASP.NET Core 3. x cílící na .NET Core 3. x

Následující soubor projektu podporuje tyto varianty prostřednictvím `TargetFrameworks` vlastnosti:

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

S předchozím souborem projektu:

* `Markdig`Balíček se přidá pro všechny uživatele.
* Odkaz na [Microsoft. AspNetCore. Mvc. Razor ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) je přidáno pro uživatele cílící na .NET Framework 4.6.1 nebo novější nebo .NET Core 2. x. 2.1.0 verze balíčku spolupracuje s ASP.NET Core 2,2 z důvodu zpětné kompatibility.
* Na sdílené rozhraní se odkazuje pro uživatele, kteří cílí na .NET Core 3. x. `Microsoft.AspNetCore.Mvc.Razor`Balíček je součástí sdílené architektury.

Alternativně můžete .NET Standard 2,0 cílit místo cílení na rozhraní .NET Core 2,1 i .NET Framework 4.6.1:

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

V předchozím souboru projektu jsou k dispozici následující upozornění:

* Vzhledem k tomu, že knihovna obsahuje pouze pomocníky značek, je snazší zaměřit se na konkrétní platformy, na kterých ASP.NET Core běžet: .NET Core a .NET Framework. Pomocníky značek nemůžou používat jiné cílové architektury kompatibilní s .NET Standard 2,0, jako je Unity, UWP a Xamarin.
* Použití .NET Standard 2,0 z .NET Framework obsahuje některé problémy, které byly vyřešeny v .NET Framework 4.7.2. Pro uživatele můžete vylepšit prostředí pomocí .NET Framework 4.6.1 prostřednictvím 4.7.1, a to tak, že zacílíte .NET Framework 4.6.1.

Pokud vaše knihovna potřebuje volat rozhraní API specifická pro platformu, místo .NET Standard pro konkrétní cílové implementace .NET. Další informace najdete v tématu [cílení na více](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting)platforem.

## <a name="use-an-api-that-hasnt-changed"></a>Použití rozhraní API, které se nezměnilo

Představte si situaci, ve které upgradujete knihovnu middlewaru z .NET Core 2,2 na 3,0. Rozhraní API middleware ASP.NET Core používaná v knihovně se mezi ASP.NET Core 2,2 a 3,0 nezměnila. Chcete-li pokračovat v podpoře knihovny middlewaru v rozhraní .NET Core 3,0, proveďte následující kroky:

* Řiďte se [standardními pokyny ke knihovně](/dotnet/standard/library-guidance/).
* Přidejte odkaz na balíček pro každý balíček NuGet rozhraní API, pokud odpovídající sestavení neexistuje ve sdíleném rozhraní.

## <a name="use-an-api-that-changed"></a>Použití rozhraní API, které se změnilo

Představte si situaci, ve které upgradujete knihovnu ze sady .NET Core 2,2 na .NET Core 3,0. ASP.NET Core rozhraní API, které se používá v knihovně, má zásadní [změnu](/dotnet/core/compatibility/breaking-changes) v ASP.NET Core 3,0. Zvažte, zda lze knihovnu přepsat, aby nepoužívala poškozené rozhraní API ve všech verzích.

Pokud můžete knihovnu přepsat, udělejte to a pokračujte do cílení na dřívější cílové rozhraní (například .NET Standard 2,0 nebo .NET Framework 4.6.1) s odkazy na balíčky.

Pokud knihovnu nelze přepsat, proveďte následující kroky:

* Přidejte cíl pro .NET Core 3,0.
* Přidejte `<FrameworkReference>` element pro sdílené rozhraní.
* Pomocí [direktivy preprocesoru #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) s příslušným cílovým symbolem rozhraní můžete podmíněně kompilovat kód.

Například synchronní čtení a zápisy v případě požadavků HTTP a datových proudů odpovědí jsou ve výchozím nastavení zakázány ASP.NET Core 3,0. ASP.NET Core 2,2 ve výchozím nastavení podporuje synchronní chování. Vezměte v úvahu knihovnu middlewaru, ve které by měla být povolena synchronní čtení a zápisy, kde se vyskytuje vstup/výstup. Knihovna by měla uzavřít kód, aby povolovala synchronní funkce v příslušné direktivě preprocesoru. Například:

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a>Použití rozhraní API představeného v 3,0

Představte si, že chcete použít ASP.NET Core rozhraní API, které bylo zavedeno v ASP.NET Core 3,0. Zvažte následující otázky:

1. Vyžaduje Knihovna funkce nové rozhraní API?
1. Může knihovna tuto funkci implementovat jiným způsobem?

Pokud knihovna funkci vyžaduje rozhraní API a neexistuje žádný způsob, jak ji implementovat na nižší úroveň:

* Pouze cílový .NET Core 3. x.
* Přidejte `<FrameworkReference>` element pro sdílené rozhraní.

Pokud knihovna může implementovat funkci jiným způsobem:

* Přidejte .NET Core 3. x jako cílovou architekturu.
* Přidejte `<FrameworkReference>` element pro sdílené rozhraní.
* Pomocí [direktivy preprocesoru #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) s příslušným cílovým symbolem rozhraní můžete podmíněně kompilovat kód.

Například následující pomocná značka používá <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> rozhraní představené v ASP.NET Core 3,0. Spotřebitelé cílící na .NET Core 3,0 spustí cestu kódu definovanou `NETCOREAPP3_0` symbolem cílového Frameworku. Změny typu parametru konstruktoru pomocné rutiny značky se mění pro <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> uživatele .NET Core 2,1 a .NET Framework 4.6.1. Tato změna byla nezbytná, protože ASP.NET Core 3,0 označená `IHostingEnvironment` jako zastaralá a doporučuje se `IWebHostEnvironment` jako náhrada.

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

Tento scénář pomocníka značek podporuje následující soubor projektu s více cíli:

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a>Použití rozhraní API odebraného ze sdíleného rozhraní

Chcete-li použít ASP.NET Core sestavení, které bylo odebráno ze sdíleného rozhraní, přidejte příslušný odkaz na balíček. Seznam balíčků odebraných ze sdíleného rozhraní v ASP.NET Core 3,0 najdete v tématu [odebrání zastaralých odkazů na balíčky](xref:migration/22-to-30#remove-obsolete-package-references).

Například pro přidání klienta webového rozhraní API:

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
* <xref:blazor/components/class-libraries>
* [Podpora implementace rozhraní .NET](/dotnet/standard/net-standard#net-implementation-support)
* [Zásady podpory rozhraní .NET](https://dotnet.microsoft.com/platform/support/policy)

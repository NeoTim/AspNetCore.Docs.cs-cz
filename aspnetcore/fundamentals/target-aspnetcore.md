---
title: Použití rozhraní API ASP.NET Core v knihovně tříd
author: scottaddie
description: Naučte se používat rozhraní API ASP.NET Core v knihovně tříd.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
no-loc:
- Blazor
uid: fundamentals/target-aspnetcore
ms.openlocfilehash: 72096fc2f03033dfe8325b5129e074913a2fbd1f
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75463886"
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

S vydáním .NET Core 3,0 se už mnoho ASP.NET Corech sestavení nepublikují do NuGet jako balíčky. Místo toho jsou sestavení zahrnuta ve `Microsoft.AspNetCore.App` sdíleném rozhraním, které je nainstalováno s instalačními programy .NET Core SDK a modulu runtime. Seznam balíčků, které už nejsou publikované, najdete v tématu [odebrání zastaralých odkazů na balíčky](xref:migration/22-to-30#remove-obsolete-package-references).

Od .NET Core 3,0 projekty, které používají `Microsoft.NET.Sdk.Web` MSBuild SDK, implicitně odkazují na sdílené rozhraní. Projekty používající `Microsoft.NET.Sdk` nebo `Microsoft.NET.Sdk.Razor` SDK musí odkazovat ASP.NET Core na použití rozhraní API ASP.NET Core ve sdíleném rozhraní.

Chcete-li odkazovat ASP.NET Core, přidejte následující `<FrameworkReference>` prvek do souboru projektu:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

Odkazování ASP.NET Core tímto způsobem je podporováno pouze pro projekty cílené na .NET Core 3. x.

## <a name="include-opno-locblazor-extensibility"></a>Zahrnout rozšíření Blazor

Blazor podporuje WebAssembly (WASM) a [hostitelské modely](xref:blazor/hosting-models)serveru. Pokud neexistuje konkrétní důvod, knihovna [komponent Razor](xref:blazor/components) by měla podporovat oba modely hostování. Knihovna komponent Razor musí používat [sadu Microsoft. NET. SDK. Razor SDK](xref:razor-pages/sdk).

### <a name="support-both-hosting-models"></a>Podpora obou modelů hostování

Pro podporu spotřeby komponent Razor z [Blazorho serveru](xref:blazor/hosting-models#blazor-server) i [Blazor projektů WASM](xref:blazor/hosting-models#blazor-webassembly) použijte následující pokyny pro Editor.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Použijte šablonu projektu **knihovny tříd Razor** . Zaškrtávací políčko pro **stránky podpory a zobrazení** šablony je třeba zrušit výběr.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

V integrovaném terminálu spusťte následující příkaz:

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Použijte šablonu projektu **knihovny tříd Razor** .

---

Projekt vygenerovaný ze šablony provádí následující akce:

* Cíle .NET Standard 2,0.
* Nastaví vlastnost `RazorLangVersion` na hodnotu `3.0`. `3.0` je výchozí hodnota pro .NET Core 3. x.
* Přidá následující odkazy na balíčky:
  * [Microsoft. AspNetCore. Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)
  * [Microsoft. AspNetCore. Components. Web](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)

Příklad:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a>Podpora konkrétního modelu hostování

Je mnohem méně běžné podporovat model hostování jednoho Blazor. Například pro podporu spotřeby komponent Razor z [Blazorch projektů serveru](xref:blazor/hosting-models#blazor-server) :

* Cílová platforma .NET Core 3. x.
* Přidejte `<FrameworkReference>` element pro sdílené rozhraní.

Příklad:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

Další informace o knihovnách obsahujících komponenty Razor naleznete v tématu [ASP.NET Core knihovny tříd komponent Razor](xref:blazor/class-libraries).

## <a name="include-mvc-extensibility"></a>Zahrnutí rozšiřitelnosti MVC

V této části najdete popis doporučení pro knihovny, které zahrnují:

* [Zobrazení a Razor Pages Razor](#razor-views-or-razor-pages)
* [Pomocné rutiny značek](#tag-helpers)
* [Komponenty zobrazení](#view-components)

Tato část se nezabývá cílením na podporu více verzí MVC. Pokyny k podpoře více verzí ASP.NET Core najdete v tématu [Podpora více verzí ASP.NET Core](#support-multiple-aspnet-core-versions).

### <a name="razor-views-or-razor-pages"></a>Zobrazení a Razor Pages Razor

Projekt, který obsahuje [zobrazení](xref:mvc/views/overview) nebo [Razor Pages](xref:razor-pages/index) Razor, musí používat [sadu Microsoft. NET. SDK. Razor SDK](xref:razor-pages/sdk).

Pokud je projekt cílen na rozhraní .NET Core 3. x, vyžaduje:

* Vlastnost `AddRazorSupportForMvc` MSBuild nastavená na hodnotu `true`.
* Element `<FrameworkReference>` pro sdílené rozhraní.

Šablona projektu **knihovny tříd Razor** splňuje předchozí požadavky pro projekty cílené na .NET Core 3. x. Pro editor použijte následující pokyny.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Použijte šablonu projektu **knihovny tříd Razor** . Je třeba vybrat zaškrtávací políčko **stránky podpory a zobrazení** šablony.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

V integrovaném terminálu spusťte následující příkaz:

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

V tuto chvíli není podporována žádná podpora šablon projektů.

---

Příklad:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

Pokud se místo toho projekt cílí .NET Standard, je vyžadován odkaz na balíček [Microsoft. AspNetCore. Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) . Balíček `Microsoft.AspNetCore.Mvc` přesunut do sdíleného rozhraní v ASP.NET Core 3,0, a proto již není publikován. Příklad:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a>Pomocné rutiny značek

Projekt, který obsahuje [pomocníky značek](xref:mvc/views/tag-helpers/intro) , by měl používat sadu SDK `Microsoft.NET.Sdk`. Pokud cílíte na rozhraní .NET Core 3. x, přidejte `<FrameworkReference>` element pro sdílené rozhraní. Příklad:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

Pokud cílíte .NET Standard (pro podporu verzí starších než ASP.NET Core 3. x), přidejte odkaz na balíček do [Microsoft. AspNetCore. Mvc. Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor). Balíček `Microsoft.AspNetCore.Mvc.Razor` přesunut do sdíleného rozhraní a proto již není publikován. Příklad:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a>Komponenty zobrazení

Projekt, který obsahuje [komponenty zobrazení](xref:mvc/views/view-components) , by měl používat sadu SDK `Microsoft.NET.Sdk`. Pokud cílíte na rozhraní .NET Core 3. x, přidejte `<FrameworkReference>` element pro sdílené rozhraní. Příklad:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

Pokud cílíte .NET Standard (pro podporu verzí starších než ASP.NET Core 3. x), přidejte odkaz na balíček do [Microsoft. AspNetCore. Mvc. ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures). Balíček `Microsoft.AspNetCore.Mvc.ViewFeatures` přesunut do sdíleného rozhraní a proto již není publikován. Příklad:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a>Podpora více verzí ASP.NET Core

Aby bylo možné vytvořit knihovnu, která podporuje více variant ASP.NET Core, vyžaduje se cílení na více verzí. Vezměte v úvahu scénář, ve kterém musí knihovna pomocníků značek podporovat následující ASP.NET Core varianty:

* ASP.NET Core 2,1 cílení na .NET Framework 4.6.1
* ASP.NET Core 2. x cílící na .NET Core 2. x
* ASP.NET Core 3. x cílící na .NET Core 3. x

Následující soubor projektu podporuje tyto varianty prostřednictvím vlastnosti `TargetFrameworks`:

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

S předchozím souborem projektu:

* Balíček `Markdig` se přidá pro všechny uživatele.
* Odkaz na [Microsoft. AspNetCore. Mvc. Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) je přidán pro uživatele cílící na .NET Framework 4.6.1 nebo novější nebo .NET Core 2. x. 2\.1.0 verze balíčku spolupracuje s ASP.NET Core 2,2 z důvodu zpětné kompatibility.
* Na sdílené rozhraní se odkazuje pro uživatele, kteří cílí na .NET Core 3. x. Balíček `Microsoft.AspNetCore.Mvc.Razor` je součástí sdílené architektury.

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

Například synchronní čtení a zápisy v případě požadavků HTTP a datových proudů odpovědí jsou ve výchozím nastavení zakázány ASP.NET Core 3,0. ASP.NET Core 2,2 ve výchozím nastavení podporuje synchronní chování. Vezměte v úvahu knihovnu middlewaru, ve které by měla být povolena synchronní čtení a zápisy v případě výskytu vstupně-výstupních operací. Knihovna by měla uzavřít kód, aby povolovala synchronní funkce v příslušné direktivě preprocesoru. Příklad:

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

Například následující pomocná značka používá rozhraní <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> představené v ASP.NET Core 3,0. Spotřebitelé cílící na .NET Core 3,0 spustí cestu kódu definovanou symbolem `NETCOREAPP3_0` Target Framework. Typ parametru konstruktoru pomocné rutiny značky změny <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> pro .NET Core 2,1 a .NET Framework 4.6.1 spotřebitelé. Tato změna byla nezbytná, protože ASP.NET Core 3,0 označil `IHostingEnvironment` jako zastaralý a doporučuje se `IWebHostEnvironment` jako náhrada.

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

## <a name="additional-resources"></a>Další materiály a zdroje informací

* <xref:razor-pages/ui-class>
* <xref:blazor/class-libraries>
* [Podpora implementace rozhraní .NET](/dotnet/standard/net-standard#net-implementation-support)
* [Zásady podpory rozhraní .NET](https://dotnet.microsoft.com/platform/support/policy)

---
title: RazorKnihovny tříd ASP.NET Core komponenty
author: guardrex
description: Zjistěte, jak mohou být součásti součástí Blazor aplikací z externí knihovny součástí.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/class-libraries
ms.openlocfilehash: afd1bfffae11520a5d9abccc1d2ee4cf3a46a4bf
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722459"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a>RazorKnihovny tříd ASP.NET Core komponenty

Od [Simon Timms](https://github.com/stimms)

Komponenty lze sdílet v [ Razor knihovně tříd (RCL)](xref:razor-pages/ui-class) napříč projekty. * Razor Knihovna tříd komponent* může obsahovat:

* Jiný projekt v řešení.
* Balíček NuGet.
* Odkazovaná knihovna .NET.

Stejně jako komponenty jsou běžné typy .NET, komponenty poskytované RCL jsou normální sestavení .NET.

## <a name="create-an-rcl"></a>Vytvoření RCL

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Vytvoření nového projektu
1. Vyberte možnost ** Razor Knihovna tříd**. Vyberte **Další**.
1. V dialogovém okně **vytvořit novou Razor knihovnu tříd** vyberte **vytvořit**.
1. Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu. V příkladech v tomto tématu se používá název projektu `ComponentLibrary` . Vyberte **Vytvořit**.
1. Přidat RCL do řešení:
   1. Klikněte pravým tlačítkem na řešení. Vyberte **Přidat**  >  **existující projekt**.
   1. Přejděte do souboru projektu RCL.
   1. Vyberte soubor projektu RCL ( `.csproj` ).
1. Přidejte odkaz na RCL z aplikace:
   1. Klikněte pravým tlačítkem na projekt aplikace. Vyberte **Přidat**  >  **odkaz**.
   1. Vyberte projekt RCL. Vyberte **OK**.

> [!NOTE]
> Pokud je zaškrtnuto políčko **stránky podpory a zobrazení** při generování RCL ze šablony, přidejte také `_Imports.razor` soubor do kořenového adresáře vygenerovaného projektu s následujícím obsahem, který umožní Razor vytváření komponent:
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> Ručně přidejte soubor do kořenového adresáře vygenerovaného projektu.

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

1. Použijte šablonu ** Razor knihovny tříd** ( `razorclasslib` ) s [`dotnet new`](/dotnet/core/tools/dotnet-new) příkazem v příkazovém prostředí. V následujícím příkladu je vytvořen RCL s názvem `ComponentLibrary` . Složka, která je uložena, `ComponentLibrary` je vytvořena automaticky při spuštění příkazu:

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > Pokud `-s|--support-pages-and-views` je použit přepínač při generování RCL ze šablony, pak přidejte `_Imports.razor` soubor do kořenového adresáře vygenerovaného projektu s následujícím obsahem, který umožní Razor vytváření komponent:
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > Ručně přidejte soubor do kořenového adresáře vygenerovaného projektu.

1. Chcete-li přidat knihovnu do existujícího projektu, použijte [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) příkaz v příkazovém prostředí. V následujícím příkladu se RCL přidá do aplikace. Spusťte následující příkaz ze složky projektu aplikace s cestou ke knihovně:

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a>Využití komponenty knihovny

Aby bylo možné využívat komponenty definované v knihovně v jiném projektu, použijte některý z následujících přístupů:

* Použijte úplný název typu s oborem názvů.
* Razor [`@using`](xref:mvc/views/razor#using) Direktiva use. Jednotlivé komponenty lze přidat podle názvu.

V následujících příkladech `ComponentLibrary` je knihovna komponent obsahující `Component1` komponentu ( `Component1.razor` ). `Component1`Komponenta je ukázková komponenta, která je automaticky přidána šablonou projektu RCL při vytvoření knihovny.

Odkaz na `Component1` komponentu pomocí jejího oboru názvů:

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

Případně přeneste knihovnu do oboru pomocí [`@using`](xref:mvc/views/razor#using) direktivy a použijte komponentu bez jejího oboru názvů:

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

Volitelně můžete zahrnout `@using ComponentLibrary` direktivu do souboru nejvyšší úrovně `_Import.razor` , aby byly komponenty knihovny k dispozici pro celý projekt. Přidejte direktivu do `_Import.razor` souboru na libovolné úrovni pro použití oboru názvů pro jednu komponentu nebo sadu součástí v rámci složky.

::: moniker range=">= aspnetcore-5.0"

Chcete- `Component1` li `my-component` do komponenty poskytnout CSS třídu, odkazujte na šablonu stylů knihovny pomocí [ `Link` komponenty](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) rozhraní v nástroji `Component1.razor` :

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

Pokud chcete šablonu stylů zadat v rámci aplikace, můžete alternativně vytvořit odkaz na šablonu stylů knihovny v souboru aplikace `wwwroot/index.html` ( Blazor WebAssembly ) nebo `Pages/_Host.cshtml` souboru ( Blazor Server ):

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

Při `Link` použití komponenty v podřízené součásti je propojená Asset také k dispozici libovolné jiné podřízené součásti nadřazené komponenty, pokud `Link` je vykreslena podřízená komponenta. Rozdíl mezi použitím `Link` komponenty v podřízené komponentě a umístěním `<link>` značky HTML v `wwwroot/index.html` nebo `Pages/_Host.cshtml` je, že vykreslená značka HTML komponenty rozhraní:

* Může být upraveno stavem aplikace. Pevně kódovaná `<link>` značka HTML nemůže být upravena stavem aplikace.
* Je odebrán z kódu HTML, `<head>` Pokud již není vykreslena nadřazená komponenta.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Chcete-li poskytnout `Component1` `my-component` CSS třídu, odkaz na šablonu stylů knihovny v `wwwroot/index.html` souboru aplikace ( Blazor WebAssembly ) nebo `Pages/_Host.cshtml` souboru ( Blazor Server ):

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a>Vytvoření Razor knihovny tříd komponent se statickými prostředky

RCL může zahrnovat statické prostředky. Statické prostředky jsou k dispozici pro všechny aplikace, které knihovnu využívají. Další informace naleznete v tématu <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a>Dodávání komponent a statických prostředků do více hostovaných Blazor aplikací

Další informace naleznete v tématu <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.

::: moniker range=">= aspnetcore-5.0"

## <a name="browser-compatibility-analyzer-for-no-locblazor-webassembly"></a>Analyzátor kompatibility prohlížeče pro Blazor WebAssembly

Blazor WebAssembly aplikace cílí na úplnou oblast rozhraní .NET API, ale ne všechna rozhraní API .NET jsou podporovaná ve webovém sestavení vzhledem k omezením izolovaného prostoru (sandbox) prohlížeče. Nepodporovaná rozhraní API se vyvolávají <xref:System.PlatformNotSupportedException> při spuštění na WebAssembly. Analyzátor kompatibility platforem upozorní vývojáře, když aplikace používá rozhraní API, která nejsou podporovaná cílovými platformami aplikace. U Blazor WebAssembly aplikací to znamená, že se v prohlížečích podporují rozhraní API. Přidání poznámek k rozhraním API .NET Framework pro analyzátor kompatibility je probíhající proces, takže ne všechna rozhraní .NET Framework API jsou momentálně Poznáma.

Blazor WebAssemblyRazorprojekty knihovny tříd a *automaticky* umožňují compatibilty kontroly prohlížeče přidáním `browser` jako podporované platformy s `SupportedPlatform` položkou MSBuild. Vývojáři knihovny mohou ručně přidat `SupportedPlatform` položku do souboru projektu knihovny a povolit tak funkci:

```xml
<ItemGroup>
  <SupportedPlatform Include="browser" />
</ItemGroup>
```

Při vytváření knihovny je nutné určit, že konkrétní rozhraní API není v prohlížečích podporováno zadáním `browser` <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute> :

```csharp
[UnsupportedOSPlatform("browser")]
private static string GetLoggingDirectory()
{
    ...
}
```

Další informace najdete v tématu věnovaném vytváření [poznámek na rozhraních API na konkrétních platformách (dotnet/Designs v úložišti GitHubu)](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms).

## <a name="no-locblazor-javascript-isolation-and-object-references"></a>Blazor JavaScript – izolace a odkazy na objekty

Blazor povoluje izolaci JavaScriptu ve standardních [modulech JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules). Izolace JavaScriptu nabízí následující výhody:

* Importovaný JavaScript již neznečišťující globální obor názvů.
* Příjemci knihovny a součásti nejsou vyžadováni k ručnímu importování souvisejícího JavaScriptu.

Další informace naleznete v tématu <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.

::: moniker-end

## <a name="build-pack-and-ship-to-nuget"></a>Sestavení, balení a odeslání do NuGet

Knihovny součástí jsou standardní knihovny .NET, balení a jejich odeslání do NuGet se neliší od balení a předává jakékoli knihovny do NuGet. Balení se provádí pomocí [`dotnet pack`](/dotnet/core/tools/dotnet-pack) příkazu v příkazovém prostředí:

```dotnetcli
dotnet pack
```

Nahrajte balíček do NuGet pomocí [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) příkazu v příkazovém prostředí.

## <a name="additional-resources"></a>Další zdroje

::: moniker range=">= aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [Přidat konfigurační soubor trávníku pro převodní jazyk (IL) XML do knihovny](xref:blazor/host-and-deploy/configure-trimmer)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [Přidat konfigurační soubor linkeru jazyka XML Intermediate Language (IL) do knihovny](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)

::: moniker-end

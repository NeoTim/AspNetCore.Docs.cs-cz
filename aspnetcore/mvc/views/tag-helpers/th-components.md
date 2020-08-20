---
title: Pomocné komponenty značek v ASP.NET Core
author: scottaddie
description: Přečtěte si, co jsou pomocné komponenty značek a jak je používat v ASP.NET Core.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.date: 06/12/2019
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
uid: mvc/views/tag-helpers/th-components
ms.openlocfilehash: d3bae9cd5b4a5c7315c795229c7eafbf1e44e22d
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627582"
---
# <a name="tag-helper-components-in-aspnet-core"></a>Pomocné komponenty značek v ASP.NET Core

Autor – [Scott Addie](https://twitter.com/Scott_Addie) a [Fiyaz bin Hasan](https://github.com/fiyazbinhasan)

Pomocná komponenta značek je pomocný pomocník značek, který umožňuje podmíněně upravovat nebo přidávat prvky HTML z kódu na straně serveru. Tato funkce je k dispozici v ASP.NET Core 2,0 nebo novějším.

ASP.NET Core obsahuje dvě vestavěné pomocné komponenty značek: `head` a `body` . Jsou umístěny v <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> oboru názvů a lze je použít v MVC a Razor stránkách. Pomocné komponenty značek nevyžadují registraci u aplikace v *_ViewImports. cshtml*.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="use-cases"></a>Případy použití

Mezi dva běžné případy použití pomocných komponent značek patří:

1. [Vložení do `<link>` `<head>` .](#inject-into-html-head-element)
1. [Vložení do `<script>` `<body>` .](#inject-into-html-body-element)

Následující části popisují tyto případy použití.

### <a name="inject-into-html-head-element"></a>Vložit do elementu Head HTML

V rámci `<head>` elementu HTML se soubory CSS běžně importují pomocí `<link>` elementu HTML. Následující kód vloží `<link>` element do `<head>` prvku pomocí `head` pomocné komponenty Tag:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressStyleTagHelperComponent.cs)]

V předchozím kódu:

* `AddressStyleTagHelperComponent` implementuje <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent> . Abstrakce:
  * Umožňuje inicializaci třídy s <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext> .
  * Povoluje použití pomocných komponent značek k přidání nebo úpravě prvků HTML.
* <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*>Vlastnost definuje pořadí, ve kterém se komponenty vykreslují. `Order` je nutné v případě, že je v aplikaci více použití komponent pomocníka značek.
* <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> Porovná <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> hodnotu vlastnosti kontextu spuštění s hodnotou `head` . Pokud se porovnání vyhodnotí jako true, obsah `_style` pole je vložen do `<head>` elementu HTML.

### <a name="inject-into-html-body-element"></a>Vložit do prvku textu HTML

`body`Komponenta pomocník značek může vložit `<script>` prvek do `<body>` prvku. Následující kód demonstruje tuto techniku:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressScriptTagHelperComponent.cs)]

K uložení prvku se používá samostatný soubor HTML `<script>` . Soubor HTML vytváří čisticí kód a je udržovatelnější. Předchozí kód přečte obsah *TagHelpers/Templates/AddressToolTipScript.html* a připojí ho k výstupu pomocníka značek. Soubor *AddressToolTipScript.html* obsahuje následující kód:

[!code-html[](th-components/samples/RazorPagesSample/TagHelpers/Templates/AddressToolTipScript.html)]

Předchozí kód váže [widget s popisem spouštěcího](https://getbootstrap.com/docs/3.3/javascript/#tooltips) prvku na libovolný `<address>` prvek, který obsahuje `printable` atribut. Je-li ukazatel myši umístěn nad prvkem, je efekt zobrazen.

## <a name="register-a-component"></a>Registrace součásti

Komponenta pomocníka značek musí být přidána do kolekce komponent pomocníka značek aplikace. Existují tři způsoby, jak přidat do kolekce:

* [Registrace prostřednictvím kontejneru služby](#registration-via-services-container)
* [Registrace prostřednictvím Razor souboru](#registration-via-razor-file)
* [Registrace přes model stránky nebo kontroler](#registration-via-page-model-or-controller)

### <a name="registration-via-services-container"></a>Registrace prostřednictvím kontejneru služby

Pokud není třída pomocné komponenty značky spravovaná pomocí <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager> , musí být zaregistrovaná v systému [vkládání závislostí (di)](xref:fundamentals/dependency-injection) . Následující `Startup.ConfigureServices` kód registruje `AddressStyleTagHelperComponent` třídy a `AddressScriptTagHelperComponent` s [přechodnou životností](xref:fundamentals/dependency-injection#lifetime-and-registration-options):

[!code-csharp[](th-components/samples/RazorPagesSample/Startup.cs?name=snippet_ConfigureServices&highlight=12-15)]

### <a name="registration-via-no-locrazor-file"></a>Registrace prostřednictvím Razor souboru

Pokud komponenta pomocníka značek není zaregistrovaná v DI, může být zaregistrovaná ze Razor stránky stránky nebo zobrazení MVC. Tato technika se používá pro řízení vloženého kódu a pořadí provádění komponent ze Razor souboru.

`ITagHelperComponentManager` slouží k přidání pomocných komponent značek nebo jejich odebrání z aplikace. Následující kód demonstruje tuto techniku s `AddressTagHelperComponent` :

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_ITagHelperComponentManager)]

V předchozím kódu:

* `@inject`Direktiva poskytuje instanci `ITagHelperComponentManager` . Instance je přiřazena k proměnné s názvem `manager` pro přístup k podřízené položce v Razor souboru.
* Instance `AddressTagHelperComponent` je přidána do kolekce komponent pomocníka značek aplikace.

`AddressTagHelperComponent` je upraveno tak, aby vyhovovalo konstruktoru, který přijímá `markup` `order` parametry a:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_Constructor)]

Zadaný `markup` parametr se používá v `ProcessAsync` následujícím příkladu:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_ProcessAsync&highlight=10-11)]

### <a name="registration-via-page-model-or-controller"></a>Registrace přes model stránky nebo kontroler

Pokud komponenta pomocníka značek není zaregistrovaná v DI, může být zaregistrovaná z Razor modelu stránky stránek nebo řadiče MVC. Tato technika je užitečná pro oddělení logiky jazyka C# ze Razor souborů.

Injektáže konstruktoru se používá pro přístup k instanci `ITagHelperComponentManager` . Komponenta pomocník značek je přidána do kolekce komponent pomocníka značek instance. Následující stránky Razor ukazují tento postup `AddressTagHelperComponent` :

[!code-csharp[](th-components/samples/RazorPagesSample/Pages/Index.cshtml.cs?name=snippet_IndexModelClass)]

V předchozím kódu:

* Injektáže konstruktoru se používá pro přístup k instanci `ITagHelperComponentManager` .
* Instance `AddressTagHelperComponent` je přidána do kolekce komponent pomocníka značek aplikace.

## <a name="create-a-component"></a>Vytvoření komponenty

Vytvoření vlastní pomocné komponenty značky:

* Vytvoří veřejnou třídu odvozenou z <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper> .
* Použijte [`[HtmlTargetElement]`](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) atribut pro třídu. Zadejte název cílového elementu HTML.
* *Volitelné*: použijte [`[EditorBrowsable(EditorBrowsableState.Never)]`](xref:System.ComponentModel.EditorBrowsableAttribute) atribut pro třídu pro potlačení zobrazení typu v IntelliSense.

Následující kód vytvoří vlastní pomocnou komponentu značky, která se zaměřuje na `<address>` element jazyka HTML:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponentTagHelper.cs)]

Použijte vlastní `address` komponentu pomocníka značek pro vložení značek HTML následujícím způsobem:

```csharp
public class AddressTagHelperComponent : TagHelperComponent
{
    private readonly string _printableButton =
        "<button type='button' class='btn btn-info' onclick=\"window.open(" +
        "'https://binged.it/2AXRRYw')\">" +
        "<span class='glyphicon glyphicon-road' aria-hidden='true'></span>" +
        "</button>";

    public override int Order => 3;

    public override async Task ProcessAsync(TagHelperContext context,
                                            TagHelperOutput output)
    {
        if (string.Equals(context.TagName, "address",
                StringComparison.OrdinalIgnoreCase) &&
            output.Attributes.ContainsName("printable"))
        {
            var content = await output.GetChildContentAsync();
            output.Content.SetHtmlContent(
                $"<div>{content.GetContent()}</div>{_printableButton}");
        }
    }
}
```

Předchozí `ProcessAsync` Metoda vloží do odpovídajícího prvku kód HTML, který je k dispozici <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> `<address>` . K injektáže dojde v těchto případech:

* Hodnota vlastnosti kontextu spuštění `TagName` se rovná `address` .
* Odpovídající `<address>` element má `printable` atribut.

Například `if` příkaz je vyhodnocen jako true při zpracování následujícího `<address>` elementu:

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_AddressPrintable)]

## <a name="additional-resources"></a>Další materiály

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
* <xref:mvc/views/tag-helpers/builtin-th/Index>

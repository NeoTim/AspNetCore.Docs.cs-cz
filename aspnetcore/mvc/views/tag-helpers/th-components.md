---
title: Pomocné komponenty značek v ASP.NET Core
author: scottaddie
description: Přečtěte si, co jsou pomocné komponenty značek a jak je používat v ASP.NET Core.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.date: 06/12/2019
uid: mvc/views/tag-helpers/th-components
ms.openlocfilehash: de053dd0138224a9bece218010eaaedcf7e084cb
ms.sourcegitcommit: 257cc3fe8c1d61341aa3b07e5bc0fa3d1c1c1d1c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69583534"
---
# <a name="tag-helper-components-in-aspnet-core"></a>Pomocné komponenty značek v ASP.NET Core

Autor – [Scott Addie](https://twitter.com/Scott_Addie) a [Fiyaz bin Hasan](https://github.com/fiyazbinhasan)

Pomocná komponenta značek je pomocný pomocník značek, který umožňuje podmíněně upravovat nebo přidávat prvky HTML z kódu na straně serveru. Tato funkce je k dispozici v ASP.NET Core 2,0 nebo novějším.

ASP.NET Core obsahuje dvě vestavěné pomocné komponenty značek: `head` a. `body` Jsou umístěny v <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> oboru názvů a lze je použít v MVC i Razor Pages. Pomocné komponenty značek nevyžadují registraci u aplikace v *_ViewImports. cshtml*.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="use-cases"></a>Případy použití

Mezi dva běžné případy použití pomocných komponent značek patří:

1. [`<link>` Vložení`<head>`do.](#inject-into-html-head-element)
1. [`<script>` Vložení`<body>`do.](#inject-into-html-body-element)

Následující části popisují tyto případy použití.

### <a name="inject-into-html-head-element"></a>Vložit do elementu Head HTML

V rámci elementu `<head>` HTML se soubory CSS běžně importují pomocí elementu HTML. `<link>` Následující kód vloží `<link>` element `<head>` do prvku pomocí `head` pomocné komponenty Tag:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressStyleTagHelperComponent.cs)]

V předchozím kódu:

* `AddressStyleTagHelperComponent`implementuje <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>. Abstrakce:
  * Umožňuje inicializaci třídy s <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>.
  * Povoluje použití pomocných komponent značek k přidání nebo úpravě prvků HTML.
* <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> Vlastnost definuje pořadí, ve kterém se komponenty vykreslují. `Order`je nutné v případě, že je v aplikaci více použití komponent pomocníka značek.
* <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*>Porovná hodnotu <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> vlastnosti kontextu spuštění s `head`hodnotou. Pokud se porovnání vyhodnotí jako true, obsah `_style` pole je vložen do elementu HTML. `<head>`

### <a name="inject-into-html-body-element"></a>Vložit do prvku textu HTML

Komponenta pomocník `<script>`značekmůže vložit prvek do `<body>` prvku. `body` Následující kód demonstruje tuto techniku:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressScriptTagHelperComponent.cs)]

K uložení `<script>` prvku se používá samostatný soubor HTML. Soubor HTML vytváří čisticí kód a je udržovatelnější. Předchozí kód přečte obsah *TagHelpers/Templates/AddressToolTipScript.html* a připojí ho k výstupu pomocníka značek. Soubor *AddressToolTipScript. html* obsahuje následující kód:

[!code-html[](th-components/samples/RazorPagesSample/TagHelpers/Templates/AddressToolTipScript.html)]

Předchozí kód váže [widget](https://getbootstrap.com/docs/3.3/javascript/#tooltips) s popisem spouštěcího `printable` prvku na `<address>` libovolný prvek, který obsahuje atribut. Je-li ukazatel myši umístěn nad prvkem, je efekt zobrazen.

## <a name="register-a-component"></a>Registrace součásti

Komponenta pomocníka značek musí být přidána do kolekce komponent pomocníka značek aplikace. Existují tři způsoby, jak přidat do kolekce:

* [Registrace prostřednictvím kontejneru služby](#registration-via-services-container)
* [Registrace prostřednictvím souboru Razor](#registration-via-razor-file)
* [Registrace přes model stránky nebo kontroler](#registration-via-page-model-or-controller)

### <a name="registration-via-services-container"></a>Registrace prostřednictvím kontejneru služby

Pokud není třída pomocné komponenty značky spravovaná pomocí <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager>, musí být zaregistrovaná v systému [vkládání závislostí (di)](xref:fundamentals/dependency-injection) . Následující `Startup.ConfigureServices` kód [](xref:fundamentals/dependency-injection#lifetime-and-registration-options)registruje třídy a `AddressScriptTagHelperComponent`s přechodnou životností: `AddressStyleTagHelperComponent`

[!code-csharp[](th-components/samples/RazorPagesSample/Startup.cs?name=snippet_ConfigureServices&highlight=12-15)]

### <a name="registration-via-razor-file"></a>Registrace prostřednictvím souboru Razor

Pokud komponenta pomocníka značek není zaregistrovaná v DI, dá se zaregistrovat ze stránky Razor Pages nebo zobrazení MVC. Tato technika se používá pro řízení vloženého kódu a pořadí provádění součástí ze souboru Razor.

`ITagHelperComponentManager`slouží k přidání pomocných komponent značek nebo jejich odebrání z aplikace. Následující kód demonstruje tuto techniku `AddressTagHelperComponent`s:

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_ITagHelperComponentManager)]

V předchozím kódu:

* Direktiva poskytuje `ITagHelperComponentManager`instanci. `@inject` Instance je přiřazena k proměnné s názvem `manager` pro přístup pro příjem dat v souboru Razor.
* Instance `AddressTagHelperComponent` je přidána do kolekce komponent pomocníka značek aplikace.

`AddressTagHelperComponent`je upraveno tak, aby vyhovovalo konstruktoru `markup` , `order` který přijímá parametry a:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_Constructor)]

Zadaný `markup` parametr se používá v `ProcessAsync` následujícím příkladu:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_ProcessAsync&highlight=10-11)]

### <a name="registration-via-page-model-or-controller"></a>Registrace přes model stránky nebo kontroler

Pokud komponenta pomocníka značek není zaregistrovaná v DI, může být zaregistrovaná z Razor Pagesho modelu nebo kontroleru MVC. Tato technika je užitečná pro oddělení C# logiky ze souborů Razor.

Injektáže konstruktoru se používá pro přístup k instanci `ITagHelperComponentManager`. Komponenta pomocník značek je přidána do kolekce komponent pomocníka značek instance. Následující Razor Pages model ukazuje tuto techniku s `AddressTagHelperComponent`:

[!code-csharp[](th-components/samples/RazorPagesSample/Pages/Index.cshtml.cs?name=snippet_IndexModelClass)]

V předchozím kódu:

* Injektáže konstruktoru se používá pro přístup k instanci `ITagHelperComponentManager`.
* Instance `AddressTagHelperComponent` je přidána do kolekce komponent pomocníka značek aplikace.

## <a name="create-a-component"></a>Vytvoření komponenty

Vytvoření vlastní pomocné komponenty značky:

* Vytvoří veřejnou třídu odvozenou z <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper>.
* Použijte pro třídu atribut [[HtmlTargetElement]](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) . Zadejte název cílového elementu HTML.
* *Volitelné*: Použijte atribut [[členy editorbrowsable (EditorBrowsableState. Never)]](xref:System.ComponentModel.EditorBrowsableAttribute) pro třídu pro potlačení zobrazení typu v technologii IntelliSense.

Následující kód vytvoří vlastní pomocnou komponentu značky, která se zaměřuje `<address>` na element jazyka HTML:

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

Předchozí `ProcessAsync` metoda vloží do odpovídajícího `<address>` prvku kód HTML, <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> který je k dispozici. K injektáže dojde v těchto případech:

* Hodnota `TagName` vlastnosti kontextu spuštění `address`se rovná.
* Odpovídající `<address>` element`printable` má atribut.

Například příkaz je `if` vyhodnocen jako true při zpracování následujícího `<address>` elementu:

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_AddressPrintable)]

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
* <xref:mvc/views/tag-helpers/builtin-th/Index>

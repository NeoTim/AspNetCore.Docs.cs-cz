---
title: Pomocné komponenty značek v ASP.NET Core
author: scottaddie
description: Přečtěte si, co jsou pomocné komponenty značek a jak je používat v ASP.NET Core.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.date: 06/12/2019
uid: mvc/views/tag-helpers/th-components
ms.openlocfilehash: 070cc3aae08664c13d8eb793a066766d0a5569ee
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880977"
---
# <a name="tag-helper-components-in-aspnet-core"></a>Pomocné komponenty značek v ASP.NET Core

Autor – [Scott Addie](https://twitter.com/Scott_Addie) a [Fiyaz bin Hasan](https://github.com/fiyazbinhasan)

Pomocná komponenta značek je pomocný pomocník značek, který umožňuje podmíněně upravovat nebo přidávat prvky HTML z kódu na straně serveru. Tato funkce je k dispozici v ASP.NET Core 2,0 nebo novějším.

ASP.NET Core obsahuje dvě vestavěné pomocné komponenty značek: `head` a `body`. Jsou umístěny v oboru názvů <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> a lze je použít v MVC i Razor Pages. Pomocné komponenty značek nevyžadují registraci u aplikace v *_ViewImports. cshtml*.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="use-cases"></a>Případy použití

Mezi dva běžné případy použití pomocných komponent značek patří:

1. [Vložení `<link>` do `<head>`.](#inject-into-html-head-element)
1. [Vložení `<script>` do `<body>`.](#inject-into-html-body-element)

Následující části popisují tyto případy použití.

### <a name="inject-into-html-head-element"></a>Vložit do elementu Head HTML

Uvnitř elementu `<head>` HTML jsou soubory CSS obvykle importovány pomocí elementu `<link>` HTML. Následující kód vloží `<link>` element do prvku `<head>` pomocí pomocné komponenty značky `head`:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressStyleTagHelperComponent.cs)]

V předchozím kódu:

* `AddressStyleTagHelperComponent` implementuje <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>. Abstrakce:
  * Umožňuje inicializaci třídy s <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>.
  * Povoluje použití pomocných komponent značek k přidání nebo úpravě prvků HTML.
* Vlastnost <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> definuje pořadí, ve kterém se komponenty vykreslují. `Order` je nutné v případě, že v aplikaci existuje více použití pomocných komponent značek.
* <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> porovná hodnotu <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> vlastnosti kontextu spuštění s `head`. Pokud se porovnání vyhodnotí jako true, obsah pole `_style` je vložen do prvku `<head>` HTML.

### <a name="inject-into-html-body-element"></a>Vložit do prvku textu HTML

Pomocná komponenta značky `body` může vložit `<script>` prvek do prvku `<body>`. Následující kód demonstruje tuto techniku:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressScriptTagHelperComponent.cs)]

K uložení prvku `<script>` slouží samostatný soubor HTML. Soubor HTML vytváří čisticí kód a je udržovatelnější. Předchozí kód přečte obsah *TagHelpers/Templates/AddressToolTipScript.html* a připojí ho k výstupu pomocníka značek. Soubor *AddressToolTipScript. html* obsahuje následující kód:

[!code-html[](th-components/samples/RazorPagesSample/TagHelpers/Templates/AddressToolTipScript.html)]

Předchozí kód váže [widget s popisem spouštěcího](https://getbootstrap.com/docs/3.3/javascript/#tooltips) prvku na jakýkoli `<address>` element, který obsahuje atribut `printable`. Je-li ukazatel myši umístěn nad prvkem, je efekt zobrazen.

## <a name="register-a-component"></a>Registrace součásti

Komponenta pomocníka značek musí být přidána do kolekce komponent pomocníka značek aplikace. Existují tři způsoby, jak přidat do kolekce:

* [Registrace prostřednictvím kontejneru služby](#registration-via-services-container)
* [Registrace prostřednictvím souboru Razor](#registration-via-razor-file)
* [Registrace přes model stránky nebo kontroler](#registration-via-page-model-or-controller)

### <a name="registration-via-services-container"></a>Registrace prostřednictvím kontejneru služby

Pokud není třída pomocné komponenty značky spravovaná pomocí <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager>, musí být zaregistrovaná v systému [vkládání závislostí (di)](xref:fundamentals/dependency-injection) . Následující kód `Startup.ConfigureServices` registruje `AddressStyleTagHelperComponent` a třídy `AddressScriptTagHelperComponent` s [přechodným trváním](xref:fundamentals/dependency-injection#lifetime-and-registration-options):

[!code-csharp[](th-components/samples/RazorPagesSample/Startup.cs?name=snippet_ConfigureServices&highlight=12-15)]

### <a name="registration-via-razor-file"></a>Registrace prostřednictvím souboru Razor

Pokud komponenta pomocníka značek není zaregistrovaná v DI, dá se zaregistrovat ze stránky Razor Pages nebo zobrazení MVC. Tato technika se používá pro řízení vloženého kódu a pořadí provádění součástí ze souboru Razor.

`ITagHelperComponentManager` slouží k přidání pomocných komponent značek nebo jejich odebrání z aplikace. Následující kód demonstruje tuto techniku s `AddressTagHelperComponent`:

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_ITagHelperComponentManager)]

V předchozím kódu:

* Direktiva `@inject` poskytuje instanci `ITagHelperComponentManager`. Instance je přiřazena k proměnné s názvem `manager` pro přístup pro příjem dat v souboru Razor.
* Do kolekce komponent pomocníka značek aplikace se přidá instance `AddressTagHelperComponent`.

`AddressTagHelperComponent` se upraví tak, aby vyhovoval konstruktoru, který přijímá parametry `markup` a `order`:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_Constructor)]

Zadaný `markup` parametr se používá v `ProcessAsync` následujícím způsobem:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_ProcessAsync&highlight=10-11)]

### <a name="registration-via-page-model-or-controller"></a>Registrace přes model stránky nebo kontroler

Pokud komponenta pomocníka značek není zaregistrovaná v DI, může být zaregistrovaná z Razor Pagesho modelu nebo kontroleru MVC. Tato technika je užitečná pro oddělení C# logiky ze souborů Razor.

Injektáže konstruktoru se používá pro přístup k instanci `ITagHelperComponentManager`. Komponenta pomocník značek je přidána do kolekce komponent pomocníka značek instance. Následující Razor Pages model ukazuje tuto techniku s `AddressTagHelperComponent`:

[!code-csharp[](th-components/samples/RazorPagesSample/Pages/Index.cshtml.cs?name=snippet_IndexModelClass)]

V předchozím kódu:

* Injektáže konstruktoru se používá pro přístup k instanci `ITagHelperComponentManager`.
* Do kolekce komponent pomocníka značek aplikace se přidá instance `AddressTagHelperComponent`.

## <a name="create-a-component"></a>Vytvoření komponenty

Vytvoření vlastní pomocné komponenty značky:

* Vytvoří veřejnou třídu odvozenou z <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper>.
* Použijte atribut [`[HtmlTargetElement]`](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) pro třídu. Zadejte název cílového elementu HTML.
* *Volitelné*: použijte atribut [`[EditorBrowsable(EditorBrowsableState.Never)]`](xref:System.ComponentModel.EditorBrowsableAttribute) pro třídu pro potlačení zobrazení typu v IntelliSense.

Následující kód vytvoří vlastní pomocnou komponentu značky, která cílí na `<address>` HTML element:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponentTagHelper.cs)]

Použijte vlastní komponentu pomocníka značek `address` pro vložení značek HTML následujícím způsobem:

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

Předchozí metoda `ProcessAsync` vloží poskytnutý kód HTML pro <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> do odpovídajícího elementu `<address>`. K injektáže dojde v těchto případech:

* Hodnota vlastnosti `TagName` v kontextu spuštění se rovná `address`.
* Odpovídající prvek `<address>` má atribut `printable`.

Například příkaz `if` se při zpracování následujícího elementu `<address>` vyhodnocuje jako true:

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_AddressPrintable)]

## <a name="additional-resources"></a>Další materiály a zdroje informací

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
* <xref:mvc/views/tag-helpers/builtin-th/Index>

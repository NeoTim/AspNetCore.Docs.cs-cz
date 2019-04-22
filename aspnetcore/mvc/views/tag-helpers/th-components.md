---
title: Součásti pomocné rutiny značky v ASP.NET Core
author: scottaddie
description: Zjistěte, jaké jsou součásti pomocné rutiny značek a jejich použití v ASP.NET Core.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.date: 04/06/2019
uid: mvc/views/tag-helpers/th-components
ms.openlocfilehash: fdad4ae367245cd3beabaf90587c1fe5e9162afe
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59468592"
---
# <a name="tag-helper-components-in-aspnet-core"></a>Součásti pomocné rutiny značky v ASP.NET Core

Podle [Scott Addie](https://twitter.com/Scott_Addie) a [Hasan Fiyaz Bin](https://github.com/fiyazbinhasan)

Komponenta pomocné rutiny značky je pomocná rutina značky, který umožňuje podmíněně úprava nebo přidání elementů HTML kódu na straně serveru. Tato funkce je dostupná v ASP.NET Core 2.0 nebo novější.

ASP.NET Core zahrnuje dvě komponenty integrované pomocné rutiny značky: `head` a `body`. Nacházejí se v <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> obor názvů a je možné v MVC a stránky Razor. Součásti pomocné rutiny značky nevyžadují registraci s touto aplikací v *_ViewImports.cshtml*.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="use-cases"></a>Případy použití

Dvě běžné případy použití komponent pomocné rutiny značky patří:

1. [Vkládání `<link>` do `<head>`.](#inject-into-html-head-element)
1. [Vkládání `<script>` do `<body>`.](#inject-into-html-body-element)

Následující části popisují tyto případy použití.

### <a name="inject-into-html-head-element"></a>Vložit do hlavního elementu HTML

V kódu HTML `<head>` elementu, soubory šablon stylů CSS se obvykle importují s HTML `<link>` elementu. Následující kód vkládá `<link>` elementu do `<head>` pomocí elementu `head` komponenty pomocné rutiny značky:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressStyleTagHelperComponent.cs)]

V předchozím kódu:

* `AddressStyleTagHelperComponent` implementuje <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>. Abstrakce:
  * Umožňuje inicializaci třídy s vlastností <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>.
  * Umožňuje použití komponent pomocné rutiny značky přidat nebo upravit prvky jazyka HTML.
* <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> Vlastnost definuje pořadí, ve kterém jsou generovány komponenty. `Order` je potřeba, pokud existuje více použití komponent pomocné rutiny značky v aplikaci.
* <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> Porovná kontextu spuštění <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> hodnoty vlastnosti `head`. Pokud porovnání vyhodnocen jako true, obsah `_style` pole se vloží do kódu HTML `<head>` elementu.

### <a name="inject-into-html-body-element"></a>Vložení do prvku text HTML

`body` Komponenty pomocné rutiny značky lze vložit `<script>` elementu do `<body>` elementu. Následující kód ukazuje tento postup:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressScriptTagHelperComponent.cs)]

Samostatný soubor HTML se používá k ukládání `<script>` elementu. Soubor HTML díky kód přehlednější a jednodušší údržbu. Předchozí kód načte obsah *TagHelpers/Templates/AddressToolTipScript.html* a připojí jej s výstupem pomocné rutiny značky. *AddressToolTipScript.html* soubor obsahuje následující kód:

[!code-html[](th-components/samples/RazorPagesSample/TagHelpers/Templates/AddressToolTipScript.html)]

Předchozí kód vytvoří vazbu [Bootstrap popisek widgetu](https://getbootstrap.com/docs/3.3/javascript/#tooltips) k libovolnému `<address>` element, který obsahuje `printable` atribut. Efekt je viditelná, když ukazatel myši pohybuje nad prvkem.

## <a name="register-a-component"></a>Registrují komponentu

Komponentu pomocné rutiny značky musí být přidán do kolekce součásti pomocné rutiny značky aplikace. Existují tři způsoby, jak přidat do kolekce:

1. [Registrace prostřednictvím služby kontejneru](#registration-via-services-container)
1. [Registrace prostřednictvím souboru Razor](#registration-via-razor-file)
1. [Registrace prostřednictvím stránky nebo Model kontroleru](#registration-via-page-model-or-controller)

### <a name="registration-via-services-container"></a>Registrace prostřednictvím služby kontejneru

Pokud třída součástí pomocné rutiny značky není spravován pomocí <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager>, musí zaregistrovat [injektáž závislostí (DI)](xref:fundamentals/dependency-injection) systému. Následující `Startup.ConfigureServices` kódu registrů `AddressStyleTagHelperComponent` a `AddressScriptTagHelperComponent` třídy s [přechodná doba života](xref:fundamentals/dependency-injection#lifetime-and-registration-options):

[!code-csharp[](th-components/samples/RazorPagesSample/Startup.cs?name=snippet_ConfigureServices&highlight=12-15)]

### <a name="registration-via-razor-file"></a>Registrace prostřednictvím souboru Razor

Pokud součást pomocné rutiny značky není registrována s DI, lze jej zaregistrovat ze stránky Razor Pages nebo zobrazení MVC. Tento postup slouží k řízení vloženého kódu a pořadí spuštění součásti ze souboru Razor.

`ITagHelperComponentManager` slouží k přidání komponenty pomocné rutiny značky nebo odebrat z aplikace. Následující kód ukazuje tento postup s `AddressTagHelperComponent`:

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_ITagHelperComponentManager)]

V předchozím kódu:

* `@inject` Směrnice poskytuje instance `ITagHelperComponentManager`. Instance je přiřazen do proměnné s názvem `manager` pro přístup k směru server-klient v souboru Razor.
* Instance `AddressTagHelperComponent` se přidá do kolekce součásti pomocné rutiny značky aplikací.

`AddressTagHelperComponent` upravit tak, aby vyhovovaly konstruktor, který přijímá `markup` a `order` parametry:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_Constructor)]

Poskytnuté `markup` parametr se používá v `ProcessAsync` následujícím způsobem:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_ProcessAsync&highlight=10-11)]

### <a name="registration-via-page-model-or-controller"></a>Registrace prostřednictvím stránky nebo Model kontroleru

Pokud součást pomocné rutiny značky není registrována s DI, lze jej zaregistrovat z modelu stránky Razor Pages nebo kontroler MVC. Tato technika je užitečná pro oddělení logiky C# v souborech Razor.

Vkládání konstruktor se používá pro přístup k instanci `ITagHelperComponentManager`. Komponenta pomocné rutiny značky se přidá do kolekce na instanci komponenty pomocné rutiny značky. Tuto techniku s demonstruje následující model stránky Razor Pages `AddressTagHelperComponent`:

[!code-csharp[](th-components/samples/RazorPagesSample/Pages/Index.cshtml.cs?name=snippet_IndexModelClass)]

V předchozím kódu:

* Vkládání konstruktor se používá pro přístup k instanci `ITagHelperComponentManager`.
* Instance `AddressTagHelperComponent` se přidá do kolekce součásti pomocné rutiny značky aplikací.

## <a name="create-a-component"></a>Vytvoření komponenty

Chcete-li vytvořit komponentu vlastní pomocné rutiny značky:

* Vytvořit veřejnou třídu odvozenou z <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper>.
* Použití [[HtmlTargetElement]](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) atribut třídy. Zadejte název cílového elementu HTML.
* *Volitelné*: Použití [[EditorBrowsable(EditorBrowsableState.Never)]](xref:System.ComponentModel.EditorBrowsableAttribute) atribut třídy pro potlačení tohoto typu zobrazení v IntelliSense.

Následující kód vytvoří komponentu vlastní pomocné rutiny značky, který cílí `<address>` prvek HTML:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponentTagHelper.cs)]

Použít vlastní `address` komponenty pomocné rutiny značky se vložit kód HTML následujícím způsobem:

```csharp
public class AddressTagHelperComponent : TagHelperComponent
{
    private readonly string _printableButton =
        "<button type='button' class='btn btn-info' onclick=\"window.open("
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

Předchozí `ProcessAsync` metoda vkládá HTML poskytnuté <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> do odpovídající `<address>` elementu. Vyvolá se vkládání při:

* Kontext spuštění `TagName` vlastnost hodnota se rovná `address`.
* Odpovídající `<address>` obsahuje element `printable` atribut.

Například `if` vyhodnotí na hodnotu true při zpracovávání následujících `<address>` element:

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_AddressPrintable)]

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
* <xref:mvc/views/tag-helpers/builtin-th/Index>

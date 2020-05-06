---
title: Tagování pomocníků ve formulářích ve ASP.NET Core
author: rick-anderson
description: Popisuje předdefinované pomocníky značek používané s formuláři.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/working-with-forms
ms.openlocfilehash: ba523fba60153e2ae804f5a875cfaa1aa8fffedd
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769099"
---
# <a name="tag-helpers-in-forms-in-aspnet-core"></a>Tagování pomocníků ve formulářích ve ASP.NET Core

Od [Rick Anderson](https://twitter.com/RickAndMSFT), [N. Taylor Mullen](https://github.com/NTaylorMullen), [Dave Paquette](https://twitter.com/Dave_Paquette)a [Jerrie Pelser](https://github.com/jerriep)

Tento dokument ukazuje práci s formuláři a prvky HTML, které se obvykle používají ve formuláři. Element [formuláře](https://www.w3.org/TR/html401/interact/forms.html) HTML poskytuje primárnímu mechanismu webové aplikace použití k odesílání dat na server. Většina tohoto dokumentu popisuje [pomocníky značek](tag-helpers/intro.md) a způsob, jak vám může pomáhat s tvorbou robustních formulářů HTML. Před čtením tohoto dokumentu doporučujeme, abyste si přečetli [Úvod k označení pomocníků](tag-helpers/intro.md) .

V mnoha případech pomocné rutiny HTML poskytují alternativní přístup k konkrétní pomocné rutině značek, ale je důležité rozpoznat, že pomocné rutiny značky nenahrazují nápovědu HTML a že pro každou pomocnou nápovědu HTML není k dispozici pomocný pomocník značek. Pokud existuje alternativa pomocníka HTML, je uvedena.

<a name="my-asp-route-param-ref-label"></a>

## <a name="the-form-tag-helper"></a>Pomocník značek formuláře

Pomocný objekt značky [formuláře](https://www.w3.org/TR/html401/interact/forms.html) :

* Vygeneruje hodnotu atributu HTML [ \<Form>](https://www.w3.org/TR/html401/interact/forms.html) `action` pro akci kontroleru MVC nebo s názvem Route.

* Vygeneruje skrytý [token pro ověření žádosti](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) , který zabrání padělání požadavků mezi weby (při použití `[ValidateAntiForgeryToken]` s atributem v metodě akce HTTP POST).

* Poskytuje `asp-route-<Parameter Name>` atribut, který `<Parameter Name>` je přidán do hodnot tras. `routeValues` Parametry `Html.BeginForm` a `Html.BeginRouteForm` poskytují podobné funkce.

* Má alternativu `Html.BeginForm` k Pomocníkovi HTML a`Html.BeginRouteForm`

Ukázka:

[!code-HTML[](working-with-forms/sample/final/Views/Demo/RegisterFormOnly.cshtml)]

Pomocník značek Form výše vygeneruje následující kód HTML:

```html
<form method="post" action="/Demo/Register">
    <!-- Input and Submit elements -->
    <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

Modul runtime MVC generuje hodnotu `action` atributu z pomocníka značek formuláře `asp-controller` a. `asp-action` Pomocník značek formuláře také generuje skrytý token pro [ověření žádosti](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) , který zabrání padělání požadavků mezi weby (při použití s `[ValidateAntiForgeryToken]` atributem v metodě akce HTTP POST). Ochrana čistě formuláře HTML proti padělání žádostí mezi weby je obtížné, a proto pomocník značek formuláře tuto službu poskytuje za vás.

### <a name="using-a-named-route"></a>Použití pojmenované trasy

Atribut `asp-route` pomocník značek může také generovat označení pro atribut HTML `action` . Aplikace s názvem `register` v [cestě](../../fundamentals/routing.md) může pro registrační stránku použít následující značky:

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterRoute.cshtml)]

Mnohé z zobrazení ve složce *zobrazení nebo účtu* (vygenerované při vytváření nové webové aplikace s *jednotlivými uživatelskými účty*) obsahují atribut [ASP-Route-ReturnUrl](xref:mvc/views/working-with-forms) :

```cshtml
<form asp-controller="Account" asp-action="Login"
     asp-route-returnurl="@ViewData["ReturnUrl"]"
     method="post" class="form-horizontal" role="form">
```

>[!NOTE]
>Pomocí integrovaných šablon se automaticky `returnUrl` naplní pouze při pokusu o přístup k autorizovanému prostředku, ale nejsou ověřeny nebo autorizovány. Když se pokusíte o neautorizovaný přístup, middleware zabezpečení vás přesměruje na přihlašovací stránku se `returnUrl` sadou.

## <a name="the-form-action-tag-helper"></a>Pomocník značek akce formuláře

Pomocník značek akce formuláře vygeneruje `formaction` atribut na vygenerované `<button ...>` nebo `<input type="image" ...>` značky. `formaction` Atribut určuje, kam formulář odešle svá data. Váže se na [ \<vstupní>](https://www.w3.org/wiki/HTML/Elements/input) prvky typu a [ \<>](https://www.w3.org/wiki/HTML/Elements/button) prvků typu. `image` Pomocná značka akce formuláře umožňuje použití několika atributů [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-` k řízení toho, které `formaction` propojení je vygenerováno pro odpovídající prvek.

Podporované atributy [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) pro řízení hodnoty `formaction`:

|Atribut|Popis|
|---|---|
|[ASP – kontroler](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-controller)|Název kontroleru.|
|[ASP – akce](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-action)|Název metody akce|
|[ASP – oblast](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-area)|Název oblasti.|
|[ASP – stránka](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page)|Název stránky Razor|
|[ASP – obslužná rutina stránky](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page-handler)|Název obslužné rutiny stránky Razor|
|[ASP – trasa](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route)|Název trasy.|
|[ASP-Route-{Value}](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route-value)|Jedna hodnota směrování adresy URL. Například, `asp-route-id="1234"`.|
|[ASP-All-Route-data](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-all-route-data)|Všechny hodnoty tras.|
|[ASP – fragment](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-fragment)|Fragment adresy URL|

### <a name="submit-to-controller-example"></a>Příklad odeslání do kontroleru

Následující kód odešle formulář na `Index` akci, `HomeController` když je vybrán vstup nebo tlačítko:

```cshtml
<form method="post">
    <button asp-controller="Home" asp-action="Index">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-controller="Home" 
                                asp-action="Index">
</form>
```

Předchozí kód generuje následující kód HTML:

```html
<form method="post">
    <button formaction="/Home">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home">
</form>
```

### <a name="submit-to-page-example"></a>Příklad odeslání na stránku

Následující kód odešle formulář na stránku `About` Razor:

```cshtml
<form method="post">
    <button asp-page="About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-page="About">
</form>
```

Předchozí kód generuje následující kód HTML:

```html
<form method="post">
    <button formaction="/About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/About">
</form>
```

### <a name="submit-to-route-example"></a>Příklad odeslání do trasy

Vezměte v `/Home/Test` úvahu koncový bod:

```csharp
public class HomeController : Controller
{
    [Route("/Home/Test", Name = "Custom")]
    public string Test()
    {
        return "This is the test page";
    }
}
```

Následující kód odešle formulář do `/Home/Test` koncového bodu.

```cshtml
<form method="post">
    <button asp-route="Custom">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-route="Custom">
</form>
```

Předchozí kód generuje následující kód HTML:

```html
<form method="post">
    <button formaction="/Home/Test">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home/Test">
</form>
```

## <a name="the-input-tag-helper"></a>Pomocná rutina vstupní značky

Pomocná rutina vstupní značky váže vstupní prvek HTML [ \<>](https://www.w3.org/wiki/HTML/Elements/input) element na výraz modelu v zobrazení Razor.

Syntaxe:

```cshtml
<input asp-for="<Expression Name>">
```

Pomocná rutina vstupní značky:

* `id` Generuje atributy `name` jazyka HTML pro název výrazu zadaného v `asp-for` atributu. `asp-for="Property1.Property2"`je ekvivalentem `m => m.Property1.Property2`k. Název výrazu je, který se používá pro hodnotu `asp-for` atributu. Další informace najdete v části [názvy výrazů](#expression-names) .

* Nastaví hodnotu atributu `type` HTML na základě typu modelu a atributů [poznámek k datům](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) použitým pro vlastnost modelu.

* Pokud je zadaná hodnota `type` atributu HTML, nepřepíše se.

* Generuje atributy ověřování [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) z atributů [poznámky k datům](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) použitých na vlastnosti modelu.

* Má pomocná funkce HTML překryv s `Html.TextBoxFor` a `Html.EditorFor`. Podrobnosti najdete v části **alternativy pomocníka HTML pro vstupní nápovědu ke značce** .

* Poskytuje silné psaní. Pokud se název vlastnosti změní a pomocník značek neaktualizujete, zobrazí se chybová zpráva podobná následující:

```
An error occurred during the compilation of a resource required to process
this request. Please review the following specific error details and modify
your source code appropriately.

Type expected
 'RegisterViewModel' does not contain a definition for 'Email' and no
 extension method 'Email' accepting a first argument of type 'RegisterViewModel'
 could be found (are you missing a using directive or an assembly reference?)
```

Pomocník `Input` značek nastaví atribut HTML `type` na základě typu .NET. Následující tabulka uvádí některé typy běžných typů .NET a generovaný typ HTML (ne každý typ .NET je uveden).

|Typ .NET|Typ vstupu|
|---|---|
|Logická hodnota|Type = "CheckBox"|
|Řetězec|Type = "text"|
|DateTime|Type =["DateTime – místní"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)|
|Byte|Type = "Number"|
|Int|Type = "Number"|
|Jednoduchá, Dvojitá|Type = "Number"|

V následující tabulce jsou uvedeny některé z běžných atributů [datových poznámek](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) , které bude Pomocník pro vstupní značky mapovat na konkrétní vstupní typy (ne každý atribut ověření je uveden):

|Atribut|Typ vstupu|
|---|---|
|EmailAddress|Type = "e-mail"|
|Adresa URL|Type = "URL"|
|[HiddenInput]|Type = "Hidden"|
|Hovor|Type = "tel"|
|[DataType (DataType. Password)]|Type = "Password"|
|[DataType (DataType. Date)]|Type = "date"|
|[Datový typ (DataType. time)]|Type = "čas"|

Ukázka:

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-HTML[](working-with-forms/sample/final/Views/Demo/RegisterInput.cshtml)]

Výše uvedený kód generuje následující kód HTML:

```html
  <form method="post" action="/Demo/RegisterInput">
      Email:
      <input type="email" data-val="true"
             data-val-email="The Email Address field is not a valid email address."
             data-val-required="The Email Address field is required."
             id="Email" name="Email" value=""><br>
      Password:
      <input type="password" data-val="true"
             data-val-required="The Password field is required."
             id="Password" name="Password"><br>
      <button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

Datové poznámky použité pro vlastnosti `Email` a `Password` generují metadata modelu. Pomocná rutina vstupní značky spotřebovává metadata modelu a vytváří atributy [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` (viz [ověření modelu](../models/validation.md)). Tyto atributy popisují validátory, které se mají připojit ke vstupním polím. To poskytuje nenápadné ověřování HTML5 a [jQuery](https://jquery.com/) . Nenápadující `data-val-rule="Error Message"`atributy mají formát, kde pravidlo je název pravidla ověření (například `data-val-required`, `data-val-email`, `data-val-maxlength`atd.). Pokud je v atributu uvedena chybová zpráva, je zobrazena jako hodnota `data-val-rule` atributu. K dispozici jsou také atributy formuláře `data-val-ruleName-argumentName="argumentValue"` , které poskytují další podrobnosti o pravidlu, například `data-val-maxlength-max="1024"` .

### <a name="html-helper-alternatives-to-input-tag-helper"></a>Pomocné možnosti pro pomocníka HTML pro pomocníka vstupní značky

`Html.TextBox`, `Html.TextBoxFor` `Html.Editor` a `Html.EditorFor` mají překrývající se funkce s pomocníkem vstupní značky. Pomocná rutina vstupní značky automaticky nastaví `type` atribut. `Html.TextBox` a `Html.TextBoxFor` ne. `Html.Editor`a `Html.EditorFor` zpracovávají kolekce, komplexní objekty a šablony; Pomocná rutina vstupní značky není. Pomocná rutina vstupní `Html.EditorFor` značky `Html.TextBoxFor` a jsou silného typu (používají lambda výrazy); `Html.TextBox` a `Html.Editor` nejsou (používají názvy výrazů).

### <a name="htmlattributes"></a>HtmlAttributes

`@Html.Editor()`a `@Html.EditorFor()` při spouštění jejich `ViewDataDictionary` výchozích šablon `htmlAttributes` použijte speciální položku s názvem. Toto chování se volitelně rozšiřuje pomocí `additionalViewData` parametrů. Klíč "htmlAttributes" rozlišuje malá a velká písmena. Klíč "htmlAttributes" se zpracovává podobně jako `htmlAttributes` objekt předaný do vstupů pomocníků, `@Html.TextBox()`jako je.

```cshtml
@Html.EditorFor(model => model.YourProperty, 
  new { htmlAttributes = new { @class="myCssClass", style="Width:100px" } })
```

### <a name="expression-names"></a>Názvy výrazů

Hodnota `asp-for` atributu je `ModelExpression` a pravá strana výrazu lambda. Proto se `asp-for="Property1"` `m => m.Property1` ve vygenerovaném kódu zobrazí, což je důvod, proč nemusíte předponu používat `Model`. Můžete použít znak "\@" pro spuštění vloženého výrazu a přesunutí před: `m.`

```cshtml
@{
  var joe = "Joe";
}

<input asp-for="@joe">
```

Generuje následující:

```html
<input type="text" id="joe" name="joe" value="Joe">
```

Pomocí vlastností kolekce `asp-for="CollectionProperty[23].Member"` vygeneruje stejný název, jako `asp-for="CollectionProperty[i].Member"` `i` má hodnota `23`.

Když ASP.NET Core MVC vypočítá hodnotu `ModelExpression`, zkontroluje několik zdrojů, včetně. `ModelState` Vezměte `<input type="text" asp-for="@Name">`v úvahu. Počítaný `value` atribut je první hodnota, která není null od:

* `ModelState`položka s klíčem "Name".
* Výsledek výrazu `Model.Name`

### <a name="navigating-child-properties"></a>Navigace podřízených vlastností

Můžete také přejít k podřízeným vlastnostem pomocí cesty vlastností modelu zobrazení. Zvažte složitější třídu modelu, která obsahuje podřízenou `Address` vlastnost.

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/AddressViewModel.cs?highlight=1,2,3,4&range=5-8)]

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/RegisterAddressViewModel.cs?highlight=8&range=5-13)]

V zobrazení se svážeme s `Address.AddressLine1`:

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterAddress.cshtml?highlight=6)]

Následující kód HTML je vygenerován `Address.AddressLine1`pro:

```html
<input type="text" id="Address_AddressLine1" name="Address.AddressLine1" value="">
```

### <a name="expression-names-and-collections"></a>Názvy a kolekce výrazů

Ukázka modelu obsahujícího pole `Colors`:

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/Person.cs?highlight=3&range=5-10)]

Metoda Action:

```csharp
public IActionResult Edit(int id, int colorIndex)
{
    ViewData["Index"] = colorIndex;
    return View(GetPerson(id));
}
```

Následující syntaxe Razor ukazuje, jak přistupujete `Color` k určitému prvku:

[!code-HTML[](working-with-forms/sample/final/Views/Demo/EditColor.cshtml)]

Šablona *views/Shared/EditorTemplates/String. cshtml* :

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/String.cshtml)]

Ukázka pomocí `List<T>`:

[!code-csharp[](working-with-forms/sample/final/ViewModels/ToDoItem.cs?range=3-8)]

Následující Razor ukazuje, jak iterovat v kolekci:

[!code-HTML[](working-with-forms/sample/final/Views/Demo/Edit.cshtml)]

Šablona *views/Shared/EditorTemplates/ToDoItem. cshtml* :

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/ToDoItem.cshtml)]

`foreach`by měla být použita, pokud je to možné, pokud se hodnota bude používat `asp-for` v `Html.DisplayFor` kontextu nebo ekvivalentním kontextu. Obecně je lepší `for` než `foreach` (pokud to scénář umožňuje), protože není nutné přidělovat enumerátor; vyhodnocení indexeru ve výrazu LINQ ale může být nákladné a mělo by být minimalizováno.

&nbsp;

>[!NOTE]
>Výše uvedený vzorový kód ukazuje, jak byste měli výraz lambda nahradit `@` operátorem pro přístup ke každému `ToDoItem` ze seznamu.

## <a name="the-textarea-tag-helper"></a>Pomocník značek TextArea

Pomocný `Textarea Tag Helper` pomocník značek je podobný jako pomocník pro vstupní značky.

* Vygeneruje `id` atributy `name` a a atributy ověřování dat z modelu pro prvek [ \<TextArea>](https://www.w3.org/wiki/HTML/Elements/textarea) element.

* Poskytuje silné psaní.

* Alternativní pomocník HTML:`Html.TextAreaFor`

Ukázka:

[!code-csharp[](working-with-forms/sample/final/ViewModels/DescriptionViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterTextArea.cshtml?highlight=4)]

Vygeneruje se následující kód HTML:

```html
<form method="post" action="/Demo/RegisterTextArea">
  <textarea data-val="true"
   data-val-maxlength="The field Description must be a string or array type with a maximum length of &#x27;1024&#x27;."
   data-val-maxlength-max="1024"
   data-val-minlength="The field Description must be a string or array type with a minimum length of &#x27;5&#x27;."
   data-val-minlength-min="5"
   id="Description" name="Description">
  </textarea>
  <button type="submit">Test</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-label-tag-helper"></a>Pomocník značek popisku

* Vygeneruje popisek a `for` atribut popisku>elementu [ \<popisku](https://www.w3.org/wiki/HTML/Elements/label) pro název výrazu.

* Alternativní pomocník HTML: `Html.LabelFor`.

`Label Tag Helper` Poskytuje následující výhody oproti čistě prvku popisku HTML:

* Z `Display` atributu se automaticky získá popisná hodnota popisku. Zamýšlené zobrazované jméno se může v průběhu času měnit a kombinace `Display` atributu a pomocníka značky Label bude používat `Display` všude, kde se používá.

* Míň značek ve zdrojovém kódu

* Silné zadání pomocí vlastnosti modelu

Ukázka:

[!code-csharp[](working-with-forms/sample/final/ViewModels/SimpleViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterLabel.cshtml?highlight=4)]

Pro `<label>` element je vygenerován následující kód HTML:

```html
<label for="Email">Email Address</label>
```

Pomocník značek Label vygeneroval hodnotu `for` atributu "e-mail", což je ID přidružené k `<input>` elementu. Pomocník značek generuje konzistentní `id` prvky a `for` , aby je bylo možné správně přidružit. Titulek v této ukázce pochází z `Display` atributu. Pokud model neobsahoval `Display` atribut, popisek by byl názvem vlastnosti výrazu.

## <a name="the-validation-tag-helpers"></a>Pomocníka značek ověřování

K dispozici jsou dva pomocníky ověřovacích značek. `Validation Message Tag Helper` (Který zobrazuje ověřovací zprávu pro jednu vlastnost v modelu) a `Validation Summary Tag Helper` (která zobrazuje souhrn chyb ověřování). `Input Tag Helper` Přidá atributy ověřování na straně klienta HTML5 pro vstupní prvky na základě atributů datových poznámek v třídách modelu. Ověřování se provádí také na serveru. Pomocník pro ověřování značek zobrazí tyto chybové zprávy, když dojde k chybě ověření.

### <a name="the-validation-message-tag-helper"></a>Pomocný pomocník značek ověřovací zprávy

* Přidá atribut [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-valmsg-for="property"` do prvku [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) , který připojí chybové zprávy ověřování ve vstupním poli zadané vlastnosti modelu.   Když dojde k chybě ověřování na straně klienta, [jQuery](https://jquery.com/) zobrazí chybovou zprávu v `<span>` elementu.

* Ověřování probíhá také na serveru. Klienti můžou mít zakázaný JavaScript a některé ověřování se dá dělat jenom na straně serveru.

* Alternativní pomocník HTML:`Html.ValidationMessageFor`

`Validation Message Tag Helper` Se používá s `asp-validation-for` atributem elementu [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) HTML.

```cshtml
<span asp-validation-for="Email"></span>
```

Pomocný pomocník značek ověřovací zprávy vygeneruje následující kód HTML:

```html
<span class="field-validation-valid"
  data-valmsg-for="Email"
  data-valmsg-replace="true"></span>
```

Obecně použijete `Validation Message Tag Helper` pomocníka `Input` značky pro stejnou vlastnost. Tím se zobrazí všechny chybové zprávy ověřování poblíž vstupu, který způsobil chybu.

> [!NOTE]
> Musíte mít zobrazení se správnými odkazy skriptu JavaScript a [jQuery](https://jquery.com/) pro ověřování na straně klienta. Další informace najdete v tématu [ověřování modelu](../models/validation.md) .

Pokud dojde k chybě ověřování na straně serveru (například když máte zakázané vlastní ověřování na straně serveru nebo když je ověřování na straně klienta zakázané), MVC umístí tuto chybovou zprávu `<span>` jako tělo elementu.

```html
<span class="field-validation-error" data-valmsg-for="Email"
            data-valmsg-replace="true">
   The Email Address field is required.
</span>
```

### <a name="the-validation-summary-tag-helper"></a>Nápověda k souhrnným značkám ověřování

* Cílí `<div>` na `asp-validation-summary` elementy s atributem.

* Alternativní pomocník HTML:`@Html.ValidationSummary`

`Validation Summary Tag Helper` Slouží k zobrazení souhrnu ověřovacích zpráv. Hodnota `asp-validation-summary` atributu může být libovolná z následujících hodnot:

|ASP – ověření – souhrn|Zobrazené ověřovací zprávy|
|--- |--- |
|Ovládací souhrnu ověření. All|Úroveň vlastnosti a modelu|
|Ovládací souhrnu ověření. ModelOnly|Model|
|Ovládací souhrnu ověření. None|Žádná|

### <a name="sample"></a>Ukázka

V následujícím příkladu má `DataAnnotation` datový model atributy, které generují chybové zprávy ověřování na `<input>` elementu.  Pokud dojde k chybě ověřování, Pomocník pro ověření značky zobrazí chybovou zprávu:

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterValidation.cshtml?highlight=4,6,8&range=1-10)]

Generovaný kód HTML (Pokud je model platný):

```html
<form action="/DemoReg/Register" method="post">
  <div class="validation-summary-valid" data-valmsg-summary="true">
  <ul><li style="display:none"></li></ul></div>
  Email:  <input name="Email" id="Email" type="email" value=""
   data-val-required="The Email field is required."
   data-val-email="The Email field is not a valid email address."
   data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Email"></span><br>
  Password: <input name="Password" id="Password" type="password"
   data-val-required="The Password field is required." data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Password"></span><br>
  <button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-select-tag-helper"></a>Pomocná rutina pro výběr značky

* Generuje [vybrané](https://www.w3.org/wiki/HTML/Elements/select) a přidružené prvky [možností](https://www.w3.org/wiki/HTML/Elements/option) pro vlastnosti modelu.

* Má alternativu `Html.DropDownListFor` k Pomocníkovi HTML a`Html.ListBoxFor`

`Select Tag Helper` [select](https://www.w3.org/wiki/HTML/Elements/select) `asp-items` [option](https://www.w3.org/wiki/HTML/Elements/option) Určuje název vlastnosti modelu pro element SELECT a určuje prvky možností. `asp-for`  Příklad:

[!code-HTML[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

Ukázka:

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryViewModel.cs)]

`Index` Metoda inicializuje `CountryViewModel`, nastaví vybranou zemi a předá ji do `Index` zobrazení.

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=8-13)]

Metoda HTTP POST `Index` zobrazuje výběr:

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=15-27)]

`Index` Zobrazení:

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?highlight=4)]

Který generuje následující HTML (s vybraným certifikačním úřadem):

```html
<form method="post" action="/">
     <select id="Country" name="Country">
       <option value="MX">Mexico</option>
       <option selected="selected" value="CA">Canada</option>
       <option value="US">USA</option>
     </select>
       <br /><button type="submit">Register</button>
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

> [!NOTE]
> Nedoporučujeme používat `ViewBag` nebo `ViewData` s pomocníkem pro výběr značky. Model zobrazení je robustnější při poskytování metadat MVC a obecně méně problematických.

Hodnota `asp-for` atributu je zvláštní případ a nevyžaduje `Model` předponu, ostatní pomocné atributy značek (například `asp-items`).

[!code-HTML[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

### <a name="enum-binding"></a>Vazba výčtu

Je často vhodné `<select>` použít s `enum` vlastností a generovat `SelectListItem` prvky z `enum` hodnot.

Ukázka:

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnumViewModel.cs?range=3-7)]

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs)]

`GetEnumSelectList` Metoda generuje `SelectList` objekt pro výčet.

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEnum.cshtml?highlight=5)]

Můžete označit seznam enumerátorů pomocí `Display` atributu pro získání BOHATŠÍHO uživatelského rozhraní:

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs?highlight=5,7)]

Vygeneruje se následující kód HTML:

```html
  <form method="post" action="/Home/IndexEnum">
         <select data-val="true" data-val-required="The EnumCountry field is required."
                 id="EnumCountry" name="EnumCountry">
             <option value="0">United Mexican States</option>
             <option value="1">United States of America</option>
             <option value="2">Canada</option>
             <option value="3">France</option>
             <option value="4">Germany</option>
             <option selected="selected" value="5">Spain</option>
         </select>
         <br /><button type="submit">Register</button>
         <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
    </form>
```

### <a name="option-group"></a>Skupina možností

Prvek HTML [ \<optgroup>](https://www.w3.org/wiki/HTML/Elements/optgroup) je generována, když model zobrazení obsahuje jeden nebo více `SelectListGroup` objektů.

`CountryViewModelGroup` Seskupí `SelectListItem` prvky do skupin "Severní Amerika" a "Evropa":

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelGroup.cs?highlight=5,6,14,20,26,32,38,44&range=6-56)]

Níže jsou uvedené dvě skupiny:

![příklad skupiny možností](working-with-forms/_static/grp.png)

Generovaný kód HTML:

```html
 <form method="post" action="/Home/IndexGroup">
      <select id="Country" name="Country">
          <optgroup label="North America">
              <option value="MEX">Mexico</option>
              <option value="CAN">Canada</option>
              <option value="US">USA</option>
          </optgroup>
          <optgroup label="Europe">
              <option value="FR">France</option>
              <option value="ES">Spain</option>
              <option value="DE">Germany</option>
          </optgroup>
      </select>
      <br /><button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
```

### <a name="multiple-select"></a>Vícenásobný výběr

Pomocník pro výběr značky automaticky vygeneruje atribut [Multiple = Multiple](https://w3c.github.io/html-reference/select.html) , pokud je vlastnost zadaná v `asp-for` atributu. `IEnumerable` Například s ohledem na následující model:

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelIEnumerable.cs?highlight=6)]

S následujícím zobrazením:

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexMultiSelect.cshtml?highlight=4)]

Generuje následující kód HTML:

```html
<form method="post" action="/Home/IndexMultiSelect">
    <select id="CountryCodes"
    multiple="multiple"
    name="CountryCodes"><option value="MX">Mexico</option>
<option value="CA">Canada</option>
<option value="US">USA</option>
<option value="FR">France</option>
<option value="ES">Spain</option>
<option value="DE">Germany</option>
</select>
    <br /><button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

### <a name="no-selection"></a>Žádný výběr

Pokud se vám na více stránkách používá možnost neurčeno, můžete vytvořit šablonu pro odstranění opakujícího se kódu HTML:

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEmptyTemplate.cshtml?highlight=5)]

Šablona *views/Shared/EditorTemplates/CountryViewModel. cshtml* :

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/CountryViewModel.cshtml)]

Přidání možnosti jazyka HTML [ \<>](https://www.w3.org/wiki/HTML/Elements/option) prvky není omezeno na případ *výběru* . Například následující metoda zobrazení a akce vygeneruje HTML podobný kódu výše:

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?name=snippetNone)]

[!code-HTML[](working-with-forms/sample/final/Views/Home/IndexOption.cshtml)]

V závislosti `<option>` na aktuální `Country` hodnotě bude vybrán správný prvek `selected="selected"` (obsahující atribut).

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=114-119)]

```html
 <form method="post" action="/Home/IndexEmpty">
      <select id="Country" name="Country">
          <option value="">&lt;none&gt;</option>
          <option value="MX">Mexico</option>
          <option value="CA" selected="selected">Canada</option>
          <option value="US">USA</option>
      </select>
      <br /><button type="submit">Register</button>
   <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
 ```

## <a name="additional-resources"></a>Další zdroje

* <xref:mvc/views/tag-helpers/intro>
* [Element HTML Form](https://www.w3.org/TR/html401/interact/forms.html)
* [Žádost o ověření tokenu](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)
* <xref:mvc/models/model-binding>
* <xref:mvc/models/validation>
* [Rozhraní IAttributeAdapter](/dotnet/api/Microsoft.AspNetCore.Mvc.DataAnnotations.IAttributeAdapter)
* [Fragmenty kódu pro tento dokument](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/working-with-forms/sample/final)

---
title: Pomocných rutin značek ve formulářích v ASP.NET Core
author: rick-anderson
description: Popisuje předdefinované pomocných rutin značek použít s formuláři.
ms.author: riande
ms.custom: mvc
ms.date: 1/11/2019
uid: mvc/views/working-with-forms
ms.openlocfilehash: cd15c641fbf702071bd57510a1d51737f6ab8e19
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54099010"
---
# <a name="tag-helpers-in-forms-in-aspnet-core"></a>Pomocných rutin značek ve formulářích v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT), [Dave Paquette](https://twitter.com/Dave_Paquette), a [Jerrie Pelser](https://github.com/jerriep)

Tento dokument vysvětluje práce s formuláři a elementů HTML běžně používají na formuláři. Kód HTML [formuláře](https://www.w3.org/TR/html401/interact/forms.html) element poskytuje primární mechanismus webové aplikace využívají k odesílání dat zpět na server. Většina tento dokument popisuje [pomocných rutin značek](tag-helpers/intro.md) a jak se vám může pomoci produktivně vytvářet robustní formuláře HTML. Doporučujeme si přečíst [Úvod do pomocné rutiny značek](tag-helpers/intro.md) předtím, než čtete tento dokument.

V mnoha případech pomocných rutin HTML poskytnout alternativní způsob konkrétní pomocné rutiny značky, ale je důležité uvědomit si, že pomocné rutiny značek nemáte nahradit pomocné rutiny HTML a není k dispozici pomocné rutiny značky pro každý pomocné rutiny HTML. Pokud existuje alternativní pomocné rutiny HTML, je uvedený.

<a name="my-asp-route-param-ref-label"></a>

## <a name="the-form-tag-helper"></a>Pomocná rutina značky formuláře

[Formuláře](https://www.w3.org/TR/html401/interact/forms.html) pomocné rutiny značky:

* Generuje kód HTML [ \<formulář >](https://www.w3.org/TR/html401/interact/forms.html) `action` hodnota atributu pro akce řadiče MVC nebo pojmenovanou trasu

* Vytváří skrytý [tokenu pro ověření žádosti](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) paděláním požadavku mezi weby (při použití s `[ValidateAntiForgeryToken]` atributu v metodě akce HTTP Post)

* Poskytuje `asp-route-<Parameter Name>` atribut, ve kterém `<Parameter Name>` se přidá do hodnoty trasy. `routeValues` Parametry `Html.BeginForm` a `Html.BeginRouteForm` poskytuje podobné funkce.

* Má alternativu pomocné rutiny HTML `Html.BeginForm` a `Html.BeginRouteForm`

Ukázka:

[!code-HTML[](working-with-forms/sample/final/Views/Demo/RegisterFormOnly.cshtml)]

Pomocná rutina formuláře značky výše uvedené vytvoří následující kód HTML:

```HTML
<form method="post" action="/Demo/Register">
    <!-- Input and Submit elements -->
    <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
</form>
```

Generuje modul runtime pro MVC `action` hodnotu atributu z atributů pomocné rutiny značky formuláře `asp-controller` a `asp-action`. Pomocná rutina značky formuláře také vytváří skrytý [tokenu pro ověření žádosti](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) paděláním požadavku mezi weby (při použití s `[ValidateAntiForgeryToken]` atributu v metodě akce HTTP Post). Ochrana proti padělání žádosti více webů čistě formuláře HTML je obtížné, pomocné rutiny značky formuláře poskytuje tato služba za vás.

### <a name="using-a-named-route"></a>Pomocí pojmenovanou trasu

`asp-route` Pomocné rutiny značky atribut může také generovat značky HTML `action` atribut. Aplikaci se službou [trasy](../../fundamentals/routing.md) s názvem `register` použít následující kód pro registrační stránky:

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterRoute.cshtml)]

Počet zobrazení v *zobrazení/účet* složky (vygeneruje, když vytvoříte novou webovou aplikaci s *jednotlivé uživatelské účty*) obsahují [asp. trasa returnurl](xref:mvc/views/working-with-forms) atribut:

```cshtml
<form asp-controller="Account" asp-action="Login"
     asp-route-returnurl="@ViewData["ReturnUrl"]"
     method="post" class="form-horizontal" role="form">
```

>[!NOTE]
>Pomocí integrované šablony `returnUrl` se jenom vyplní automaticky při pokusu o přístup k autorizovaným prostředků, ale nejsou ověřeny nebo oprávnění. Při pokusu o neoprávněný přístup, middleware zabezpečení vás přesměruje na přihlašovací stránku s `returnUrl` nastavit.

## <a name="the-input-tag-helper"></a>Pomocná rutina vstupní značky

Sváže vstupní pomocné rutiny značky HTML [ \<vstupní >](https://www.w3.org/wiki/HTML/Elements/input) element modelu výrazu v zobrazení syntaxe razor.

Syntaxe:

```HTML
<input asp-for="<Expression Name>" />
```

Pomocná rutina vstupní značky:

* Generuje `id` a `name` atributy HTML pro název výraz zadaný v `asp-for` atribut. `asp-for="Property1.Property2"` je ekvivalentní `m => m.Property1.Property2`. Název výrazu se používá pro `asp-for` hodnotu atributu. Zobrazit [názvy výrazů](#expression-names) části Další informace.

* Nastaví kód HTML `type` atribut hodnotu podle typu modelu a [dat. Poznámka](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) atributy použité na vlastnost modelu

* Nedojde k přepsání HTML `type` když je zadaná hodnota atributu

* Generuje [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) atributy ověření ze [dat. Poznámka](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) atributy použité na vlastnosti modelu

* Obsahuje funkci pomocné rutiny HTML, které se překrývají s `Html.TextBoxFor` a `Html.EditorFor`. Zobrazit **alternativy pomocné rutiny HTML pro pomocné rutiny značky vstup** podrobné informace.

* Poskytuje silné typování. Pokud název změny vlastností a vy nechcete aktualizovat pomocné rutiny značky získáte chybu podobný následujícímu:

```HTML
An error occurred during the compilation of a resource required to process
this request. Please review the following specific error details and modify
your source code appropriately.

Type expected
 'RegisterViewModel' does not contain a definition for 'Email' and no
 extension method 'Email' accepting a first argument of type 'RegisterViewModel'
 could be found (are you missing a using directive or an assembly reference?)
```

`Input` Nastaví pomocné rutiny značky HTML `type` atribut založený na typ .NET. Následující tabulka uvádí některé běžné typy .NET a generovaný typ HTML (ne každý typ formátu .NET je uvedena).

|Typ formátu .NET|Typ vstupu|
|---|---|
|BOOL|typ = "zaškrtávací políčko"|
|String|typ = "text"|
|DateTime|typ =["místní data a času"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)|
|Byte|typ = "cislo"|
|Int|typ = "cislo"|
|Jednoduché, Double|typ = "cislo"|


V následující tabulce jsou uvedeny některé běžné [anotacemi dat](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) atributy, které pomocné rutiny vstupní značky se mapují na konkrétní typy vstupu (ne každý atribut ověření je uvedená):


|Atribut|Typ vstupu|
|---|---|
|[EmailAddress]|typ = "e-mail"|
|[Url]|typ = "url"|
|[HiddenInput]|typ = "skrytá"|
|[Phone]|typ = "tel"|
|[DataType(DataType.Password)]| typ = "password"|
|[DataType(DataType.Date)]| typ = "datum"|
|[DataType(DataType.Time)]| typ = "čas"|


Ukázka:

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-HTML[](working-with-forms/sample/final/Views/Demo/RegisterInput.cshtml)]

Výše uvedený kód generuje následující kód HTML:

```HTML
  <form method="post" action="/Demo/RegisterInput">
       Email:
       <input type="email" data-val="true"
              data-val-email="The Email Address field is not a valid email address."
              data-val-required="The Email Address field is required."
              id="Email" name="Email" value="" /> <br>
       Password:
       <input type="password" data-val="true"
              data-val-required="The Password field is required."
              id="Password" name="Password" /><br>
       <button type="submit">Register</button>
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
   </form>
```

Datové poznámky u `Email` a `Password` vlastnosti generovat metadata v modelu. Pomocná rutina značky vstup využívá metadat modelu a vytváří [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` atributy (viz [ověření modelu](../models/validation.md)). Tyto atributy popisují validátory pro připojení k vstupních polí. To umožňuje nerušivý HTML5 a [jQuery](https://jquery.com/) ověření. Nerušivý atributy mají formát `data-val-rule="Error Message"`, kde pravidlo je název ověřovacího pravidla (třeba `data-val-required`, `data-val-email`, `data-val-maxlength`atd.) Pokud chybová zpráva v atributu se zobrazí jako hodnota `data-val-rule` atribut. Existují také atributy ve formátu `data-val-ruleName-argumentName="argumentValue"` , které poskytují další informace o pravidle, třeba `data-val-maxlength-max="1024"` .

### <a name="html-helper-alternatives-to-input-tag-helper"></a>Alternativy pomocné rutiny HTML pro pomocné rutiny značky vstup

`Html.TextBox`, `Html.TextBoxFor`, `Html.Editor` a `Html.EditorFor` překrývat funkcí pomocné rutiny značky vstup. Pomocná rutina značky vstup automaticky nastaví `type` atribut; `Html.TextBox` a `Html.TextBoxFor` nebude. `Html.Editor` a `Html.EditorFor` zpracování kolekce komplexních objektů a šablon; není pomocné rutiny značky vstup. Pomocná rutina značky vstup `Html.EditorFor` a `Html.TextBoxFor` jsou silného typu (používají výrazy lambda); `Html.TextBox` a `Html.Editor` nejsou (používají názvy výrazů).

### <a name="htmlattributes"></a>HtmlAttributes

`@Html.Editor()` a `@Html.EditorFor()` použít speciální `ViewDataDictionary` položku s názvem `htmlAttributes` při provádění jejich výchozích šablon. Toto chování je volitelně rozšířeno pomocí `additionalViewData` parametry. Klíč "htmlAttributes" je velká a malá písmena. Klíč "htmlAttributes" probíhá podobně jako `htmlAttributes` objekt předaný k zadání pomocných rutin, jako je `@Html.TextBox()`.

```HTML
@Html.EditorFor(model => model.YourProperty, 
  new { htmlAttributes = new { @class="myCssClass", style="Width:100px" } })
```

### <a name="expression-names"></a>Názvy výrazů

`asp-for` Hodnota atributu je `ModelExpression` a pravou stranu výrazu lambda. Proto `asp-for="Property1"` stane `m => m.Property1` ve vygenerovaném kódu, což je důvod, proč není nutné použijte předponu `Model`. Můžete použít "\@" znak spuštění vložený výraz a před přesunout `m.`:

```HTML
@{
       var joe = "Joe";
   }
   <input asp-for="@joe" />
```

Generuje následující:

```HTML
<input type="text" id="joe" name="joe" value="Joe" />
```

S vlastnostmi kolekce `asp-for="CollectionProperty[23].Member"` generuje stejný název jako `asp-for="CollectionProperty[i].Member"` při `i` má hodnotu `23`.

Když ASP.NET Core MVC vypočítá hodnotu `ModelExpression`, zkontroluje několik zdrojů, včetně `ModelState`. Vezměte v úvahu `<input type="text" asp-for="@Name" />`. Vypočítané `value` atribut je první nenulová hodnota z:

* `ModelState` Položka s klíčem "Name".
* Výsledek výrazu `Model.Name`.

### <a name="navigating-child-properties"></a>Podřízené vlastnosti navigace

Můžete také přejít na podřízené vlastnosti pomocí cesta k vlastnosti zobrazení modelu. Vezměte v úvahu složitější třídu modelu, která obsahuje podřízenou `Address` vlastnost.

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/AddressViewModel.cs?highlight=1,2,3,4&range=5-8)]

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/RegisterAddressViewModel.cs?highlight=8&range=5-13)]

V zobrazení, můžeme vytvořit vazbu k `Address.AddressLine1`:

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterAddress.cshtml?highlight=6)]

Následující kód HTML je generován pro `Address.AddressLine1`:

```HTML
<input type="text" id="Address_AddressLine1" name="Address.AddressLine1" value="" />
```

### <a name="expression-names-and-collections"></a>Názvy výrazů a kolekce

Ukázkový model obsahující pole `Colors`:

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/Person.cs?highlight=3&range=5-10)]

Metoda akce:

```csharp
public IActionResult Edit(int id, int colorIndex)
   {
       ViewData["Index"] = colorIndex;
       return View(GetPerson(id));
   }
```

Následující syntaxe Razor ukazuje, jak získat přístup k určitému `Color` element:

[!code-HTML[](working-with-forms/sample/final/Views/Demo/EditColor.cshtml)]

*Views/Shared/EditorTemplates/String.cshtml* šablony:

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/String.cshtml)]

Ukázkový používání `List<T>`:

[!code-csharp[](working-with-forms/sample/final/ViewModels/ToDoItem.cs?range=3-8)]

Následující syntaxe Razor ukazuje, jak k iteraci v kolekci:

[!code-HTML[](working-with-forms/sample/final/Views/Demo/Edit.cshtml)]

*Views/Shared/EditorTemplates/ToDoItem.cshtml* šablony:

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/ToDoItem.cshtml)]

`foreach` má být použit pokud je to možné, při hodnotě se bude používat v `asp-for` nebo `Html.DisplayFor` ekvivalentní kontextu. Obecně platí `for` je obecně lepší než `foreach` (Pokud tento scénář umožňuje) vzhledem k tomu, že to nemusí přidělovat enumerátor; ale vyhodnocování indexeru ve výrazu LINQ může být nákladné a byste měli minimalizovat.

&nbsp;

>[!NOTE]
>Výše uvedené komentářem ukázkový kód ukazuje, jak by nahradit výraz lambda s `@` operátor má přístup ke všem `ToDoItem` v seznamu.

## <a name="the-textarea-tag-helper"></a>Pomocná rutina značky Textarea

`Textarea Tag Helper` Pomocné rutiny značky je podobný pomocné rutiny značky vstup.

* Generuje `id` a `name` atributů a atributů ověření dat z modelu pro [ \<textarea >](https://www.w3.org/wiki/HTML/Elements/textarea) elementu.

* Poskytuje silné typování.

* Pomocné rutiny HTML ve zkratce: `Html.TextAreaFor`

Ukázka:

[!code-csharp[](working-with-forms/sample/final/ViewModels/DescriptionViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterTextArea.cshtml?highlight=4)]

Následující kód HTML je vygenerována:

```HTML
<form method="post" action="/Demo/RegisterTextArea">
  <textarea data-val="true"
   data-val-maxlength="The field Description must be a string or array type with a maximum length of &#x27;1024&#x27;."
   data-val-maxlength-max="1024"
   data-val-minlength="The field Description must be a string or array type with a minimum length of &#x27;5&#x27;."
   data-val-minlength-min="5"
   id="Description" name="Description">
  </textarea>
  <button type="submit">Test</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
</form>
```

## <a name="the-label-tag-helper"></a>Pomocná rutina značky popisek

* Generuje titulek popisek a `for` atribut na [ <label> ](https://www.w3.org/wiki/HTML/Elements/label) – element pro název výrazu

* Pomocné rutiny HTML alternativní: `Html.LabelFor`.

`Label Tag Helper` Přes čistě element HTML label poskytuje následující výhody:

* Automaticky získána hodnota popisné označení `Display` atribut. Zamýšlený zobrazované jméno může změnit postupem času zdokonalují kombinace `Display` atribut a pomocná rutina značek v popisku se vztahují `Display` všude, kde se používá.

* Méně značek ve zdrojovém kódu

* Silné typování s vlastností modelu.

Ukázka:

[!code-csharp[](working-with-forms/sample/final/ViewModels/SimpleViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterLabel.cshtml?highlight=4)]

Následující kód HTML je generován pro `<label>` element:

```HTML
<label for="Email">Email Address</label>
```

Pomocná rutina značky popisek, vygeneruje `for` hodnotu atributu "Email", což je Identifikátor přidružený `<input>` elementu. Pomocné rutiny značek generovat konzistentní `id` a `for` prvky, aby mohly být správně přidružena. Titulek v této ukázce se segmenty Convenience `Display` atribut. Pokud model neobsahovalo `Display` atribut, popisek bude název vlastnosti výrazu.

## <a name="the-validation-tag-helpers"></a>Pomocné rutiny značek ověření

Existují dva pomocných rutin značek ověření. `Validation Message Tag Helper` (Který zobrazí zprávu ověření pro jedinou vlastnost v modelu) a `Validation Summary Tag Helper` (který zobrazuje souhrn chyb ověřování). `Input Tag Helper` Přidá atributů ověření na straně HTML5 klienta k zadání prvky založené na datech anotace atributů ve třídách modelu. Ověřování provádí na serveru. Pomocná rutina značky ověření zobrazí těchto chybových zpráv při výskytu chyby ověření.

### <a name="the-validation-message-tag-helper"></a>Pomocná rutina značky zpráva ověření

* Přidá [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-valmsg-for="property"` atribut [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) element, který připojí chybové zprávy ověření na vstupní pole vlastnosti zadaného modelu. Pokud dojde k chybě ověření na straně klienta, [jQuery](https://jquery.com/) zobrazí chybová zpráva portálu `<span>` elementu.

* Taky uplatněním ověřování na serveru. Klienti mohou mít zakázaný JavaScript a nějaké ověření lze provést pouze na straně serveru.

* Pomocné rutiny HTML ve zkratce: `Html.ValidationMessageFor`

`Validation Message Tag Helper` Se používá s `asp-validation-for` atribut HTML [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) elementu.

```HTML
<span asp-validation-for="Email"></span>
```

Pomocná rutina značky zpráva ověření bude generovat následující kód HTML:

```HTML
<span class="field-validation-valid"
  data-valmsg-for="Email"
  data-valmsg-replace="true"></span>
```

Obvykle použijete `Validation Message Tag Helper` po `Input` pomocné rutiny značky pro stejnou vlastnost. Tím se zobrazí chybové zprávy ověření téměř vstup, který způsobil chybu.

> [!NOTE]
> Musíte mít zobrazení správné JavaScript a [jQuery](https://jquery.com/) skriptu odkazy na místě pro ověřování na straně klienta. Zobrazit [ověření modelu](../models/validation.md) Další informace.

Pokud dojde k chybě ověření na straně serveru, (například když máte vlastní server ověřování na straně nebo je zakázáno ověřování na straně klienta), MVC umístí danou chybovou zprávu jako text `<span>` elementu.

```HTML
<span class="field-validation-error" data-valmsg-for="Email"
            data-valmsg-replace="true">
   The Email Address field is required.
</span>
```

### <a name="the-validation-summary-tag-helper"></a>Pomocná rutina pro ověření Summary – značka

* Cíle `<div>` elementy `asp-validation-summary` atribut

* Pomocné rutiny HTML ve zkratce: `@Html.ValidationSummary`

`Validation Summary Tag Helper` Slouží k zobrazení shrnutí ověřovacích zpráv. `asp-validation-summary` Hodnota atributu může být cokoli z následujícího:

|Souhrn ověření ASP|Zobrazí ověřovacích zpráv|
|--- |--- |
|ValidationSummary.All|Vlastnost a model úroveň|
|ValidationSummary.ModelOnly|Model|
|ValidationSummary.None|Žádná|

### <a name="sample"></a>Ukázka

V následujícím příkladu je doplněn datový model `DataAnnotation` atributy, které generuje chybové zprávy ověření na `<input>` elementu.  Pokud dojde k chybě ověření, zobrazí pomocná rutina značky ověření chybová zpráva:

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterValidation.cshtml?highlight=4,6,8&range=1-10)]

Generovaný kód HTML, (když je model platný):

```HTML
<form action="/DemoReg/Register" method="post">
  <div class="validation-summary-valid" data-valmsg-summary="true">
  <ul><li style="display:none"></li></ul></div>
  Email:  <input name="Email" id="Email" type="email" value=""
   data-val-required="The Email field is required."
   data-val-email="The Email field is not a valid email address."
   data-val="true"> <br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Email"></span><br>
  Password: <input name="Password" id="Password" type="password"
   data-val-required="The Password field is required." data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Password"></span><br>
  <button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
</form>
```

## <a name="the-select-tag-helper"></a>Pomocná rutina vyberte značky

* Generuje [vyberte](https://www.w3.org/wiki/HTML/Elements/select) a přidružených [možnost](https://www.w3.org/wiki/HTML/Elements/option) prvky pro vlastnosti modelu.

* Má alternativu pomocné rutiny HTML `Html.DropDownListFor` a `Html.ListBoxFor`

`Select Tag Helper` `asp-for` Určuje název vlastnosti modelu [vyberte](https://www.w3.org/wiki/HTML/Elements/select) elementu a `asp-items` Určuje [možnost](https://www.w3.org/wiki/HTML/Elements/option) elementy.  Příklad:

[!code-HTML[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

Ukázka:

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryViewModel.cs)]

`Index` Metoda inicializuje `CountryViewModel`, nastaví pro vybranou zemi a předává jej do `Index` zobrazení.

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=8-13)]

HTTP POST `Index` metoda zobrazí výběr:

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=15-27)]

`Index` Zobrazení:

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?highlight=4)]

Které generují následující kód HTML (s "CA" vybrali):

```html
<form method="post" action="/">
     <select id="Country" name="Country">
       <option value="MX">Mexico</option>
       <option selected="selected" value="CA">Canada</option>
       <option value="US">USA</option>
     </select>
       <br /><button type="submit">Register</button>
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
   </form>
```

> [!NOTE]
> Nedoporučujeme ale používat `ViewBag` nebo `ViewData` s pomocné rutiny značky vyberte. Model zobrazení je na poskytování metadat MVC více robustní a obecně menší problém.

`asp-for` Hodnota atributu je zvláštní případ a nevyžaduje, aby `Model` prefix, ostatní atributy pomocné rutiny značky (například `asp-items`)

[!code-HTML[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

### <a name="enum-binding"></a>Výčet vazby

Často je vhodné použít `<select>` s `enum` vlastnost a generovat `SelectListItem` elementy ze `enum` hodnoty.

Ukázka:

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnumViewModel.cs?range=3-7)]

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs)]

`GetEnumSelectList` Generuje metodu `SelectList` objekt výčtu.

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEnum.cshtml?highlight=5)]

Můžete uspořádání seznamu výčtu s `Display` atribut získat bohatší uživatelské rozhraní:

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs?highlight=5,7)]

Následující kód HTML je vygenerována:

```HTML
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
         <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
    </form>
```

### <a name="option-group"></a>Možnost skupiny

Kód HTML [ \<skupiny voleb >](https://www.w3.org/wiki/HTML/Elements/optgroup) element se vygeneruje, když model zobrazení obsahuje jednu nebo více `SelectListGroup` objekty.

`CountryViewModelGroup` Skupin `SelectListItem` prvky do skupiny "Severní Amerika" a "Evropa":

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelGroup.cs?highlight=5,6,14,20,26,32,38,44&range=6-56)]

Dvě skupiny jsou uvedeny níže:

![Příklad možnost skupiny](working-with-forms/_static/grp.png)

Generovaný kód HTML:

```HTML
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
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
 </form>
```

### <a name="multiple-select"></a>Vícenásobný výběr

Pomocná rutina značky vyberte automaticky vygeneruje [více = "více"](http://w3c.github.io/html-reference/select.html) atribut, pokud v rámci specifikovaná vlastnost `asp-for` atribut je `IEnumerable`. Mějme například následující model:

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelIEnumerable.cs?highlight=6)]

S následujícím způsobem:

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexMultiSelect.cshtml?highlight=4)]

Generuje následující kód HTML:

```HTML
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
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
</form>
```

### <a name="no-selection"></a>Žádný výběr

Pokud se pomocí možnosti "nebyl zadán" ve více stránek, můžete vytvořit šablonu chcete-li odstranit opakující se kód HTML:

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEmptyTemplate.cshtml?highlight=5)]

*Views/Shared/EditorTemplates/CountryViewModel.cshtml* šablony:

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/CountryViewModel.cshtml)]

Přidání kódu HTML [ \<možnost >](https://www.w3.org/wiki/HTML/Elements/option) se neomezuje na prvky *žádný výběr* případ. Například následující metodu pro zobrazení a akce se generují kód HTML podobně jako výše uvedený kód:

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=114-119)]

[!code-HTML[](working-with-forms/sample/final/Views/Home/IndexOption.cshtml)]

Správné `<option>` bude vybraný element (obsahují `selected="selected"` atribut) v závislosti na aktuální `Country` hodnotu.

```HTML
 <form method="post" action="/Home/IndexEmpty">
      <select id="Country" name="Country">
          <option value="">&lt;none&gt;</option>
          <option value="MX">Mexico</option>
          <option value="CA" selected="selected">Canada</option>
          <option value="US">USA</option>
      </select>
      <br /><button type="submit">Register</button>
   <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
 </form>
 ```

## <a name="additional-resources"></a>Další zdroje

* <xref:mvc/views/tag-helpers/intro>
* [Element formuláře HTML](https://www.w3.org/TR/html401/interact/forms.html)
* [Požádat o Token pro ověření](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)
* <xref:mvc/models/model-binding>
* <xref:mvc/models/validation>
* [IAttributeAdapter rozhraní](/dotnet/api/Microsoft.AspNetCore.Mvc.DataAnnotations.IAttributeAdapter)
* [Fragmenty kódu pro tento dokument](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/working-with-forms/sample/final)

---
title: Autor pomocných rutin značek v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak vytvářet pomocných rutin značek v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: mvc/views/tag-helpers/authoring
ms.openlocfilehash: 43bd4eccfc06d27ade5de0e3387247a753609336
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78662370"
---
# <a name="author-tag-helpers-in-aspnet-core"></a>Autor pomocných rutin značek v ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/authoring/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="get-started-with-tag-helpers"></a>Začínáme s pomocných rutin značek

Tento kurz obsahuje úvod do programování pomocných rutin značek. [Úvod do pomocníků značek](intro.md) popisuje výhody, které poskytují pomocníky pro tag.

Pomocný objekt tag je jakákoliv třída, která implementuje rozhraní `ITagHelper`. Při vytváření pomocníka značek se však obecně odvodit z `TagHelper`, takže k tomu získáte přístup k metodě `Process`.

1. Vytvořte nový projekt ASP.NET Core s názvem **AuthoringTagHelpers**. Nemusíte ověřování pro tento projekt.

1. Vytvořte složku pro uložení pomocníků značek s názvem *TagHelpers*. Složka *TagHelpers* *není povinná* , ale jedná se o rozumnou konvenci. Nyní můžeme začít zápis pomocné rutiny některé jednoduché značky.

## <a name="a-minimal-tag-helper"></a>Minimální pomocné rutiny značky

V této části napíšete pomocné rutiny značky, která aktualizuje značku e-mailu. Příklad:

```html
<email>Support</email>
```

Server bude používat naše pomocné rutiny značky e-mailu převést do následujících značek:

```html
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

To znamená značku ukotvení díky tomu se tento odkaz na e-mailu. Můžete to provést, pokud píšete modul blogu a potřebovat k odeslání e-mailu pro marketing, podpory a další kontakty, všechny ke stejné doméně.

1. Do složky *TagHelpers* přidejte následující třídu `EmailTagHelper`.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1EmailTagHelperCopy.cs)]

   * Pomocník značek používá konvenci pojmenování, která cílí na prvky názvu kořenové třídy (mínus část *taghelperu* názvu třídy). V tomto příkladu je kořenový název **EmailTagHelper** *e-mailem*, takže se bude cílit na `<email>` tag. Tyto zásady vytváření názvů by mělo fungovat pro většinu pomocných rutin značek, později ukážeme postup jeho přepsání.

   * Třída `EmailTagHelper` je odvozena z `TagHelper`. Třída `TagHelper` poskytuje metody a vlastnosti pro psaní pomocníků značek.

   * Přepsaná metoda `Process` řídí, co má pomocník značek při spuštění. Třída `TagHelper` také poskytuje asynchronní verzi (`ProcessAsync`) se stejnými parametry.

   * Kontextový parametr pro `Process` (a `ProcessAsync`) obsahuje informace spojené s prováděním aktuální značky HTML.

   * Výstupní parametr pro `Process` (a `ProcessAsync`) obsahuje stavový zástupce prvku HTML původního zdroje použitého ke generování značek a obsahu jazyka HTML.

   * Název naší třídy má příponu **taghelperu**, která *není požadována, ale je* považována za osvědčenou konvenci. Můžete deklarovat třídu jako:

   ```csharp
   public class Email : TagHelper
   ```

1. Chcete-li zpřístupnit třídu `EmailTagHelper` všem našim zobrazením Razor, přidejte do souboru *views/_ViewImports. cshtml* direktivu `addTagHelper`.

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopyEmail.cshtml?highlight=2,3)]

   Výše uvedený kód pomocí syntaxe zástupných znaků určuje, že všechny pomocných rutin značek v našich sestavení bude k dispozici. První řetězec po `@addTagHelper` určuje pomocný objekt tag, který se má načíst (použijte * pro všechny pomocné rutiny značky) a druhý řetězec "AuthoringTagHelpers" Určuje sestavení, ve kterém je pomocník značek. Všimněte si také, že druhý řádek přináší do ASP.NET Core pomocníka značek MVC pomocí syntaxe zástupných znaků (tyto pomocníky jsou popsány v tématu [Úvod k označení pomocníků se značkami](intro.md)). Je to direktiva `@addTagHelper`, která zpřístupňuje pomocníka značky pro zobrazení Razor. Alternativně je můžete zadat plně kvalifikovaný název (FQN) pomocné rutiny značky jak je znázorněno níže:

```csharp
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

<!--
the following snippet uses TagHelpers3 and should use TagHelpers (not the 3)
    [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImports.cshtml?highlight=3&range=1-3)]
-->

Chcete-li přidat pomocníka značek pro zobrazení pomocí FQN, nejprve přidejte FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`) a potom **název sestavení** (*AuthoringTagHelpers*, ne nutně `namespace`). Většina vývojářů budou chtít použít syntaxe zástupných znaků. [Úvod k](intro.md) pomocníkům značek odkazuje na podrobné informace o přidání, odebrání, hierarchii a zástupné syntaxi pomocníka značek.

1. Aktualizujte kód v souboru *views/Home/Contact. cshtml* pomocí těchto změn:

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. Spusťte aplikaci a použijte svůj oblíbený prohlížeč k zobrazení zdroje HTML, abyste mohli ověřit, že jsou značky e-mailu nahrazeny označením kotvy (například `<a>Support</a>`). *Podpora* a *Marketing* se vykreslují jako odkazy, ale nemají atribut `href`, aby byly funkční. To Změníme v další části.

## <a name="setattribute-and-setcontent"></a>SetAttribute a SetContent

V této části aktualizujeme `EmailTagHelper` tak, aby se pro e-mail vytvořila platná značka ukotvení. Aktualizujeme ji, aby provedla informace ze zobrazení Razor (ve formě atributu `mail-to`) a použila se při vytváření kotvy.

Aktualizujte třídu `EmailTagHelper` následujícím způsobem:

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?range=6-22)]

* Názvy tříd a vlastností jazyka Pascal-použita pro pomocníky značek jsou přeloženy do jejich [kebabho případu](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101). Proto pro použití atributu `MailTo` použijete `<email mail-to="value"/>` ekvivalent.

* Poslední řádek nastaví dokončené obsah pro naše pomocné rutiny značky minimálně funkční.

* Zvýrazněný řádek ukazuje syntaxi pro přidávání atributů:

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?highlight=6&range=14-21)]

Tento postup funguje u atributu "href" tak dlouho, dokud aktuálně neexistuje v kolekci atributů. Můžete také použít metodu `output.Attributes.Add` pro přidání atributu pomocníka značky na konec kolekce atributů značek.

1. Aktualizujte kód v souboru *views/Home/Contact. cshtml* pomocí těchto změn:

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/ContactCopy.cshtml?highlight=15,16)]

1. Spusťte aplikaci a ověřte, že se vygeneruje správné odkazy.

<a name="self-closing"></a>

   > [!NOTE]
   > Pokud byste chtěli napsat značku e-mailu na samoobslužné uzavírání (`<email mail-to="Rick" />`), konečný výstup by taky byl samouzavírací. Chcete-li povolit možnost psaní značky pouze pomocí počáteční značky (`<email mail-to="Rick">`), je nutné označit třídu následujícím způsobem:
   >
   > [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailVoid.cs?highlight=1&range=6-10)]

   Pomocí samoobslužného pomocníka značek e-mailu by byl výstup `<a href="mailto:Rick@contoso.com" />`. Samouzavírací značky ukotvení nejsou platné HTML, proto není vhodné vytvořit, ale můžete chtít vytvořit pomocné rutiny značky, který je samouzavírací. Pomocí značek můžete nastavit typ vlastnosti `TagMode` po čtení značky.

### <a name="processasync"></a>ProcessAsync

V této části budeme psát pomocné rutiny asynchronní e-mailu.

1. Třídu `EmailTagHelper` nahraďte následujícím kódem:

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelper.cs?range=6-17)]

   **Poznámky:**

   * Tato verze používá asynchronní metodu `ProcessAsync`. Asynchronní `GetChildContentAsync` vrátí `Task` obsahující `TagHelperContent`.

   * K získání obsahu elementu HTML použijte parametr `output`.

1. Proveďte následující změny v souboru *views/Home/Contact. cshtml* , aby pomocník značek mohl získat cílový e-mail.

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. Spusťte aplikaci a ověřte, že generuje odkazy platnou e-mailovou.

### <a name="removeall-precontentsethtmlcontent-and-postcontentsethtmlcontent"></a>RemoveAll, PreContent.SetHtmlContent a PostContent.SetHtmlContent

1. Do složky *TagHelpers* přidejte následující třídu `BoldTagHelper`.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/BoldTagHelper.cs)]

   * Atribut `[HtmlTargetElement]` předává parametr atributu, který určuje, že všechny prvky jazyka HTML obsahující atribut HTML s názvem "bold" budou odpovídat a spustí se metoda `Process` override ve třídě. V naší ukázce `Process` metoda odstraní atribut "bold" a obklopí obsahující značku pomocí `<strong></strong>`.

   * Vzhledem k tomu, že nechcete nahradit existující obsah značky, je nutné napsat značku otevírací `<strong>` s metodou `PreContent.SetHtmlContent` a uzavírací `</strong>`ovou značkou metodou `PostContent.SetHtmlContent`.

1. Upravte zobrazení *About. cshtml* tak, aby obsahovalo hodnotu atributu `bold`. Dokončený kód je uveden níže.

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutBoldOnly.cshtml?highlight=7)]

1. Spusťte aplikaci. Svůj oblíbený prohlížeč můžete použít ke kontrole zdroji a ověřte kód.

   Atribut `[HtmlTargetElement]` výše překračuje pouze označení HTML, které poskytuje název atributu "bold". Element `<bold>` nebyl změněn pomocníkem značek.

1. Odkomentujte řádek atributu `[HtmlTargetElement]` a ve výchozím nastavení se zacílí na `<bold>` značky, tj. HTML značky `<bold>`formuláře. Nezapomeňte, že výchozí konvence pojmenování bude odpovídat názvu třídy **tučně**taghelperu, aby `<bold>` značky.

1. Spusťte aplikaci a ověřte, zda je značka `<bold>` zpracována pomocníkem značek.

Upravení třídu s více atributy `[HtmlTargetElement]` má za následek logický nebo cíl. Například pomocí níže uvedeného kódu, odpovídat tučné značku nebo atribut tučného písma.

[!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zBoldTagHelperCopy.cs?highlight=1,2&range=5-15)]

Při přidávání více atributů na stejný příkaz, modul runtime považuje za ně logickým operátorem a. Například v následujícím kódu musí být prvek HTML pojmenovaný "bold" s atributem s názvem "bold" (`<bold bold />`), který se má shodovat.

```csharp
[HtmlTargetElement("bold", Attributes = "bold")]
   ```

Můžete také použít `[HtmlTargetElement]` ke změně názvu cílového prvku. Například pokud jste chtěli `BoldTagHelper` cílit `<MyBold>` značky, použijte následující atribut:

```csharp
[HtmlTargetElement("MyBold")]
```

## <a name="pass-a-model-to-a-tag-helper"></a>Předání modelu do pomocné rutiny značky

1. Přidejte složku *modelů* .

1. Do složky *modely* přidejte následující třídu `WebsiteContext`:

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Models/WebsiteContext.cs)]

1. Do složky *TagHelpers* přidejte následující třídu `WebsiteInformationTagHelper`.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/WebsiteInformationTagHelper.cs)]

   * Jak už bylo uvedeno výše, pomocník značek překládá názvy tříd Pascal C# -použita a vlastnosti pro pomocníky značek na [kebab případ](https://wiki.c2.com/?KebabCase). Proto pokud chcete použít `WebsiteInformationTagHelper` v Razor, budete psát `<website-information />`.

   * Nejste explicitně identifikovali cílový element s atributem `[HtmlTargetElement]`, takže výchozí `website-information` bude cílen. Pokud jste provedli následující atribut (Poznámka: není případ kebab ale odpovídá názvu třídy):

   ```csharp
   [HtmlTargetElement("WebsiteInformation")]
   ```

   Značka Case kebab `<website-information />` nesouhlasí. Pokud chcete použít atribut `[HtmlTargetElement]`, použijte kebab případ, jak je znázorněno níže:

   ```csharp
   [HtmlTargetElement("Website-Information")]
   ```

   * Prvky, které jsou samouzavírací nemají žádný obsah. V tomto příkladu kód Razor použije samouzavírací značku, ale pomocná rutina značky vytvoří prvek [oddílu](https://www.w3.org/TR/html5/sections.html#the-section-element) (který není samo-závěrkou a píšete obsah uvnitř `section` elementu). Proto je nutné nastavit `TagMode` pro `StartTagAndEndTag` k zápisu výstupu. Alternativně můžete zadat komentář k nastavení čáry `TagMode` a zapsat značku s uzavírací značkou. (Příklad kódu je zadáno později v tomto kurzu).

   * `$` (znak dolaru) na následujícím řádku používá [interpolované řetězce](/dotnet/csharp/language-reference/keywords/interpolated-strings):

   ```cshtml
   $@"<ul><li><strong>Version:</strong> {Info.Version}</li>
   ```

1. Do zobrazení *About. cshtml* přidejte následující kód. Zvýrazněná značka se zobrazí informace webu.

   [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?highlight=1,4-8, 18-999)]

   > [!NOTE]
   > V kódu Razor, vidíte níže:
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?range=18-18)]
   >
   > Razor ví, že atribut `info` je třída, nikoli řetězec a vy chcete napsat C# kód. Jakýkoli pomocný atribut neřetězcové značky by měl být napsán bez `@`ho znaku.

1. Spusťte aplikaci a přejděte do zobrazení o zobrazíte informace o webovém serveru.

   > [!NOTE]
   > Můžete použít následující kód s uzavírací značkou a odebrat čáru s `TagMode.StartTagAndEndTag` v Pomocníkovi značek:
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutNotSelfClosing.cshtml?range=20-21)]

## <a name="condition-tag-helper"></a>Podmínka vyhodnocena jako pomocné rutiny značky

Pomocná rutina značky podmínku vykreslí výstup při předání hodnotu true.

1. Do složky *TagHelpers* přidejte následující třídu `ConditionTagHelper`.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/ConditionTagHelper.cs)]

1. Obsah souboru *views/Home/index. cshtml* nahraďte následujícím kódem:

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Index.cshtml)]

1. Metodu `Index` v kontroleru `Home` nahraďte následujícím kódem:

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Controllers/HomeController.cs?range=9-18)]

1. Spusťte aplikaci a přejděte na domovskou stránku. Značky v podmíněném `div` nebudou vykresleny. Připojí řetězec dotazu `?approved=true` k adrese URL (například `http://localhost:1235/Home/Index?approved=true`). `approved` je nastavena na hodnotu true a zobrazí se podmíněný kód.

> [!NOTE]
> Použijte operátor [nameof](/dotnet/csharp/language-reference/keywords/nameof) k určení atributu pro cílení namísto zadání řetězce jako u pomocníka s tučnými značkami:
>
> [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zConditionTagHelperCopy.cs?highlight=1,2,5&range=5-18)]
>
> Operátor [nameof](/dotnet/csharp/language-reference/keywords/nameof) bude chránit kód by měl být někdy refaktorované (název můžeme změnit na `RedCondition`).

### <a name="avoid-tag-helper-conflicts"></a>Nedocházelo ke konfliktům pomocné rutiny značky

V této části napíšete pár automatické propojování pomocných rutin značek. První nahradí značek obsahující adresy URL začínající HTTP do HTML anchor značky obsahují stejnou adresu URL (a tedy získávání odkaz na adresu URL). Druhá bude totéž proveďte pro adresu URL začínající WWW.

Protože tyto dvě pomocné rutiny jsou úzce souvisí a je může v budoucnu Refaktorovat, budeme je ve stejném souboru.

1. Do složky *TagHelpers* přidejte následující třídu `AutoLinkerHttpTagHelper`.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=7-19)]

   >[!NOTE]
   >Třída `AutoLinkerHttpTagHelper` cílí `p` prvky a pomocí [regulárního výrazu](/dotnet/standard/base-types/regular-expression-language-quick-reference) vytvoří kotvu.

1. Na konec souboru *views/Home/Contact. cshtml* přidejte následující kód:

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=19)]

1. Spusťte aplikaci a ověřte, že pomocné rutiny značky správně vykresluje ukotvení.

1. Aktualizujte třídu `AutoLinker` tak, aby zahrnovala `AutoLinkerWwwTagHelper`, který převede text na značku kotvy, který obsahuje také původní text na webové stránce. Aktualizovaný kód je zvýrazněn níže:

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?highlight=15-34&range=7-34)]

1. Spusťte aplikaci. Všimněte si, že www text je vykreslen jako odkaz, ale není HTTP text. Když vložíte přerušení v obou třídách, uvidíte, že třída pomocné rutiny značky HTTP spustí první. Problém je, že výstup pomocné rutiny značky do mezipaměti, a při spuštění pomocné rutiny značky WWW přepíše uložené v mezipaměti výstup z pomocné rutiny značky protokolu HTTP. V pozdější části kurzu uvidíme, jak řídit pořadí, ve kterém pomocných rutin značek spustit v. Opravíme kód následujícím kódem:

   [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10,21,22,26&range=8-37)]

   > [!NOTE]
   > V první verzi pomocných rutin značek automatické propojování obdržela obsah cíle s následujícím kódem:
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=12)]
   >
   > To znamená, že zavoláte `GetChildContentAsync` pomocí `TagHelperOutput` předaného do metody `ProcessAsync`. Jak bylo zmíněno dříve, protože je v mezipaměti výstupu, poslední značku pomocné rutiny pro spuštění služby wins. Jste opravili tohoto problému s následujícím kódem:
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?range=34-35)]
   >
   > Výše uvedený kód zkontroluje, zda byl změněn obsah, a pokud ano, získá obsah z výstupní vyrovnávací paměť.

1. Spusťte aplikaci a ověřte, že dva odkazy fungovat podle očekávání. Může se zdát, že je naše pomocné rutiny značky linkeru automaticky správnosti a úplnosti, to je drobný problém. Pokud se spustí první pomocné rutiny značky WWW, webové odkazy nebudou správná. Aktualizujte kód přidáním `Order` přetížení pro řízení pořadí, ve kterém se značka spouští. Vlastnost `Order` určuje pořadí spouštění relativně k jiným pomocníkům značek cílících na stejný prvek. Výchozí hodnota pořadí je nula a instancí s nižšími hodnotami jsou nejprve spuštěn.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?highlight=5,6,7,8&range=8-15)]

   Předchozí kód zaručuje, že pomocné rutiny značky HTTP spuštěn dříve, než pomocné rutiny značky WWW. Změňte `Order` na `MaxValue` a ověřte, zda je kód vygenerovaný pro značku WWW nesprávný.

## <a name="inspect-and-retrieve-child-content"></a>Kontrolovat a načítat obsah podřízených

Pomocné rutiny značky poskytují několik vlastností k načtení obsahu.

* Výsledek `GetChildContentAsync` lze připojit k `output.Content`.
* Výsledek `GetChildContentAsync` lze zkontrolovat pomocí `GetContent`.
* Pokud upravíte `output.Content`, text Taghelperu se nespustí ani nebude vykreslený, pokud nebudete volat `GetChildContentAsync` jako v našem příkladu automatického linkeru:

[!code-csharp[](../../views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10&range=8-21)]

* Více volání `GetChildContentAsync` vrací stejnou hodnotu a znovu nespustí `TagHelper` tělo, pokud nepředáte parametr false, který označuje, že nechcete použít výsledek uložený v mezipaměti.

## <a name="load-minified-partial-view-taghelper"></a>Načíst minifikovaného částečného zobrazení Taghelperu

V produkčních prostředích je možné výkon zlepšit načtením částečně minifikovaného zobrazení. Využití částečného zobrazení minifikovaného v produkčním prostředí:

* Vytvořte nebo nastavte proces před sestavením, který minifies částečná zobrazení.
* Pomocí následujícího kódu načtěte minifikovaného částečná zobrazení v nevývojovém prostředí.

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/MinifiedVersionTagHelper.cs?name=snippet)]

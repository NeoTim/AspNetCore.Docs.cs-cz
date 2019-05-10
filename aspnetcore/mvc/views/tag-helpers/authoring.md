---
title: Autor pomocných rutin značek v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak vytvářet pomocných rutin značek v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/29/2019
uid: mvc/views/tag-helpers/authoring
ms.openlocfilehash: 37e39ac93e7b67184dfc238d58e12c2be8d84f91
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087357"
---
# <a name="author-tag-helpers-in-aspnet-core"></a>Autor pomocných rutin značek v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/authoring/sample) ([stažení](xref:index#how-to-download-a-sample))

## <a name="get-started-with-tag-helpers"></a>Začínáme s pomocných rutin značek

Tento kurz obsahuje úvod do programování pomocných rutin značek. [Úvod do pomocné rutiny značek](intro.md) popisuje výhody, které poskytují pomocných rutin značek.

Pomocná rutina značky se jakékoli třídy, která implementuje `ITagHelper` rozhraní. Ale při vytváření pomocné rutiny značky obecně odvozujete od `TagHelper`, to tedy dává vám přístup k `Process` metoda.

1. Vytvořit nový projekt ASP.NET Core s názvem **AuthoringTagHelpers**. Nemusíte ověřování pro tento projekt.

1. Vytvořte složku pro uložení pomocné rutiny značek volá *TagHelpers*. *TagHelpers* složka je *není* povinné, ale je rozumné konvence. Nyní můžeme začít zápis pomocné rutiny některé jednoduché značky.

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

1. Přidejte následující `EmailTagHelper` třídu *TagHelpers* složky.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1EmailTagHelperCopy.cs)]

   * Použít zásady vytváření názvů, který cílí na prvky názvu kořenové třídy pomocných rutin značek (minus *Taghelperu* část názvu třídy). V tomto příkladu názvem kořenového **EmailTagHelper** je *e-mailu*, takže `<email>` budou cílem značky. Tyto zásady vytváření názvů by mělo fungovat pro většinu pomocných rutin značek, později ukážeme postup jeho přepsání.

   * `EmailTagHelper` Třída odvozena z `TagHelper`. `TagHelper` Třída poskytuje metody a vlastnosti pro zápis pomocné rutiny značek.

   * Přepsané `Process` metodu ovládá, co dělá pomocné rutiny značky při spuštění. `TagHelper` Třída rovněž poskytuje asynchronní verze (`ProcessAsync`) se stejnými parametry.

   * Kontextový parametr pro `Process` (a `ProcessAsync`) obsahuje informace o přidružené k provádění aktuální značku HTML.

   * Výstupní parametr `Process` (a `ProcessAsync`) obsahuje stavové prvek HTML reprezentativní původního zdroje sloužící ke generování značky HTML a obsah služby.

   * Naše název třídy má příponu **Taghelperu.**, což je *není* povinné, ale se považuje za osvědčených postupů konvence. Můžete deklarovat třídu jako:

   ```csharp
   public class Email : TagHelper
   ```

1. Chcete-li `EmailTagHelper` , do třídy k dispozici pro všechny naše zobrazeními Razor `addTagHelper` direktivu *Views/_ViewImports.cshtml* souboru:

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopyEmail.cshtml?highlight=2,3)]

   Výše uvedený kód pomocí syntaxe zástupných znaků určuje, že všechny pomocných rutin značek v našich sestavení bude k dispozici. První řetězec, který po `@addTagHelper` určuje pomocné rutiny značky k načtení (použijte "*" pro všechny pomocných rutin značek), a druhý řetězec "AuthoringTagHelpers" Určuje sestavení, je pomocná rutina značek v. Všimněte si také, že druhý řádek přináší pomocných rutin značek ASP.NET Core MVC pomocí syntaxe zástupných znaků (tyto pomocné rutiny jsou popsány v [Úvod do pomocné rutiny značek](intro.md).) Je `@addTagHelper` direktiva, která zpřístupní pomocné rutiny značky do zobrazení Razor. Alternativně je můžete zadat plně kvalifikovaný název (FQN) pomocné rutiny značky jak je znázorněno níže:

```csharp
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

<!--
the following snippet uses TagHelpers3 and should use TagHelpers (not the 3)
    [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImports.cshtml?highlight=3&range=1-3)]
-->

Pomocné rutiny značky do zobrazení pomocí FQN, nejprve přidáte FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`) a pak **název sestavení** (*AuthoringTagHelpers*, ne nutně co `namespace`). Většina vývojářů budou chtít použít syntaxe zástupných znaků. [Úvod do pomocné rutiny značek](intro.md) obsahuje podrobnosti o syntaxi pro přidání, odebrání, hierarchie a zástupný znak pomocné rutiny značky.

1. Aktualizace značky *Views/Home/Contact.cshtml* soubor se tyto změny:

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. Spusťte aplikaci a použijte svůj oblíbený prohlížeč Chcete-li zobrazit zdrojový kód HTML to vám umožní ověřit e-mailu značky jsou nahrazeny ukotvení značky (například `<a>Support</a>`). *Podpora* a *marketingové* jsou vykresleny jako odkazy, ale nebudou mít `href` atribut, aby se daly funkční. To Změníme v další části.

## <a name="setattribute-and-setcontent"></a>SetAttribute a SetContent

V této části, aktualizujeme `EmailTagHelper` tak, aby se vytvoří platné značky pro e-mailu. K provedení informace ze zobrazení Razor aktualizujeme (ve formě `mail-to` atribut), který budete používat při generování ukotvení.

Aktualizace `EmailTagHelper` třídy následujícím kódem:

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?range=6-22)]

* Jazyka Pascal – třídy a vlastnosti názvy pro pomocné rutiny značek jsou přeloženy do jejich [kebab případ](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101). Proto použít `MailTo` atributu, budete používat `<email mail-to="value"/>` ekvivalentní.

* Poslední řádek nastaví dokončené obsah pro naše pomocné rutiny značky minimálně funkční.

* Zvýrazněný řádek ukazuje syntaxi pro přidávání atributů:

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?highlight=6&range=14-21)]

Tento postup funguje u atributu "href" tak dlouho, dokud aktuálně neexistuje v kolekci atributů. Můžete také použít `output.Attributes.Add` způsob, jak přidat na konec kolekce atributy značky pomocný atribut příznaku.

1. Aktualizace značky *Views/Home/Contact.cshtml* soubor se tyto změny:

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/ContactCopy.cshtml?highlight=15,16)]

1. Spusťte aplikaci a ověřte, že se vygeneruje správné odkazy.

<a name="self-closing"></a>

   > [!NOTE]
   > Pokud byste chtěli zapsat e-mailu značky samouzavírací (`<email mail-to="Rick" />`), by také být samouzavírací závěrečný výstup. Povolit možnost zapisovat značka s počáteční značce (`<email mail-to="Rick">`) musí uspořádání třídy následujícím kódem:
   >
   > [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailVoid.cs?highlight=1&range=6-10)]

   S samouzavírací e-mailu pomocné rutiny značky, výstup by měl `<a href="mailto:Rick@contoso.com" />`. Samouzavírací značky ukotvení nejsou platné HTML, proto není vhodné vytvořit, ale můžete chtít vytvořit pomocné rutiny značky, který je samouzavírací. Nastavení typu pomocných rutin značek `TagMode` vlastnost po přečtení značku.

### <a name="processasync"></a>ProcessAsync

V této části budeme psát pomocné rutiny asynchronní e-mailu.

1. Nahraďte `EmailTagHelper` třídy následujícím kódem:

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelper.cs?range=6-17)]

   **Poznámky:**

   * Tato verze používá asynchronní `ProcessAsync` metody. Asynchronní `GetChildContentAsync` vrátí `Task` obsahující `TagHelperContent`.

   * Použití `output` parametr načíst obsah elementu HTML.

1. Proveďte následující změnu *Views/Home/Contact.cshtml* souboru pomocné rutiny značky můžete získat cílové e-mailu.

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. Spusťte aplikaci a ověřte, že generuje odkazy platnou e-mailovou.

### <a name="removeall-precontentsethtmlcontent-and-postcontentsethtmlcontent"></a>RemoveAll, PreContent.SetHtmlContent a PostContent.SetHtmlContent

1. Přidejte následující `BoldTagHelper` třídu *TagHelpers* složky.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/BoldTagHelper.cs)]

   * `[HtmlTargetElement]` Atribut předá parametr atributu, který určuje, že libovolný prvek HTML, který obsahuje atribut HTML s názvem "bold" bude odpovídat, a `Process` spustí metodu přepsání metody ve třídě. V naší ukázce `Process` metoda odebere atribut "bold" a obklopuje obsahující kód s `<strong></strong>`.

   * Vzhledem k tomu, že nechcete nahradit existující značky obsahu, je nutné napsat otevírání `<strong>` označit `PreContent.SetHtmlContent` metoda a uzavírací `</strong>` označit `PostContent.SetHtmlContent` – metoda.

1. Upravit *About.cshtml* zobrazení tak, aby obsahovala `bold` hodnotu atributu. Dokončený kód je uveden níže.

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutBoldOnly.cshtml?highlight=7)]

1. Spusťte aplikaci. Svůj oblíbený prohlížeč můžete použít ke kontrole zdroji a ověřte kód.

   `[HtmlTargetElement]` Výše uvedený atribut se zaměřuje pouze kód HTML, který obsahuje název atributu "tučného písma". `<bold>` Prvek nebyl změněn pomocí pomocné rutiny značky.

1. Okomentujte `[HtmlTargetElement]` tak, aby se výchozí atribut řádku a `<bold>` značky, to znamená, že kód HTML ve formátu `<bold>`. Nezapomeňte, že výchozí zásady vytváření názvů bude shodovat s názvem třídy **tučné**Taghelperu. k `<bold>` značky.

1. Spusťte aplikaci a ověřte, zda `<bold>` značka je zpracován pomocné rutiny značky.

Upravení třídy s několika `[HtmlTargetElement]` atributy výsledky v logického operátoru OR cílů. Například pomocí níže uvedeného kódu, odpovídat tučné značku nebo atribut tučného písma.

[!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zBoldTagHelperCopy.cs?highlight=1,2&range=5-15)]

Při přidávání více atributů na stejný příkaz, modul runtime považuje za ně logickým operátorem a. Například v následujícím kódu HTML elementu musí mít název "bold" s atributem s názvem "bold" (`<bold bold />`) tak, aby odpovídaly.

```csharp
[HtmlTargetElement("bold", Attributes = "bold")]
   ```

Můžete také použít `[HtmlTargetElement]` můžete změnit název cílový element. Například pokud jste chtěli `BoldTagHelper` k cíli `<MyBold>` značky, můžete využít následující atribut:

```csharp
[HtmlTargetElement("MyBold")]
```

## <a name="pass-a-model-to-a-tag-helper"></a>Předání modelu do pomocné rutiny značky

1. Přidat *modely* složky.

1. Přidejte následující `WebsiteContext` třídu *modely* složky:

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Models/WebsiteContext.cs)]

1. Přidejte následující `WebsiteInformationTagHelper` třídu *TagHelpers* složky.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/WebsiteInformationTagHelper.cs)]

   * Jak už bylo zmíněno dříve, přeloží pomocných rutin značek-jazyka Pascal C# třídy názvy a vlastnosti pro pomocné rutiny značky do [kebab případ](http://wiki.c2.com/?KebabCase). Proto použít `WebsiteInformationTagHelper` v Razor, napíšete `<website-information />`.

   * Nejsou explicitně určení cílového elementu s `[HtmlTargetElement]` atribut, tak výchozí `website-information` budou cílem. Pokud jste provedli následující atribut (Poznámka: není případ kebab ale odpovídá názvu třídy):

   ```csharp
   [HtmlTargetElement("WebsiteInformation")]
   ```

   Značka případu kebab `<website-information />` shodě. Pokud chcete použít `[HtmlTargetElement]` atribut, můžete využít kebab případu, jak je znázorněno níže:

   ```csharp
   [HtmlTargetElement("Website-Information")]
   ```

   * Prvky, které jsou samouzavírací nemají žádný obsah. V tomto příkladu kód Razor používat samouzavírací značky, ale pomocné rutiny značky vytvářet [části](http://www.w3.org/TR/html5/sections.html#the-section-element) – element (což není samouzavírací a vy píšete obsah uvnitř `section` element). Proto je potřeba nastavit `TagMode` k `StartTagAndEndTag` k zápisu výstupu. Alternativně můžete Zakomentovat řádek nastavení `TagMode` a psát kód s koncovou značku. (Příklad kódu je zadáno později v tomto kurzu).

   * `$` (Znak dolaru) na následujícím řádku používá [interpolovaný řetězec](/dotnet/csharp/language-reference/keywords/interpolated-strings):

   ```cshtml
   $@"<ul><li><strong>Version:</strong> {Info.Version}</li>
   ```

1. Přidejte následující kód k *About.cshtml* zobrazení. Zvýrazněná značka se zobrazí informace webu.

   [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?highlight=1,4-8, 18-999)]

   > [!NOTE]
   > V kódu Razor, vidíte níže:
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?range=18-18)]
   >
   > Razor ví, `info` atribut je třída, není řetězec, a že chcete napsat kód jazyka C#. Žádné pomocný atribut příznaku jiné než řetězec by měl napsat bez `@` znak.

1. Spusťte aplikaci a přejděte do zobrazení o zobrazíte informace o webovém serveru.

   > [!NOTE]
   > Můžete použít následující kód s koncovou značku a odebrání řádku s `TagMode.StartTagAndEndTag` v pomocné rutiny značky:
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutNotSelfClosing.cshtml?range=20-21)]

## <a name="condition-tag-helper"></a>Podmínka vyhodnocena jako pomocné rutiny značky

Pomocná rutina značky podmínku vykreslí výstup při předání hodnotu true.

1. Přidejte následující `ConditionTagHelper` třídu *TagHelpers* složky.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/ConditionTagHelper.cs)]

1. Nahraďte obsah *Views/Home/Index.cshtml* souboru následujícím kódem:

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Index.cshtml)]

1. Nahradit `Index` metoda ve `Home` řadiče s následujícím kódem:

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Controllers/HomeController.cs?range=9-18)]

1. Spusťte aplikaci a přejděte na domovskou stránku. Kód v podmíněnou `div` se nevykreslí. Připojte řetězec dotazu `?approved=true` na adresu URL (například `http://localhost:1235/Home/Index?approved=true`). `approved` je nastavena na hodnotu true a podmíněným zobrazí značky.

> [!NOTE]
> Použití [nameof](/dotnet/csharp/language-reference/keywords/nameof) operátor zadat atribut na cílové místo zadání řetězce, jako jste to udělali s pomocné rutiny tučné značky:
>
> [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zConditionTagHelperCopy.cs?highlight=1,2,5&range=5-18)]
>
> [Nameof](/dotnet/csharp/language-reference/keywords/nameof) operátor bude chránit kód byste je někdy možné Refaktorovat (chceme změnit název, který má `RedCondition`).

### <a name="avoid-tag-helper-conflicts"></a>Nedocházelo ke konfliktům pomocné rutiny značky

V této části napíšete pár automatické propojování pomocných rutin značek. První nahradí značek obsahující adresy URL začínající HTTP do HTML anchor značky obsahují stejnou adresu URL (a tedy získávání odkaz na adresu URL). Druhá bude totéž proveďte pro adresu URL začínající WWW.

Protože tyto dvě pomocné rutiny jsou úzce souvisí a je může v budoucnu Refaktorovat, budeme je ve stejném souboru.

1. Přidejte následující `AutoLinkerHttpTagHelper` třídu *TagHelpers* složky.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=7-19)]

   >[!NOTE]
   >`AutoLinkerHttpTagHelper` Třídy cíle `p` prvků a používá [regulární výraz](/dotnet/standard/base-types/regular-expression-language-quick-reference) vytvořit ukotvení.

1. Přidejte následující kód do konce *Views/Home/Contact.cshtml* souboru:

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=19)]

1. Spusťte aplikaci a ověřte, že pomocné rutiny značky správně vykresluje ukotvení.

1. Aktualizace `AutoLinker` třídy, aby obsahoval `AutoLinkerWwwTagHelper` www text který se převede na značku ukotvení, obsahující původní text www. Aktualizovaný kód je zvýrazněn níže:

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?highlight=15-34&range=7-34)]

1. Spusťte aplikaci. Všimněte si, že www text je vykreslen jako odkaz, ale není HTTP text. Když vložíte přerušení v obou třídách, uvidíte, že třída pomocné rutiny značky HTTP spustí první. Problém je, že výstup pomocné rutiny značky do mezipaměti, a při spuštění pomocné rutiny značky WWW přepíše uložené v mezipaměti výstup z pomocné rutiny značky protokolu HTTP. V pozdější části kurzu uvidíme, jak řídit pořadí, ve kterém pomocných rutin značek spustit v. Opravíme kód následujícím kódem:

   [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10,21,22,26&range=8-37)]

   > [!NOTE]
   > V první verzi pomocných rutin značek automatické propojování obdržela obsah cíle s následujícím kódem:
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=12)]
   >
   > To znamená, že zavoláte `GetChildContentAsync` pomocí `TagHelperOutput` předán `ProcessAsync` metoda. Jak bylo zmíněno dříve, protože je v mezipaměti výstupu, poslední značku pomocné rutiny pro spuštění služby wins. Jste opravili tohoto problému s následujícím kódem:
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?range=34-35)]
   >
   > Výše uvedený kód zkontroluje, zda byl změněn obsah, a pokud ano, získá obsah z výstupní vyrovnávací paměť.

1. Spusťte aplikaci a ověřte, že dva odkazy fungovat podle očekávání. Může se zdát, že je naše pomocné rutiny značky linkeru automaticky správnosti a úplnosti, to je drobný problém. Pokud se spustí první pomocné rutiny značky WWW, webové odkazy nebudou správná. Aktualizovat kód tak, že přidáte `Order` přetížení k řízení, na kterém běží značky v pořadí. `Order` Vlastnost určuje pořadí zpracování relativně k cílení na stejný prvek jiných pomocných rutin značek. Výchozí hodnota pořadí je nula a instancí s nižšími hodnotami jsou nejprve spuštěn.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?highlight=5,6,7,8&range=8-15)]

   Předchozí kód zaručuje, že pomocné rutiny značky HTTP spuštěn dříve, než pomocné rutiny značky WWW. Změna `Order` k `MaxValue` a ověřte, zda je nesprávný kód vygeneruje pro značku WWW.

## <a name="inspect-and-retrieve-child-content"></a>Kontrolovat a načítat obsah podřízených

Pomocné rutiny značky poskytují několik vlastností k načtení obsahu.

* Výsledek `GetChildContentAsync` lze připojit k `output.Content`.
* Můžete si prohlédnout výsledek `GetChildContentAsync` s `GetContent`.
* Pokud upravíte `output.Content`, tělo Taghelperu. nebudou provedeny nebo vykreslení Pokud zavoláte `GetChildContentAsync` stejně jako v naší ukázce automaticky linkeru:

[!code-csharp[](../../views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10&range=8-21)]

* Více volání `GetChildContentAsync` vrací stejnou hodnotu a nebude znovu spouštět `TagHelper` subjektu, pokud nepředáte v false parametr určující nepoužívat výsledky uložené v mezipaměti.

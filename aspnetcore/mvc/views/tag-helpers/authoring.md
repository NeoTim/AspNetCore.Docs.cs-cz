---
title: Vytváření značek pomocníků v ASP.NET Core
author: rick-anderson
description: Naučte se vytvářet pomocníky značek v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/29/2019
uid: mvc/views/tag-helpers/authoring
ms.openlocfilehash: f0c7e114583b2ca2e681c507bef3487c863d8cd0
ms.sourcegitcommit: a166291c6708f5949c417874108332856b53b6a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2019
ms.locfileid: "72589875"
---
# <a name="author-tag-helpers-in-aspnet-core"></a>Vytváření značek pomocníků v ASP.NET Core

Od [Rick Anderson](https://twitter.com/RickAndMSFT)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/authoring/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="get-started-with-tag-helpers"></a>Začínáme s pomocníky značek

V tomto kurzu se seznámíte se sestavami programování značek. [Úvod do pomocníků značek](intro.md) popisuje výhody, které poskytují pomocníky pro tag.

Pomocný objekt tag je jakákoliv třída, která implementuje rozhraní `ITagHelper`. Při vytváření pomocníka značek se však obecně odvodit z `TagHelper`, takže k tomu získáte přístup k metodě `Process`.

1. Vytvořte nový projekt ASP.NET Core s názvem **AuthoringTagHelpers**. Pro tento projekt nebudete potřebovat ověření.

1. Vytvořte složku pro uložení pomocníků značek s názvem *TagHelpers*. Složka *TagHelpers* *není povinná* , ale jedná se o rozumnou konvenci. Teď začneme psát některé pomocníky jednoduchých značek.

## <a name="a-minimal-tag-helper"></a>Pomocný Pomocník s minimální značkou

V této části napíšete pomocníka značek, který aktualizuje značku e-mailu. Příklad:

```html
<email>Support</email>
```

Server použije pomocníka pro označení e-mailu k převedení tohoto označení na následující:

```html
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

To znamená, že značka kotvy vytvoří tento e-mailový odkaz. To můžete chtít udělat, pokud píšete modul blogu a potřebujete ho poslat e-mailem pro marketing, podporu a další kontakty, a to vše ve stejné doméně.

1. Do složky *TagHelpers* přidejte následující třídu `EmailTagHelper`.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1EmailTagHelperCopy.cs)]

   * Pomocník značek používá konvenci pojmenování, která cílí na prvky názvu kořenové třídy (mínus část *taghelperu* názvu třídy). V tomto příkladu je kořenový název **EmailTagHelper** *e-mailem*, takže se bude cílit na `<email>` tag. Tato konvence vytváření názvů by měla fungovat pro většinu pomocníků se značkami, a to později v části jak ji přepsat.

   * Třída `EmailTagHelper` je odvozena z `TagHelper`. Třída `TagHelper` poskytuje metody a vlastnosti pro psaní pomocníků značek.

   * Přepsaná metoda `Process` řídí, co má pomocník značek při spuštění. Třída `TagHelper` také poskytuje asynchronní verzi (`ProcessAsync`) se stejnými parametry.

   * Kontextový parametr pro `Process` (a `ProcessAsync`) obsahuje informace spojené s prováděním aktuální značky HTML.

   * Výstupní parametr pro `Process` (a `ProcessAsync`) obsahuje stavový zástupce prvku HTML původního zdroje použitého ke generování značek a obsahu jazyka HTML.

   * Název naší třídy má příponu **taghelperu**, která *není požadována, ale je* považována za osvědčenou konvenci. Třídu můžete deklarovat jako:

   ```csharp
   public class Email : TagHelper
   ```

1. Chcete-li zpřístupnit třídu `EmailTagHelper` všem našim zobrazením Razor, přidejte direktivu `addTagHelper` do souboru *views/_ViewImports. cshtml* :

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopyEmail.cshtml?highlight=2,3)]

   Výše uvedený kód používá syntaxi zástupných znaků k určení všech pomocníků značek v našem sestavení bude k dispozici. První řetězec po `@addTagHelper` určuje pomocný objekt tag, který se má načíst (použijte * pro všechny pomocné rutiny značky) a druhý řetězec "AuthoringTagHelpers" Určuje sestavení, ve kterém je pomocník značek. Všimněte si také, že druhý řádek přináší do ASP.NET Core pomocníka značek MVC pomocí syntaxe zástupných znaků (tyto pomocníky jsou popsány v tématu [Úvod k označení pomocníků se značkami](intro.md)). Je to direktiva `@addTagHelper`, která zpřístupňuje pomocníka značky pro zobrazení Razor. Alternativně můžete zadat plně kvalifikovaný název (FQN) pro pomocnou značku, jak je znázorněno níže:

```csharp
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

<!--
the following snippet uses TagHelpers3 and should use TagHelpers (not the 3)
    [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImports.cshtml?highlight=3&range=1-3)]
-->

Chcete-li přidat pomocníka značek pro zobrazení pomocí FQN, nejprve přidejte FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`) a potom **název sestavení** (*AuthoringTagHelpers*, ne nutně `namespace`). Většina vývojářů bude chtít použít zástupnou syntaxi. [Úvod k](intro.md) pomocníkům značek odkazuje na podrobné informace o přidání, odebrání, hierarchii a zástupné syntaxi pomocníka značek.

1. Aktualizujte kód v souboru *views/Home/Contact. cshtml* pomocí těchto změn:

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. Spusťte aplikaci a použijte svůj oblíbený prohlížeč k zobrazení zdroje HTML, abyste mohli ověřit, že jsou značky e-mailu nahrazeny označením kotvy (například `<a>Support</a>`). *Podpora* a *Marketing* se vykreslují jako odkazy, ale nemají atribut `href`, aby byly funkční. Opravujeme ho v další části.

## <a name="setattribute-and-setcontent"></a>SetAttributes a SetContent

V této části aktualizujeme `EmailTagHelper` tak, aby se pro e-mail vytvořila platná značka ukotvení. Aktualizujeme ji, aby provedla informace ze zobrazení Razor (ve formě atributu `mail-to`) a použila se při vytváření kotvy.

Aktualizujte třídu `EmailTagHelper` následujícím způsobem:

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?range=6-22)]

* Názvy tříd a vlastností jazyka Pascal-použita pro pomocníky značek jsou přeloženy do jejich [kebabho případu](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101). Proto pro použití atributu `MailTo` použijete `<email mail-to="value"/>` ekvivalent.

* Poslední řádek nastaví dokončený obsah pro naši pomocníka s minimální funkční značkou.

* Zvýrazněný řádek ukazuje syntaxi pro přidání atributů:

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?highlight=6&range=14-21)]

Tento přístup funguje pro atribut "href", pokud aktuálně neexistuje v kolekci atributů. Můžete také použít metodu `output.Attributes.Add` pro přidání atributu pomocníka značky na konec kolekce atributů značek.

1. Aktualizujte kód v souboru *views/Home/Contact. cshtml* pomocí těchto změn:

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/ContactCopy.cshtml?highlight=15,16)]

1. Spusťte aplikaci a ověřte, že generuje správné odkazy.

<a name="self-closing"></a>

   > [!NOTE]
   > Pokud byste chtěli napsat značku e-mailu na samoobslužné uzavírání (`<email mail-to="Rick" />`), konečný výstup by taky byl samouzavírací. Chcete-li povolit možnost psaní značky pouze pomocí počáteční značky (`<email mail-to="Rick">`), je nutné třídu vytřídit následujícím způsobem:
   >
   > [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailVoid.cs?highlight=1&range=6-10)]

   Pomocí samoobslužného pomocníka značek e-mailu by byl výstup `<a href="mailto:Rick@contoso.com" />`. Samouzavírací značky kotvy nejsou platné HTML, takže byste ji neměli chtít vytvořit, ale možná budete chtít vytvořit pomocníka značek, který je samo samouzavírací. Pomocí značek můžete nastavit typ vlastnosti `TagMode` po čtení značky.

### <a name="processasync"></a>ProcessAsync

V této části napíšeme pomocníka asynchronního e-mailu.

1. Třídu `EmailTagHelper` nahraďte následujícím kódem:

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelper.cs?range=6-17)]

   **Poznámky:**

   * Tato verze používá asynchronní metodu `ProcessAsync`. Asynchronní `GetChildContentAsync` vrátí `Task` obsahující `TagHelperContent`.

   * K získání obsahu elementu HTML použijte parametr `output`.

1. Proveďte následující změny v souboru *views/Home/Contact. cshtml* , aby pomocník značek mohl získat cílový e-mail.

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. Spusťte aplikaci a ověřte, že generuje platné e-mailové odkazy.

### <a name="removeall-precontentsethtmlcontent-and-postcontentsethtmlcontent"></a>RemoveAll, Content. SetHtmlContent a PostContent. SetHtmlContent

1. Do složky *TagHelpers* přidejte následující třídu `BoldTagHelper`.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/BoldTagHelper.cs)]

   * Atribut `[HtmlTargetElement]` předává parametr atributu, který určuje, že všechny prvky jazyka HTML obsahující atribut HTML s názvem "bold" budou odpovídat a spustí se metoda `Process` override ve třídě. V naší ukázce `Process` metoda odstraní atribut "bold" a obklopí obsahující značku pomocí `<strong></strong>`.

   * Vzhledem k tomu, že nechcete nahradit existující obsah značky, je nutné napsat značku otevírací `<strong>` s metodou `PreContent.SetHtmlContent` a uzavírací `</strong>`ovou značkou metodou `PostContent.SetHtmlContent`.

1. Upravte zobrazení *About. cshtml* tak, aby obsahovalo hodnotu atributu `bold`. Dokončený kód je uveden níže.

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutBoldOnly.cshtml?highlight=7)]

1. Spusťte aplikaci. Pomocí oblíbeného prohlížeče můžete zkontrolovat zdroj a ověřit označení.

   Atribut `[HtmlTargetElement]` výše překračuje pouze označení HTML, které poskytuje název atributu "bold". Element `<bold>` nebyl změněn pomocníkem značek.

1. Odkomentujte řádek atributu `[HtmlTargetElement]` a ve výchozím nastavení se zacílí na `<bold>` značky, tj. HTML značky `<bold>` formuláře. Nezapomeňte, že výchozí konvence pojmenování bude odpovídat názvu třídy **tučně**taghelperu, aby `<bold>` značky.

1. Spusťte aplikaci a ověřte, zda je značka `<bold>` zpracována pomocníkem značek.

Upravení třídu s více atributy `[HtmlTargetElement]` má za následek logický nebo cíl. Například pomocí následujícího kódu se bude shodovat značka tučného písma nebo tučného atributu.

[!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zBoldTagHelperCopy.cs?highlight=1,2&range=5-15)]

Pokud je do stejného příkazu přidáno více atributů, modul runtime je považuje za logický a. Například v následujícím kódu musí být prvek HTML pojmenovaný "bold" s atributem s názvem "bold" (`<bold bold />`), který se má shodovat.

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

   * Nejste explicitně identifikovali cílový element s atributem `[HtmlTargetElement]`, takže výchozí `website-information` bude cílen. Pokud jste použili následující atribut (Všimněte si, že není kebab Case, ale odpovídá názvu třídy):

   ```csharp
   [HtmlTargetElement("WebsiteInformation")]
   ```

   Značka Case kebab `<website-information />` nesouhlasí. Pokud chcete použít atribut `[HtmlTargetElement]`, použijte kebab případ, jak je znázorněno níže:

   ```csharp
   [HtmlTargetElement("Website-Information")]
   ```

   * Prvky, které jsou samy samouzavírací, nemají žádný obsah. V tomto příkladu kód Razor použije samouzavírací značku, ale pomocná rutina značky vytvoří prvek [oddílu](https://www.w3.org/TR/html5/sections.html#the-section-element) (který není samo-závěrkou a píšete obsah uvnitř `section` elementu). Proto je nutné nastavit `TagMode` pro `StartTagAndEndTag` k zápisu výstupu. Alternativně můžete zadat komentář k nastavení čáry `TagMode` a zapsat značku s uzavírací značkou. (Příklad kódu je poskytován později v tomto kurzu.)

   * @No__t_0 (znak dolaru) na následujícím řádku používá [interpolované řetězce](/dotnet/csharp/language-reference/keywords/interpolated-strings):

   ```cshtml
   $@"<ul><li><strong>Version:</strong> {Info.Version}</li>
   ```

1. Do zobrazení *About. cshtml* přidejte následující kód. Zvýrazněný kód zobrazí informace o webu.

   [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?highlight=1,4-8, 18-999)]

   > [!NOTE]
   > Ve značce Razor zobrazené níže:
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?range=18-18)]
   >
   > Razor ví, že atribut `info` je třída, nikoli řetězec a vy chcete napsat C# kód. Jakýkoli pomocný atribut neřetězcové značky by měl být napsán bez `@`ho znaku.

1. Spusťte aplikaci a přejděte do zobrazení o webu, kde najdete informace o webovém serveru.

   > [!NOTE]
   > Můžete použít následující kód s uzavírací značkou a odebrat čáru s `TagMode.StartTagAndEndTag` v Pomocníkovi značek:
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutNotSelfClosing.cshtml?range=20-21)]

## <a name="condition-tag-helper"></a>Pomocník značek podmínky

Pomocník značek podmínky vykreslí výstup, když prošla pravdivá hodnota.

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

### <a name="avoid-tag-helper-conflicts"></a>Vyhnout se konfliktům pomocníka značek

V této části napíšete dvojici pomocníků značek pro Automatické propojování. První nahradí značku obsahující adresu URL začínající řetězcem HTTP na značku kotvy HTML obsahující stejnou adresu URL (a tím poskytuje odkaz na adresu URL). Druhý postup bude stejný jako u adresy URL začínající na WWW.

Vzhledem k tomu, že tyto dva pomocníky úzce souvisejí a vy je můžete v budoucnu Refaktorovat, uchováváme je ve stejném souboru.

1. Do složky *TagHelpers* přidejte následující třídu `AutoLinkerHttpTagHelper`.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=7-19)]

   >[!NOTE]
   >Třída `AutoLinkerHttpTagHelper` cílí `p` prvky a pomocí [regulárního výrazu](/dotnet/standard/base-types/regular-expression-language-quick-reference) vytvoří kotvu.

1. Na konec souboru *views/Home/Contact. cshtml* přidejte následující kód:

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=19)]

1. Spusťte aplikaci a ověřte, že pomocná rutina značky vykresluje kotvu správně.

1. Aktualizujte třídu `AutoLinker` tak, aby zahrnovala `AutoLinkerWwwTagHelper`, který převede text na značku kotvy, který obsahuje také původní text na webové stránce. Aktualizovaný kód je zvýrazněný níže:

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?highlight=15-34&range=7-34)]

1. Spusťte aplikaci. Všimněte si, že text www se vykresluje jako odkaz, ale text HTTP není. Pokud umístíte bod přerušení do obou tříd, vidíte, že se nejprve spustí pomocná třída značek HTTP. Problémem je, že výstup pomocníka značek je uložen do mezipaměti a když je spuštěná pomocná rutina webové značky, přepíše výstup uložený v mezipaměti z pomocné rutiny značky HTTP. Později v tomto kurzu se dozvíte, jak řídit pořadí, ve kterém se budou zobrazovat pomocníka značek. Kód opravujeme následujícím způsobem:

   [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10,21,22,26&range=8-37)]

   > [!NOTE]
   > V první edici pomocníků značek pro Automatické propojování jste získali obsah cíle s následujícím kódem:
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=12)]
   >
   > To znamená, že zavoláte `GetChildContentAsync` pomocí `TagHelperOutput` předaného do metody `ProcessAsync`. Jak bylo zmíněno dříve, protože výstup je uložen do mezipaměti, poslední pomocník značek spouští službu WINS. Vyřešili jste tento problém s následujícím kódem:
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?range=34-35)]
   >
   > Výše uvedený kód zkontroluje, zda byl obsah změněn a v případě, že má, získá obsah z výstupní vyrovnávací paměti.

1. Spusťte aplikaci a ověřte, že tyto dva odkazy fungují podle očekávání. I když se může zdát, že je naše pomocník značek automatického linkeru správný a úplný, má malý problém. Pokud se jako první spustí pomocník webové značky, odkazy na webové odkazy nebudou správné. Aktualizujte kód přidáním `Order` přetížení pro řízení pořadí, ve kterém se značka spouští. Vlastnost `Order` určuje pořadí spouštění relativně k jiným pomocníkům značek cílících na stejný prvek. Výchozí hodnota pořadí je nula a instance s nižšími hodnotami jsou spouštěny jako první.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?highlight=5,6,7,8&range=8-15)]

   Předchozí kód zaručuje, že pomocník značek HTTP se spustí před pomocníkem webové značky. Změňte `Order` na `MaxValue` a ověřte, zda je kód vygenerovaný pro značku WWW nesprávný.

## <a name="inspect-and-retrieve-child-content"></a>Kontrola a načtení podřízeného obsahu

Pomocník značek poskytuje několik vlastností pro načtení obsahu.

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

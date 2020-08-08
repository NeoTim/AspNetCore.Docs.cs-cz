---
title: Vytváření značek pomocníků v ASP.NET Core
author: rick-anderson
description: Naučte se vytvářet pomocníky značek v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/authoring
ms.openlocfilehash: d40b37a4ca8acf57b662124597a8ebc3b90bffd2
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018162"
---
# <a name="author-tag-helpers-in-aspnet-core"></a>Vytváření značek pomocníků v ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/authoring/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="get-started-with-tag-helpers"></a>Začínáme s pomocníky značek

V tomto kurzu se seznámíte se sestavami programování značek. [Úvod do pomocníků značek](intro.md) popisuje výhody, které poskytují pomocníky pro tag.

Pomocný objekt tag je libovolná třída, která implementuje `ITagHelper` rozhraní. Nicméně při vytváření pomocné rutiny značky se všeobecně odvodit z, pokud k tomu dojde, získáte `TagHelper` přístup k `Process` metodě.

1. Vytvořte nový projekt ASP.NET Core s názvem **AuthoringTagHelpers**. Pro tento projekt nebudete potřebovat ověření.

1. Vytvořte složku pro uložení pomocníků značek s názvem *TagHelpers*. Složka *TagHelpers* *není povinná* , ale jedná se o rozumnou konvenci. Teď začneme psát některé pomocníky jednoduchých značek.

## <a name="a-minimal-tag-helper"></a>Pomocný Pomocník s minimální značkou

V této části napíšete pomocníka značek, který aktualizuje značku e-mailu. Například:

```html
<email>Support</email>
```

Server použije pomocníka pro označení e-mailu k převedení tohoto označení na následující:

```html
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

To znamená, že značka kotvy vytvoří tento e-mailový odkaz. To můžete chtít udělat, pokud píšete modul blogu a potřebujete ho poslat e-mailem pro marketing, podporu a další kontakty, a to vše ve stejné doméně.

1. `EmailTagHelper`Do složky *TagHelpers* přidejte následující třídu.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1EmailTagHelperCopy.cs)]

   * Pomocník značek používá konvenci pojmenování, která cílí na prvky názvu kořenové třídy (mínus část *taghelperu* názvu třídy). V tomto příkladu je kořenový název **EmailTagHelper** *e-mailem*, takže bude `<email>` cílová značka. Tato konvence vytváření názvů by měla fungovat pro většinu pomocníků se značkami, a to později v části jak ji přepsat.

   * `EmailTagHelper`Třída je odvozena z `TagHelper` . `TagHelper`Třída poskytuje metody a vlastnosti pro psaní pomocníků značek.

   * Přepsaná `Process` Metoda řídí, co má pomocník značek při spuštění. `TagHelper`Třída také poskytuje asynchronní verzi ( `ProcessAsync` ) se stejnými parametry.

   * Kontextový parametr pro `Process` (a `ProcessAsync` ) obsahuje informace spojené s prováděním aktuální značky HTML.

   * Výstupní parametr do `Process` (a `ProcessAsync` ) obsahuje stavový zástupce prvku HTML původního zdroje použitého ke generování značek a obsahu jazyka HTML.

   * Název naší třídy má příponu **taghelperu**, která *není požadována, ale je* považována za osvědčenou konvenci. Třídu můžete deklarovat jako:

   ```csharp
   public class Email : TagHelper
   ```

1. Pro zpřístupnění `EmailTagHelper` třídy všem našim Razor zobrazením přidejte `addTagHelper` direktivu do souboru *views/_ViewImports. cshtml* :

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopyEmail.cshtml?highlight=2,3)]

   Výše uvedený kód používá syntaxi zástupných znaků k určení všech pomocníků značek v našem sestavení bude k dispozici. První řetězec po `@addTagHelper` určení pomocné rutiny značky, která se má načíst (použijte * pro všechny pomocné rutiny značky) a druhý řetězec "AuthoringTagHelpers" Určuje sestavení, ve kterém je pomocník značek. Všimněte si také, že druhý řádek přináší do ASP.NET Core pomocníka značek MVC pomocí syntaxe zástupných znaků (tyto pomocníky jsou popsány v tématu [Úvod k označení pomocníků se značkami](intro.md)). Je to `@addTagHelper` direktiva, která zpřístupňuje pomocníka značky pro Razor zobrazení. Alternativně můžete zadat plně kvalifikovaný název (FQN) pro pomocnou značku, jak je znázorněno níže:

```csharp
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

<!--
the following snippet uses TagHelpers3 and should use TagHelpers (not the 3)
    [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImports.cshtml?highlight=3&range=1-3)]
-->

Chcete-li přidat pomocníka značek pro zobrazení pomocí FQN, nejprve přidejte FQN ( `AuthoringTagHelpers.TagHelpers.EmailTagHelper` ) a potom **název sestavení** (*AuthoringTagHelpers*, ne nutně `namespace` ). Většina vývojářů bude chtít použít zástupnou syntaxi. [Úvod k](intro.md) pomocníkům značek odkazuje na podrobné informace o přidání, odebrání, hierarchii a zástupné syntaxi pomocníka značek.

1. Aktualizujte kód v souboru *views/Home/Contact. cshtml* pomocí těchto změn:

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. Spusťte aplikaci a použijte oblíbený prohlížeč k zobrazení zdroje HTML, abyste mohli ověřit, že jsou značky e-mailu nahrazeny značkou ukotvení (například `<a>Support</a>` ). *Podpora* a *Marketing* se vykreslují jako odkazy, ale nemají atribut, aby byly `href` funkční. Opravujeme ho v další části.

## <a name="setattribute-and-setcontent"></a>SetAttributes a SetContent

V této části aktualizujeme, aby se `EmailTagHelper` vytvořila platná značka ukotvení pro e-mail. Aktualizujeme ji, aby provedla informace ze Razor zobrazení (ve formě `mail-to` atributu) a použila ho při generování kotvy.

Aktualizujte `EmailTagHelper` třídu následujícím způsobem:

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?range=6-22)]

* Názvy tříd a vlastností jazyka Pascal-použita pro pomocníky značek jsou přeloženy do jejich [kebabho případu](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101). Proto pro použití atributu použijete `MailTo` `<email mail-to="value"/>` ekvivalent.

* Poslední řádek nastaví dokončený obsah pro naši pomocníka s minimální funkční značkou.

* Zvýrazněný řádek ukazuje syntaxi pro přidání atributů:

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?highlight=6&range=14-21)]

Tento přístup funguje pro atribut "href", pokud aktuálně neexistuje v kolekci atributů. Můžete také použít `output.Attributes.Add` metodu pro přidání atributu pomocníka značky na konec kolekce atributů značek.

1. Aktualizujte kód v souboru *views/Home/Contact. cshtml* pomocí těchto změn:

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/ContactCopy.cshtml?highlight=15,16)]

1. Spusťte aplikaci a ověřte, že generuje správné odkazy.

<a name="self-closing"></a>

   > [!NOTE]
   > Pokud byste chtěli napsat označení e-mailu jako samoobslužné ( `<email mail-to="Rick" />` ), konečný výstup by taky měl samo uzavírací. Chcete-li povolit možnost psaní značky pouze pomocí počáteční značky ( `<email mail-to="Rick">` ), je nutné označit třídu následujícím způsobem:
   >
   > [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailVoid.cs?highlight=1&range=6-10)]

   Pomocí samoobslužného pomocníka značek e-mailu by byl výstup `<a href="mailto:Rick@contoso.com" />` . Samouzavírací značky kotvy nejsou platné HTML, takže byste ji neměli chtít vytvořit, ale možná budete chtít vytvořit pomocníka značek, který je samo samouzavírací. Pomocník značek nastaví typ `TagMode` vlastnosti po čtení značky.

### <a name="processasync"></a>ProcessAsync

V této části napíšeme pomocníka asynchronního e-mailu.

1. Třídu nahraďte `EmailTagHelper` následujícím kódem:

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelper.cs?range=6-17)]

   **Poznámky:**

   * Tato verze používá asynchronní `ProcessAsync` metodu. Asynchronní `GetChildContentAsync` vrátí objekt `Task` obsahující `TagHelperContent` .

   * `output`K získání obsahu elementu HTML použijte parametr.

1. Proveďte následující změny v souboru *views/Home/Contact. cshtml* , aby pomocník značek mohl získat cílový e-mail.

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. Spusťte aplikaci a ověřte, že generuje platné e-mailové odkazy.

### <a name="removeall-precontentsethtmlcontent-and-postcontentsethtmlcontent"></a>RemoveAll, Content. SetHtmlContent a PostContent. SetHtmlContent

1. `BoldTagHelper`Do složky *TagHelpers* přidejte následující třídu.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/BoldTagHelper.cs)]

   * `[HtmlTargetElement]`Atribut předává parametr atributu, který určuje, že všechny prvky jazyka HTML obsahující atribut HTML s názvem "bold" budou odpovídat a `Process` Metoda override ve třídě bude spuštěna. V naší ukázce `Process` Metoda odebere atribut "bold" a obklopí obsahující značku pomocí `<strong></strong>` .

   * Vzhledem k tomu, že nechcete nahradit existující obsah značky, je nutné zapsat počáteční `<strong>` značku s `PreContent.SetHtmlContent` metodou a uzavírací `</strong>` značkou s `PostContent.SetHtmlContent` metodou.

1. Upravte zobrazení *About. cshtml* tak, aby obsahovalo `bold` hodnotu atributu. Dokončený kód je uveden níže.

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutBoldOnly.cshtml?highlight=7)]

1. Spusťte aplikaci. Pomocí oblíbeného prohlížeče můžete zkontrolovat zdroj a ověřit označení.

   `[HtmlTargetElement]`Atribut uvedený výše se zaměřuje pouze na kód HTML, který poskytuje název atributu "bold". `<bold>`Element nebyl změněn pomocníkem značek.

1. Odkomentujte `[HtmlTargetElement]` řádek atributu a ve výchozím nastavení se zacílí na `<bold>` značky, tj. HTML značky formuláře `<bold>` . Nezapomeňte, že výchozí konvence pojmenování budou odpovídat názvu třídy **tučně**taghelperu na `<bold>` značky.

1. Spusťte aplikaci a ověřte, zda `<bold>` je značka zpracována pomocníkem značek.

Upravení třídu s více `[HtmlTargetElement]` atributy má za následek logický nebo cílový. Například pomocí následujícího kódu se bude shodovat značka tučného písma nebo tučného atributu.

[!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zBoldTagHelperCopy.cs?highlight=1,2&range=5-15)]

Pokud je do stejného příkazu přidáno více atributů, modul runtime je považuje za logický a. Například v následujícím kódu musí být element HTML pojmenován tučně s atributem s názvem "bold" ( `<bold bold />` ), aby odpovídal.

```csharp
[HtmlTargetElement("bold", Attributes = "bold")]
   ```

Můžete také použít `[HtmlTargetElement]` ke změně názvu cílového prvku. Například pokud jste chtěli `BoldTagHelper` cílit na `<MyBold>` značky, použijte následující atribut:

```csharp
[HtmlTargetElement("MyBold")]
```

## <a name="pass-a-model-to-a-tag-helper"></a>Předání modelu do pomocné rutiny značky

1. Přidejte složku *modelů* .

1. `WebsiteContext`Do složky *modely* přidejte následující třídu:

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Models/WebsiteContext.cs)]

1. `WebsiteInformationTagHelper`Do složky *TagHelpers* přidejte následující třídu.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/WebsiteInformationTagHelper.cs)]

   * Jak už bylo uvedeno výše, pomocník značek překládá názvy tříd C# použita a vlastnosti pro pomocníky značek na [kebab](https://wiki.c2.com/?KebabCase). Proto pro použití `WebsiteInformationTagHelper` v Razor můžete psát `<website-information />` .

   * Nejste explicitně identifikovali cílový element s `[HtmlTargetElement]` atributem, takže výchozí nastavení `website-information` bude cíleno. Pokud jste použili následující atribut (Všimněte si, že není kebab Case, ale odpovídá názvu třídy):

   ```csharp
   [HtmlTargetElement("WebsiteInformation")]
   ```

   Značka Case kebab se `<website-information />` neshoduje. Pokud chcete použít `[HtmlTargetElement]` atribut, použijte kebab případ, jak je znázorněno níže:

   ```csharp
   [HtmlTargetElement("Website-Information")]
   ```

   * Prvky, které jsou samy samouzavírací, nemají žádný obsah. V tomto příkladu bude Razor značka používat samočinně uzavírací značku, ale pomocník značek vytvoří prvek [oddílu](https://www.w3.org/TR/html5/sections.html#the-section-element) (což není samoobslužné zavírání a píšete obsah uvnitř `section` elementu). Proto je nutné nastavit `TagMode` pro `StartTagAndEndTag` zápis výstupu. Alternativně můžete zadat komentář k nastavení čáry `TagMode` a zapsat značku s uzavírací značkou. (Příklad kódu je poskytován později v tomto kurzu.)

   * `$`Znak dolaru (dolar) na následujícím řádku používá [interpolované řetězce](/dotnet/csharp/language-reference/keywords/interpolated-strings):

   ```cshtml
   $@"<ul><li><strong>Version:</strong> {Info.Version}</li>
   ```

1. Do zobrazení *About. cshtml* přidejte následující kód. Zvýrazněný kód zobrazí informace o webu.

   [!code-cshtml[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?highlight=1,4-8, 18-999)]

   > [!NOTE]
   > V Razor kódu zobrazeném níže:
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?range=18-18)]
   >
   > Razorví, že `info` atribut je třída, ne řetězec a vy chcete psát kód jazyka C#. Všechny pomocné atributy značek bez řetězce by měly být zapsány bez `@` znaku.

1. Spusťte aplikaci a přejděte do zobrazení o webu, kde najdete informace o webovém serveru.

   > [!NOTE]
   > Můžete použít následující kód s uzavírací značkou a odebrat řádek s `TagMode.StartTagAndEndTag` pomocníkem značek:
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutNotSelfClosing.cshtml?range=20-21)]

## <a name="condition-tag-helper"></a>Pomocník značek podmínky

Pomocník značek podmínky vykreslí výstup, když prošla pravdivá hodnota.

1. `ConditionTagHelper`Do složky *TagHelpers* přidejte následující třídu.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/ConditionTagHelper.cs)]

1. Obsah souboru *views/Home/index. cshtml* nahraďte následujícím kódem:

   [!code-cshtml[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Index.cshtml)]

1. Nahraďte `Index` metodu v `Home` kontroleru následujícím kódem:

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Controllers/HomeController.cs?range=9-18)]

1. Spusťte aplikaci a přejděte na domovskou stránku. Značky v podmíněném kódu `div` nebudou vykresleny. Připojit řetězec dotazu `?approved=true` k adrese URL (například `http://localhost:1235/Home/Index?approved=true` ). `approved`je nastaven na hodnotu true a zobrazí se podmíněný kód.

> [!NOTE]
> Použijte operátor [nameof](/dotnet/csharp/language-reference/keywords/nameof) k určení atributu pro cílení namísto zadání řetězce jako u pomocníka s tučnými značkami:
>
> [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zConditionTagHelperCopy.cs?highlight=1,2,5&range=5-18)]
>
> [Nameof](/dotnet/csharp/language-reference/keywords/nameof) operátor ochrany kódu by měl být někdy refaktorované (název můžeme změnit na `RedCondition` ).

### <a name="avoid-tag-helper-conflicts"></a>Vyhnout se konfliktům pomocníka značek

V této části napíšete dvojici pomocníků značek pro Automatické propojování. První nahradí značku obsahující adresu URL začínající řetězcem HTTP na značku kotvy HTML obsahující stejnou adresu URL (a tím poskytuje odkaz na adresu URL). Druhý postup bude stejný jako u adresy URL začínající na WWW.

Vzhledem k tomu, že tyto dva pomocníky úzce souvisejí a vy je můžete v budoucnu Refaktorovat, uchováváme je ve stejném souboru.

1. `AutoLinkerHttpTagHelper`Do složky *TagHelpers* přidejte následující třídu.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=7-19)]

   >[!NOTE]
   >`AutoLinkerHttpTagHelper`Třída cílí na `p` prvky a používá [regulární výraz](/dotnet/standard/base-types/regular-expression-language-quick-reference) pro vytvoření kotvy.

1. Na konec souboru *views/Home/Contact. cshtml* přidejte následující kód:

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=19)]

1. Spusťte aplikaci a ověřte, že pomocná rutina značky vykresluje kotvu správně.

1. Aktualizujte `AutoLinker` třídu tak, aby obsahovala, `AutoLinkerWwwTagHelper` která převede text na značku kotvy, která obsahuje také původní text na webové stránce. Aktualizovaný kód je zvýrazněný níže:

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?highlight=15-34&range=7-34)]

1. Spusťte aplikaci. Všimněte si, že text www se vykresluje jako odkaz, ale text HTTP není. Pokud umístíte bod přerušení do obou tříd, vidíte, že se nejprve spustí pomocná třída značek HTTP. Problémem je, že výstup pomocníka značek je uložen do mezipaměti a když je spuštěná pomocná rutina webové značky, přepíše výstup uložený v mezipaměti z pomocné rutiny značky HTTP. Později v tomto kurzu se dozvíte, jak řídit pořadí, ve kterém se budou zobrazovat pomocníka značek. Kód opravujeme následujícím způsobem:

   [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10,21,22,26&range=8-37)]

   > [!NOTE]
   > V první edici pomocníků značek pro Automatické propojování jste získali obsah cíle s následujícím kódem:
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=12)]
   >
   > To znamená, že zavoláte `GetChildContentAsync` pomocí `TagHelperOutput` předané `ProcessAsync` metody. Jak bylo zmíněno dříve, protože výstup je uložen do mezipaměti, poslední pomocník značek spouští službu WINS. Vyřešili jste tento problém s následujícím kódem:
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?range=34-35)]
   >
   > Výše uvedený kód zkontroluje, zda byl obsah změněn a v případě, že má, získá obsah z výstupní vyrovnávací paměti.

1. Spusťte aplikaci a ověřte, že tyto dva odkazy fungují podle očekávání. I když se může zdát, že je naše pomocník značek automatického linkeru správný a úplný, má malý problém. Pokud se jako první spustí pomocník webové značky, odkazy na webové odkazy nebudou správné. Aktualizujte kód přidáním `Order` přetížení pro řízení pořadí, v němž je značka spuštěna. `Order`Vlastnost určuje pořadí spouštění relativně k jiným pomocníkům značek cílících na stejný prvek. Výchozí hodnota pořadí je nula a instance s nižšími hodnotami jsou spouštěny jako první.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?highlight=5,6,7,8&range=8-15)]

   Předchozí kód zaručuje, že pomocník značek HTTP se spustí před pomocníkem webové značky. Změňte `Order` na `MaxValue` a ověřte, zda je kód vygenerovaný pro značku www nesprávný.

## <a name="inspect-and-retrieve-child-content"></a>Kontrola a načtení podřízeného obsahu

Pomocník značek poskytuje několik vlastností pro načtení obsahu.

* Výsledek `GetChildContentAsync` lze připojit k `output.Content` .
* Můžete zkontrolovat výsledek `GetChildContentAsync` s `GetContent` .
* Pokud upravíte `output.Content` , text taghelperu nebude proveden ani vykreslen, pokud nebudete volat `GetChildContentAsync` jako v našem příkladu automatického linkeru:

[!code-csharp[](../../views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10&range=8-21)]

* Více volání `GetChildContentAsync` vrátí stejnou hodnotu a znovu nespustí `TagHelper` tělo, pokud nepředáte parametr false, který označuje, že nechcete použít výsledek uložený v mezipaměti.

## <a name="load-minified-partial-view-taghelper"></a>Načíst minifikovaného částečného zobrazení Taghelperu

V produkčních prostředích je možné výkon zlepšit načtením částečně minifikovaného zobrazení. Využití částečného zobrazení minifikovaného v produkčním prostředí:

* Vytvořte nebo nastavte proces před sestavením, který minifies částečná zobrazení.
* Pomocí následujícího kódu načtěte minifikovaného částečná zobrazení v nevývojovém prostředí.

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/MinifiedVersionTagHelper.cs?name=snippet)]

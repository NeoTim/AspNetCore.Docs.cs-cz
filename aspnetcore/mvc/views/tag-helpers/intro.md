---
title: Tagování pomocníků v ASP.NET Core
author: rick-anderson
description: Přečtěte si, co jsou pomocníky značek a jak je používat v ASP.NET Core.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 03/18/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/intro
ms.openlocfilehash: 36d8c3dd42ac9d0acdb93bba4bc39ab18ad5c52f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400892"
---
# <a name="tag-helpers-in-aspnet-core"></a>Tagování pomocníků v ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="what-are-tag-helpers"></a>Co jsou pomocníky značek

Pomáhat pomocníkům při vytváření kódu a vykreslování prvků HTML v souborech, které umožňují kód na straně serveru Razor Například předdefinovaná `ImageTagHelper` verze může k názvu image připojit číslo verze. Pokaždé, když se obrázek změní, server vygeneruje pro Image novou jedinečnou verzi, takže klienti budou mít zaručený přístup k aktuální imagi (místo k zastaralému obrázku v mezipaměti). K dispozici je mnoho vestavěných pomocníků značek pro běžné úkoly, jako je vytváření formulářů, odkazů, načítání assetů a ještě více dostupných ve veřejných úložištích GitHub a jako balíčky NuGet. Pomocníky značek jsou vytvořeny v jazyce C# a jsou cíleny na prvky HTML na základě názvu elementu, názvu atributu nebo nadřazené značky. Například Vestavěná `LabelTagHelper` možnost může cílit na prvek jazyka HTML `<label>` při `LabelTagHelper` použití atributů. Pokud jste obeznámeni s [pomocníky HTML](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers), značky pomocníků vám omezí explicitní přechody mezi HTML a C# v Razor zobrazeních. V mnoha případech pomocné rutiny HTML poskytují alternativní přístup k konkrétní pomocné rutině značek, ale je důležité rozpoznat, že pomocné rutiny značky nenahrazují nápovědu HTML a že pro každou pomocnou nápovědu HTML není k dispozici pomocný pomocník značek. [Pomocník značek v porovnání s pomocníky HTML](#tag-helpers-compared-to-html-helpers) vysvětluje rozdíly podrobněji.

## <a name="what-tag-helpers-provide"></a>Co poskytují pomocníky značek

**Prostředí pro vývoj s uživatelsky přívětivými HTML** Ve většině případů Razor značky pomocí pomocníků značek vypadají jako standardní HTML. Návrháři front-end Conversant s HTML/CSS/JavaScriptem mohou upravovat Razor bez výuky Razor syntaxe jazyka C#.

**Bohatý prostředí IntelliSense pro vytváření kódu HTML a Razor značení** je ostrého kontrastu s pomocníky HTML, předchozím přístupem k vytváření značek v zobrazeních na straně serveru Razor . [Pomocník značek v porovnání s pomocníky HTML](#tag-helpers-compared-to-html-helpers) vysvětluje rozdíly podrobněji. [Podpora technologie IntelliSense pro pomocníci značek](#intellisense-support-for-tag-helpers) vysvětluje prostředí IntelliSense. I vývojáři, kteří se s Razor syntaxí jazyka c# setkali, mají vyšší produktivitu pomocí pomocníků značek než psaní kódu jazyka c# Razor .

**Způsob, jak zvýšit produktivitu a umožnit vám poskytovat robustnější, spolehlivý a udržovatelný kód s využitím informací, které jsou k dispozici pouze na serveru** Například historicky heslo při aktualizaci imagí bylo změnit název obrázku při změně obrázku. Obrázky by měly být agresivní v mezipaměti z důvodu výkonu, a pokud nezměníte název obrázku, riskujete, že klienti získávají starou kopii. V minulosti se po úpravě obrázku změnil název a každý odkaz na obrázek ve webové aplikaci, který je potřeba aktualizovat. Nejenom je to velmi náročné na práci, je to také náchylné k chybám (může se stát, že nechtěně zadáte špatný řetězec atd.). Integrovaná `ImageTagHelper` možnost může to provést automaticky. Rozhraní `ImageTagHelper` může k názvu image připojit číslo verze, takže když se obrázek změní, server automaticky vygeneruje novou jedinečnou verzi image. Klientům je zaručeno získat aktuální bitovou kopii. Tato odolnost a úspory na práci jsou v podstatě bezplatné pomocí `ImageTagHelper` .

Většina vestavěných pomocníků značek cílí na standardní prvky HTML a poskytují atributy na straně serveru pro element. Například `<input>` prvek použitý v mnoha zobrazeních ve složce *views/Account* obsahuje `asp-for` atribut. Tento atribut extrahuje název zadané vlastnosti modelu do vykresleného HTML. Vezměte v úvahu Razor zobrazení s následujícím modelem:

```csharp
public class Movie
{
    public int ID { get; set; }
    public string Title { get; set; }
    public DateTime ReleaseDate { get; set; }
    public string Genre { get; set; }
    public decimal Price { get; set; }
}
```

Následující Razor kód:

```cshtml
<label asp-for="Movie.Title"></label>
```

Generuje následující kód HTML:

```html
<label for="Movie_Title">Title</label>
```

`asp-for`Atribut je zpřístupněn `For` vlastností v [LabelTagHelper](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.labeltaghelper?view=aspnetcore-2.0). Další informace najdete v tématu [autora značek pomocníka](xref:mvc/views/tag-helpers/authoring) .

## <a name="managing-tag-helper-scope"></a>Správa oboru pomocníka značek

Obor pomocníků značek je ovládán kombinací `@addTagHelper` , `@removeTagHelper` a znaku "!" pro odhlášení.

<a name="add-helper-label"></a>

### <a name="addtaghelper-makes-tag-helpers-available"></a>`@addTagHelper`zpřístupňuje pomocníkům značky

Pokud vytvoříte novou ASP.NET Core webovou aplikaci s názvem *AuthoringTagHelpers*, budou do projektu přidány následující soubory *zobrazení nebo _ViewImports. cshtml* :

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=2&range=2-3)]

`@addTagHelper`Direktiva zpřístupňuje pomocníkům značky pro zobrazení. V tomto případě je soubor zobrazení *stránky/_ViewImports. cshtml*, který je ve výchozím nastavení děděn všemi soubory ve složce a složkách *stránky* . zpřístupňují se pomocníkům značek. Výše uvedený kód používá syntaxi zástupných znaků (" \* ") k určení, že všechny pomocníky značek v zadaném sestavení (*Microsoft. AspNetCore. Mvc. TagHelpers*) budou k dispozici pro každý soubor zobrazení v adresáři *zobrazení* nebo v podadresáři. První parametr po `@addTagHelper` určení pomocníka značek pro načtení (používáme " \* " pro všechny pomocníky značky) a druhý parametr "Microsoft. AspNetCore. Mvc. TagHelpers" Určuje sestavení obsahující pomocníka značek. *Microsoft. AspNetCore. Mvc. TagHelpers* je sestavení pro předdefinované ASP.NET Core pomocníka značek.

Chcete-li zveřejnit všechny pomocníky značek v tomto projektu (který vytvoří sestavení s názvem *AuthoringTagHelpers*), použijte následující:

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=3)]

Pokud projekt obsahuje `EmailTagHelper` s výchozím oborem názvů ( `AuthoringTagHelpers.TagHelpers.EmailTagHelper` ), můžete zadat plně kvalifikovaný název (FQN) pomocné rutiny značky:

```cshtml
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

Chcete-li přidat pomocníka značek pro zobrazení pomocí FQN, nejprve přidejte FQN ( `AuthoringTagHelpers.TagHelpers.EmailTagHelper` ) a potom název sestavení (*AuthoringTagHelpers*). Většina vývojářů upřednostňuje použití \* Syntaxe zástupných znaků. Syntaxe zástupných znaků umožňuje vložit zástupný znak " \* " jako příponu do FQN. Například kterákoli z následujících direktiv bude přinášet do `EmailTagHelper` :

```cshtml
@addTagHelper AuthoringTagHelpers.TagHelpers.E*, AuthoringTagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.Email*, AuthoringTagHelpers
```

Jak bylo zmíněno dříve, přidání `@addTagHelper` direktivy do souboru views */_ViewImports. cshtml* zpřístupní pomocníka značek pro všechny soubory zobrazení v adresáři *zobrazení* a podadresářích. Direktivu můžete použít pro `@addTagHelper` konkrétní soubory zobrazení, pokud se chcete přihlásit k vystavení pomocníka značek jenom pro zobrazení.

<a name="remove-razor-directives-label"></a>

### <a name="removetaghelper-removes-tag-helpers"></a>`@removeTagHelper`Odebere pomocníky značek.

`@removeTagHelper`Má stejné dva parametry jako `@addTagHelper` a odebere pomocníka značek, který byl dříve přidán. Například `@removeTagHelper` použití na konkrétní zobrazení odebere ze zobrazení určenou pomocnou značku. Použití `@removeTagHelper` v souboru *views/Folder/_ViewImports. cshtml* odstraní určenou pomocnou značku ze všech zobrazení ve *složce*.

### <a name="controlling-tag-helper-scope-with-the-_viewimportscshtml-file"></a>Řízení oboru pomocné rutiny značek pomocí souboru *_ViewImports. cshtml*

*_ViewImports. cshtml* můžete přidat do libovolné složky zobrazení a modul zobrazení použije direktivy z obou souborů i ze souboru *views/_ViewImports. cshtml* . Pokud jste přidali prázdné *zobrazení/soubor _ViewImports. cshtml* pro *domovskou* zobrazení, nebude by žádná změna, protože *_ViewImports soubor. cshtml* je aditivní. Jakékoli `@addTagHelper` direktivy, které přidáte do souboru *views/Home/_ViewImports. cshtml* (které nejsou ve výchozím *zobrazení/_ViewImports. cshtml* ), by tyto pomocníky značek mohli vystavit pouze pro zobrazení v *domovské* složce.

<a name="opt-out"></a>

### <a name="opting-out-of-individual-elements"></a>Zrušení individuálních prvků

Pomocníka značek můžete zakázat na úrovni elementu pomocí znaku pro výslovný odkaz na značku ("!"). `Email`Ověřování je například zakázáno v `<span>` nástroji pomocí znaku pro výslovný souhlas s pomocníkem značek:

```cshtml
<!span asp-validation-for="Email" class="text-danger"></!span>
```

Pro otevírací a uzavírací značku musíte použít znak výslovného souhlasu s tagem. (Editor sady Visual Studio automaticky přidá znak pro odhlášení do uzavírací značky, když ho přidáte do počáteční značky). Po přidání znaku pro odhlášení se prvky a pomocné atributy značky již nebudou zobrazovat v charakteristickém písmu.

<a name="prefix-razor-directives-label"></a>

### <a name="using-taghelperprefix-to-make-tag-helper-usage-explicit"></a>Použití `@tagHelperPrefix` k nastavení explicitního použití pomocníka značek

`@tagHelperPrefix`Direktiva umožňuje zadat řetězec předpony značky pro povolení podpory značek a nastavit explicitní použití pomocníka značek. Například můžete přidat následující kód do souboru *views/_ViewImports. cshtml* :

```cshtml
@tagHelperPrefix th:
```

V níže uvedeném obrázku kódu je předpona pomocníka značek nastavena na hodnotu `th:` , takže pouze ty prvky, které používají předpony `th:` značky support, mají pro pomocná pole značka (s podporou značek). `<label>`Elementy a `<input>` mají předponu pomocné rutiny značky a jsou zapnuty značkou pomocníka značek, zatímco `<span>` element není.

![image](intro/_static/thp.png)

Stejná pravidla hierarchie, která platí `@addTagHelper` také pro platí pro `@tagHelperPrefix` .

## <a name="self-closing-tag-helpers"></a>Samoobslužné pomocníky značek

Mnoho pomocníků značek se nedá použít jako samouzavírací značky. Některé pomocníky s značkami jsou navržené tak, aby se samočinně uzavírající značky. Použití pomocníka značek, který nebyl navržen jako samouzavírací, potlačí Vykreslený výstup. Samostatný uzavírací pomocník značek má za následek samouzavírací značku ve vykresleném výstupu. Další informace najdete v [této poznámce](xref:mvc/views/tag-helpers/authoring#self-closing) při [vytváření pomocníků značek](xref:mvc/views/tag-helpers/authoring).

## <a name="c-in-tag-helpers-attributedeclaration"></a>C# v atributu nebo deklaraci pomocníka značek 

Pomocníka značek nepovoluje C# v oblasti atributu nebo deklarace značky elementu. Například následující kód není platný:

```cshtml
<input asp-for="LastName"  
       @(Model?.LicenseId == null ? "disabled" : string.Empty) />
```

Předchozí kód lze zapsat jako:

```cshtml
<input asp-for="LastName" 
       disabled="@(Model?.LicenseId == null)" />
```

## <a name="intellisense-support-for-tag-helpers"></a>Podpora technologie IntelliSense pro pomocné pomocníky značek

Když vytvoříte novou ASP.NET Core webovou aplikaci v aplikaci Visual Studio, přidá balíček NuGet "Microsoft. AspNetCore. Razor . Nástroje ". Toto je balíček, který přidává nástroje pomocníka značek.

Zvažte zápis elementu HTML `<label>` . Jakmile zadáte `<l` v editoru sady Visual Studio, IntelliSense zobrazí vyhovující prvky:

![image](intro/_static/label.png)

Nejenom získáte nápovědu HTML, ale ikonu (" @" symbol with "<>" pod ní).

![image](intro/_static/tagSym.png)

identifikuje element jako cílený pomocníky značek. Čistě prvky HTML (například `fieldset` ) zobrazí ikonu "<>".

Čistě značka HTML `<label>` zobrazuje značku HTML (s výchozím barevným motivem sady Visual Studio) v hnědém písmu, atributy červeně a hodnoty atributu modrou.

![image](intro/_static/LableHtmlTag.png)

Po zadání `<label` technologie IntelliSense zobrazí seznam dostupných atributů HTML/CSS a atributy cílené na pomocnou značku:

![image](intro/_static/labelattr.png)

Dokončení příkazu IntelliSense umožňuje zadat klávesu TAB k dokončení příkazu s vybranou hodnotou:

![image](intro/_static/stmtcomplete.png)

Jakmile je atribut pomocník značek zadán, změní se písma značky a atributu. Když použijete výchozí barevný motiv sady Visual Studio "Blue" nebo "Light", písmo je tučně fialové. Pokud používáte motiv "tmavý", písmo je tučně šedozelená. Obrázky v tomto dokumentu byly pořízeny pomocí výchozího motivu.

![image](intro/_static/labelaspfor2.png)

Můžete zadat zástupce sady Visual Studio *CompleteWord* (CTRL + MEZERNÍK je [Výchozí hodnota](/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio) uvnitř dvojitých uvozovek ("") a teď jste v jazyce c#, stejně jako byste byli ve třídě jazyka c#. IntelliSense zobrazí všechny metody a vlastnosti v modelu stránky. Metody a vlastnosti jsou k dispozici, protože typ vlastnosti je `ModelExpression` . Na obrázku níže upravujeme `Register` zobrazení, takže `RegisterViewModel` je k dispozici.

![image](intro/_static/intellemail.png)

IntelliSense zobrazí seznam vlastností a metod, které jsou k dispozici pro model na stránce. Bohatá prostředí IntelliSense vám pomůže vybrat třídu CSS:

![image](intro/_static/iclass.png)

![image](intro/_static/intel3.png)

## <a name="tag-helpers-compared-to-html-helpers"></a>Tagování pomocníků v porovnání s pomocníky HTML

Přihlaste se k prvkům jazyka HTML v Razor zobrazeních, zatímco [pomocníky HTML](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) jsou vyvolány jako metody s HTML v Razor zobrazeních. Vezměte v úvahu následující Razor kód, který vytvoří popisek HTML s nadpisem CSS třídy "titulek":

```cshtml
@Html.Label("FirstName", "First Name:", new {@class="caption"})
```

Symbol on ( `@` ) oznamuje, Razor že se jedná o začátek kódu. Následující dva parametry ("FirstName" a "first name": ") jsou řetězce, takže [IntelliSense](/visualstudio/ide/using-intellisense) nemůže pomáhat. Poslední argument:

```cshtml
new {@class="caption"}
```

Je anonymní objekt, který slouží k reprezentaci atributů. Protože `class` je klíčové slovo rezervované v jazyce C#, použijete `@` symbol k vynucení interpretace jazyka c# `@class=` jako symbolu (název vlastnosti). Do předkoncového návrháře (někdo známý s HTML/CSS/JavaScriptem a jinými klientskými technologiemi, ale není známý v jazyce C# a Razor ), je většina řádku cizí. Celý řádek musí být vytvořen bez usnadnění z IntelliSense.

Pomocí `LabelTagHelper` lze napsat stejné označení jako:

```cshtml
<label class="caption" asp-for="FirstName"></label>
```

V případě verze pomocné rutiny značek, jakmile zadáte `<l` v editoru sady Visual Studio, IntelliSense zobrazí vyhovující prvky:

![image](intro/_static/label.png)

Technologie IntelliSense vám pomůže napsat celý řádek.

Následující obrázek kódu ukazuje formu zobrazení */účtu/registru. cshtml* Razor vygenerovaného ze šablony ASP.NET 4.5. x MVC, která je součástí sady Visual Studio.

![image](intro/_static/regCS.png)

Editor sady Visual Studio zobrazí kód jazyka C# s šedým pozadím. Například `AntiForgeryToken` Pomocník HTML:

```cshtml
@Html.AntiForgeryToken()
```

se zobrazí se šedým pozadím. Většina značek v zobrazení registrů je C#. Porovnejte je s ekvivalentním přístupem pomocí pomocníků značek:

![image](intro/_static/regTH.png)

Značky jsou mnohem čisticí a snadněji se čtou, upravují a udržují než přístup k HTML pomocníkům. Kód jazyka C# se zmenší na minimum, o které server potřebuje znát. V editoru sady Visual Studio se zobrazí označení cílené pomocí pomocníka značek v rozlišujícím písmu.

Vezměte v úvahu tuto *e-mailovou* skupinu:

[!code-csharp[](intro/sample/Register.cshtml?range=12-18)]

Každý z atributů ASP-má hodnotu "E-mail", ale "E-mail" není řetězec. V tomto kontextu je "e-mailem" vlastnost výrazu modelu C# pro `RegisterViewModel` .

Editor sady Visual Studio vám pomůže napsat **všechny** značky v pomocné metodě příznaku značky ve formuláři registru, zatímco Visual Studio neposkytuje nápovědu pro většinu kódu v přístupu k HTML pomocníkům. [Podpora technologie IntelliSense pro pomocné pomocníky značek](#intellisense-support-for-tag-helpers) se dohlíží na podrobnosti o práci s pomocníky značek v editoru sady Visual Studio.

## <a name="tag-helpers-compared-to-web-server-controls"></a>Porovnávání pomocníků značek v porovnání s ovládacími prvky webového serveru

* Pomocníka značek nevlastní prvek, ke kterému jsou přidruženy; jednoduše se účastní vykreslování prvku a obsahu. [Ovládací prvky webového serveru](https://msdn.microsoft.com/library/7698y1f0.aspx) ASP.NET jsou deklarovány a vyvolány na stránce.

* [Ovládací prvky webového serveru](https://msdn.microsoft.com/library/zsyt68f1.aspx) mají netriviální životní cyklus, který může provádět vývoj a ladění obtížné.

* Ovládací prvky webového serveru umožňují přidat funkce do prvků model DOM (Document Object Model) klienta (DOM) pomocí ovládacího prvku klienta. Pomocník značek nemá žádný model DOM.

* Ovládací prvky webového serveru zahrnují automatické zjišťování prohlížečů. Pomocník značek nemá žádné znalosti o prohlížeči.

* Více pomocných rutin značek může působit na stejný prvek (viz téma [zamezení konfliktů pomocníka značek](xref:mvc/views/tag-helpers/authoring#avoid-tag-helper-conflicts) ), zatímco obvykle nelze vytvářet ovládací prvky webového serveru.

* Pomocník značek může upravit značku a obsah prvků HTML, na které jsou vymezeny, ale přímo na stránku nemění nic jiného. Ovládací prvky webového serveru mají méně konkrétní rozsah a mohou provádět akce, které mají vliv na ostatní části stránky. povolení nezamýšlených vedlejších účinků.

* Ovládací prvky webového serveru používají k převodu řetězců do objektů převaděče typů. U pomocníků se značkami pracujete nativně v jazyce C#, takže nemusíte provádět převod typu.

* Ovládací prvky webového serveru používají [System. ComponentModel](/dotnet/api/system.componentmodel) k implementaci chování komponent a ovládacích prvků za běhu a v době návrhu. `System.ComponentModel`zahrnuje základní třídy a rozhraní pro implementaci atributů a převaděčů typů, vázání na zdroje dat a součásti licencování. Na rozdíl od toho, že chcete označit pomocníky, které jsou obvykle odvozeny z `TagHelper` a `TagHelper` základní třída zpřístupňuje pouze dvě metody `Process` a `ProcessAsync` .

## <a name="customizing-the-tag-helper-element-font"></a>Přizpůsobení písma prvku pomocné značky

Písma a barvy můžete přizpůsobit v **nabídce nástroje**  >  **Možnosti**  >  **prostředí**  >  **a barvy**:

![image](intro/_static/fontoptions2.png)

[!INCLUDE[](~/includes/built-in-TH.md)]

## <a name="additional-resources"></a>Další zdroje

* [Vytváření pomocníků se značkami](xref:mvc/views/tag-helpers/authoring)
* [Práce s formuláři](xref:mvc/views/working-with-forms)
* [TagHelperSamples na GitHubu](https://github.com/dpaquette/TagHelperSamples) obsahuje ukázky pomocníka značek pro práci s [bootstrap](https://getbootstrap.com/).

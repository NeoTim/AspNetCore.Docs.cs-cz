---
title: Pomocné rutiny značek v ASP.NET Core
author: rick-anderson
description: Zjistěte, jaké jsou pomocné rutiny značek a jejich použití v ASP.NET Core.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 03/18/2019
uid: mvc/views/tag-helpers/intro
ms.openlocfilehash: 870ce2eb28f384b380cc1178842325dc28199f09
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814994"
---
# <a name="tag-helpers-in-aspnet-core"></a>Pomocné rutiny značek v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="what-are-tag-helpers"></a>Co jsou pomocné rutiny značek

Pomocné rutiny značek povolit kód na straně serveru k účasti na vytváření a vykreslování prvků HTML v souborech Razor. Například předdefinované `ImageTagHelper` číslo verze můžete připojit k názvu image. Pokaždé, když se změní na obrázku, server vygeneruje nové jedinečné verze pro bitovou kopii, proto je zaručeno, že klienti získat aktuální image (namísto zastaralých bitové kopie v mezipaměti). Existuje mnoho integrovaných pomocných rutin značek pro běžné úlohy – například vytváření formulářů, odkazy, načítání prostředků a další – a ještě větší počet je dostupný ve veřejných úložištích GitHub a jako NuGet balíčky. Pomocné rutiny značek jsou vytvořené v jazyce C# a cílí na základě název elementu, atributu nebo nadřazené značky elementů HTML. Například předdefinované `LabelTagHelper` HTML můžete cílit na `<label>` element při `LabelTagHelper` atributy jsou použity. Pokud jste obeznámeni s [pomocných rutin HTML](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers), pomocných rutin značek snížit explicitní přechody mezi HTML a C# v zobrazení syntaxe Razor. V mnoha případech pomocných rutin HTML poskytnout alternativní způsob konkrétní pomocné rutiny značky, ale je důležité uvědomit si, že pomocné rutiny značek nemáte nahradit pomocné rutiny HTML a není k dispozici pomocné rutiny značky pro každý pomocné rutiny HTML. [Ve srovnání s pomocných rutin HTML pomocné rutiny značky](#tag-helpers-compared-to-html-helpers) vysvětluje rozdíly mezi více podrobností.

## <a name="what-tag-helpers-provide"></a>Co poskytuje pomocné rutiny značek

**Vývojové prostředí podporou HTML** pro největší část kódu Razor pomocí pomocných rutin značek vypadá jako standardní HTML. Front-endu návrháři obeznámen s HTML/CSS a JavaScript můžete upravit Razor bez znalosti syntaxe jazyka C# Razor.

**Bohaté prostředí IntelliSense pro vytváření značky HTML a Razor** jde sharp oproti pomocné rutiny HTML, předchozí přístup k serverové vytváření značky v zobrazení syntaxe Razor. [Ve srovnání s pomocných rutin HTML pomocné rutiny značky](#tag-helpers-compared-to-html-helpers) vysvětluje rozdíly mezi více podrobností. [Podpora IntelliSense pro pomocné rutiny značek](#intellisense-support-for-tag-helpers) vysvětluje prostředí IntelliSense. Zaznamenal se syntaxí Razor C# i vývojáře zvýší se produktivita použití pomocných rutin značek než psaní kódu jazyka C# Razor.

**Způsob, jak budete produktivnější a nedokáže vytvořit robustnější a spolehlivé a udržovatelný kód pomocí informace, které jsou k dispozici pouze na serveru** v minulosti mantrou na aktualizace bitových kopií byl například chcete-li změnit název obrázku při změně na obrázku. Obrázky agresivně do mezipaměti kvůli výkonu a pokud změníte název image, riskujete získat kopii zastaralých klientů. V minulosti po obrázku se upraví, museli změnit název a každý odkaz na obrázek ve webové aplikaci potřeba aktualizovat. Nejenže je to velmi práce že náročné, je také náchylné k chybám (vám může přijít o odkaz, neúmyslném nesprávný řetězec atd.) Předdefinované `ImageTagHelper` můžete to udělal za vás automaticky. `ImageTagHelper` Připojit verze čísla do image s názvem, tak pokaždé, když se změní na obrázku, server automaticky vygeneruje nové jedinečné verze pro bitovou kopii. Je zaručeno, že klienti získat aktuální image. Tato úspory odolnosti a práci dodává v podstatě bezplatné pomocí `ImageTagHelper`.

Nejvíce integrovaných pomocných rutin značek cílit na standardní elementy jazyka HTML a poskytovat na straně serveru atributy pro element. Například `<input>` použít v mnoha zobrazení v prvku *zobrazení/účet* složka obsahuje `asp-for` atribut. Tento atribut extrahuje název vlastnosti zadaný model zobrazený HTML. Vezměte v úvahu zobrazení Razor s modelem následující:

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

Následující kód Razor:

```cshtml
<label asp-for="Movie.Title"></label>
```

Generuje následující kód HTML:

```html
<label for="Movie_Title">Title</label>
```

`asp-for` Atribut je k dispozici ve `For` vlastnost [LabelTagHelper](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.labeltaghelper?view=aspnetcore-2.0). Zobrazit [pomocných rutin značek Autor](xref:mvc/views/tag-helpers/authoring) Další informace.

## <a name="managing-tag-helper-scope"></a>Správa pomocné rutiny značky oboru

Obor pomocné rutiny značky je řízen pomocí kombinace `@addTagHelper`, `@removeTagHelper`a "!" znak odhlásit.

<a name="add-helper-label"></a>

### <a name="addtaghelper-makes-tag-helpers-available"></a>`@addTagHelper` zpřístupňuje pomocných rutin značek

Pokud vytvoříte novou webovou aplikaci ASP.NET Core s názvem *AuthoringTagHelpers*, následující *Views/_ViewImports.cshtml* soubor bude přidán do projektu:

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=2&range=2-3)]

`@addTagHelper` – Direktiva zpřístupní pomocných rutin značek k zobrazení. V takovém případě je soubor zobrazení *Pages/_ViewImports.cshtml*, která ve výchozím nastavení zdědí všechny soubory v *stránky* složce a jejích podsložkách; zpřístupnění pomocných rutin značek. Výše uvedený kód používá syntaxe zástupných znaků ("\*") určíte, že všechny pomocných rutin značek v zadaném sestavení (*Microsoft.AspNetCore.Mvc.TagHelpers*) bude k dispozici pro každý soubor zobrazení v *zobrazení* adresáře nebo podadresáře. První parametr po `@addTagHelper` určuje pomocných rutin značek k načtení (používáme "\*" pro všechny pomocných rutin značek), a druhý parametr "Microsoft.AspNetCore.Mvc.TagHelpers" Určuje sestavení, který obsahuje pomocné rutiny značek. *Microsoft.AspNetCore.Mvc.TagHelpers* je sestavení pro integrované pomocné rutiny značek základní technologie ASP.NET.

K vystavení všechny pomocných rutin značek v tomto projektu (která vytvoří sestavení s názvem *AuthoringTagHelpers*), můžete využít následující:

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=3)]

Pokud váš projekt obsahuje `EmailTagHelper` s výchozí obor názvů (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), můžete zadat plně kvalifikovaný název (FQN) pomocné rutiny značky:

```cshtml
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

Pomocné rutiny značky do zobrazení pomocí FQN, nejprve přidáte FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`) a potom název sestavení (*AuthoringTagHelpers*). Většina vývojářů dávají přednost používání "\*" syntaxe zástupných znaků. Syntaxe zástupných znaků umožňuje vložit zástupného znaku "\*" jako v FQN příponu. Například některé z následující direktivy přinese `EmailTagHelper`:

```cshtml
@addTagHelper AuthoringTagHelpers.TagHelpers.E*, AuthoringTagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.Email*, AuthoringTagHelpers
```

Jak už bylo zmíněno dříve, přidání `@addTagHelper` direktivu *Views/_ViewImports.cshtml* souboru zpřístupní pomocné rutiny značky na všechny soubory zobrazit v *zobrazení* adresáře a podadresářů. Můžete použít `@addTagHelper` direktiv v souborech konkrétního zobrazení, pokud chcete vyjádřit výslovný souhlas pro vystavení pomocné rutiny značky do jenom tato zobrazení.

<a name="remove-razor-directives-label"></a>

### <a name="removetaghelper-removes-tag-helpers"></a>`@removeTagHelper` Odebere pomocných rutin značek

`@removeTagHelper` Má dva stejné parametry jako `@addTagHelper`, a odebere pomocné rutiny značky, který byl dříve přidán. Například `@removeTagHelper` platí pro konkrétní zobrazení odebere zadané pomocné rutiny značky ze zobrazení. Pomocí `@removeTagHelper` v *Views/Folder/_ViewImports.cshtml* soubor odebere ze všech zobrazení v zadané pomocné rutiny značky *složky*.

### <a name="controlling-tag-helper-scope-with-the-viewimportscshtml-file"></a>Řízení pomocné rutiny značky oboru *_ViewImports.cshtml* souboru

Můžete přidat *_ViewImports.cshtml* do libovolné složky zobrazení a zobrazení, použije modul direktivy z obou tento soubor a *Views/_ViewImports.cshtml* souboru. Pokud jste přidali prázdná *Views/Home/_ViewImports.cshtml* souboru *Domů* zobrazení, by existovat žádná změna protože *_ViewImports.cshtml* soubor je sčítání. Žádné `@addTagHelper` direktivy, které přidáte do *Views/Home/_ViewImports.cshtml* souboru (, které nejsou ve výchozím *Views/_ViewImports.cshtml* souboru) by vystavit tyto pomocné rutiny značek k zobrazení pouze v *Domů* složky.

<a name="opt-out"></a>

### <a name="opting-out-of-individual-elements"></a>Výslovné odhlášení z jednotlivých prvků

Můžete zakázat pomocné rutiny značky na úrovni prvku odhlásit znakem pomocné rutiny značky ("!"). Například `Email` ověření je zakázané v `<span>` odhlásit znakem pomocné rutiny značky:

```cshtml
<!span asp-validation-for="Email" class="text-danger"></!span>
```

Musíte použít znak odhlásit pomocné rutiny značky pro počáteční a koncovou značku. (Editoru sady Visual Studio automaticky přidá znak výslovného nesouhlasu s uzavírací značku při přidejte jej do počáteční značka). Po přidání znak výslovného nesouhlasu s prvkem a pomocné rutiny značky atributy se už nezobrazují rozlišovací písmem.

<a name="prefix-razor-directives-label"></a>

### <a name="using-taghelperprefix-to-make-tag-helper-usage-explicit"></a>Pomocí `@tagHelperPrefix` provést explicitní použití pomocné rutiny značky

`@tagHelperPrefix` – Direktiva umožňuje zadat řetězec předpony značky k povolení podpory pomocné rutiny značky a aby explicitní použití pomocné rutiny značky. Můžete například přidat následující kód k *Views/_ViewImports.cshtml* souboru:

```cshtml
@tagHelperPrefix th:
```

Na následujícím obrázku kód předpony pomocné rutiny značky nastavena na `th:`, takže pouze elementy pomocí předpony `th:` podporují pomocné rutiny značek (pomocné rutiny značky povolena elementy mají zvláštní písma). `<label>` a `<input>` elementy mají předponu pomocné rutiny značky a povolenými pomocné rutiny značky při `<span>` není element.

![obrázek](intro/_static/thp.png)

Stejná pravidla hierarchie, které se vztahují `@addTagHelper` platí také pro `@tagHelperPrefix`.

## <a name="self-closing-tag-helpers"></a>Samouzavírací pomocných rutin značek

Mnoho pomocných rutin značek nelze použít jako samouzavírací značky. Několik pomocných rutin značek jsou navržené tak být samouzavírací značky. Použití pomocné rutiny značky, který nebyl navržen, aby se samouzavírací potlačí vykresleného výstupu. Samouzavírací pomocné rutiny značky výsledkem samouzavírací značky v vykresleného výstupu. Další informace najdete v tématu [tato poznámka](xref:mvc/views/tag-helpers/authoring#self-closing) v [vytváření pomocných rutin značek](xref:mvc/views/tag-helpers/authoring).

## <a name="intellisense-support-for-tag-helpers"></a>Podpora IntelliSense pro pomocné rutiny značek

Když vytvoříte novou webovou aplikaci ASP.NET Core v sadě Visual Studio, přidá balíček NuGet "Microsoft.AspNetCore.Razor.Tools". Toto je balíček, který přidá nástroje pomocné rutiny značky.

Zvažte vytvoření HTML `<label>` elementu. Jakmile zadáte `<l` IntelliSense v editoru sady Visual Studio zobrazí odpovídající prvky:

![obrázek](intro/_static/label.png)

Nejen že získáte Nápověda jazyka HTML, ale na ikonu ("@" symbol with "<>" je pod ním).

![obrázek](intro/_static/tagSym.png)

identifikuje prvek jako cílem pomocných rutin značek. Čistě prvky jazyka HTML (například `fieldset`) zobrazuje ikona "<>".

Prostý jazyk HTML `<label>` značky zobrazí značky HTML (s výchozí barevný motiv sady Visual Studio) Hnědý písmem, atributy červeně, a atribut hodnoty modrou barvu.

![obrázek](intro/_static/LableHtmlTag.png)

Po zadání `<label`, IntelliSense vypíše dostupné atributy HTML/CSS a atributy cílené pomocné rutiny značky:

![obrázek](intro/_static/labelattr.png)

Doplňování technologie IntelliSense umožňuje zadat klávesu tab k dokončení příkazu s vybranou hodnotu:

![obrázek](intro/_static/stmtcomplete.png)

Poté, co je zadán atribut pomocné rutiny značky, změňte značku a atribut písma. Použití výchozí sady Visual Studio "Blue" nebo "výraz Light" barevný motiv, je písmo tučné nachová. Pokud používáte "Tmavý" motiv je písmo tučné šedozelená. Obrázky v tomto dokumentu se nevytvořily pomocí výchozí motiv.

![obrázek](intro/_static/labelaspfor2.png)

Visual Studio můžete zadat *CompleteWord* místní (Ctrl + mezerník je [výchozí](/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio) do dvojitých uvozovek (""), a je nyní v jazyce C#, stejně jako by měly být v třída jazyka C#. Technologie IntelliSense zobrazí všechny metody a vlastnosti v modelu stránky. Metody a vlastnosti jsou k dispozici, protože je vlastnost typu `ModelExpression`. Na obrázku níže, můžu jsem úpravy `Register` zobrazení, takže `RegisterViewModel` je k dispozici.

![obrázek](intro/_static/intellemail.png)

Technologie IntelliSense jsou uvedené vlastnosti a metody dostupné pro model na stránce. Bohaté prostředí IntelliSense vám pomůže vybrat třídu šablony stylů CSS:

![obrázek](intro/_static/iclass.png)

![obrázek](intro/_static/intel3.png)

## <a name="tag-helpers-compared-to-html-helpers"></a>Pomocných rutin značek ve srovnání s pomocných rutin HTML

Pomocné rutiny značek připojit k elementů HTML v zobrazení syntaxe Razor, zatímco [pomocných rutin HTML](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) jsou vyvolány jako metody spolu s HTML v zobrazení syntaxe Razor. Vezměte v úvahu následující kód Razor, která vytvoří popisek HTML pomocí třídy šablony stylů CSS "titulek":

```cshtml
@Html.Label("FirstName", "First Name:", new {@class="caption"})
```

Na (`@`) symbol říká Razor, je toto začátek kódu. Následující dva parametry ("FirstName" a "jméno:") jsou řetězce, takže [IntelliSense](/visualstudio/ide/using-intellisense) nemůže pomoct. Poslední argument:

```cshtml
new {@class="caption"}
```

Je anonymní objekt používá k reprezentování atributy. Protože `class` je vyhrazené klíčové slovo v C#, můžete použít `@` symbolů pro vynucení C# interpretovat `@class=` jako symbol (název vlastnosti). Pro front-endu návrháře (někdo zkušenosti s HTML/CSS a JavaScript a dalších technologií klienta, ale nejsou obeznámeni s C# a Razor), většina řádku je cizí. Celý řádek musí být vytvořen žádný pomocí technologie IntelliSense.

Použití `LabelTagHelper`, stejné značky lze zapsat jako:

```cshtml
<label class="caption" asp-for="FirstName"></label>
```

Pomocná rutina značky verzi, jakmile zadáte `<l` IntelliSense v editoru sady Visual Studio zobrazí odpovídající prvky:

![obrázek](intro/_static/label.png)

Technologie IntelliSense umožňuje napsat celý řádek.

Následující kód obrázek ukazuje část formuláře *Views/Account/Register.cshtml* zobrazení Razor vygenerovaná z této šablony MVC ASP.NET 4.5.x součástí sady Visual Studio.

![obrázek](intro/_static/regCS.png)

Editor sady Visual Studio zobrazuje šedé pozadí kód C#. Například `AntiForgeryToken` pomocné rutiny HTML:

```cshtml
@Html.AntiForgeryToken()
```

Zobrazí se šedé pozadí. Většina kódu v registru zobrazení je C#. Porovnejte ji s ekvivalentní postup použití pomocných rutin značek:

![obrázek](intro/_static/regTH.png)

Kód je mnohem přehlednější a čitelnější, upravit a udržovat než pomocných rutin HTML. Kód jazyka C# je snížit na minimum, kterou je potřeba vědět o serveru. Editor sady Visual Studio zobrazí značky cílem pomocné rutiny značky rozlišovací písmem.

Vezměte v úvahu *e-mailu* skupiny:

[!code-csharp[](intro/sample/Register.cshtml?range=12-18)]

Každý atribut "asp-" má hodnotu "Email", ale není řetězce "E-mail". V tomto kontextu "Email" je C# model výrazu vlastnost `RegisterViewModel`.

Editor sady Visual Studio vám pomůže psát **všechny** značek v pomocné rutiny značky přístup register formuláře, zatímco Visual Studio poskytuje většinu kódu v metodě pomocných rutin HTML není žádná nápověda. [Podpora IntelliSense pro pomocné rutiny značek](#intellisense-support-for-tag-helpers) obsahuje podrobnosti o práci s pomocných rutin značek v editoru sady Visual Studio.

## <a name="tag-helpers-compared-to-web-server-controls"></a>Pomocných rutin značek ve srovnání s ovládací prvky webového serveru

* Pomocné rutiny značek nevlastníte prvek, na který jste spojené s; jednoduše účast při vykreslování element a obsahu. ASP.NET [ovládací prvky webového serveru](https://msdn.microsoft.com/library/7698y1f0.aspx) deklarovány a vyvolána na stránce.

* [Ovládací prvky webového serveru](https://msdn.microsoft.com/library/zsyt68f1.aspx) mají netriviální životní cyklus, který může ztěžovat vývoji a ladění.

* Ovládací prvky webového serveru umožňují přidat funkce na prvky Document Object Model (DOM) klient pomocí ovládacího prvku klienta. Pomocné rutiny značek mít žádné modelu DOM.

* Ovládací prvky webového serveru zahrnují automatickou detekci prohlížeče. Pomocné rutiny značek nemají žádné informace o prohlížeči.

* Několik pomocných rutin značek může fungovat u stejného elementu (naleznete v tématu [vyhnout pomocné rutiny značky konfliktů](xref:mvc/views/tag-helpers/authoring#avoid-tag-helper-conflicts) ) zatímco obvykle nelze vytvořit ovládací prvky webového serveru.

* Můžete upravit značky a obsah prvků HTML, která jsou omezená na pomocných rutin značek, ale nemusíte upravovat přímo cokoli, na stránce. Ovládací prvky webového serveru mají méně konkrétní rozsah a můžete provádět akce, které ovlivňují ostatní části stránky; povolení nezamýšlenými vedlejšími účinky.

* Ovládací prvky webového serveru pomocí převaděče typů pro převod řetězců na objekty. Pomocné rutiny značek můžete pracovat nativně v jazyce C#, takže není nutné pro převod typu.

* Použijte ovládací prvky webového serveru [System.ComponentModel](/dotnet/api/system.componentmodel) k implementaci chování za běhu a návrhu komponent a ovládacích prvků. `System.ComponentModel` obsahuje základní třídy a rozhraní pro implementaci atributy a převaděče typů vazeb na zdroje dat a součásti licencování. Kontrast, který pomocných rutin značek, které obvykle jsou odvozeny z `TagHelper`a `TagHelper` základní třída poskytuje pouze dvě metody `Process` a `ProcessAsync`.

## <a name="customizing-the-tag-helper-element-font"></a>Přizpůsobení písma elementu pomocné rutiny značky

Můžete přizpůsobit písma a barevné zvýraznění z **nástroje** > **možnosti** > **prostředí** > **písma a barvy**:

![obrázek](intro/_static/fontoptions2.png)

[!INCLUDE[](~/includes/built-in-TH.md)]

## <a name="additional-resources"></a>Další zdroje

* [Autor pomocných rutin značek](xref:mvc/views/tag-helpers/authoring)
* [Práce s formuláři](xref:mvc/views/working-with-forms)
* [TagHelperSamples na Githubu](https://github.com/dpaquette/TagHelperSamples) obsahuje pomocné rutiny značky ukázky pro práci s [Bootstrap](https://getbootstrap.com/).

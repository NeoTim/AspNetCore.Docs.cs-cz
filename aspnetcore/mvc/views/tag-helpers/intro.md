---
title: Tagování pomocníků v ASP.NET Core
author: rick-anderson
description: Přečtěte si, co jsou pomocníky značek a jak je používat v ASP.NET Core.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 03/18/2019
uid: mvc/views/tag-helpers/intro
ms.openlocfilehash: 15f94fd1c619e9f69c5783f664eafc9ca28f86f9
ms.sourcegitcommit: 8157e5a351f49aeef3769f7d38b787b4386aad5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74239861"
---
# <a name="tag-helpers-in-aspnet-core"></a>Tagování pomocníků v ASP.NET Core

Od [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="what-are-tag-helpers"></a>Co jsou pomocníky značek

Pomáhat pomocníkům při vytváření kódu na straně serveru pro účast v vytváření a vykreslování prvků HTML v souborech Razor. Například integrovaná `ImageTagHelper` může k názvu image připojit číslo verze. Pokaždé, když se obrázek změní, server vygeneruje pro Image novou jedinečnou verzi, takže klienti budou mít zaručený přístup k aktuální imagi (místo k zastaralému obrázku v mezipaměti). K dispozici je mnoho vestavěných pomocníků značek pro běžné úkoly, jako je vytváření formulářů, odkazů, načítání assetů a ještě více dostupných ve veřejných úložištích GitHub a jako balíčky NuGet. Pomocníky značek jsou vytvořeny v C#a jsou cíleny na prvky HTML na základě názvu elementu, názvu atributu nebo nadřazené značky. Například integrovaná `LabelTagHelper` mohou cílit na prvek `<label>` HTML při použití atributů `LabelTagHelper`. Pokud jste obeznámeni s [pomocníky HTML](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers), značky pomocníků značek omezují explicitní přechody mezi HTML C# a v zobrazení Razor. V mnoha případech pomocné rutiny HTML poskytují alternativní přístup k konkrétní pomocné rutině značek, ale je důležité rozpoznat, že pomocné rutiny značky nenahrazují nápovědu HTML a že pro každou pomocnou nápovědu HTML není k dispozici pomocný pomocník značek. [Pomocník značek v porovnání s pomocníky HTML](#tag-helpers-compared-to-html-helpers) vysvětluje rozdíly podrobněji.

## <a name="what-tag-helpers-provide"></a>Co poskytují pomocníky značek

**Prostředí pro vývoj s uživatelsky přívětivými HTML** Ve většině případů se značky Razor pomocí značek tag hledají jako standardní HTML. Návrháři front-end Conversant s HTML/CSS/JavaScript může upravovat Razor bez učení C# syntaxe Razor.

**Bohatý prostředí IntelliSense pro vytváření značek HTML a Razor** To je ostré kontrast vůči pomocníkům HTML, předchozí přístup k vytváření značek v zobrazeních Razor na straně serveru. [Pomocník značek v porovnání s pomocníky HTML](#tag-helpers-compared-to-html-helpers) vysvětluje rozdíly podrobněji. [Podpora technologie IntelliSense pro pomocníci značek](#intellisense-support-for-tag-helpers) vysvětluje prostředí IntelliSense. I vývojáři, kteří se C# setkali se syntaxí Razor, mají vyšší produktivitu pomocí pomocníků značek než psaní C# značek Razor.

**Způsob, jak zvýšit produktivitu a umožnit vám poskytovat robustnější, spolehlivý a udržovatelný kód s využitím informací, které jsou k dispozici pouze na serveru** Například historicky heslo při aktualizaci imagí bylo změnit název obrázku při změně obrázku. Obrázky by měly být agresivní v mezipaměti z důvodu výkonu, a pokud nezměníte název obrázku, riskujete, že klienti získávají starou kopii. V minulosti se po úpravě obrázku změnil název a každý odkaz na obrázek ve webové aplikaci, který je potřeba aktualizovat. Nejenom je to velmi náročné na práci, je to také náchylné k chybám (může se stát, že nechtěně zadáte špatný řetězec atd.). Integrovaná `ImageTagHelper` to může udělat automaticky. `ImageTagHelper` může k názvu image připojit číslo verze, takže když se obrázek změní, server automaticky vygeneruje novou jedinečnou verzi image. Klientům je zaručeno získat aktuální bitovou kopii. Tato odolnost a úspory na práci jsou v podstatě bezplatné pomocí `ImageTagHelper`.

Většina vestavěných pomocníků značek cílí na standardní prvky HTML a poskytují atributy na straně serveru pro element. Například prvek `<input>` použitý v mnoha zobrazeních ve složce *views/Account* obsahuje atribut `asp-for`. Tento atribut extrahuje název zadané vlastnosti modelu do vykresleného HTML. Zvažte zobrazení Razor s následujícím modelem:

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

Následující značky Razor:

```cshtml
<label asp-for="Movie.Title"></label>
```

Generuje následující kód HTML:

```html
<label for="Movie_Title">Title</label>
```

Atribut `asp-for` je zpřístupněn vlastností `For` v [LabelTagHelper](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.labeltaghelper?view=aspnetcore-2.0). Další informace najdete v tématu [autora značek pomocníka](xref:mvc/views/tag-helpers/authoring) .

## <a name="managing-tag-helper-scope"></a>Správa oboru pomocníka značek

Obor pomocníků značek je ovládán kombinací `@addTagHelper`, `@removeTagHelper`a znaku pro odhlášení "!".

<a name="add-helper-label"></a>

### <a name="addtaghelper-makes-tag-helpers-available"></a>`@addTagHelper` zpřístupňuje pomocníky značek.

Pokud vytvoříte novou ASP.NET Core webovou aplikaci s názvem *AuthoringTagHelpers*, budou do projektu přidány následující soubory *zobrazení nebo _ViewImports. cshtml* :

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=2&range=2-3)]

Direktiva `@addTagHelper` zpřístupňuje k zobrazení pomocníky značek. V tomto případě je soubor zobrazení *stránky/_ViewImports. cshtml*, který je ve výchozím nastavení děděn všemi soubory ve složce a složkách *stránky* . zpřístupňují se pomocníkům značek. Výše uvedený kód používá syntaxi zástupných znaků ("\*") k určení, že všechny pomocníky značek v zadaném sestavení (*Microsoft. AspNetCore. Mvc. TagHelpers*) budou k dispozici pro každý soubor zobrazení v adresáři *zobrazení* nebo v podadresáři. První parametr po `@addTagHelper` určuje, které pomocníky značek se mají načíst (pro všechny pomocníky značka používáme "\*") a druhý parametr "Microsoft. AspNetCore. Mvc. TagHelpers" Určuje sestavení obsahující pomocníka značek. *Microsoft. AspNetCore. Mvc. TagHelpers* je sestavení pro předdefinované ASP.NET Core pomocníka značek.

Chcete-li zveřejnit všechny pomocníky značek v tomto projektu (který vytvoří sestavení s názvem *AuthoringTagHelpers*), použijte následující:

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=3)]

Pokud projekt obsahuje `EmailTagHelper` s výchozím oborem názvů (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), můžete zadat plně kvalifikovaný název (FQN) pomocné rutiny značky:

```cshtml
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

Chcete-li přidat pomocníka značek pro zobrazení pomocí FQN, nejprve přidejte FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`) a potom název sestavení (*AuthoringTagHelpers*). Většina vývojářů upřednostňuje použití syntaxe zástupných znaků "\*". Syntaxe zástupných znaků umožňuje vložit zástupný znak "\*" jako příponu do FQN. Do `EmailTagHelper`například přijde kterákoli z následujících direktiv:

```cshtml
@addTagHelper AuthoringTagHelpers.TagHelpers.E*, AuthoringTagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.Email*, AuthoringTagHelpers
```

Jak již bylo zmíněno dříve, přidání direktivy `@addTagHelper` do souboru views */_ViewImports. cshtml* zpřístupní pomocníka značek pro všechny soubory zobrazení v adresáři *zobrazení* a podadresářích. Direktivu `@addTagHelper` můžete použít pro konkrétní soubory zobrazení, pokud se chcete přihlásit k vystavení pomocníka značek jenom pro zobrazení.

<a name="remove-razor-directives-label"></a>

### <a name="removetaghelper-removes-tag-helpers"></a>`@removeTagHelper` odebere pomocníky značek.

`@removeTagHelper` má stejné dva parametry jako `@addTagHelper`a odebere pomocníka značek, který byl dříve přidán. Například `@removeTagHelper` aplikované na konkrétní zobrazení odebere ze zobrazení určenou pomocnou značku. Použití `@removeTagHelper` v souboru *views/Folder/_ViewImports. cshtml* odebere ze všech zobrazení ve *složce*určenou pomocnou značku.

### <a name="controlling-tag-helper-scope-with-the-_viewimportscshtml-file"></a>Řízení oboru pomocné rutiny značek pomocí souboru *_ViewImports. cshtml*

*_ViewImports. cshtml* můžete přidat do libovolné složky zobrazení a modul zobrazení použije direktivy z obou souborů i ze souboru *views/_ViewImports. cshtml* . Pokud jste přidali prázdné *zobrazení/soubor _ViewImports. cshtml* pro *domovskou* zobrazení, nebude by žádná změna, protože *_ViewImports soubor. cshtml* je aditivní. Jakékoli direktivy `@addTagHelper`, které přidáte do souboru *views/Home/_ViewImports. cshtml* (které nejsou ve výchozích *zobrazeních _ViewImports nebo souboru. cshtml* ), by vystavily tyto pomocníky značek pro zobrazení pouze v *domovské* složce.

<a name="opt-out"></a>

### <a name="opting-out-of-individual-elements"></a>Zrušení individuálních prvků

Pomocníka značek můžete zakázat na úrovni elementu pomocí znaku pro výslovný odkaz na značku ("!"). Například `Email` ověřování je v `<span>` zakázáno pomocí znaku pro výslovný souhlas s pomocníkem značek:

```cshtml
<!span asp-validation-for="Email" class="text-danger"></!span>
```

Pro otevírací a uzavírací značku musíte použít znak výslovného souhlasu s tagem. (Editor sady Visual Studio automaticky přidá znak pro odhlášení do uzavírací značky, když ho přidáte do počáteční značky). Po přidání znaku pro odhlášení se prvky a pomocné atributy značky již nebudou zobrazovat v charakteristickém písmu.

<a name="prefix-razor-directives-label"></a>

### <a name="using-taghelperprefix-to-make-tag-helper-usage-explicit"></a>Použití `@tagHelperPrefix` k nastavení explicitního použití pomocníka značek

Direktiva `@tagHelperPrefix` umožňuje zadat řetězec předpony značky pro povolení podpory značek a nastavit explicitní použití pomocníka značek. Například můžete přidat následující kód do souboru *views/_ViewImports. cshtml* :

```cshtml
@tagHelperPrefix th:
```

V níže uvedeném obrázku kódu je předpona pomocníka značek nastavena na hodnotu `th:`, takže pouze ty prvky, které používají předponu `th:` podporují pomocné rutiny značek (značky s povoleným pomocníkem značek mají charakteristické písmo). Prvky `<label>` a `<input>` mají předponu pomocné rutiny značky a jsou povolené příznakem pomocníka značek, zatímco `<span>` prvek ne.

![image](intro/_static/thp.png)

Stejná pravidla hierarchie, která platí pro `@addTagHelper`, se vztahují také na `@tagHelperPrefix`.

## <a name="self-closing-tag-helpers"></a>Samoobslužné pomocníky značek

Mnoho pomocníků značek se nedá použít jako samouzavírací značky. Některé pomocníky s značkami jsou navržené tak, aby se samočinně uzavírající značky. Použití pomocníka značek, který nebyl navržen jako samouzavírací, potlačí Vykreslený výstup. Samostatný uzavírací pomocník značek má za následek samouzavírací značku ve vykresleném výstupu. Další informace najdete v [této poznámce](xref:mvc/views/tag-helpers/authoring#self-closing) při [vytváření pomocníků značek](xref:mvc/views/tag-helpers/authoring).

## <a name="c-in-tag-helpers-attributedeclaration"></a>C#atribut/deklarace v Pomocníkech značek 

Pomocníky značek neumožňují C# v oblasti atributu nebo deklarace značky. Například následující kód není platný:

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

Když vytvoříte novou ASP.NET Core webovou aplikaci v aplikaci Visual Studio, přidá balíček NuGet "Microsoft. AspNetCore. Razor. Tools". Toto je balíček, který přidává nástroje pomocníka značek.

Zvažte zápis elementu `<label>` HTML. Jakmile zadáte `<l` v editoru sady Visual Studio, IntelliSense zobrazí vyhovující prvky:

![image](intro/_static/label.png)

Nejenom získáte nápovědu HTML, ale také ikonu (@" symbol with "< > pod ní).

![image](intro/_static/tagSym.png)

identifikuje element jako cílený pomocníky značek. Čistě prvky HTML (například `fieldset`) zobrazí ikonu "< >".

Prázdná značka HTML `<label>` zobrazuje značku jazyka HTML (s výchozím barevným motivem sady Visual Studio) v hnědém písmu, atributy červeně a hodnoty atributu modrou.

![image](intro/_static/LableHtmlTag.png)

Po zadání `<label`IntelliSense zobrazí seznam dostupných atributů HTML/CSS a atributy cílené na pomocnou značku:

![image](intro/_static/labelattr.png)

Dokončení příkazu IntelliSense umožňuje zadat klávesu TAB k dokončení příkazu s vybranou hodnotou:

![image](intro/_static/stmtcomplete.png)

Jakmile je atribut pomocník značek zadán, změní se písma značky a atributu. Když použijete výchozí barevný motiv sady Visual Studio "Blue" nebo "Light", písmo je tučně fialové. Pokud používáte motiv "tmavý", písmo je tučně šedozelená. Obrázky v tomto dokumentu byly pořízeny pomocí výchozího motivu.

![image](intro/_static/labelaspfor2.png)

Můžete zadat zástupce sady Visual Studio *CompleteWord* (CTRL + MEZERNÍK je [Výchozí hodnota](/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio) uvnitř dvojitých uvozovek ("") a teď jste v C#, stejně jako byste byli ve C# třídě. IntelliSense zobrazí všechny metody a vlastnosti v modelu stránky. Metody a vlastnosti jsou k dispozici, protože typ vlastnosti je `ModelExpression`. Na obrázku níže upravujeme zobrazení `Register`, takže `RegisterViewModel` k dispozici.

![image](intro/_static/intellemail.png)

IntelliSense zobrazí seznam vlastností a metod, které jsou k dispozici pro model na stránce. Bohatá prostředí IntelliSense vám pomůže vybrat třídu CSS:

![image](intro/_static/iclass.png)

![image](intro/_static/intel3.png)

## <a name="tag-helpers-compared-to-html-helpers"></a>Tagování pomocníků v porovnání s pomocníky HTML

Přihlaste se k prvkům jazyka HTML v zobrazeních Razor pomocí značek, zatímco jsou v zobrazení Razor vyvolány [pomocníky HTML](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) jako metody prokládáné pomocí HTML. Vezměte v úvahu následující značky Razor, které vytvoří popisek HTML s nadpisem CSS třídy "titulek":

```cshtml
@Html.Label("FirstName", "First Name:", new {@class="caption"})
```

Symbol na pozici (`@`) oznamuje syntaxi Razor, jedná se o začátek kódu. Následující dva parametry ("FirstName" a "first name": ") jsou řetězce, takže [IntelliSense](/visualstudio/ide/using-intellisense) nemůže pomáhat. Poslední argument:

```cshtml
new {@class="caption"}
```

Je anonymní objekt, který slouží k reprezentaci atributů. Protože `class` je rezervované klíčové slovo v C#, použijete symbol `@` k vynucení C# interpretace `@class=` jako symbolu (název vlastnosti). Do předběžného návrháře (někoho, kdo se seznámil s HTML/CSS/JavaScriptem a jinými klientskými technologiemi, ale nemá zkušenosti C# a Razor), je většina řádku cizí. Celý řádek musí být vytvořen bez usnadnění z IntelliSense.

Pomocí `LabelTagHelper`lze zapsat stejné označení jako:

```cshtml
<label class="caption" asp-for="FirstName"></label>
```

Po zadání `<l` v editoru sady Visual Studio s použitím pomocné verze značky IntelliSense zobrazí stejné prvky:

![image](intro/_static/label.png)

Technologie IntelliSense vám pomůže napsat celý řádek.

Následující obrázek kódu zobrazuje část formuláře zobrazení */účet/Register. cshtml* Razor, která je vygenerovaná ze šablony ASP.NET 4.5. x MVC, která je součástí sady Visual Studio.

![image](intro/_static/regCS.png)

Editor sady Visual Studio zobrazí C# kód s šedým pozadím. Například Pomocník pro `AntiForgeryToken` HTML:

```cshtml
@Html.AntiForgeryToken()
```

se zobrazí se šedým pozadím. Většina značek v zobrazení registrů je C#. Porovnejte je s ekvivalentním přístupem pomocí pomocníků značek:

![image](intro/_static/regTH.png)

Značky jsou mnohem čisticí a snadněji se čtou, upravují a udržují než přístup k HTML pomocníkům. C# Kód se zmenší na minimum, o které server potřebuje znát. V editoru sady Visual Studio se zobrazí označení cílené pomocí pomocníka značek v rozlišujícím písmu.

Vezměte v úvahu tuto *e-mailovou* skupinu:

[!code-csharp[](intro/sample/Register.cshtml?range=12-18)]

Každý z atributů ASP-má hodnotu "E-mail", ale "E-mail" není řetězec. V tomto kontextu je "E-mail" vlastnost výrazu C# modelu pro `RegisterViewModel`.

Editor sady Visual Studio vám pomůže napsat **všechny** značky v pomocné metodě příznaku značky ve formuláři registru, zatímco Visual Studio neposkytuje nápovědu pro většinu kódu v přístupu k HTML pomocníkům. [Podpora technologie IntelliSense pro pomocné pomocníky značek](#intellisense-support-for-tag-helpers) se dohlíží na podrobnosti o práci s pomocníky značek v editoru sady Visual Studio.

## <a name="tag-helpers-compared-to-web-server-controls"></a>Porovnávání pomocníků značek v porovnání s ovládacími prvky webového serveru

* Pomocníka značek nevlastní prvek, ke kterému jsou přidruženy; jednoduše se účastní vykreslování prvku a obsahu. [Ovládací prvky webového serveru](https://msdn.microsoft.com/library/7698y1f0.aspx) ASP.NET jsou deklarovány a vyvolány na stránce.

* [Ovládací prvky webového serveru](https://msdn.microsoft.com/library/zsyt68f1.aspx) mají netriviální životní cyklus, který může provádět vývoj a ladění obtížné.

* Ovládací prvky webového serveru umožňují přidat funkce do prvků model DOM (Document Object Model) klienta (DOM) pomocí ovládacího prvku klienta. Pomocník značek nemá žádný model DOM.

* Ovládací prvky webového serveru zahrnují automatické zjišťování prohlížečů. Pomocník značek nemá žádné znalosti o prohlížeči.

* Více pomocných rutin značek může působit na stejný prvek (viz téma [zamezení konfliktů pomocníka značek](xref:mvc/views/tag-helpers/authoring#avoid-tag-helper-conflicts) ), zatímco obvykle nelze vytvářet ovládací prvky webového serveru.

* Pomocník značek může upravit značku a obsah prvků HTML, na které jsou vymezeny, ale přímo na stránku nemění nic jiného. Ovládací prvky webového serveru mají méně konkrétní rozsah a mohou provádět akce, které mají vliv na ostatní části stránky. povolení nezamýšlených vedlejších účinků.

* Ovládací prvky webového serveru používají k převodu řetězců do objektů převaděče typů. S pomocníky značek budete nativně pracovat v C#, takže nemusíte provádět převod typu.

* Ovládací prvky webového serveru používají [System. ComponentModel](/dotnet/api/system.componentmodel) k implementaci chování komponent a ovládacích prvků za běhu a v době návrhu. `System.ComponentModel` zahrnuje základní třídy a rozhraní pro implementaci atributů a převaděčů typů, vázání na zdroje dat a součásti licencování. Na rozdíl od toho, že chcete označit pomocníky, které jsou obvykle odvozeny od `TagHelper`a `TagHelper` základní třídy zpřístupňují pouze dvě metody, `Process` a `ProcessAsync`.

## <a name="customizing-the-tag-helper-element-font"></a>Přizpůsobení písma prvku pomocné značky

Písma a barvy můžete přizpůsobit pomocí **nástrojů** > **možností** > **prostředí** > **písma a barvy**:

![image](intro/_static/fontoptions2.png)

[!INCLUDE[](~/includes/built-in-TH.md)]

## <a name="additional-resources"></a>Další zdroje informací:

* [Vytváření pomocníků se značkami](xref:mvc/views/tag-helpers/authoring)
* [Práce s formuláři](xref:mvc/views/working-with-forms)
* [TagHelperSamples na GitHubu](https://github.com/dpaquette/TagHelperSamples) obsahuje ukázky pomocníka značek pro práci s [bootstrap](https://getbootstrap.com/).

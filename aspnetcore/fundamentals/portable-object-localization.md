---
title: Konfigurace lokalizace přenosných objektů v ASP.NET Jádra
author: sebastienros
description: Tento článek představuje soubory přenosných objektů a popisuje kroky pro jejich použití v ASP.NET základní aplikaci s architekturou Orchard Core.
ms.author: scaddie
ms.date: 09/26/2017
uid: fundamentals/portable-object-localization
ms.openlocfilehash: 08002564eb68bc04eebaeafed560202d0d69958a
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656189"
---
# <a name="configure-portable-object-localization-in-aspnet-core"></a>Konfigurace lokalizace přenosných objektů v ASP.NET Jádra

Podle [Sébastien Ros](https://github.com/sebastienros) a [Scott Addie](https://twitter.com/Scott_Addie)

Tento článek vás provede kroky pro použití souborů přenosného objektu (PO) v ASP.NET základní aplikace s architekturou [Orchard Core.](https://github.com/OrchardCMS/OrchardCore)

**Poznámka:** Orchard Core není produkt společnosti Microsoft. V důsledku toho společnost Microsoft neposkytuje žádnou podporu pro tuto funkci.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="what-is-a-po-file"></a>Co je soubor PO?

Soubory PO jsou distribuovány jako textové soubory obsahující přeložené řetězce pro daný jazyk. Mezi výhody použití souborů PO místo *.resx* patří:
- Soubory PO podporují pluralizaci; *Soubory RESX* nepodporují pluralizaci.
- Soubory PO nejsou kompilovány jako soubory *RESX.* Jako takové nejsou vyžadovány specializované nástroje a kroky sestavení.
- PO soubory dobře pracovat s kolaborativní on-line editační nástroje.

### <a name="example"></a>Příklad

Zde je ukázkový soubor PO obsahující překlad pro dva řetězce ve francouzštině, včetně jednoho s množným číslem:

*fr.po*

```text
#: Services/EmailService.cs:29
msgid "Enter a comma separated list of email addresses."
msgstr "Entrez une liste d'emails séparés par une virgule."

#: Views/Email.cshtml:112
msgid "The email address is \"{0}\"."
msgid_plural "The email addresses are \"{0}\"."
msgstr[0] "L'adresse email est \"{0}\"."
msgstr[1] "Les adresses email sont \"{0}\""
```

Tento příklad používá následující syntaxi:

- `#:`: Komentář označující kontext řetězce, který má být přeložen. Stejný řetězec může být přeložen odlišně v závislosti na tom, kde se používá.
- `msgid`: Nepřeložený řetězec.
- `msgstr`: Přeložený řetězec.

V případě podpory pluralizace lze definovat více položek.

- `msgid_plural`: Nepřeložený řetězec množného čísla.
- `msgstr[0]`: Přeložený řetězec pro případ 0.
- `msgstr[N]`: Přeložený řetězec pro případ N.

Specifikace souboru PO naleznete [zde](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).

## <a name="configuring-po-file-support-in-aspnet-core"></a>Konfigurace podpory souborů PO v ASP.NET Core

Tento příklad je založen na aplikaci ASP.NET Core MVC generované ze šablony projektu Sady Visual Studio 2017.

### <a name="referencing-the-package"></a>Odkazování na balíček

Přidejte odkaz `OrchardCore.Localization.Core` na balíček NuGet. Je k dispozici na [MyGet](https://www.myget.org/) na následující zdroj balíčku:https://www.myget.org/F/orchardcore-preview/api/v3/index.json

Soubor *.csproj* nyní obsahuje řádek podobný následujícímu (číslo verze se může lišit):

[!code-xml[](localization/sample/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a>Registrace služby

Přidejte požadované služby k metodě `ConfigureServices` *Startup.cs*:

[!code-csharp[](localization/sample/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

Přidejte požadovaný middleware `Configure` k metodě *Startup.cs*:

[!code-csharp[](localization/sample/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

Přidejte do svého zvoleného zobrazení Razor následující kód. *About.cshtml* se používá v tomto příkladu.

[!code-cshtml[](localization/sample/POLocalization/Views/Home/About.cshtml)]

Instance `IViewLocalizer` je injektována a slouží k překladu textu "Hello world!".

### <a name="creating-a-po-file"></a>Vytvoření souboru PO

Vytvořte soubor s názvem * \<kód jazykové verze>.po* v kořenové složce aplikace. V tomto příkladu je název souboru *fr.po,* protože se používá francouzský jazyk:

[!code-text[](localization/sample/POLocalization/fr.po)]

Tento soubor ukládá řetězec přeložit a francouzský přeložený řetězec. Překlady vrátit do jejich nadřazené jazykové verze, v případě potřeby. V tomto příkladu se soubor *fr.po* používá, pokud je `fr-FR` požadovaná jazyková verze nebo `fr-CA`.

### <a name="testing-the-application"></a>Testování aplikace

Spusťte aplikaci a `/Home/About`přejděte na adresu URL . Text **Hello world!** se zobrazí.

Přejděte na `/Home/About?culture=fr-FR`adresu URL . Text **Bonjour le monde!** se zobrazí.

## <a name="pluralization"></a>Pluralizace

Soubory PO podporují pluralizační formuláře, což je užitečné, když stejný řetězec musí být přeložen odlišně na základě mohutnosti. Tento úkol je komplikován skutečností, že každý jazyk definuje vlastní pravidla pro výběr řetězce, který má být používán na základě mohutnosti.

Balíček orchard lokalizace poskytuje rozhraní API pro automatické vyvolání těchto různých formulářů množného čísla.

### <a name="creating-pluralization-po-files"></a>Vytváření pluralizačních souborů PO

Přidejte do výše uvedeného souboru *fr.po* následující obsah:

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

Viz [Co je soubor PO?](#what-is-a-po-file) vysvětlení, co každá položka v tomto příkladu představuje.

### <a name="adding-a-language-using-different-pluralization-forms"></a>Přidání jazyka pomocí různých pluralizačních formulářů

V předchozím příkladu byly použity anglické a francouzské řetězce. Angličtina a francouzština mají pouze dvě formy pluralizace a sdílejí stejná pravidla formuláře, což je, že mohutnost jednoho je mapována na první množné číslo formuláře. Všechny ostatní mohutnost je mapována na druhý množný formulář.

Ne všechny jazyky sdílejí stejná pravidla. To je ilustrováno českým jazykem, který má tři plurální formy.

Vytvořte `cs.po` soubor následujícím způsobem a všimněte si, jak pluralizace potřebuje tři různé překlady:

[!code-text[](localization/sample/POLocalization/cs.po)]

Chcete-li přijmout české `"cs"` lokalizace, přidejte do `ConfigureServices` seznamu podporovaných kultur v metodě:

```csharp
var supportedCultures = new List<CultureInfo>
{
    new CultureInfo("en-US"),
    new CultureInfo("en"),
    new CultureInfo("fr-FR"),
    new CultureInfo("fr"),
    new CultureInfo("cs")
};
```

Upravte soubor *Zobrazení/Domů/About.cshtml* tak, aby vykresloval lokalizované řetězce v množném čísle pro několik kardinálit:

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

**Poznámka:** V reálném scénáři by proměnná použít k reprezentaci počtu. Zde opakujeme stejný kód se třemi různými hodnotami, abychom odhalili velmi specifický případ.

Při přepínání kultur se zobrazí následující:

Příkaz `/Home/About`:

```html
There is one item.
There are 2 items.
There are 5 items.
```

Příkaz `/Home/About?culture=fr`:

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

Příkaz `/Home/About?culture=cs`:

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

Všimněte si, že pro českou kulturu jsou tři překlady odlišné. Francouzská a anglická kultura sdílejí stejnou konstrukci pro dva poslední přeložené řetězce.

## <a name="advanced-tasks"></a>Pokročilé úlohy

### <a name="contextualizing-strings"></a>Kontextové řetězce

Aplikace často obsahují řetězce, které mají být přeloženy na několika místech. Stejný řetězec může mít jiný překlad v určitých umístěních v rámci aplikace (Razor zobrazení nebo soubory tříd). Soubor PO podporuje pojem kontextu souboru, který lze použít ke kategorizaci řetězce, který je reprezentován. Pomocí kontextu souboru řetězec lze přeložit odlišně, v závislosti na kontextu souboru (nebo nedostatek kontextu souboru).

Služby lokalizace PO používají název celé třídy nebo zobrazení, které se používá při překladu řetězce. Toho lze dosáhnout nastavením hodnoty `msgctxt` v položce.

Vezměme si menší přírůstek k předchozímu příkladu *fr.po.* Zobrazení Razor umístěné na *adrese Views/Home/About.cshtml* lze definovat jako `msgctxt` kontext souboru nastavením hodnoty rezervované položky:

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

S `msgctxt` sadou jako takovou dochází k překladu textu při navigaci na . `/Home/About?culture=fr-FR` K překladu nedojde při navigaci `/Home/Contact?culture=fr-FR`do aplikace .

Pokud žádná konkrétní položka je uzavřeno s daným kontextem souboru, Orchard Core záložní mechanismus hledá odpovídající soubor PO bez kontextu. Za předpokladu, že pro *zobrazení/Home/Contact.cshtml*není definován `/Home/Contact?culture=fr-FR` žádný konkrétní kontext souboru , načte se soubor PO, například:

[!code-text[](localization/sample/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a>Změna umístění souborů PO

Výchozí umístění souborů PO lze `ConfigureServices`změnit v :

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

V tomto příkladu jsou soubory PO načteny ze složky *Lokalizace.*

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a>Implementace vlastní logiky pro hledání lokalizačních souborů

Pokud je k vyhledání souborů `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` nákupních opojení zapotřebí složitější logika, může být rozhraní implementováno a zaregistrováno jako služba. To je užitečné, když soubory PO mohou být uloženy v různých umístěních nebo když soubory musí být nalezeny v hierarchii složek.

### <a name="using-a-different-default-pluralized-language"></a>Použití jiného výchozího pluralitního jazyka

Balíček obsahuje `Plural` metodu rozšíření, která je specifická pro dvě formy množného čísla. Pro jazyky, které vyžadují více formulářů množného čísla, vytvořte metodu rozšíření. S metodou rozšíření nebudete muset poskytnout žádný lokalizační soubor &mdash; pro výchozí jazyk, původní řetězce jsou již k dispozici přímo v kódu.

Můžete použít obecnější `Plural(int count, string[] pluralForms, params object[] arguments)` přetížení, které přijímá pole řetězců překladů.

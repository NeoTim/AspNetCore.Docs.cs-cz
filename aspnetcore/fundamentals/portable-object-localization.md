---
title: Konfigurace lokalizace přenosné objektu v ASP.NET Core
author: sebastienros
description: Tento článek představuje souborů Portable Object a popisuje kroky pro použití v aplikaci ASP.NET Core pomocí Orchard Core framework.
ms.author: scaddie
ms.date: 09/26/2017
uid: fundamentals/portable-object-localization
ms.openlocfilehash: 6ec7afc59d6dfd2629a3d6d83ae619575397a9df
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64899424"
---
# <a name="configure-portable-object-localization-in-aspnet-core"></a>Konfigurace lokalizace přenosné objektu v ASP.NET Core

Podle [Sébastien železnic](https://github.com/sebastienros) a [Scott Addie](https://twitter.com/Scott_Addie)

Tento článek vás provede kroky pro použití souborů Portable Object (PO) v aplikaci ASP.NET Core s [Orchard Core](https://github.com/OrchardCMS/OrchardCore) rozhraní framework.

**Poznámka:** Orchard Core není produktů společnosti Microsoft. V důsledku toho společnost Microsoft poskytuje pro tuto funkci nepodporuje.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([stažení](xref:index#how-to-download-a-sample))

## <a name="what-is-a-po-file"></a>Co je soubor PO?

Soubory PO se distribuují jako textové soubory obsahující přeložené řetězce pro daný jazyk. Některé výhody použití PO soubory místo toho *RESX* soubory zahrnují:
- Soubory PO podporují pluralizace; *RESX* soubory nepodporují pluralizace.
- PO soubory nejsou kompilovány jako *RESX* soubory. V důsledku toho nejsou požadované kroky specializované nástroje a sestavení.
- Soubory PO dobře pracovat spolupráci online nástroje pro úpravy.

### <a name="example"></a>Příklad

Tady je ukázkový soubor PO obsahující překlad pro dva řetězce ve francouzštině, včetně s jeho množné číslo vlastnosti:

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

- `#:`: Komentář označující kontextu řetězec, který má být přeložen. Stejný řetězec může přeložit odlišně v závislosti na tom, kde se používá.
- `msgid`: Nepřeložené řetězce.
- `msgstr`: Přeložené řetězce.

V případě podpory pluralizace lze definovat další položky.

- `msgid_plural`: Nepřeložené řetězce množném čísle.
- `msgstr[0]`: Přeložené řetězce pro případ 0.
- `msgstr[N]`: Přeložené řetězce pro případ N.

Specifikace souboru PO najdete [tady](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).

## <a name="configuring-po-file-support-in-aspnet-core"></a>Konfigurace PO podpora souborů v ASP.NET Core

Tento příklad je založen na aplikace ASP.NET Core MVC generován ze šablony projektů sady Visual Studio 2017.

### <a name="referencing-the-package"></a>Odkazování na balíček

Přidejte odkaz na `OrchardCore.Localization.Core` balíček NuGet. Je k dispozici na [MyGet](https://www.myget.org/) na následující zdroje balíčku: https://www.myget.org/F/orchardcore-preview/api/v3/index.json

*.Csproj* souboru teď obsahuje jeden řádek podobný následujícímu (číslo verze se může lišit):

[!code-xml[](localization/sample/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a>Registrace služby

Přidání požadovaných služeb, které mají `ConfigureServices` metoda *Startup.cs*:

[!code-csharp[](localization/sample/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

Přidejte požadované middlewaru, který má `Configure` metoda *Startup.cs*:

[!code-csharp[](localization/sample/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

Přidejte následující kód do zobrazení Razor podle výběru. *About.cshtml* se používá v tomto příkladu.

[!code-cshtml[](localization/sample/POLocalization/Views/Home/About.cshtml)]

`IViewLocalizer` Instance je vložený a používat pro překlad textu "Hello world!".

### <a name="creating-a-po-file"></a>Vytváří se soubor PO

Vytvořte soubor s názvem  *\<jazykovou verzi kódu > a* v kořenové složce aplikace. V tomto příkladu je název souboru *fr.po* vzhledem k tomu, že se používá francouzštině:

[!code-text[](localization/sample/POLocalization/fr.po)]

Tento soubor obsahuje řetězec, který se převede a Francouzština přeložit řetězce. Překlady vrátit k jejich nadřazená jazykové verze, v případě potřeby. V tomto příkladu *fr.po* soubor se používá, pokud je požadovaná jazyková verze `fr-FR` nebo `fr-CA`.

### <a name="testing-the-application"></a>Testování aplikace

Spusťte aplikaci a přejděte na adresu URL `/Home/About`. Text **Hello world!** Zobrazí se.

Přejděte na adresu URL `/Home/About?culture=fr-FR`. Text **Bonjour the World!** Zobrazí se.

## <a name="pluralization"></a>Pluralizace

Soubory PO podporují pluralizace formulářů, což je užitečné, když do jednoho řetězce musí být přeložen různě v závislosti na kardinalitu. Tato úloha se provádí složité fakt, že každý jazyk definuje vlastní pravidla k výběru, který řetězec určený podle kardinalitu.

Orchard lokalizační balíček poskytuje rozhraní API k vyvolání těchto různých podob množném čísle automaticky.

### <a name="creating-pluralization-po-files"></a>Vytváření souborů PO pluralizace

Přidejte následující obsah do výše uvedené *fr.po* souboru:

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

Zobrazit [co je soubor PO?](#what-is-a-po-file) pro vysvětlení, co představuje každé položky v tomto příkladu.

### <a name="adding-a-language-using-different-pluralization-forms"></a>Přidání jazyk pomocí různých pluralizace formuláře

V předchozím příkladu byly použity řetězce angličtinu a francouzštinu. Angličtinu a francouzštinu mít pouze dva formuláře pluralizace a sdílet stejná pravidla formulář, což je, že Kardinalita jednoho je namapována na první množný tvar. Další Kardinalita se mapuje na druhý množný tvar.

Ne všechny jazyky sdílet stejná pravidla. To je znázorněno České jazyk, který má tři množném čísle formuláře.

Vytvořte `cs.po` to následujícím způsobem a Všimněte si, jak pluralizace potřebuje tři odlišnému překladu:

[!code-text[](localization/sample/POLocalization/cs.po)]

Tak, aby přijímal České lokalizace, přidejte `"cs"` do seznamu podporovaných jazykových verzí v `ConfigureServices` metody:

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

Upravit *Views/Home/About.cshtml* souboru k vykreslení lokalizované, plural řetězce pro několik kardinality:

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

**Poznámka:** V reálném scénáři by použít proměnnou představující počet. Jsme zde, opakujte stejný kód s tři různé hodnoty k vystavení velmi zvláštní případ.

Při přepínání jazykových verzí, naleznete v následujících tématech:

Pro `/Home/About`:

```html
There is one item.
There are 2 items.
There are 5 items.
```

Pro `/Home/About?culture=fr`:

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

Pro `/Home/About?culture=cs`:

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

Všimněte si, že pro Českou jazykovou verzi, se liší tři překlady. Francouzština a anglické jazykové verze sdílet stejné konstrukce pro dvě poslední přeložené řetězce.

## <a name="advanced-tasks"></a>Pokročilé úlohy

### <a name="contextualizing-strings"></a>Contextualizing řetězce

Aplikace často obsahují řetězce k převodu na několika místech. Stejný řetězec může mít různé překlad v určitých umístěních v rámci aplikace (zobrazení syntaxe Razor nebo soubory třídy). Soubor PO podporuje pojem kontext souboru, který slouží ke kategorizaci řetězců reprezentované. Pomocí kontextu souboru, řetězce lze přeložit odlišně, v závislosti na kontextu souboru (nebo chybějící kontextu souboru).

PO lokalizační služby použijte název úplné třídy nebo zobrazení, které se používá při převodu na řetězec. Toho lze dosáhnout nastavením této hodnoty na `msgctxt` položka.

Vezměte v úvahu záležitostí drobné na předchozí *fr.po* příklad. Zobrazení Razor umístění *Views/Home/About.cshtml* lze definovat jako kontext souboru tak, že nastavíte vyhrazený `msgctxt` hodnotu položky:

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

S `msgctxt` nastavit jako takové, překlad textu vyvolá se při přechodu na `/Home/About?culture=fr-FR`. Překlad nedojde, když přejdete na `/Home/Contact?culture=fr-FR`.

Pokud žádná konkrétní položka je nalezena shoda s kontextem daný soubor, záložní mechanismus Orchard Core hledá odpovídající soubor PO bez kontextu. Za předpokladu, že se žádný kontext konkrétní soubor definice pro *Views/Home/Contact.cshtml*, navigační k `/Home/Contact?culture=fr-FR` načte soubor PO, například:

[!code-text[](localization/sample/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a>Změna umístění souborů PO

Výchozí umístění souborů PO lze změnit v `ConfigureServices`:

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

V tomto příkladu jsou načteny PO soubory z *lokalizace* složky.

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a>Implementace vlastní logiky pro vyhledávání souborů lokalizace

Když je potřeba složitější logiku pro nalezení souborů PO `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` rozhraní můžete implementovat a zaregistrovat jako služba. To je užitečné, pokud se PO soubory mohou být uloženy v různých umístěních nebo pokud soubory mají být nalezen v hierarchii složek.

### <a name="using-a-different-default-pluralized-language"></a>Použití různých pluralized výchozí jazyk

Balíček obsahuje `Plural` rozšiřující metoda, která je specifická pro dvě různými formami množném čísle. Pro jazyky, které vyžadují další množném vytvořte rozšiřující metoda. Pomocí metody rozšíření, nebudete muset zadávat jakýkoli soubor lokalizace pro výchozí jazyk &mdash; původním řetězců jsou už k dispozici přímo v kódu.

Můžete použít další Obecné `Plural(int count, string[] pluralForms, params object[] arguments)` přetížení, která přijímá pole řetězců překladů.

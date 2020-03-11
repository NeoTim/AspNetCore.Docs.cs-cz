---
title: Konfigurace lokalizace přenosných objektů v ASP.NET Core
author: sebastienros
description: Tento článek představuje přenosné objektové soubory a popisuje postup jejich použití v aplikaci ASP.NET Core se sadou virtuálních počítačů Core.
ms.author: scaddie
ms.date: 09/26/2017
uid: fundamentals/portable-object-localization
ms.openlocfilehash: 08002564eb68bc04eebaeafed560202d0d69958a
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78656189"
---
# <a name="configure-portable-object-localization-in-aspnet-core"></a>Konfigurace lokalizace přenosných objektů v ASP.NET Core

Od [Sébastien ROS](https://github.com/sebastienros) a [Scott Addie](https://twitter.com/Scott_Addie)

Tento článek vás provede kroky pro použití přenosných souborů objektů (v ASP.NET Core) v aplikaci se sadou virtuálních počítačů [Core](https://github.com/OrchardCMS/OrchardCore) .

**Poznámka:** Sadu nepředstavuje produkt společnosti Microsoft. V důsledku toho společnost Microsoft neposkytuje žádnou podporu pro tuto funkci.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="what-is-a-po-file"></a>Co je soubor PO?

Soubory. PO jsou distribuovány jako textové soubory obsahující přeložené řetězce pro daný jazyk. Některé výhody použití souborů *. resx* obsahují tyto soubory:
- Soubory PO, podporují zámnožování; soubory *RESX* nepodporují zámnožení.
- Soubory na objednávce nejsou kompilovány jako soubory *. resx* . V takovém případě nejsou vyžadovány specializované nástroje a kroky sestavení.
- Soubory na pracovišti dobře fungují s online editačními nástroji pro spolupráci.

### <a name="example"></a>Příklad

Tady je ukázkový soubor s příponou, který obsahuje překlad dvou řetězců ve francouzštině, včetně jednoho s množném číslovou formou:

*fr. po*

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

V tomto příkladu se používá následující syntaxe:

- `#:`: komentář označující kontext řetězce, který se má přeložit. Stejný řetězec může být přeložen odlišně v závislosti na tom, kde se používá.
- `msgid`: nepřeložený řetězec.
- `msgstr`: přeložený řetězec.

V případě podpory plurality lze definovat více položek.

- `msgid_plural`: nepřeložený řetězec plural.
- `msgstr[0]`: přeložený řetězec pro případ 0.
- `msgstr[N]`: přeložený řetězec pro případ N.

Specifikaci souboru PO, najdete [tady](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).

## <a name="configuring-po-file-support-in-aspnet-core"></a>Konfigurace podpory souborů na objednávce v ASP.NET Core

Tento příklad je založen na ASP.NET Core aplikaci MVC vygenerovanou šablonou projektu sady Visual Studio 2017.

### <a name="referencing-the-package"></a>Odkazování na balíček

Přidejte odkaz na balíček NuGet `OrchardCore.Localization.Core`. Je k dispozici na [MyGet](https://www.myget.org/) v následujícím zdroji balíčku: https://www.myget.org/F/orchardcore-preview/api/v3/index.json

Soubor *. csproj* nyní obsahuje řádek podobný následujícímu (číslo verze se může lišit):

[!code-xml[](localization/sample/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a>Registrace služby

Přidejte požadované služby do metody `ConfigureServices` *Startup.cs*:

[!code-csharp[](localization/sample/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

Přidejte požadovaný middleware do metody `Configure` *Startup.cs*:

[!code-csharp[](localization/sample/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

Do zobrazení Razor podle volby přidejte následující kód. V tomto příkladu se používá *About. cshtml* .

[!code-cshtml[](localization/sample/POLocalization/Views/Home/About.cshtml)]

Instance `IViewLocalizer` je vložená a používá se k překladu textu "Hello World!".

### <a name="creating-a-po-file"></a>Vytváření souboru.

Vytvořte soubor s názvem *\<kód jazykové verze >. po* v kořenové složce vaší aplikace. V tomto příkladu je název souboru *fr. po* , protože se používá francouzský jazyk:

[!code-text[](localization/sample/POLocalization/fr.po)]

Tento soubor uloží jak řetězec, který se má přeložit, a řetězec přeložený ze francouzštiny. V případě potřeby se překlady vrátí do své nadřazené jazykové verze. V tomto příkladu se použije soubor *fr. No* , pokud je požadovaná jazyková verze `fr-FR` nebo `fr-CA`.

### <a name="testing-the-application"></a>Testování aplikace

Spusťte aplikaci a přejděte na adresu URL `/Home/About`. Text **Hello World!** se zobrazí.

Přejděte na adresu URL `/Home/About?culture=fr-FR`. Text **Bonjour Le Monde!** se zobrazí.

## <a name="pluralization"></a>Pluralizace

Soubory na více než podporují formuláře pro zápočty, což je užitečné v případě, že stejný řetězec musí být přeložen odlišně na základě mohutnosti. Tato úloha je složitá faktem, že jednotlivé jazyky definují vlastní pravidla pro výběr toho, který řetězec se má použít na základě mohutnosti.

Balíček lokalizace sady funkcí poskytuje rozhraní API k automatickému vyvolání těchto různých formulářů plural.

### <a name="creating-pluralization-po-files"></a>Vytváření souborů v množném čísle

Do dříve zmíněného souboru *fr. No* přidejte následující obsah:

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

Vysvětlení toho, co jednotlivé položky v tomto příkladu představují, najdete v tématu [co je soubor](#what-is-a-po-file) .

### <a name="adding-a-language-using-different-pluralization-forms"></a>Přidání jazyka s různými formuláři pro práci v množném čísle

V předchozím příkladu byly použity anglické a francouzské řetězce. Angličtina a francouzština mají pouze dva formy plurality a sdílejí stejná pravidla, což znamená, že mohutnost jednoho je namapována na první množnou formu. Jakákoli jiná mohutnost je namapována na druhý plurální tvar.

Ne všechny jazyky sdílejí stejná pravidla. To je znázorněno v českém jazyce, který má tři množné formuláře.

Vytvořte `cs.po` soubor následujícím způsobem a Všimněte si, jak by měla být v množném čísle tři různé překlady:

[!code-text[](localization/sample/POLocalization/cs.po)]

Chcete-li přijmout Česká lokalizace, přidejte `"cs"` do seznamu podporovaných kultur v metodě `ConfigureServices`:

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

Upravte *zobrazení/domů/o soubor. cshtml* pro vykreslování lokalizovaných řetězců plural pro několik mohutnosti:

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

**Poznámka:** Ve scénáři reálného světa by se pro reprezentaci počtu použila proměnná. Tady zopakujeme stejný kód se třemi různými hodnotami, aby se vystavoval velmi konkrétní případ.

Po přepnutí na jazykové verze se zobrazí následující:

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

Všimněte si, že pro českou kulturu se tři překlady liší. Francouzské a anglické jazykové verze sdílejí stejnou konstrukci pro dva poslední přeložené řetězce.

## <a name="advanced-tasks"></a>Pokročilé úlohy

### <a name="contextualizing-strings"></a>Contextualizing řetězce

Aplikace často obsahují řetězce, které mají být přeloženy na několika místech. Stejný řetězec může mít jiný překlad v určitých umístěních v rámci aplikace (zobrazení Razor nebo soubory tříd). Soubor. No podporuje pojem kontext souboru, který lze použít ke kategorizaci reprezentovaného řetězce. Pomocí kontextu souboru lze řetězec přeložit odlišně v závislosti na kontextu souboru (nebo nedostatku kontextu souboru).

Lokalizační služby nákupních objednávek používají název celé třídy nebo zobrazení, které se používá při překladu řetězce. Toho je možné dosáhnout nastavením hodnoty v položce `msgctxt`.

Zvažte menší doplněk k předchozímu příkladu *fr. po* . Zobrazení Razor nacházející se v *zobrazeních/domů/o. cshtml* lze definovat jako kontext souboru nastavením hodnoty rezervovaného `msgctxt` položky:

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

Když `msgctxt` jako takový nastavíte, přesměruje se textový překlad při přechodu na `/Home/About?culture=fr-FR`. Při přechodu na `/Home/Contact?culture=fr-FR`se převod neprojeví.

Pokud se zadaným kontextem souboru nevyhovuje žádná konkrétní položka, záložní mechanizmus sady sadů vyhledá příslušný soubor. No bez kontextu. Za předpokladu, že není definován žádný konkrétní kontext souboru pro *views/Home/Contact. cshtml*, přejděte k `/Home/Contact?culture=fr-FR` načte soubor.

[!code-text[](localization/sample/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a>Změna umístění souborů PO.

Výchozí umístění souborů PO lze změnit v `ConfigureServices`:

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

V tomto příkladu jsou soubory PO načtení načteny ze složky *lokalizace* .

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a>Implementace vlastní logiky pro hledání souborů lokalizace

Je-li pro vyhledání souborů PO umístění potřeba komplexnější logiku, `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` rozhraní může být implementováno a registrováno jako služba. To je užitečné v případě, že soubory. mohou být uloženy v různých umístěních nebo v případě, že je třeba soubory najít v rámci hierarchie složek.

### <a name="using-a-different-default-pluralized-language"></a>Použití jiného výchozího množném jazyku

Balíček obsahuje metodu rozšíření `Plural`, která je specifická pro dvě množné formy. Pro jazyky, které vyžadují více formulářů plural, vytvořte metodu rozšíření. V případě metody rozšíření nemusíte pro výchozí jazyk zadat žádný soubor lokalizace &mdash; původní řetězce jsou již k dispozici přímo v kódu.

Můžete použít obecnější přetížení `Plural(int count, string[] pluralForms, params object[] arguments)`, které přijímá pole řetězců překladu.

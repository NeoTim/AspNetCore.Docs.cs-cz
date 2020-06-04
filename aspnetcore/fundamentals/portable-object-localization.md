---
title: Nakonfigurujte lokalizaci přenosných objektů v ASP.NET Core autor: sebastienros Popis: Tento článek představuje přenositelné soubory objektů a popisuje postup jejich použití v aplikaci ASP.NET Core se sadou virtuálních počítačů Core.
MS. Author: scaddie MS. Date: 09/26/2017 No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: Základy/přenosné objekty-lokalizace

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

- `#:`: Komentář označující kontext řetězce, který se má přeložit. Stejný řetězec může být přeložen odlišně v závislosti na tom, kde se používá.
- `msgid`: Nepřeložený řetězec.
- `msgstr`: Přeložený řetězec.

V případě podpory plurality lze definovat více položek.

- `msgid_plural`: Nepřeložený řetězec plural.
- `msgstr[0]`: Přeložený řetězec pro případ 0.
- `msgstr[N]`: Přeložený řetězec pro případ N.

Specifikaci souboru PO, najdete [tady](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).

## <a name="configuring-po-file-support-in-aspnet-core"></a>Konfigurace podpory souborů na objednávce v ASP.NET Core

Tento příklad je založen na ASP.NET Core aplikaci MVC vygenerovanou šablonou projektu sady Visual Studio 2017.

### <a name="referencing-the-package"></a>Odkazování na balíček

Přidejte odkaz na `OrchardCore.Localization.Core` balíček NuGet. Je k dispozici na [MyGet](https://www.myget.org/) v následujícím zdroji balíčku:https://www.myget.org/F/orchardcore-preview/api/v3/index.json

Soubor *. csproj* nyní obsahuje řádek podobný následujícímu (číslo verze se může lišit):

[!code-xml[](localization/sample/2.x/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a>Registrace služby

Přidejte požadované služby k `ConfigureServices` metodě *Startup.cs*:

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

Přidejte požadovaný middleware do `Configure` metody *Startup.cs*:

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

Přidejte následující kód pro Razor zobrazení volby. V tomto příkladu se používá *About. cshtml* .

[!code-cshtml[](localization/sample/2.x/POLocalization/Views/Home/About.cshtml)]

`IViewLocalizer`Instance je vložená a používá se k překladu textu "Hello World!".

### <a name="creating-a-po-file"></a>Vytváření souboru.

Vytvořte soubor s názvem * \<culture code> . po* v kořenové složce vaší aplikace. V tomto příkladu je název souboru *fr. po* , protože se používá francouzský jazyk:

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

Tento soubor uloží jak řetězec, který se má přeložit, a řetězec přeložený ze francouzštiny. V případě potřeby se překlady vrátí do své nadřazené jazykové verze. V tomto příkladu se použije soubor *fr. No* , pokud je požadovaná jazyková verze `fr-FR` nebo `fr-CA` .

### <a name="testing-the-application"></a>Testování aplikace

Spusťte aplikaci a přejděte na adresu URL `/Home/About` . Text **Hello World!** se zobrazí.

Přejděte na adresu URL `/Home/About?culture=fr-FR` . Text **Bonjour Le Monde!** se zobrazí.

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

Vytvořte `cs.po` soubor následujícím způsobem a poznamenejte si, jak pluralita potřebuje tři různé překlady:

[!code-text[](localization/sample/2.x/POLocalization/cs.po)]

Chcete-li přijmout Česká lokalizace, přidejte `"cs"` do seznamu podporovaných kultur v `ConfigureServices` metodě:

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

Aplikace často obsahují řetězce, které mají být přeloženy na několika místech. Stejný řetězec může mít jiný překlad v určitých umístěních v rámci aplikace ( Razor zobrazení nebo soubory tříd). Soubor. No podporuje pojem kontext souboru, který lze použít ke kategorizaci reprezentovaného řetězce. Pomocí kontextu souboru lze řetězec přeložit odlišně v závislosti na kontextu souboru (nebo nedostatku kontextu souboru).

Lokalizační služby nákupních objednávek používají název celé třídy nebo zobrazení, které se používá při překladu řetězce. Toho je možné dosáhnout nastavením hodnoty `msgctxt` položky.

Zvažte menší doplněk k předchozímu příkladu *fr. po* . RazorZobrazení, které se nachází na *stránce zobrazení/domů/o. cshtml* , lze definovat jako kontext souboru nastavením `msgctxt` hodnoty rezervované položky:

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

Když se `msgctxt` nastaví jako takové, překlad textu se objeví při přechodu na `/Home/About?culture=fr-FR` . K překladu nedojde při přechodu na `/Home/Contact?culture=fr-FR` .

Pokud se zadaným kontextem souboru nevyhovuje žádná konkrétní položka, záložní mechanizmus sady sadů vyhledá příslušný soubor. No bez kontextu. Za předpokladu, že není definován žádný konkrétní kontext souboru pro *views/Home/Contact. cshtml*, přejděte k `/Home/Contact?culture=fr-FR` načtení souboru objednávky, například:

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a>Změna umístění souborů PO.

Výchozí umístění souborů PO lze změnit v `ConfigureServices` :

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

V tomto příkladu jsou soubory PO načtení načteny ze složky *lokalizace* .

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a>Implementace vlastní logiky pro hledání souborů lokalizace

Pokud je pro vyhledání souborů na místě potřeba složitější logika, `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` rozhraní se dá implementovat a zaregistrovat jako služba. To je užitečné v případě, že soubory. mohou být uloženy v různých umístěních nebo v případě, že je třeba soubory najít v rámci hierarchie složek.

### <a name="using-a-different-default-pluralized-language"></a>Použití jiného výchozího množném jazyku

Balíček obsahuje `Plural` metodu rozšíření, která je specifická pro dva množné formy. Pro jazyky, které vyžadují více formulářů plural, vytvořte metodu rozšíření. V případě metody rozšíření nemusíte pro výchozí jazyk zadat žádný soubor lokalizace, protože &mdash; původní řetězce jsou již k dispozici přímo v kódu.

Můžete použít obecnější `Plural(int count, string[] pluralForms, params object[] arguments)` přetížení, které přijímá pole řetězců překladu.

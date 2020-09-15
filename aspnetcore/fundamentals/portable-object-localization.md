---
title: Konfigurace lokalizace přenosných objektů v ASP.NET Core
author: sebastienros
description: Tento článek představuje přenosné objektové soubory a popisuje postup jejich použití v aplikaci ASP.NET Core se sadou virtuálních počítačů Core.
ms.author: scaddie
ms.date: 09/26/2017
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/portable-object-localization
ms.openlocfilehash: f471c5b7511434cf42717e52ef271663c2e36647
ms.sourcegitcommit: 6ecdc481d5b9a10d2c6e091217f017b36bdba957
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90456046"
---
# <a name="configure-portable-object-localization-in-aspnet-core"></a><span data-ttu-id="9a630-103">Konfigurace lokalizace přenosných objektů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9a630-103">Configure portable object localization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9a630-104">[Sébastien ROS](https://github.com/sebastienros), [Scott Addie](https://twitter.com/Scott_Addie) a [Hisham bin Ateya](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="9a630-104">By [Sébastien Ros](https://github.com/sebastienros), [Scott Addie](https://twitter.com/Scott_Addie) and [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="9a630-105">Tento článek vás provede kroky pro použití přenosných souborů objektů (v ASP.NET Core) v aplikaci se sadou virtuálních počítačů [Core](https://github.com/OrchardCMS/OrchardCore) .</span><span class="sxs-lookup"><span data-stu-id="9a630-105">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="9a630-106">**Poznámka:** Sadu nepředstavuje produkt společnosti Microsoft.</span><span class="sxs-lookup"><span data-stu-id="9a630-106">**Note:** Orchard Core isn't a Microsoft product.</span></span> <span data-ttu-id="9a630-107">V důsledku toho společnost Microsoft neposkytuje žádnou podporu pro tuto funkci.</span><span class="sxs-lookup"><span data-stu-id="9a630-107">Consequently, Microsoft provides no support for this feature.</span></span>

<span data-ttu-id="9a630-108">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/POLocalization) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9a630-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="9a630-109">Co je soubor PO?</span><span class="sxs-lookup"><span data-stu-id="9a630-109">What is a PO file?</span></span>

<span data-ttu-id="9a630-110">Soubory. PO jsou distribuovány jako textové soubory obsahující přeložené řetězce pro daný jazyk.</span><span class="sxs-lookup"><span data-stu-id="9a630-110">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="9a630-111">Některé výhody použití souborů *. resx* obsahují tyto soubory:</span><span class="sxs-lookup"><span data-stu-id="9a630-111">Some advantages of using PO files instead *.resx* files include:</span></span>
- <span data-ttu-id="9a630-112">Soubory PO, podporují zámnožování; soubory *RESX* nepodporují zámnožení.</span><span class="sxs-lookup"><span data-stu-id="9a630-112">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="9a630-113">Soubory na objednávce nejsou kompilovány jako soubory *. resx* .</span><span class="sxs-lookup"><span data-stu-id="9a630-113">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="9a630-114">V takovém případě nejsou vyžadovány specializované nástroje a kroky sestavení.</span><span class="sxs-lookup"><span data-stu-id="9a630-114">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="9a630-115">Soubory na pracovišti dobře fungují s online editačními nástroji pro spolupráci.</span><span class="sxs-lookup"><span data-stu-id="9a630-115">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="9a630-116">Příklad</span><span class="sxs-lookup"><span data-stu-id="9a630-116">Example</span></span>

<span data-ttu-id="9a630-117">Tady je ukázkový soubor s příponou, který obsahuje překlad dvou řetězců ve francouzštině, včetně jednoho s množném číslovou formou:</span><span class="sxs-lookup"><span data-stu-id="9a630-117">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="9a630-118">*fr. po*</span><span class="sxs-lookup"><span data-stu-id="9a630-118">*fr.po*</span></span>

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

<span data-ttu-id="9a630-119">V tomto příkladu se používá následující syntaxe:</span><span class="sxs-lookup"><span data-stu-id="9a630-119">This example uses the following syntax:</span></span>

- <span data-ttu-id="9a630-120">`#:`: Komentář označující kontext řetězce, který se má přeložit.</span><span class="sxs-lookup"><span data-stu-id="9a630-120">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="9a630-121">Stejný řetězec může být přeložen odlišně v závislosti na tom, kde se používá.</span><span class="sxs-lookup"><span data-stu-id="9a630-121">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="9a630-122">`msgid`: Nepřeložený řetězec.</span><span class="sxs-lookup"><span data-stu-id="9a630-122">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="9a630-123">`msgstr`: Přeložený řetězec.</span><span class="sxs-lookup"><span data-stu-id="9a630-123">`msgstr`: The translated string.</span></span>

<span data-ttu-id="9a630-124">V případě podpory plurality lze definovat více položek.</span><span class="sxs-lookup"><span data-stu-id="9a630-124">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="9a630-125">`msgid_plural`: Nepřeložený řetězec plural.</span><span class="sxs-lookup"><span data-stu-id="9a630-125">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="9a630-126">`msgstr[0]`: Přeložený řetězec pro případ 0.</span><span class="sxs-lookup"><span data-stu-id="9a630-126">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="9a630-127">`msgstr[N]`: Přeložený řetězec pro případ N.</span><span class="sxs-lookup"><span data-stu-id="9a630-127">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="9a630-128">Specifikaci souboru PO, najdete [tady](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span><span class="sxs-lookup"><span data-stu-id="9a630-128">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="9a630-129">Konfigurace podpory souborů na objednávce v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9a630-129">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="9a630-130">Tento příklad je založen na ASP.NET Core aplikaci MVC vygenerovanou šablonou projektu sady Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="9a630-130">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="9a630-131">Odkazování na balíček</span><span class="sxs-lookup"><span data-stu-id="9a630-131">Referencing the package</span></span>

<span data-ttu-id="9a630-132">Přidejte odkaz na `OrchardCore.Localization.Core` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="9a630-132">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span> <span data-ttu-id="9a630-133">Je k dispozici na [MyGet](https://www.myget.org/) v následujícím zdroji balíčku: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span><span class="sxs-lookup"><span data-stu-id="9a630-133">It's available on [MyGet](https://www.myget.org/) at the following package source: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span></span>

<span data-ttu-id="9a630-134">Soubor *. csproj* nyní obsahuje řádek podobný následujícímu (číslo verze se může lišit):</span><span class="sxs-lookup"><span data-stu-id="9a630-134">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[](localization/sample/3.x/POLocalization/POLocalization.csproj?range=8)]

### <a name="registering-the-service"></a><span data-ttu-id="9a630-135">Registrace služby</span><span class="sxs-lookup"><span data-stu-id="9a630-135">Registering the service</span></span>

<span data-ttu-id="9a630-136">Přidejte požadované služby k `ConfigureServices` metodě *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9a630-136">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/3.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="9a630-137">Přidejte požadovaný middleware do `Configure` metody *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9a630-137">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/3.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="9a630-138">Přidejte následující kód pro Razor zobrazení volby.</span><span class="sxs-lookup"><span data-stu-id="9a630-138">Add the following code to your Razor view of choice.</span></span> <span data-ttu-id="9a630-139">V tomto příkladu se používá *About. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="9a630-139">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[](localization/sample/3.x/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="9a630-140">`IViewLocalizer`Instance je vložená a používá se k překladu textu "Hello World!".</span><span class="sxs-lookup"><span data-stu-id="9a630-140">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="9a630-141">Vytváření souboru.</span><span class="sxs-lookup"><span data-stu-id="9a630-141">Creating a PO file</span></span>

<span data-ttu-id="9a630-142">Vytvořte soubor s názvem \* \<culture code> . po\* v kořenové složce vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="9a630-142">Create a file named *\<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="9a630-143">V tomto příkladu je název souboru *fr. po* , protože se používá francouzský jazyk:</span><span class="sxs-lookup"><span data-stu-id="9a630-143">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[](localization/sample/3.x/POLocalization/fr.po)]

<span data-ttu-id="9a630-144">Tento soubor uloží jak řetězec, který se má přeložit, a řetězec přeložený ze francouzštiny.</span><span class="sxs-lookup"><span data-stu-id="9a630-144">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="9a630-145">V případě potřeby se překlady vrátí do své nadřazené jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="9a630-145">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="9a630-146">V tomto příkladu se použije soubor *fr. No* , pokud je požadovaná jazyková verze `fr-FR` nebo `fr-CA` .</span><span class="sxs-lookup"><span data-stu-id="9a630-146">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="9a630-147">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="9a630-147">Testing the application</span></span>

<span data-ttu-id="9a630-148">Spusťte aplikaci a přejděte na adresu URL `/Home/About` .</span><span class="sxs-lookup"><span data-stu-id="9a630-148">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="9a630-149">Text **Hello World!**</span><span class="sxs-lookup"><span data-stu-id="9a630-149">The text **Hello world!**</span></span> <span data-ttu-id="9a630-150">se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="9a630-150">is displayed.</span></span>

<span data-ttu-id="9a630-151">Přejděte na adresu URL `/Home/About?culture=fr-FR` .</span><span class="sxs-lookup"><span data-stu-id="9a630-151">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="9a630-152">Text **Bonjour Le Monde!**</span><span class="sxs-lookup"><span data-stu-id="9a630-152">The text **Bonjour le monde!**</span></span> <span data-ttu-id="9a630-153">se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="9a630-153">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="9a630-154">Pluralizace</span><span class="sxs-lookup"><span data-stu-id="9a630-154">Pluralization</span></span>

<span data-ttu-id="9a630-155">Soubory na více než podporují formuláře pro zápočty, což je užitečné v případě, že stejný řetězec musí být přeložen odlišně na základě mohutnosti.</span><span class="sxs-lookup"><span data-stu-id="9a630-155">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="9a630-156">Tato úloha je složitá faktem, že jednotlivé jazyky definují vlastní pravidla pro výběr toho, který řetězec se má použít na základě mohutnosti.</span><span class="sxs-lookup"><span data-stu-id="9a630-156">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="9a630-157">Balíček lokalizace sady funkcí poskytuje rozhraní API k automatickému vyvolání těchto různých formulářů plural.</span><span class="sxs-lookup"><span data-stu-id="9a630-157">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="9a630-158">Vytváření souborů v množném čísle</span><span class="sxs-lookup"><span data-stu-id="9a630-158">Creating pluralization PO files</span></span>

<span data-ttu-id="9a630-159">Do dříve zmíněného souboru *fr. No* přidejte následující obsah:</span><span class="sxs-lookup"><span data-stu-id="9a630-159">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="9a630-160">Vysvětlení toho, co jednotlivé položky v tomto příkladu představují, najdete v tématu [co je soubor](#what-is-a-po-file) .</span><span class="sxs-lookup"><span data-stu-id="9a630-160">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="9a630-161">Přidání jazyka s různými formuláři pro práci v množném čísle</span><span class="sxs-lookup"><span data-stu-id="9a630-161">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="9a630-162">V předchozím příkladu byly použity anglické a francouzské řetězce.</span><span class="sxs-lookup"><span data-stu-id="9a630-162">English and French strings were used in the previous example.</span></span> <span data-ttu-id="9a630-163">Angličtina a francouzština mají pouze dva formy plurality a sdílejí stejná pravidla, což znamená, že mohutnost jednoho je namapována na první množnou formu.</span><span class="sxs-lookup"><span data-stu-id="9a630-163">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="9a630-164">Jakákoli jiná mohutnost je namapována na druhý plurální tvar.</span><span class="sxs-lookup"><span data-stu-id="9a630-164">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="9a630-165">Ne všechny jazyky sdílejí stejná pravidla.</span><span class="sxs-lookup"><span data-stu-id="9a630-165">Not all languages share the same rules.</span></span> <span data-ttu-id="9a630-166">To je znázorněno v českém jazyce, který má tři množné formuláře.</span><span class="sxs-lookup"><span data-stu-id="9a630-166">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="9a630-167">Vytvořte `cs.po` soubor následujícím způsobem a poznamenejte si, jak pluralita potřebuje tři různé překlady:</span><span class="sxs-lookup"><span data-stu-id="9a630-167">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[](localization/sample/3.x/POLocalization/cs.po)]

<span data-ttu-id="9a630-168">Chcete-li přijmout Česká lokalizace, přidejte `"cs"` do seznamu podporovaných kultur v `ConfigureServices` metodě:</span><span class="sxs-lookup"><span data-stu-id="9a630-168">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

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

<span data-ttu-id="9a630-169">Upravte *zobrazení/domů/o soubor. cshtml* pro vykreslování lokalizovaných řetězců plural pro několik mohutnosti:</span><span class="sxs-lookup"><span data-stu-id="9a630-169">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="9a630-170">**Poznámka:** Ve scénáři reálného světa by se pro reprezentaci počtu použila proměnná.</span><span class="sxs-lookup"><span data-stu-id="9a630-170">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="9a630-171">Tady zopakujeme stejný kód se třemi různými hodnotami, aby se vystavoval velmi konkrétní případ.</span><span class="sxs-lookup"><span data-stu-id="9a630-171">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="9a630-172">Po přepnutí na jazykové verze se zobrazí následující:</span><span class="sxs-lookup"><span data-stu-id="9a630-172">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="9a630-173">Pro `/Home/About`:</span><span class="sxs-lookup"><span data-stu-id="9a630-173">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="9a630-174">Pro `/Home/About?culture=fr`:</span><span class="sxs-lookup"><span data-stu-id="9a630-174">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="9a630-175">Pro `/Home/About?culture=cs`:</span><span class="sxs-lookup"><span data-stu-id="9a630-175">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="9a630-176">Všimněte si, že pro českou kulturu se tři překlady liší.</span><span class="sxs-lookup"><span data-stu-id="9a630-176">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="9a630-177">Francouzské a anglické jazykové verze sdílejí stejnou konstrukci pro dva poslední přeložené řetězce.</span><span class="sxs-lookup"><span data-stu-id="9a630-177">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="9a630-178">Pokročilé úlohy</span><span class="sxs-lookup"><span data-stu-id="9a630-178">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="9a630-179">Contextualizing řetězce</span><span class="sxs-lookup"><span data-stu-id="9a630-179">Contextualizing strings</span></span>

<span data-ttu-id="9a630-180">Aplikace často obsahují řetězce, které mají být přeloženy na několika místech.</span><span class="sxs-lookup"><span data-stu-id="9a630-180">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="9a630-181">Stejný řetězec může mít jiný překlad v určitých umístěních v rámci aplikace ( Razor zobrazení nebo soubory tříd).</span><span class="sxs-lookup"><span data-stu-id="9a630-181">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="9a630-182">Soubor. No podporuje pojem kontext souboru, který lze použít ke kategorizaci reprezentovaného řetězce.</span><span class="sxs-lookup"><span data-stu-id="9a630-182">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="9a630-183">Pomocí kontextu souboru lze řetězec přeložit odlišně v závislosti na kontextu souboru (nebo nedostatku kontextu souboru).</span><span class="sxs-lookup"><span data-stu-id="9a630-183">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="9a630-184">Lokalizační služby nákupních objednávek používají název celé třídy nebo zobrazení, které se používá při překladu řetězce.</span><span class="sxs-lookup"><span data-stu-id="9a630-184">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span> <span data-ttu-id="9a630-185">Toho je možné dosáhnout nastavením hodnoty `msgctxt` položky.</span><span class="sxs-lookup"><span data-stu-id="9a630-185">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="9a630-186">Zvažte menší doplněk k předchozímu příkladu *fr. po* .</span><span class="sxs-lookup"><span data-stu-id="9a630-186">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="9a630-187">RazorZobrazení, které se nachází na *stránce zobrazení/domů/o. cshtml* , lze definovat jako kontext souboru nastavením `msgctxt` hodnoty rezervované položky:</span><span class="sxs-lookup"><span data-stu-id="9a630-187">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="9a630-188">Když se `msgctxt` nastaví jako takové, překlad textu se objeví při přechodu na `/Home/About?culture=fr-FR` .</span><span class="sxs-lookup"><span data-stu-id="9a630-188">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="9a630-189">K překladu nedojde při přechodu na `/Home/Contact?culture=fr-FR` .</span><span class="sxs-lookup"><span data-stu-id="9a630-189">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="9a630-190">Pokud se zadaným kontextem souboru nevyhovuje žádná konkrétní položka, záložní mechanizmus sady sadů vyhledá příslušný soubor. No bez kontextu.</span><span class="sxs-lookup"><span data-stu-id="9a630-190">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="9a630-191">Za předpokladu, že není definován žádný konkrétní kontext souboru pro *views/Home/Contact. cshtml*, přejděte k `/Home/Contact?culture=fr-FR` načtení souboru objednávky, například:</span><span class="sxs-lookup"><span data-stu-id="9a630-191">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[](localization/sample/3.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="9a630-192">Změna umístění souborů PO.</span><span class="sxs-lookup"><span data-stu-id="9a630-192">Changing the location of PO files</span></span>

<span data-ttu-id="9a630-193">Výchozí umístění souborů PO lze změnit v `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9a630-193">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="9a630-194">V tomto příkladu jsou soubory PO načtení načteny ze složky *lokalizace* .</span><span class="sxs-lookup"><span data-stu-id="9a630-194">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="9a630-195">Implementace vlastní logiky pro hledání souborů lokalizace</span><span class="sxs-lookup"><span data-stu-id="9a630-195">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="9a630-196">Pokud je pro vyhledání souborů na místě potřeba složitější logika, `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` rozhraní se dá implementovat a zaregistrovat jako služba.</span><span class="sxs-lookup"><span data-stu-id="9a630-196">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="9a630-197">To je užitečné v případě, že soubory. mohou být uloženy v různých umístěních nebo v případě, že je třeba soubory najít v rámci hierarchie složek.</span><span class="sxs-lookup"><span data-stu-id="9a630-197">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="9a630-198">Použití jiného výchozího množném jazyku</span><span class="sxs-lookup"><span data-stu-id="9a630-198">Using a different default pluralized language</span></span>

<span data-ttu-id="9a630-199">Balíček obsahuje `Plural` metodu rozšíření, která je specifická pro dva množné formy.</span><span class="sxs-lookup"><span data-stu-id="9a630-199">The package includes a `Plural` extension method that's specific to two plural forms.</span></span> <span data-ttu-id="9a630-200">Pro jazyky, které vyžadují více formulářů plural, vytvořte metodu rozšíření.</span><span class="sxs-lookup"><span data-stu-id="9a630-200">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="9a630-201">V případě metody rozšíření nemusíte pro výchozí jazyk zadat žádný soubor lokalizace, protože &mdash; původní řetězce jsou již k dispozici přímo v kódu.</span><span class="sxs-lookup"><span data-stu-id="9a630-201">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="9a630-202">Můžete použít obecnější `Plural(int count, string[] pluralForms, params object[] arguments)` přetížení, které přijímá pole řetězců překladu.</span><span class="sxs-lookup"><span data-stu-id="9a630-202">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9a630-203">Od [Sébastien ROS](https://github.com/sebastienros) a [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="9a630-203">By [Sébastien Ros](https://github.com/sebastienros) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="9a630-204">Tento článek vás provede kroky pro použití přenosných souborů objektů (v ASP.NET Core) v aplikaci se sadou virtuálních počítačů [Core](https://github.com/OrchardCMS/OrchardCore) .</span><span class="sxs-lookup"><span data-stu-id="9a630-204">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="9a630-205">**Poznámka:** Sadu nepředstavuje produkt společnosti Microsoft.</span><span class="sxs-lookup"><span data-stu-id="9a630-205">**Note:** Orchard Core isn't a Microsoft product.</span></span> <span data-ttu-id="9a630-206">V důsledku toho společnost Microsoft neposkytuje žádnou podporu pro tuto funkci.</span><span class="sxs-lookup"><span data-stu-id="9a630-206">Consequently, Microsoft provides no support for this feature.</span></span>

<span data-ttu-id="9a630-207">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/POLocalization) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9a630-207">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="9a630-208">Co je soubor PO?</span><span class="sxs-lookup"><span data-stu-id="9a630-208">What is a PO file?</span></span>

<span data-ttu-id="9a630-209">Soubory. PO jsou distribuovány jako textové soubory obsahující přeložené řetězce pro daný jazyk.</span><span class="sxs-lookup"><span data-stu-id="9a630-209">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="9a630-210">Některé výhody použití souborů *. resx* obsahují tyto soubory:</span><span class="sxs-lookup"><span data-stu-id="9a630-210">Some advantages of using PO files instead *.resx* files include:</span></span>
- <span data-ttu-id="9a630-211">Soubory PO, podporují zámnožování; soubory *RESX* nepodporují zámnožení.</span><span class="sxs-lookup"><span data-stu-id="9a630-211">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="9a630-212">Soubory na objednávce nejsou kompilovány jako soubory *. resx* .</span><span class="sxs-lookup"><span data-stu-id="9a630-212">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="9a630-213">V takovém případě nejsou vyžadovány specializované nástroje a kroky sestavení.</span><span class="sxs-lookup"><span data-stu-id="9a630-213">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="9a630-214">Soubory na pracovišti dobře fungují s online editačními nástroji pro spolupráci.</span><span class="sxs-lookup"><span data-stu-id="9a630-214">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="9a630-215">Příklad</span><span class="sxs-lookup"><span data-stu-id="9a630-215">Example</span></span>

<span data-ttu-id="9a630-216">Tady je ukázkový soubor s příponou, který obsahuje překlad dvou řetězců ve francouzštině, včetně jednoho s množném číslovou formou:</span><span class="sxs-lookup"><span data-stu-id="9a630-216">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="9a630-217">*fr. po*</span><span class="sxs-lookup"><span data-stu-id="9a630-217">*fr.po*</span></span>

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

<span data-ttu-id="9a630-218">V tomto příkladu se používá následující syntaxe:</span><span class="sxs-lookup"><span data-stu-id="9a630-218">This example uses the following syntax:</span></span>

- <span data-ttu-id="9a630-219">`#:`: Komentář označující kontext řetězce, který se má přeložit.</span><span class="sxs-lookup"><span data-stu-id="9a630-219">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="9a630-220">Stejný řetězec může být přeložen odlišně v závislosti na tom, kde se používá.</span><span class="sxs-lookup"><span data-stu-id="9a630-220">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="9a630-221">`msgid`: Nepřeložený řetězec.</span><span class="sxs-lookup"><span data-stu-id="9a630-221">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="9a630-222">`msgstr`: Přeložený řetězec.</span><span class="sxs-lookup"><span data-stu-id="9a630-222">`msgstr`: The translated string.</span></span>

<span data-ttu-id="9a630-223">V případě podpory plurality lze definovat více položek.</span><span class="sxs-lookup"><span data-stu-id="9a630-223">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="9a630-224">`msgid_plural`: Nepřeložený řetězec plural.</span><span class="sxs-lookup"><span data-stu-id="9a630-224">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="9a630-225">`msgstr[0]`: Přeložený řetězec pro případ 0.</span><span class="sxs-lookup"><span data-stu-id="9a630-225">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="9a630-226">`msgstr[N]`: Přeložený řetězec pro případ N.</span><span class="sxs-lookup"><span data-stu-id="9a630-226">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="9a630-227">Specifikaci souboru PO, najdete [tady](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span><span class="sxs-lookup"><span data-stu-id="9a630-227">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="9a630-228">Konfigurace podpory souborů na objednávce v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9a630-228">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="9a630-229">Tento příklad je založen na ASP.NET Core aplikaci MVC vygenerovanou šablonou projektu sady Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="9a630-229">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="9a630-230">Odkazování na balíček</span><span class="sxs-lookup"><span data-stu-id="9a630-230">Referencing the package</span></span>

<span data-ttu-id="9a630-231">Přidejte odkaz na `OrchardCore.Localization.Core` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="9a630-231">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span> <span data-ttu-id="9a630-232">Je k dispozici na [MyGet](https://www.myget.org/) v následujícím zdroji balíčku: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span><span class="sxs-lookup"><span data-stu-id="9a630-232">It's available on [MyGet](https://www.myget.org/) at the following package source: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span></span>

<span data-ttu-id="9a630-233">Soubor *. csproj* nyní obsahuje řádek podobný následujícímu (číslo verze se může lišit):</span><span class="sxs-lookup"><span data-stu-id="9a630-233">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[](localization/sample/2.x/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a><span data-ttu-id="9a630-234">Registrace služby</span><span class="sxs-lookup"><span data-stu-id="9a630-234">Registering the service</span></span>

<span data-ttu-id="9a630-235">Přidejte požadované služby k `ConfigureServices` metodě *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9a630-235">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="9a630-236">Přidejte požadovaný middleware do `Configure` metody *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9a630-236">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="9a630-237">Přidejte následující kód pro Razor zobrazení volby.</span><span class="sxs-lookup"><span data-stu-id="9a630-237">Add the following code to your Razor view of choice.</span></span> <span data-ttu-id="9a630-238">V tomto příkladu se používá *About. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="9a630-238">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[](localization/sample/2.x/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="9a630-239">`IViewLocalizer`Instance je vložená a používá se k překladu textu "Hello World!".</span><span class="sxs-lookup"><span data-stu-id="9a630-239">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="9a630-240">Vytváření souboru.</span><span class="sxs-lookup"><span data-stu-id="9a630-240">Creating a PO file</span></span>

<span data-ttu-id="9a630-241">Vytvořte soubor s názvem \* \<culture code> . po\* v kořenové složce vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="9a630-241">Create a file named *\<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="9a630-242">V tomto příkladu je název souboru *fr. po* , protože se používá francouzský jazyk:</span><span class="sxs-lookup"><span data-stu-id="9a630-242">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

<span data-ttu-id="9a630-243">Tento soubor uloží jak řetězec, který se má přeložit, a řetězec přeložený ze francouzštiny.</span><span class="sxs-lookup"><span data-stu-id="9a630-243">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="9a630-244">V případě potřeby se překlady vrátí do své nadřazené jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="9a630-244">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="9a630-245">V tomto příkladu se použije soubor *fr. No* , pokud je požadovaná jazyková verze `fr-FR` nebo `fr-CA` .</span><span class="sxs-lookup"><span data-stu-id="9a630-245">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="9a630-246">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="9a630-246">Testing the application</span></span>

<span data-ttu-id="9a630-247">Spusťte aplikaci a přejděte na adresu URL `/Home/About` .</span><span class="sxs-lookup"><span data-stu-id="9a630-247">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="9a630-248">Text **Hello World!**</span><span class="sxs-lookup"><span data-stu-id="9a630-248">The text **Hello world!**</span></span> <span data-ttu-id="9a630-249">se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="9a630-249">is displayed.</span></span>

<span data-ttu-id="9a630-250">Přejděte na adresu URL `/Home/About?culture=fr-FR` .</span><span class="sxs-lookup"><span data-stu-id="9a630-250">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="9a630-251">Text **Bonjour Le Monde!**</span><span class="sxs-lookup"><span data-stu-id="9a630-251">The text **Bonjour le monde!**</span></span> <span data-ttu-id="9a630-252">se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="9a630-252">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="9a630-253">Pluralizace</span><span class="sxs-lookup"><span data-stu-id="9a630-253">Pluralization</span></span>

<span data-ttu-id="9a630-254">Soubory na více než podporují formuláře pro zápočty, což je užitečné v případě, že stejný řetězec musí být přeložen odlišně na základě mohutnosti.</span><span class="sxs-lookup"><span data-stu-id="9a630-254">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="9a630-255">Tato úloha je složitá faktem, že jednotlivé jazyky definují vlastní pravidla pro výběr toho, který řetězec se má použít na základě mohutnosti.</span><span class="sxs-lookup"><span data-stu-id="9a630-255">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="9a630-256">Balíček lokalizace sady funkcí poskytuje rozhraní API k automatickému vyvolání těchto různých formulářů plural.</span><span class="sxs-lookup"><span data-stu-id="9a630-256">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="9a630-257">Vytváření souborů v množném čísle</span><span class="sxs-lookup"><span data-stu-id="9a630-257">Creating pluralization PO files</span></span>

<span data-ttu-id="9a630-258">Do dříve zmíněného souboru *fr. No* přidejte následující obsah:</span><span class="sxs-lookup"><span data-stu-id="9a630-258">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="9a630-259">Vysvětlení toho, co jednotlivé položky v tomto příkladu představují, najdete v tématu [co je soubor](#what-is-a-po-file) .</span><span class="sxs-lookup"><span data-stu-id="9a630-259">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="9a630-260">Přidání jazyka s různými formuláři pro práci v množném čísle</span><span class="sxs-lookup"><span data-stu-id="9a630-260">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="9a630-261">V předchozím příkladu byly použity anglické a francouzské řetězce.</span><span class="sxs-lookup"><span data-stu-id="9a630-261">English and French strings were used in the previous example.</span></span> <span data-ttu-id="9a630-262">Angličtina a francouzština mají pouze dva formy plurality a sdílejí stejná pravidla, což znamená, že mohutnost jednoho je namapována na první množnou formu.</span><span class="sxs-lookup"><span data-stu-id="9a630-262">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="9a630-263">Jakákoli jiná mohutnost je namapována na druhý plurální tvar.</span><span class="sxs-lookup"><span data-stu-id="9a630-263">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="9a630-264">Ne všechny jazyky sdílejí stejná pravidla.</span><span class="sxs-lookup"><span data-stu-id="9a630-264">Not all languages share the same rules.</span></span> <span data-ttu-id="9a630-265">To je znázorněno v českém jazyce, který má tři množné formuláře.</span><span class="sxs-lookup"><span data-stu-id="9a630-265">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="9a630-266">Vytvořte `cs.po` soubor následujícím způsobem a poznamenejte si, jak pluralita potřebuje tři různé překlady:</span><span class="sxs-lookup"><span data-stu-id="9a630-266">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/cs.po)]

<span data-ttu-id="9a630-267">Chcete-li přijmout Česká lokalizace, přidejte `"cs"` do seznamu podporovaných kultur v `ConfigureServices` metodě:</span><span class="sxs-lookup"><span data-stu-id="9a630-267">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

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

<span data-ttu-id="9a630-268">Upravte *zobrazení/domů/o soubor. cshtml* pro vykreslování lokalizovaných řetězců plural pro několik mohutnosti:</span><span class="sxs-lookup"><span data-stu-id="9a630-268">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="9a630-269">**Poznámka:** Ve scénáři reálného světa by se pro reprezentaci počtu použila proměnná.</span><span class="sxs-lookup"><span data-stu-id="9a630-269">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="9a630-270">Tady zopakujeme stejný kód se třemi různými hodnotami, aby se vystavoval velmi konkrétní případ.</span><span class="sxs-lookup"><span data-stu-id="9a630-270">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="9a630-271">Po přepnutí na jazykové verze se zobrazí následující:</span><span class="sxs-lookup"><span data-stu-id="9a630-271">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="9a630-272">Pro `/Home/About`:</span><span class="sxs-lookup"><span data-stu-id="9a630-272">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="9a630-273">Pro `/Home/About?culture=fr`:</span><span class="sxs-lookup"><span data-stu-id="9a630-273">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="9a630-274">Pro `/Home/About?culture=cs`:</span><span class="sxs-lookup"><span data-stu-id="9a630-274">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="9a630-275">Všimněte si, že pro českou kulturu se tři překlady liší.</span><span class="sxs-lookup"><span data-stu-id="9a630-275">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="9a630-276">Francouzské a anglické jazykové verze sdílejí stejnou konstrukci pro dva poslední přeložené řetězce.</span><span class="sxs-lookup"><span data-stu-id="9a630-276">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="9a630-277">Pokročilé úlohy</span><span class="sxs-lookup"><span data-stu-id="9a630-277">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="9a630-278">Contextualizing řetězce</span><span class="sxs-lookup"><span data-stu-id="9a630-278">Contextualizing strings</span></span>

<span data-ttu-id="9a630-279">Aplikace často obsahují řetězce, které mají být přeloženy na několika místech.</span><span class="sxs-lookup"><span data-stu-id="9a630-279">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="9a630-280">Stejný řetězec může mít jiný překlad v určitých umístěních v rámci aplikace ( Razor zobrazení nebo soubory tříd).</span><span class="sxs-lookup"><span data-stu-id="9a630-280">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="9a630-281">Soubor. No podporuje pojem kontext souboru, který lze použít ke kategorizaci reprezentovaného řetězce.</span><span class="sxs-lookup"><span data-stu-id="9a630-281">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="9a630-282">Pomocí kontextu souboru lze řetězec přeložit odlišně v závislosti na kontextu souboru (nebo nedostatku kontextu souboru).</span><span class="sxs-lookup"><span data-stu-id="9a630-282">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="9a630-283">Lokalizační služby nákupních objednávek používají název celé třídy nebo zobrazení, které se používá při překladu řetězce.</span><span class="sxs-lookup"><span data-stu-id="9a630-283">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span> <span data-ttu-id="9a630-284">Toho je možné dosáhnout nastavením hodnoty `msgctxt` položky.</span><span class="sxs-lookup"><span data-stu-id="9a630-284">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="9a630-285">Zvažte menší doplněk k předchozímu příkladu *fr. po* .</span><span class="sxs-lookup"><span data-stu-id="9a630-285">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="9a630-286">RazorZobrazení, které se nachází na *stránce zobrazení/domů/o. cshtml* , lze definovat jako kontext souboru nastavením `msgctxt` hodnoty rezervované položky:</span><span class="sxs-lookup"><span data-stu-id="9a630-286">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="9a630-287">Když se `msgctxt` nastaví jako takové, překlad textu se objeví při přechodu na `/Home/About?culture=fr-FR` .</span><span class="sxs-lookup"><span data-stu-id="9a630-287">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="9a630-288">K překladu nedojde při přechodu na `/Home/Contact?culture=fr-FR` .</span><span class="sxs-lookup"><span data-stu-id="9a630-288">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="9a630-289">Pokud se zadaným kontextem souboru nevyhovuje žádná konkrétní položka, záložní mechanizmus sady sadů vyhledá příslušný soubor. No bez kontextu.</span><span class="sxs-lookup"><span data-stu-id="9a630-289">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="9a630-290">Za předpokladu, že není definován žádný konkrétní kontext souboru pro *views/Home/Contact. cshtml*, přejděte k `/Home/Contact?culture=fr-FR` načtení souboru objednávky, například:</span><span class="sxs-lookup"><span data-stu-id="9a630-290">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="9a630-291">Změna umístění souborů PO.</span><span class="sxs-lookup"><span data-stu-id="9a630-291">Changing the location of PO files</span></span>

<span data-ttu-id="9a630-292">Výchozí umístění souborů PO lze změnit v `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9a630-292">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="9a630-293">V tomto příkladu jsou soubory PO načtení načteny ze složky *lokalizace* .</span><span class="sxs-lookup"><span data-stu-id="9a630-293">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="9a630-294">Implementace vlastní logiky pro hledání souborů lokalizace</span><span class="sxs-lookup"><span data-stu-id="9a630-294">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="9a630-295">Pokud je pro vyhledání souborů na místě potřeba složitější logika, `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` rozhraní se dá implementovat a zaregistrovat jako služba.</span><span class="sxs-lookup"><span data-stu-id="9a630-295">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="9a630-296">To je užitečné v případě, že soubory. mohou být uloženy v různých umístěních nebo v případě, že je třeba soubory najít v rámci hierarchie složek.</span><span class="sxs-lookup"><span data-stu-id="9a630-296">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="9a630-297">Použití jiného výchozího množném jazyku</span><span class="sxs-lookup"><span data-stu-id="9a630-297">Using a different default pluralized language</span></span>

<span data-ttu-id="9a630-298">Balíček obsahuje `Plural` metodu rozšíření, která je specifická pro dva množné formy.</span><span class="sxs-lookup"><span data-stu-id="9a630-298">The package includes a `Plural` extension method that's specific to two plural forms.</span></span> <span data-ttu-id="9a630-299">Pro jazyky, které vyžadují více formulářů plural, vytvořte metodu rozšíření.</span><span class="sxs-lookup"><span data-stu-id="9a630-299">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="9a630-300">V případě metody rozšíření nemusíte pro výchozí jazyk zadat žádný soubor lokalizace, protože &mdash; původní řetězce jsou již k dispozici přímo v kódu.</span><span class="sxs-lookup"><span data-stu-id="9a630-300">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="9a630-301">Můžete použít obecnější `Plural(int count, string[] pluralForms, params object[] arguments)` přetížení, které přijímá pole řetězců překladu.</span><span class="sxs-lookup"><span data-stu-id="9a630-301">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>

::: moniker-end
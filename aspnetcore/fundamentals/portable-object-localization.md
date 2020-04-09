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
# <a name="configure-portable-object-localization-in-aspnet-core"></a><span data-ttu-id="cfe94-103">Konfigurace lokalizace přenosných objektů v ASP.NET Jádra</span><span class="sxs-lookup"><span data-stu-id="cfe94-103">Configure portable object localization in ASP.NET Core</span></span>

<span data-ttu-id="cfe94-104">Podle [Sébastien Ros](https://github.com/sebastienros) a [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="cfe94-104">By [Sébastien Ros](https://github.com/sebastienros) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="cfe94-105">Tento článek vás provede kroky pro použití souborů přenosného objektu (PO) v ASP.NET základní aplikace s architekturou [Orchard Core.](https://github.com/OrchardCMS/OrchardCore)</span><span class="sxs-lookup"><span data-stu-id="cfe94-105">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="cfe94-106">**Poznámka:** Orchard Core není produkt společnosti Microsoft.</span><span class="sxs-lookup"><span data-stu-id="cfe94-106">**Note:** Orchard Core isn't a Microsoft product.</span></span> <span data-ttu-id="cfe94-107">V důsledku toho společnost Microsoft neposkytuje žádnou podporu pro tuto funkci.</span><span class="sxs-lookup"><span data-stu-id="cfe94-107">Consequently, Microsoft provides no support for this feature.</span></span>

<span data-ttu-id="cfe94-108">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="cfe94-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="cfe94-109">Co je soubor PO?</span><span class="sxs-lookup"><span data-stu-id="cfe94-109">What is a PO file?</span></span>

<span data-ttu-id="cfe94-110">Soubory PO jsou distribuovány jako textové soubory obsahující přeložené řetězce pro daný jazyk.</span><span class="sxs-lookup"><span data-stu-id="cfe94-110">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="cfe94-111">Mezi výhody použití souborů PO místo *.resx* patří:</span><span class="sxs-lookup"><span data-stu-id="cfe94-111">Some advantages of using PO files instead *.resx* files include:</span></span>
- <span data-ttu-id="cfe94-112">Soubory PO podporují pluralizaci; *Soubory RESX* nepodporují pluralizaci.</span><span class="sxs-lookup"><span data-stu-id="cfe94-112">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="cfe94-113">Soubory PO nejsou kompilovány jako soubory *RESX.*</span><span class="sxs-lookup"><span data-stu-id="cfe94-113">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="cfe94-114">Jako takové nejsou vyžadovány specializované nástroje a kroky sestavení.</span><span class="sxs-lookup"><span data-stu-id="cfe94-114">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="cfe94-115">PO soubory dobře pracovat s kolaborativní on-line editační nástroje.</span><span class="sxs-lookup"><span data-stu-id="cfe94-115">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="cfe94-116">Příklad</span><span class="sxs-lookup"><span data-stu-id="cfe94-116">Example</span></span>

<span data-ttu-id="cfe94-117">Zde je ukázkový soubor PO obsahující překlad pro dva řetězce ve francouzštině, včetně jednoho s množným číslem:</span><span class="sxs-lookup"><span data-stu-id="cfe94-117">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="cfe94-118">*fr.po*</span><span class="sxs-lookup"><span data-stu-id="cfe94-118">*fr.po*</span></span>

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

<span data-ttu-id="cfe94-119">Tento příklad používá následující syntaxi:</span><span class="sxs-lookup"><span data-stu-id="cfe94-119">This example uses the following syntax:</span></span>

- <span data-ttu-id="cfe94-120">`#:`: Komentář označující kontext řetězce, který má být přeložen.</span><span class="sxs-lookup"><span data-stu-id="cfe94-120">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="cfe94-121">Stejný řetězec může být přeložen odlišně v závislosti na tom, kde se používá.</span><span class="sxs-lookup"><span data-stu-id="cfe94-121">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="cfe94-122">`msgid`: Nepřeložený řetězec.</span><span class="sxs-lookup"><span data-stu-id="cfe94-122">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="cfe94-123">`msgstr`: Přeložený řetězec.</span><span class="sxs-lookup"><span data-stu-id="cfe94-123">`msgstr`: The translated string.</span></span>

<span data-ttu-id="cfe94-124">V případě podpory pluralizace lze definovat více položek.</span><span class="sxs-lookup"><span data-stu-id="cfe94-124">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="cfe94-125">`msgid_plural`: Nepřeložený řetězec množného čísla.</span><span class="sxs-lookup"><span data-stu-id="cfe94-125">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="cfe94-126">`msgstr[0]`: Přeložený řetězec pro případ 0.</span><span class="sxs-lookup"><span data-stu-id="cfe94-126">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="cfe94-127">`msgstr[N]`: Přeložený řetězec pro případ N.</span><span class="sxs-lookup"><span data-stu-id="cfe94-127">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="cfe94-128">Specifikace souboru PO naleznete [zde](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span><span class="sxs-lookup"><span data-stu-id="cfe94-128">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="cfe94-129">Konfigurace podpory souborů PO v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cfe94-129">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="cfe94-130">Tento příklad je založen na aplikaci ASP.NET Core MVC generované ze šablony projektu Sady Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="cfe94-130">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="cfe94-131">Odkazování na balíček</span><span class="sxs-lookup"><span data-stu-id="cfe94-131">Referencing the package</span></span>

<span data-ttu-id="cfe94-132">Přidejte odkaz `OrchardCore.Localization.Core` na balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="cfe94-132">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span> <span data-ttu-id="cfe94-133">Je k dispozici na [MyGet](https://www.myget.org/) na následující zdroj balíčku:https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span><span class="sxs-lookup"><span data-stu-id="cfe94-133">It's available on [MyGet](https://www.myget.org/) at the following package source: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span></span>

<span data-ttu-id="cfe94-134">Soubor *.csproj* nyní obsahuje řádek podobný následujícímu (číslo verze se může lišit):</span><span class="sxs-lookup"><span data-stu-id="cfe94-134">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[](localization/sample/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a><span data-ttu-id="cfe94-135">Registrace služby</span><span class="sxs-lookup"><span data-stu-id="cfe94-135">Registering the service</span></span>

<span data-ttu-id="cfe94-136">Přidejte požadované služby k metodě `ConfigureServices` *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="cfe94-136">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="cfe94-137">Přidejte požadovaný middleware `Configure` k metodě *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="cfe94-137">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="cfe94-138">Přidejte do svého zvoleného zobrazení Razor následující kód.</span><span class="sxs-lookup"><span data-stu-id="cfe94-138">Add the following code to your Razor view of choice.</span></span> <span data-ttu-id="cfe94-139">*About.cshtml* se používá v tomto příkladu.</span><span class="sxs-lookup"><span data-stu-id="cfe94-139">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[](localization/sample/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="cfe94-140">Instance `IViewLocalizer` je injektována a slouží k překladu textu "Hello world!".</span><span class="sxs-lookup"><span data-stu-id="cfe94-140">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="cfe94-141">Vytvoření souboru PO</span><span class="sxs-lookup"><span data-stu-id="cfe94-141">Creating a PO file</span></span>

<span data-ttu-id="cfe94-142">Vytvořte soubor s názvem \* \<kód jazykové verze>.po\* v kořenové složce aplikace.</span><span class="sxs-lookup"><span data-stu-id="cfe94-142">Create a file named *\<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="cfe94-143">V tomto příkladu je název souboru *fr.po,* protože se používá francouzský jazyk:</span><span class="sxs-lookup"><span data-stu-id="cfe94-143">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[](localization/sample/POLocalization/fr.po)]

<span data-ttu-id="cfe94-144">Tento soubor ukládá řetězec přeložit a francouzský přeložený řetězec.</span><span class="sxs-lookup"><span data-stu-id="cfe94-144">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="cfe94-145">Překlady vrátit do jejich nadřazené jazykové verze, v případě potřeby.</span><span class="sxs-lookup"><span data-stu-id="cfe94-145">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="cfe94-146">V tomto příkladu se soubor *fr.po* používá, pokud je `fr-FR` požadovaná jazyková verze nebo `fr-CA`.</span><span class="sxs-lookup"><span data-stu-id="cfe94-146">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="cfe94-147">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="cfe94-147">Testing the application</span></span>

<span data-ttu-id="cfe94-148">Spusťte aplikaci a `/Home/About`přejděte na adresu URL .</span><span class="sxs-lookup"><span data-stu-id="cfe94-148">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="cfe94-149">Text **Hello world!**</span><span class="sxs-lookup"><span data-stu-id="cfe94-149">The text **Hello world!**</span></span> <span data-ttu-id="cfe94-150">se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="cfe94-150">is displayed.</span></span>

<span data-ttu-id="cfe94-151">Přejděte na `/Home/About?culture=fr-FR`adresu URL .</span><span class="sxs-lookup"><span data-stu-id="cfe94-151">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="cfe94-152">Text **Bonjour le monde!**</span><span class="sxs-lookup"><span data-stu-id="cfe94-152">The text **Bonjour le monde!**</span></span> <span data-ttu-id="cfe94-153">se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="cfe94-153">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="cfe94-154">Pluralizace</span><span class="sxs-lookup"><span data-stu-id="cfe94-154">Pluralization</span></span>

<span data-ttu-id="cfe94-155">Soubory PO podporují pluralizační formuláře, což je užitečné, když stejný řetězec musí být přeložen odlišně na základě mohutnosti.</span><span class="sxs-lookup"><span data-stu-id="cfe94-155">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="cfe94-156">Tento úkol je komplikován skutečností, že každý jazyk definuje vlastní pravidla pro výběr řetězce, který má být používán na základě mohutnosti.</span><span class="sxs-lookup"><span data-stu-id="cfe94-156">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="cfe94-157">Balíček orchard lokalizace poskytuje rozhraní API pro automatické vyvolání těchto různých formulářů množného čísla.</span><span class="sxs-lookup"><span data-stu-id="cfe94-157">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="cfe94-158">Vytváření pluralizačních souborů PO</span><span class="sxs-lookup"><span data-stu-id="cfe94-158">Creating pluralization PO files</span></span>

<span data-ttu-id="cfe94-159">Přidejte do výše uvedeného souboru *fr.po* následující obsah:</span><span class="sxs-lookup"><span data-stu-id="cfe94-159">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="cfe94-160">Viz [Co je soubor PO?](#what-is-a-po-file) vysvětlení, co každá položka v tomto příkladu představuje.</span><span class="sxs-lookup"><span data-stu-id="cfe94-160">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="cfe94-161">Přidání jazyka pomocí různých pluralizačních formulářů</span><span class="sxs-lookup"><span data-stu-id="cfe94-161">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="cfe94-162">V předchozím příkladu byly použity anglické a francouzské řetězce.</span><span class="sxs-lookup"><span data-stu-id="cfe94-162">English and French strings were used in the previous example.</span></span> <span data-ttu-id="cfe94-163">Angličtina a francouzština mají pouze dvě formy pluralizace a sdílejí stejná pravidla formuláře, což je, že mohutnost jednoho je mapována na první množné číslo formuláře.</span><span class="sxs-lookup"><span data-stu-id="cfe94-163">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="cfe94-164">Všechny ostatní mohutnost je mapována na druhý množný formulář.</span><span class="sxs-lookup"><span data-stu-id="cfe94-164">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="cfe94-165">Ne všechny jazyky sdílejí stejná pravidla.</span><span class="sxs-lookup"><span data-stu-id="cfe94-165">Not all languages share the same rules.</span></span> <span data-ttu-id="cfe94-166">To je ilustrováno českým jazykem, který má tři plurální formy.</span><span class="sxs-lookup"><span data-stu-id="cfe94-166">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="cfe94-167">Vytvořte `cs.po` soubor následujícím způsobem a všimněte si, jak pluralizace potřebuje tři různé překlady:</span><span class="sxs-lookup"><span data-stu-id="cfe94-167">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[](localization/sample/POLocalization/cs.po)]

<span data-ttu-id="cfe94-168">Chcete-li přijmout české `"cs"` lokalizace, přidejte do `ConfigureServices` seznamu podporovaných kultur v metodě:</span><span class="sxs-lookup"><span data-stu-id="cfe94-168">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

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

<span data-ttu-id="cfe94-169">Upravte soubor *Zobrazení/Domů/About.cshtml* tak, aby vykresloval lokalizované řetězce v množném čísle pro několik kardinálit:</span><span class="sxs-lookup"><span data-stu-id="cfe94-169">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="cfe94-170">**Poznámka:** V reálném scénáři by proměnná použít k reprezentaci počtu.</span><span class="sxs-lookup"><span data-stu-id="cfe94-170">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="cfe94-171">Zde opakujeme stejný kód se třemi různými hodnotami, abychom odhalili velmi specifický případ.</span><span class="sxs-lookup"><span data-stu-id="cfe94-171">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="cfe94-172">Při přepínání kultur se zobrazí následující:</span><span class="sxs-lookup"><span data-stu-id="cfe94-172">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="cfe94-173">Příkaz `/Home/About`:</span><span class="sxs-lookup"><span data-stu-id="cfe94-173">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="cfe94-174">Příkaz `/Home/About?culture=fr`:</span><span class="sxs-lookup"><span data-stu-id="cfe94-174">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="cfe94-175">Příkaz `/Home/About?culture=cs`:</span><span class="sxs-lookup"><span data-stu-id="cfe94-175">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="cfe94-176">Všimněte si, že pro českou kulturu jsou tři překlady odlišné.</span><span class="sxs-lookup"><span data-stu-id="cfe94-176">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="cfe94-177">Francouzská a anglická kultura sdílejí stejnou konstrukci pro dva poslední přeložené řetězce.</span><span class="sxs-lookup"><span data-stu-id="cfe94-177">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="cfe94-178">Pokročilé úlohy</span><span class="sxs-lookup"><span data-stu-id="cfe94-178">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="cfe94-179">Kontextové řetězce</span><span class="sxs-lookup"><span data-stu-id="cfe94-179">Contextualizing strings</span></span>

<span data-ttu-id="cfe94-180">Aplikace často obsahují řetězce, které mají být přeloženy na několika místech.</span><span class="sxs-lookup"><span data-stu-id="cfe94-180">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="cfe94-181">Stejný řetězec může mít jiný překlad v určitých umístěních v rámci aplikace (Razor zobrazení nebo soubory tříd).</span><span class="sxs-lookup"><span data-stu-id="cfe94-181">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="cfe94-182">Soubor PO podporuje pojem kontextu souboru, který lze použít ke kategorizaci řetězce, který je reprezentován.</span><span class="sxs-lookup"><span data-stu-id="cfe94-182">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="cfe94-183">Pomocí kontextu souboru řetězec lze přeložit odlišně, v závislosti na kontextu souboru (nebo nedostatek kontextu souboru).</span><span class="sxs-lookup"><span data-stu-id="cfe94-183">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="cfe94-184">Služby lokalizace PO používají název celé třídy nebo zobrazení, které se používá při překladu řetězce.</span><span class="sxs-lookup"><span data-stu-id="cfe94-184">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span> <span data-ttu-id="cfe94-185">Toho lze dosáhnout nastavením hodnoty `msgctxt` v položce.</span><span class="sxs-lookup"><span data-stu-id="cfe94-185">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="cfe94-186">Vezměme si menší přírůstek k předchozímu příkladu *fr.po.*</span><span class="sxs-lookup"><span data-stu-id="cfe94-186">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="cfe94-187">Zobrazení Razor umístěné na *adrese Views/Home/About.cshtml* lze definovat jako `msgctxt` kontext souboru nastavením hodnoty rezervované položky:</span><span class="sxs-lookup"><span data-stu-id="cfe94-187">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="cfe94-188">S `msgctxt` sadou jako takovou dochází k překladu textu při navigaci na . `/Home/About?culture=fr-FR`</span><span class="sxs-lookup"><span data-stu-id="cfe94-188">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="cfe94-189">K překladu nedojde při navigaci `/Home/Contact?culture=fr-FR`do aplikace .</span><span class="sxs-lookup"><span data-stu-id="cfe94-189">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="cfe94-190">Pokud žádná konkrétní položka je uzavřeno s daným kontextem souboru, Orchard Core záložní mechanismus hledá odpovídající soubor PO bez kontextu.</span><span class="sxs-lookup"><span data-stu-id="cfe94-190">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="cfe94-191">Za předpokladu, že pro *zobrazení/Home/Contact.cshtml*není definován `/Home/Contact?culture=fr-FR` žádný konkrétní kontext souboru , načte se soubor PO, například:</span><span class="sxs-lookup"><span data-stu-id="cfe94-191">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[](localization/sample/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="cfe94-192">Změna umístění souborů PO</span><span class="sxs-lookup"><span data-stu-id="cfe94-192">Changing the location of PO files</span></span>

<span data-ttu-id="cfe94-193">Výchozí umístění souborů PO lze `ConfigureServices`změnit v :</span><span class="sxs-lookup"><span data-stu-id="cfe94-193">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="cfe94-194">V tomto příkladu jsou soubory PO načteny ze složky *Lokalizace.*</span><span class="sxs-lookup"><span data-stu-id="cfe94-194">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="cfe94-195">Implementace vlastní logiky pro hledání lokalizačních souborů</span><span class="sxs-lookup"><span data-stu-id="cfe94-195">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="cfe94-196">Pokud je k vyhledání souborů `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` nákupních opojení zapotřebí složitější logika, může být rozhraní implementováno a zaregistrováno jako služba.</span><span class="sxs-lookup"><span data-stu-id="cfe94-196">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="cfe94-197">To je užitečné, když soubory PO mohou být uloženy v různých umístěních nebo když soubory musí být nalezeny v hierarchii složek.</span><span class="sxs-lookup"><span data-stu-id="cfe94-197">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="cfe94-198">Použití jiného výchozího pluralitního jazyka</span><span class="sxs-lookup"><span data-stu-id="cfe94-198">Using a different default pluralized language</span></span>

<span data-ttu-id="cfe94-199">Balíček obsahuje `Plural` metodu rozšíření, která je specifická pro dvě formy množného čísla.</span><span class="sxs-lookup"><span data-stu-id="cfe94-199">The package includes a `Plural` extension method that's specific to two plural forms.</span></span> <span data-ttu-id="cfe94-200">Pro jazyky, které vyžadují více formulářů množného čísla, vytvořte metodu rozšíření.</span><span class="sxs-lookup"><span data-stu-id="cfe94-200">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="cfe94-201">S metodou rozšíření nebudete muset poskytnout žádný lokalizační soubor &mdash; pro výchozí jazyk, původní řetězce jsou již k dispozici přímo v kódu.</span><span class="sxs-lookup"><span data-stu-id="cfe94-201">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="cfe94-202">Můžete použít obecnější `Plural(int count, string[] pluralForms, params object[] arguments)` přetížení, které přijímá pole řetězců překladů.</span><span class="sxs-lookup"><span data-stu-id="cfe94-202">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>

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
# <a name="configure-portable-object-localization-in-aspnet-core"></a><span data-ttu-id="128a2-103">Konfigurace lokalizace přenosné objektu v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="128a2-103">Configure portable object localization in ASP.NET Core</span></span>

<span data-ttu-id="128a2-104">Podle [Sébastien železnic](https://github.com/sebastienros) a [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="128a2-104">By [Sébastien Ros](https://github.com/sebastienros) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="128a2-105">Tento článek vás provede kroky pro použití souborů Portable Object (PO) v aplikaci ASP.NET Core s [Orchard Core](https://github.com/OrchardCMS/OrchardCore) rozhraní framework.</span><span class="sxs-lookup"><span data-stu-id="128a2-105">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="128a2-106">**Poznámka:** Orchard Core není produktů společnosti Microsoft.</span><span class="sxs-lookup"><span data-stu-id="128a2-106">**Note:** Orchard Core isn't a Microsoft product.</span></span> <span data-ttu-id="128a2-107">V důsledku toho společnost Microsoft poskytuje pro tuto funkci nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="128a2-107">Consequently, Microsoft provides no support for this feature.</span></span>

<span data-ttu-id="128a2-108">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="128a2-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="128a2-109">Co je soubor PO?</span><span class="sxs-lookup"><span data-stu-id="128a2-109">What is a PO file?</span></span>

<span data-ttu-id="128a2-110">Soubory PO se distribuují jako textové soubory obsahující přeložené řetězce pro daný jazyk.</span><span class="sxs-lookup"><span data-stu-id="128a2-110">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="128a2-111">Některé výhody použití PO soubory místo toho *RESX* soubory zahrnují:</span><span class="sxs-lookup"><span data-stu-id="128a2-111">Some advantages of using PO files instead *.resx* files include:</span></span>
- <span data-ttu-id="128a2-112">Soubory PO podporují pluralizace; *RESX* soubory nepodporují pluralizace.</span><span class="sxs-lookup"><span data-stu-id="128a2-112">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="128a2-113">PO soubory nejsou kompilovány jako *RESX* soubory.</span><span class="sxs-lookup"><span data-stu-id="128a2-113">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="128a2-114">V důsledku toho nejsou požadované kroky specializované nástroje a sestavení.</span><span class="sxs-lookup"><span data-stu-id="128a2-114">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="128a2-115">Soubory PO dobře pracovat spolupráci online nástroje pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="128a2-115">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="128a2-116">Příklad</span><span class="sxs-lookup"><span data-stu-id="128a2-116">Example</span></span>

<span data-ttu-id="128a2-117">Tady je ukázkový soubor PO obsahující překlad pro dva řetězce ve francouzštině, včetně s jeho množné číslo vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="128a2-117">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="128a2-118">*fr.po*</span><span class="sxs-lookup"><span data-stu-id="128a2-118">*fr.po*</span></span>

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

<span data-ttu-id="128a2-119">Tento příklad používá následující syntaxi:</span><span class="sxs-lookup"><span data-stu-id="128a2-119">This example uses the following syntax:</span></span>

- <span data-ttu-id="128a2-120">`#:`: Komentář označující kontextu řetězec, který má být přeložen.</span><span class="sxs-lookup"><span data-stu-id="128a2-120">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="128a2-121">Stejný řetězec může přeložit odlišně v závislosti na tom, kde se používá.</span><span class="sxs-lookup"><span data-stu-id="128a2-121">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="128a2-122">`msgid`: Nepřeložené řetězce.</span><span class="sxs-lookup"><span data-stu-id="128a2-122">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="128a2-123">`msgstr`: Přeložené řetězce.</span><span class="sxs-lookup"><span data-stu-id="128a2-123">`msgstr`: The translated string.</span></span>

<span data-ttu-id="128a2-124">V případě podpory pluralizace lze definovat další položky.</span><span class="sxs-lookup"><span data-stu-id="128a2-124">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="128a2-125">`msgid_plural`: Nepřeložené řetězce množném čísle.</span><span class="sxs-lookup"><span data-stu-id="128a2-125">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="128a2-126">`msgstr[0]`: Přeložené řetězce pro případ 0.</span><span class="sxs-lookup"><span data-stu-id="128a2-126">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="128a2-127">`msgstr[N]`: Přeložené řetězce pro případ N.</span><span class="sxs-lookup"><span data-stu-id="128a2-127">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="128a2-128">Specifikace souboru PO najdete [tady](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span><span class="sxs-lookup"><span data-stu-id="128a2-128">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="128a2-129">Konfigurace PO podpora souborů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="128a2-129">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="128a2-130">Tento příklad je založen na aplikace ASP.NET Core MVC generován ze šablony projektů sady Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="128a2-130">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="128a2-131">Odkazování na balíček</span><span class="sxs-lookup"><span data-stu-id="128a2-131">Referencing the package</span></span>

<span data-ttu-id="128a2-132">Přidejte odkaz na `OrchardCore.Localization.Core` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="128a2-132">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span> <span data-ttu-id="128a2-133">Je k dispozici na [MyGet](https://www.myget.org/) na následující zdroje balíčku: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span><span class="sxs-lookup"><span data-stu-id="128a2-133">It's available on [MyGet](https://www.myget.org/) at the following package source: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span></span>

<span data-ttu-id="128a2-134">*.Csproj* souboru teď obsahuje jeden řádek podobný následujícímu (číslo verze se může lišit):</span><span class="sxs-lookup"><span data-stu-id="128a2-134">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[](localization/sample/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a><span data-ttu-id="128a2-135">Registrace služby</span><span class="sxs-lookup"><span data-stu-id="128a2-135">Registering the service</span></span>

<span data-ttu-id="128a2-136">Přidání požadovaných služeb, které mají `ConfigureServices` metoda *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="128a2-136">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="128a2-137">Přidejte požadované middlewaru, který má `Configure` metoda *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="128a2-137">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="128a2-138">Přidejte následující kód do zobrazení Razor podle výběru.</span><span class="sxs-lookup"><span data-stu-id="128a2-138">Add the following code to your Razor view of choice.</span></span> <span data-ttu-id="128a2-139">*About.cshtml* se používá v tomto příkladu.</span><span class="sxs-lookup"><span data-stu-id="128a2-139">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[](localization/sample/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="128a2-140">`IViewLocalizer` Instance je vložený a používat pro překlad textu "Hello world!".</span><span class="sxs-lookup"><span data-stu-id="128a2-140">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="128a2-141">Vytváří se soubor PO</span><span class="sxs-lookup"><span data-stu-id="128a2-141">Creating a PO file</span></span>

<span data-ttu-id="128a2-142">Vytvořte soubor s názvem  *\<jazykovou verzi kódu > a* v kořenové složce aplikace.</span><span class="sxs-lookup"><span data-stu-id="128a2-142">Create a file named *\<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="128a2-143">V tomto příkladu je název souboru *fr.po* vzhledem k tomu, že se používá francouzštině:</span><span class="sxs-lookup"><span data-stu-id="128a2-143">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[](localization/sample/POLocalization/fr.po)]

<span data-ttu-id="128a2-144">Tento soubor obsahuje řetězec, který se převede a Francouzština přeložit řetězce.</span><span class="sxs-lookup"><span data-stu-id="128a2-144">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="128a2-145">Překlady vrátit k jejich nadřazená jazykové verze, v případě potřeby.</span><span class="sxs-lookup"><span data-stu-id="128a2-145">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="128a2-146">V tomto příkladu *fr.po* soubor se používá, pokud je požadovaná jazyková verze `fr-FR` nebo `fr-CA`.</span><span class="sxs-lookup"><span data-stu-id="128a2-146">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="128a2-147">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="128a2-147">Testing the application</span></span>

<span data-ttu-id="128a2-148">Spusťte aplikaci a přejděte na adresu URL `/Home/About`.</span><span class="sxs-lookup"><span data-stu-id="128a2-148">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="128a2-149">Text **Hello world!**</span><span class="sxs-lookup"><span data-stu-id="128a2-149">The text **Hello world!**</span></span> <span data-ttu-id="128a2-150">Zobrazí se.</span><span class="sxs-lookup"><span data-stu-id="128a2-150">is displayed.</span></span>

<span data-ttu-id="128a2-151">Přejděte na adresu URL `/Home/About?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="128a2-151">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="128a2-152">Text **Bonjour the World!**</span><span class="sxs-lookup"><span data-stu-id="128a2-152">The text **Bonjour le monde!**</span></span> <span data-ttu-id="128a2-153">Zobrazí se.</span><span class="sxs-lookup"><span data-stu-id="128a2-153">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="128a2-154">Pluralizace</span><span class="sxs-lookup"><span data-stu-id="128a2-154">Pluralization</span></span>

<span data-ttu-id="128a2-155">Soubory PO podporují pluralizace formulářů, což je užitečné, když do jednoho řetězce musí být přeložen různě v závislosti na kardinalitu.</span><span class="sxs-lookup"><span data-stu-id="128a2-155">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="128a2-156">Tato úloha se provádí složité fakt, že každý jazyk definuje vlastní pravidla k výběru, který řetězec určený podle kardinalitu.</span><span class="sxs-lookup"><span data-stu-id="128a2-156">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="128a2-157">Orchard lokalizační balíček poskytuje rozhraní API k vyvolání těchto různých podob množném čísle automaticky.</span><span class="sxs-lookup"><span data-stu-id="128a2-157">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="128a2-158">Vytváření souborů PO pluralizace</span><span class="sxs-lookup"><span data-stu-id="128a2-158">Creating pluralization PO files</span></span>

<span data-ttu-id="128a2-159">Přidejte následující obsah do výše uvedené *fr.po* souboru:</span><span class="sxs-lookup"><span data-stu-id="128a2-159">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="128a2-160">Zobrazit [co je soubor PO?](#what-is-a-po-file) pro vysvětlení, co představuje každé položky v tomto příkladu.</span><span class="sxs-lookup"><span data-stu-id="128a2-160">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="128a2-161">Přidání jazyk pomocí různých pluralizace formuláře</span><span class="sxs-lookup"><span data-stu-id="128a2-161">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="128a2-162">V předchozím příkladu byly použity řetězce angličtinu a francouzštinu.</span><span class="sxs-lookup"><span data-stu-id="128a2-162">English and French strings were used in the previous example.</span></span> <span data-ttu-id="128a2-163">Angličtinu a francouzštinu mít pouze dva formuláře pluralizace a sdílet stejná pravidla formulář, což je, že Kardinalita jednoho je namapována na první množný tvar.</span><span class="sxs-lookup"><span data-stu-id="128a2-163">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="128a2-164">Další Kardinalita se mapuje na druhý množný tvar.</span><span class="sxs-lookup"><span data-stu-id="128a2-164">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="128a2-165">Ne všechny jazyky sdílet stejná pravidla.</span><span class="sxs-lookup"><span data-stu-id="128a2-165">Not all languages share the same rules.</span></span> <span data-ttu-id="128a2-166">To je znázorněno České jazyk, který má tři množném čísle formuláře.</span><span class="sxs-lookup"><span data-stu-id="128a2-166">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="128a2-167">Vytvořte `cs.po` to následujícím způsobem a Všimněte si, jak pluralizace potřebuje tři odlišnému překladu:</span><span class="sxs-lookup"><span data-stu-id="128a2-167">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[](localization/sample/POLocalization/cs.po)]

<span data-ttu-id="128a2-168">Tak, aby přijímal České lokalizace, přidejte `"cs"` do seznamu podporovaných jazykových verzí v `ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="128a2-168">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

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

<span data-ttu-id="128a2-169">Upravit *Views/Home/About.cshtml* souboru k vykreslení lokalizované, plural řetězce pro několik kardinality:</span><span class="sxs-lookup"><span data-stu-id="128a2-169">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="128a2-170">**Poznámka:** V reálném scénáři by použít proměnnou představující počet.</span><span class="sxs-lookup"><span data-stu-id="128a2-170">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="128a2-171">Jsme zde, opakujte stejný kód s tři různé hodnoty k vystavení velmi zvláštní případ.</span><span class="sxs-lookup"><span data-stu-id="128a2-171">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="128a2-172">Při přepínání jazykových verzí, naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="128a2-172">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="128a2-173">Pro `/Home/About`:</span><span class="sxs-lookup"><span data-stu-id="128a2-173">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="128a2-174">Pro `/Home/About?culture=fr`:</span><span class="sxs-lookup"><span data-stu-id="128a2-174">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="128a2-175">Pro `/Home/About?culture=cs`:</span><span class="sxs-lookup"><span data-stu-id="128a2-175">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="128a2-176">Všimněte si, že pro Českou jazykovou verzi, se liší tři překlady.</span><span class="sxs-lookup"><span data-stu-id="128a2-176">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="128a2-177">Francouzština a anglické jazykové verze sdílet stejné konstrukce pro dvě poslední přeložené řetězce.</span><span class="sxs-lookup"><span data-stu-id="128a2-177">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="128a2-178">Pokročilé úlohy</span><span class="sxs-lookup"><span data-stu-id="128a2-178">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="128a2-179">Contextualizing řetězce</span><span class="sxs-lookup"><span data-stu-id="128a2-179">Contextualizing strings</span></span>

<span data-ttu-id="128a2-180">Aplikace často obsahují řetězce k převodu na několika místech.</span><span class="sxs-lookup"><span data-stu-id="128a2-180">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="128a2-181">Stejný řetězec může mít různé překlad v určitých umístěních v rámci aplikace (zobrazení syntaxe Razor nebo soubory třídy).</span><span class="sxs-lookup"><span data-stu-id="128a2-181">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="128a2-182">Soubor PO podporuje pojem kontext souboru, který slouží ke kategorizaci řetězců reprezentované.</span><span class="sxs-lookup"><span data-stu-id="128a2-182">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="128a2-183">Pomocí kontextu souboru, řetězce lze přeložit odlišně, v závislosti na kontextu souboru (nebo chybějící kontextu souboru).</span><span class="sxs-lookup"><span data-stu-id="128a2-183">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="128a2-184">PO lokalizační služby použijte název úplné třídy nebo zobrazení, které se používá při převodu na řetězec.</span><span class="sxs-lookup"><span data-stu-id="128a2-184">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span> <span data-ttu-id="128a2-185">Toho lze dosáhnout nastavením této hodnoty na `msgctxt` položka.</span><span class="sxs-lookup"><span data-stu-id="128a2-185">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="128a2-186">Vezměte v úvahu záležitostí drobné na předchozí *fr.po* příklad.</span><span class="sxs-lookup"><span data-stu-id="128a2-186">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="128a2-187">Zobrazení Razor umístění *Views/Home/About.cshtml* lze definovat jako kontext souboru tak, že nastavíte vyhrazený `msgctxt` hodnotu položky:</span><span class="sxs-lookup"><span data-stu-id="128a2-187">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="128a2-188">S `msgctxt` nastavit jako takové, překlad textu vyvolá se při přechodu na `/Home/About?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="128a2-188">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="128a2-189">Překlad nedojde, když přejdete na `/Home/Contact?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="128a2-189">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="128a2-190">Pokud žádná konkrétní položka je nalezena shoda s kontextem daný soubor, záložní mechanismus Orchard Core hledá odpovídající soubor PO bez kontextu.</span><span class="sxs-lookup"><span data-stu-id="128a2-190">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="128a2-191">Za předpokladu, že se žádný kontext konkrétní soubor definice pro *Views/Home/Contact.cshtml*, navigační k `/Home/Contact?culture=fr-FR` načte soubor PO, například:</span><span class="sxs-lookup"><span data-stu-id="128a2-191">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[](localization/sample/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="128a2-192">Změna umístění souborů PO</span><span class="sxs-lookup"><span data-stu-id="128a2-192">Changing the location of PO files</span></span>

<span data-ttu-id="128a2-193">Výchozí umístění souborů PO lze změnit v `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="128a2-193">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="128a2-194">V tomto příkladu jsou načteny PO soubory z *lokalizace* složky.</span><span class="sxs-lookup"><span data-stu-id="128a2-194">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="128a2-195">Implementace vlastní logiky pro vyhledávání souborů lokalizace</span><span class="sxs-lookup"><span data-stu-id="128a2-195">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="128a2-196">Když je potřeba složitější logiku pro nalezení souborů PO `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` rozhraní můžete implementovat a zaregistrovat jako služba.</span><span class="sxs-lookup"><span data-stu-id="128a2-196">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="128a2-197">To je užitečné, pokud se PO soubory mohou být uloženy v různých umístěních nebo pokud soubory mají být nalezen v hierarchii složek.</span><span class="sxs-lookup"><span data-stu-id="128a2-197">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="128a2-198">Použití různých pluralized výchozí jazyk</span><span class="sxs-lookup"><span data-stu-id="128a2-198">Using a different default pluralized language</span></span>

<span data-ttu-id="128a2-199">Balíček obsahuje `Plural` rozšiřující metoda, která je specifická pro dvě různými formami množném čísle.</span><span class="sxs-lookup"><span data-stu-id="128a2-199">The package includes a `Plural` extension method that's specific to two plural forms.</span></span> <span data-ttu-id="128a2-200">Pro jazyky, které vyžadují další množném vytvořte rozšiřující metoda.</span><span class="sxs-lookup"><span data-stu-id="128a2-200">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="128a2-201">Pomocí metody rozšíření, nebudete muset zadávat jakýkoli soubor lokalizace pro výchozí jazyk &mdash; původním řetězců jsou už k dispozici přímo v kódu.</span><span class="sxs-lookup"><span data-stu-id="128a2-201">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="128a2-202">Můžete použít další Obecné `Plural(int count, string[] pluralForms, params object[] arguments)` přetížení, která přijímá pole řetězců překladů.</span><span class="sxs-lookup"><span data-stu-id="128a2-202">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>

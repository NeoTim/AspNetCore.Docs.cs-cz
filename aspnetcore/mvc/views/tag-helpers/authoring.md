---
title: Vytváření značek pomocníků v ASP.NET Core
author: rick-anderson
description: Naučte se vytvářet pomocníky značek v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/authoring
ms.openlocfilehash: 0b60468b96ded559d180e7b3bf5f799ce2f4d7e3
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775086"
---
# <a name="author-tag-helpers-in-aspnet-core"></a><span data-ttu-id="c83c9-103">Vytváření značek pomocníků v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c83c9-103">Author Tag Helpers in ASP.NET Core</span></span>

<span data-ttu-id="c83c9-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c83c9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c83c9-105">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/authoring/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c83c9-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/authoring/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="get-started-with-tag-helpers"></a><span data-ttu-id="c83c9-106">Začínáme s pomocníky značek</span><span class="sxs-lookup"><span data-stu-id="c83c9-106">Get started with Tag Helpers</span></span>

<span data-ttu-id="c83c9-107">V tomto kurzu se seznámíte se sestavami programování značek.</span><span class="sxs-lookup"><span data-stu-id="c83c9-107">This tutorial provides an introduction to programming Tag Helpers.</span></span> <span data-ttu-id="c83c9-108">[Úvod do pomocníků značek](intro.md) popisuje výhody, které poskytují pomocníky pro tag.</span><span class="sxs-lookup"><span data-stu-id="c83c9-108">[Introduction to Tag Helpers](intro.md) describes the benefits that Tag Helpers provide.</span></span>

<span data-ttu-id="c83c9-109">Pomocný objekt tag je libovolná třída, která `ITagHelper` implementuje rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c83c9-109">A tag helper is any class that implements the `ITagHelper` interface.</span></span> <span data-ttu-id="c83c9-110">Nicméně při vytváření pomocné rutiny značky se všeobecně odvodit z `TagHelper`, pokud k tomu dojde, získáte přístup k `Process` metodě.</span><span class="sxs-lookup"><span data-stu-id="c83c9-110">However, when you author a tag helper, you generally derive from `TagHelper`, doing so gives you access to the `Process` method.</span></span>

1. <span data-ttu-id="c83c9-111">Vytvořte nový projekt ASP.NET Core s názvem **AuthoringTagHelpers**.</span><span class="sxs-lookup"><span data-stu-id="c83c9-111">Create a new ASP.NET Core project called **AuthoringTagHelpers**.</span></span> <span data-ttu-id="c83c9-112">Pro tento projekt nebudete potřebovat ověření.</span><span class="sxs-lookup"><span data-stu-id="c83c9-112">You won't need authentication for this project.</span></span>

1. <span data-ttu-id="c83c9-113">Vytvořte složku pro uložení pomocníků značek s názvem *TagHelpers*.</span><span class="sxs-lookup"><span data-stu-id="c83c9-113">Create a folder to hold the Tag Helpers called *TagHelpers*.</span></span> <span data-ttu-id="c83c9-114">Složka *TagHelpers* *není povinná* , ale jedná se o rozumnou konvenci.</span><span class="sxs-lookup"><span data-stu-id="c83c9-114">The *TagHelpers* folder is *not* required, but it's a reasonable convention.</span></span> <span data-ttu-id="c83c9-115">Teď začneme psát některé pomocníky jednoduchých značek.</span><span class="sxs-lookup"><span data-stu-id="c83c9-115">Now let's get started writing some simple tag helpers.</span></span>

## <a name="a-minimal-tag-helper"></a><span data-ttu-id="c83c9-116">Pomocný Pomocník s minimální značkou</span><span class="sxs-lookup"><span data-stu-id="c83c9-116">A minimal Tag Helper</span></span>

<span data-ttu-id="c83c9-117">V této části napíšete pomocníka značek, který aktualizuje značku e-mailu.</span><span class="sxs-lookup"><span data-stu-id="c83c9-117">In this section, you write a tag helper that updates an email tag.</span></span> <span data-ttu-id="c83c9-118">Příklad:</span><span class="sxs-lookup"><span data-stu-id="c83c9-118">For example:</span></span>

```html
<email>Support</email>
```

<span data-ttu-id="c83c9-119">Server použije pomocníka pro označení e-mailu k převedení tohoto označení na následující:</span><span class="sxs-lookup"><span data-stu-id="c83c9-119">The server will use our email tag helper to convert that markup into the following:</span></span>

```html
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

<span data-ttu-id="c83c9-120">To znamená, že značka kotvy vytvoří tento e-mailový odkaz.</span><span class="sxs-lookup"><span data-stu-id="c83c9-120">That is, an anchor tag that makes this an email link.</span></span> <span data-ttu-id="c83c9-121">To můžete chtít udělat, pokud píšete modul blogu a potřebujete ho poslat e-mailem pro marketing, podporu a další kontakty, a to vše ve stejné doméně.</span><span class="sxs-lookup"><span data-stu-id="c83c9-121">You might want to do this if you are writing a blog engine and need it to send email for marketing, support, and other contacts, all to the same domain.</span></span>

1. <span data-ttu-id="c83c9-122">Do složky `EmailTagHelper` *TagHelpers* přidejte následující třídu.</span><span class="sxs-lookup"><span data-stu-id="c83c9-122">Add the following `EmailTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1EmailTagHelperCopy.cs)]

   * <span data-ttu-id="c83c9-123">Pomocník značek používá konvenci pojmenování, která cílí na prvky názvu kořenové třídy (mínus část *taghelperu* názvu třídy).</span><span class="sxs-lookup"><span data-stu-id="c83c9-123">Tag helpers use a naming convention that targets elements of the root class name (minus the *TagHelper* portion of the class name).</span></span> <span data-ttu-id="c83c9-124">V tomto příkladu je kořenový název **EmailTagHelper** *e-mailem*, takže bude `<email>` cílová značka.</span><span class="sxs-lookup"><span data-stu-id="c83c9-124">In this example, the root name of **EmailTagHelper** is *email*, so the `<email>` tag will be targeted.</span></span> <span data-ttu-id="c83c9-125">Tato konvence vytváření názvů by měla fungovat pro většinu pomocníků se značkami, a to později v části jak ji přepsat.</span><span class="sxs-lookup"><span data-stu-id="c83c9-125">This naming convention should work for most tag helpers, later on I'll show how to override it.</span></span>

   * <span data-ttu-id="c83c9-126">`EmailTagHelper` Třída je odvozena z `TagHelper`.</span><span class="sxs-lookup"><span data-stu-id="c83c9-126">The `EmailTagHelper` class derives from `TagHelper`.</span></span> <span data-ttu-id="c83c9-127">`TagHelper` Třída poskytuje metody a vlastnosti pro psaní pomocníků značek.</span><span class="sxs-lookup"><span data-stu-id="c83c9-127">The `TagHelper` class provides methods and properties for writing Tag Helpers.</span></span>

   * <span data-ttu-id="c83c9-128">Přepsaná `Process` Metoda řídí, co má pomocník značek při spuštění.</span><span class="sxs-lookup"><span data-stu-id="c83c9-128">The overridden `Process` method controls what the tag helper does when executed.</span></span> <span data-ttu-id="c83c9-129">`TagHelper` Třída také poskytuje asynchronní verzi (`ProcessAsync`) se stejnými parametry.</span><span class="sxs-lookup"><span data-stu-id="c83c9-129">The `TagHelper` class also provides an asynchronous version (`ProcessAsync`) with the same parameters.</span></span>

   * <span data-ttu-id="c83c9-130">Kontextový parametr pro `Process` (a `ProcessAsync`) obsahuje informace spojené s prováděním aktuální značky HTML.</span><span class="sxs-lookup"><span data-stu-id="c83c9-130">The context parameter to `Process` (and `ProcessAsync`) contains information associated with the execution of the current HTML tag.</span></span>

   * <span data-ttu-id="c83c9-131">Výstupní parametr do `Process` (a `ProcessAsync`) obsahuje stavový zástupce prvku HTML původního zdroje použitého ke generování značek a obsahu jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="c83c9-131">The output parameter to `Process` (and `ProcessAsync`) contains a stateful HTML element representative of the original source used to generate an HTML tag and content.</span></span>

   * <span data-ttu-id="c83c9-132">Název naší třídy má příponu **taghelperu**, která *není požadována, ale je* považována za osvědčenou konvenci.</span><span class="sxs-lookup"><span data-stu-id="c83c9-132">Our class name has a suffix of **TagHelper**, which is *not* required, but it's considered a best practice convention.</span></span> <span data-ttu-id="c83c9-133">Třídu můžete deklarovat jako:</span><span class="sxs-lookup"><span data-stu-id="c83c9-133">You could declare the class as:</span></span>

   ```csharp
   public class Email : TagHelper
   ```

1. <span data-ttu-id="c83c9-134">Pro zpřístupnění `EmailTagHelper` třídy všem našim Razor zobrazením přidejte `addTagHelper` direktivu do souboru *views/_ViewImports. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="c83c9-134">To make the `EmailTagHelper` class available to all our Razor views, add the `addTagHelper` directive to the *Views/_ViewImports.cshtml* file:</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopyEmail.cshtml?highlight=2,3)]

   <span data-ttu-id="c83c9-135">Výše uvedený kód používá syntaxi zástupných znaků k určení všech pomocníků značek v našem sestavení bude k dispozici.</span><span class="sxs-lookup"><span data-stu-id="c83c9-135">The code above uses the wildcard syntax to specify all the tag helpers in our assembly will be available.</span></span> <span data-ttu-id="c83c9-136">První řetězec po `@addTagHelper` určení pomocné rutiny značky, která se má načíst (použijte \* pro všechny pomocné rutiny značky) a druhý řetězec "AuthoringTagHelpers" Určuje sestavení, ve kterém je pomocník značek.</span><span class="sxs-lookup"><span data-stu-id="c83c9-136">The first string after `@addTagHelper` specifies the tag helper to load (Use "\*" for all tag helpers), and the second string "AuthoringTagHelpers" specifies the assembly the tag helper is in.</span></span> <span data-ttu-id="c83c9-137">Všimněte si také, že druhý řádek přináší do ASP.NET Core pomocníka značek MVC pomocí syntaxe zástupných znaků (tyto pomocníky jsou popsány v tématu [Úvod k označení pomocníků se značkami](intro.md)). Je to `@addTagHelper` direktiva, která zpřístupňuje pomocníka značky pro Razor zobrazení.</span><span class="sxs-lookup"><span data-stu-id="c83c9-137">Also, note that the second line brings in the ASP.NET Core MVC tag helpers using the wildcard syntax (those helpers are discussed in [Introduction to Tag Helpers](intro.md).) It's the `@addTagHelper` directive that makes the tag helper available to the Razor view.</span></span> <span data-ttu-id="c83c9-138">Alternativně můžete zadat plně kvalifikovaný název (FQN) pro pomocnou značku, jak je znázorněno níže:</span><span class="sxs-lookup"><span data-stu-id="c83c9-138">Alternatively, you can provide the fully qualified name (FQN) of a tag helper as shown below:</span></span>

```csharp
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

<!--
the following snippet uses TagHelpers3 and should use TagHelpers (not the 3)
    [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImports.cshtml?highlight=3&range=1-3)]
-->

<span data-ttu-id="c83c9-139">Chcete-li přidat pomocníka značek pro zobrazení pomocí FQN, nejprve přidejte FQN`AuthoringTagHelpers.TagHelpers.EmailTagHelper`() a potom **název sestavení** (*AuthoringTagHelpers*, ne nutně `namespace`).</span><span class="sxs-lookup"><span data-stu-id="c83c9-139">To add a tag helper to a view using a FQN, you first add the FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), and then the **assembly name** (*AuthoringTagHelpers*, not necessarily the `namespace`).</span></span> <span data-ttu-id="c83c9-140">Většina vývojářů bude chtít použít zástupnou syntaxi.</span><span class="sxs-lookup"><span data-stu-id="c83c9-140">Most developers will prefer to use the wildcard syntax.</span></span> <span data-ttu-id="c83c9-141">[Úvod k](intro.md) pomocníkům značek odkazuje na podrobné informace o přidání, odebrání, hierarchii a zástupné syntaxi pomocníka značek.</span><span class="sxs-lookup"><span data-stu-id="c83c9-141">[Introduction to Tag Helpers](intro.md) goes into detail on tag helper adding, removing, hierarchy, and wildcard syntax.</span></span>

1. <span data-ttu-id="c83c9-142">Aktualizujte kód v souboru *views/Home/Contact. cshtml* pomocí těchto změn:</span><span class="sxs-lookup"><span data-stu-id="c83c9-142">Update the markup in the *Views/Home/Contact.cshtml* file with these changes:</span></span>

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. <span data-ttu-id="c83c9-143">Spusťte aplikaci a použijte oblíbený prohlížeč k zobrazení zdroje HTML, abyste mohli ověřit, že jsou značky e-mailu nahrazeny značkou ukotvení (například `<a>Support</a>`).</span><span class="sxs-lookup"><span data-stu-id="c83c9-143">Run the app and use your favorite browser to view the HTML source so you can verify that the email tags are replaced with anchor markup (For example, `<a>Support</a>`).</span></span> <span data-ttu-id="c83c9-144">*Podpora* a *Marketing* se vykreslují jako odkazy, ale nemají `href` atribut, aby byly funkční.</span><span class="sxs-lookup"><span data-stu-id="c83c9-144">*Support* and *Marketing* are rendered as a links, but they don't have an `href` attribute to make them functional.</span></span> <span data-ttu-id="c83c9-145">Opravujeme ho v další části.</span><span class="sxs-lookup"><span data-stu-id="c83c9-145">We'll fix that in the next section.</span></span>

## <a name="setattribute-and-setcontent"></a><span data-ttu-id="c83c9-146">SetAttributes a SetContent</span><span class="sxs-lookup"><span data-stu-id="c83c9-146">SetAttribute and SetContent</span></span>

<span data-ttu-id="c83c9-147">V této části aktualizujeme `EmailTagHelper` , aby se vytvořila platná značka ukotvení pro e-mail.</span><span class="sxs-lookup"><span data-stu-id="c83c9-147">In this section, we'll update the `EmailTagHelper` so that it will create a valid anchor tag for email.</span></span> <span data-ttu-id="c83c9-148">Aktualizujeme ji, aby provedla informace Razor ze zobrazení (ve formě `mail-to` atributu) a použila ho při generování kotvy.</span><span class="sxs-lookup"><span data-stu-id="c83c9-148">We'll update it to take information from a Razor view (in the form of a `mail-to` attribute) and use that in generating the anchor.</span></span>

<span data-ttu-id="c83c9-149">Aktualizujte `EmailTagHelper` třídu následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="c83c9-149">Update the `EmailTagHelper` class with the following:</span></span>

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?range=6-22)]

* <span data-ttu-id="c83c9-150">Názvy tříd a vlastností jazyka Pascal-použita pro pomocníky značek jsou přeloženy do jejich [kebabho případu](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span><span class="sxs-lookup"><span data-stu-id="c83c9-150">Pascal-cased class and property names for tag helpers are translated into their [kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span></span> <span data-ttu-id="c83c9-151">Proto pro použití `MailTo` atributu použijete `<email mail-to="value"/>` ekvivalent.</span><span class="sxs-lookup"><span data-stu-id="c83c9-151">Therefore, to use the `MailTo` attribute, you'll use `<email mail-to="value"/>` equivalent.</span></span>

* <span data-ttu-id="c83c9-152">Poslední řádek nastaví dokončený obsah pro naši pomocníka s minimální funkční značkou.</span><span class="sxs-lookup"><span data-stu-id="c83c9-152">The last line sets the completed content for our minimally functional tag helper.</span></span>

* <span data-ttu-id="c83c9-153">Zvýrazněný řádek ukazuje syntaxi pro přidání atributů:</span><span class="sxs-lookup"><span data-stu-id="c83c9-153">The highlighted line shows the syntax for adding attributes:</span></span>

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?highlight=6&range=14-21)]

<span data-ttu-id="c83c9-154">Tento přístup funguje pro atribut "href", pokud aktuálně neexistuje v kolekci atributů.</span><span class="sxs-lookup"><span data-stu-id="c83c9-154">That approach works for the attribute "href" as long as it doesn't currently exist in the attributes collection.</span></span> <span data-ttu-id="c83c9-155">Můžete také použít `output.Attributes.Add` metodu pro přidání atributu pomocníka značky na konec kolekce atributů značek.</span><span class="sxs-lookup"><span data-stu-id="c83c9-155">You can also use the `output.Attributes.Add` method to add a tag helper attribute to the end of the collection of tag attributes.</span></span>

1. <span data-ttu-id="c83c9-156">Aktualizujte kód v souboru *views/Home/Contact. cshtml* pomocí těchto změn:</span><span class="sxs-lookup"><span data-stu-id="c83c9-156">Update the markup in the *Views/Home/Contact.cshtml* file with these changes:</span></span>

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/ContactCopy.cshtml?highlight=15,16)]

1. <span data-ttu-id="c83c9-157">Spusťte aplikaci a ověřte, že generuje správné odkazy.</span><span class="sxs-lookup"><span data-stu-id="c83c9-157">Run the app and verify that it generates the correct links.</span></span>

<a name="self-closing"></a>

   > [!NOTE]
   > <span data-ttu-id="c83c9-158">Pokud byste chtěli napsat označení e-mailu jako samoobslužné`<email mail-to="Rick" />`(), konečný výstup by taky měl samo uzavírací.</span><span class="sxs-lookup"><span data-stu-id="c83c9-158">If you were to write the email tag self-closing (`<email mail-to="Rick" />`), the final output would also be self-closing.</span></span> <span data-ttu-id="c83c9-159">Chcete-li povolit možnost psaní značky pouze pomocí počáteční značky (`<email mail-to="Rick">`), je nutné označit třídu následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="c83c9-159">To enable the ability to write the tag with only a start tag (`<email mail-to="Rick">`) you must mark the class with the following:</span></span>
   >
   > [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailVoid.cs?highlight=1&range=6-10)]

   <span data-ttu-id="c83c9-160">Pomocí samoobslužného pomocníka značek e-mailu by byl výstup `<a href="mailto:Rick@contoso.com" />`.</span><span class="sxs-lookup"><span data-stu-id="c83c9-160">With a self-closing email tag helper, the output would be `<a href="mailto:Rick@contoso.com" />`.</span></span> <span data-ttu-id="c83c9-161">Samouzavírací značky kotvy nejsou platné HTML, takže byste ji neměli chtít vytvořit, ale možná budete chtít vytvořit pomocníka značek, který je samo samouzavírací.</span><span class="sxs-lookup"><span data-stu-id="c83c9-161">Self-closing anchor tags are not valid HTML, so you wouldn't want to create one, but you might want to create a tag helper that's self-closing.</span></span> <span data-ttu-id="c83c9-162">Pomocník značek nastaví typ `TagMode` vlastnosti po čtení značky.</span><span class="sxs-lookup"><span data-stu-id="c83c9-162">Tag helpers set the type of the `TagMode` property after reading a tag.</span></span>

### <a name="processasync"></a><span data-ttu-id="c83c9-163">ProcessAsync</span><span class="sxs-lookup"><span data-stu-id="c83c9-163">ProcessAsync</span></span>

<span data-ttu-id="c83c9-164">V této části napíšeme pomocníka asynchronního e-mailu.</span><span class="sxs-lookup"><span data-stu-id="c83c9-164">In this section, we'll write an asynchronous email helper.</span></span>

1. <span data-ttu-id="c83c9-165">`EmailTagHelper` Třídu nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c83c9-165">Replace the `EmailTagHelper` class with the following code:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelper.cs?range=6-17)]

   <span data-ttu-id="c83c9-166">**Poznámka**</span><span class="sxs-lookup"><span data-stu-id="c83c9-166">**Notes:**</span></span>

   * <span data-ttu-id="c83c9-167">Tato verze používá asynchronní `ProcessAsync` metodu.</span><span class="sxs-lookup"><span data-stu-id="c83c9-167">This version uses the asynchronous `ProcessAsync` method.</span></span> <span data-ttu-id="c83c9-168">Asynchronní `GetChildContentAsync` vrátí objekt `Task` obsahující. `TagHelperContent`</span><span class="sxs-lookup"><span data-stu-id="c83c9-168">The asynchronous `GetChildContentAsync` returns a `Task` containing the `TagHelperContent`.</span></span>

   * <span data-ttu-id="c83c9-169">K získání `output` obsahu elementu HTML použijte parametr.</span><span class="sxs-lookup"><span data-stu-id="c83c9-169">Use the `output` parameter to get contents of the HTML element.</span></span>

1. <span data-ttu-id="c83c9-170">Proveďte následující změny v souboru *views/Home/Contact. cshtml* , aby pomocník značek mohl získat cílový e-mail.</span><span class="sxs-lookup"><span data-stu-id="c83c9-170">Make the following change to the *Views/Home/Contact.cshtml* file so the tag helper can get the target email.</span></span>

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. <span data-ttu-id="c83c9-171">Spusťte aplikaci a ověřte, že generuje platné e-mailové odkazy.</span><span class="sxs-lookup"><span data-stu-id="c83c9-171">Run the app and verify that it generates valid email links.</span></span>

### <a name="removeall-precontentsethtmlcontent-and-postcontentsethtmlcontent"></a><span data-ttu-id="c83c9-172">RemoveAll, Content. SetHtmlContent a PostContent. SetHtmlContent</span><span class="sxs-lookup"><span data-stu-id="c83c9-172">RemoveAll, PreContent.SetHtmlContent and PostContent.SetHtmlContent</span></span>

1. <span data-ttu-id="c83c9-173">Do složky `BoldTagHelper` *TagHelpers* přidejte následující třídu.</span><span class="sxs-lookup"><span data-stu-id="c83c9-173">Add the following `BoldTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/BoldTagHelper.cs)]

   * <span data-ttu-id="c83c9-174">`[HtmlTargetElement]` Atribut předává parametr atributu, který určuje, že všechny prvky jazyka HTML obsahující atribut HTML s názvem "bold" budou odpovídat a metoda `Process` override ve třídě bude spuštěna.</span><span class="sxs-lookup"><span data-stu-id="c83c9-174">The `[HtmlTargetElement]` attribute passes an attribute parameter that specifies that any HTML element that contains an HTML attribute named "bold" will match, and the `Process` override method in the class will run.</span></span> <span data-ttu-id="c83c9-175">V naší ukázce `Process` metoda odebere atribut "bold" a obklopí obsahující značku pomocí `<strong></strong>`.</span><span class="sxs-lookup"><span data-stu-id="c83c9-175">In our sample, the `Process` method removes the "bold" attribute and surrounds the containing markup with `<strong></strong>`.</span></span>

   * <span data-ttu-id="c83c9-176">Vzhledem k tomu, že nechcete nahradit existující obsah značky, je nutné zapsat počáteční `<strong>` značku s `PreContent.SetHtmlContent` metodou a uzavírací `</strong>` značkou s `PostContent.SetHtmlContent` metodou.</span><span class="sxs-lookup"><span data-stu-id="c83c9-176">Because you don't want to replace the existing tag content, you must write the opening `<strong>` tag with the `PreContent.SetHtmlContent` method and the closing `</strong>` tag with the `PostContent.SetHtmlContent` method.</span></span>

1. <span data-ttu-id="c83c9-177">Upravte zobrazení *About. cshtml* tak, aby obsahovalo hodnotu `bold` atributu.</span><span class="sxs-lookup"><span data-stu-id="c83c9-177">Modify the *About.cshtml* view to contain a `bold` attribute value.</span></span> <span data-ttu-id="c83c9-178">Dokončený kód je uveden níže.</span><span class="sxs-lookup"><span data-stu-id="c83c9-178">The completed code is shown below.</span></span>

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutBoldOnly.cshtml?highlight=7)]

1. <span data-ttu-id="c83c9-179">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c83c9-179">Run the app.</span></span> <span data-ttu-id="c83c9-180">Pomocí oblíbeného prohlížeče můžete zkontrolovat zdroj a ověřit označení.</span><span class="sxs-lookup"><span data-stu-id="c83c9-180">You can use your favorite browser to inspect the source and verify the markup.</span></span>

   <span data-ttu-id="c83c9-181">Atribut `[HtmlTargetElement]` uvedený výše se zaměřuje pouze na kód HTML, který poskytuje název atributu "bold".</span><span class="sxs-lookup"><span data-stu-id="c83c9-181">The `[HtmlTargetElement]` attribute above only targets HTML markup that provides an attribute name of "bold".</span></span> <span data-ttu-id="c83c9-182">`<bold>` Element nebyl změněn pomocníkem značek.</span><span class="sxs-lookup"><span data-stu-id="c83c9-182">The `<bold>` element wasn't modified by the tag helper.</span></span>

1. <span data-ttu-id="c83c9-183">Odkomentujte `[HtmlTargetElement]` řádek atributu a ve výchozím nastavení se zacílí `<bold>` na značky, tj. HTML značky formuláře. `<bold>`</span><span class="sxs-lookup"><span data-stu-id="c83c9-183">Comment out the `[HtmlTargetElement]` attribute line and it will default to targeting `<bold>` tags, that is, HTML markup of the form `<bold>`.</span></span> <span data-ttu-id="c83c9-184">Nezapomeňte, že výchozí konvence pojmenování budou odpovídat názvu třídy **tučně**taghelperu na `<bold>` značky.</span><span class="sxs-lookup"><span data-stu-id="c83c9-184">Remember, the default naming convention will match the class name **Bold**TagHelper to `<bold>` tags.</span></span>

1. <span data-ttu-id="c83c9-185">Spusťte aplikaci a ověřte, zda je `<bold>` značka zpracována pomocníkem značek.</span><span class="sxs-lookup"><span data-stu-id="c83c9-185">Run the app and verify that the `<bold>` tag is processed by the tag helper.</span></span>

<span data-ttu-id="c83c9-186">Upravení třídu s více `[HtmlTargetElement]` atributy má za následek logický nebo cílový.</span><span class="sxs-lookup"><span data-stu-id="c83c9-186">Decorating a class with multiple `[HtmlTargetElement]` attributes results in a logical-OR of the targets.</span></span> <span data-ttu-id="c83c9-187">Například pomocí následujícího kódu se bude shodovat značka tučného písma nebo tučného atributu.</span><span class="sxs-lookup"><span data-stu-id="c83c9-187">For example, using the code below, a bold tag or a bold attribute will match.</span></span>

[!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zBoldTagHelperCopy.cs?highlight=1,2&range=5-15)]

<span data-ttu-id="c83c9-188">Pokud je do stejného příkazu přidáno více atributů, modul runtime je považuje za logický a.</span><span class="sxs-lookup"><span data-stu-id="c83c9-188">When multiple attributes are added to the same statement, the runtime treats them as a logical-AND.</span></span> <span data-ttu-id="c83c9-189">Například v následujícím kódu musí být element HTML pojmenován tučně s atributem s názvem "bold" (`<bold bold />`), aby odpovídal.</span><span class="sxs-lookup"><span data-stu-id="c83c9-189">For example, in the code below, an HTML element must be named "bold" with an attribute named "bold" (`<bold bold />`) to match.</span></span>

```csharp
[HtmlTargetElement("bold", Attributes = "bold")]
   ```

<span data-ttu-id="c83c9-190">Můžete také použít `[HtmlTargetElement]` ke změně názvu cílového prvku.</span><span class="sxs-lookup"><span data-stu-id="c83c9-190">You can also use the `[HtmlTargetElement]` to change the name of the targeted element.</span></span> <span data-ttu-id="c83c9-191">Například pokud jste chtěli cílit `BoldTagHelper` `<MyBold>` na značky, použijte následující atribut:</span><span class="sxs-lookup"><span data-stu-id="c83c9-191">For example if you wanted the `BoldTagHelper` to target `<MyBold>` tags, you would use the following attribute:</span></span>

```csharp
[HtmlTargetElement("MyBold")]
```

## <a name="pass-a-model-to-a-tag-helper"></a><span data-ttu-id="c83c9-192">Předání modelu do pomocné rutiny značky</span><span class="sxs-lookup"><span data-stu-id="c83c9-192">Pass a model to a Tag Helper</span></span>

1. <span data-ttu-id="c83c9-193">Přidejte složku *modelů* .</span><span class="sxs-lookup"><span data-stu-id="c83c9-193">Add a *Models* folder.</span></span>

1. <span data-ttu-id="c83c9-194">Do složky `WebsiteContext` *modely* přidejte následující třídu:</span><span class="sxs-lookup"><span data-stu-id="c83c9-194">Add the following `WebsiteContext` class to the *Models* folder:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Models/WebsiteContext.cs)]

1. <span data-ttu-id="c83c9-195">Do složky `WebsiteInformationTagHelper` *TagHelpers* přidejte následující třídu.</span><span class="sxs-lookup"><span data-stu-id="c83c9-195">Add the following `WebsiteInformationTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/WebsiteInformationTagHelper.cs)]

   * <span data-ttu-id="c83c9-196">Jak už bylo uvedeno výše, pomocník značek překládá názvy tříd C# použita a vlastnosti pro pomocníky značek na [kebab](https://wiki.c2.com/?KebabCase).</span><span class="sxs-lookup"><span data-stu-id="c83c9-196">As mentioned previously, tag helpers translates Pascal-cased C# class names and properties for tag helpers into [kebab case](https://wiki.c2.com/?KebabCase).</span></span> <span data-ttu-id="c83c9-197">Proto pro použití `WebsiteInformationTagHelper` v Razormůžete psát. `<website-information />`</span><span class="sxs-lookup"><span data-stu-id="c83c9-197">Therefore, to use the `WebsiteInformationTagHelper` in Razor, you'll write `<website-information />`.</span></span>

   * <span data-ttu-id="c83c9-198">Nejste explicitně identifikovali cílový element s `[HtmlTargetElement]` atributem, takže výchozí nastavení `website-information` bude cíleno.</span><span class="sxs-lookup"><span data-stu-id="c83c9-198">You are not explicitly identifying the target element with the `[HtmlTargetElement]` attribute, so the default of `website-information` will be targeted.</span></span> <span data-ttu-id="c83c9-199">Pokud jste použili následující atribut (Všimněte si, že není kebab Case, ale odpovídá názvu třídy):</span><span class="sxs-lookup"><span data-stu-id="c83c9-199">If you applied the following attribute (note it's not kebab case but matches the class name):</span></span>

   ```csharp
   [HtmlTargetElement("WebsiteInformation")]
   ```

   <span data-ttu-id="c83c9-200">Značka `<website-information />` Case kebab se neshoduje.</span><span class="sxs-lookup"><span data-stu-id="c83c9-200">The kebab case tag `<website-information />` wouldn't match.</span></span> <span data-ttu-id="c83c9-201">Pokud chcete použít `[HtmlTargetElement]` atribut, použijte kebab případ, jak je znázorněno níže:</span><span class="sxs-lookup"><span data-stu-id="c83c9-201">If you want use the `[HtmlTargetElement]` attribute, you would use kebab case as shown below:</span></span>

   ```csharp
   [HtmlTargetElement("Website-Information")]
   ```

   * <span data-ttu-id="c83c9-202">Prvky, které jsou samy samouzavírací, nemají žádný obsah.</span><span class="sxs-lookup"><span data-stu-id="c83c9-202">Elements that are self-closing have no content.</span></span> <span data-ttu-id="c83c9-203">V tomto příkladu bude Razor značka používat samočinně uzavírací značku, ale pomocník značek vytvoří prvek [oddílu](https://www.w3.org/TR/html5/sections.html#the-section-element) (což není samoobslužné zavírání a píšete obsah uvnitř `section` elementu).</span><span class="sxs-lookup"><span data-stu-id="c83c9-203">For this example, the Razor markup will use a self-closing tag, but the tag helper will be creating a [section](https://www.w3.org/TR/html5/sections.html#the-section-element) element (which isn't self-closing and you are writing content inside the `section` element).</span></span> <span data-ttu-id="c83c9-204">Proto je nutné nastavit `TagMode` pro `StartTagAndEndTag` zápis výstupu.</span><span class="sxs-lookup"><span data-stu-id="c83c9-204">Therefore, you need to set `TagMode` to `StartTagAndEndTag` to write output.</span></span> <span data-ttu-id="c83c9-205">Alternativně můžete zadat komentář k nastavení `TagMode` čáry a zapsat značku s uzavírací značkou.</span><span class="sxs-lookup"><span data-stu-id="c83c9-205">Alternatively, you can comment out the line setting `TagMode` and write markup with a closing tag.</span></span> <span data-ttu-id="c83c9-206">(Příklad kódu je poskytován později v tomto kurzu.)</span><span class="sxs-lookup"><span data-stu-id="c83c9-206">(Example markup is provided later in this tutorial.)</span></span>

   * <span data-ttu-id="c83c9-207">Znak `$` dolaru (dolar) na následujícím řádku používá [interpolované řetězce](/dotnet/csharp/language-reference/keywords/interpolated-strings):</span><span class="sxs-lookup"><span data-stu-id="c83c9-207">The `$` (dollar sign) in the following line uses an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings):</span></span>

   ```cshtml
   $@"<ul><li><strong>Version:</strong> {Info.Version}</li>
   ```

1. <span data-ttu-id="c83c9-208">Do zobrazení *About. cshtml* přidejte následující kód.</span><span class="sxs-lookup"><span data-stu-id="c83c9-208">Add the following markup to the *About.cshtml* view.</span></span> <span data-ttu-id="c83c9-209">Zvýrazněný kód zobrazí informace o webu.</span><span class="sxs-lookup"><span data-stu-id="c83c9-209">The highlighted markup displays the web site information.</span></span>

   [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?highlight=1,4-8, 18-999)]

   > [!NOTE]
   > <span data-ttu-id="c83c9-210">V Razor kódu zobrazeném níže:</span><span class="sxs-lookup"><span data-stu-id="c83c9-210">In the Razor markup shown below:</span></span>
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?range=18-18)]
   >
   > Razor<span data-ttu-id="c83c9-211">ví, `info` že atribut je třída, ne řetězec a vy chcete psát kód jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="c83c9-211"> knows the `info` attribute is a class, not a string, and you want to write C# code.</span></span> <span data-ttu-id="c83c9-212">Všechny pomocné atributy značek bez řetězce by měly být zapsány `@` bez znaku.</span><span class="sxs-lookup"><span data-stu-id="c83c9-212">Any non-string tag helper attribute should be written without the `@` character.</span></span>

1. <span data-ttu-id="c83c9-213">Spusťte aplikaci a přejděte do zobrazení o webu, kde najdete informace o webovém serveru.</span><span class="sxs-lookup"><span data-stu-id="c83c9-213">Run the app, and navigate to the About view to see the web site information.</span></span>

   > [!NOTE]
   > <span data-ttu-id="c83c9-214">Můžete použít následující kód s uzavírací značkou a odebrat řádek s `TagMode.StartTagAndEndTag` pomocníkem značek:</span><span class="sxs-lookup"><span data-stu-id="c83c9-214">You can use the following markup with a closing tag and remove the line with `TagMode.StartTagAndEndTag` in the tag helper:</span></span>
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutNotSelfClosing.cshtml?range=20-21)]

## <a name="condition-tag-helper"></a><span data-ttu-id="c83c9-215">Pomocník značek podmínky</span><span class="sxs-lookup"><span data-stu-id="c83c9-215">Condition Tag Helper</span></span>

<span data-ttu-id="c83c9-216">Pomocník značek podmínky vykreslí výstup, když prošla pravdivá hodnota.</span><span class="sxs-lookup"><span data-stu-id="c83c9-216">The condition tag helper renders output when passed a true value.</span></span>

1. <span data-ttu-id="c83c9-217">Do složky `ConditionTagHelper` *TagHelpers* přidejte následující třídu.</span><span class="sxs-lookup"><span data-stu-id="c83c9-217">Add the following `ConditionTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/ConditionTagHelper.cs)]

1. <span data-ttu-id="c83c9-218">Obsah souboru *views/Home/index. cshtml* nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c83c9-218">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Index.cshtml)]

1. <span data-ttu-id="c83c9-219">Nahraďte `Index` metodu v `Home` kontroleru následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c83c9-219">Replace the `Index` method in the `Home` controller with the following code:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Controllers/HomeController.cs?range=9-18)]

1. <span data-ttu-id="c83c9-220">Spusťte aplikaci a přejděte na domovskou stránku.</span><span class="sxs-lookup"><span data-stu-id="c83c9-220">Run the app and browse to the home page.</span></span> <span data-ttu-id="c83c9-221">Značky v podmíněném `div` kódu nebudou vykresleny.</span><span class="sxs-lookup"><span data-stu-id="c83c9-221">The markup in the conditional `div` won't be rendered.</span></span> <span data-ttu-id="c83c9-222">Připojit řetězec `?approved=true` dotazu k adrese URL (například `http://localhost:1235/Home/Index?approved=true`).</span><span class="sxs-lookup"><span data-stu-id="c83c9-222">Append the query string `?approved=true` to the URL (for example, `http://localhost:1235/Home/Index?approved=true`).</span></span> <span data-ttu-id="c83c9-223">`approved`je nastaven na hodnotu true a zobrazí se podmíněný kód.</span><span class="sxs-lookup"><span data-stu-id="c83c9-223">`approved` is set to true and the conditional markup will be displayed.</span></span>

> [!NOTE]
> <span data-ttu-id="c83c9-224">Použijte operátor [nameof](/dotnet/csharp/language-reference/keywords/nameof) k určení atributu pro cílení namísto zadání řetězce jako u pomocníka s tučnými značkami:</span><span class="sxs-lookup"><span data-stu-id="c83c9-224">Use the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator to specify the attribute to target rather than specifying a string as you did with the bold tag helper:</span></span>
>
> [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zConditionTagHelperCopy.cs?highlight=1,2,5&range=5-18)]
>
> <span data-ttu-id="c83c9-225">[Nameof](/dotnet/csharp/language-reference/keywords/nameof) operátor ochrany kódu by měl být někdy refaktorované (název můžeme změnit na `RedCondition`).</span><span class="sxs-lookup"><span data-stu-id="c83c9-225">The [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator will protect the code should it ever be refactored (we might want to change the name to `RedCondition`).</span></span>

### <a name="avoid-tag-helper-conflicts"></a><span data-ttu-id="c83c9-226">Vyhnout se konfliktům pomocníka značek</span><span class="sxs-lookup"><span data-stu-id="c83c9-226">Avoid Tag Helper conflicts</span></span>

<span data-ttu-id="c83c9-227">V této části napíšete dvojici pomocníků značek pro Automatické propojování.</span><span class="sxs-lookup"><span data-stu-id="c83c9-227">In this section, you write a pair of auto-linking tag helpers.</span></span> <span data-ttu-id="c83c9-228">První nahradí značku obsahující adresu URL začínající řetězcem HTTP na značku kotvy HTML obsahující stejnou adresu URL (a tím poskytuje odkaz na adresu URL).</span><span class="sxs-lookup"><span data-stu-id="c83c9-228">The first will replace markup containing a URL starting with HTTP to an HTML anchor tag containing the same URL (and thus yielding a link to the URL).</span></span> <span data-ttu-id="c83c9-229">Druhý postup bude stejný jako u adresy URL začínající na WWW.</span><span class="sxs-lookup"><span data-stu-id="c83c9-229">The second will do the same for a URL starting with WWW.</span></span>

<span data-ttu-id="c83c9-230">Vzhledem k tomu, že tyto dva pomocníky úzce souvisejí a vy je můžete v budoucnu Refaktorovat, uchováváme je ve stejném souboru.</span><span class="sxs-lookup"><span data-stu-id="c83c9-230">Because these two helpers are closely related and you may refactor them in the future, we'll keep them in the same file.</span></span>

1. <span data-ttu-id="c83c9-231">Do složky `AutoLinkerHttpTagHelper` *TagHelpers* přidejte následující třídu.</span><span class="sxs-lookup"><span data-stu-id="c83c9-231">Add the following `AutoLinkerHttpTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=7-19)]

   >[!NOTE]
   ><span data-ttu-id="c83c9-232">`AutoLinkerHttpTagHelper` Třída cílí `p` na prvky a používá [regulární výraz](/dotnet/standard/base-types/regular-expression-language-quick-reference) pro vytvoření kotvy.</span><span class="sxs-lookup"><span data-stu-id="c83c9-232">The `AutoLinkerHttpTagHelper` class targets `p` elements and uses [Regex](/dotnet/standard/base-types/regular-expression-language-quick-reference) to create the anchor.</span></span>

1. <span data-ttu-id="c83c9-233">Na konec souboru *views/Home/Contact. cshtml* přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="c83c9-233">Add the following markup to the end of the *Views/Home/Contact.cshtml* file:</span></span>

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=19)]

1. <span data-ttu-id="c83c9-234">Spusťte aplikaci a ověřte, že pomocná rutina značky vykresluje kotvu správně.</span><span class="sxs-lookup"><span data-stu-id="c83c9-234">Run the app and verify that the tag helper renders the anchor correctly.</span></span>

1. <span data-ttu-id="c83c9-235">Aktualizujte `AutoLinker` třídu tak, aby `AutoLinkerWwwTagHelper` obsahovala, která převede text na značku kotvy, která obsahuje také původní text na webové stránce.</span><span class="sxs-lookup"><span data-stu-id="c83c9-235">Update the `AutoLinker` class to include the `AutoLinkerWwwTagHelper` which will convert www text to an anchor tag that also contains the original www text.</span></span> <span data-ttu-id="c83c9-236">Aktualizovaný kód je zvýrazněný níže:</span><span class="sxs-lookup"><span data-stu-id="c83c9-236">The updated code is highlighted below:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?highlight=15-34&range=7-34)]

1. <span data-ttu-id="c83c9-237">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c83c9-237">Run the app.</span></span> <span data-ttu-id="c83c9-238">Všimněte si, že text www se vykresluje jako odkaz, ale text HTTP není.</span><span class="sxs-lookup"><span data-stu-id="c83c9-238">Notice the www text is rendered as a link but the HTTP text isn't.</span></span> <span data-ttu-id="c83c9-239">Pokud umístíte bod přerušení do obou tříd, vidíte, že se nejprve spustí pomocná třída značek HTTP.</span><span class="sxs-lookup"><span data-stu-id="c83c9-239">If you put a break point in both classes, you can see that the HTTP tag helper class runs first.</span></span> <span data-ttu-id="c83c9-240">Problémem je, že výstup pomocníka značek je uložen do mezipaměti a když je spuštěná pomocná rutina webové značky, přepíše výstup uložený v mezipaměti z pomocné rutiny značky HTTP.</span><span class="sxs-lookup"><span data-stu-id="c83c9-240">The problem is that the tag helper output is cached, and when the WWW tag helper is run, it overwrites the cached output from the HTTP tag helper.</span></span> <span data-ttu-id="c83c9-241">Později v tomto kurzu se dozvíte, jak řídit pořadí, ve kterém se budou zobrazovat pomocníka značek.</span><span class="sxs-lookup"><span data-stu-id="c83c9-241">Later in the tutorial we'll see how to control the order that tag helpers run in.</span></span> <span data-ttu-id="c83c9-242">Kód opravujeme následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="c83c9-242">We'll fix the code with the following:</span></span>

   [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10,21,22,26&range=8-37)]

   > [!NOTE]
   > <span data-ttu-id="c83c9-243">V první edici pomocníků značek pro Automatické propojování jste získali obsah cíle s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c83c9-243">In the first edition of the auto-linking tag helpers, you got the content of the target with the following code:</span></span>
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=12)]
   >
   > <span data-ttu-id="c83c9-244">To znamená, že `GetChildContentAsync` zavoláte `TagHelperOutput` pomocí předané `ProcessAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="c83c9-244">That is, you call `GetChildContentAsync` using the `TagHelperOutput` passed into the `ProcessAsync` method.</span></span> <span data-ttu-id="c83c9-245">Jak bylo zmíněno dříve, protože výstup je uložen do mezipaměti, poslední pomocník značek spouští službu WINS.</span><span class="sxs-lookup"><span data-stu-id="c83c9-245">As mentioned previously, because the output is cached, the last tag helper to run wins.</span></span> <span data-ttu-id="c83c9-246">Vyřešili jste tento problém s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c83c9-246">You fixed that problem with the following code:</span></span>
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?range=34-35)]
   >
   > <span data-ttu-id="c83c9-247">Výše uvedený kód zkontroluje, zda byl obsah změněn a v případě, že má, získá obsah z výstupní vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="c83c9-247">The code above checks to see if the content has been modified, and if it has, it gets the content from the output buffer.</span></span>

1. <span data-ttu-id="c83c9-248">Spusťte aplikaci a ověřte, že tyto dva odkazy fungují podle očekávání.</span><span class="sxs-lookup"><span data-stu-id="c83c9-248">Run the app and verify that the two links work as expected.</span></span> <span data-ttu-id="c83c9-249">I když se může zdát, že je naše pomocník značek automatického linkeru správný a úplný, má malý problém.</span><span class="sxs-lookup"><span data-stu-id="c83c9-249">While it might appear our auto linker tag helper is correct and complete, it has a subtle problem.</span></span> <span data-ttu-id="c83c9-250">Pokud se jako první spustí pomocník webové značky, odkazy na webové odkazy nebudou správné.</span><span class="sxs-lookup"><span data-stu-id="c83c9-250">If the WWW tag helper runs first, the www links won't be correct.</span></span> <span data-ttu-id="c83c9-251">Aktualizujte kód přidáním `Order` přetížení pro řízení pořadí, v němž je značka spuštěna.</span><span class="sxs-lookup"><span data-stu-id="c83c9-251">Update the code by adding the `Order` overload to control the order that the tag runs in.</span></span> <span data-ttu-id="c83c9-252">`Order` Vlastnost určuje pořadí spouštění relativně k jiným pomocníkům značek cílících na stejný prvek.</span><span class="sxs-lookup"><span data-stu-id="c83c9-252">The `Order` property determines the execution order relative to other tag helpers targeting the same element.</span></span> <span data-ttu-id="c83c9-253">Výchozí hodnota pořadí je nula a instance s nižšími hodnotami jsou spouštěny jako první.</span><span class="sxs-lookup"><span data-stu-id="c83c9-253">The default order value is zero and instances with lower values are executed first.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?highlight=5,6,7,8&range=8-15)]

   <span data-ttu-id="c83c9-254">Předchozí kód zaručuje, že pomocník značek HTTP se spustí před pomocníkem webové značky.</span><span class="sxs-lookup"><span data-stu-id="c83c9-254">The preceding code guarantees that the HTTP tag helper runs before the WWW tag helper.</span></span> <span data-ttu-id="c83c9-255">Změňte `Order` na `MaxValue` a ověřte, zda je kód VYgenerovaný pro značku www nesprávný.</span><span class="sxs-lookup"><span data-stu-id="c83c9-255">Change `Order` to `MaxValue` and verify that the markup generated for the WWW tag is incorrect.</span></span>

## <a name="inspect-and-retrieve-child-content"></a><span data-ttu-id="c83c9-256">Kontrola a načtení podřízeného obsahu</span><span class="sxs-lookup"><span data-stu-id="c83c9-256">Inspect and retrieve child content</span></span>

<span data-ttu-id="c83c9-257">Pomocník značek poskytuje několik vlastností pro načtení obsahu.</span><span class="sxs-lookup"><span data-stu-id="c83c9-257">The tag helpers provide several properties to retrieve content.</span></span>

* <span data-ttu-id="c83c9-258">Výsledek `GetChildContentAsync` lze připojit k `output.Content`.</span><span class="sxs-lookup"><span data-stu-id="c83c9-258">The result of `GetChildContentAsync` can be appended to `output.Content`.</span></span>
* <span data-ttu-id="c83c9-259">Můžete zkontrolovat výsledek `GetChildContentAsync` s `GetContent`.</span><span class="sxs-lookup"><span data-stu-id="c83c9-259">You can inspect the result of `GetChildContentAsync` with `GetContent`.</span></span>
* <span data-ttu-id="c83c9-260">Pokud upravíte `output.Content`, text taghelperu nebude proveden ani vykreslen, pokud nebudete volat `GetChildContentAsync` jako v našem příkladu automatického linkeru:</span><span class="sxs-lookup"><span data-stu-id="c83c9-260">If you modify `output.Content`, the TagHelper body won't be executed or rendered unless you call `GetChildContentAsync` as in our auto-linker sample:</span></span>

[!code-csharp[](../../views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10&range=8-21)]

* <span data-ttu-id="c83c9-261">Více volání `GetChildContentAsync` vrátí stejnou hodnotu a znovu nespustí `TagHelper` tělo, pokud nepředáte parametr false, který označuje, že nechcete použít výsledek uložený v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="c83c9-261">Multiple calls to `GetChildContentAsync` returns the same value and doesn't re-execute the `TagHelper` body unless you pass in a false parameter indicating not to use the cached result.</span></span>

## <a name="load-minified-partial-view-taghelper"></a><span data-ttu-id="c83c9-262">Načíst minifikovaného částečného zobrazení Taghelperu</span><span class="sxs-lookup"><span data-stu-id="c83c9-262">Load minified partial view TagHelper</span></span>

<span data-ttu-id="c83c9-263">V produkčních prostředích je možné výkon zlepšit načtením částečně minifikovaného zobrazení.</span><span class="sxs-lookup"><span data-stu-id="c83c9-263">In production environments, performance can be improved by loading minified partial views.</span></span> <span data-ttu-id="c83c9-264">Využití částečného zobrazení minifikovaného v produkčním prostředí:</span><span class="sxs-lookup"><span data-stu-id="c83c9-264">To take advantage of minified partial view in production:</span></span>

* <span data-ttu-id="c83c9-265">Vytvořte nebo nastavte proces před sestavením, který minifies částečná zobrazení.</span><span class="sxs-lookup"><span data-stu-id="c83c9-265">Create/set up a pre-build process that minifies partial views.</span></span>
* <span data-ttu-id="c83c9-266">Pomocí následujícího kódu načtěte minifikovaného částečná zobrazení v nevývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="c83c9-266">Use the following code to load  minified partial views in non-development environments.</span></span>

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/MinifiedVersionTagHelper.cs?name=snippet)]

---
title: Vytváření značek pomocníků v ASP.NET Core
author: rick-anderson
description: Naučte se vytvářet pomocníky značek v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/29/2019
uid: mvc/views/tag-helpers/authoring
ms.openlocfilehash: f0c7e114583b2ca2e681c507bef3487c863d8cd0
ms.sourcegitcommit: a166291c6708f5949c417874108332856b53b6a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2019
ms.locfileid: "72589875"
---
# <a name="author-tag-helpers-in-aspnet-core"></a><span data-ttu-id="a8494-103">Vytváření značek pomocníků v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a8494-103">Author Tag Helpers in ASP.NET Core</span></span>

<span data-ttu-id="a8494-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a8494-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a8494-105">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/authoring/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a8494-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/authoring/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="get-started-with-tag-helpers"></a><span data-ttu-id="a8494-106">Začínáme s pomocníky značek</span><span class="sxs-lookup"><span data-stu-id="a8494-106">Get started with Tag Helpers</span></span>

<span data-ttu-id="a8494-107">V tomto kurzu se seznámíte se sestavami programování značek.</span><span class="sxs-lookup"><span data-stu-id="a8494-107">This tutorial provides an introduction to programming Tag Helpers.</span></span> <span data-ttu-id="a8494-108">[Úvod do pomocníků značek](intro.md) popisuje výhody, které poskytují pomocníky pro tag.</span><span class="sxs-lookup"><span data-stu-id="a8494-108">[Introduction to Tag Helpers](intro.md) describes the benefits that Tag Helpers provide.</span></span>

<span data-ttu-id="a8494-109">Pomocný objekt tag je jakákoliv třída, která implementuje rozhraní `ITagHelper`.</span><span class="sxs-lookup"><span data-stu-id="a8494-109">A tag helper is any class that implements the `ITagHelper` interface.</span></span> <span data-ttu-id="a8494-110">Při vytváření pomocníka značek se však obecně odvodit z `TagHelper`, takže k tomu získáte přístup k metodě `Process`.</span><span class="sxs-lookup"><span data-stu-id="a8494-110">However, when you author a tag helper, you generally derive from `TagHelper`, doing so gives you access to the `Process` method.</span></span>

1. <span data-ttu-id="a8494-111">Vytvořte nový projekt ASP.NET Core s názvem **AuthoringTagHelpers**.</span><span class="sxs-lookup"><span data-stu-id="a8494-111">Create a new ASP.NET Core project called **AuthoringTagHelpers**.</span></span> <span data-ttu-id="a8494-112">Pro tento projekt nebudete potřebovat ověření.</span><span class="sxs-lookup"><span data-stu-id="a8494-112">You won't need authentication for this project.</span></span>

1. <span data-ttu-id="a8494-113">Vytvořte složku pro uložení pomocníků značek s názvem *TagHelpers*.</span><span class="sxs-lookup"><span data-stu-id="a8494-113">Create a folder to hold the Tag Helpers called *TagHelpers*.</span></span> <span data-ttu-id="a8494-114">Složka *TagHelpers* *není povinná* , ale jedná se o rozumnou konvenci.</span><span class="sxs-lookup"><span data-stu-id="a8494-114">The *TagHelpers* folder is *not* required, but it's a reasonable convention.</span></span> <span data-ttu-id="a8494-115">Teď začneme psát některé pomocníky jednoduchých značek.</span><span class="sxs-lookup"><span data-stu-id="a8494-115">Now let's get started writing some simple tag helpers.</span></span>

## <a name="a-minimal-tag-helper"></a><span data-ttu-id="a8494-116">Pomocný Pomocník s minimální značkou</span><span class="sxs-lookup"><span data-stu-id="a8494-116">A minimal Tag Helper</span></span>

<span data-ttu-id="a8494-117">V této části napíšete pomocníka značek, který aktualizuje značku e-mailu.</span><span class="sxs-lookup"><span data-stu-id="a8494-117">In this section, you write a tag helper that updates an email tag.</span></span> <span data-ttu-id="a8494-118">Příklad:</span><span class="sxs-lookup"><span data-stu-id="a8494-118">For example:</span></span>

```html
<email>Support</email>
```

<span data-ttu-id="a8494-119">Server použije pomocníka pro označení e-mailu k převedení tohoto označení na následující:</span><span class="sxs-lookup"><span data-stu-id="a8494-119">The server will use our email tag helper to convert that markup into the following:</span></span>

```html
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

<span data-ttu-id="a8494-120">To znamená, že značka kotvy vytvoří tento e-mailový odkaz.</span><span class="sxs-lookup"><span data-stu-id="a8494-120">That is, an anchor tag that makes this an email link.</span></span> <span data-ttu-id="a8494-121">To můžete chtít udělat, pokud píšete modul blogu a potřebujete ho poslat e-mailem pro marketing, podporu a další kontakty, a to vše ve stejné doméně.</span><span class="sxs-lookup"><span data-stu-id="a8494-121">You might want to do this if you are writing a blog engine and need it to send email for marketing, support, and other contacts, all to the same domain.</span></span>

1. <span data-ttu-id="a8494-122">Do složky *TagHelpers* přidejte následující třídu `EmailTagHelper`.</span><span class="sxs-lookup"><span data-stu-id="a8494-122">Add the following `EmailTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1EmailTagHelperCopy.cs)]

   * <span data-ttu-id="a8494-123">Pomocník značek používá konvenci pojmenování, která cílí na prvky názvu kořenové třídy (mínus část *taghelperu* názvu třídy).</span><span class="sxs-lookup"><span data-stu-id="a8494-123">Tag helpers use a naming convention that targets elements of the root class name (minus the *TagHelper* portion of the class name).</span></span> <span data-ttu-id="a8494-124">V tomto příkladu je kořenový název **EmailTagHelper** *e-mailem*, takže se bude cílit na `<email>` tag.</span><span class="sxs-lookup"><span data-stu-id="a8494-124">In this example, the root name of **EmailTagHelper** is *email*, so the `<email>` tag will be targeted.</span></span> <span data-ttu-id="a8494-125">Tato konvence vytváření názvů by měla fungovat pro většinu pomocníků se značkami, a to později v části jak ji přepsat.</span><span class="sxs-lookup"><span data-stu-id="a8494-125">This naming convention should work for most tag helpers, later on I'll show how to override it.</span></span>

   * <span data-ttu-id="a8494-126">Třída `EmailTagHelper` je odvozena z `TagHelper`.</span><span class="sxs-lookup"><span data-stu-id="a8494-126">The `EmailTagHelper` class derives from `TagHelper`.</span></span> <span data-ttu-id="a8494-127">Třída `TagHelper` poskytuje metody a vlastnosti pro psaní pomocníků značek.</span><span class="sxs-lookup"><span data-stu-id="a8494-127">The `TagHelper` class provides methods and properties for writing Tag Helpers.</span></span>

   * <span data-ttu-id="a8494-128">Přepsaná metoda `Process` řídí, co má pomocník značek při spuštění.</span><span class="sxs-lookup"><span data-stu-id="a8494-128">The overridden `Process` method controls what the tag helper does when executed.</span></span> <span data-ttu-id="a8494-129">Třída `TagHelper` také poskytuje asynchronní verzi (`ProcessAsync`) se stejnými parametry.</span><span class="sxs-lookup"><span data-stu-id="a8494-129">The `TagHelper` class also provides an asynchronous version (`ProcessAsync`) with the same parameters.</span></span>

   * <span data-ttu-id="a8494-130">Kontextový parametr pro `Process` (a `ProcessAsync`) obsahuje informace spojené s prováděním aktuální značky HTML.</span><span class="sxs-lookup"><span data-stu-id="a8494-130">The context parameter to `Process` (and `ProcessAsync`) contains information associated with the execution of the current HTML tag.</span></span>

   * <span data-ttu-id="a8494-131">Výstupní parametr pro `Process` (a `ProcessAsync`) obsahuje stavový zástupce prvku HTML původního zdroje použitého ke generování značek a obsahu jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="a8494-131">The output parameter to `Process` (and `ProcessAsync`) contains a stateful HTML element representative of the original source used to generate an HTML tag and content.</span></span>

   * <span data-ttu-id="a8494-132">Název naší třídy má příponu **taghelperu**, která *není požadována, ale je* považována za osvědčenou konvenci.</span><span class="sxs-lookup"><span data-stu-id="a8494-132">Our class name has a suffix of **TagHelper**, which is *not* required, but it's considered a best practice convention.</span></span> <span data-ttu-id="a8494-133">Třídu můžete deklarovat jako:</span><span class="sxs-lookup"><span data-stu-id="a8494-133">You could declare the class as:</span></span>

   ```csharp
   public class Email : TagHelper
   ```

1. <span data-ttu-id="a8494-134">Chcete-li zpřístupnit třídu `EmailTagHelper` všem našim zobrazením Razor, přidejte direktivu `addTagHelper` do souboru *views/_ViewImports. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="a8494-134">To make the `EmailTagHelper` class available to all our Razor views, add the `addTagHelper` directive to the *Views/_ViewImports.cshtml* file:</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopyEmail.cshtml?highlight=2,3)]

   <span data-ttu-id="a8494-135">Výše uvedený kód používá syntaxi zástupných znaků k určení všech pomocníků značek v našem sestavení bude k dispozici.</span><span class="sxs-lookup"><span data-stu-id="a8494-135">The code above uses the wildcard syntax to specify all the tag helpers in our assembly will be available.</span></span> <span data-ttu-id="a8494-136">První řetězec po `@addTagHelper` určuje pomocný objekt tag, který se má načíst (použijte \* pro všechny pomocné rutiny značky) a druhý řetězec "AuthoringTagHelpers" Určuje sestavení, ve kterém je pomocník značek.</span><span class="sxs-lookup"><span data-stu-id="a8494-136">The first string after `@addTagHelper` specifies the tag helper to load (Use "\*" for all tag helpers), and the second string "AuthoringTagHelpers" specifies the assembly the tag helper is in.</span></span> <span data-ttu-id="a8494-137">Všimněte si také, že druhý řádek přináší do ASP.NET Core pomocníka značek MVC pomocí syntaxe zástupných znaků (tyto pomocníky jsou popsány v tématu [Úvod k označení pomocníků se značkami](intro.md)). Je to direktiva `@addTagHelper`, která zpřístupňuje pomocníka značky pro zobrazení Razor.</span><span class="sxs-lookup"><span data-stu-id="a8494-137">Also, note that the second line brings in the ASP.NET Core MVC tag helpers using the wildcard syntax (those helpers are discussed in [Introduction to Tag Helpers](intro.md).) It's the `@addTagHelper` directive that makes the tag helper available to the Razor view.</span></span> <span data-ttu-id="a8494-138">Alternativně můžete zadat plně kvalifikovaný název (FQN) pro pomocnou značku, jak je znázorněno níže:</span><span class="sxs-lookup"><span data-stu-id="a8494-138">Alternatively, you can provide the fully qualified name (FQN) of a tag helper as shown below:</span></span>

```csharp
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

<!--
the following snippet uses TagHelpers3 and should use TagHelpers (not the 3)
    [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImports.cshtml?highlight=3&range=1-3)]
-->

<span data-ttu-id="a8494-139">Chcete-li přidat pomocníka značek pro zobrazení pomocí FQN, nejprve přidejte FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`) a potom **název sestavení** (*AuthoringTagHelpers*, ne nutně `namespace`).</span><span class="sxs-lookup"><span data-stu-id="a8494-139">To add a tag helper to a view using a FQN, you first add the FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), and then the **assembly name** (*AuthoringTagHelpers*, not necessarily the `namespace`).</span></span> <span data-ttu-id="a8494-140">Většina vývojářů bude chtít použít zástupnou syntaxi.</span><span class="sxs-lookup"><span data-stu-id="a8494-140">Most developers will prefer to use the wildcard syntax.</span></span> <span data-ttu-id="a8494-141">[Úvod k](intro.md) pomocníkům značek odkazuje na podrobné informace o přidání, odebrání, hierarchii a zástupné syntaxi pomocníka značek.</span><span class="sxs-lookup"><span data-stu-id="a8494-141">[Introduction to Tag Helpers](intro.md) goes into detail on tag helper adding, removing, hierarchy, and wildcard syntax.</span></span>

1. <span data-ttu-id="a8494-142">Aktualizujte kód v souboru *views/Home/Contact. cshtml* pomocí těchto změn:</span><span class="sxs-lookup"><span data-stu-id="a8494-142">Update the markup in the *Views/Home/Contact.cshtml* file with these changes:</span></span>

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. <span data-ttu-id="a8494-143">Spusťte aplikaci a použijte svůj oblíbený prohlížeč k zobrazení zdroje HTML, abyste mohli ověřit, že jsou značky e-mailu nahrazeny označením kotvy (například `<a>Support</a>`).</span><span class="sxs-lookup"><span data-stu-id="a8494-143">Run the app and use your favorite browser to view the HTML source so you can verify that the email tags are replaced with anchor markup (For example, `<a>Support</a>`).</span></span> <span data-ttu-id="a8494-144">*Podpora* a *Marketing* se vykreslují jako odkazy, ale nemají atribut `href`, aby byly funkční.</span><span class="sxs-lookup"><span data-stu-id="a8494-144">*Support* and *Marketing* are rendered as a links, but they don't have an `href` attribute to make them functional.</span></span> <span data-ttu-id="a8494-145">Opravujeme ho v další části.</span><span class="sxs-lookup"><span data-stu-id="a8494-145">We'll fix that in the next section.</span></span>

## <a name="setattribute-and-setcontent"></a><span data-ttu-id="a8494-146">SetAttributes a SetContent</span><span class="sxs-lookup"><span data-stu-id="a8494-146">SetAttribute and SetContent</span></span>

<span data-ttu-id="a8494-147">V této části aktualizujeme `EmailTagHelper` tak, aby se pro e-mail vytvořila platná značka ukotvení.</span><span class="sxs-lookup"><span data-stu-id="a8494-147">In this section, we'll update the `EmailTagHelper` so that it will create a valid anchor tag for email.</span></span> <span data-ttu-id="a8494-148">Aktualizujeme ji, aby provedla informace ze zobrazení Razor (ve formě atributu `mail-to`) a použila se při vytváření kotvy.</span><span class="sxs-lookup"><span data-stu-id="a8494-148">We'll update it to take information from a Razor view (in the form of a `mail-to` attribute) and use that in generating the anchor.</span></span>

<span data-ttu-id="a8494-149">Aktualizujte třídu `EmailTagHelper` následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="a8494-149">Update the `EmailTagHelper` class with the following:</span></span>

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?range=6-22)]

* <span data-ttu-id="a8494-150">Názvy tříd a vlastností jazyka Pascal-použita pro pomocníky značek jsou přeloženy do jejich [kebabho případu](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span><span class="sxs-lookup"><span data-stu-id="a8494-150">Pascal-cased class and property names for tag helpers are translated into their [kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span></span> <span data-ttu-id="a8494-151">Proto pro použití atributu `MailTo` použijete `<email mail-to="value"/>` ekvivalent.</span><span class="sxs-lookup"><span data-stu-id="a8494-151">Therefore, to use the `MailTo` attribute, you'll use `<email mail-to="value"/>` equivalent.</span></span>

* <span data-ttu-id="a8494-152">Poslední řádek nastaví dokončený obsah pro naši pomocníka s minimální funkční značkou.</span><span class="sxs-lookup"><span data-stu-id="a8494-152">The last line sets the completed content for our minimally functional tag helper.</span></span>

* <span data-ttu-id="a8494-153">Zvýrazněný řádek ukazuje syntaxi pro přidání atributů:</span><span class="sxs-lookup"><span data-stu-id="a8494-153">The highlighted line shows the syntax for adding attributes:</span></span>

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?highlight=6&range=14-21)]

<span data-ttu-id="a8494-154">Tento přístup funguje pro atribut "href", pokud aktuálně neexistuje v kolekci atributů.</span><span class="sxs-lookup"><span data-stu-id="a8494-154">That approach works for the attribute "href" as long as it doesn't currently exist in the attributes collection.</span></span> <span data-ttu-id="a8494-155">Můžete také použít metodu `output.Attributes.Add` pro přidání atributu pomocníka značky na konec kolekce atributů značek.</span><span class="sxs-lookup"><span data-stu-id="a8494-155">You can also use the `output.Attributes.Add` method to add a tag helper attribute to the end of the collection of tag attributes.</span></span>

1. <span data-ttu-id="a8494-156">Aktualizujte kód v souboru *views/Home/Contact. cshtml* pomocí těchto změn:</span><span class="sxs-lookup"><span data-stu-id="a8494-156">Update the markup in the *Views/Home/Contact.cshtml* file with these changes:</span></span>

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/ContactCopy.cshtml?highlight=15,16)]

1. <span data-ttu-id="a8494-157">Spusťte aplikaci a ověřte, že generuje správné odkazy.</span><span class="sxs-lookup"><span data-stu-id="a8494-157">Run the app and verify that it generates the correct links.</span></span>

<a name="self-closing"></a>

   > [!NOTE]
   > <span data-ttu-id="a8494-158">Pokud byste chtěli napsat značku e-mailu na samoobslužné uzavírání (`<email mail-to="Rick" />`), konečný výstup by taky byl samouzavírací.</span><span class="sxs-lookup"><span data-stu-id="a8494-158">If you were to write the email tag self-closing (`<email mail-to="Rick" />`), the final output would also be self-closing.</span></span> <span data-ttu-id="a8494-159">Chcete-li povolit možnost psaní značky pouze pomocí počáteční značky (`<email mail-to="Rick">`), je nutné třídu vytřídit následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="a8494-159">To enable the ability to write the tag with only a start tag (`<email mail-to="Rick">`) you must decorate the class with the following:</span></span>
   >
   > [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailVoid.cs?highlight=1&range=6-10)]

   <span data-ttu-id="a8494-160">Pomocí samoobslužného pomocníka značek e-mailu by byl výstup `<a href="mailto:Rick@contoso.com" />`.</span><span class="sxs-lookup"><span data-stu-id="a8494-160">With a self-closing email tag helper, the output would be `<a href="mailto:Rick@contoso.com" />`.</span></span> <span data-ttu-id="a8494-161">Samouzavírací značky kotvy nejsou platné HTML, takže byste ji neměli chtít vytvořit, ale možná budete chtít vytvořit pomocníka značek, který je samo samouzavírací.</span><span class="sxs-lookup"><span data-stu-id="a8494-161">Self-closing anchor tags are not valid HTML, so you wouldn't want to create one, but you might want to create a tag helper that's self-closing.</span></span> <span data-ttu-id="a8494-162">Pomocí značek můžete nastavit typ vlastnosti `TagMode` po čtení značky.</span><span class="sxs-lookup"><span data-stu-id="a8494-162">Tag helpers set the type of the `TagMode` property after reading a tag.</span></span>

### <a name="processasync"></a><span data-ttu-id="a8494-163">ProcessAsync</span><span class="sxs-lookup"><span data-stu-id="a8494-163">ProcessAsync</span></span>

<span data-ttu-id="a8494-164">V této části napíšeme pomocníka asynchronního e-mailu.</span><span class="sxs-lookup"><span data-stu-id="a8494-164">In this section, we'll write an asynchronous email helper.</span></span>

1. <span data-ttu-id="a8494-165">Třídu `EmailTagHelper` nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a8494-165">Replace the `EmailTagHelper` class with the following code:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelper.cs?range=6-17)]

   <span data-ttu-id="a8494-166">**Poznámky:**</span><span class="sxs-lookup"><span data-stu-id="a8494-166">**Notes:**</span></span>

   * <span data-ttu-id="a8494-167">Tato verze používá asynchronní metodu `ProcessAsync`.</span><span class="sxs-lookup"><span data-stu-id="a8494-167">This version uses the asynchronous `ProcessAsync` method.</span></span> <span data-ttu-id="a8494-168">Asynchronní `GetChildContentAsync` vrátí `Task` obsahující `TagHelperContent`.</span><span class="sxs-lookup"><span data-stu-id="a8494-168">The asynchronous `GetChildContentAsync` returns a `Task` containing the `TagHelperContent`.</span></span>

   * <span data-ttu-id="a8494-169">K získání obsahu elementu HTML použijte parametr `output`.</span><span class="sxs-lookup"><span data-stu-id="a8494-169">Use the `output` parameter to get contents of the HTML element.</span></span>

1. <span data-ttu-id="a8494-170">Proveďte následující změny v souboru *views/Home/Contact. cshtml* , aby pomocník značek mohl získat cílový e-mail.</span><span class="sxs-lookup"><span data-stu-id="a8494-170">Make the following change to the *Views/Home/Contact.cshtml* file so the tag helper can get the target email.</span></span>

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. <span data-ttu-id="a8494-171">Spusťte aplikaci a ověřte, že generuje platné e-mailové odkazy.</span><span class="sxs-lookup"><span data-stu-id="a8494-171">Run the app and verify that it generates valid email links.</span></span>

### <a name="removeall-precontentsethtmlcontent-and-postcontentsethtmlcontent"></a><span data-ttu-id="a8494-172">RemoveAll, Content. SetHtmlContent a PostContent. SetHtmlContent</span><span class="sxs-lookup"><span data-stu-id="a8494-172">RemoveAll, PreContent.SetHtmlContent and PostContent.SetHtmlContent</span></span>

1. <span data-ttu-id="a8494-173">Do složky *TagHelpers* přidejte následující třídu `BoldTagHelper`.</span><span class="sxs-lookup"><span data-stu-id="a8494-173">Add the following `BoldTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/BoldTagHelper.cs)]

   * <span data-ttu-id="a8494-174">Atribut `[HtmlTargetElement]` předává parametr atributu, který určuje, že všechny prvky jazyka HTML obsahující atribut HTML s názvem "bold" budou odpovídat a spustí se metoda `Process` override ve třídě.</span><span class="sxs-lookup"><span data-stu-id="a8494-174">The `[HtmlTargetElement]` attribute passes an attribute parameter that specifies that any HTML element that contains an HTML attribute named "bold" will match, and the `Process` override method in the class will run.</span></span> <span data-ttu-id="a8494-175">V naší ukázce `Process` metoda odstraní atribut "bold" a obklopí obsahující značku pomocí `<strong></strong>`.</span><span class="sxs-lookup"><span data-stu-id="a8494-175">In our sample, the `Process` method removes the "bold" attribute and surrounds the containing markup with `<strong></strong>`.</span></span>

   * <span data-ttu-id="a8494-176">Vzhledem k tomu, že nechcete nahradit existující obsah značky, je nutné napsat značku otevírací `<strong>` s metodou `PreContent.SetHtmlContent` a uzavírací `</strong>`ovou značkou metodou `PostContent.SetHtmlContent`.</span><span class="sxs-lookup"><span data-stu-id="a8494-176">Because you don't want to replace the existing tag content, you must write the opening `<strong>` tag with the `PreContent.SetHtmlContent` method and the closing `</strong>` tag with the `PostContent.SetHtmlContent` method.</span></span>

1. <span data-ttu-id="a8494-177">Upravte zobrazení *About. cshtml* tak, aby obsahovalo hodnotu atributu `bold`.</span><span class="sxs-lookup"><span data-stu-id="a8494-177">Modify the *About.cshtml* view to contain a `bold` attribute value.</span></span> <span data-ttu-id="a8494-178">Dokončený kód je uveden níže.</span><span class="sxs-lookup"><span data-stu-id="a8494-178">The completed code is shown below.</span></span>

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutBoldOnly.cshtml?highlight=7)]

1. <span data-ttu-id="a8494-179">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a8494-179">Run the app.</span></span> <span data-ttu-id="a8494-180">Pomocí oblíbeného prohlížeče můžete zkontrolovat zdroj a ověřit označení.</span><span class="sxs-lookup"><span data-stu-id="a8494-180">You can use your favorite browser to inspect the source and verify the markup.</span></span>

   <span data-ttu-id="a8494-181">Atribut `[HtmlTargetElement]` výše překračuje pouze označení HTML, které poskytuje název atributu "bold".</span><span class="sxs-lookup"><span data-stu-id="a8494-181">The `[HtmlTargetElement]` attribute above only targets HTML markup that provides an attribute name of "bold".</span></span> <span data-ttu-id="a8494-182">Element `<bold>` nebyl změněn pomocníkem značek.</span><span class="sxs-lookup"><span data-stu-id="a8494-182">The `<bold>` element wasn't modified by the tag helper.</span></span>

1. <span data-ttu-id="a8494-183">Odkomentujte řádek atributu `[HtmlTargetElement]` a ve výchozím nastavení se zacílí na `<bold>` značky, tj. HTML značky `<bold>` formuláře.</span><span class="sxs-lookup"><span data-stu-id="a8494-183">Comment out the `[HtmlTargetElement]` attribute line and it will default to targeting `<bold>` tags, that is, HTML markup of the form `<bold>`.</span></span> <span data-ttu-id="a8494-184">Nezapomeňte, že výchozí konvence pojmenování bude odpovídat názvu třídy **tučně**taghelperu, aby `<bold>` značky.</span><span class="sxs-lookup"><span data-stu-id="a8494-184">Remember, the default naming convention will match the class name **Bold**TagHelper to `<bold>` tags.</span></span>

1. <span data-ttu-id="a8494-185">Spusťte aplikaci a ověřte, zda je značka `<bold>` zpracována pomocníkem značek.</span><span class="sxs-lookup"><span data-stu-id="a8494-185">Run the app and verify that the `<bold>` tag is processed by the tag helper.</span></span>

<span data-ttu-id="a8494-186">Upravení třídu s více atributy `[HtmlTargetElement]` má za následek logický nebo cíl.</span><span class="sxs-lookup"><span data-stu-id="a8494-186">Decorating a class with multiple `[HtmlTargetElement]` attributes results in a logical-OR of the targets.</span></span> <span data-ttu-id="a8494-187">Například pomocí následujícího kódu se bude shodovat značka tučného písma nebo tučného atributu.</span><span class="sxs-lookup"><span data-stu-id="a8494-187">For example, using the code below, a bold tag or a bold attribute will match.</span></span>

[!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zBoldTagHelperCopy.cs?highlight=1,2&range=5-15)]

<span data-ttu-id="a8494-188">Pokud je do stejného příkazu přidáno více atributů, modul runtime je považuje za logický a.</span><span class="sxs-lookup"><span data-stu-id="a8494-188">When multiple attributes are added to the same statement, the runtime treats them as a logical-AND.</span></span> <span data-ttu-id="a8494-189">Například v následujícím kódu musí být prvek HTML pojmenovaný "bold" s atributem s názvem "bold" (`<bold bold />`), který se má shodovat.</span><span class="sxs-lookup"><span data-stu-id="a8494-189">For example, in the code below, an HTML element must be named "bold" with an attribute named "bold" (`<bold bold />`) to match.</span></span>

```csharp
[HtmlTargetElement("bold", Attributes = "bold")]
   ```

<span data-ttu-id="a8494-190">Můžete také použít `[HtmlTargetElement]` ke změně názvu cílového prvku.</span><span class="sxs-lookup"><span data-stu-id="a8494-190">You can also use the `[HtmlTargetElement]` to change the name of the targeted element.</span></span> <span data-ttu-id="a8494-191">Například pokud jste chtěli `BoldTagHelper` cílit `<MyBold>` značky, použijte následující atribut:</span><span class="sxs-lookup"><span data-stu-id="a8494-191">For example if you wanted the `BoldTagHelper` to target `<MyBold>` tags, you would use the following attribute:</span></span>

```csharp
[HtmlTargetElement("MyBold")]
```

## <a name="pass-a-model-to-a-tag-helper"></a><span data-ttu-id="a8494-192">Předání modelu do pomocné rutiny značky</span><span class="sxs-lookup"><span data-stu-id="a8494-192">Pass a model to a Tag Helper</span></span>

1. <span data-ttu-id="a8494-193">Přidejte složku *modelů* .</span><span class="sxs-lookup"><span data-stu-id="a8494-193">Add a *Models* folder.</span></span>

1. <span data-ttu-id="a8494-194">Do složky *modely* přidejte následující třídu `WebsiteContext`:</span><span class="sxs-lookup"><span data-stu-id="a8494-194">Add the following `WebsiteContext` class to the *Models* folder:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Models/WebsiteContext.cs)]

1. <span data-ttu-id="a8494-195">Do složky *TagHelpers* přidejte následující třídu `WebsiteInformationTagHelper`.</span><span class="sxs-lookup"><span data-stu-id="a8494-195">Add the following `WebsiteInformationTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/WebsiteInformationTagHelper.cs)]

   * <span data-ttu-id="a8494-196">Jak už bylo uvedeno výše, pomocník značek překládá názvy tříd Pascal C# -použita a vlastnosti pro pomocníky značek na [kebab případ](https://wiki.c2.com/?KebabCase).</span><span class="sxs-lookup"><span data-stu-id="a8494-196">As mentioned previously, tag helpers translates Pascal-cased C# class names and properties for tag helpers into [kebab case](https://wiki.c2.com/?KebabCase).</span></span> <span data-ttu-id="a8494-197">Proto pokud chcete použít `WebsiteInformationTagHelper` v Razor, budete psát `<website-information />`.</span><span class="sxs-lookup"><span data-stu-id="a8494-197">Therefore, to use the `WebsiteInformationTagHelper` in Razor, you'll write `<website-information />`.</span></span>

   * <span data-ttu-id="a8494-198">Nejste explicitně identifikovali cílový element s atributem `[HtmlTargetElement]`, takže výchozí `website-information` bude cílen.</span><span class="sxs-lookup"><span data-stu-id="a8494-198">You are not explicitly identifying the target element with the `[HtmlTargetElement]` attribute, so the default of `website-information` will be targeted.</span></span> <span data-ttu-id="a8494-199">Pokud jste použili následující atribut (Všimněte si, že není kebab Case, ale odpovídá názvu třídy):</span><span class="sxs-lookup"><span data-stu-id="a8494-199">If you applied the following attribute (note it's not kebab case but matches the class name):</span></span>

   ```csharp
   [HtmlTargetElement("WebsiteInformation")]
   ```

   <span data-ttu-id="a8494-200">Značka Case kebab `<website-information />` nesouhlasí.</span><span class="sxs-lookup"><span data-stu-id="a8494-200">The kebab case tag `<website-information />` wouldn't match.</span></span> <span data-ttu-id="a8494-201">Pokud chcete použít atribut `[HtmlTargetElement]`, použijte kebab případ, jak je znázorněno níže:</span><span class="sxs-lookup"><span data-stu-id="a8494-201">If you want use the `[HtmlTargetElement]` attribute, you would use kebab case as shown below:</span></span>

   ```csharp
   [HtmlTargetElement("Website-Information")]
   ```

   * <span data-ttu-id="a8494-202">Prvky, které jsou samy samouzavírací, nemají žádný obsah.</span><span class="sxs-lookup"><span data-stu-id="a8494-202">Elements that are self-closing have no content.</span></span> <span data-ttu-id="a8494-203">V tomto příkladu kód Razor použije samouzavírací značku, ale pomocná rutina značky vytvoří prvek [oddílu](https://www.w3.org/TR/html5/sections.html#the-section-element) (který není samo-závěrkou a píšete obsah uvnitř `section` elementu).</span><span class="sxs-lookup"><span data-stu-id="a8494-203">For this example, the Razor markup will use a self-closing tag, but the tag helper will be creating a [section](https://www.w3.org/TR/html5/sections.html#the-section-element) element (which isn't self-closing and you are writing content inside the `section` element).</span></span> <span data-ttu-id="a8494-204">Proto je nutné nastavit `TagMode` pro `StartTagAndEndTag` k zápisu výstupu.</span><span class="sxs-lookup"><span data-stu-id="a8494-204">Therefore, you need to set `TagMode` to `StartTagAndEndTag` to write output.</span></span> <span data-ttu-id="a8494-205">Alternativně můžete zadat komentář k nastavení čáry `TagMode` a zapsat značku s uzavírací značkou.</span><span class="sxs-lookup"><span data-stu-id="a8494-205">Alternatively, you can comment out the line setting `TagMode` and write markup with a closing tag.</span></span> <span data-ttu-id="a8494-206">(Příklad kódu je poskytován později v tomto kurzu.)</span><span class="sxs-lookup"><span data-stu-id="a8494-206">(Example markup is provided later in this tutorial.)</span></span>

   * <span data-ttu-id="a8494-207">@No__t_0 (znak dolaru) na následujícím řádku používá [interpolované řetězce](/dotnet/csharp/language-reference/keywords/interpolated-strings):</span><span class="sxs-lookup"><span data-stu-id="a8494-207">The `$` (dollar sign) in the following line uses an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings):</span></span>

   ```cshtml
   $@"<ul><li><strong>Version:</strong> {Info.Version}</li>
   ```

1. <span data-ttu-id="a8494-208">Do zobrazení *About. cshtml* přidejte následující kód.</span><span class="sxs-lookup"><span data-stu-id="a8494-208">Add the following markup to the *About.cshtml* view.</span></span> <span data-ttu-id="a8494-209">Zvýrazněný kód zobrazí informace o webu.</span><span class="sxs-lookup"><span data-stu-id="a8494-209">The highlighted markup displays the web site information.</span></span>

   [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?highlight=1,4-8, 18-999)]

   > [!NOTE]
   > <span data-ttu-id="a8494-210">Ve značce Razor zobrazené níže:</span><span class="sxs-lookup"><span data-stu-id="a8494-210">In the Razor markup shown below:</span></span>
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?range=18-18)]
   >
   > <span data-ttu-id="a8494-211">Razor ví, že atribut `info` je třída, nikoli řetězec a vy chcete napsat C# kód.</span><span class="sxs-lookup"><span data-stu-id="a8494-211">Razor knows the `info` attribute is a class, not a string, and you want to write C# code.</span></span> <span data-ttu-id="a8494-212">Jakýkoli pomocný atribut neřetězcové značky by měl být napsán bez `@`ho znaku.</span><span class="sxs-lookup"><span data-stu-id="a8494-212">Any non-string tag helper attribute should be written without the `@` character.</span></span>

1. <span data-ttu-id="a8494-213">Spusťte aplikaci a přejděte do zobrazení o webu, kde najdete informace o webovém serveru.</span><span class="sxs-lookup"><span data-stu-id="a8494-213">Run the app, and navigate to the About view to see the web site information.</span></span>

   > [!NOTE]
   > <span data-ttu-id="a8494-214">Můžete použít následující kód s uzavírací značkou a odebrat čáru s `TagMode.StartTagAndEndTag` v Pomocníkovi značek:</span><span class="sxs-lookup"><span data-stu-id="a8494-214">You can use the following markup with a closing tag and remove the line with `TagMode.StartTagAndEndTag` in the tag helper:</span></span>
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutNotSelfClosing.cshtml?range=20-21)]

## <a name="condition-tag-helper"></a><span data-ttu-id="a8494-215">Pomocník značek podmínky</span><span class="sxs-lookup"><span data-stu-id="a8494-215">Condition Tag Helper</span></span>

<span data-ttu-id="a8494-216">Pomocník značek podmínky vykreslí výstup, když prošla pravdivá hodnota.</span><span class="sxs-lookup"><span data-stu-id="a8494-216">The condition tag helper renders output when passed a true value.</span></span>

1. <span data-ttu-id="a8494-217">Do složky *TagHelpers* přidejte následující třídu `ConditionTagHelper`.</span><span class="sxs-lookup"><span data-stu-id="a8494-217">Add the following `ConditionTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/ConditionTagHelper.cs)]

1. <span data-ttu-id="a8494-218">Obsah souboru *views/Home/index. cshtml* nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a8494-218">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Index.cshtml)]

1. <span data-ttu-id="a8494-219">Metodu `Index` v kontroleru `Home` nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a8494-219">Replace the `Index` method in the `Home` controller with the following code:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Controllers/HomeController.cs?range=9-18)]

1. <span data-ttu-id="a8494-220">Spusťte aplikaci a přejděte na domovskou stránku.</span><span class="sxs-lookup"><span data-stu-id="a8494-220">Run the app and browse to the home page.</span></span> <span data-ttu-id="a8494-221">Značky v podmíněném `div` nebudou vykresleny.</span><span class="sxs-lookup"><span data-stu-id="a8494-221">The markup in the conditional `div` won't be rendered.</span></span> <span data-ttu-id="a8494-222">Připojí řetězec dotazu `?approved=true` k adrese URL (například `http://localhost:1235/Home/Index?approved=true`).</span><span class="sxs-lookup"><span data-stu-id="a8494-222">Append the query string `?approved=true` to the URL (for example, `http://localhost:1235/Home/Index?approved=true`).</span></span> <span data-ttu-id="a8494-223">`approved` je nastavena na hodnotu true a zobrazí se podmíněný kód.</span><span class="sxs-lookup"><span data-stu-id="a8494-223">`approved` is set to true and the conditional markup will be displayed.</span></span>

> [!NOTE]
> <span data-ttu-id="a8494-224">Použijte operátor [nameof](/dotnet/csharp/language-reference/keywords/nameof) k určení atributu pro cílení namísto zadání řetězce jako u pomocníka s tučnými značkami:</span><span class="sxs-lookup"><span data-stu-id="a8494-224">Use the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator to specify the attribute to target rather than specifying a string as you did with the bold tag helper:</span></span>
>
> [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zConditionTagHelperCopy.cs?highlight=1,2,5&range=5-18)]
>
> <span data-ttu-id="a8494-225">Operátor [nameof](/dotnet/csharp/language-reference/keywords/nameof) bude chránit kód by měl být někdy refaktorované (název můžeme změnit na `RedCondition`).</span><span class="sxs-lookup"><span data-stu-id="a8494-225">The [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator will protect the code should it ever be refactored (we might want to change the name to `RedCondition`).</span></span>

### <a name="avoid-tag-helper-conflicts"></a><span data-ttu-id="a8494-226">Vyhnout se konfliktům pomocníka značek</span><span class="sxs-lookup"><span data-stu-id="a8494-226">Avoid Tag Helper conflicts</span></span>

<span data-ttu-id="a8494-227">V této části napíšete dvojici pomocníků značek pro Automatické propojování.</span><span class="sxs-lookup"><span data-stu-id="a8494-227">In this section, you write a pair of auto-linking tag helpers.</span></span> <span data-ttu-id="a8494-228">První nahradí značku obsahující adresu URL začínající řetězcem HTTP na značku kotvy HTML obsahující stejnou adresu URL (a tím poskytuje odkaz na adresu URL).</span><span class="sxs-lookup"><span data-stu-id="a8494-228">The first will replace markup containing a URL starting with HTTP to an HTML anchor tag containing the same URL (and thus yielding a link to the URL).</span></span> <span data-ttu-id="a8494-229">Druhý postup bude stejný jako u adresy URL začínající na WWW.</span><span class="sxs-lookup"><span data-stu-id="a8494-229">The second will do the same for a URL starting with WWW.</span></span>

<span data-ttu-id="a8494-230">Vzhledem k tomu, že tyto dva pomocníky úzce souvisejí a vy je můžete v budoucnu Refaktorovat, uchováváme je ve stejném souboru.</span><span class="sxs-lookup"><span data-stu-id="a8494-230">Because these two helpers are closely related and you may refactor them in the future, we'll keep them in the same file.</span></span>

1. <span data-ttu-id="a8494-231">Do složky *TagHelpers* přidejte následující třídu `AutoLinkerHttpTagHelper`.</span><span class="sxs-lookup"><span data-stu-id="a8494-231">Add the following `AutoLinkerHttpTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=7-19)]

   >[!NOTE]
   ><span data-ttu-id="a8494-232">Třída `AutoLinkerHttpTagHelper` cílí `p` prvky a pomocí [regulárního výrazu](/dotnet/standard/base-types/regular-expression-language-quick-reference) vytvoří kotvu.</span><span class="sxs-lookup"><span data-stu-id="a8494-232">The `AutoLinkerHttpTagHelper` class targets `p` elements and uses [Regex](/dotnet/standard/base-types/regular-expression-language-quick-reference) to create the anchor.</span></span>

1. <span data-ttu-id="a8494-233">Na konec souboru *views/Home/Contact. cshtml* přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="a8494-233">Add the following markup to the end of the *Views/Home/Contact.cshtml* file:</span></span>

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=19)]

1. <span data-ttu-id="a8494-234">Spusťte aplikaci a ověřte, že pomocná rutina značky vykresluje kotvu správně.</span><span class="sxs-lookup"><span data-stu-id="a8494-234">Run the app and verify that the tag helper renders the anchor correctly.</span></span>

1. <span data-ttu-id="a8494-235">Aktualizujte třídu `AutoLinker` tak, aby zahrnovala `AutoLinkerWwwTagHelper`, který převede text na značku kotvy, který obsahuje také původní text na webové stránce.</span><span class="sxs-lookup"><span data-stu-id="a8494-235">Update the `AutoLinker` class to include the `AutoLinkerWwwTagHelper` which will convert www text to an anchor tag that also contains the original www text.</span></span> <span data-ttu-id="a8494-236">Aktualizovaný kód je zvýrazněný níže:</span><span class="sxs-lookup"><span data-stu-id="a8494-236">The updated code is highlighted below:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?highlight=15-34&range=7-34)]

1. <span data-ttu-id="a8494-237">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a8494-237">Run the app.</span></span> <span data-ttu-id="a8494-238">Všimněte si, že text www se vykresluje jako odkaz, ale text HTTP není.</span><span class="sxs-lookup"><span data-stu-id="a8494-238">Notice the www text is rendered as a link but the HTTP text isn't.</span></span> <span data-ttu-id="a8494-239">Pokud umístíte bod přerušení do obou tříd, vidíte, že se nejprve spustí pomocná třída značek HTTP.</span><span class="sxs-lookup"><span data-stu-id="a8494-239">If you put a break point in both classes, you can see that the HTTP tag helper class runs first.</span></span> <span data-ttu-id="a8494-240">Problémem je, že výstup pomocníka značek je uložen do mezipaměti a když je spuštěná pomocná rutina webové značky, přepíše výstup uložený v mezipaměti z pomocné rutiny značky HTTP.</span><span class="sxs-lookup"><span data-stu-id="a8494-240">The problem is that the tag helper output is cached, and when the WWW tag helper is run, it overwrites the cached output from the HTTP tag helper.</span></span> <span data-ttu-id="a8494-241">Později v tomto kurzu se dozvíte, jak řídit pořadí, ve kterém se budou zobrazovat pomocníka značek.</span><span class="sxs-lookup"><span data-stu-id="a8494-241">Later in the tutorial we'll see how to control the order that tag helpers run in.</span></span> <span data-ttu-id="a8494-242">Kód opravujeme následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="a8494-242">We'll fix the code with the following:</span></span>

   [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10,21,22,26&range=8-37)]

   > [!NOTE]
   > <span data-ttu-id="a8494-243">V první edici pomocníků značek pro Automatické propojování jste získali obsah cíle s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a8494-243">In the first edition of the auto-linking tag helpers, you got the content of the target with the following code:</span></span>
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=12)]
   >
   > <span data-ttu-id="a8494-244">To znamená, že zavoláte `GetChildContentAsync` pomocí `TagHelperOutput` předaného do metody `ProcessAsync`.</span><span class="sxs-lookup"><span data-stu-id="a8494-244">That is, you call `GetChildContentAsync` using the `TagHelperOutput` passed into the `ProcessAsync` method.</span></span> <span data-ttu-id="a8494-245">Jak bylo zmíněno dříve, protože výstup je uložen do mezipaměti, poslední pomocník značek spouští službu WINS.</span><span class="sxs-lookup"><span data-stu-id="a8494-245">As mentioned previously, because the output is cached, the last tag helper to run wins.</span></span> <span data-ttu-id="a8494-246">Vyřešili jste tento problém s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a8494-246">You fixed that problem with the following code:</span></span>
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?range=34-35)]
   >
   > <span data-ttu-id="a8494-247">Výše uvedený kód zkontroluje, zda byl obsah změněn a v případě, že má, získá obsah z výstupní vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="a8494-247">The code above checks to see if the content has been modified, and if it has, it gets the content from the output buffer.</span></span>

1. <span data-ttu-id="a8494-248">Spusťte aplikaci a ověřte, že tyto dva odkazy fungují podle očekávání.</span><span class="sxs-lookup"><span data-stu-id="a8494-248">Run the app and verify that the two links work as expected.</span></span> <span data-ttu-id="a8494-249">I když se může zdát, že je naše pomocník značek automatického linkeru správný a úplný, má malý problém.</span><span class="sxs-lookup"><span data-stu-id="a8494-249">While it might appear our auto linker tag helper is correct and complete, it has a subtle problem.</span></span> <span data-ttu-id="a8494-250">Pokud se jako první spustí pomocník webové značky, odkazy na webové odkazy nebudou správné.</span><span class="sxs-lookup"><span data-stu-id="a8494-250">If the WWW tag helper runs first, the www links won't be correct.</span></span> <span data-ttu-id="a8494-251">Aktualizujte kód přidáním `Order` přetížení pro řízení pořadí, ve kterém se značka spouští.</span><span class="sxs-lookup"><span data-stu-id="a8494-251">Update the code by adding the `Order` overload to control the order that the tag runs in.</span></span> <span data-ttu-id="a8494-252">Vlastnost `Order` určuje pořadí spouštění relativně k jiným pomocníkům značek cílících na stejný prvek.</span><span class="sxs-lookup"><span data-stu-id="a8494-252">The `Order` property determines the execution order relative to other tag helpers targeting the same element.</span></span> <span data-ttu-id="a8494-253">Výchozí hodnota pořadí je nula a instance s nižšími hodnotami jsou spouštěny jako první.</span><span class="sxs-lookup"><span data-stu-id="a8494-253">The default order value is zero and instances with lower values are executed first.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?highlight=5,6,7,8&range=8-15)]

   <span data-ttu-id="a8494-254">Předchozí kód zaručuje, že pomocník značek HTTP se spustí před pomocníkem webové značky.</span><span class="sxs-lookup"><span data-stu-id="a8494-254">The preceding code guarantees that the HTTP tag helper runs before the WWW tag helper.</span></span> <span data-ttu-id="a8494-255">Změňte `Order` na `MaxValue` a ověřte, zda je kód vygenerovaný pro značku WWW nesprávný.</span><span class="sxs-lookup"><span data-stu-id="a8494-255">Change `Order` to `MaxValue` and verify that the markup generated for the WWW tag is incorrect.</span></span>

## <a name="inspect-and-retrieve-child-content"></a><span data-ttu-id="a8494-256">Kontrola a načtení podřízeného obsahu</span><span class="sxs-lookup"><span data-stu-id="a8494-256">Inspect and retrieve child content</span></span>

<span data-ttu-id="a8494-257">Pomocník značek poskytuje několik vlastností pro načtení obsahu.</span><span class="sxs-lookup"><span data-stu-id="a8494-257">The tag helpers provide several properties to retrieve content.</span></span>

* <span data-ttu-id="a8494-258">Výsledek `GetChildContentAsync` lze připojit k `output.Content`.</span><span class="sxs-lookup"><span data-stu-id="a8494-258">The result of `GetChildContentAsync` can be appended to `output.Content`.</span></span>
* <span data-ttu-id="a8494-259">Výsledek `GetChildContentAsync` lze zkontrolovat pomocí `GetContent`.</span><span class="sxs-lookup"><span data-stu-id="a8494-259">You can inspect the result of `GetChildContentAsync` with `GetContent`.</span></span>
* <span data-ttu-id="a8494-260">Pokud upravíte `output.Content`, text Taghelperu se nespustí ani nebude vykreslený, pokud nebudete volat `GetChildContentAsync` jako v našem příkladu automatického linkeru:</span><span class="sxs-lookup"><span data-stu-id="a8494-260">If you modify `output.Content`, the TagHelper body won't be executed or rendered unless you call `GetChildContentAsync` as in our auto-linker sample:</span></span>

[!code-csharp[](../../views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10&range=8-21)]

* <span data-ttu-id="a8494-261">Více volání `GetChildContentAsync` vrací stejnou hodnotu a znovu nespustí `TagHelper` tělo, pokud nepředáte parametr false, který označuje, že nechcete použít výsledek uložený v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="a8494-261">Multiple calls to `GetChildContentAsync` returns the same value and doesn't re-execute the `TagHelper` body unless you pass in a false parameter indicating not to use the cached result.</span></span>

## <a name="load-minified-partial-view-taghelper"></a><span data-ttu-id="a8494-262">Načíst minifikovaného částečného zobrazení Taghelperu</span><span class="sxs-lookup"><span data-stu-id="a8494-262">Load minified partial view TagHelper</span></span>

<span data-ttu-id="a8494-263">V produkčních prostředích je možné výkon zlepšit načtením částečně minifikovaného zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a8494-263">In production environments, performance can be improved by loading minified partial views.</span></span> <span data-ttu-id="a8494-264">Využití částečného zobrazení minifikovaného v produkčním prostředí:</span><span class="sxs-lookup"><span data-stu-id="a8494-264">To take advantage of minified partial view in production:</span></span>

* <span data-ttu-id="a8494-265">Vytvořte nebo nastavte proces před sestavením, který minifies částečná zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a8494-265">Create/set up a pre-build process that minifies partial views.</span></span>
* <span data-ttu-id="a8494-266">Pomocí následujícího kódu načtěte minifikovaného částečná zobrazení v nevývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="a8494-266">Use the following code to load  minified partial views in non-development environments.</span></span>

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/MinifiedVersionTagHelper.cs?name=snippet)]

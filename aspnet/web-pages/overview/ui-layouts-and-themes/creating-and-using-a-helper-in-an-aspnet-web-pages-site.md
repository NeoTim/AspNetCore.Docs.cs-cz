---
uid: web-pages/overview/ui-layouts-and-themes/creating-and-using-a-helper-in-an-aspnet-web-pages-site
title: Vytvoření a použití pomocné rutiny v ASP.NET Web Pages lokality (Razor) | Dokumentace Microsoftu
author: Rick-Anderson
description: Tento článek popisuje, jak vytvořit pomocné rutiny na webu rozhraní ASP.NET Web Pages (Razor). Pomocné rutiny je opětovně použitelnou komponentu, která obsahuje kód a značky výkonu...
ms.author: riande
ms.date: 02/17/2014
ms.assetid: 46bff772-01e0-40f0-9ae6-9e18c5442ee6
msc.legacyurl: /web-pages/overview/ui-layouts-and-themes/creating-and-using-a-helper-in-an-aspnet-web-pages-site
msc.type: authoredcontent
ms.openlocfilehash: 3b54ba8a35a354eb0562a47866cd8b5dcb66bc86
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021505"
---
<a name="creating-and-using-a-helper-in-an-aspnet-web-pages-razor-site"></a><span data-ttu-id="c828e-104">Vytvoření a použití pomocné rutiny na webu technologie ASP.NET Web Pages (Razor)</span><span class="sxs-lookup"><span data-stu-id="c828e-104">Creating and Using a Helper in an ASP.NET Web Pages (Razor) Site</span></span>
====================
<span data-ttu-id="c828e-105">podle [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="c828e-105">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="c828e-106">Tento článek popisuje, jak vytvořit pomocné rutiny na webu rozhraní ASP.NET Web Pages (Razor).</span><span class="sxs-lookup"><span data-stu-id="c828e-106">This article describes how to create a helper in an ASP.NET Web Pages (Razor) website.</span></span> <span data-ttu-id="c828e-107">A *pomocné rutiny* je opětovně použitelnou komponentu, která obsahuje kód a značky k provedení úkolu, který může být zdlouhavý nebo složitý.</span><span class="sxs-lookup"><span data-stu-id="c828e-107">A *helper* is a reusable component that includes code and markup to perform a task that might be tedious or complex.</span></span>
> 
> <span data-ttu-id="c828e-108">**Co se dozvíte:**</span><span class="sxs-lookup"><span data-stu-id="c828e-108">**What you'll learn:**</span></span> 
> 
> - <span data-ttu-id="c828e-109">Jak vytvořit a používat jednoduché pomocné rutiny.</span><span class="sxs-lookup"><span data-stu-id="c828e-109">How to create and use a simple helper.</span></span>
> 
> <span data-ttu-id="c828e-110">Toto jsou funkce technologie ASP.NET v následujícím článku:</span><span class="sxs-lookup"><span data-stu-id="c828e-110">These are the ASP.NET features introduced in the article:</span></span>
> 
> - <span data-ttu-id="c828e-111">`@helper` Syntaxe.</span><span class="sxs-lookup"><span data-stu-id="c828e-111">The `@helper` syntax.</span></span>
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="c828e-112">V tomto kurzu použili verze softwaru</span><span class="sxs-lookup"><span data-stu-id="c828e-112">Software versions used in the tutorial</span></span>
> 
> 
> - <span data-ttu-id="c828e-113">Webové stránky ASP.NET (Razor) 3</span><span class="sxs-lookup"><span data-stu-id="c828e-113">ASP.NET Web Pages (Razor) 3</span></span>
>   
> 
> <span data-ttu-id="c828e-114">V tomto kurzu se také pracuje s ASP.NET Web Pages 2.</span><span class="sxs-lookup"><span data-stu-id="c828e-114">This tutorial also works with ASP.NET Web Pages 2.</span></span>


## <a name="overview-of-helpers"></a><span data-ttu-id="c828e-115">Přehled pomocné rutiny</span><span class="sxs-lookup"><span data-stu-id="c828e-115">Overview of Helpers</span></span>

<span data-ttu-id="c828e-116">Pokud potřebujete stejné úlohy provádět na různých stránkách ve vaší lokalitě, můžete použít pomocné rutiny.</span><span class="sxs-lookup"><span data-stu-id="c828e-116">If you need to perform the same tasks on different pages in your site, you can use a helper.</span></span> <span data-ttu-id="c828e-117">Rozhraní ASP.NET Web Pages obsahuje několik pomocných rutin a existuje mnoho dalších, které si můžete stáhnout a nainstalovat.</span><span class="sxs-lookup"><span data-stu-id="c828e-117">ASP.NET Web Pages includes a number of helpers, and there are many more that you can download and install.</span></span> <span data-ttu-id="c828e-118">(Seznam integrovaných pomocných rutin v ASP.NET Web Pages je uveden v [Stručná referenční příručka rozhraní API technologie ASP.NET](https://go.microsoft.com/fwlink/?LinkId=202907).) Pokud žádná z existující pomocné rutiny nevyhovuje vašim potřebám, můžete vytvořit vlastní pomocné rutiny.</span><span class="sxs-lookup"><span data-stu-id="c828e-118">(A list of the built-in helpers in ASP.NET Web Pages is listed in the [ASP.NET API Quick Reference](https://go.microsoft.com/fwlink/?LinkId=202907).) If none of the existing helpers meet your needs, you can create your own helper.</span></span>

<span data-ttu-id="c828e-119">Pomocné rutiny vám umožní používat běžné blok kódu na několika stránkách.</span><span class="sxs-lookup"><span data-stu-id="c828e-119">A helper lets you use a common block of code across multiple pages.</span></span> <span data-ttu-id="c828e-120">Předpokládejme, že na stránce často chcete vytvořit položku Poznámka, která je nastavena kromě Normální odstavce.</span><span class="sxs-lookup"><span data-stu-id="c828e-120">Suppose that in your page you often want to create a note item that's set apart from normal paragraphs.</span></span> <span data-ttu-id="c828e-121">Třeba poznámky se vytvoří jako `<div>` element, který byl navržen jako pole s ohraničením.</span><span class="sxs-lookup"><span data-stu-id="c828e-121">Perhaps the note is created as a `<div>` element that's styled as a box with a border.</span></span> <span data-ttu-id="c828e-122">Místo na stránku přidat tento stejný kód, pokaždé, když chcete zobrazit poznámky, můžete zabalit kód jako pomůcka pro.</span><span class="sxs-lookup"><span data-stu-id="c828e-122">Rather than add this same markup to a page every time you want to display a note, you can package the markup as a helper.</span></span> <span data-ttu-id="c828e-123">Pak můžete vložit poznámky s jedním řádkem kódu kdekoli ho potřebujete.</span><span class="sxs-lookup"><span data-stu-id="c828e-123">You can then insert the note with a single line of code anywhere you need it.</span></span>

<span data-ttu-id="c828e-124">Použití pomocné rutiny tímto způsobem díky kód v jednotlivých stránek, jednodušší a snadněji čitelné.</span><span class="sxs-lookup"><span data-stu-id="c828e-124">Using a helper like this makes the code in each of your pages simpler and easier to read.</span></span> <span data-ttu-id="c828e-125">Je také usnadňuje udržovat lokalitu, protože pokud potřebujete změnit vzhled poznámky, můžete změnit kód v jednom místě.</span><span class="sxs-lookup"><span data-stu-id="c828e-125">It also makes it easier to maintain your site, because if you need to change how the notes look, you can change the markup in one place.</span></span>

## <a name="creating-a-helper"></a><span data-ttu-id="c828e-126">Vytváření pomocné rutiny</span><span class="sxs-lookup"><span data-stu-id="c828e-126">Creating a Helper</span></span>

<span data-ttu-id="c828e-127">Tento postup ukazuje, jak vytvořit pomocné rutiny, která vytvoří Všimněte si, jak bylo právě popsáno.</span><span class="sxs-lookup"><span data-stu-id="c828e-127">This procedure shows you how to create the helper that creates the note, as just described.</span></span> <span data-ttu-id="c828e-128">Toto je jednoduchý příklad, ale vlastní pomocné rutiny může obsahovat libovolný značek a kódu ASP.NET, které potřebujete.</span><span class="sxs-lookup"><span data-stu-id="c828e-128">This is a simple example, but the custom helper can include any markup and ASP.NET code that you need.</span></span>

1. <span data-ttu-id="c828e-129">V kořenové složce webové stránky, vytvořte složku s názvem *aplikace\_kód*.</span><span class="sxs-lookup"><span data-stu-id="c828e-129">In the root folder of the website, create a folder named *App\_Code*.</span></span> <span data-ttu-id="c828e-130">Jde o název vyhrazené složky v technologii ASP.NET, kde můžete vložit kód pro komponenty, jako jsou pomocné rutiny.</span><span class="sxs-lookup"><span data-stu-id="c828e-130">This is a reserved folder name in ASP.NET where you can put code for components like helpers.</span></span>
2. <span data-ttu-id="c828e-131">V *aplikace\_kód* vytvořte novou složku *.cshtml* soubor a pojmenujte ho *MyHelpers.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="c828e-131">In the *App\_Code* folder create a new *.cshtml* file and name it *MyHelpers.cshtml*.</span></span>
3. <span data-ttu-id="c828e-132">Nahraďte existující obsah následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c828e-132">Replace the existing content with the following:</span></span>

    [!code-cshtml[Main](creating-and-using-a-helper-in-an-aspnet-web-pages-site/samples/sample1.cshtml)]

    <span data-ttu-id="c828e-133">Tento kód použije `@helper` syntaxi pro deklaraci nového Pomocníka s názvem `MakeNote`.</span><span class="sxs-lookup"><span data-stu-id="c828e-133">The code uses the `@helper` syntax to declare a new helper named `MakeNote`.</span></span> <span data-ttu-id="c828e-134">Tento konkrétní pomocné rutiny umožňuje předat parametr s názvem `content` , který může obsahovat kombinaci textu a kódu.</span><span class="sxs-lookup"><span data-stu-id="c828e-134">This particular helper lets you pass a parameter named `content` that can contain a combination of text and markup.</span></span> <span data-ttu-id="c828e-135">Pomocné rutiny vloží do těla Poznámka: pomocí řetězce `@content` proměnné.</span><span class="sxs-lookup"><span data-stu-id="c828e-135">The helper inserts the string into the note body using the `@content` variable.</span></span>

    <span data-ttu-id="c828e-136">Všimněte si, že je soubor s názvem *MyHelpers.cshtml*, ale má název pomocné rutiny `MakeNote`.</span><span class="sxs-lookup"><span data-stu-id="c828e-136">Notice that the file is named *MyHelpers.cshtml*, but the helper is named `MakeNote`.</span></span> <span data-ttu-id="c828e-137">Více vlastních pomocných rutin můžete umístit do jednoho souboru.</span><span class="sxs-lookup"><span data-stu-id="c828e-137">You can put multiple custom helpers into a single file.</span></span>
4. <span data-ttu-id="c828e-138">Soubor uložte a zavřete.</span><span class="sxs-lookup"><span data-stu-id="c828e-138">Save and close the file.</span></span>

## <a name="using-the-helper-in-a-page"></a><span data-ttu-id="c828e-139">Na stránce využitím pomocné rutiny</span><span class="sxs-lookup"><span data-stu-id="c828e-139">Using the Helper in a Page</span></span>

1. <span data-ttu-id="c828e-140">V kořenové složce vytvořte nový prázdný soubor s názvem *TestHelper.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="c828e-140">In the root folder, create a new blank file called *TestHelper.cshtml*.</span></span>
2. <span data-ttu-id="c828e-141">Přidejte následující kód do souboru:</span><span class="sxs-lookup"><span data-stu-id="c828e-141">Add the following code to the file:</span></span>

    [!code-html[Main](creating-and-using-a-helper-in-an-aspnet-web-pages-site/samples/sample2.html)]

    <span data-ttu-id="c828e-142">Chcete-li volat pomocné rutiny, které jste vytvořili, použijte `@` za nímž následuje název souboru, kde pomocné rutiny je, tečku a potom název pomocné rutiny.</span><span class="sxs-lookup"><span data-stu-id="c828e-142">To call the helper you created, use `@` followed by the file name where the helper is, a dot, and then the helper name.</span></span> <span data-ttu-id="c828e-143">(Pokud jste měli více složek *aplikace\_kód* složky, můžete použít syntaxi `@FolderName.FileName.HelperName` volat váš pomocný objekt v rámci žádné vnořené složky).</span><span class="sxs-lookup"><span data-stu-id="c828e-143">(If you had multiple folders in the *App\_Code* folder, you could use the syntax `@FolderName.FileName.HelperName` to call your helper within any nested folder level).</span></span> <span data-ttu-id="c828e-144">Text, který přidáte do uvozovek v závorkách je text, který pomocné rutiny se zobrazí jako součást Poznámka na webové stránce.</span><span class="sxs-lookup"><span data-stu-id="c828e-144">The text that you add in quotation marks within the parentheses is the text that the helper will display as part of the note in the web page.</span></span>
3. <span data-ttu-id="c828e-145">Uložit na stránku a spustíte ji v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="c828e-145">Save the page and run it in a browser.</span></span> <span data-ttu-id="c828e-146">Pomocná rutina generuje Poznámka položku přímo ve kterém jste volali pomocné rutiny: mezi dva odstavce.</span><span class="sxs-lookup"><span data-stu-id="c828e-146">The helper generates the note item right where you called the helper: between the two paragraphs.</span></span>

    ![Snímek obrazovky zobrazující stránku v prohlížeči a způsob, jakým pomocné rutiny vygeneruje kód, který vloží polem a zadaný text.](creating-and-using-a-helper-in-an-aspnet-web-pages-site/_static/image1.jpg)

## <a name="additional-resources"></a><span data-ttu-id="c828e-148">Další prostředky</span><span class="sxs-lookup"><span data-stu-id="c828e-148">Additional Resources</span></span>


<span data-ttu-id="c828e-149">[Vodorovné menu jako pomocné rutiny Razor](http://mikepope.com/blog/DisplayBlog.aspx?permalink=2341).</span><span class="sxs-lookup"><span data-stu-id="c828e-149">[Horizontal menu as a Razor helper](http://mikepope.com/blog/DisplayBlog.aspx?permalink=2341).</span></span> <span data-ttu-id="c828e-150">Tento blog položky ze Mike Pope ukazuje postup vytvoření vodorovné nabídce jako pomůcka pro použití značek, šablon stylů CSS a kódu.</span><span class="sxs-lookup"><span data-stu-id="c828e-150">This blog entry by Mike Pope shows how to create a horizontal menu as a helper using markup, CSS, and code.</span></span>

<span data-ttu-id="c828e-151">[Využití HTML5 v rozhraní ASP.NET Web Pages pomocné rutiny pro službu WebMatrix a ASP.NET MVC3](http://geekswithblogs.net/wildturtle/archive/2010/11/08/html5-in-asp.net-web-pages-helpers-for-webmatrix-and_aspnet_mvc3.aspx).</span><span class="sxs-lookup"><span data-stu-id="c828e-151">[Leveraging HTML5 in ASP.NET Web Pages Helpers for WebMatrix and ASP.NET MVC3](http://geekswithblogs.net/wildturtle/archive/2010/11/08/html5-in-asp.net-web-pages-helpers-for-webmatrix-and_aspnet_mvc3.aspx).</span></span> <span data-ttu-id="c828e-152">Tento blog položky ze Sam Abraham ukazuje pomocné rutiny, která vykresluje HTML5 `Canvas` elementu.</span><span class="sxs-lookup"><span data-stu-id="c828e-152">This blog entry by Sam Abraham shows a helper that renders an HTML5 `Canvas` element.</span></span>

<span data-ttu-id="c828e-153">[Rozdíl mezi @Helpers a @Functions v nástroji WebMatrix](http://www.mikesdotnetting.com/Article/173/The-Difference-Between-@Helpers-and-@Functions-In-WebMatrix).</span><span class="sxs-lookup"><span data-stu-id="c828e-153">[The Difference Between @Helpers and @Functions in WebMatrix](http://www.mikesdotnetting.com/Article/173/The-Difference-Between-@Helpers-and-@Functions-In-WebMatrix).</span></span> <span data-ttu-id="c828e-154">Popisuje tuto položku blogu podle Mike Brind `@helper` syntaxi a `@function` syntaxi a kdy se má použít.</span><span class="sxs-lookup"><span data-stu-id="c828e-154">This blog entry by Mike Brind describes `@helper` syntax and `@function` syntax and when to use each.</span></span>

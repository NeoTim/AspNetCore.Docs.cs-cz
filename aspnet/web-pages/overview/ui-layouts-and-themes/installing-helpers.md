---
uid: web-pages/overview/ui-layouts-and-themes/installing-helpers
title: Instalace pomocné rutiny v ASP.NET Web Pages lokality (Razor) | Dokumentace Microsoftu
author: Rick-Anderson
description: Tento článek popisuje postup instalace pomocné rutiny na webu rozhraní ASP.NET Web Pages (Razor). Pomocné rutiny je opětovně použitelnou komponentu, která obsahuje kód a značky pro každý...
ms.author: riande
ms.date: 02/18/2014
ms.assetid: 5e968ead-906a-45ea-ac2a-c70e57e1a9b1
msc.legacyurl: /web-pages/overview/ui-layouts-and-themes/installing-helpers
msc.type: authoredcontent
ms.openlocfilehash: 5ad717cd7c64e830ce66d5e1361d0eb6ef3cbbec
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021362"
---
<a name="installing-a-helper-in-an-aspnet-web-pages-razor-site"></a><span data-ttu-id="cd89d-104">Instalace pomocné rutiny na webu rozhraní ASP.NET Web Pages (Razor)</span><span class="sxs-lookup"><span data-stu-id="cd89d-104">Installing a Helper in an ASP.NET Web Pages (Razor) Site</span></span>
====================
<span data-ttu-id="cd89d-105">podle [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="cd89d-105">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="cd89d-106">Tento článek popisuje postup instalace pomocné rutiny na webu rozhraní ASP.NET Web Pages (Razor).</span><span class="sxs-lookup"><span data-stu-id="cd89d-106">This article describes how to install a helper in an ASP.NET Web Pages (Razor) website.</span></span> <span data-ttu-id="cd89d-107">A *pomocné rutiny* je opětovně použitelnou komponentu, která obsahuje kód a značky k provedení úkolu, který může být zdlouhavý nebo složitý.</span><span class="sxs-lookup"><span data-stu-id="cd89d-107">A *helper* is a reusable component that includes code and markup to perform a task that might be tedious or complex.</span></span>
> 
> <span data-ttu-id="cd89d-108">Co se dozvíte:</span><span class="sxs-lookup"><span data-stu-id="cd89d-108">What you'll learn:</span></span>
> 
> - <span data-ttu-id="cd89d-109">Postup instalace pomocné rutiny na webu vytvořené pomocí služby WebMatrix 3.</span><span class="sxs-lookup"><span data-stu-id="cd89d-109">How to install a helper in a website created using WebMatrix 3.</span></span>
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="cd89d-110">V tomto kurzu použili verze softwaru</span><span class="sxs-lookup"><span data-stu-id="cd89d-110">Software versions used in the tutorial</span></span>
> 
> 
> - <span data-ttu-id="cd89d-111">Služba WebMatrix 3</span><span class="sxs-lookup"><span data-stu-id="cd89d-111">WebMatrix 3</span></span>


## <a name="overview-of-helpers"></a><span data-ttu-id="cd89d-112">Přehled pomocné rutiny</span><span class="sxs-lookup"><span data-stu-id="cd89d-112">Overview of Helpers</span></span>

<span data-ttu-id="cd89d-113">Některé úlohy, které uživatelé často chtějí dělat na webových stránkách vyžadují velké množství kódu nebo vyžadovat další znalostní báze.</span><span class="sxs-lookup"><span data-stu-id="cd89d-113">Some tasks that people often want to do on web pages require a lot of code or require extra knowledge.</span></span> <span data-ttu-id="cd89d-114">Mezi příklady patří zobrazení grafu pro data. Vložení tlačítko "Sledovat" Twitter na stránce. odeslání e-mailů z vašeho webu; oříznutí nebo změny velikosti obrázků; pomocí služby PayPal pro váš web.</span><span class="sxs-lookup"><span data-stu-id="cd89d-114">Examples include displaying a chart for data; putting a Twitter "Follow" button on a page; sending email from your website; cropping or resizing images; using PayPal for your site.</span></span> <span data-ttu-id="cd89d-115">Abyste usnadnili snadnou udělat Tyhle druhy věcí, rozhraní ASP.NET Web Pages vám umožní používat *pomocné rutiny*.</span><span class="sxs-lookup"><span data-stu-id="cd89d-115">To make it easy to do these kinds of things, ASP.NET Web Pages lets you use *helpers*.</span></span> <span data-ttu-id="cd89d-116">Pomocné rutiny jsou součásti nainstalovat pro lokality a, které umožní vám provádět typické úlohy s použitím pouze řádku či dvou od kódu Razor.</span><span class="sxs-lookup"><span data-stu-id="cd89d-116">Helpers are components that you install for a site and that let you perform typical tasks by using just a line or two of Razor code.</span></span>

<span data-ttu-id="cd89d-117">ASP.NET Web Pages má několik pomocných rutin součástí.</span><span class="sxs-lookup"><span data-stu-id="cd89d-117">ASP.NET Web Pages has a few helpers built in.</span></span> <span data-ttu-id="cd89d-118">Mnoho pomocné rutiny jsou však k dispozici v balíčcích (doplňky), které jsou k dispozici pomocí Správce balíčků NuGet.</span><span class="sxs-lookup"><span data-stu-id="cd89d-118">However, many helpers are available in packages (add-ins) that are provided using the NuGet package manager.</span></span> <span data-ttu-id="cd89d-119">NuGet umožňuje vybrat balíček pro instalaci a pak se postará o všechny podrobnosti o instalaci.</span><span class="sxs-lookup"><span data-stu-id="cd89d-119">NuGet lets you select a package to install and then it takes care of all the details of the installation.</span></span>

## <a name="installing-a-helper-in-webmatrix-3"></a><span data-ttu-id="cd89d-120">Instalace pomocné rutiny v nástroji WebMatrix 3</span><span class="sxs-lookup"><span data-stu-id="cd89d-120">Installing a Helper in WebMatrix 3</span></span>

1. <span data-ttu-id="cd89d-121">Ve službě WebMatrix 3, klikněte na tlačítko **NuGet** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="cd89d-121">In WebMatrix 3, click the **NuGet** button.</span></span>

    ![Dialogové okno Galerie NuGet v nástroji WebMatrix](installing-helpers/_static/image1.png)
2. <span data-ttu-id="cd89d-123">To spustí Správce balíčků NuGet a zobrazí dostupné balíčky.</span><span class="sxs-lookup"><span data-stu-id="cd89d-123">This launches the NuGet package manager and displays available packages.</span></span> <span data-ttu-id="cd89d-124">Do vyhledávacího pole zadejte klíčové slovo pro pomocné rutiny, které chcete nainstalovat.</span><span class="sxs-lookup"><span data-stu-id="cd89d-124">In the search box, enter a keyword for the helper you want to install.</span></span>

    ![Dialogové okno Galerie NuGet v nástroji WebMatrix](installing-helpers/_static/image2.png)
3. <span data-ttu-id="cd89d-126">Vyberte balíček a pak klikněte na tlačítko **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="cd89d-126">Select the package and then click **Install**.</span></span> <span data-ttu-id="cd89d-127">Klikněte na tlačítko **Ano** když se zobrazí výzva, pokud chcete nainstalovat balíček a značí, že souhlasíte s podmínkami.</span><span class="sxs-lookup"><span data-stu-id="cd89d-127">Click **Yes** when asked if you want to install the package and indicate that you accept the terms.</span></span>

     <span data-ttu-id="cd89d-128">Pokud to je poprvé, kdy jste nainstalovali pomocné rutiny, vytvoří NuGet složky na vašem webu pro kód, který tvoří pomocné rutiny.</span><span class="sxs-lookup"><span data-stu-id="cd89d-128">If this is the first time you've installed a helper, NuGet creates folders in your website for the code that makes up the helper.</span></span>
4. <span data-ttu-id="cd89d-129">K odinstalaci pomocné rutiny, klikněte na tlačítko **Galerie** tlačítko, klikněte na tlačítko **nainstalováno** kartu a vyberte balíček, který chcete odinstalovat.</span><span class="sxs-lookup"><span data-stu-id="cd89d-129">To uninstall a helper, click the **Gallery** button, click the **Installed** tab, and pick the package you want to uninstall.</span></span>

## <a name="installing-the-twitter-helper"></a><span data-ttu-id="cd89d-130">Instalace pomocné rutiny Twitter</span><span class="sxs-lookup"><span data-stu-id="cd89d-130">Installing the Twitter helper</span></span>

<span data-ttu-id="cd89d-131">Nejnovější verzi rozhraní Twitter API není kompatibilní s Pomocník Twitter, kterou instalujete prostřednictvím balíčku NuGet.</span><span class="sxs-lookup"><span data-stu-id="cd89d-131">The latest version of the Twitter API is not compatible with the Twitter helper you install through NuGet.</span></span> <span data-ttu-id="cd89d-132">Místo toho [Pomocník Twitter se službou WebMatrix](twitter-helper.md) najdete informace o tom, jak nastavit pomocná rutina Twitteru ve vašem projektu.</span><span class="sxs-lookup"><span data-stu-id="cd89d-132">Instead, see the [Twitter Helper with WebMatrix](twitter-helper.md) topic for information about how to set up the Twitter helper in your project.</span></span>

<a id="Additional_Resources"></a>
## <a name="additional-resources"></a><span data-ttu-id="cd89d-133">Další prostředky</span><span class="sxs-lookup"><span data-stu-id="cd89d-133">Additional Resources</span></span>


[<span data-ttu-id="cd89d-134">Představujeme ASP.NET Web Pages 2 – základy programování</span><span class="sxs-lookup"><span data-stu-id="cd89d-134">Introducing ASP.NET Web Pages 2 - Programming Basics</span></span>](../getting-started/introducing-razor-syntax-c.md)

[<span data-ttu-id="cd89d-135">Pomocník Twitter pomocí služby WebMatrix</span><span class="sxs-lookup"><span data-stu-id="cd89d-135">Twitter Helper with WebMatrix</span></span>](twitter-helper.md)

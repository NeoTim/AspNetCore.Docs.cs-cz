---
uid: web-pages/overview/releases/running-v1-and-v2-sites-side-by-side
title: Spuštění různých verzí webových stránek ASP.NET (Razor) vedle sebe | Dokumentace Microsoftu
author: Rick-Anderson
description: Tento článek vysvětluje, jak spustit websites webových stránek ASP.NET (Razor) na stejném počítači nebo serveru, když weby jsou nakonfigurovány pro použití různých verzí...
ms.author: riande
ms.date: 02/10/2014
ms.assetid: a861409b-4ae6-4868-9e09-87edfac3535f
msc.legacyurl: /web-pages/overview/releases/running-v1-and-v2-sites-side-by-side
msc.type: authoredcontent
ms.openlocfilehash: e587398b430795c12a1dcee394852b4e2b8a0e44
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021170"
---
<a name="running-different-versions-of-aspnet-web-pages-razor-side-by-side"></a><span data-ttu-id="ccccd-103">Souběžné spuštění různých verzí webových stránek ASP.NET (Razor)</span><span class="sxs-lookup"><span data-stu-id="ccccd-103">Running Different Versions of ASP.NET Web Pages (Razor) Side by Side</span></span>
====================
<span data-ttu-id="ccccd-104">podle [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="ccccd-104">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="ccccd-105">Tento článek vysvětluje, jak spustit websites webových stránek ASP.NET (Razor) na stejném počítači nebo serveru, když weby jsou nakonfigurovány pro použití různých verzí z rozhraní ASP.NET Web Pages.</span><span class="sxs-lookup"><span data-stu-id="ccccd-105">This article explains how to run ASP.NET Web Pages (Razor) websites on the same computer or server when the websites are configured to use different versions of ASP.NET Web Pages.</span></span>
> 
> <span data-ttu-id="ccccd-106">Co se dozvíte:</span><span class="sxs-lookup"><span data-stu-id="ccccd-106">What you'll learn:</span></span>
> 
> - <span data-ttu-id="ccccd-107">Výchozí chování Novinky v technologii ASP.NET případě, že máte webů vytvořených s webovými stránkami ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="ccccd-107">What the default behavior is in ASP.NET when you have sites built with ASP.NET Web Pages.</span></span>
> - <span data-ttu-id="ccccd-108">Jak konfigurovat novou lokalitu ke spuštění ve starší verzi z rozhraní ASP.NET Web Pages.</span><span class="sxs-lookup"><span data-stu-id="ccccd-108">How to configure a new site to run with an older version of ASP.NET Web Pages.</span></span>
>   
> 
> <span data-ttu-id="ccccd-109">Toto je funkce technologie ASP.NET zavedené v následujícím článku:</span><span class="sxs-lookup"><span data-stu-id="ccccd-109">This is the ASP.NET feature introduced in the article:</span></span>
> 
> - <span data-ttu-id="ccccd-110">`webPages:Version` Nastavení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="ccccd-110">The `webPages:Version` configuration setting.</span></span>
>   
> 
> ## <a name="software-versions"></a><span data-ttu-id="ccccd-111">Verze softwaru</span><span class="sxs-lookup"><span data-stu-id="ccccd-111">Software versions</span></span>
> 
> 
> - <span data-ttu-id="ccccd-112">Webové stránky ASP.NET (Razor) 3</span><span class="sxs-lookup"><span data-stu-id="ccccd-112">ASP.NET Web Pages (Razor) 3</span></span>
>   
> 
> <span data-ttu-id="ccccd-113">V tomto kurzu se také pracuje s ASP.NET Web Pages 2 a ASP.NET Web Pages 1.0.</span><span class="sxs-lookup"><span data-stu-id="ccccd-113">This tutorial also works with ASP.NET Web Pages 2 and ASP.NET Web Pages 1.0.</span></span>


<span data-ttu-id="ccccd-114">ASP.NET Web Pages podporuje možnost spouštění websites vedle sebe.</span><span class="sxs-lookup"><span data-stu-id="ccccd-114">ASP.NET Web Pages supports the ability to run websites side by side.</span></span> <span data-ttu-id="ccccd-115">Tímto způsobem můžete i nadále spouštět starší aplikace ASP.NET Web Pages, vytvářet nové aplikace ASP.NET Web Pages a spustit všechny z nich na stejném počítači.</span><span class="sxs-lookup"><span data-stu-id="ccccd-115">This lets you continue to run your older ASP.NET Web Pages applications, build new ASP.NET Web Pages applications, and run all of them on the same computer.</span></span>

<span data-ttu-id="ccccd-116">Zde jsou některé kroky, nezapomeňte při instalaci webových stránek pomocí služby WebMatrix:</span><span class="sxs-lookup"><span data-stu-id="ccccd-116">Here are some things to remember when you install the Web Pages with WebMatrix:</span></span>

- <span data-ttu-id="ccccd-117">Ve výchozím nastavení bude existující aplikace Web Pages spustit jako na nejnovější verzi ve vašem počítači.</span><span class="sxs-lookup"><span data-stu-id="ccccd-117">By default, existing Web Pages applications will run as the latest version on your computer.</span></span> <span data-ttu-id="ccccd-118">(Sestavení jsou nainstalované v globální mezipaměti sestavení (GAC) a automaticky se používají.)</span><span class="sxs-lookup"><span data-stu-id="ccccd-118">(The assemblies are installed in the global assembly cache (GAC) and are used automatically.)</span></span>
- <span data-ttu-id="ccccd-119">Pokud budete chtít spuštění webu v jiné verzi z webových stránek ASP.NET, weby k tomu můžete konfigurovat.</span><span class="sxs-lookup"><span data-stu-id="ccccd-119">If you want to run a site using a different version of ASP.NET Web Pages, you can configure the site to do that.</span></span> <span data-ttu-id="ccccd-120">Pokud váš web už nemá *web.config* souboru v kořenovém adresáři serveru, vytvořte novou a zkopírujte následující kód XML do něj, přepisování stávajícího obsahu.</span><span class="sxs-lookup"><span data-stu-id="ccccd-120">If your site doesn't already have a *web.config* file in the root of the site, create a new one and copy the following XML into it, overwriting the existing content.</span></span> <span data-ttu-id="ccccd-121">Pokud web již obsahuje *web.config* přidejte `<appSettings>` prvky jako následující ten, který má `<configuration>` oddílu.</span><span class="sxs-lookup"><span data-stu-id="ccccd-121">If the site already contains a *web.config* file, add an `<appSettings>` element like the following one to the `<configuration>` section.</span></span>

    [!code-xml[Main](running-v1-and-v2-sites-side-by-side/samples/sample1.xml)]
  <span data-ttu-id="ccccd-122">. – Pokud nezadáte verzi v *web.config* soubor lokality je nasazená jako na nejnovější verzi.</span><span class="sxs-lookup"><span data-stu-id="ccccd-122">\`- If you do not specify a version in the *web.config* file, a site is deployed as the latest version.</span></span> <span data-ttu-id="ccccd-123">(Sestavení se zkopírují do *bin* složky v nasazené lokality.)</span><span class="sxs-lookup"><span data-stu-id="ccccd-123">(The assemblies are copied to the *bin* folder in the deployed site.)</span></span>
- <span data-ttu-id="ccccd-124">Nové aplikace, které vytvoříte pomocí šablony webu v Web Matrix zahrnují budou sestavení verze webové stránky na webu *bin* složky.</span><span class="sxs-lookup"><span data-stu-id="ccccd-124">New applications that you create using the site templates in Web Matrix include the Web Pages version assemblies in the site's *bin* folder.</span></span>

<span data-ttu-id="ccccd-125">Obecně platí, můžete vždy určit, kterou verzi webových stránek pomocí vašeho webu pomocí NuGet nainstalovat odpovídající sestavení na web *bin* složky.</span><span class="sxs-lookup"><span data-stu-id="ccccd-125">In general, you can always control which version of Web Pages to use with your site by using NuGet to install the appropriate assemblies into the site's *bin* folder.</span></span> <span data-ttu-id="ccccd-126">Balíčky, najdete v tématu [NuGet.org](http://NuGet.org).</span><span class="sxs-lookup"><span data-stu-id="ccccd-126">To find packages, visit [NuGet.org](http://NuGet.org).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ccccd-127">Další prostředky</span><span class="sxs-lookup"><span data-stu-id="ccccd-127">Additional Resources</span></span>

[<span data-ttu-id="ccccd-128">Hlavní funkce webových stránek v ASP.NET 2</span><span class="sxs-lookup"><span data-stu-id="ccccd-128">The Top Features in ASP.NET Web Pages 2</span></span>](top-features-in-web-pages-2.md)

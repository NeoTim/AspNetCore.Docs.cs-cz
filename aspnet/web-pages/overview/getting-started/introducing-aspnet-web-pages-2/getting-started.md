---
uid: web-pages/overview/getting-started/introducing-aspnet-web-pages-2/getting-started
title: Začínáme | Dokumentace Microsoftu
author: Rick-Anderson
description: Služba WebMatrix už nedoporučuje jako integrované vývojové prostředí pro ASP.NET Web Pages. Pomocí sady Visual Studio nebo Visual Studio Code. Tyto doprovodné materiály...
ms.author: riande
ms.date: 05/28/2015
ms.assetid: a36d3bdf-ef1b-47a4-b932-3a0cf4cad716
msc.legacyurl: /web-pages/overview/getting-started/introducing-aspnet-web-pages-2/getting-started
msc.type: authoredcontent
ms.openlocfilehash: 79f76410e091b137e2792127b0a42524270761e8
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021492"
---
<a name="getting-started"></a><span data-ttu-id="5ecbb-105">Začínáme</span><span class="sxs-lookup"><span data-stu-id="5ecbb-105">Getting Started</span></span>
====================
<span data-ttu-id="5ecbb-106">podle [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="5ecbb-106">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

[!INCLUDE[](~/includes/rp.md)]

> > [!NOTE] 
> > 
> > <span data-ttu-id="5ecbb-107">Služba WebMatrix už nedoporučuje jako integrované vývojové prostředí pro ASP.NET Web Pages.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-107">WebMatrix is no longer recommended as an integrated development environment for ASP.NET Web Pages.</span></span> <span data-ttu-id="5ecbb-108">Použití [sady Visual Studio](../program-asp-net-web-pages-in-visual-studio.md) nebo [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="5ecbb-108">Use [Visual Studio](../program-asp-net-web-pages-in-visual-studio.md) or [Visual Studio Code](https://code.visualstudio.com/).</span></span>
> 
> 
> <span data-ttu-id="5ecbb-109">Tento návod a aplikace poskytuje přehled o webových stránkách ASP.NET (verze 2 nebo novější) a syntaxi Razor, jednoduché rozhraní pro vytváření dynamických webů.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-109">This guidance and application gives you an overview of ASP.NET Web Pages (version 2 or later) and Razor syntax, a lightweight framework for creating dynamic websites.</span></span> <span data-ttu-id="5ecbb-110">Zavádí také služby WebMatrix, nástroj pro vytváření stránek a webů.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-110">It also introduces WebMatrix, a tool for creating pages and sites.</span></span>
> 
> <span data-ttu-id="5ecbb-111">**Úroveň**: Nová rozhraní ASP.NET Web Pages.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-111">**Level**: New to ASP.NET Web Pages.</span></span>  
> <span data-ttu-id="5ecbb-112">**Dovednosti předpokládá, že**: HTML, základní šablony stylů CSS (CSS).</span><span class="sxs-lookup"><span data-stu-id="5ecbb-112">**Skills assumed**: HTML, basic cascading style sheets (CSS).</span></span>
> 
> <span data-ttu-id="5ecbb-113">Co se dozvíte v tomto prvním kurzu sady:</span><span class="sxs-lookup"><span data-stu-id="5ecbb-113">What you'll learn in the first tutorial of the set:</span></span>
> 
> - <span data-ttu-id="5ecbb-114">Jaké technologie ASP.NET Web Pages je a co to je pro.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-114">What ASP.NET Web Pages technology is and what it's for.</span></span>
> - <span data-ttu-id="5ecbb-115">Co je služba WebMatrix.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-115">What WebMatrix is.</span></span>
> - <span data-ttu-id="5ecbb-116">Jak nainstalovat vše, co došlo k chybě.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-116">How to install everything.</span></span>
> - <span data-ttu-id="5ecbb-117">Jak vytvořit web ve službě WebMatrix.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-117">How to create a website by using WebMatrix.</span></span>
>   
> 
> <span data-ttu-id="5ecbb-118">Popsané funkce a technologie:</span><span class="sxs-lookup"><span data-stu-id="5ecbb-118">Features/technologies discussed:</span></span>
> 
> - <span data-ttu-id="5ecbb-119">Instalace webové platformy Microsoft.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-119">Microsoft Web Platform Installer.</span></span>
> - <span data-ttu-id="5ecbb-120">Služba WebMatrix.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-120">WebMatrix.</span></span>
> - <span data-ttu-id="5ecbb-121">*.cshtml* stránky</span><span class="sxs-lookup"><span data-stu-id="5ecbb-121">*.cshtml* pages</span></span>
>   
> 
> <span data-ttu-id="5ecbb-122">Mike Pope původně vytvořil v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-122">Mike Pope originally wrote this tutorial.</span></span> <span data-ttu-id="5ecbb-123">Tom FitzMacken aktualizaci pro sadu Microsoft WebMatrix 3.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-123">Tom FitzMacken updated it for Microsoft WebMatrix 3.</span></span>
> 
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="5ecbb-124">V tomto kurzu použili verze softwaru</span><span class="sxs-lookup"><span data-stu-id="5ecbb-124">Software versions used in the tutorial</span></span>
> 
> 
> - <span data-ttu-id="5ecbb-125">Rozhraní ASP.NET Web Pages (Razor) 2</span><span class="sxs-lookup"><span data-stu-id="5ecbb-125">ASP.NET Web Pages (Razor) 2</span></span>
> - <span data-ttu-id="5ecbb-126">Služba WebMatrix 3</span><span class="sxs-lookup"><span data-stu-id="5ecbb-126">WebMatrix 3</span></span>


## <a name="what-should-you-know"></a><span data-ttu-id="5ecbb-127">Co můžete vědět?</span><span class="sxs-lookup"><span data-stu-id="5ecbb-127">What Should You Know?</span></span>

<span data-ttu-id="5ecbb-128">Předpokládáme, že už znáte:</span><span class="sxs-lookup"><span data-stu-id="5ecbb-128">We're assuming that you're familiar with:</span></span>

- <span data-ttu-id="5ecbb-129">**HTML**.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-129">**HTML**.</span></span> <span data-ttu-id="5ecbb-130">Vyžaduje se žádné podrobné znalosti.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-130">No in-depth expertise is required.</span></span> <span data-ttu-id="5ecbb-131">Nebudou vám vysvětlíme, HTML, ale nebudeme také používat cokoli složitého.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-131">We won't explain HTML, but we also don't use anything complex.</span></span> <span data-ttu-id="5ecbb-132">Poskytujeme odkazy na kurzy HTML, kde myslíme si, že jsou užitečná.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-132">We'll provide links to HTML tutorials where we think they're useful.</span></span>
- <span data-ttu-id="5ecbb-133">**Šablony stylů CSS (CSS)**.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-133">**Cascading style sheets (CSS)**.</span></span> <span data-ttu-id="5ecbb-134">Stejným způsobem jako s HTML.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-134">Same as with HTML.</span></span>
- <span data-ttu-id="5ecbb-135">**Databáze Basic nápady**.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-135">**Basic database ideas**.</span></span> <span data-ttu-id="5ecbb-136">Pokud jste použít tabulku pro data a seřadit a filtrovat data, ke kterým je úroveň znalosti obecně předpokládáme pro tuto sérii kurzů.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-136">If you've used a spreadsheet for data and sorted and filtered the data, that's the level of expertise we're generally assuming for this tutorial set.</span></span>

<span data-ttu-id="5ecbb-137">Také předpokládáme, že vás zajímá výukové základní programování.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-137">We're also assuming that you're interested in learning basic programming.</span></span> <span data-ttu-id="5ecbb-138">ASP.NET Web Pages použít programovací jazyk C#.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-138">ASP.NET Web Pages use a programming language called C#.</span></span> <span data-ttu-id="5ecbb-139">Nemusíte mít žádné na pozadí v programování, stačí zájem v něm.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-139">You don't have to have any background in programming, just an interest in it.</span></span> <span data-ttu-id="5ecbb-140">Pokud žádné JavaScript někdy jste napsali na webové stránce před, máte spoustu na pozadí.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-140">If you've ever written any JavaScript in a web page before, you've got plenty of background.</span></span>

<span data-ttu-id="5ecbb-141">Všimněte si, že pokud jste se seznámili s programováním, můžete se setkat zpočátku nastavený v tomto kurzu se pomalu pohybuje, zatímco přeneseme programátory zrychlit.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-141">Note that if you are familiar with programming, you might find that this tutorial set initially moves slowly while we bring new programmers up to speed.</span></span> <span data-ttu-id="5ecbb-142">Získáme po prvních několika výukových programech, ale bude méně základní programování vysvětlete a věci se přesunou na rychlejší klipu.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-142">As we get past the first few tutorials, though, there will be less basic programming to explain and things will move along at a faster clip.</span></span>

## <a name="what-do-you-need"></a><span data-ttu-id="5ecbb-143">Co je potřeba?</span><span class="sxs-lookup"><span data-stu-id="5ecbb-143">What Do You Need?</span></span>

<span data-ttu-id="5ecbb-144">Zde je, co budete potřebovat:</span><span class="sxs-lookup"><span data-stu-id="5ecbb-144">Here's what you'll need:</span></span>

- <span data-ttu-id="5ecbb-145">Počítač se systémem Windows 8, Windows 7, Windows Server 2008 nebo Windows Server 2012.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-145">A computer that is running Windows 8, Windows 7, Windows Server 2008, or Windows Server 2012.</span></span>
- <span data-ttu-id="5ecbb-146">Živé připojení k Internetu.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-146">A live internet connection.</span></span>
- <span data-ttu-id="5ecbb-147">Oprávnění správce (požadováno pro instalaci).</span><span class="sxs-lookup"><span data-stu-id="5ecbb-147">Administrator privileges (required for the installation process).</span></span>

## <a name="what-is-aspnet-web-pages"></a><span data-ttu-id="5ecbb-148">Novinky webových stránek ASP.NET</span><span class="sxs-lookup"><span data-stu-id="5ecbb-148">What Is ASP.NET Web Pages?</span></span>

<span data-ttu-id="5ecbb-149">ASP.NET Web Pages je architektura, která slouží k vytvoření dynamické webové stránky.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-149">ASP.NET Web Pages is a framework that you can use to create dynamic web pages.</span></span> <span data-ttu-id="5ecbb-150">Jednoduché webové stránky HTML je statická; jeho obsah je určeno pevné značka jazyka HTML, který je na stránce.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-150">A simple HTML web page is static; its content is determined by the fixed HTML markup that's in the page.</span></span> <span data-ttu-id="5ecbb-151">Dynamických stránek podobné těm, které vytvoříte s webovými stránkami ASP.NET umožňují vytvářet obsah stránky v reálném čase, pomocí kódu.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-151">Dynamic pages like those you create with ASP.NET Web Pages let you create the page content on the fly, by using code.</span></span>

<span data-ttu-id="5ecbb-152">Dynamických stránek jde dělat nejrůznější věci.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-152">Dynamic pages let you do all sorts of things.</span></span> <span data-ttu-id="5ecbb-153">Můžete požádat uživatele o vstup pomocí formuláře a změňte na stránce zobrazí a jak vypadá.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-153">You can ask a user for input by using a form and then change what the page displays or how it looks.</span></span> <span data-ttu-id="5ecbb-154">Můžete využít informace od uživatele, uložit do databáze a seznamu později.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-154">You can take information from a user, save it in a database, and then list it later.</span></span> <span data-ttu-id="5ecbb-155">Odesílat e-maily z vašeho webu.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-155">You can send email from your site.</span></span> <span data-ttu-id="5ecbb-156">Můžete pracovat s jinými službami na webu (například mapování služby) a vytvářet stránky, které se integrují informace z těchto zdrojů.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-156">You can interact with other services on the web (for example, a mapping service) and produce pages that integrate information from those sources.</span></span>

## <a name="what-is-webmatrix"></a><span data-ttu-id="5ecbb-157">Co je služba WebMatrix?</span><span class="sxs-lookup"><span data-stu-id="5ecbb-157">What is WebMatrix?</span></span>

<span data-ttu-id="5ecbb-158">WebMatrix je nástroj, který integruje editoru webové stránky, nástroje databáze, webový server pro testování stránky a funkce pro publikování webu na Internetu.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-158">WebMatrix is a tool that integrates a web page editor, a database utility, a web server for testing pages, and features for publishing your website to the Internet.</span></span> <span data-ttu-id="5ecbb-159">WebMatrix je bezplatný a je snadné k instalaci a snadno se používá.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-159">WebMatrix is free, and it's easy to install and easy to use.</span></span> <span data-ttu-id="5ecbb-160">(Funguje i pro stejně jednoduché stránky HTML, stejně jako pro jiné technologie, jako je PHP.)</span><span class="sxs-lookup"><span data-stu-id="5ecbb-160">(It also works for just plain HTML pages, as well as for other technologies like PHP.)</span></span>

<span data-ttu-id="5ecbb-161">Ve skutečnosti nepotřebujete *mají* k práci s webovými stránkami ASP.NET pomocí Webmatrixu.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-161">You don't actually *have* to use WebMatrix to work with ASP.NET Web Pages.</span></span> <span data-ttu-id="5ecbb-162">Můžete vytvářet stránky pomocí textového editoru, například a testování stránek s využitím webového serveru, máte přístup.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-162">You can create pages by using a text editor, for example, and test pages by using a web server that you have access to.</span></span> <span data-ttu-id="5ecbb-163">Ale služba WebMatrix usnadňuje všechny velmi, tak tyto kurzy budou používat služby WebMatrix v průběhu.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-163">However, WebMatrix makes it all very easy, so these tutorials will use WebMatrix throughout.</span></span>

## <a name="about-these-tutorials"></a><span data-ttu-id="5ecbb-164">Informace o těchto kurzů</span><span class="sxs-lookup"><span data-stu-id="5ecbb-164">About These Tutorials</span></span>

<span data-ttu-id="5ecbb-165">Této sérii kurzů je úvodem k tom, jak používat rozhraní ASP.NET Web Pages.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-165">This tutorial set is an introduction to how to use ASP.NET Web Pages.</span></span> <span data-ttu-id="5ecbb-166">Celkový počet v této úvodní kurz sadě jsou 9 kurzů.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-166">There are 9 tutorials total in this introductory tutorial set.</span></span> <span data-ttu-id="5ecbb-167">To je částí série kurzů sady, která vás od začátečníků po rozhraní ASP.NET Web Pages, k vytvoření skutečné, profesionální weby.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-167">It's part of a series of tutorial sets that takes you from ASP.NET Web Pages novice to creating real, professional-looking websites.</span></span>

<span data-ttu-id="5ecbb-168">Tento první kurz nastavit koncentráty zobrazuje základní informace o tom, jak pracovat s webovými stránkami ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-168">This first tutorial set concentrates on showing you the basics of how to work with ASP.NET Web Pages.</span></span> <span data-ttu-id="5ecbb-169">Jakmile budete hotovi, můžete pracovat s další kurz sady, které vyzvednutí kde ukončení této a webové stránky, která prozkoumat podrobněji.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-169">When you're done, you can work with additional tutorial sets that pick up where this one ends and that explore Web Pages in more depth.</span></span>

<span data-ttu-id="5ecbb-170">Záměrně přejdeme snadno na podrobnější vysvětlení.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-170">We deliberately go easy on the in-depth explanations.</span></span> <span data-ttu-id="5ecbb-171">A pokaždé, když něco ukazujeme, pro tuto sérii kurzů jsme vždy vyberte způsob, jakým myslíme, že je nejjednodušší pochopit.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-171">And whenever we show something, for this tutorial set we always choose the way that we think is easiest to understand.</span></span> <span data-ttu-id="5ecbb-172">Novější kurz sady přejít do větší hloubky a zobrazení efektivnější a flexibilnější přístupy (také další zábavný).</span><span class="sxs-lookup"><span data-stu-id="5ecbb-172">Later tutorial sets go into more depth and show you more efficient or more flexible approaches (also more fun).</span></span> <span data-ttu-id="5ecbb-173">Ale tyto kurzy vyžadují, abyste nejprve porozumět základům o.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-173">But those tutorials require you to understand the basics first.</span></span>

<span data-ttu-id="5ecbb-174">Této sérii kurzů, které jste začali zahrnuje tyto funkce z webových stránek ASP.NET:</span><span class="sxs-lookup"><span data-stu-id="5ecbb-174">The tutorial set you've just started covers these features of ASP.NET Web Pages:</span></span>

- <span data-ttu-id="5ecbb-175">Úvod a připravuje se vše nainstalované.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-175">Introduction and getting everything installed.</span></span> <span data-ttu-id="5ecbb-176">(To je v tomto kurzu, který právě čtete.)</span><span class="sxs-lookup"><span data-stu-id="5ecbb-176">(That's in the tutorial you're reading.)</span></span>
- <span data-ttu-id="5ecbb-177">Základy programování v rozhraní ASP.NET Web Pages.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-177">The basics of ASP.NET Web Pages programming.</span></span>
- <span data-ttu-id="5ecbb-178">Vytvoření databáze.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-178">Creating a database.</span></span>
- <span data-ttu-id="5ecbb-179">Vytvoření a zpracování formulář vstupu uživatele.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-179">Creating and processing a user input form.</span></span>
- <span data-ttu-id="5ecbb-180">Přidání, aktualizaci a odstraňování dat v databázi.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-180">Adding, updating, and deleting data in the database.</span></span>

## <a name="what-will-you-create"></a><span data-ttu-id="5ecbb-181">Co se vytvoří?</span><span class="sxs-lookup"><span data-stu-id="5ecbb-181">What Will You Create?</span></span>

<span data-ttu-id="5ecbb-182">V tomto kurzu nastavte a následné ty točí kolem webu, kde můžete zobrazit seznam filmy, které vám vyhovuje.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-182">This tutorial set and subsequent ones revolve around a website where you can list movies that you like.</span></span> <span data-ttu-id="5ecbb-183">Budete moct zadat filmy, upravovat a jejich seznam.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-183">You'll be able to enter movies, edit them, and list them.</span></span> <span data-ttu-id="5ecbb-184">Tady je několik stránek, které vytvoříte v této sérii kurzů.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-184">Here are a couple of the pages you'll create in this tutorial set.</span></span> <span data-ttu-id="5ecbb-185">První z nich ukazuje film zobrazení stránky, kterou vytvoříte:</span><span class="sxs-lookup"><span data-stu-id="5ecbb-185">The first one shows the movie listing page that you'll create:</span></span>

![Dokončil filmová aplikace zobrazuje seznam video](getting-started/_static/image1.png)

<span data-ttu-id="5ecbb-187">A tady je stránka, která umožňuje přidat nový film informace na váš web:</span><span class="sxs-lookup"><span data-stu-id="5ecbb-187">And here's the page that lets you add new movie information to your site:</span></span>

![Dokončení filmová aplikace stránkou přidat video](getting-started/_static/image2.png)

<span data-ttu-id="5ecbb-189">Další kurz sady sestavení v tomto nastavení a přidávají další funkce, jako je nahrávání obrázků, umožňuje uživatelům přihlášení, odesílání e-mailů a integraci s sociálních médií.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-189">Subsequent tutorial sets build on this set and add more functionality, like uploading pictures, letting people log in, sending email, and integrating with social media.</span></span>

## <a name="see-this-app-running-on-azure"></a><span data-ttu-id="5ecbb-190">Zobrazit tuto aplikaci běžící v Azure</span><span class="sxs-lookup"><span data-stu-id="5ecbb-190">See this App Running on Azure</span></span>

<span data-ttu-id="5ecbb-191">Chcete zobrazit dokončené web spuštěný jako živou webovou aplikaci?</span><span class="sxs-lookup"><span data-stu-id="5ecbb-191">Would you like to see the finished site running as a live web app?</span></span> <span data-ttu-id="5ecbb-192">Kompletní verze aplikace můžete nasadit ke svému účtu Azure jednoduše kliknutím na následující tlačítko.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-192">You can deploy a complete version of the app to your Azure account by simply clicking the following button.</span></span>

[![](http://azuredeploy.net/deploybutton.png)](https://azuredeploy.net/?WT.mc_id=deploy_azure_aspnet&repository=https://github.com/tfitzmac/WebPagesMovies)

<span data-ttu-id="5ecbb-193">Potřebujete účet Azure k nasazení tohoto řešení do Azure.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-193">You need an Azure account to deploy this solution to Azure.</span></span> <span data-ttu-id="5ecbb-194">Pokud ještě nemáte účet, máte následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="5ecbb-194">If you do not already have an account, you have the following options:</span></span>

- <span data-ttu-id="5ecbb-195">[Zdarma otevřít účet Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A443DD604) – budete dostávat kredity můžete použít k vyzkoušení placených služeb Azure a dokonce i po jejich použití až můžete účet ponechat a dál používat bezplatné služby Azure.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-195">[Open an Azure account for free](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A443DD604) - You get credits you can use to try out paid Azure services, and even after they're used up you can keep the account and use free Azure services.</span></span>
- <span data-ttu-id="5ecbb-196">[Aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A443DD604) – vaše předplatné MSDN vám dává kredity každý měsíc, můžete použít k placení za služby Azure.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-196">[Activate MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A443DD604) - Your MSDN subscription gives you credits every month that you can use for paid Azure services.</span></span>

## <a name="installing-everything"></a><span data-ttu-id="5ecbb-197">Všechno, co instalace</span><span class="sxs-lookup"><span data-stu-id="5ecbb-197">Installing Everything</span></span>

<span data-ttu-id="5ecbb-198">Všechno, co můžete nainstalovat pomocí instalačního programu webové platformy společnosti Microsoft.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-198">You can install everything by using the Web Platform Installer from Microsoft.</span></span> <span data-ttu-id="5ecbb-199">V důsledku toho nainstalovat Instalační program a potom ho použít k instalaci všechno ostatní.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-199">In effect, you install the installer, and then use it to install everything else.</span></span>

<span data-ttu-id="5ecbb-200">Pomocí webových stránek, je nutné mít minimálně Windows XP s aktualizací SP3 nainstalovaná, nebo Windows Server 2008 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-200">To use Web Pages, you have to be have at least Windows XP with SP3 installed, or Windows Server 2008 or later.</span></span>

<span data-ttu-id="5ecbb-201">Na [webové stránky](../../../index.md) webu technologie ASP.NET, klikněte na tlačítko **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-201">On the [Web Pages page](../../../index.md) of the ASP.NET website, click **Install**.</span></span>

![Zobrazení webu rozhraní ASP.NET Web &quot;instalace služby WebMatrix&quot; tlačítko](getting-started/_static/image3.png)

<span data-ttu-id="5ecbb-203">Zobrazí se výzva k přijetí licenční podmínky a prohlášení o ochraně před instalací služby WebMatrix.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-203">You are asked to accept the license terms and privacy statement before installing WebMatrix.</span></span>

![Přijměte termín k zahájení instalace](getting-started/_static/image4.png)

<span data-ttu-id="5ecbb-205">Klikněte na tlačítko **spustit** spusťte instalaci.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-205">Click **Run** to start the installation.</span></span> <span data-ttu-id="5ecbb-206">(Pokud budete chtít uložte instalační program, klikněte na tlačítko **Uložit** a poté spusťte instalační program ze složky, kam jste jej uložili.)</span><span class="sxs-lookup"><span data-stu-id="5ecbb-206">(If you want to save the installer, click **Save** and then run the installer from the folder where you saved it.)</span></span>

![](getting-started/_static/image5.png)

<span data-ttu-id="5ecbb-207">Instalace webové platformy se zobrazí, připravena k instalaci služby WebMatrix.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-207">The Web Platform Installer appears, ready to install WebMatrix.</span></span> <span data-ttu-id="5ecbb-208">Klikněte na tlačítko **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-208">Click **Install**.</span></span>

![](getting-started/_static/image6.png)

<span data-ttu-id="5ecbb-209">Proces instalace přijde na to, co bylo potřeba nainstalovat na počítače a zahájí proces.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-209">The installation process figures out what it has to install on your computer and starts the process.</span></span> <span data-ttu-id="5ecbb-210">V závislosti na tom, co přesně musí být nainstalovaná proces může trvat od několika okamžiků několik minut.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-210">Depending on what exactly has to be installed, the process can take anywhere from a few moments to several minutes.</span></span> <span data-ttu-id="5ecbb-211">Vyberte **souhlasím** k potvrzení licenčních podmínek.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-211">Select **I Accept** to accept the license terms.</span></span>

## <a name="hello-webmatrix"></a><span data-ttu-id="5ecbb-212">Dobrý den, služba WebMatrix</span><span class="sxs-lookup"><span data-stu-id="5ecbb-212">Hello, WebMatrix</span></span>

<span data-ttu-id="5ecbb-213">Po dokončení procesu instalace můžete spustit služby WebMatrix.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-213">When it's done, the installation process can launch WebMatrix automatically.</span></span> <span data-ttu-id="5ecbb-214">Pokud ne, ve Windows, od **Start** nabídky, spuštění **Microsoft WebMatrix**.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-214">If it doesn't, in Windows, from the **Start** menu, launch **Microsoft WebMatrix**.</span></span>

<span data-ttu-id="5ecbb-215">Při prvním spuštění služby WebMatrix, budete mít příležitost dobře se přihlaste se k Microsoft Azure se svým účtem Microsoft.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-215">When you launch WebMatrix for the first time, you are given a chance to sign in to Microsoft Azure with your Microsoft account.</span></span> <span data-ttu-id="5ecbb-216">Po přihlášení, zobrazí se 10 bezplatných webových aplikací prostřednictvím Azure.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-216">By signing in, you will receive 10 free web apps through Azure.</span></span> <span data-ttu-id="5ecbb-217">Tato bezplatná webová aplikace poskytují pohodlný způsob pro testování vašich aplikací.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-217">These free web apps provide a convenient way to test your apps.</span></span> <span data-ttu-id="5ecbb-218">Pokud ještě nemáte účet Azure, ale máte předplatné MSDN, můžete si [aktivovat výhody předplatného MSDN](https://www.windowsazure.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A443DD604).</span><span class="sxs-lookup"><span data-stu-id="5ecbb-218">If you don't already have an Azure account, but you do have an MSDN subscription, you can [activate your MSDN subscription benefits](https://www.windowsazure.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A443DD604).</span></span> <span data-ttu-id="5ecbb-219">V opačném případě můžete vytvořit bezplatného zkušebního účtu stačí pár minut.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-219">Otherwise, you can create a free trial account in just a couple of minutes.</span></span> <span data-ttu-id="5ecbb-220">Podrobnosti najdete v tématu [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/free/?WT.mc_id=A443DD604).</span><span class="sxs-lookup"><span data-stu-id="5ecbb-220">For details, see [Azure Free Trial](https://azure.microsoft.com/free/?WT.mc_id=A443DD604).</span></span>

<span data-ttu-id="5ecbb-221">Není potřeba Přihlaste se hned teď a pokračujte v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-221">You do not have to sign in right now to continue with this tutorial.</span></span> <span data-ttu-id="5ecbb-222">Pokud není přihlásíte teď, budete stále mít možnost se přihlásit později.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-222">If you do not sign in now, you will still have the option to sign in later.</span></span> <span data-ttu-id="5ecbb-223">Poslední [tématu](publishing.md) řady v tomto kurzu dozvíte, jak nasadit svůj web do Azure, proto budete muset přihlásit k dokončení tohoto tématu.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-223">The last [topic](publishing.md) in this tutorial series covers how to deploy your website to Azure; therefore, you would need to sign in to complete that topic.</span></span>

<span data-ttu-id="5ecbb-224">V tomto okamžiku buď Přihlaste se pomocí svého účtu Microsoft nebo vyberte **teď ne** v pravém dolním rohu.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-224">At this point, either sign in with your Microsoft account or select **Not Now** in the lower right corner.</span></span>

![Přihlásit se](getting-started/_static/image7.png)

<span data-ttu-id="5ecbb-226">Pokud chcete začít, bude vytvoření prázdného webu a přidat na stránku.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-226">To begin, you'll create a blank website and add a page.</span></span> <span data-ttu-id="5ecbb-227">V pozdějších kurzech v této sadě bude přehrávat s některou ze šablon integrované webu.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-227">In a later tutorial in this set you'll play with one of the built-in website templates.</span></span>

<span data-ttu-id="5ecbb-228">V okně start klikněte na tlačítko **nový**.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-228">In the start window, click **New**.</span></span>

![Služba WebMatrix úvodní obrazovky](getting-started/_static/image8.png)

<span data-ttu-id="5ecbb-230">Šablony jsou předem připravených souborů a stránky pro různé druhy webů.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-230">Templates are prebuilt files and pages for different types of websites.</span></span> <span data-ttu-id="5ecbb-231">Pokud chcete zobrazit všechny šablony, které jsou k dispozici ve výchozím nastavení, vyberte možnost galerie šablon.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-231">To see all of the templates that are available by default, select the Template Gallery option.</span></span>

![Vyberte šablonu Galerie](getting-started/_static/image9.png)

<span data-ttu-id="5ecbb-233">V **rychlý Start** okně **prázdný web** z **ASP.NET** skupinu a pojmenujte nový web "WebPagesMovies".</span><span class="sxs-lookup"><span data-stu-id="5ecbb-233">In the **Quick Start** window, select **Empty Site** from the **ASP.NET** group and name the new site "WebPagesMovies".</span></span>

![Rychlý Start pro službu WebMatrix okna máte zvolenou šablonu prázdný web](getting-started/_static/image10.png)

<span data-ttu-id="5ecbb-235">Klikněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-235">Click **Next**.</span></span>

<span data-ttu-id="5ecbb-236">Pokud jste se přihlásili ke svému účtu Microsoft, budete mít příležitost k vytvoření webu v Azure.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-236">If you have signed in to your Microsoft account, you will be given the opportunity to create the site on Azure.</span></span> <span data-ttu-id="5ecbb-237">Na základě názvu vašeho webu, výchozí název **WebPagesMovies.azurewebsites.net** navržený; však vykřičník označuje, že tento název není k dispozici na Windows Azure.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-237">Based on the name of your site, the default name of **WebPagesMovies.azurewebsites.net** is suggested; however, the exclamation point indicates that this name is not available on Windows Azure.</span></span> <span data-ttu-id="5ecbb-238">Pro jednoduchost, vyberte **přeskočit** obejít vytvoření webu v Azure hned teď.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-238">For simplicity, select **Skip** to bypass creating the web site on Azure right now.</span></span> <span data-ttu-id="5ecbb-239">Později v této sérii publikujeme webu do Azure.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-239">Later in this series, we will publish the site to Azure.</span></span>

![Vytvoření serveru azure](getting-started/_static/image11.png)

<span data-ttu-id="5ecbb-241">Služba WebMatrix se vytvoří a otevře web:</span><span class="sxs-lookup"><span data-stu-id="5ecbb-241">WebMatrix creates and opens the site:</span></span>

![Nový web WebPagesMovies otevřete v nástroji WebMatrix](getting-started/_static/image12.png)

<span data-ttu-id="5ecbb-243">V horní části je panel nástrojů Rychlý přístup a pás karet.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-243">At the top, there's a Quick Access Toolbar and a ribbon.</span></span> <span data-ttu-id="5ecbb-244">Vlevo dole, zobrazí selektor pracovního prostoru kde přepínat mezi úlohami (**lokality**, **soubory**, **databází**, **sestavy**).</span><span class="sxs-lookup"><span data-stu-id="5ecbb-244">At the bottom left, you see the workspace selector where you switch between tasks (**Site**, **Files**, **Databases**, **Reports**).</span></span> <span data-ttu-id="5ecbb-245">Na pravé straně je obsahu podokna editoru a pro sestavy.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-245">On the right is the content pane for the editor and for reports.</span></span> <span data-ttu-id="5ecbb-246">A v dolní části se zobrazí čas od času oznamovací zprávy.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-246">And across the bottom you'll occasionally see a notification bar for messages.</span></span>

<span data-ttu-id="5ecbb-247">Dozvíte další informace o službě WebMatrix a jeho funkcí při procházení těmito kurzy.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-247">You'll learn more about WebMatrix and its features as you go through these tutorials.</span></span>

## <a name="creating-a-web-page"></a><span data-ttu-id="5ecbb-248">Vytvoření webové stránky</span><span class="sxs-lookup"><span data-stu-id="5ecbb-248">Creating a Web Page</span></span>

<span data-ttu-id="5ecbb-249">Abyste se seznámili s nástrojem WebMatrix a webových stránek ASP.NET, vytvoříte jednoduchou stránku.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-249">To become familiar with WebMatrix and ASP.NET Web Pages, you'll create a simple page.</span></span>

<span data-ttu-id="5ecbb-250">V modulu pro výběr pracovního prostoru, vyberte **soubory** pracovního prostoru.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-250">In the workspace selector, select the **Files** workspace.</span></span> <span data-ttu-id="5ecbb-251">Tento pracovní prostor vám umožní pracovat se soubory a složkami.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-251">This workspace lets you work with files and folders.</span></span> <span data-ttu-id="5ecbb-252">V levém podokně zobrazí strukturu souborů vašeho webu.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-252">The left pane shows the file structure of your site.</span></span> <span data-ttu-id="5ecbb-253">Změny pásu karet zobrazit úkoly související se soubory.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-253">The ribbon changes to show file-related tasks.</span></span>

![Soubor pracovního prostoru v nástroji WebMatrix](getting-started/_static/image13.png)

<span data-ttu-id="5ecbb-255">Na pásu karet klikněte na šipku v rámci **nový** a potom klikněte na tlačítko **nový soubor**.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-255">In the ribbon, click the arrow under **New** and then click **New File**.</span></span>

![Použití &quot;nový&quot; v pásu karet a vytvořte nový soubor](getting-started/_static/image14.png)

<span data-ttu-id="5ecbb-257">Služba WebMatrix zobrazí seznam typů souborů.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-257">WebMatrix displays a list of file types.</span></span> <span data-ttu-id="5ecbb-258">Vyberte **CSHTML**a **název** zadejte "HelloWorld".</span><span class="sxs-lookup"><span data-stu-id="5ecbb-258">Select **CSHTML**, and in the **Name** box, type "HelloWorld".</span></span> <span data-ttu-id="5ecbb-259">Na stránce CSHTML je na stránce rozhraní ASP.NET Web Pages.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-259">A CSHTML page is an ASP.NET Web Pages page.</span></span>

![Vytváří se nová stránka CSHTML s názvem HelloWorld.cshtml](getting-started/_static/image15.png)

<span data-ttu-id="5ecbb-261">Klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-261">Click **OK**.</span></span>

<span data-ttu-id="5ecbb-262">Služba WebMatrix vytvoří stránky a otevře v editoru.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-262">WebMatrix creates the page and opens it in the editor.</span></span>

![Nová stránka HelloWorld v editoru WebMatrix](getting-started/_static/image16.png)

<span data-ttu-id="5ecbb-264">Jak vidíte, tato stránka obsahuje většinou běžné kódování HTML, s výjimkou bloku v horní části, který vypadá takto:</span><span class="sxs-lookup"><span data-stu-id="5ecbb-264">As you can see, the page contains mostly ordinary HTML markup, except for a block at the top that looks like this:</span></span>

[!code-cshtml[Main](getting-started/samples/sample1.cshtml)]

<span data-ttu-id="5ecbb-265">To pro přidání kódu, jak brzy zjistíte.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-265">That's for adding code, as you'll see shortly.</span></span>

<span data-ttu-id="5ecbb-266">Všimněte si, že různé části na stránce &mdash; názvy elementů, atributů a text a navíc bloku v horní části, jsou všechny na různé barvy.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-266">Notice that the different parts of the page &mdash; the element names, attributes, and text, plus the block at the top — are all in different colors.</span></span> <span data-ttu-id="5ecbb-267">Tento postup se nazývá *zvýraznění syntaxe*, a usnadňuje mít všechno Vymazat.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-267">This is called *syntax highlighting*, and it makes it easier to keep everything clear.</span></span> <span data-ttu-id="5ecbb-268">Je jednou z funkcí, které usnadňuje práci s webovými stránkami ve Webmatrixu.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-268">It's one of the features that makes it easy to work with web pages in WebMatrix.</span></span>

<span data-ttu-id="5ecbb-269">Přidat obsah `<head>` a `<body>` prvky jako v následujícím příkladu.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-269">Add content for the `<head>` and `<body>` elements like in the following example.</span></span> <span data-ttu-id="5ecbb-270">(Pokud chcete, můžete pouze zkopírujte následující blok a nahraďte tento kód celé stávající stránky.)</span><span class="sxs-lookup"><span data-stu-id="5ecbb-270">(If you want, you can just copy the following block and replace the entire existing page with this code.)</span></span>

[!code-cshtml[Main](getting-started/samples/sample2.cshtml)]

<span data-ttu-id="5ecbb-271">V panelu nástrojů Rychlý přístup nebo v **souboru** nabídky, klikněte na tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-271">In the Quick Access Toolbar or in the **File** menu, click **Save**.</span></span>

![Tlačítko Uložit ve službě WebMatrix panelu nástrojů Rychlý přístup](getting-started/_static/image17.png)

## <a name="testing-the-page"></a><span data-ttu-id="5ecbb-273">Testování stránky</span><span class="sxs-lookup"><span data-stu-id="5ecbb-273">Testing the Page</span></span>

<span data-ttu-id="5ecbb-274">V **soubory** pracovního prostoru, klikněte pravým tlačítkem myši *HelloWorld.cshtml* stránce a potom klikněte na **spustit v prohlížeči**.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-274">In the **Files** workspace, right-click the *HelloWorld.cshtml* page and then click **Launch in browser**.</span></span>

![Spuštění stránky pomocí tlačítka pro spuštění na pásu karet nástroje WebMatrix](getting-started/_static/image18.png)

<span data-ttu-id="5ecbb-276">Služba WebMatrix spustí integrovaný webový server (služba IIS Express), můžete použít k testování stránek ve vašem počítači.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-276">WebMatrix starts a built-in web server (IIS Express) that you can use to test pages on your computer.</span></span> <span data-ttu-id="5ecbb-277">(Bez služby IIS Express v nástroji WebMatrix, je třeba publikovat na webový server stránky někde předtím, než je možné otestovat.) Na stránce se zobrazí ve vašem výchozím prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-277">(Without IIS Express in WebMatrix, you'd have to publish your page to a web server somewhere before you could test it.) The page is displayed in your default browser.</span></span>

![&quot;Hello World&quot; stránku spuštění v prohlížeči](getting-started/_static/image19.png)

<span data-ttu-id="5ecbb-279">Všimněte si, že při testování stránku ve službě WebMatrix, adresu URL v prohlížeči je něco jako `http://localhost:33651/HelloWorld.cshtml.` název *localhost* odkazuje na místním serveru, což znamená, že na stránce obsluhuje webový server, který je ve vašem počítači.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-279">Notice that when you test a page in WebMatrix, the URL in the browser is something like `http://localhost:33651/HelloWorld.cshtml.` The name *localhost* refers to a local server, meaning that the page is served by a web server that's on your own computer.</span></span> <span data-ttu-id="5ecbb-280">Jak jsme uvedli, služba WebMatrix nabízí program webového serveru s názvem služby IIS Express, která se spouští při spuštění na stránce.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-280">As noted, WebMatrix includes a web server program named IIS Express that runs when you launch a page.</span></span>

<span data-ttu-id="5ecbb-281">Počet po *localhost* (například *localhost:33651*) odkazuje *číslo portu* ve vašem počítači.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-281">The number after *localhost* (for example, *localhost:33651*) refers to a *port number* on your computer.</span></span> <span data-ttu-id="5ecbb-282">Toto je počet "kanál" využívající službu IIS Express pro tento konkrétní web.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-282">This is the number of the "channel" that IIS Express uses for this particular website.</span></span> <span data-ttu-id="5ecbb-283">Číslo portu je z rozsahu 1024 až 65536 vybraného náhodně, když vytvoříte web, a se liší pro každý web, který vytvoříte.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-283">The port number is selected at random from the range 1024 through 65536 when you create your site, and it's different for every site that you create.</span></span> <span data-ttu-id="5ecbb-284">(Při testování svůj vlastní web, číslo portu téměř jistě budou jiné číslo než 33561.) Když použijete jiný port pro každý web, službu IIS Express můžete ponechat přímo které z vašich lokalit se mluví k.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-284">(When you test your own site, the port number will almost certainly be a different number than 33561.) By using a different port for each website, IIS Express can keep straight which of your sites it's talking to.</span></span>

<span data-ttu-id="5ecbb-285">Později při publikování webu do veřejné webového serveru, už se nezobrazují *localhost* v adrese URL.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-285">Later when you publish your site to a public web server, you no longer see *localhost* in the URL.</span></span> <span data-ttu-id="5ecbb-286">Od tohoto okamžiku zobrazí více klasickou adresu URL jako `http://myhostingsite/mywebsite/HelloWorld.cshtml` nebo je bez ohledu na stránce.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-286">At that point, you'll see a more typical URL like `http://myhostingsite/mywebsite/HelloWorld.cshtml` or whatever the page is.</span></span> <span data-ttu-id="5ecbb-287">Můžete se dozvíte víc o publikování lokality dále v této sérii kurzů.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-287">You'll learn more about publishing a site later in this tutorial series.</span></span>

## <a name="adding-some-server-side-code"></a><span data-ttu-id="5ecbb-288">Přidání kódu na straně serveru</span><span class="sxs-lookup"><span data-stu-id="5ecbb-288">Adding Some Server-Side Code</span></span>

<span data-ttu-id="5ecbb-289">Zavřete prohlížeč a přejděte zpět na stránku ve Webmatrixu.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-289">Close the browser and go back to the page in WebMatrix.</span></span>

<span data-ttu-id="5ecbb-290">Přidá řádek do bloku kódu tak, aby vypadal jako v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="5ecbb-290">Add a line to the code block so that it looks like the following code:</span></span>

[!code-cshtml[Main](getting-started/samples/sample3.cshtml)]

<span data-ttu-id="5ecbb-291">To je něco kódu Razor.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-291">This is a little bit of Razor code.</span></span> <span data-ttu-id="5ecbb-292">Je nejspíš jasné, že získá aktuální datum a čas a vloží tuto hodnotu do *proměnnou* s názvem `currentDateTime`.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-292">It's probably clear that it gets the current date and time and puts that value into a *variable* named `currentDateTime`.</span></span> <span data-ttu-id="5ecbb-293">Nemusíte se věnovat čtení informace o syntaxi Razor v dalším kurzu.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-293">You'll read more about Razor syntax in the next tutorial.</span></span>

<span data-ttu-id="5ecbb-294">V těle stránky až `<p>Hello World!</p>` prvku, přidejte následující:</span><span class="sxs-lookup"><span data-stu-id="5ecbb-294">In the body of the page, after the `<p>Hello World!</p>` element, add the following:</span></span>

[!code-html[Main](getting-started/samples/sample4.html)]

<span data-ttu-id="5ecbb-295">Tento kód získá hodnotu, kam si ukládáte do `currentDateTime` proměnné v horní části a vloží jej do kódu stránky.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-295">This code gets the value that you put into the `currentDateTime` variable at the top and inserts it into the markup of the page.</span></span> <span data-ttu-id="5ecbb-296">`@` Znak označuje rozhraní ASP.NET Web Pages kód na stránce.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-296">The `@` character marks the ASP.NET Web Pages code in the page.</span></span>

<span data-ttu-id="5ecbb-297">Spusťte znovu (WebMatrix uloží změny můžete před spuštěním na stránce).</span><span class="sxs-lookup"><span data-stu-id="5ecbb-297">Run the page again (WebMatrix saves the changes for you before it runs the page).</span></span> <span data-ttu-id="5ecbb-298">Tentokrát zobrazí datum a čas ve stránce.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-298">This time you see the date and time in the page.</span></span>

![&quot;Hello World&quot; spuštění v prohlížeči pomocí dynamicky generované času zobrazení stránky](getting-started/_static/image20.png)

<span data-ttu-id="5ecbb-300">Chvíli počkejte a pak aktualizujte stránku v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-300">Wait a few moments and then refresh the page in the browser.</span></span> <span data-ttu-id="5ecbb-301">Zobrazení data a času se aktualizuje.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-301">The date and time display is updated.</span></span>

<span data-ttu-id="5ecbb-302">V prohlížeči podívejte se na zdroje stránky.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-302">In the browser, look at the page source.</span></span> <span data-ttu-id="5ecbb-303">Vypadá podobně jako následující kód:</span><span class="sxs-lookup"><span data-stu-id="5ecbb-303">It looks like the following markup:</span></span>

[!code-html[Main](getting-started/samples/sample5.html)]

<span data-ttu-id="5ecbb-304">Všimněte si, že `@{ }` tam není bloku v horní části.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-304">Notice that the `@{ }` block at the top isn't there.</span></span> <span data-ttu-id="5ecbb-305">Všimněte si také, že zobrazuje displej datum a čas vytvoření skutečného řetězce znaků (`1/18/2012 2:49:50 PM` nebo jakékoli), nikoli `@currentDateTime` jako došlo v *.cshtml* stránky.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-305">Also notice that the date and time display shows an actual string of characters (`1/18/2012 2:49:50 PM` or whatever), not `@currentDateTime` like you had in the *.cshtml* page.</span></span> <span data-ttu-id="5ecbb-306">Co se stalo, zde je, že při spuštění stránky technologie ASP.NET zpracovány veškerý kód (velmi málo v tomto případě), která byla označena jako `@`.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-306">What happened here is that when you ran the page, ASP.NET processed all the code (very little in this case) that was marked with `@`.</span></span> <span data-ttu-id="5ecbb-307">Kód vytvoří výstup, a tento výstup byl vložen do stránky.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-307">The code produces output, and that output was inserted into the page.</span></span>

## <a name="this-is-what-aspnet-web-pages-are-about"></a><span data-ttu-id="5ecbb-308">Toto je ASP.NET Web Pages se týkají</span><span class="sxs-lookup"><span data-stu-id="5ecbb-308">This Is What ASP.NET Web Pages Are About</span></span>

<span data-ttu-id="5ecbb-309">Při čtení, rozhraní ASP.NET Web Pages vytváří dynamického webového obsahu, co už víte, zde je cílem.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-309">When you read that ASP.NET Web Pages produces dynamic web content, what you've seen here is the idea.</span></span> <span data-ttu-id="5ecbb-310">Stránka, kterou jste právě vytvořili obsahuje stejný kód HTML, který jste se seznámili před.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-310">The page you just created contains the same HTML markup that you've seen before.</span></span> <span data-ttu-id="5ecbb-311">Může také obsahovat kód, který může provádět všechny možné druhy úloh.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-311">It can also contain code that can perform all sorts of tasks.</span></span> <span data-ttu-id="5ecbb-312">V tomto příkladu stejně jednoduchá získat aktuální datum a čas.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-312">In this example, it did the trivial task of getting the current date and time.</span></span> <span data-ttu-id="5ecbb-313">Jak už jste viděli, můžete proložit HTML výstup na stránce kódu.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-313">As you saw, you can intersperse code with the HTML to produce output in the page.</span></span> <span data-ttu-id="5ecbb-314">Když uživatel požádá *.cshtml* stránku v prohlížeči, ASP.NET zpracovává stránky, i když je stále v rámci webového serveru.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-314">When someone requests a *.cshtml* page in the browser, ASP.NET processes the page while it's still in the hands of the web server.</span></span> <span data-ttu-id="5ecbb-315">ASP.NET vloží výstup kódu (pokud existuje) do stránky ve formátu HTML.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-315">ASP.NET inserts the output of the code (if any) into the page as HTML.</span></span> <span data-ttu-id="5ecbb-316">Po dokončení zpracování kódu, technologie ASP.NET odešle výslednou stránku v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-316">When the code processing is done, ASP.NET sends the resulting page to the browser.</span></span> <span data-ttu-id="5ecbb-317">Všechno, co se někdy získá prohlížeče je ve formátu HTML.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-317">All the browser ever gets is HTML.</span></span> <span data-ttu-id="5ecbb-318">Zde je diagram:</span><span class="sxs-lookup"><span data-stu-id="5ecbb-318">Here's a diagram:</span></span>

![Koncepční tok způsobu, jakým technologie ASP.NET generuje HTML dynamicky](getting-started/_static/image21.png)

<span data-ttu-id="5ecbb-320">Cílem je jednoduché, ale kód můžete provádět mnoho zajímavých úlohy a existuje mnoho způsobů zajímavé, ve kterých můžete dynamicky přidat obsah ve formátu HTML na stránce.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-320">The idea is simple, but there are many interesting tasks that the code can perform, and there are many interesting ways in which you can dynamically add HTML content to the page.</span></span> <span data-ttu-id="5ecbb-321">A ASP.NET *.cshtml* stránky, jako jsou jakékoli stránky HTML, může také obsahovat kód, který běží v prohlížeči (kód jazyka JavaScript a jQuery).</span><span class="sxs-lookup"><span data-stu-id="5ecbb-321">And ASP.NET *.cshtml* pages, like any HTML page, can also include code that runs in the browser itself (JavaScript and jQuery code).</span></span> <span data-ttu-id="5ecbb-322">Prozkoumáte všechny tyto věci v této sérii kurzů a dalších ty.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-322">You'll explore all of these things in this tutorial set and in subsequent ones.</span></span>

## <a name="coming-up-next"></a><span data-ttu-id="5ecbb-323">Chystá se další</span><span class="sxs-lookup"><span data-stu-id="5ecbb-323">Coming Up Next</span></span>

<span data-ttu-id="5ecbb-324">V dalším kurzu této série prozkoumejte trochu programování rozhraní ASP.NET Web Pages.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-324">In the next tutorial in this series, you explore ASP.NET Web Pages programming a little more.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5ecbb-325">Další prostředky</span><span class="sxs-lookup"><span data-stu-id="5ecbb-325">Additional Resources</span></span>

<span data-ttu-id="5ecbb-326">[Vytvoření zcela nové webové stránky ASP.NET](https://www.microsoft.com/web/post/create-an-aspnet-website-from-scratch).</span><span class="sxs-lookup"><span data-stu-id="5ecbb-326">[Create an ASP.NET website from scratch](https://www.microsoft.com/web/post/create-an-aspnet-website-from-scratch).</span></span> <span data-ttu-id="5ecbb-327">Toto je kurz, který této volbě jde jenom o pomocí služby WebMatrix (ne webových stránek ASP.NET).</span><span class="sxs-lookup"><span data-stu-id="5ecbb-327">This is a tutorial that's specifically about using WebMatrix (not ASP.NET Web Pages).</span></span> <span data-ttu-id="5ecbb-328">Přejde do trochu více podrobností o některé další funkce služby WebMatrix, který jsme nebudeme se zabývat v této sérii kurzů.</span><span class="sxs-lookup"><span data-stu-id="5ecbb-328">It goes into a little more detail about some of the additional features of WebMatrix that we won't cover in this tutorial set.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="5ecbb-329">Next</span><span class="sxs-lookup"><span data-stu-id="5ecbb-329">Next</span></span>](intro-to-web-pages-programming.md)

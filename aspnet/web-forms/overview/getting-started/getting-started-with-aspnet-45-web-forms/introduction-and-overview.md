---
uid: web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/introduction-and-overview
title: Začínáme s ASP.NET 4.7 webové formuláře a sady Visual Studio 2017 | Dokumentace Microsoftu
author: Erikre
description: Tato série podrobných kurzů se seznámíte se základy vytváření webových formulářů ASP.NET aplikace pomocí ASP.NET 4.7 a Microsoft Visual Studio
ms.author: riande
ms.date: 01/09/2019
ms.assetid: 9b96eaa1-8ef0-4338-a2e8-e0f970bfaf68
msc.legacyurl: /web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/introduction-and-overview
msc.type: authoredcontent
ms.openlocfilehash: fb41ce72e9454d8d670a0b95234d2bc3f909f0ee
ms.sourcegitcommit: 42a8164b8aba21f322ffefacb92301bdfb4d3c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54341548"
---
<a name="getting-started-with-aspnet-45-web-forms-and-visual-studio-2017"></a><span data-ttu-id="f90d8-103">Začínáme s webovými formuláři ASP.NET 4.5 a Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="f90d8-103">Getting Started with ASP.NET 4.5 Web Forms and Visual Studio 2017</span></span>
====================

<span data-ttu-id="f90d8-104">[Stáhněte si ukázkový projekt Wingtip Toys (C#)](http://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) nebo [stáhnout elektronickou knihu (PDF)](http://download.microsoft.com/download/0/F/B/0FBFAA46-2BFD-478F-8E56-7BF3C672DF9D/Getting%20Started%20with%20ASP.NET%204.5%20Web%20Forms%20and%20Visual%20Studio%202013.pdf)</span><span class="sxs-lookup"><span data-stu-id="f90d8-104">[Download Wingtip Toys Sample Project (C#)](http://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) or [Download E-book (PDF)](http://download.microsoft.com/download/0/F/B/0FBFAA46-2BFD-478F-8E56-7BF3C672DF9D/Getting%20Started%20with%20ASP.NET%204.5%20Web%20Forms%20and%20Visual%20Studio%202013.pdf)</span></span>

<span data-ttu-id="f90d8-105">V této sérii kurzů se dozvíte, jak vytvářet aplikace webových formulářů ASP.NET s ASP.NET 4.5 a Microsoft Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="f90d8-105">This tutorial series shows you how to build an ASP.NET Web Forms application with ASP.NET 4.5 and Microsoft Visual Studio 2017.</span></span> 

## <a name="introduction"></a><span data-ttu-id="f90d8-106">Úvod</span><span class="sxs-lookup"><span data-stu-id="f90d8-106">Introduction</span></span>

<span data-ttu-id="f90d8-107">V této sérii kurzů vás provede procesem vytvoření aplikace webových formulářů ASP.NET pomocí sady Visual Studio 2017 a technologii ASP.NET 4.5.</span><span class="sxs-lookup"><span data-stu-id="f90d8-107">This tutorial series guides you through creating an ASP.NET Web Forms application using Visual Studio 2017 and ASP.NET 4.5.</span></span> <span data-ttu-id="f90d8-108">Vytvoříte aplikaci s názvem **Wingtip Toys** – zjednodušené webu prezentace prodej zboží online.</span><span class="sxs-lookup"><span data-stu-id="f90d8-108">You'll create an application named **Wingtip Toys** - a simplified storefront web site selling items online.</span></span> <span data-ttu-id="f90d8-109">Během řady jsou zvýrazněny nových funkcí technologie ASP.NET 4.5.</span><span class="sxs-lookup"><span data-stu-id="f90d8-109">During the series, new ASP.NET 4.5 features are highlighted.</span></span>

### <a name="target-audience"></a><span data-ttu-id="f90d8-110">Cílová skupina</span><span class="sxs-lookup"><span data-stu-id="f90d8-110">Target audience</span></span>

<span data-ttu-id="f90d8-111">Vývojáři nové webové formuláře ASP.NET jsou cílovou skupinou pro tuto řadu kurzů.</span><span class="sxs-lookup"><span data-stu-id="f90d8-111">Developers new to ASP.NET Web Forms are the target audience for this tutorial series.</span></span>

<span data-ttu-id="f90d8-112">Měli byste určitá znalost v následujících oblastech:</span><span class="sxs-lookup"><span data-stu-id="f90d8-112">You should have some knowledge in the following areas:</span></span>

- <span data-ttu-id="f90d8-113">Objektově orientované programování (OOP) a jazyky</span><span class="sxs-lookup"><span data-stu-id="f90d8-113">Object-oriented programming (OOP) and languages</span></span>
- <span data-ttu-id="f90d8-114">Vývoj pro web (HTML, CSS a JavaScript)</span><span class="sxs-lookup"><span data-stu-id="f90d8-114">Web development (HTML, CSS, JavaScript)</span></span>
- <span data-ttu-id="f90d8-115">Relační databáze</span><span class="sxs-lookup"><span data-stu-id="f90d8-115">Relational databases</span></span>
- <span data-ttu-id="f90d8-116">N-vrstvou architekturu</span><span class="sxs-lookup"><span data-stu-id="f90d8-116">N-tier architecture</span></span>

<span data-ttu-id="f90d8-117">Pokud chcete zkontrolovat tyto oblasti, vezměte v úvahu zkoumání následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="f90d8-117">To review these areas, consider studying the following content:</span></span>

- [<span data-ttu-id="f90d8-118">Začínáme s Visual C#</span><span class="sxs-lookup"><span data-stu-id="f90d8-118">Getting Started with Visual C#</span></span>](https://msdn.microsoft.com/library/a72418yk.aspx)
- <span data-ttu-id="f90d8-119">[Web Development](https://msdn.microsoft.com/beginner/bb308760.aspx), [HTML, CSS, JavaScript, SQL, PHP, JQuery](http://w3schools.com/)</span><span class="sxs-lookup"><span data-stu-id="f90d8-119">[Web Development](https://msdn.microsoft.com/beginner/bb308760.aspx), [HTML, CSS, JavaScript, SQL, PHP, JQuery](http://w3schools.com/)</span></span>
- [<span data-ttu-id="f90d8-120">Relační databáze</span><span class="sxs-lookup"><span data-stu-id="f90d8-120">Relational database</span></span>](http://en.wikipedia.org/wiki/Relational_database)
- [<span data-ttu-id="f90d8-121">Vícevrstvé architektury</span><span class="sxs-lookup"><span data-stu-id="f90d8-121">Multitier architecture</span></span>](http://en.wikipedia.org/wiki/Multitier_architecture)

### <a name="application-features"></a><span data-ttu-id="f90d8-122">Funkce aplikací</span><span class="sxs-lookup"><span data-stu-id="f90d8-122">Application features</span></span>

<span data-ttu-id="f90d8-123">Webový formulář ASP.NET funkce uvedené v této sérii:</span><span class="sxs-lookup"><span data-stu-id="f90d8-123">The ASP.NET Web Form features presented in this series include:</span></span>

- <span data-ttu-id="f90d8-124">Projekt webové aplikace (ne webový projekt)</span><span class="sxs-lookup"><span data-stu-id="f90d8-124">The Web Application Project (not Web Site Project)</span></span>
- <span data-ttu-id="f90d8-125">webové formuláře</span><span class="sxs-lookup"><span data-stu-id="f90d8-125">Web Forms</span></span>
- <span data-ttu-id="f90d8-126">Stránky předlohy, konfigurace</span><span class="sxs-lookup"><span data-stu-id="f90d8-126">Master Pages, Configuration</span></span>
- <span data-ttu-id="f90d8-127">Bootstrap</span><span class="sxs-lookup"><span data-stu-id="f90d8-127">Bootstrap</span></span>
- <span data-ttu-id="f90d8-128">Entity Framework Code nejprve LocalDB</span><span class="sxs-lookup"><span data-stu-id="f90d8-128">Entity Framework Code First, LocalDB</span></span>
- <span data-ttu-id="f90d8-129">Žádost o ověření</span><span class="sxs-lookup"><span data-stu-id="f90d8-129">Request Validation</span></span>
- <span data-ttu-id="f90d8-130">Ovládací prvky dat silného typu</span><span class="sxs-lookup"><span data-stu-id="f90d8-130">Strongly-typed Data Controls</span></span>
- <span data-ttu-id="f90d8-131">Vazby modelu</span><span class="sxs-lookup"><span data-stu-id="f90d8-131">Model Binding</span></span>
- <span data-ttu-id="f90d8-132">Datové poznámky</span><span class="sxs-lookup"><span data-stu-id="f90d8-132">Data Annotations</span></span>
- <span data-ttu-id="f90d8-133">Zprostředkovatele hodnot</span><span class="sxs-lookup"><span data-stu-id="f90d8-133">Value Providers</span></span>
- <span data-ttu-id="f90d8-134">Protokol SSL a protokolem OAuth</span><span class="sxs-lookup"><span data-stu-id="f90d8-134">SSL and OAuth</span></span>
- <span data-ttu-id="f90d8-135">ASP.NET Identity, konfigurace a autorizace</span><span class="sxs-lookup"><span data-stu-id="f90d8-135">ASP.NET Identity, Configuration, and Authorization</span></span>
- <span data-ttu-id="f90d8-136">Nerušivý ověření</span><span class="sxs-lookup"><span data-stu-id="f90d8-136">Unobtrusive Validation</span></span>
- <span data-ttu-id="f90d8-137">Směrování</span><span class="sxs-lookup"><span data-stu-id="f90d8-137">Routing</span></span>
- <span data-ttu-id="f90d8-138">Zpracování chyb v ASP.NET</span><span class="sxs-lookup"><span data-stu-id="f90d8-138">ASP.NET Error Handling</span></span>

### <a name="application-scenarios-and-tasks"></a><span data-ttu-id="f90d8-139">Scénáře aplikací a úloh</span><span class="sxs-lookup"><span data-stu-id="f90d8-139">Application scenarios and tasks</span></span>

<span data-ttu-id="f90d8-140">Série kurzů úlohy patří:</span><span class="sxs-lookup"><span data-stu-id="f90d8-140">Tutorial series tasks include:</span></span>

- <span data-ttu-id="f90d8-141">Vytváření, revizí a spuštění nového projektu</span><span class="sxs-lookup"><span data-stu-id="f90d8-141">Creating, reviewing, and running a new project</span></span>
- <span data-ttu-id="f90d8-142">Vytvoření struktury databáze</span><span class="sxs-lookup"><span data-stu-id="f90d8-142">Creating a database structure</span></span>
- <span data-ttu-id="f90d8-143">Inicializace a synchronizace replik indexů databáze</span><span class="sxs-lookup"><span data-stu-id="f90d8-143">Initializing and seeding a database</span></span>
- <span data-ttu-id="f90d8-144">Přizpůsobení uživatelského rozhraní se styly, grafikou a na stránku předlohy</span><span class="sxs-lookup"><span data-stu-id="f90d8-144">Customizing the UI with styles, graphics, and a master page</span></span>
- <span data-ttu-id="f90d8-145">Přidání stránky a navigace</span><span class="sxs-lookup"><span data-stu-id="f90d8-145">Adding pages and navigation</span></span>
- <span data-ttu-id="f90d8-146">Zobrazení Podrobnosti o nabídce a data produktu.</span><span class="sxs-lookup"><span data-stu-id="f90d8-146">Displaying menu details and product data</span></span>
- <span data-ttu-id="f90d8-147">Vytvoření nákupního košíku</span><span class="sxs-lookup"><span data-stu-id="f90d8-147">Creating a shopping cart</span></span>
- <span data-ttu-id="f90d8-148">Podpora přidání SSL a protokolem OAuth</span><span class="sxs-lookup"><span data-stu-id="f90d8-148">Adding SSL and OAuth support</span></span>
- <span data-ttu-id="f90d8-149">Přidání způsob platby</span><span class="sxs-lookup"><span data-stu-id="f90d8-149">Adding a payment method</span></span>
- <span data-ttu-id="f90d8-150">Včetně role správce a uživatele k aplikaci</span><span class="sxs-lookup"><span data-stu-id="f90d8-150">Including an administrator role and a user to the application</span></span>
- <span data-ttu-id="f90d8-151">Omezení přístupu ke konkrétní stránky a složka</span><span class="sxs-lookup"><span data-stu-id="f90d8-151">Restricting access to specific pages and folder</span></span>
- <span data-ttu-id="f90d8-152">Po nahrání souboru do webové aplikace</span><span class="sxs-lookup"><span data-stu-id="f90d8-152">Uploading a file to the web application</span></span>
- <span data-ttu-id="f90d8-153">Implementace ověření vstupu</span><span class="sxs-lookup"><span data-stu-id="f90d8-153">Implementing input validation</span></span>
- <span data-ttu-id="f90d8-154">Registrace trasy pro webovou aplikaci</span><span class="sxs-lookup"><span data-stu-id="f90d8-154">Registering routes for the web application</span></span>
- <span data-ttu-id="f90d8-155">Implementace zpracování chyb a protokolování chyb</span><span class="sxs-lookup"><span data-stu-id="f90d8-155">Implementing error handling and error logging</span></span>

## <a name="overview"></a><span data-ttu-id="f90d8-156">Přehled</span><span class="sxs-lookup"><span data-stu-id="f90d8-156">Overview</span></span>

<span data-ttu-id="f90d8-157">V této sérii kurzů je určené pro někdo zkušenosti s koncepty programování, ale nové webových formulářů ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="f90d8-157">This tutorial series is intended for someone familiar with programming concepts, but new to ASP.NET Web Forms.</span></span> <span data-ttu-id="f90d8-158">Pokud jste již obeznámeni s webovými formuláři ASP.NET, tuto řadu stále můžete informace o nových funkcích technologie ASP.NET 4.5.</span><span class="sxs-lookup"><span data-stu-id="f90d8-158">If you're already familiar with ASP.NET Web Forms, this series can still help you learn about new ASP.NET 4.5 features.</span></span> <span data-ttu-id="f90d8-159">Čtenáři neznáte programování konceptů a webových formulářů ASP.NET, naleznete v části Další kurzy webových formulářů, které jsou součástí [Začínáme](../../../index.md) části na webu technologie ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="f90d8-159">For readers unfamiliar with programming concepts and ASP.NET Web Forms, see the additional Web Forms tutorials provided in the [Getting Started](../../../index.md) section on the ASP.NET Web site.</span></span>

<span data-ttu-id="f90d8-160">ASP.NET 4.5 uvedené v této sérii kurzů zahrnuje následující funkce:</span><span class="sxs-lookup"><span data-stu-id="f90d8-160">The ASP.NET 4.5 provided in this tutorial series includes the following features:</span></span>

- <span data-ttu-id="f90d8-161">Jednoduché uživatelské rozhraní pro vytváření projektů, které nabízí [podporu pro mnoho platforem ASP.NET](../../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#add) (webové formuláře, MVC a webového rozhraní API).</span><span class="sxs-lookup"><span data-stu-id="f90d8-161">A simple UI for creating projects that offers [support for many ASP.NET frameworks](../../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#add) (Web Forms, MVC, and Web API).</span></span>
- <span data-ttu-id="f90d8-162">[Bootstrap](../../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#bootstrap), rozložení, motivy a responzivní design framework.</span><span class="sxs-lookup"><span data-stu-id="f90d8-162">[Bootstrap](../../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#bootstrap), a layout, theming, and responsive design framework.</span></span>
- <span data-ttu-id="f90d8-163">[ASP.NET Identity](../../../../identity/index.md), nový systém členství technologie ASP.NET, který funguje stejně ve všech platforem ASP.NET a funguje s webhosting softwaru než služby IIS.</span><span class="sxs-lookup"><span data-stu-id="f90d8-163">[ASP.NET Identity](../../../../identity/index.md), a new ASP.NET membership system that works the same in all ASP.NET frameworks and works with web hosting software other than IIS.</span></span>
- [<span data-ttu-id="f90d8-164">Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="f90d8-164">Entity Framework 6</span></span>](https://msdn.microsoft.com/data/ef.aspx)

  <span data-ttu-id="f90d8-165">Aktualizace Entity Framework umožňuje:</span><span class="sxs-lookup"><span data-stu-id="f90d8-165">An update to the Entity Framework enabling you to:</span></span>
  - <span data-ttu-id="f90d8-166">Načítání a manipulaci s daty jako silně typované objekty</span><span class="sxs-lookup"><span data-stu-id="f90d8-166">Retrieve and manipulate data as strongly-typed objects</span></span>
  - <span data-ttu-id="f90d8-167">Asynchronní přístup k datům</span><span class="sxs-lookup"><span data-stu-id="f90d8-167">Access data asynchronously</span></span>
  - <span data-ttu-id="f90d8-168">Zpracování přechodné chyby připojení</span><span class="sxs-lookup"><span data-stu-id="f90d8-168">Handle transient connection faults</span></span>
  - <span data-ttu-id="f90d8-169">Příkazy SQL</span><span class="sxs-lookup"><span data-stu-id="f90d8-169">Log SQL statements</span></span>

<span data-ttu-id="f90d8-170">Úplný seznam funkcí technologie ASP.NET 4.5 naleznete v tématu [ASP.NET and Web Tools pro Visual Studio 2013 – poznámky k](../../../../visual-studio/overview/2013/release-notes.md).</span><span class="sxs-lookup"><span data-stu-id="f90d8-170">For a complete ASP.NET 4.5 feature list, see [ASP.NET and Web Tools for Visual Studio 2013 Release Notes](../../../../visual-studio/overview/2013/release-notes.md).</span></span>

### <a name="the-wingtip-toys-sample-application"></a><span data-ttu-id="f90d8-171">Ukázkové aplikace Wingtip Toys</span><span class="sxs-lookup"><span data-stu-id="f90d8-171">The Wingtip Toys sample application</span></span>

<span data-ttu-id="f90d8-172">Na následujících snímcích obrazovky jsou z aplikace webových formulářů ASP.NET, který vytvoříte v této sérii kurzů.</span><span class="sxs-lookup"><span data-stu-id="f90d8-172">The following screenshots are from the ASP.NET Web Forms application that you create in this tutorial series.</span></span> <span data-ttu-id="f90d8-173">Při spuštění aplikace v sadě Visual Studio se zobrazí následující webové domovské stránky.</span><span class="sxs-lookup"><span data-stu-id="f90d8-173">When you run the application in Visual Studio, the following web Home page appears.</span></span>

![Vzorkový – výchozí stránku](introduction-and-overview/_static/image1.png)

<span data-ttu-id="f90d8-175">Můžete zaregistrovat jako nový uživatel nebo Přihlaste se jako stávajícího uživatele.</span><span class="sxs-lookup"><span data-stu-id="f90d8-175">You can register as a new user, or sign in as an existing user.</span></span> <span data-ttu-id="f90d8-176">Horní navigační obsahuje odkazy na kategorie produktů a jejich produktů z databáze.</span><span class="sxs-lookup"><span data-stu-id="f90d8-176">The top navigation has links to product categories and their products from the database.</span></span>

<span data-ttu-id="f90d8-177">Pokud vyberete **produkty**, se zobrazí všechny produkty k dispozici.</span><span class="sxs-lookup"><span data-stu-id="f90d8-177">If you select **Products**, all available products are displayed.</span></span> 

![Vzorkový - produkty](introduction-and-overview/_static/image2.png)

<span data-ttu-id="f90d8-179">Pokud vyberete určitý produkt, zobrazí se podrobnosti o produktu.</span><span class="sxs-lookup"><span data-stu-id="f90d8-179">If you select a specific product, product details are displayed.</span></span>


![Vzorkový – podrobnosti o produktu](introduction-and-overview/_static/image3.png)

<span data-ttu-id="f90d8-181">Jako uživatel můžete registrovat a přihlaste se pomocí funkce výchozí šablony webových formulářů.</span><span class="sxs-lookup"><span data-stu-id="f90d8-181">As a user, you can register and sign in with Web Forms template default functionality.</span></span> <span data-ttu-id="f90d8-182">V tomto kurzu vysvětluje, jak se přihlásit pomocí existujícího účtu Gmail.</span><span class="sxs-lookup"><span data-stu-id="f90d8-182">This tutorial also explains how to sign in using an existing Gmail account.</span></span> <span data-ttu-id="f90d8-183">Kromě toho můžete přihlásit jako správce přidávat a odebírat produkty z databáze.</span><span class="sxs-lookup"><span data-stu-id="f90d8-183">Additionally, you can sign in as the administrator to add and remove products from the database.</span></span>

![Přihlaste se na adresář Wingtip Toys-](introduction-and-overview/_static/image4.png)

<span data-ttu-id="f90d8-185">Jakmile jste přihlášení jako uživatel, můžete přidat produkty do nákupního košíku a ověření přes PayPal.</span><span class="sxs-lookup"><span data-stu-id="f90d8-185">Once you've signed in as a user, you can add products to the shopping cart and checkout with PayPal.</span></span> <span data-ttu-id="f90d8-186">Ukázková aplikace je navržen pro práci v sandboxu pro vývojáře společnosti PayPal.</span><span class="sxs-lookup"><span data-stu-id="f90d8-186">The sample application is designed to work in PayPal's developer sandbox.</span></span> <span data-ttu-id="f90d8-187">Žádná skutečná peněžní transakce probíhá.</span><span class="sxs-lookup"><span data-stu-id="f90d8-187">No actual money transaction takes place.</span></span>

![Vzorkový – nákupní košík](introduction-and-overview/_static/image5.png)

<span data-ttu-id="f90d8-189">PayPal potvrdí účtu, pořadí a platební informace.</span><span class="sxs-lookup"><span data-stu-id="f90d8-189">PayPal confirms your account, order, and payment information.</span></span>

![Vzorkový - PayPal](introduction-and-overview/_static/image6.png)

<span data-ttu-id="f90d8-191">Po návratu z PayPal, můžete zkontrolovat a dokončete vaši objednávku.</span><span class="sxs-lookup"><span data-stu-id="f90d8-191">After returning from PayPal, you can review and complete your order.</span></span>

![Vzorkový – pořadí revize](introduction-and-overview/_static/image7.png)

## <a name="prerequisites"></a><span data-ttu-id="f90d8-193">Požadavky</span><span class="sxs-lookup"><span data-stu-id="f90d8-193">Prerequisites</span></span>

<span data-ttu-id="f90d8-194">Než začnete, ujistěte se, že je v počítači nainstalován následující software:</span><span class="sxs-lookup"><span data-stu-id="f90d8-194">Before you start, make sure the following software is installed on your computer:</span></span>

- <span data-ttu-id="f90d8-195">[Microsoft Visual Studio 2017 nebo Microsoft Visual Studio Community 2017](https://visualstudio.microsoft.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="f90d8-195">[Microsoft Visual Studio 2017 or Microsoft Visual Studio Community 2017](https://visualstudio.microsoft.com/downloads/).</span></span>

<span data-ttu-id="f90d8-196">Rozhraní .NET Framework se instaluje automaticky.</span><span class="sxs-lookup"><span data-stu-id="f90d8-196">The .NET Framework is installed automatically.</span></span>

<span data-ttu-id="f90d8-197">V této sérii kurzů používá Microsoft Visual Studio Community 2017.</span><span class="sxs-lookup"><span data-stu-id="f90d8-197">This tutorial series uses Microsoft Visual Studio Community 2017.</span></span> <span data-ttu-id="f90d8-198">Můžete použít buď nebo Microsoft Visual Studio 2017 k dokončení této sérii kurzů.</span><span class="sxs-lookup"><span data-stu-id="f90d8-198">You can use either that or Microsoft Visual Studio 2017 to complete this tutorial series.</span></span>

<span data-ttu-id="f90d8-199">Mějte na paměti následující skutečnosti související sady Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="f90d8-199">Note the following about Visual Studio:</span></span>

* <span data-ttu-id="f90d8-200">Microsoft Visual Studio 2017 a Microsoft Visual Studio Community 2017 jsou označovány jako *sady Visual Studio* v celé této sérii kurzů.</span><span class="sxs-lookup"><span data-stu-id="f90d8-200">Microsoft Visual Studio 2017 and Microsoft Visual Studio Community 2017 are referred to as *Visual Studio* throughout this tutorial series.</span></span>

* <span data-ttu-id="f90d8-201">Visual Studio 2017 se nainstaluje vedle všechny starší verze už nainstalovaná.</span><span class="sxs-lookup"><span data-stu-id="f90d8-201">Visual Studio 2017 is installed next to any older versions already installed.</span></span> <span data-ttu-id="f90d8-202">Servery, které jsou vytvořeny v dřívějších verzích lze otevřít v sadě Visual Studio 2017 a pokračujte otevřením v předchozích verzích.</span><span class="sxs-lookup"><span data-stu-id="f90d8-202">Sites created in earlier versions can be opened in Visual Studio 2017 and continue to open in previous versions.</span></span>

* <span data-ttu-id="f90d8-203">Při prvním spuštění sady Visual Studio, se předpokládá, že jste vybrali *vývoj pro Web* nastavení.</span><span class="sxs-lookup"><span data-stu-id="f90d8-203">The first time you started Visual Studio, it is assumed you selected the *Web Development* settings.</span></span> <span data-ttu-id="f90d8-204">Další informace najdete v tématu [jak: Vyberte nastavení prostředí vývoje webu](https://msdn.microsoft.com/library/ff521558.aspx).</span><span class="sxs-lookup"><span data-stu-id="f90d8-204">For more information, see [How to: Select Web Development Environment Settings](https://msdn.microsoft.com/library/ff521558.aspx).</span></span>

<span data-ttu-id="f90d8-205">Po instalaci požadavků, jste připraveni začít vytvářet webový projekt v této sérii kurzů.</span><span class="sxs-lookup"><span data-stu-id="f90d8-205">After installing the prerequisites, you're ready to begin creating the Web project presented in this tutorial series.</span></span>

## <a name="download-the-sample-application"></a><span data-ttu-id="f90d8-206">Stažení ukázkové aplikace</span><span class="sxs-lookup"><span data-stu-id="f90d8-206">Download the sample application</span></span>

 <span data-ttu-id="f90d8-207">Úplnou vzorovou applicatiion v si můžete stáhnout kdykoli z webu MSDN ukázky:</span><span class="sxs-lookup"><span data-stu-id="f90d8-207">You can download the completed sample applicatiion at anytime from the MSDN Samples site:</span></span>

<span data-ttu-id="f90d8-208">[Začínáme s webovými formuláři ASP.NET 4.5 a Visual Studio 2013 – na adresář Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (C#)</span><span class="sxs-lookup"><span data-stu-id="f90d8-208">[Getting Started with ASP.NET 4.5 Web Forms and Visual Studio 2013 - Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (C#)</span></span> 

 <span data-ttu-id="f90d8-209">Tento soubor má následující položky:</span><span class="sxs-lookup"><span data-stu-id="f90d8-209">This download has the following items:</span></span>

- <span data-ttu-id="f90d8-210">Ukázková aplikace v *Northwind* složky.</span><span class="sxs-lookup"><span data-stu-id="f90d8-210">The sample application in the *WingtipToys* folder.</span></span>
- <span data-ttu-id="f90d8-211">Prostředky použité k vytvoření ukázkové aplikace v *Northwind prostředky* složky *Northwind* složky.</span><span class="sxs-lookup"><span data-stu-id="f90d8-211">The resources used to create the sample application in the *WingtipToys-Assets* folder in the *WingtipToys* folder.</span></span>

<span data-ttu-id="f90d8-212">Stažení *ZIP* souboru.</span><span class="sxs-lookup"><span data-stu-id="f90d8-212">The download is a *.zip* file.</span></span> <span data-ttu-id="f90d8-213">Pokud chcete zobrazit dokončené projekt, který vytvoří v této sérii kurzů, vyhledejte a vyberte *C#* složky v souboru ZIP.</span><span class="sxs-lookup"><span data-stu-id="f90d8-213">To see the completed project that this tutorial series creates, find and select the *C#* folder in the .zip file.</span></span> <span data-ttu-id="f90d8-214">Uložit C# složku ke složce můžete použít pro práci s projekty aplikace Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f90d8-214">Save the C# folder to the folder you use to work with Visual Studio projects.</span></span> <span data-ttu-id="f90d8-215">Ve výchozím nastavení je složka projektů sady Visual Studio 2017:</span><span class="sxs-lookup"><span data-stu-id="f90d8-215">By default, the Visual Studio 2017 projects folder is:</span></span>

<span data-ttu-id="f90d8-216"><strong>C:\Users&#92;</strong><strong><em>&lt;uživatelské jméno&gt;</em></strong><strong>\source\repos</strong></span><span class="sxs-lookup"><span data-stu-id="f90d8-216"><strong>C:\Users&#92;</strong><strong><em>&lt;username&gt;</em></strong><strong>\source\repos</strong></span></span>

<span data-ttu-id="f90d8-217">Přejmenovat ***jazyka C#*** složku ***Northwind***.</span><span class="sxs-lookup"><span data-stu-id="f90d8-217">Rename the ***C#*** folder to ***WingtipToys***.</span></span>

> [!NOTE]
> <span data-ttu-id="f90d8-218">Pokud už máte složku s názvem *Northwind* ve vaší složce projekty dočasně Přejmenujte tuto složku před přejmenováním *jazyka C#* složku *Northwind*.</span><span class="sxs-lookup"><span data-stu-id="f90d8-218">If you already have a folder named *WingtipToys* in your Projects folder, temporarily rename that existing folder before renaming the *C#* folder to *WingtipToys*.</span></span>

<span data-ttu-id="f90d8-219">Chcete-li spustit dokončený projekt, otevřete *Northwind* složky a dvojím kliknutím *WingtipToys.sln* souboru.</span><span class="sxs-lookup"><span data-stu-id="f90d8-219">To run the completed project, open the *WingtipToys* folder and double-click the *WingtipToys.sln* file.</span></span> <span data-ttu-id="f90d8-220">Visual Studio 2017 otevřete projekt.</span><span class="sxs-lookup"><span data-stu-id="f90d8-220">Visual Studio 2017 opens the project.</span></span> <span data-ttu-id="f90d8-221">Pak klikněte pravým tlačítkem *Default.aspx* ve **Průzkumníka řešení** a vyberte **zobrazit v prohlížeči**.</span><span class="sxs-lookup"><span data-stu-id="f90d8-221">Next, right-click the *Default.aspx* file in **Solution Explorer** and select **View In Browser**.</span></span>

## <a name="take-a-aspnet-web-forms-quiz-to-review-content"></a><span data-ttu-id="f90d8-222">Kontrola obsahu kvízu webových formulářů ASP.NET</span><span class="sxs-lookup"><span data-stu-id="f90d8-222">Take a ASP.NET Web Forms quiz to review content</span></span>

<span data-ttu-id="f90d8-223">Po dokončení série kurzů, kvízu Otestujte svoje znalosti a posílit klíčových konceptů.</span><span class="sxs-lookup"><span data-stu-id="f90d8-223">After completing the tutorial series, take a quiz to test your knowledge and reinforce key concepts.</span></span> <span data-ttu-id="f90d8-224">Každý dotaz poskytuje vysvětlení a odkazy na další pokyny.</span><span class="sxs-lookup"><span data-stu-id="f90d8-224">Each question provides an explanation and links to additional guidance.</span></span>

 * [<span data-ttu-id="f90d8-225">Webové formuláře ASP.NET kvíz</span><span class="sxs-lookup"><span data-stu-id="f90d8-225">ASP.NET Web Forms Quiz</span></span>](https://blogs.msdn.microsoft.com/erikreitan/2016/01/08/asp-net-web-forms-quiz/) 

## <a name="tutorial-support-and-comments"></a><span data-ttu-id="f90d8-226">Kurz podpory a komentáře</span><span class="sxs-lookup"><span data-stu-id="f90d8-226">Tutorial support and comments</span></span>

<span data-ttu-id="f90d8-227">Pro otázky a připomínky, použijte oddíl Q a A součástí [Začínáme se službou webových formulářů ASP.NET 4.5 a Visual Studio 2013 – Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (C#) stránka s ukázkou.</span><span class="sxs-lookup"><span data-stu-id="f90d8-227">For questions and comments, use the Q and A section included on the [Getting Started with ASP.NET 4.5 Web Forms and Visual Studio 2013 - Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (C#) sample page.</span></span>

<span data-ttu-id="f90d8-228">Si mohou komentáře k této sérii kurzů.</span><span class="sxs-lookup"><span data-stu-id="f90d8-228">Comments on this tutorial series are welcome.</span></span> <span data-ttu-id="f90d8-229">Když se aktualizuje v této sérii kurzů, každý úsilí vezměte v úvahu opravy nebo návrhy na vylepšení.</span><span class="sxs-lookup"><span data-stu-id="f90d8-229">When this tutorial series is updated, every effort is made to consider corrections or suggestions for improvements.</span></span>

<span data-ttu-id="f90d8-230">Pokud dojde k chybě, může být matoucí, odpovídající chybové zprávy s dobrým vysvětlení o tom, jak ho opravit.</span><span class="sxs-lookup"><span data-stu-id="f90d8-230">If an error occurs, the corresponding error messages could be confusing, with no good explanation on how to fix it.</span></span> <span data-ttu-id="f90d8-231">Potřebujete pomoc, můžete zkontrolovat [fóra ASP.NET](https://forums.asp.net/).</span><span class="sxs-lookup"><span data-stu-id="f90d8-231">For help, you can check the [ASP.NET forums](https://forums.asp.net/).</span></span> <span data-ttu-id="f90d8-232">Jiné vhodným zdrojem je v části Q a A [Začínáme se službou webových formulářů ASP.NET 4.5 a Visual Studio 2013 – Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (C#) stránka s ukázkou.</span><span class="sxs-lookup"><span data-stu-id="f90d8-232">Another good source is the Q and A section in the [Getting Started with ASP.NET 4.5 Web Forms and Visual Studio 2013 - Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (C#) sample page.</span></span> 

> [!div class="step-by-step"]
> [<span data-ttu-id="f90d8-233">Next</span><span class="sxs-lookup"><span data-stu-id="f90d8-233">Next</span></span>](create-the-project.md)

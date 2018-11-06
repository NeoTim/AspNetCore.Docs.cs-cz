---
uid: web-api/overview/testing-and-debugging/unit-testing-with-aspnet-web-api
title: Testování rozhraní ASP.NET Web API 2 | Dokumentace Microsoftu
author: Rick-Anderson
description: Tento průvodce a aplikace ukazují, jak vytvořit jednoduchý částí pro vaši aplikaci s webovým rozhraním API 2. Tento kurz ukazuje, jak zahrnout proj test jednotek...
ms.author: riande
ms.date: 06/05/2014
ms.assetid: bf20f78d-ff91-48be-abd1-88e23dcc70ba
msc.legacyurl: /web-api/overview/testing-and-debugging/unit-testing-with-aspnet-web-api
msc.type: authoredcontent
ms.openlocfilehash: 915610e6646ebe86dd8f16f290ecabd36bf7f48d
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51020829"
---
<a name="unit-testing-aspnet-web-api-2"></a><span data-ttu-id="b70cd-104">Testování rozhraní ASP.NET Web API 2</span><span class="sxs-lookup"><span data-stu-id="b70cd-104">Unit Testing ASP.NET Web API 2</span></span>
====================
<span data-ttu-id="b70cd-105">podle [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="b70cd-105">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

[<span data-ttu-id="b70cd-106">Stáhnout dokončený projekt</span><span class="sxs-lookup"><span data-stu-id="b70cd-106">Download Completed Project</span></span>](https://code.msdn.microsoft.com/Unit-Testing-with-ASPNET-1374bc11)

> <span data-ttu-id="b70cd-107">Tento průvodce a aplikace ukazují, jak vytvořit jednoduchý částí pro vaši aplikaci s webovým rozhraním API 2.</span><span class="sxs-lookup"><span data-stu-id="b70cd-107">This guidance and application demonstrate how to create simple unit tests for your Web API 2 application.</span></span> <span data-ttu-id="b70cd-108">Tento kurz ukazuje, jak zahrnout projekt testu jednotek ve vašem řešení a zapisovat testovací metody, které zkontrolujte hodnoty vrácené z metody kontroleru.</span><span class="sxs-lookup"><span data-stu-id="b70cd-108">This tutorial shows how to include a unit test project in your solution, and write test methods that check the returned values from a controller method.</span></span>
>
> <span data-ttu-id="b70cd-109">Tento kurz předpokládá, že jste obeznámeni se základními koncepcemi rozhraní ASP.NET Web API.</span><span class="sxs-lookup"><span data-stu-id="b70cd-109">This tutorial assumes you are familiar with the basic concepts of ASP.NET Web API.</span></span> <span data-ttu-id="b70cd-110">Úvodní tutoriál naleznete v tématu [Začínáme s ASP.NET Web API 2](../getting-started-with-aspnet-web-api/tutorial-your-first-web-api.md).</span><span class="sxs-lookup"><span data-stu-id="b70cd-110">For an introductory tutorial, see [Getting Started with ASP.NET Web API 2](../getting-started-with-aspnet-web-api/tutorial-your-first-web-api.md).</span></span>
>
> <span data-ttu-id="b70cd-111">Testování částí v tomto tématu jsou záměrně omezené na jednoduché datové scénáře.</span><span class="sxs-lookup"><span data-stu-id="b70cd-111">The unit tests in this topic are intentionally limited to simple data scenarios.</span></span> <span data-ttu-id="b70cd-112">Testování pokročilejší scénáře datových částí, naleznete v tématu [vytvoření modelu Entity Framework při jednotky testování ASP.NET Web API 2](mocking-entity-framework-when-unit-testing-aspnet-web-api-2.md).</span><span class="sxs-lookup"><span data-stu-id="b70cd-112">For unit testing more advanced data scenarios, see [Mocking Entity Framework when Unit Testing ASP.NET Web API 2](mocking-entity-framework-when-unit-testing-aspnet-web-api-2.md).</span></span>
>
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="b70cd-113">V tomto kurzu použili verze softwaru</span><span class="sxs-lookup"><span data-stu-id="b70cd-113">Software versions used in the tutorial</span></span>
>
> - [<span data-ttu-id="b70cd-114">Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="b70cd-114">Visual Studio 2017</span></span>](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)
> - <span data-ttu-id="b70cd-115">Webové rozhraní API 2</span><span class="sxs-lookup"><span data-stu-id="b70cd-115">Web API 2</span></span>

## <a name="in-this-topic"></a><span data-ttu-id="b70cd-116">V tomto tématu</span><span class="sxs-lookup"><span data-stu-id="b70cd-116">In this topic</span></span>

<span data-ttu-id="b70cd-117">Toto téma obsahuje následující oddíly:</span><span class="sxs-lookup"><span data-stu-id="b70cd-117">This topic contains the following sections:</span></span>

- [<span data-ttu-id="b70cd-118">Požadované součásti</span><span class="sxs-lookup"><span data-stu-id="b70cd-118">Prerequisites</span></span>](#prereqs)
- [<span data-ttu-id="b70cd-119">Stáhnout kód</span><span class="sxs-lookup"><span data-stu-id="b70cd-119">Download code</span></span>](#download)
- [<span data-ttu-id="b70cd-120">Vytvoření aplikace pomocí projektu testů jednotek</span><span class="sxs-lookup"><span data-stu-id="b70cd-120">Create application with unit test project</span></span>](#appwithunittest)
    - [<span data-ttu-id="b70cd-121">Přidejte projekt testu jednotek při vytváření aplikace</span><span class="sxs-lookup"><span data-stu-id="b70cd-121">Add unit test project when creating the application</span></span>](#whencreate)
    - [<span data-ttu-id="b70cd-122">Přidat projekt testování částí do existující aplikace</span><span class="sxs-lookup"><span data-stu-id="b70cd-122">Add unit test project to an existing application</span></span>](#addtoexisting)
- [<span data-ttu-id="b70cd-123">Nastavení aplikace webového rozhraní API 2</span><span class="sxs-lookup"><span data-stu-id="b70cd-123">Set up the Web API 2 application</span></span>](#setupproject)
- [<span data-ttu-id="b70cd-124">Instalace balíčků NuGet v projektu testů</span><span class="sxs-lookup"><span data-stu-id="b70cd-124">Install NuGet packages in test project</span></span>](#testpackages)
- [<span data-ttu-id="b70cd-125">Vytváření testů</span><span class="sxs-lookup"><span data-stu-id="b70cd-125">Create tests</span></span>](#tests)
- [<span data-ttu-id="b70cd-126">Spuštění testů</span><span class="sxs-lookup"><span data-stu-id="b70cd-126">Run tests</span></span>](#runtests)

<a id="prereqs"></a>
## <a name="prerequisites"></a><span data-ttu-id="b70cd-127">Požadavky</span><span class="sxs-lookup"><span data-stu-id="b70cd-127">Prerequisites</span></span>

<span data-ttu-id="b70cd-128">[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017) Community, Professional nebo Enterprise edition</span><span class="sxs-lookup"><span data-stu-id="b70cd-128">[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017) Community, Professional or Enterprise edition</span></span>

<a id="download"></a>
## <a name="download-code"></a><span data-ttu-id="b70cd-129">Stáhnout kód</span><span class="sxs-lookup"><span data-stu-id="b70cd-129">Download code</span></span>

<span data-ttu-id="b70cd-130">Stáhněte si [dokončený projekt](https://code.msdn.microsoft.com/Unit-Testing-with-ASPNET-1374bc11).</span><span class="sxs-lookup"><span data-stu-id="b70cd-130">Download the [completed project](https://code.msdn.microsoft.com/Unit-Testing-with-ASPNET-1374bc11).</span></span> <span data-ttu-id="b70cd-131">Ke stažení projekt zahrnuje kód testu jednotek pro toto téma a [vytvoření modelu Entity Framework při jednotky testování webového rozhraní API ASP.NET](mocking-entity-framework-when-unit-testing-aspnet-web-api-2.md) tématu.</span><span class="sxs-lookup"><span data-stu-id="b70cd-131">The downloadable project includes unit test code for this topic and for the [Mocking Entity Framework when Unit Testing ASP.NET Web API](mocking-entity-framework-when-unit-testing-aspnet-web-api-2.md) topic.</span></span>

<a id="appwithunittest"></a>
## <a name="create-application-with-unit-test-project"></a><span data-ttu-id="b70cd-132">Vytvoření aplikace pomocí projektu testů jednotek</span><span class="sxs-lookup"><span data-stu-id="b70cd-132">Create application with unit test project</span></span>

<span data-ttu-id="b70cd-133">Můžete buď při vytváření aplikace vytvořit projekt testování částí nebo přidat projekt testování částí do stávající aplikace.</span><span class="sxs-lookup"><span data-stu-id="b70cd-133">You can either create a unit test project when creating your application or add a unit test project to an existing application.</span></span> <span data-ttu-id="b70cd-134">Tento kurz ukazuje obě metody pro vytvoření projektu testů jednotek.</span><span class="sxs-lookup"><span data-stu-id="b70cd-134">This tutorial shows both methods for creating a unit test project.</span></span> <span data-ttu-id="b70cd-135">Chcete-li v tomto kurzu můžete použít kterýkoliv přístup.</span><span class="sxs-lookup"><span data-stu-id="b70cd-135">To follow this tutorial, you can use either approach.</span></span>

<a id="whencreate"></a>
### <a name="add-unit-test-project-when-creating-the-application"></a><span data-ttu-id="b70cd-136">Přidejte projekt testu jednotek při vytváření aplikace</span><span class="sxs-lookup"><span data-stu-id="b70cd-136">Add unit test project when creating the application</span></span>

<span data-ttu-id="b70cd-137">Vytvořit novou webovou aplikaci ASP.NET s názvem **StoreApp**.</span><span class="sxs-lookup"><span data-stu-id="b70cd-137">Create a new ASP.NET Web Application named **StoreApp**.</span></span>

![Vytvoření projektu](unit-testing-with-aspnet-web-api/_static/image1.png)

<span data-ttu-id="b70cd-139">V oknech Nový projekt ASP.NET, vyberte **prázdný** šablony a přidat složky a základní odkazy pro webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="b70cd-139">In the New ASP.NET Project windows, select the **Empty** template and add folders and core references for Web API.</span></span> <span data-ttu-id="b70cd-140">Vyberte **přidání jednotkových testů** možnost.</span><span class="sxs-lookup"><span data-stu-id="b70cd-140">Select the **Add unit tests** option.</span></span> <span data-ttu-id="b70cd-141">Automaticky s názvem projektu testování částí **StoreApp.Tests**.</span><span class="sxs-lookup"><span data-stu-id="b70cd-141">The unit test project is automatically named **StoreApp.Tests**.</span></span> <span data-ttu-id="b70cd-142">Abyste mohli tento název.</span><span class="sxs-lookup"><span data-stu-id="b70cd-142">You can keep this name.</span></span>

![Vytvořte projekt testu jednotek](unit-testing-with-aspnet-web-api/_static/image2.png)

<span data-ttu-id="b70cd-144">Po vytvoření aplikace, uvidíte, že obsahuje dva projekty.</span><span class="sxs-lookup"><span data-stu-id="b70cd-144">After creating the application, you will see it contains two projects.</span></span>

![dva projekty](unit-testing-with-aspnet-web-api/_static/image3.png)

<a id="addtoexisting"></a>
### <a name="add-unit-test-project-to-an-existing-application"></a><span data-ttu-id="b70cd-146">Přidat projekt testování částí do existující aplikace</span><span class="sxs-lookup"><span data-stu-id="b70cd-146">Add unit test project to an existing application</span></span>

<span data-ttu-id="b70cd-147">Pokud jste nevytvořili projekt testu jednotek při vytváření vaší aplikace, můžete přidat jednu kdykoli.</span><span class="sxs-lookup"><span data-stu-id="b70cd-147">If you did not create the unit test project when you created your application, you can add one at any time.</span></span> <span data-ttu-id="b70cd-148">Předpokládejme například, že již máte aplikaci s názvem StoreApp a chcete přidat testy jednotek.</span><span class="sxs-lookup"><span data-stu-id="b70cd-148">For example, suppose you already have an application named StoreApp, and you want to add unit tests.</span></span> <span data-ttu-id="b70cd-149">Chcete-li přidat projekt testování částí, klikněte pravým tlačítkem na řešení a vyberte **přidat** a **nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="b70cd-149">To add a unit test project, right-click your solution and select **Add** and **New Project**.</span></span>

![Přidat nový projekt do řešení](unit-testing-with-aspnet-web-api/_static/image4.png)

<span data-ttu-id="b70cd-151">Vyberte **testovací** v levém podokně a vyberte **projekt testu jednotek** pro typ projektu.</span><span class="sxs-lookup"><span data-stu-id="b70cd-151">Select **Test** in the left pane, and select **Unit Test Project** for the project type.</span></span> <span data-ttu-id="b70cd-152">Pojmenujte projekt **StoreApp.Tests**.</span><span class="sxs-lookup"><span data-stu-id="b70cd-152">Name the project **StoreApp.Tests**.</span></span>

![přidejte projekt testu jednotek](unit-testing-with-aspnet-web-api/_static/image5.png)

<span data-ttu-id="b70cd-154">Projekt testu jednotek se zobrazí ve vašem řešení.</span><span class="sxs-lookup"><span data-stu-id="b70cd-154">You will see the unit test project in your solution.</span></span>

<span data-ttu-id="b70cd-155">V projektu testování částí přidejte odkaz na projekt do původního projektu.</span><span class="sxs-lookup"><span data-stu-id="b70cd-155">In the unit test project, add a project reference to the original project.</span></span>

<a id="setupproject"></a>
## <a name="set-up-the-web-api-2-application"></a><span data-ttu-id="b70cd-156">Nastavení aplikace webového rozhraní API 2</span><span class="sxs-lookup"><span data-stu-id="b70cd-156">Set up the Web API 2 application</span></span>

<span data-ttu-id="b70cd-157">V projektu StoreApp, přidejte soubor třídy do **modely** složku s názvem **Product.cs**.</span><span class="sxs-lookup"><span data-stu-id="b70cd-157">In your StoreApp project, add a class file to the **Models** folder named **Product.cs**.</span></span> <span data-ttu-id="b70cd-158">Obsah souboru nahraďte následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="b70cd-158">Replace the contents of the file with the following code.</span></span>

[!code-csharp[Main](unit-testing-with-aspnet-web-api/samples/sample1.cs)]

<span data-ttu-id="b70cd-159">Sestavte řešení.</span><span class="sxs-lookup"><span data-stu-id="b70cd-159">Build the solution.</span></span>

<span data-ttu-id="b70cd-160">Klikněte pravým tlačítkem na složku řadiče a vyberte **přidat** a **novou vygenerovanou položku**.</span><span class="sxs-lookup"><span data-stu-id="b70cd-160">Right-click the Controllers folder and select **Add** and **New Scaffolded Item**.</span></span> <span data-ttu-id="b70cd-161">Vyberte **rozhraní Web API 2 kontroler – prázdný**.</span><span class="sxs-lookup"><span data-stu-id="b70cd-161">Select **Web API 2 Controller - Empty**.</span></span>

![Přidat nový kontroler](unit-testing-with-aspnet-web-api/_static/image6.png)

<span data-ttu-id="b70cd-163">Nastavte název kontroleru **SimpleProductController**a klikněte na tlačítko **přidat**.</span><span class="sxs-lookup"><span data-stu-id="b70cd-163">Set the controller name to **SimpleProductController**, and click **Add**.</span></span>

![Zadejte kontroler](unit-testing-with-aspnet-web-api/_static/image7.png)

<span data-ttu-id="b70cd-165">Nahraďte stávající kód následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="b70cd-165">Replace the existing code with the following code.</span></span> <span data-ttu-id="b70cd-166">Pro zjednodušení tento příklad, jsou data uložená v seznamu, nikoli databázi.</span><span class="sxs-lookup"><span data-stu-id="b70cd-166">To simplify this example, the data is stored in a list rather than a database.</span></span> <span data-ttu-id="b70cd-167">Seznam definované v této třídě představuje produkční data.</span><span class="sxs-lookup"><span data-stu-id="b70cd-167">The list defined in this class represents the production data.</span></span> <span data-ttu-id="b70cd-168">Všimněte si, že kontroler obsahuje konstruktor, který jako parametr používá seznam objektů produktu.</span><span class="sxs-lookup"><span data-stu-id="b70cd-168">Notice that the controller includes a constructor that takes as a parameter a list of Product objects.</span></span> <span data-ttu-id="b70cd-169">Tento konstruktor umožňuje předat testovacích dat při testování částí.</span><span class="sxs-lookup"><span data-stu-id="b70cd-169">This constructor enables you to pass test data when unit testing.</span></span> <span data-ttu-id="b70cd-170">Kontroler také zahrnuje dva **asynchronní** metody pro ilustraci asynchronních metod testování částí.</span><span class="sxs-lookup"><span data-stu-id="b70cd-170">The controller also includes two **async** methods to illustrate unit testing asynchronous methods.</span></span> <span data-ttu-id="b70cd-171">Asynchronní metody byly implementované voláním **Task.FromResult** minimalizovat cizí kód, ale obvykle metody bude zahrnovat náročná operace.</span><span class="sxs-lookup"><span data-stu-id="b70cd-171">These async methods were implemented by calling **Task.FromResult** to minimize extraneous code, but normally the methods would include resource-intensive operations.</span></span>

[!code-csharp[Main](unit-testing-with-aspnet-web-api/samples/sample2.cs)]

<span data-ttu-id="b70cd-172">Metoda GetProduct vrátí instanci **IHttpActionResult** rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b70cd-172">The GetProduct method returns an instance of the **IHttpActionResult** interface.</span></span> <span data-ttu-id="b70cd-173">IHttpActionResult je jedním z nových funkcí ve webovém rozhraní API 2 a zjednodušuje vývoj jednotkových testů.</span><span class="sxs-lookup"><span data-stu-id="b70cd-173">IHttpActionResult is one of the new features in Web API 2, and it simplifies unit test development.</span></span> <span data-ttu-id="b70cd-174">Třídy, které implementují rozhraní IHttpActionResult se nacházejí v [System.Web.Http.Results](https://msdn.microsoft.com/library/system.web.http.results.aspx) oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="b70cd-174">Classes that implement the IHttpActionResult interface are found in the [System.Web.Http.Results](https://msdn.microsoft.com/library/system.web.http.results.aspx) namespace.</span></span> <span data-ttu-id="b70cd-175">Tyto třídy představují možné odpovědi z požadavku akce a odpovídají stavové kódy HTTP.</span><span class="sxs-lookup"><span data-stu-id="b70cd-175">These classes represent possible responses from an action request, and they correspond to HTTP status codes.</span></span>

<span data-ttu-id="b70cd-176">Sestavte řešení.</span><span class="sxs-lookup"><span data-stu-id="b70cd-176">Build the solution.</span></span>

<span data-ttu-id="b70cd-177">Nyní jste připraveni nastavit testovacího projektu.</span><span class="sxs-lookup"><span data-stu-id="b70cd-177">You are now ready to set up the test project.</span></span>

<a id="testpackages"></a>
## <a name="install-nuget-packages-in-test-project"></a><span data-ttu-id="b70cd-178">Instalace balíčků NuGet v projektu testů</span><span class="sxs-lookup"><span data-stu-id="b70cd-178">Install NuGet packages in test project</span></span>

<span data-ttu-id="b70cd-179">Když použijete prázdnou šablonu k vytvoření aplikace, projekt testu jednotek (StoreApp.Tests) neobsahuje žádné nainstalované balíčky NuGet.</span><span class="sxs-lookup"><span data-stu-id="b70cd-179">When you use the Empty template to create an application, the unit test project (StoreApp.Tests) does not include any installed NuGet packages.</span></span> <span data-ttu-id="b70cd-180">Další šablony, jako je například šabloně webového rozhraní API patří některé balíčky NuGet v projektu testování částí.</span><span class="sxs-lookup"><span data-stu-id="b70cd-180">Other templates, such as the Web API template, include some NuGet packages in the unit test project.</span></span> <span data-ttu-id="b70cd-181">Pro účely tohoto kurzu musí zahrnovat balíček Microsoft ASP.NET Web API 2 Core do testovacího projektu.</span><span class="sxs-lookup"><span data-stu-id="b70cd-181">For this tutorial, you must include the Microsoft ASP.NET Web API 2 Core package to the test project.</span></span>

<span data-ttu-id="b70cd-182">Klikněte pravým tlačítkem na projekt StoreApp.Tests a vyberte **spravovat balíčky NuGet**.</span><span class="sxs-lookup"><span data-stu-id="b70cd-182">Right-click the StoreApp.Tests project and select **Manage NuGet Packages**.</span></span> <span data-ttu-id="b70cd-183">Je nutné vybrat projekt StoreApp.Tests přidat balíčky pro tento projekt.</span><span class="sxs-lookup"><span data-stu-id="b70cd-183">You must select the StoreApp.Tests project to add the packages to that project.</span></span>

![Správa balíčků](unit-testing-with-aspnet-web-api/_static/image8.png)

<span data-ttu-id="b70cd-185">Najít a nainstalovat balíček Microsoft ASP.NET Web API 2 jádra.</span><span class="sxs-lookup"><span data-stu-id="b70cd-185">Find and install Microsoft ASP.NET Web API 2 Core package.</span></span>

![Nainstalujte balíček základního webového rozhraní api](unit-testing-with-aspnet-web-api/_static/image9.png)

<span data-ttu-id="b70cd-187">Zavřete okno Spravovat balíčky NuGet.</span><span class="sxs-lookup"><span data-stu-id="b70cd-187">Close the Manage NuGet Packages window.</span></span>

<a id="tests"></a>
## <a name="create-tests"></a><span data-ttu-id="b70cd-188">Vytváření testů</span><span class="sxs-lookup"><span data-stu-id="b70cd-188">Create tests</span></span>

<span data-ttu-id="b70cd-189">Ve výchozím nastavení obsahuje soubor prázdný test s názvem UnitTest1.cs testovacího projektu.</span><span class="sxs-lookup"><span data-stu-id="b70cd-189">By default, your test project includes an empty test file named UnitTest1.cs.</span></span> <span data-ttu-id="b70cd-190">Tento soubor obsahuje atributy, že pomocí kterých lze vytvářet testovací metody.</span><span class="sxs-lookup"><span data-stu-id="b70cd-190">This file shows the attributes you use to create test methods.</span></span> <span data-ttu-id="b70cd-191">Pro testy jednotky můžete použít tento soubor nebo vytvořte svůj vlastní soubor.</span><span class="sxs-lookup"><span data-stu-id="b70cd-191">For your unit tests, you can either use this file or create your own file.</span></span>

![UnitTest1](unit-testing-with-aspnet-web-api/_static/image10.png)

<span data-ttu-id="b70cd-193">V tomto kurzu vytvoříte testovací třídy.</span><span class="sxs-lookup"><span data-stu-id="b70cd-193">For this tutorial, you will create your own test class.</span></span> <span data-ttu-id="b70cd-194">Můžete odstranit soubor UnitTest1.cs.</span><span class="sxs-lookup"><span data-stu-id="b70cd-194">You can delete the UnitTest1.cs file.</span></span> <span data-ttu-id="b70cd-195">Přidejte třídu pojmenovanou **TestSimpleProductController.cs**a nahraďte kód následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="b70cd-195">Add a class named **TestSimpleProductController.cs**, and replace the code with the following code.</span></span>

[!code-csharp[Main](unit-testing-with-aspnet-web-api/samples/sample3.cs)]

<a id="runtests"></a>
## <a name="run-tests"></a><span data-ttu-id="b70cd-196">Spouštění testů</span><span class="sxs-lookup"><span data-stu-id="b70cd-196">Run tests</span></span>

<span data-ttu-id="b70cd-197">Nyní jste připraveni ke spuštění testů.</span><span class="sxs-lookup"><span data-stu-id="b70cd-197">You are now ready to run the tests.</span></span> <span data-ttu-id="b70cd-198">Všechny metody, která jsou označena **TestMethod** atribut bude ověřovat.</span><span class="sxs-lookup"><span data-stu-id="b70cd-198">All of the method that are marked with the **TestMethod** attribute will be tested.</span></span> <span data-ttu-id="b70cd-199">Z **Test** položku nabídky, spusťte testy.</span><span class="sxs-lookup"><span data-stu-id="b70cd-199">From the **Test** menu item, run the tests.</span></span>

![Provádění testů](unit-testing-with-aspnet-web-api/_static/image11.png)

<span data-ttu-id="b70cd-201">Otevřít **Průzkumník testů** okně a Všimněte si, že výsledky testů.</span><span class="sxs-lookup"><span data-stu-id="b70cd-201">Open the **Test Explorer** window, and notice the results of the tests.</span></span>

![výsledky testu](unit-testing-with-aspnet-web-api/_static/image12.png)

## <a name="summary"></a><span data-ttu-id="b70cd-203">Souhrn</span><span class="sxs-lookup"><span data-stu-id="b70cd-203">Summary</span></span>

<span data-ttu-id="b70cd-204">Dokončili jste tento kurz.</span><span class="sxs-lookup"><span data-stu-id="b70cd-204">You have completed this tutorial.</span></span> <span data-ttu-id="b70cd-205">Data v tomto kurzu se záměrně zjednodušená zaměřit se na testování podmínek.</span><span class="sxs-lookup"><span data-stu-id="b70cd-205">The data in this tutorial was intentionally simplified to focus on unit testing conditions.</span></span> <span data-ttu-id="b70cd-206">Testování pokročilejší scénáře datových částí, naleznete v tématu [vytvoření modelu Entity Framework při jednotky testování ASP.NET Web API 2](mocking-entity-framework-when-unit-testing-aspnet-web-api-2.md).</span><span class="sxs-lookup"><span data-stu-id="b70cd-206">For unit testing more advanced data scenarios, see [Mocking Entity Framework when Unit Testing ASP.NET Web API 2](mocking-entity-framework-when-unit-testing-aspnet-web-api-2.md).</span></span>

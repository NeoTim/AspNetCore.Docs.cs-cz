---
title: Zkontrolujte podrobnosti a odstranit metody aplikace ASP.NET Core
author: rick-anderson
description: Další informace o metodě kontroleru podrobnosti a zobrazit v základní aplikaci ASP.NET Core MVC.
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/details
ms.openlocfilehash: 056017ea4f4073fa0b1cd747d06775b2a33616cf
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64901944"
---
# <a name="examine-the-details-and-delete-methods-of-an-aspnet-core-app"></a><span data-ttu-id="33244-103">Zkontrolujte podrobnosti a odstranit metody aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="33244-103">Examine the Details and Delete methods of an ASP.NET Core app</span></span>

<span data-ttu-id="33244-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="33244-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="33244-105">Otevřete řadič video a zkontrolovat `Details` metody:</span><span class="sxs-lookup"><span data-stu-id="33244-105">Open the Movie controller and examine the `Details` method:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_details)]

<span data-ttu-id="33244-106">Modul generování uživatelského rozhraní MVC, který vytvořili této metodě akce přidá komentář zobrazující požadavek HTTP, který vyvolá metodu.</span><span class="sxs-lookup"><span data-stu-id="33244-106">The MVC scaffolding engine that created this action method adds a comment showing an HTTP request that invokes the method.</span></span> <span data-ttu-id="33244-107">V tomto případě je požadavek GET s tři segmenty adres URL, `Movies` kontroleru, `Details` metody a `id` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="33244-107">In this case it's a GET request with three URL segments, the `Movies` controller, the `Details` method, and an `id` value.</span></span> <span data-ttu-id="33244-108">Odvolání tyto segmenty jsou definovány v *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="33244-108">Recall these segments are defined in *Startup.cs*.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

<span data-ttu-id="33244-109">EF usnadňuje hledání pro data s využitím `FirstOrDefaultAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="33244-109">EF makes it easy to search for data using the `FirstOrDefaultAsync` method.</span></span> <span data-ttu-id="33244-110">Důležitou funkci zabezpečení integrované do metody je, že kód ověří, že metoda hledání našla filmu předtím, než se pokusí provádět s ním.</span><span class="sxs-lookup"><span data-stu-id="33244-110">An important security feature built into the method is that the code verifies that the search method has found a movie before it tries to do anything with it.</span></span> <span data-ttu-id="33244-111">Například se hacker by mohla zanést chyby do lokality tak, že změníte adresu URL vytvořené odkazy z `http://localhost:xxxx/Movies/Details/1` na něco jako `http://localhost:xxxx/Movies/Details/12345` (nebo jinou hodnotu, která nepředstavuje skutečný film).</span><span class="sxs-lookup"><span data-stu-id="33244-111">For example, a hacker could introduce errors into the site by changing the URL created by the links from `http://localhost:xxxx/Movies/Details/1` to something like  `http://localhost:xxxx/Movies/Details/12345` (or some other value that doesn't represent an actual movie).</span></span> <span data-ttu-id="33244-112">Pokud jste nezaškrtli null filmu, aplikace by vyvolat výjimku.</span><span class="sxs-lookup"><span data-stu-id="33244-112">If you didn't check for a null movie, the app would throw an exception.</span></span>

<span data-ttu-id="33244-113">Zkontrolujte `Delete` a `DeleteConfirmed` metody.</span><span class="sxs-lookup"><span data-stu-id="33244-113">Examine the `Delete` and `DeleteConfirmed` methods.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_delete)]

<span data-ttu-id="33244-114">Všimněte si, `HTTP GET Delete` metody nedojde k odstranění zadaného film, vrátí zobrazení videa ve kterém můžete odeslat (HttpPost) odstranění.</span><span class="sxs-lookup"><span data-stu-id="33244-114">Note that the `HTTP GET Delete` method doesn't delete the specified movie, it returns a view of the movie where you can submit (HttpPost) the deletion.</span></span> <span data-ttu-id="33244-115">Operace delete v reakci na příkaz GET požádat o (nebo k tomuto účelu, provádění operace úpravy, vytváření operace nebo jiné operace, která se mění data) otevře bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="33244-115">Performing a delete operation in response to a GET request (or for that matter, performing an edit operation, create operation, or any other operation that changes data) opens up a security hole.</span></span>

<span data-ttu-id="33244-116">`[HttpPost]` Pojmenovanou metodu, která odstraní data `DeleteConfirmed` poskytnout metodu HTTP POST jedinečný podpis nebo název.</span><span class="sxs-lookup"><span data-stu-id="33244-116">The `[HttpPost]` method that deletes the data is named `DeleteConfirmed` to give the HTTP POST method a unique signature or name.</span></span> <span data-ttu-id="33244-117">Níže jsou zobrazena podpisy dvou metod:</span><span class="sxs-lookup"><span data-stu-id="33244-117">The two method signatures are shown below:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]

<span data-ttu-id="33244-118">Modul CLR (CLR) vyžaduje přetížené metody mají jedinečný podpis (stejný název metody, ale jiný seznam parametrů).</span><span class="sxs-lookup"><span data-stu-id="33244-118">The common language runtime (CLR) requires overloaded methods to have a unique parameter signature (same method name but different list of parameters).</span></span> <span data-ttu-id="33244-119">Tady však dva `Delete` metody – jeden u metody GET - a jeden pro metodu POST, že mají stejný podpis parametru.</span><span class="sxs-lookup"><span data-stu-id="33244-119">However, here you need two `Delete` methods -- one for GET and one for POST -- that both have the same parameter signature.</span></span> <span data-ttu-id="33244-120">(I potřebují tak, aby přijímal celá čísla jako parametr.)</span><span class="sxs-lookup"><span data-stu-id="33244-120">(They both need to accept a single integer as a parameter.)</span></span>

<span data-ttu-id="33244-121">Existují dva přístupy k tomuto problému, jeden je umožnit metody různými názvy.</span><span class="sxs-lookup"><span data-stu-id="33244-121">There are two approaches to this problem, one is to give the methods different names.</span></span> <span data-ttu-id="33244-122">Je to, co mechanismu generování uživatelského rozhraní nebyl v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="33244-122">That's what the scaffolding mechanism did in the preceding example.</span></span> <span data-ttu-id="33244-123">Ale zavádí malé problému: ASP.NET mapuje segmentů adresy URL na metody akce podle názvu, a Pokud přejmenujete metodu, obvykle směrování nebude moci najít tuto metodu.</span><span class="sxs-lookup"><span data-stu-id="33244-123">However, this introduces a small problem: ASP.NET maps segments of a URL to action methods by name, and if you rename a method, routing normally wouldn't be able to find that method.</span></span> <span data-ttu-id="33244-124">Řešení se zobrazí v příkladu, který je přidat `ActionName("Delete")` atribut `DeleteConfirmed` metoda.</span><span class="sxs-lookup"><span data-stu-id="33244-124">The solution is what you see in the example, which is to add the `ActionName("Delete")` attribute to the `DeleteConfirmed` method.</span></span> <span data-ttu-id="33244-125">Tento atribut provádí mapování pro směrování systém tak, aby se najít adresu URL, která zahrnuje /Delete/ pro požadavek POST `DeleteConfirmed` metody.</span><span class="sxs-lookup"><span data-stu-id="33244-125">That attribute performs mapping for the routing system so that a URL that includes /Delete/ for a POST request will find the `DeleteConfirmed` method.</span></span>

<span data-ttu-id="33244-126">Další běžné alternativní pro metody, které mají stejné názvy a podpisy se uměle změna podpisu metody POST, která zahrnují speciální (nepoužívané) parametru.</span><span class="sxs-lookup"><span data-stu-id="33244-126">Another common work around for methods that have identical names and signatures is to artificially change the signature of the POST method to include an extra (unused) parameter.</span></span> <span data-ttu-id="33244-127">To je, co jsme to udělali v předchozí odeslání když jsme přidali `notUsed` parametru.</span><span class="sxs-lookup"><span data-stu-id="33244-127">That's what we did in a previous post when we added the `notUsed` parameter.</span></span> <span data-ttu-id="33244-128">Může to samé udělá zde pro `[HttpPost] Delete` metody:</span><span class="sxs-lookup"><span data-stu-id="33244-128">You could do the same thing here for the `[HttpPost] Delete` method:</span></span>

```csharp
// POST: Movies/Delete/6
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

### <a name="publish-to-azure"></a><span data-ttu-id="33244-129">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="33244-129">Publish to Azure</span></span>

<span data-ttu-id="33244-130">Informace o nasazení do Azure najdete v tématu [kurzu: Vytvoření webové aplikace .NET Core využívající SQL Database ve službě Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span><span class="sxs-lookup"><span data-stu-id="33244-130">For information on deploying to Azure, see [Tutorial: Build a .NET Core and SQL Database web app in Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="33244-131">Předchozí</span><span class="sxs-lookup"><span data-stu-id="33244-131">Previous</span></span>](validation.md)

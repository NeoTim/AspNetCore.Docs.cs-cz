---
title: Část 10, Projděte si metody Details a DELETE aplikace ASP.NET Core.
author: rick-anderson
description: Část 10 kurzů kurzu na ASP.NET Core MVC.
ms.author: riande
ms.date: 12/13/2018
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/details
ms.openlocfilehash: 4683f106b3372a2f14f289b39c8a0b8b6c8b5aa1
ms.sourcegitcommit: c9b03d8a6a4dcc59e4aacb30a691f349235a74c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379364"
---
# <a name="part-10-examine-the-details-and-delete-methods-of-an-aspnet-core-app"></a><span data-ttu-id="5baec-103">Část 10, Projděte si metody Details a DELETE aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5baec-103">Part 10, examine the Details and Delete methods of an ASP.NET Core app</span></span>

<span data-ttu-id="5baec-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5baec-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5baec-105">Otevřete kontroler filmů a prověřte tuto `Details` metodu:</span><span class="sxs-lookup"><span data-stu-id="5baec-105">Open the Movie controller and examine the `Details` method:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_details)]

<span data-ttu-id="5baec-106">Modul generování uživatelského rozhraní MVC, který vytvořil tuto metodu Action, přidá komentář ukazující požadavek HTTP, který vyvolá metodu.</span><span class="sxs-lookup"><span data-stu-id="5baec-106">The MVC scaffolding engine that created this action method adds a comment showing an HTTP request that invokes the method.</span></span> <span data-ttu-id="5baec-107">V tomto případě se jedná o požadavek GET se třemi segmenty adresy URL, `Movies` řadičem, `Details` metodou a `id` hodnotou.</span><span class="sxs-lookup"><span data-stu-id="5baec-107">In this case it's a GET request with three URL segments, the `Movies` controller, the `Details` method, and an `id` value.</span></span> <span data-ttu-id="5baec-108">Odvolání těchto segmentů je definováno v *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="5baec-108">Recall these segments are defined in *Startup.cs*.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie3/Startup.cs?highlight=5&name=snippet_1)]

<span data-ttu-id="5baec-109">EF usnadňuje hledání dat pomocí `FirstOrDefaultAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="5baec-109">EF makes it easy to search for data using the `FirstOrDefaultAsync` method.</span></span> <span data-ttu-id="5baec-110">Důležitou funkcí zabezpečení, která je integrována do metody, je, že kód ověřuje, zda metoda hledání nalezla film předtím, než se pokusí s ním něco udělat.</span><span class="sxs-lookup"><span data-stu-id="5baec-110">An important security feature built into the method is that the code verifies that the search method has found a movie before it tries to do anything with it.</span></span> <span data-ttu-id="5baec-111">Hacker by například mohl do lokality způsobit chyby změnou adresy URL vytvořené odkazy z `http://localhost:{PORT}/Movies/Details/1` na něco podobného  `http://localhost:{PORT}/Movies/Details/12345` (nebo jiné hodnoty, která nepředstavuje skutečný film).</span><span class="sxs-lookup"><span data-stu-id="5baec-111">For example, a hacker could introduce errors into the site by changing the URL created by the links from `http://localhost:{PORT}/Movies/Details/1` to something like  `http://localhost:{PORT}/Movies/Details/12345` (or some other value that doesn't represent an actual movie).</span></span> <span data-ttu-id="5baec-112">Pokud jste nezkontrolovali film s hodnotou null, aplikace vyvolá výjimku.</span><span class="sxs-lookup"><span data-stu-id="5baec-112">If you didn't check for a null movie, the app would throw an exception.</span></span>

<span data-ttu-id="5baec-113">Projděte `Delete` si `DeleteConfirmed` metody a.</span><span class="sxs-lookup"><span data-stu-id="5baec-113">Examine the `Delete` and `DeleteConfirmed` methods.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_delete)]

<span data-ttu-id="5baec-114">Všimněte si, že `HTTP GET Delete` Metoda neodstraní zadaný film, vrátí zobrazení videa, kde můžete odeslat (HTTPPOST) odstranění.</span><span class="sxs-lookup"><span data-stu-id="5baec-114">Note that the `HTTP GET Delete` method doesn't delete the specified movie, it returns a view of the movie where you can submit (HttpPost) the deletion.</span></span> <span data-ttu-id="5baec-115">Provádění operace odstranění v reakci na požadavek GET (nebo pro tuto skutečnost, provádění operace Edit, operace vytvoření nebo jakékoli jiné operace, která mění data) otevře bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="5baec-115">Performing a delete operation in response to a GET request (or for that matter, performing an edit operation, create operation, or any other operation that changes data) opens up a security hole.</span></span>

<span data-ttu-id="5baec-116">`[HttpPost]`Metoda, která odstraňuje data, je pojmenována `DeleteConfirmed` tak, aby metodě http post poskytovala jedinečný podpis nebo název.</span><span class="sxs-lookup"><span data-stu-id="5baec-116">The `[HttpPost]` method that deletes the data is named `DeleteConfirmed` to give the HTTP POST method a unique signature or name.</span></span> <span data-ttu-id="5baec-117">Níže jsou uvedené signatury dvou metod:</span><span class="sxs-lookup"><span data-stu-id="5baec-117">The two method signatures are shown below:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]

<span data-ttu-id="5baec-118">Modul CLR (Common Language Runtime) vyžaduje, aby přetížené metody měly jedinečný podpis parametru (stejný název metody, ale jiný seznam parametrů).</span><span class="sxs-lookup"><span data-stu-id="5baec-118">The common language runtime (CLR) requires overloaded methods to have a unique parameter signature (same method name but different list of parameters).</span></span> <span data-ttu-id="5baec-119">Zde však budete potřebovat dvě `Delete` metody--One pro Get a One pro post--oba mají stejný podpis parametru.</span><span class="sxs-lookup"><span data-stu-id="5baec-119">However, here you need two `Delete` methods -- one for GET and one for POST -- that both have the same parameter signature.</span></span> <span data-ttu-id="5baec-120">(Obě musí přijmout jedno celé číslo jako parametr.)</span><span class="sxs-lookup"><span data-stu-id="5baec-120">(They both need to accept a single integer as a parameter.)</span></span>

<span data-ttu-id="5baec-121">Existují dva přístupy k tomuto problému. jedna z nich poskytuje metody odlišné názvy.</span><span class="sxs-lookup"><span data-stu-id="5baec-121">There are two approaches to this problem, one is to give the methods different names.</span></span> <span data-ttu-id="5baec-122">To je to, co byl mechanismus generování uživatelského rozhraní použit v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="5baec-122">That's what the scaffolding mechanism did in the preceding example.</span></span> <span data-ttu-id="5baec-123">To však přináší malý problém: ASP.NET mapuje segmenty adresy URL na metody akcí podle názvu a Pokud přejmenujete metodu, směrování normálně nedokáže tuto metodu najít.</span><span class="sxs-lookup"><span data-stu-id="5baec-123">However, this introduces a small problem: ASP.NET maps segments of a URL to action methods by name, and if you rename a method, routing normally wouldn't be able to find that method.</span></span> <span data-ttu-id="5baec-124">Řešení je to, co vidíte v příkladu, což je přidání `ActionName("Delete")` atributu do `DeleteConfirmed` metody.</span><span class="sxs-lookup"><span data-stu-id="5baec-124">The solution is what you see in the example, which is to add the `ActionName("Delete")` attribute to the `DeleteConfirmed` method.</span></span> <span data-ttu-id="5baec-125">Tento atribut provádí mapování pro systém směrování, takže adresa URL, která obsahuje/DELETE/pro požadavek POST, tuto metodu najde `DeleteConfirmed` .</span><span class="sxs-lookup"><span data-stu-id="5baec-125">That attribute performs mapping for the routing system so that a URL that includes /Delete/ for a POST request will find the `DeleteConfirmed` method.</span></span>

<span data-ttu-id="5baec-126">Dalším běžným řešením pro metody, které mají stejný název a signatury, je umělá změna signatury metody POST tak, aby zahrnovala nadbytečný (nepoužitý) parametr.</span><span class="sxs-lookup"><span data-stu-id="5baec-126">Another common work around for methods that have identical names and signatures is to artificially change the signature of the POST method to include an extra (unused) parameter.</span></span> <span data-ttu-id="5baec-127">To je to, co jsme při přidání parametru použili v předchozím příspěvku `notUsed` .</span><span class="sxs-lookup"><span data-stu-id="5baec-127">That's what we did in a previous post when we added the `notUsed` parameter.</span></span> <span data-ttu-id="5baec-128">To samé můžete udělat pro `[HttpPost] Delete` metodu:</span><span class="sxs-lookup"><span data-stu-id="5baec-128">You could do the same thing here for the `[HttpPost] Delete` method:</span></span>

```csharp
// POST: Movies/Delete/6
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

### <a name="publish-to-azure"></a><span data-ttu-id="5baec-129">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="5baec-129">Publish to Azure</span></span>

<span data-ttu-id="5baec-130">Informace o nasazení do Azure najdete v tématu [kurz: sestavení aplikace ASP.NET Core a SQL Database v Azure App Service](/azure/app-service/tutorial-dotnetcore-sqldb-app).</span><span class="sxs-lookup"><span data-stu-id="5baec-130">For information on deploying to Azure, see [Tutorial: Build an ASP.NET Core and SQL Database app in Azure App Service](/azure/app-service/tutorial-dotnetcore-sqldb-app).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="5baec-131">Předchozí</span><span class="sxs-lookup"><span data-stu-id="5baec-131">Previous</span></span>](validation.md)

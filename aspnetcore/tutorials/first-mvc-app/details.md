---
title: Prohlédněte si metody podrobnosti a odstranění aplikace ASP.NET Core
author: rick-anderson
description: Seznamte se s metodou kontroleru podrobností a zobrazením v základní aplikaci ASP.NET Core MVC.
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/details
ms.openlocfilehash: 04eb2efa4e67d84e575580a6248d0b5b567064af
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662909"
---
# <a name="examine-the-details-and-delete-methods-of-an-aspnet-core-app"></a><span data-ttu-id="06dfa-103">Prohlédněte si metody podrobnosti a odstranění aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="06dfa-103">Examine the Details and Delete methods of an ASP.NET Core app</span></span>

<span data-ttu-id="06dfa-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="06dfa-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="06dfa-105">Otevřete movie kontroler `Details` a zkontrolujte metodu:</span><span class="sxs-lookup"><span data-stu-id="06dfa-105">Open the Movie controller and examine the `Details` method:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_details)]

<span data-ttu-id="06dfa-106">Modul scaffing Engine MVC, který vytvořil tuto metodu akce přidá komentář zobrazující požadavek HTTP, který vyvolá metodu.</span><span class="sxs-lookup"><span data-stu-id="06dfa-106">The MVC scaffolding engine that created this action method adds a comment showing an HTTP request that invokes the method.</span></span> <span data-ttu-id="06dfa-107">V tomto případě je požadavek GET se třemi segmenty URL, `Movies` řadič, `Details` metoda a `id` hodnota.</span><span class="sxs-lookup"><span data-stu-id="06dfa-107">In this case it's a GET request with three URL segments, the `Movies` controller, the `Details` method, and an `id` value.</span></span> <span data-ttu-id="06dfa-108">Odvolání tyto segmenty jsou definovány v *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="06dfa-108">Recall these segments are defined in *Startup.cs*.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie3/Startup.cs?highlight=5&name=snippet_1)]

<span data-ttu-id="06dfa-109">EF usnadňuje vyhledávání dat pomocí `FirstOrDefaultAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="06dfa-109">EF makes it easy to search for data using the `FirstOrDefaultAsync` method.</span></span> <span data-ttu-id="06dfa-110">Důležitou funkcí zabezpečení integrovanou do metody je, že kód ověří, zda metoda hledání nalezla film dříve, než se s ním pokusí něco udělat.</span><span class="sxs-lookup"><span data-stu-id="06dfa-110">An important security feature built into the method is that the code verifies that the search method has found a movie before it tries to do anything with it.</span></span> <span data-ttu-id="06dfa-111">Hacker může například zavést chyby na webu změnou `http://localhost:{PORT}/Movies/Details/1` adresy URL `http://localhost:{PORT}/Movies/Details/12345` vytvořené odkazy z něčeho podobného (nebo jiné hodnoty, která nepředstavuje skutečný film).</span><span class="sxs-lookup"><span data-stu-id="06dfa-111">For example, a hacker could introduce errors into the site by changing the URL created by the links from `http://localhost:{PORT}/Movies/Details/1` to something like  `http://localhost:{PORT}/Movies/Details/12345` (or some other value that doesn't represent an actual movie).</span></span> <span data-ttu-id="06dfa-112">Pokud jste nezkontrolovali nulový film, aplikace by vyvolat výjimku.</span><span class="sxs-lookup"><span data-stu-id="06dfa-112">If you didn't check for a null movie, the app would throw an exception.</span></span>

<span data-ttu-id="06dfa-113">Prozkoumejte `Delete` `DeleteConfirmed` metody a.</span><span class="sxs-lookup"><span data-stu-id="06dfa-113">Examine the `Delete` and `DeleteConfirmed` methods.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_delete)]

<span data-ttu-id="06dfa-114">Všimněte `HTTP GET Delete` si, že metoda neodstraní zadaný film, vrátí zobrazení filmu, kde můžete odeslat (HttpPost) odstranění.</span><span class="sxs-lookup"><span data-stu-id="06dfa-114">Note that the `HTTP GET Delete` method doesn't delete the specified movie, it returns a view of the movie where you can submit (HttpPost) the deletion.</span></span> <span data-ttu-id="06dfa-115">Provedení operace odstranění v reakci na požadavek GET (nebo když na to přijde, provedení operace úprav, vytvoření operace nebo jiné operace, která mění data) otevře bezpečnostní díru.</span><span class="sxs-lookup"><span data-stu-id="06dfa-115">Performing a delete operation in response to a GET request (or for that matter, performing an edit operation, create operation, or any other operation that changes data) opens up a security hole.</span></span>

<span data-ttu-id="06dfa-116">Metoda, `[HttpPost]` která odstraní data, `DeleteConfirmed` je pojmenována tak, aby metoda HTTP POST měla jedinečný podpis nebo název.</span><span class="sxs-lookup"><span data-stu-id="06dfa-116">The `[HttpPost]` method that deletes the data is named `DeleteConfirmed` to give the HTTP POST method a unique signature or name.</span></span> <span data-ttu-id="06dfa-117">Dvě podpisy metody jsou uvedeny níže:</span><span class="sxs-lookup"><span data-stu-id="06dfa-117">The two method signatures are shown below:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]

<span data-ttu-id="06dfa-118">Běžný jazyk runtime (CLR) vyžaduje přetížené metody mít jedinečný podpis parametru (stejný název metody, ale jiný seznam parametrů).</span><span class="sxs-lookup"><span data-stu-id="06dfa-118">The common language runtime (CLR) requires overloaded methods to have a unique parameter signature (same method name but different list of parameters).</span></span> <span data-ttu-id="06dfa-119">Zde však potřebujete `Delete` dvě metody – jednu pro GET a jednu pro POST – které mají oba stejný podpis parametru.</span><span class="sxs-lookup"><span data-stu-id="06dfa-119">However, here you need two `Delete` methods -- one for GET and one for POST -- that both have the same parameter signature.</span></span> <span data-ttu-id="06dfa-120">(Oba potřebují přijmout jedno celé číslo jako parametr.)</span><span class="sxs-lookup"><span data-stu-id="06dfa-120">(They both need to accept a single integer as a parameter.)</span></span>

<span data-ttu-id="06dfa-121">Existují dva přístupy k tomuto problému, jeden je dát metody různé názvy.</span><span class="sxs-lookup"><span data-stu-id="06dfa-121">There are two approaches to this problem, one is to give the methods different names.</span></span> <span data-ttu-id="06dfa-122">To je to, co lešení mechanismus udělal v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="06dfa-122">That's what the scaffolding mechanism did in the preceding example.</span></span> <span data-ttu-id="06dfa-123">To však zavádí malý problém: ASP.NET mapuje segmenty adresy URL na metody akce podle názvu, a pokud přejmenujete metodu, směrování by normálně nebylo možné najít tuto metodu.</span><span class="sxs-lookup"><span data-stu-id="06dfa-123">However, this introduces a small problem: ASP.NET maps segments of a URL to action methods by name, and if you rename a method, routing normally wouldn't be able to find that method.</span></span> <span data-ttu-id="06dfa-124">Řešení je to, co vidíte v příkladu, který je přidat `ActionName("Delete")` atribut metody. `DeleteConfirmed`</span><span class="sxs-lookup"><span data-stu-id="06dfa-124">The solution is what you see in the example, which is to add the `ActionName("Delete")` attribute to the `DeleteConfirmed` method.</span></span> <span data-ttu-id="06dfa-125">Tento atribut provádí mapování pro systém směrování tak, aby adresa URL, `DeleteConfirmed` která obsahuje /Delete/ pro požadavek POST najde metodu.</span><span class="sxs-lookup"><span data-stu-id="06dfa-125">That attribute performs mapping for the routing system so that a URL that includes /Delete/ for a POST request will find the `DeleteConfirmed` method.</span></span>

<span data-ttu-id="06dfa-126">Další běžné řešení pro metody, které mají identické názvy a podpisy je uměle změnit podpis post metody zahrnout další (nepoužívané) parametr.</span><span class="sxs-lookup"><span data-stu-id="06dfa-126">Another common work around for methods that have identical names and signatures is to artificially change the signature of the POST method to include an extra (unused) parameter.</span></span> <span data-ttu-id="06dfa-127">To je to, co jsme udělali `notUsed` v předchozím příspěvku, když jsme přidali parametr.</span><span class="sxs-lookup"><span data-stu-id="06dfa-127">That's what we did in a previous post when we added the `notUsed` parameter.</span></span> <span data-ttu-id="06dfa-128">Dalo by se udělat totéž zde pro metodu: `[HttpPost] Delete`</span><span class="sxs-lookup"><span data-stu-id="06dfa-128">You could do the same thing here for the `[HttpPost] Delete` method:</span></span>

```csharp
// POST: Movies/Delete/6
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

### <a name="publish-to-azure"></a><span data-ttu-id="06dfa-129">Publikování aplikací do Azure</span><span class="sxs-lookup"><span data-stu-id="06dfa-129">Publish to Azure</span></span>

<span data-ttu-id="06dfa-130">Informace o nasazení do Azure najdete [v tématu: Vytvoření webové aplikace .NET Core a SQL Database ve službě Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span><span class="sxs-lookup"><span data-stu-id="06dfa-130">For information on deploying to Azure, see [Tutorial: Build a .NET Core and SQL Database web app in Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="06dfa-131">Předchozí</span><span class="sxs-lookup"><span data-stu-id="06dfa-131">Previous</span></span>](validation.md)

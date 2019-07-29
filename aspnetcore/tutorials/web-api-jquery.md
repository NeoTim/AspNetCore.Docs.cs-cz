---
title: 'Kurz: Volání webového rozhraní API pomocí jQuery pomocí ASP.NET Core'
author: rick-anderson
description: Naučte se volat ASP.NET Core webového rozhraní API pomocí jQuery.
ms.author: riande
ms.custom: mvc
ms.date: 07/20/2019
uid: tutorials/web-api-jquery
ms.openlocfilehash: eb8b2453fd037170a49f531fea4c3ef1c056292d
ms.sourcegitcommit: 0efb9e219fef481dee35f7b763165e488aa6cf9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602585"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-jquery"></a><span data-ttu-id="3cff1-103">Kurz: Volání rozhraní Web API ASP.NET Core pomocí jQuery</span><span class="sxs-lookup"><span data-stu-id="3cff1-103">Tutorial: Call an ASP.NET Core web API with jQuery</span></span>

<span data-ttu-id="3cff1-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3cff1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="3cff1-105">V tomto kurzu se dozvíte, jak volat ASP.NET Core webového rozhraní API pomocí jQuery.</span><span class="sxs-lookup"><span data-stu-id="3cff1-105">This tutorial shows how to call an ASP.NET Core web API with jQuery</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3cff1-106">Pro ASP.NET Core 2,2 se podívejte na verzi 2,2 [volání webového rozhraní API pomocí jQuery](xref:tutorials/first-web-api#call-the-api-with-jquery).</span><span class="sxs-lookup"><span data-stu-id="3cff1-106">For ASP.NET Core 2.2, see the 2.2 version of [Call the Web API with jQuery](xref:tutorials/first-web-api#call-the-api-with-jquery).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="3cff1-107">Požadavky</span><span class="sxs-lookup"><span data-stu-id="3cff1-107">Prerequisites</span></span>

<span data-ttu-id="3cff1-108">Úplný [kurz: Vytvoření webového rozhraní API](xref:tutorials/first-web-api)</span><span class="sxs-lookup"><span data-stu-id="3cff1-108">Complete [Tutorial: Create a web API](xref:tutorials/first-web-api)</span></span>

## <a name="call-the-api-with-jquery"></a><span data-ttu-id="3cff1-109">Volání rozhraní API pomocí jQuery</span><span class="sxs-lookup"><span data-stu-id="3cff1-109">Call the API with jQuery</span></span>

<span data-ttu-id="3cff1-110">V této části se přidá stránku HTML, který používá jQuery volat webové rozhraní api.</span><span class="sxs-lookup"><span data-stu-id="3cff1-110">In this section, an HTML page is added that uses jQuery to call the web api.</span></span> <span data-ttu-id="3cff1-111">jQuery zahájí požadavek a aktualizuje stránku s podrobnostmi o z odpovědi rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="3cff1-111">jQuery initiates the request and updates the page with the details from the API's response.</span></span>

<span data-ttu-id="3cff1-112">Nakonfigurujte aplikaci tak, aby sloužila pro [statické soubory](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) , a [Povolte výchozí mapování souborů](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) aktualizací *Startup.cs* pomocí následujícího zvýrazněného kódu:</span><span class="sxs-lookup"><span data-stu-id="3cff1-112">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJquery.cs?highlight=8-9&name=snippet_configure)]

<span data-ttu-id="3cff1-113">Vytvoření *wwwroot* složku v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="3cff1-113">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="3cff1-114">Přidat soubor HTML s názvem *index.html* k *wwwroot* adresáře.</span><span class="sxs-lookup"><span data-stu-id="3cff1-114">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="3cff1-115">Nahraďte jeho obsah následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="3cff1-115">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

<span data-ttu-id="3cff1-116">Přidejte soubor JavaScriptu s názvem *site.js* k *wwwroot* adresáře.</span><span class="sxs-lookup"><span data-stu-id="3cff1-116">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="3cff1-117">Nahraďte jeho obsah následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="3cff1-117">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="3cff1-118">Ke změně nastavení spouštěcí projekt ASP.NET Core může být nutné testovací stránka HTML místně:</span><span class="sxs-lookup"><span data-stu-id="3cff1-118">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="3cff1-119">Otevřít *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="3cff1-119">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="3cff1-120">Odeberte `launchUrl` vlastnost, aby se aplikace na *index.html*&mdash;výchozí soubor projektu.</span><span class="sxs-lookup"><span data-stu-id="3cff1-120">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="3cff1-121">Existuje několik způsobů, jak získat jQuery.</span><span class="sxs-lookup"><span data-stu-id="3cff1-121">There are several ways to get jQuery.</span></span> <span data-ttu-id="3cff1-122">V předchozím fragmentu kódu je načíst knihovnu ze sítě CDN.</span><span class="sxs-lookup"><span data-stu-id="3cff1-122">In the preceding snippet, the library is loaded from a CDN.</span></span>

<span data-ttu-id="3cff1-123">Tato ukázka volá všechny metody CRUD rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="3cff1-123">This sample calls all of the CRUD methods of the API.</span></span> <span data-ttu-id="3cff1-124">Následuje vysvětlení volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="3cff1-124">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="3cff1-125">Získání seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="3cff1-125">Get a list of to-do items</span></span>

<span data-ttu-id="3cff1-126">JQuery [ajax](https://api.jquery.com/jquery.ajax/) funkce odešle `GET` žádosti na rozhraní API, které vrací JSON představující pole položek úkolů.</span><span class="sxs-lookup"><span data-stu-id="3cff1-126">The jQuery [ajax](https://api.jquery.com/jquery.ajax/) function sends a `GET` request to the API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="3cff1-127">`success` Funkce zpětného volání je vyvolána, pokud neproběhne.</span><span class="sxs-lookup"><span data-stu-id="3cff1-127">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="3cff1-128">Při zpětném volání modelu DOM se aktualizuje informacemi úkolů.</span><span class="sxs-lookup"><span data-stu-id="3cff1-128">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="3cff1-129">Přidat položku seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="3cff1-129">Add a to-do item</span></span>

<span data-ttu-id="3cff1-130">[Ajax](https://api.jquery.com/jquery.ajax/) funkce odešle `POST` požadavek se úkol v textu požadavku.</span><span class="sxs-lookup"><span data-stu-id="3cff1-130">The [ajax](https://api.jquery.com/jquery.ajax/) function sends a `POST` request with the to-do item in the request body.</span></span> <span data-ttu-id="3cff1-131">`accepts` a `contentType` možnosti jsou nastaveny na `application/json` zadat typ média, který se přijalo a odeslalo.</span><span class="sxs-lookup"><span data-stu-id="3cff1-131">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="3cff1-132">Položky seznamu úkolů je převést na JSON pomocí [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="3cff1-132">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="3cff1-133">Rozhraní API po návratu úspěšné stavový kód, `getData` funkce se vyvolala aktualizovat tabulku HTML.</span><span class="sxs-lookup"><span data-stu-id="3cff1-133">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="3cff1-134">Aktualizace položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="3cff1-134">Update a to-do item</span></span>

<span data-ttu-id="3cff1-135">Aktualizace položky úkolu je podobné jako přidání jednoho.</span><span class="sxs-lookup"><span data-stu-id="3cff1-135">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="3cff1-136">`url` Změny přidat jedinečný identifikátor položky a `type` je `PUT`.</span><span class="sxs-lookup"><span data-stu-id="3cff1-136">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="3cff1-137">Odstranit úkol</span><span class="sxs-lookup"><span data-stu-id="3cff1-137">Delete a to-do item</span></span>

<span data-ttu-id="3cff1-138">Odstranění položky úkolu se provádí tak, že nastavíte `type` při volání AJAX `DELETE` a zadáte jedinečný identifikátor položky v adrese URL.</span><span class="sxs-lookup"><span data-stu-id="3cff1-138">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

<span data-ttu-id="3cff1-139">Přejděte k dalšímu kurzu se naučíte generování stránek nápovědy rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="3cff1-139">Advance to the next tutorial to learn how to generate API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>
::: moniker-end
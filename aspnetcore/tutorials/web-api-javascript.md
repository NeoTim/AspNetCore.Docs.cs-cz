---
title: 'Kurz: volání ASP.NET Core webového rozhraní API pomocí JavaScriptu'
author: rick-anderson
description: Naučte se volat ASP.NET Core webového rozhraní API pomocí JavaScriptu.
ms.author: riande
ms.custom: mvc
ms.date: 11/26/2019
uid: tutorials/web-api-javascript
ms.openlocfilehash: 2a19a7d16ca8b8f5d6ac8eb99ad919b89f1e368b
ms.sourcegitcommit: 235623b6e5a5d1841139c82a11ac2b4b3f31a7a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114650"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a><span data-ttu-id="2423d-103">Kurz: volání ASP.NET Core webového rozhraní API pomocí JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="2423d-103">Tutorial: Call an ASP.NET Core web API with JavaScript</span></span>

<span data-ttu-id="2423d-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2423d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2423d-105">V tomto kurzu se dozvíte, jak volat ASP.NET Core webového rozhraní API s JavaScriptem pomocí [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API).</span><span class="sxs-lookup"><span data-stu-id="2423d-105">This tutorial shows how to call an ASP.NET Core web API with JavaScript, using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API).</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2423d-106">Pro ASP.NET Core 2,2 se podívejte na verzi 2,2 [volání webového rozhraní API pomocí JavaScriptu](xref:tutorials/first-web-api#call-the-web-api-with-javascript).</span><span class="sxs-lookup"><span data-stu-id="2423d-106">For ASP.NET Core 2.2, see the 2.2 version of [Call the web API with JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="2423d-107">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="2423d-107">Prerequisites</span></span>

* <span data-ttu-id="2423d-108">Úplný [kurz: Vytvoření webového rozhraní API](xref:tutorials/first-web-api)</span><span class="sxs-lookup"><span data-stu-id="2423d-108">Complete [Tutorial: Create a web API](xref:tutorials/first-web-api)</span></span>
* <span data-ttu-id="2423d-109">Znalost šablon stylů CSS, HTML a JavaScript</span><span class="sxs-lookup"><span data-stu-id="2423d-109">Familiarity with CSS, HTML, and JavaScript</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="2423d-110">Volání webového rozhraní API pomocí JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="2423d-110">Call the web API with JavaScript</span></span>

<span data-ttu-id="2423d-111">V této části přidáte stránku HTML obsahující formuláře pro vytváření a správu položek úkolů.</span><span class="sxs-lookup"><span data-stu-id="2423d-111">In this section, you'll add an HTML page containing forms for creating and managing to-do items.</span></span> <span data-ttu-id="2423d-112">Obslužné rutiny událostí jsou připojeny k prvkům na stránce.</span><span class="sxs-lookup"><span data-stu-id="2423d-112">Event handlers are attached to elements on the page.</span></span> <span data-ttu-id="2423d-113">Obslužné rutiny událostí mají za následek požadavky HTTP na metody akcí webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="2423d-113">The event handlers result in HTTP requests to the web API's action methods.</span></span> <span data-ttu-id="2423d-114">Funkce `fetch` pro načtení rozhraní API inicializuje jednotlivé požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="2423d-114">The Fetch API's `fetch` function initiates each HTTP request.</span></span>

<span data-ttu-id="2423d-115">Funkce `fetch` vrátí objekt [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) , který obsahuje odpověď HTTP reprezentovanou jako objekt `Response`.</span><span class="sxs-lookup"><span data-stu-id="2423d-115">The `fetch` function returns a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) object, which contains an HTTP response represented as a `Response` object.</span></span> <span data-ttu-id="2423d-116">Běžným vzorem je extrakce textu odpovědi JSON vyvoláním funkce `json` na objektu `Response`.</span><span class="sxs-lookup"><span data-stu-id="2423d-116">A common pattern is to extract the JSON response body by invoking the `json` function on the `Response` object.</span></span> <span data-ttu-id="2423d-117">JavaScript aktualizuje stránku s podrobnostmi z odpovědi webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="2423d-117">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="2423d-118">Nejjednodušší `fetch` volání přijímá jeden parametr reprezentující trasu.</span><span class="sxs-lookup"><span data-stu-id="2423d-118">The simplest `fetch` call accepts a single parameter representing the route.</span></span> <span data-ttu-id="2423d-119">Druhý parametr, známý jako objekt `init`, je volitelný.</span><span class="sxs-lookup"><span data-stu-id="2423d-119">A second parameter, known as the `init` object, is optional.</span></span> <span data-ttu-id="2423d-120">pro konfiguraci požadavku HTTP se používá `init`.</span><span class="sxs-lookup"><span data-stu-id="2423d-120">`init` is used to configure the HTTP request.</span></span>

1. <span data-ttu-id="2423d-121">Nakonfigurujte aplikaci tak, aby [sloužila statickým souborům](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a [povolovala výchozí mapování souborů](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="2423d-121">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="2423d-122">V metodě `Configure` *Startup.cs*je potřeba následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="2423d-122">The following highlighted code is needed in the `Configure` method of *Startup.cs*:</span></span>

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. <span data-ttu-id="2423d-123">Vytvořte složku *wwwroot* v kořenu projektu.</span><span class="sxs-lookup"><span data-stu-id="2423d-123">Create a *wwwroot* folder in the project root.</span></span>

1. <span data-ttu-id="2423d-124">Vytvořte složku *js* ve složce *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="2423d-124">Create a *js* folder inside of the *wwwroot* folder.</span></span>

1. <span data-ttu-id="2423d-125">Do složky *wwwroot* přidejte soubor HTML s názvem *index. html* .</span><span class="sxs-lookup"><span data-stu-id="2423d-125">Add an HTML file named *index.html* to the *wwwroot* folder.</span></span> <span data-ttu-id="2423d-126">Obsah souboru *index. html* nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="2423d-126">Replace the contents of *index.html* with the following markup:</span></span>

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. <span data-ttu-id="2423d-127">Do složky *wwwroot/js* přidejte soubor JavaScriptu s názvem *Web. js* .</span><span class="sxs-lookup"><span data-stu-id="2423d-127">Add a JavaScript file named *site.js* to the *wwwroot/js* folder.</span></span> <span data-ttu-id="2423d-128">Obsah *stránky site. js* nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="2423d-128">Replace the contents of *site.js* with the following code:</span></span>

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

<span data-ttu-id="2423d-129">Ke změně nastavení spouštěcí projekt ASP.NET Core může být nutné testovací stránka HTML místně:</span><span class="sxs-lookup"><span data-stu-id="2423d-129">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

1. <span data-ttu-id="2423d-130">Otevřete *Properties\launchSettings.JSON*.</span><span class="sxs-lookup"><span data-stu-id="2423d-130">Open *Properties\launchSettings.json*.</span></span>
1. <span data-ttu-id="2423d-131">Odebráním vlastnosti `launchUrl` vynutíte otevření aplikace v *indexu. html*&mdash;výchozím souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="2423d-131">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="2423d-132">Tato ukázka volá všechny metody CRUD webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="2423d-132">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="2423d-133">Následují vysvětlení požadavků webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="2423d-133">Following are explanations of the web API requests.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="2423d-134">Získání seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="2423d-134">Get a list of to-do items</span></span>

<span data-ttu-id="2423d-135">V následujícím kódu se pošle požadavek HTTP GET do trasy *API/TodoItems* :</span><span class="sxs-lookup"><span data-stu-id="2423d-135">In the following code, an HTTP GET request is sent to the *api/TodoItems* route:</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

<span data-ttu-id="2423d-136">Když webové rozhraní API vrátí úspěšný kód stavu, vyvolá se funkce `_displayItems`.</span><span class="sxs-lookup"><span data-stu-id="2423d-136">When the web API returns a successful status code, the `_displayItems` function is invoked.</span></span> <span data-ttu-id="2423d-137">Každá položka k tomuto účelu v parametru Array Accept `_displayItems` je přidána do tabulky s tlačítky **Upravit** a **Odstranit** .</span><span class="sxs-lookup"><span data-stu-id="2423d-137">Each to-do item in the array parameter accepted by `_displayItems` is added to a table with **Edit** and **Delete** buttons.</span></span> <span data-ttu-id="2423d-138">Pokud se požadavek webového rozhraní API nepovede, do konzoly prohlížeče se zaprotokoluje chyba.</span><span class="sxs-lookup"><span data-stu-id="2423d-138">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="add-a-to-do-item"></a><span data-ttu-id="2423d-139">Přidat položku seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="2423d-139">Add a to-do item</span></span>

<span data-ttu-id="2423d-140">V následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="2423d-140">In the following code:</span></span>

* <span data-ttu-id="2423d-141">`item` proměnná je deklarována pro sestavení řetězcové literálové reprezentace položky.</span><span class="sxs-lookup"><span data-stu-id="2423d-141">An `item` variable is declared to construct an object literal representation of the to-do item.</span></span>
* <span data-ttu-id="2423d-142">Požadavek na načtení je nakonfigurovaný s následujícími možnostmi:</span><span class="sxs-lookup"><span data-stu-id="2423d-142">A Fetch request is configured with the following options:</span></span>
  * <span data-ttu-id="2423d-143">`method`&mdash;určuje operaci POST HTTP Action.</span><span class="sxs-lookup"><span data-stu-id="2423d-143">`method`&mdash;specifies the POST HTTP action verb.</span></span>
  * <span data-ttu-id="2423d-144">`body`&mdash;určuje reprezentaci textu žádosti ve formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="2423d-144">`body`&mdash;specifies the JSON representation of the request body.</span></span> <span data-ttu-id="2423d-145">KÓD JSON je vytvořen předáním literálu objektu uloženého v `item` do funkce [JSON. stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) .</span><span class="sxs-lookup"><span data-stu-id="2423d-145">The JSON is produced by passing the object literal stored in `item` to the [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) function.</span></span>
  * <span data-ttu-id="2423d-146">`headers`&mdash;Určuje hlavičku požadavku HTTP `Accept` a `Content-Type`.</span><span class="sxs-lookup"><span data-stu-id="2423d-146">`headers`&mdash;specifies the `Accept` and `Content-Type` HTTP request headers.</span></span> <span data-ttu-id="2423d-147">Obě hlavičky jsou nastavené na `application/json` k určení typu média přijímaného a odeslaného v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="2423d-147">Both headers are set to `application/json` to specify the media type being received and sent, respectively.</span></span>
* <span data-ttu-id="2423d-148">Požadavek HTTP POST se odešle do trasy *API/TodoItems* .</span><span class="sxs-lookup"><span data-stu-id="2423d-148">An HTTP POST request is sent to the *api/TodoItems* route.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

<span data-ttu-id="2423d-149">Když webové rozhraní API vrátí úspěšný kód stavu, vyvolá se funkce `getItems`, která aktualizuje tabulku HTML.</span><span class="sxs-lookup"><span data-stu-id="2423d-149">When the web API returns a successful status code, the `getItems` function is invoked to update the HTML table.</span></span> <span data-ttu-id="2423d-150">Pokud se požadavek webového rozhraní API nepovede, do konzoly prohlížeče se zaprotokoluje chyba.</span><span class="sxs-lookup"><span data-stu-id="2423d-150">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="update-a-to-do-item"></a><span data-ttu-id="2423d-151">Aktualizace položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="2423d-151">Update a to-do item</span></span>

<span data-ttu-id="2423d-152">Aktualizace položky úkolů je podobná přidání. Existují však dva významné rozdíly:</span><span class="sxs-lookup"><span data-stu-id="2423d-152">Updating a to-do item is similar to adding one; however, there are two significant differences:</span></span>

* <span data-ttu-id="2423d-153">Trasa má příponu s jedinečným identifikátorem položky, která se má aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="2423d-153">The route is suffixed with the unique identifier of the item to update.</span></span> <span data-ttu-id="2423d-154">Například *API/TodoItems/1*.</span><span class="sxs-lookup"><span data-stu-id="2423d-154">For example, *api/TodoItems/1*.</span></span>
* <span data-ttu-id="2423d-155">Operace HTTP Action je PUT, jak je uvedeno v možnosti `method`.</span><span class="sxs-lookup"><span data-stu-id="2423d-155">The HTTP action verb is PUT, as indicated by the `method` option.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="2423d-156">Odstranit úkol</span><span class="sxs-lookup"><span data-stu-id="2423d-156">Delete a to-do item</span></span>

<span data-ttu-id="2423d-157">Chcete-li odstranit položku, nastavte možnost `method` požadavku na `DELETE` a v adrese URL zadejte jedinečný identifikátor položky.</span><span class="sxs-lookup"><span data-stu-id="2423d-157">To delete a to-do item, set the request's `method` option to `DELETE` and specify the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

<span data-ttu-id="2423d-158">Přejděte k dalšímu kurzu, kde se dozvíte, jak vygenerovat stránky s usnadněním webového rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="2423d-158">Advance to the next tutorial to learn how to generate web API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

::: moniker-end

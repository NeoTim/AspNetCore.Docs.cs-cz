---
title: 'Kurz: Volání webového rozhraní API ASP.NET Core pomocí JavaScriptu'
author: rick-anderson
description: Přečtěte si, jak volat ASP.NET základní webové rozhraní API pomocí JavaScriptu.
ms.author: riande
ms.custom: mvc
ms.date: 11/26/2019
uid: tutorials/web-api-javascript
ms.openlocfilehash: 2a19a7d16ca8b8f5d6ac8eb99ad919b89f1e368b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78655251"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a><span data-ttu-id="6b224-103">Kurz: Volání webového rozhraní API ASP.NET Core pomocí JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="6b224-103">Tutorial: Call an ASP.NET Core web API with JavaScript</span></span>

<span data-ttu-id="6b224-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6b224-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6b224-105">Tento kurz ukazuje, jak volat ASP.NET základní webové rozhraní API s JavaScriptem pomocí [načíst rozhraní API](https://developer.mozilla.org/docs/Web/API/Fetch_API).</span><span class="sxs-lookup"><span data-stu-id="6b224-105">This tutorial shows how to call an ASP.NET Core web API with JavaScript, using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API).</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6b224-106">Informace ASP.NET Core 2.2 naleznete v 2.2 verzi [volání webového rozhraní API pomocí JavaScriptu](xref:tutorials/first-web-api#call-the-web-api-with-javascript).</span><span class="sxs-lookup"><span data-stu-id="6b224-106">For ASP.NET Core 2.2, see the 2.2 version of [Call the web API with JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="6b224-107">Požadavky</span><span class="sxs-lookup"><span data-stu-id="6b224-107">Prerequisites</span></span>

* <span data-ttu-id="6b224-108">Kompletní [kurz: Vytvoření webového rozhraní API](xref:tutorials/first-web-api)</span><span class="sxs-lookup"><span data-stu-id="6b224-108">Complete [Tutorial: Create a web API](xref:tutorials/first-web-api)</span></span>
* <span data-ttu-id="6b224-109">Znalost CSS, HTML a JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="6b224-109">Familiarity with CSS, HTML, and JavaScript</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="6b224-110">Volání webového rozhraní API pomocí JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="6b224-110">Call the web API with JavaScript</span></span>

<span data-ttu-id="6b224-111">V této části přidáte stránku HTML obsahující formuláře pro vytváření a správu položek.</span><span class="sxs-lookup"><span data-stu-id="6b224-111">In this section, you'll add an HTML page containing forms for creating and managing to-do items.</span></span> <span data-ttu-id="6b224-112">Obslužné rutiny událostí jsou připojeny k prvkům na stránce.</span><span class="sxs-lookup"><span data-stu-id="6b224-112">Event handlers are attached to elements on the page.</span></span> <span data-ttu-id="6b224-113">Výsledkem obslužných rutin událostí jsou požadavky HTTP pro metody akce webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="6b224-113">The event handlers result in HTTP requests to the web API's action methods.</span></span> <span data-ttu-id="6b224-114">`fetch` Funkce rozhraní Fetch API iniciuje každý požadavek HTTP.</span><span class="sxs-lookup"><span data-stu-id="6b224-114">The Fetch API's `fetch` function initiates each HTTP request.</span></span>

<span data-ttu-id="6b224-115">Funkce `fetch` vrátí objekt [Promise,](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) který obsahuje odpověď `Response` HTTP reprezentovanou jako objekt.</span><span class="sxs-lookup"><span data-stu-id="6b224-115">The `fetch` function returns a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) object, which contains an HTTP response represented as a `Response` object.</span></span> <span data-ttu-id="6b224-116">Běžným vzorem je extrahovat tělo odezvy JSON vyvoláním `json` funkce na objektu. `Response`</span><span class="sxs-lookup"><span data-stu-id="6b224-116">A common pattern is to extract the JSON response body by invoking the `json` function on the `Response` object.</span></span> <span data-ttu-id="6b224-117">JavaScript aktualizuje stránku podrobnostmi z odpovědi webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="6b224-117">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="6b224-118">Nejjednodušší `fetch` volání přijme jeden parametr představující trasu.</span><span class="sxs-lookup"><span data-stu-id="6b224-118">The simplest `fetch` call accepts a single parameter representing the route.</span></span> <span data-ttu-id="6b224-119">Druhý parametr, označovaný `init` jako objekt, je volitelný.</span><span class="sxs-lookup"><span data-stu-id="6b224-119">A second parameter, known as the `init` object, is optional.</span></span> <span data-ttu-id="6b224-120">`init`slouží ke konfiguraci požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="6b224-120">`init` is used to configure the HTTP request.</span></span>

1. <span data-ttu-id="6b224-121">Nakonfigurujte aplikaci tak, aby [zobrazovala statické soubory](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a [povolila výchozí mapování souborů](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="6b224-121">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="6b224-122">Následující zvýrazněný kód je `Configure` potřeba v metodě *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="6b224-122">The following highlighted code is needed in the `Configure` method of *Startup.cs*:</span></span>

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. <span data-ttu-id="6b224-123">Vytvořte složku *wwwroot* v kořenovém adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="6b224-123">Create a *wwwroot* folder in the project root.</span></span>

1. <span data-ttu-id="6b224-124">Vytvořte složku *js* uvnitř složky *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="6b224-124">Create a *js* folder inside of the *wwwroot* folder.</span></span>

1. <span data-ttu-id="6b224-125">Přidejte soubor HTML s názvem *index.html* do složky *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="6b224-125">Add an HTML file named *index.html* to the *wwwroot* folder.</span></span> <span data-ttu-id="6b224-126">Nahraďte obsah *souboru index.html* následujícími značkami:</span><span class="sxs-lookup"><span data-stu-id="6b224-126">Replace the contents of *index.html* with the following markup:</span></span>

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. <span data-ttu-id="6b224-127">Přidejte soubor JavaScript s názvem *site.js* do složky *wwwroot/js.*</span><span class="sxs-lookup"><span data-stu-id="6b224-127">Add a JavaScript file named *site.js* to the *wwwroot/js* folder.</span></span> <span data-ttu-id="6b224-128">Nahraďte obsah souboru *site.js* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="6b224-128">Replace the contents of *site.js* with the following code:</span></span>

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

<span data-ttu-id="6b224-129">Ke místnímu testování stránky HTML může být nutná změna nastavení spuštění projektu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="6b224-129">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

1. <span data-ttu-id="6b224-130">*Spusťte položku Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="6b224-130">Open *Properties\launchSettings.json*.</span></span>
1. <span data-ttu-id="6b224-131">Odeberte `launchUrl` vlastnost, chcete-li aplikaci vynutit otevření na *adrese index.html*&mdash;výchozího souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="6b224-131">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="6b224-132">Tato ukázka volá všechny metody CRUD webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="6b224-132">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="6b224-133">Následují vysvětlení požadavků webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="6b224-133">Following are explanations of the web API requests.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="6b224-134">Získání seznamu položek úkolů</span><span class="sxs-lookup"><span data-stu-id="6b224-134">Get a list of to-do items</span></span>

<span data-ttu-id="6b224-135">V následujícím kódu je na trasu *API/TodoItems* odeslán požadavek HTTP GET:</span><span class="sxs-lookup"><span data-stu-id="6b224-135">In the following code, an HTTP GET request is sent to the *api/TodoItems* route:</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

<span data-ttu-id="6b224-136">Když webové rozhraní API vrátí úspěšný `_displayItems` stavový kód, funkce je vyvolána.</span><span class="sxs-lookup"><span data-stu-id="6b224-136">When the web API returns a successful status code, the `_displayItems` function is invoked.</span></span> <span data-ttu-id="6b224-137">Každá položka soupou v `_displayItems` parametru pole přijatá tlačítkem Je přidána do tabulky pomocí tlačítek **Upravit** a **Odstranit.**</span><span class="sxs-lookup"><span data-stu-id="6b224-137">Each to-do item in the array parameter accepted by `_displayItems` is added to a table with **Edit** and **Delete** buttons.</span></span> <span data-ttu-id="6b224-138">Pokud se požadavek webového rozhraní API nezdaří, je do konzoly prohlížeče zaznamenána chyba.</span><span class="sxs-lookup"><span data-stu-id="6b224-138">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="add-a-to-do-item"></a><span data-ttu-id="6b224-139">Přidání položky s cílem</span><span class="sxs-lookup"><span data-stu-id="6b224-139">Add a to-do item</span></span>

<span data-ttu-id="6b224-140">V následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="6b224-140">In the following code:</span></span>

* <span data-ttu-id="6b224-141">Proměnná `item` je deklarována k vytvoření literálové reprezentace objektu položky, kterou chcete provést.</span><span class="sxs-lookup"><span data-stu-id="6b224-141">An `item` variable is declared to construct an object literal representation of the to-do item.</span></span>
* <span data-ttu-id="6b224-142">Požadavek na načtení je nakonfigurován s následujícími možnostmi:</span><span class="sxs-lookup"><span data-stu-id="6b224-142">A Fetch request is configured with the following options:</span></span>
  * <span data-ttu-id="6b224-143">`method`&mdash;určuje sloveso akce POST HTTP.</span><span class="sxs-lookup"><span data-stu-id="6b224-143">`method`&mdash;specifies the POST HTTP action verb.</span></span>
  * <span data-ttu-id="6b224-144">`body`&mdash;určuje reprezentaci JSON těla požadavku.</span><span class="sxs-lookup"><span data-stu-id="6b224-144">`body`&mdash;specifies the JSON representation of the request body.</span></span> <span data-ttu-id="6b224-145">JSON je vytvořen předáním literálu `item` objektu uloženého v [json.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) funkce.</span><span class="sxs-lookup"><span data-stu-id="6b224-145">The JSON is produced by passing the object literal stored in `item` to the [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) function.</span></span>
  * <span data-ttu-id="6b224-146">`headers`&mdash;určuje `Accept` hlavičky `Content-Type` požadavků a http.</span><span class="sxs-lookup"><span data-stu-id="6b224-146">`headers`&mdash;specifies the `Accept` and `Content-Type` HTTP request headers.</span></span> <span data-ttu-id="6b224-147">Obě záhlaví jsou `application/json` nastavena tak, aby určovat typ média přijímány a odesílané, v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="6b224-147">Both headers are set to `application/json` to specify the media type being received and sent, respectively.</span></span>
* <span data-ttu-id="6b224-148">Požadavek HTTP POST je odeslán na trasu *api/TodoItems.*</span><span class="sxs-lookup"><span data-stu-id="6b224-148">An HTTP POST request is sent to the *api/TodoItems* route.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

<span data-ttu-id="6b224-149">Pokud webové rozhraní API vrátí úspěšný `getItems` stavový kód, funkce je vyvolána k aktualizaci tabulky HTML.</span><span class="sxs-lookup"><span data-stu-id="6b224-149">When the web API returns a successful status code, the `getItems` function is invoked to update the HTML table.</span></span> <span data-ttu-id="6b224-150">Pokud se požadavek webového rozhraní API nezdaří, je do konzoly prohlížeče zaznamenána chyba.</span><span class="sxs-lookup"><span data-stu-id="6b224-150">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="update-a-to-do-item"></a><span data-ttu-id="6b224-151">Aktualizace položky s cílem</span><span class="sxs-lookup"><span data-stu-id="6b224-151">Update a to-do item</span></span>

<span data-ttu-id="6b224-152">Aktualizace položky s cílem je podobná přidání položky; existují však dva významné rozdíly:</span><span class="sxs-lookup"><span data-stu-id="6b224-152">Updating a to-do item is similar to adding one; however, there are two significant differences:</span></span>

* <span data-ttu-id="6b224-153">Trasa je připnout s jedinečným identifikátorem položky k aktualizaci.</span><span class="sxs-lookup"><span data-stu-id="6b224-153">The route is suffixed with the unique identifier of the item to update.</span></span> <span data-ttu-id="6b224-154">Například *api/TodoItems/1*.</span><span class="sxs-lookup"><span data-stu-id="6b224-154">For example, *api/TodoItems/1*.</span></span>
* <span data-ttu-id="6b224-155">Sloveso akce HTTP je PUT, `method` jak je uvedeno v možnosti.</span><span class="sxs-lookup"><span data-stu-id="6b224-155">The HTTP action verb is PUT, as indicated by the `method` option.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="6b224-156">Odstranění položky pro práci</span><span class="sxs-lookup"><span data-stu-id="6b224-156">Delete a to-do item</span></span>

<span data-ttu-id="6b224-157">Chcete-li odstranit položku pro zadání, `method` nastavte `DELETE` možnost požadavku na jedinečný identifikátor položky v adrese URL a zadejte její jedinečný identifikátor.</span><span class="sxs-lookup"><span data-stu-id="6b224-157">To delete a to-do item, set the request's `method` option to `DELETE` and specify the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

<span data-ttu-id="6b224-158">Přejdete k dalšímu kurzu, kde se dozvíte, jak generovat stránky nápovědy webového rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="6b224-158">Advance to the next tutorial to learn how to generate web API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

::: moniker-end

---
title: 'Kurz: volání ASP.NET Core webového rozhraní API pomocí JavaScriptu'
author: rick-anderson
description: Naučte se volat ASP.NET Core webového rozhraní API pomocí JavaScriptu.
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: tutorials/web-api-javascript
ms.openlocfilehash: bbe261307f6f68af002cb98cc4895888ade7f61c
ms.sourcegitcommit: dd026eceee79e943bd6b4a37b144803b50617583
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72378707"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a><span data-ttu-id="53ed5-103">Kurz: volání ASP.NET Core webového rozhraní API pomocí JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="53ed5-103">Tutorial: Call an ASP.NET Core web API with JavaScript</span></span>

<span data-ttu-id="53ed5-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="53ed5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="53ed5-105">V tomto kurzu se dozvíte, jak volat ASP.NET Core webového rozhraní API s JavaScriptem pomocí [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API).</span><span class="sxs-lookup"><span data-stu-id="53ed5-105">This tutorial shows how to call an ASP.NET Core web API with JavaScript, using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="53ed5-106">Požadavky</span><span class="sxs-lookup"><span data-stu-id="53ed5-106">Prerequisites</span></span>

* <span data-ttu-id="53ed5-107">Úplný [kurz: Vytvoření webového rozhraní API](xref:tutorials/first-web-api)</span><span class="sxs-lookup"><span data-stu-id="53ed5-107">Complete [Tutorial: Create a web API](xref:tutorials/first-web-api)</span></span>
* <span data-ttu-id="53ed5-108">Znalost šablon stylů CSS, HTML a JavaScript</span><span class="sxs-lookup"><span data-stu-id="53ed5-108">Familiarity with CSS, HTML, and JavaScript</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="53ed5-109">Volání webového rozhraní API pomocí JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="53ed5-109">Call the web API with JavaScript</span></span>

<span data-ttu-id="53ed5-110">V této části přidáte stránku HTML obsahující formuláře pro vytváření a správu položek úkolů.</span><span class="sxs-lookup"><span data-stu-id="53ed5-110">In this section, you'll add an HTML page containing forms for creating and managing to-do items.</span></span> <span data-ttu-id="53ed5-111">Obslužné rutiny událostí jsou připojeny k prvkům na stránce.</span><span class="sxs-lookup"><span data-stu-id="53ed5-111">Event handlers are attached to elements on the page.</span></span> <span data-ttu-id="53ed5-112">Obslužné rutiny událostí mají za následek požadavky HTTP na metody akcí webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="53ed5-112">The event handlers result in HTTP requests to the web API's action methods.</span></span> <span data-ttu-id="53ed5-113">Funkce `fetch` rozhraní API pro načtení inicializuje jednotlivé požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="53ed5-113">The Fetch API's `fetch` function initiates each HTTP request.</span></span>

<span data-ttu-id="53ed5-114">Funkce `fetch` vrátí objekt `Promise`, který obsahuje odpověď HTTP reprezentovanou jako objekt `Response`.</span><span class="sxs-lookup"><span data-stu-id="53ed5-114">The `fetch` function returns a `Promise` object, which contains an HTTP response represented as a `Response` object.</span></span> <span data-ttu-id="53ed5-115">Běžným vzorem je extrakce textu odpovědi JSON vyvoláním funkce `json` u objektu `Response`.</span><span class="sxs-lookup"><span data-stu-id="53ed5-115">A common pattern is to extract the JSON response body by invoking the `json` function on the `Response` object.</span></span> <span data-ttu-id="53ed5-116">JavaScript aktualizuje stránku s podrobnostmi z odpovědi webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="53ed5-116">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="53ed5-117">Nejjednodušší volání `fetch` přijímá jeden parametr reprezentující trasu.</span><span class="sxs-lookup"><span data-stu-id="53ed5-117">The simplest `fetch` call accepts a single parameter representing the route.</span></span> <span data-ttu-id="53ed5-118">Druhý parametr, označovaný jako objekt `init`, je volitelný.</span><span class="sxs-lookup"><span data-stu-id="53ed5-118">A second parameter, known as the `init` object, is optional.</span></span> <span data-ttu-id="53ed5-119">pro konfiguraci požadavku HTTP se používá `init`.</span><span class="sxs-lookup"><span data-stu-id="53ed5-119">`init` is used to configure the HTTP request.</span></span>

1. <span data-ttu-id="53ed5-120">Nakonfigurujte aplikaci tak, aby [sloužila statickým souborům](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a [povolovala výchozí mapování souborů](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="53ed5-120">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="53ed5-121">Následující zvýrazněný kód je vyžadován v metodě `Configure` *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="53ed5-121">The following highlighted code is needed in the `Configure` method of *Startup.cs*:</span></span>

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. <span data-ttu-id="53ed5-122">V kořenovém adresáři projektu vytvořte adresář *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="53ed5-122">Create a *wwwroot* directory in the project root.</span></span>

1. <span data-ttu-id="53ed5-123">Do adresáře *wwwroot* přidejte soubor HTML s názvem *index. html* .</span><span class="sxs-lookup"><span data-stu-id="53ed5-123">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="53ed5-124">Nahraďte jeho obsah následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="53ed5-124">Replace its contents with the following markup:</span></span>

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. <span data-ttu-id="53ed5-125">Do adresáře *wwwroot* přidejte soubor JavaScriptu s názvem *Web. js* .</span><span class="sxs-lookup"><span data-stu-id="53ed5-125">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="53ed5-126">Nahraďte jeho obsah následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="53ed5-126">Replace its contents with the following code:</span></span>

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

<span data-ttu-id="53ed5-127">Pro místní testování stránky HTML může být nutné změnit nastavení spouštění ASP.NET Core projektu:</span><span class="sxs-lookup"><span data-stu-id="53ed5-127">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

1. <span data-ttu-id="53ed5-128">Otevřete *Properties\launchSettings.JSON*.</span><span class="sxs-lookup"><span data-stu-id="53ed5-128">Open *Properties\launchSettings.json*.</span></span>
1. <span data-ttu-id="53ed5-129">Odebráním vlastnosti `launchUrl` vynutíte otevření aplikace v *indexu. html*@no__t – výchozí soubor projektu 2the.</span><span class="sxs-lookup"><span data-stu-id="53ed5-129">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="53ed5-130">Tato ukázka volá všechny metody CRUD webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="53ed5-130">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="53ed5-131">Následují vysvětlení požadavků webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="53ed5-131">Following are explanations of the web API requests.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="53ed5-132">Získat seznam úkolů</span><span class="sxs-lookup"><span data-stu-id="53ed5-132">Get a list of to-do items</span></span>

<span data-ttu-id="53ed5-133">V následujícím kódu se pošle požadavek HTTP GET do trasy *API/TodoItems* :</span><span class="sxs-lookup"><span data-stu-id="53ed5-133">In the following code, an HTTP GET request is sent to the *api/TodoItems* route:</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

<span data-ttu-id="53ed5-134">Když webové rozhraní API vrátí úspěšný kód stavu, vyvolá se funkce `_displayItems`.</span><span class="sxs-lookup"><span data-stu-id="53ed5-134">When the web API returns a successful status code, the `_displayItems` function is invoked.</span></span> <span data-ttu-id="53ed5-135">Každá položka úkolů v parametru pole přijatém `_displayItems` je přidána do tabulky s tlačítky **Upravit** a **Odstranit** .</span><span class="sxs-lookup"><span data-stu-id="53ed5-135">Each to-do item in the array parameter accepted by `_displayItems` is added to a table with **Edit** and **Delete** buttons.</span></span> <span data-ttu-id="53ed5-136">Pokud se požadavek webového rozhraní API nepovede, do konzoly prohlížeče se zaprotokoluje chyba.</span><span class="sxs-lookup"><span data-stu-id="53ed5-136">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="add-a-to-do-item"></a><span data-ttu-id="53ed5-137">Přidat položku úkolů</span><span class="sxs-lookup"><span data-stu-id="53ed5-137">Add a to-do item</span></span>

<span data-ttu-id="53ed5-138">V následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="53ed5-138">In the following code:</span></span>

* <span data-ttu-id="53ed5-139">Proměnná `item` je deklarována k sestavení řetězcové literálové reprezentace položky.</span><span class="sxs-lookup"><span data-stu-id="53ed5-139">An `item` variable is declared to construct an object literal representation of the to-do item.</span></span>
* <span data-ttu-id="53ed5-140">Požadavek na načtení je nakonfigurovaný s následujícími možnostmi:</span><span class="sxs-lookup"><span data-stu-id="53ed5-140">A Fetch request is configured with the following options:</span></span>
  * <span data-ttu-id="53ed5-141">`method` @ no__t-1specifies příkaz POST HTTP Action.</span><span class="sxs-lookup"><span data-stu-id="53ed5-141">`method`&mdash;specifies the POST HTTP action verb.</span></span>
  * <span data-ttu-id="53ed5-142">`body` @ no__t-1specifies reprezentace textu žádosti ve formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="53ed5-142">`body`&mdash;specifies the JSON representation of the request body.</span></span> <span data-ttu-id="53ed5-143">KÓD JSON je vytvořen předáním literálu objektu uloženého v `item` do funkce [JSON. stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) .</span><span class="sxs-lookup"><span data-stu-id="53ed5-143">The JSON is produced by passing the object literal stored in `item` to the [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) function.</span></span>
  * <span data-ttu-id="53ed5-144">`headers` @ no__t-1specifies hlavičky žádosti HTTP `Accept` a `Content-Type`.</span><span class="sxs-lookup"><span data-stu-id="53ed5-144">`headers`&mdash;specifies the `Accept` and `Content-Type` HTTP request headers.</span></span> <span data-ttu-id="53ed5-145">Obě hlavičky jsou nastavené na `application/json` a určují typ média, který se přijímá a pošle v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="53ed5-145">Both headers are set to `application/json` to specify the media type being received and sent, respectively.</span></span>
* <span data-ttu-id="53ed5-146">Požadavek HTTP POST se odešle do trasy *API/TodoItems* .</span><span class="sxs-lookup"><span data-stu-id="53ed5-146">An HTTP POST request is sent to the *api/TodoItems* route.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

<span data-ttu-id="53ed5-147">Když webové rozhraní API vrátí úspěšný stavový kód, vyvolá se funkce `getItems`, aby se aktualizovala tabulka HTML.</span><span class="sxs-lookup"><span data-stu-id="53ed5-147">When the web API returns a successful status code, the `getItems` function is invoked to update the HTML table.</span></span> <span data-ttu-id="53ed5-148">Pokud se požadavek webového rozhraní API nepovede, do konzoly prohlížeče se zaprotokoluje chyba.</span><span class="sxs-lookup"><span data-stu-id="53ed5-148">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="update-a-to-do-item"></a><span data-ttu-id="53ed5-149">Aktualizace položky úkolů</span><span class="sxs-lookup"><span data-stu-id="53ed5-149">Update a to-do item</span></span>

<span data-ttu-id="53ed5-150">Aktualizace položky úkolů je podobná přidání. Existují však dva významné rozdíly:</span><span class="sxs-lookup"><span data-stu-id="53ed5-150">Updating a to-do item is similar to adding one; however, there are two significant differences:</span></span>

* <span data-ttu-id="53ed5-151">Trasa má příponu s jedinečným identifikátorem položky, která se má aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="53ed5-151">The route is suffixed with the unique identifier of the item to update.</span></span> <span data-ttu-id="53ed5-152">Například *API/TodoItems/1*.</span><span class="sxs-lookup"><span data-stu-id="53ed5-152">For example, *api/TodoItems/1*.</span></span>
* <span data-ttu-id="53ed5-153">Operace HTTP Action je PUT, jak je uvedeno v možnosti `method`.</span><span class="sxs-lookup"><span data-stu-id="53ed5-153">The HTTP action verb is PUT, as indicated by the `method` option.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="53ed5-154">Odstranění položky úkolů</span><span class="sxs-lookup"><span data-stu-id="53ed5-154">Delete a to-do item</span></span>

<span data-ttu-id="53ed5-155">Chcete-li odstranit položku, nastavte možnost `method` žádosti na hodnotu `DELETE` a v adrese URL zadejte jedinečný identifikátor položky.</span><span class="sxs-lookup"><span data-stu-id="53ed5-155">To delete a to-do item, set the request's `method` option to `DELETE` and specify the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

<span data-ttu-id="53ed5-156">Přejděte k dalšímu kurzu, kde se dozvíte, jak vygenerovat stránky s usnadněním webového rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="53ed5-156">Advance to the next tutorial to learn how to generate web API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

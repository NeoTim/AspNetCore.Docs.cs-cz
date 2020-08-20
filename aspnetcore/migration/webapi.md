---
title: Migrace z webového rozhraní API ASP.NET do ASP.NET Core
author: ardalis
description: Naučte se migrovat implementaci webového rozhraní API z webového rozhraní API ASP.NET 4. x na ASP.NET Core MVC.
ms.author: scaddie
ms.custom: mvc
ms.date: 05/26/2020
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
uid: migration/webapi
ms.openlocfilehash: e3e46f8050ba87c3108885341675c9d2a2cb7847
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635161"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a><span data-ttu-id="939e6-103">Migrace z webového rozhraní API ASP.NET do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="939e6-103">Migrate from ASP.NET Web API to ASP.NET Core</span></span>

<span data-ttu-id="939e6-104">[Scottem Addie](https://twitter.com/scott_addie) a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="939e6-104">By [Scott Addie](https://twitter.com/scott_addie) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="939e6-105">Webové rozhraní API ASP.NET 4. x je služba HTTP, která dosahuje široké škály klientů, včetně prohlížečů a mobilních zařízení.</span><span class="sxs-lookup"><span data-stu-id="939e6-105">An ASP.NET 4.x Web API is an HTTP service that reaches a broad range of clients, including browsers and mobile devices.</span></span> <span data-ttu-id="939e6-106">ASP.NET Core kombinuje modely MVC a webové rozhraní API ASP.NET 4. x do jednoho programovacího modelu známého jako ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="939e6-106">ASP.NET Core combines ASP.NET 4.x's MVC and Web API app models into a single programming model known as ASP.NET Core MVC.</span></span> <span data-ttu-id="939e6-107">Tento článek popisuje kroky potřebné k migraci z webového rozhraní API ASP.NET 4. x na ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="939e6-107">This article demonstrates the steps required to migrate from ASP.NET 4.x Web API to ASP.NET Core MVC.</span></span>

<span data-ttu-id="939e6-108">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="939e6-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="939e6-109">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="939e6-109">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="939e6-110">Kontrola projektu webového rozhraní API ASP.NET 4. x</span><span class="sxs-lookup"><span data-stu-id="939e6-110">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="939e6-111">Tento článek používá projekt *ProductsApp* vytvořený v [Začínáme s webovým rozhraním API 2 pro ASP.NET](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span><span class="sxs-lookup"><span data-stu-id="939e6-111">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="939e6-112">V tomto projektu je základní projekt webového rozhraní API ASP.NET 4. x nakonfigurovaný takto.</span><span class="sxs-lookup"><span data-stu-id="939e6-112">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="939e6-113">V *Global.asax.cs*je volání provedeno na `WebApiConfig.Register` :</span><span class="sxs-lookup"><span data-stu-id="939e6-113">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="939e6-114">`WebApiConfig`Třída se nachází ve složce *app_start* a má statickou `Register` metodu:</span><span class="sxs-lookup"><span data-stu-id="939e6-114">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="939e6-115">Předchozí třída:</span><span class="sxs-lookup"><span data-stu-id="939e6-115">The preceding class:</span></span>

* <span data-ttu-id="939e6-116">Konfiguruje [Směrování atributů](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), i když se ve skutečnosti nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="939e6-116">Configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used.</span></span>
* <span data-ttu-id="939e6-117">Nakonfiguruje směrovací tabulku.</span><span class="sxs-lookup"><span data-stu-id="939e6-117">Configures the routing table.</span></span>
<span data-ttu-id="939e6-118">Vzorový kód očekává, že adresy URL budou odpovídat formátu `/api/{controller}/{id}` a `{id}` jsou volitelné.</span><span class="sxs-lookup"><span data-stu-id="939e6-118">The sample code expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="939e6-119">Následující části demonstrují migraci projektu webového rozhraní API do ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="939e6-119">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="939e6-120">Vytvořit cílový projekt</span><span class="sxs-lookup"><span data-stu-id="939e6-120">Create the destination project</span></span>

<span data-ttu-id="939e6-121">Vytvořte nové prázdné řešení v aplikaci Visual Studio a přidejte projekt webového rozhraní API ASP.NET 4. x k migraci:</span><span class="sxs-lookup"><span data-stu-id="939e6-121">Create a new blank solution in Visual Studio and add the ASP.NET 4.x Web API project to migrate:</span></span>

1. <span data-ttu-id="939e6-122">V nabídce **soubor** vyberte možnost **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="939e6-122">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="939e6-123">Vyberte šablonu **prázdná řešení** a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="939e6-123">Select the **Blank Solution** template and select **Next**.</span></span>
1. <span data-ttu-id="939e6-124">Pojmenujte řešení *WebAPIMigration*.</span><span class="sxs-lookup"><span data-stu-id="939e6-124">Name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="939e6-125">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="939e6-125">Select **Create**.</span></span>
1. <span data-ttu-id="939e6-126">Přidejte existující projekt *ProductsApp* do řešení.</span><span class="sxs-lookup"><span data-stu-id="939e6-126">Add the existing *ProductsApp* project to the solution.</span></span>

<span data-ttu-id="939e6-127">Přidejte nový projekt API, který se má migrovat na:</span><span class="sxs-lookup"><span data-stu-id="939e6-127">Add a new API project to migrate to:</span></span>

1. <span data-ttu-id="939e6-128">Přidejte do řešení nový projekt **webové aplikace ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="939e6-128">Add a new **ASP.NET Core Web Application** project to the solution.</span></span>
1. <span data-ttu-id="939e6-129">V dialogovém okně **Konfigurovat nový projekt** pojmenujte projekt *ProductsCore*a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="939e6-129">In the **Configure your new project** dialog, Name the project *ProductsCore*, and select **Create**.</span></span>
1. <span data-ttu-id="939e6-130">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 3,1** .</span><span class="sxs-lookup"><span data-stu-id="939e6-130">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="939e6-131">Vyberte šablonu projektu **rozhraní API** a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="939e6-131">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="939e6-132">Odeberte příklady souborů *WeatherForecast.cs* a *Controllers/WeatherForecastController. cs* z nového projektu *ProductsCore* .</span><span class="sxs-lookup"><span data-stu-id="939e6-132">Remove the *WeatherForecast.cs* and *Controllers/WeatherForecastController.cs* example files from the new *ProductsCore* project.</span></span>

<span data-ttu-id="939e6-133">Řešení teď obsahuje dva projekty.</span><span class="sxs-lookup"><span data-stu-id="939e6-133">The solution now contains two projects.</span></span> <span data-ttu-id="939e6-134">Následující části vysvětlují migraci obsahu projektu *ProductsApp* do projektu *ProductsCore* .</span><span class="sxs-lookup"><span data-stu-id="939e6-134">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="939e6-135">Migrace konfigurace</span><span class="sxs-lookup"><span data-stu-id="939e6-135">Migrate configuration</span></span>

<span data-ttu-id="939e6-136">ASP.NET Core nepoužívá složku *app_start* nebo soubor *Global. asax* .</span><span class="sxs-lookup"><span data-stu-id="939e6-136">ASP.NET Core doesn't use the *App_Start* folder or the *Global.asax* file.</span></span> <span data-ttu-id="939e6-137">Navíc se soubor *web.config* přidá v době publikování.</span><span class="sxs-lookup"><span data-stu-id="939e6-137">Additionally, the *web.config* file is added at publish time.</span></span>

<span data-ttu-id="939e6-138">Třída `Startup`:</span><span class="sxs-lookup"><span data-stu-id="939e6-138">The `Startup` class:</span></span>

* <span data-ttu-id="939e6-139">Nahradí *Global. asax*.</span><span class="sxs-lookup"><span data-stu-id="939e6-139">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="939e6-140">Zpracuje všechny úlohy při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="939e6-140">Handles all app startup tasks.</span></span>

<span data-ttu-id="939e6-141">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="939e6-141">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="939e6-142">Migrace modelů a řadičů</span><span class="sxs-lookup"><span data-stu-id="939e6-142">Migrate models and controllers</span></span>

<span data-ttu-id="939e6-143">Následující kód ukazuje, že `ProductsController` má být aktualizován pro ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="939e6-143">The following code shows the `ProductsController` to be updated for ASP.NET Core:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Controllers/ProductsController.cs)]

<span data-ttu-id="939e6-144">Aktualizujte `ProductsController` ASP.NET Core pro:</span><span class="sxs-lookup"><span data-stu-id="939e6-144">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="939e6-145">Zkopírujte *Controllers/ProductsController. cs* a složku *modely* z původního projektu na nový.</span><span class="sxs-lookup"><span data-stu-id="939e6-145">Copy *Controllers/ProductsController.cs* and the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="939e6-146">Změňte kořenový obor názvů zkopírovaných souborů na `ProductsCore` .</span><span class="sxs-lookup"><span data-stu-id="939e6-146">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="939e6-147">Aktualizujte `using ProductsApp.Models;` příkaz na `using ProductsCore.Models;` .</span><span class="sxs-lookup"><span data-stu-id="939e6-147">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="939e6-148">Následující komponenty v ASP.NET Core neexistují:</span><span class="sxs-lookup"><span data-stu-id="939e6-148">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="939e6-149">Třída `ApiController`</span><span class="sxs-lookup"><span data-stu-id="939e6-149">`ApiController` class</span></span>
* <span data-ttu-id="939e6-150">`System.Web.Http` hosting</span><span class="sxs-lookup"><span data-stu-id="939e6-150">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="939e6-151">`IHttpActionResult` prostředí</span><span class="sxs-lookup"><span data-stu-id="939e6-151">`IHttpActionResult` interface</span></span>

<span data-ttu-id="939e6-152">Proveďte následující změny:</span><span class="sxs-lookup"><span data-stu-id="939e6-152">Make the following changes:</span></span>

1. <span data-ttu-id="939e6-153">Změňte `ApiController` na <xref:Microsoft.AspNetCore.Mvc.ControllerBase> .</span><span class="sxs-lookup"><span data-stu-id="939e6-153">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="939e6-154">Přidejte `using Microsoft.AspNetCore.Mvc;` pro vyřešení `ControllerBase` odkazu.</span><span class="sxs-lookup"><span data-stu-id="939e6-154">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="939e6-155">Odstraňte `using System.Web.Http;`.</span><span class="sxs-lookup"><span data-stu-id="939e6-155">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="939e6-156">Změňte `GetProduct` návratový typ akce z `IHttpActionResult` na `ActionResult<Product>` .</span><span class="sxs-lookup"><span data-stu-id="939e6-156">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="939e6-157">Zjednodušte `GetProduct` příkaz akce `return` následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="939e6-157">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="939e6-158">Konfigurace směrování</span><span class="sxs-lookup"><span data-stu-id="939e6-158">Configure routing</span></span>

<span data-ttu-id="939e6-159">Šablona projektu ASP.NET Core *API* zahrnuje konfiguraci směrování koncových bodů ve vygenerovaném kódu.</span><span class="sxs-lookup"><span data-stu-id="939e6-159">The ASP.NET Core *API* project template includes endpoint routing configuration in the generated code.</span></span>

<span data-ttu-id="939e6-160">Následující <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> a <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> volání:</span><span class="sxs-lookup"><span data-stu-id="939e6-160">The following <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> calls:</span></span>

* <span data-ttu-id="939e6-161">Zaregistrujte spárování tras a spuštění koncového bodu v kanálu [middlewaru](xref:fundamentals/middleware/index) .</span><span class="sxs-lookup"><span data-stu-id="939e6-161">Register route matching and endpoint execution in the [middleware](xref:fundamentals/middleware/index) pipeline.</span></span>
* <span data-ttu-id="939e6-162">Nahraďte *app_start souboru/webapiconfig.cs* projektu *ProductsApp* .</span><span class="sxs-lookup"><span data-stu-id="939e6-162">Replace the *ProductsApp* project's *App_Start/WebApiConfig.cs* file.</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=10,14)]

<span data-ttu-id="939e6-163">Nakonfigurujte směrování následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="939e6-163">Configure routing as follows:</span></span>

1. <span data-ttu-id="939e6-164">Označte `ProductsController` třídu následujícími atributy:</span><span class="sxs-lookup"><span data-stu-id="939e6-164">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="939e6-165">Předchozí [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) atribut nakonfiguruje vzor směrování atributů řadiče.</span><span class="sxs-lookup"><span data-stu-id="939e6-165">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="939e6-166">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)Atribut dělá směrování požadavku u všech akcí v tomto kontroleru.</span><span class="sxs-lookup"><span data-stu-id="939e6-166">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="939e6-167">Směrování atributů podporuje tokeny, jako například `[controller]` a `[action]` .</span><span class="sxs-lookup"><span data-stu-id="939e6-167">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="939e6-168">Za běhu je každý token nahrazen názvem kontroleru nebo akce, v uvedeném pořadí, na který byl atribut použit.</span><span class="sxs-lookup"><span data-stu-id="939e6-168">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="939e6-169">Tokeny:</span><span class="sxs-lookup"><span data-stu-id="939e6-169">The tokens:</span></span>
    * <span data-ttu-id="939e6-170">Snižte počet řetězců Magic v projektu.</span><span class="sxs-lookup"><span data-stu-id="939e6-170">Reduce the number of magic strings in the project.</span></span>
    * <span data-ttu-id="939e6-171">Zajistěte, aby trasy zůstaly synchronizované s odpovídajícími kontroléry a akcemi při použití refaktoringu automatického přejmenování.</span><span class="sxs-lookup"><span data-stu-id="939e6-171">Ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="939e6-172">Povolit požadavky HTTP GET na `ProductController` akce:</span><span class="sxs-lookup"><span data-stu-id="939e6-172">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="939e6-173">Použijte [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atribut pro `GetAllProducts` akci.</span><span class="sxs-lookup"><span data-stu-id="939e6-173">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="939e6-174">Použijte `[HttpGet("{id}")]` atribut pro `GetProduct` akci.</span><span class="sxs-lookup"><span data-stu-id="939e6-174">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="939e6-175">Spusťte migrovaný projekt a přejděte na adresu `/api/products` .</span><span class="sxs-lookup"><span data-stu-id="939e6-175">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="939e6-176">Zobrazí se úplný seznam tří produktů.</span><span class="sxs-lookup"><span data-stu-id="939e6-176">A full list of three products appears.</span></span> <span data-ttu-id="939e6-177">Přejděte na adresu `/api/products/1`.</span><span class="sxs-lookup"><span data-stu-id="939e6-177">Browse to `/api/products/1`.</span></span> <span data-ttu-id="939e6-178">Zobrazí se první produkt.</span><span class="sxs-lookup"><span data-stu-id="939e6-178">The first product appears.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="939e6-179">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="939e6-179">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
## <a name="prerequisites"></a><span data-ttu-id="939e6-180">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="939e6-180">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="939e6-181">Kontrola projektu webového rozhraní API ASP.NET 4. x</span><span class="sxs-lookup"><span data-stu-id="939e6-181">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="939e6-182">Tento článek používá projekt *ProductsApp* vytvořený v [Začínáme s webovým rozhraním API 2 pro ASP.NET](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span><span class="sxs-lookup"><span data-stu-id="939e6-182">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="939e6-183">V tomto projektu je základní projekt webového rozhraní API ASP.NET 4. x nakonfigurovaný takto.</span><span class="sxs-lookup"><span data-stu-id="939e6-183">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="939e6-184">V *Global.asax.cs*je volání provedeno na `WebApiConfig.Register` :</span><span class="sxs-lookup"><span data-stu-id="939e6-184">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="939e6-185">`WebApiConfig`Třída se nachází ve složce *app_start* a má statickou `Register` metodu:</span><span class="sxs-lookup"><span data-stu-id="939e6-185">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="939e6-186">Tato třída konfiguruje [Směrování atributů](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), i když není ve skutečnosti použita v projektu.</span><span class="sxs-lookup"><span data-stu-id="939e6-186">This class configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used in the project.</span></span> <span data-ttu-id="939e6-187">Také nakonfiguruje směrovací tabulku, která je používána webovým rozhraním API ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="939e6-187">It also configures the routing table, which is used by ASP.NET Web API.</span></span> <span data-ttu-id="939e6-188">V takovém případě webový rozhraní API ASP.NET 4. x očekává, že adresy URL odpovídají formátu `/api/{controller}/{id}` a `{id}` jsou volitelné.</span><span class="sxs-lookup"><span data-stu-id="939e6-188">In this case, ASP.NET 4.x Web API expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="939e6-189">Následující části demonstrují migraci projektu webového rozhraní API do ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="939e6-189">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="939e6-190">Vytvořit cílový projekt</span><span class="sxs-lookup"><span data-stu-id="939e6-190">Create the destination project</span></span>

<span data-ttu-id="939e6-191">Proveďte následující kroky v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="939e6-191">Complete the following steps in Visual Studio:</span></span>

* <span data-ttu-id="939e6-192">Přejít na **soubor**  >  **Nový**  >  **projekt**  >  **Další typy projektů**  >  **řešení Visual Studio**.</span><span class="sxs-lookup"><span data-stu-id="939e6-192">Go to **File** > **New** > **Project** > **Other Project Types** > **Visual Studio Solutions**.</span></span> <span data-ttu-id="939e6-193">Vyberte **prázdné řešení**a pojmenujte řešení *WebAPIMigration*.</span><span class="sxs-lookup"><span data-stu-id="939e6-193">Select **Blank Solution**, and name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="939e6-194">Klikněte na tlačítko **OK** .</span><span class="sxs-lookup"><span data-stu-id="939e6-194">Click the **OK** button.</span></span>
* <span data-ttu-id="939e6-195">Přidejte existující projekt *ProductsApp* do řešení.</span><span class="sxs-lookup"><span data-stu-id="939e6-195">Add the existing *ProductsApp* project to the solution.</span></span>
* <span data-ttu-id="939e6-196">Přidejte do řešení nový projekt **webové aplikace ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="939e6-196">Add a new **ASP.NET Core Web Application** project to the solution.</span></span> <span data-ttu-id="939e6-197">V rozevíracím seznamu vyberte cílové rozhraní **.NET Core** a vyberte šablonu projektu **rozhraní API** .</span><span class="sxs-lookup"><span data-stu-id="939e6-197">Select the **.NET Core** target framework from the drop-down, and select the **API** project template.</span></span> <span data-ttu-id="939e6-198">Pojmenujte projekt *ProductsCore*a klikněte na tlačítko **OK** .</span><span class="sxs-lookup"><span data-stu-id="939e6-198">Name the project *ProductsCore*, and click the **OK** button.</span></span>

<span data-ttu-id="939e6-199">Řešení teď obsahuje dva projekty.</span><span class="sxs-lookup"><span data-stu-id="939e6-199">The solution now contains two projects.</span></span> <span data-ttu-id="939e6-200">Následující části vysvětlují migraci obsahu projektu *ProductsApp* do projektu *ProductsCore* .</span><span class="sxs-lookup"><span data-stu-id="939e6-200">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="939e6-201">Migrace konfigurace</span><span class="sxs-lookup"><span data-stu-id="939e6-201">Migrate configuration</span></span>

<span data-ttu-id="939e6-202">ASP.NET Core nepoužívá:</span><span class="sxs-lookup"><span data-stu-id="939e6-202">ASP.NET Core doesn't use:</span></span>

* <span data-ttu-id="939e6-203">*App_start* složka nebo soubor *Global. asax*</span><span class="sxs-lookup"><span data-stu-id="939e6-203">*App_Start* folder or the *Global.asax* file</span></span>
* <span data-ttu-id="939e6-204">V době publikování se přidá soubor *web.config* .</span><span class="sxs-lookup"><span data-stu-id="939e6-204">*web.config* file is added at publish time.</span></span>

<span data-ttu-id="939e6-205">Třída `Startup`:</span><span class="sxs-lookup"><span data-stu-id="939e6-205">The `Startup` class:</span></span>

* <span data-ttu-id="939e6-206">Nahradí *Global. asax*.</span><span class="sxs-lookup"><span data-stu-id="939e6-206">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="939e6-207">Zpracuje všechny úlohy při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="939e6-207">Handles all app startup tasks.</span></span>

<span data-ttu-id="939e6-208">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="939e6-208">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="939e6-209">Ve ASP.NET Core MVC je směrování atributů ve výchozím nastavení součástí, pokud <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> je volána v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="939e6-209">In ASP.NET Core MVC, attribute routing is included by default when <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> is called in `Startup.Configure`.</span></span> <span data-ttu-id="939e6-210">Následující `UseMvc` volání nahrazuje soubor *app_start/Webapiconfig.cs* projektu *ProductsApp* :</span><span class="sxs-lookup"><span data-stu-id="939e6-210">The following `UseMvc` call replaces the *ProductsApp* project's *App_Start/WebApiConfig.cs* file:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13)]

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="939e6-211">Migrace modelů a řadičů</span><span class="sxs-lookup"><span data-stu-id="939e6-211">Migrate models and controllers</span></span>

<span data-ttu-id="939e6-212">Následující kód ukazuje `ProductsController` aktualizaci ASP.NET Core: [!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span><span class="sxs-lookup"><span data-stu-id="939e6-212">The following code shows the `ProductsController` update for ASP.NET Core: [!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span></span>

<span data-ttu-id="939e6-213">Aktualizujte `ProductsController` ASP.NET Core pro:</span><span class="sxs-lookup"><span data-stu-id="939e6-213">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="939e6-214">Zkopírujte *Controllers/ProductsController. cs* z původního projektu na nový.</span><span class="sxs-lookup"><span data-stu-id="939e6-214">Copy *Controllers/ProductsController.cs* from the original project to the new one.</span></span>
1. <span data-ttu-id="939e6-215">Zkopírujte složku *modely* z původního projektu do nového.</span><span class="sxs-lookup"><span data-stu-id="939e6-215">Copy the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="939e6-216">Změňte kořenový obor názvů zkopírovaných souborů na `ProductsCore` .</span><span class="sxs-lookup"><span data-stu-id="939e6-216">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="939e6-217">Aktualizujte `using ProductsApp.Models;` příkaz na `using ProductsCore.Models;` .</span><span class="sxs-lookup"><span data-stu-id="939e6-217">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="939e6-218">Následující komponenty v ASP.NET Core neexistují:</span><span class="sxs-lookup"><span data-stu-id="939e6-218">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="939e6-219">Třída `ApiController`</span><span class="sxs-lookup"><span data-stu-id="939e6-219">`ApiController` class</span></span>
* <span data-ttu-id="939e6-220">`System.Web.Http` hosting</span><span class="sxs-lookup"><span data-stu-id="939e6-220">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="939e6-221">`IHttpActionResult` prostředí</span><span class="sxs-lookup"><span data-stu-id="939e6-221">`IHttpActionResult` interface</span></span>

<span data-ttu-id="939e6-222">Proveďte následující změny:</span><span class="sxs-lookup"><span data-stu-id="939e6-222">Make the following changes:</span></span>

1. <span data-ttu-id="939e6-223">Změňte `ApiController` na <xref:Microsoft.AspNetCore.Mvc.ControllerBase> .</span><span class="sxs-lookup"><span data-stu-id="939e6-223">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="939e6-224">Přidejte `using Microsoft.AspNetCore.Mvc;` pro vyřešení `ControllerBase` odkazu.</span><span class="sxs-lookup"><span data-stu-id="939e6-224">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="939e6-225">Odstraňte `using System.Web.Http;`.</span><span class="sxs-lookup"><span data-stu-id="939e6-225">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="939e6-226">Změňte `GetProduct` návratový typ akce z `IHttpActionResult` na `ActionResult<Product>` .</span><span class="sxs-lookup"><span data-stu-id="939e6-226">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="939e6-227">Zjednodušte `GetProduct` příkaz akce `return` následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="939e6-227">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="939e6-228">Konfigurace směrování</span><span class="sxs-lookup"><span data-stu-id="939e6-228">Configure routing</span></span>

<span data-ttu-id="939e6-229">Nakonfigurujte směrování následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="939e6-229">Configure routing as follows:</span></span>

1. <span data-ttu-id="939e6-230">Označte `ProductsController` třídu následujícími atributy:</span><span class="sxs-lookup"><span data-stu-id="939e6-230">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="939e6-231">Předchozí [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) atribut nakonfiguruje vzor směrování atributů řadiče.</span><span class="sxs-lookup"><span data-stu-id="939e6-231">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="939e6-232">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)Atribut dělá směrování požadavku u všech akcí v tomto kontroleru.</span><span class="sxs-lookup"><span data-stu-id="939e6-232">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="939e6-233">Směrování atributů podporuje tokeny, jako například `[controller]` a `[action]` .</span><span class="sxs-lookup"><span data-stu-id="939e6-233">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="939e6-234">Za běhu je každý token nahrazen názvem kontroleru nebo akce, v uvedeném pořadí, na který byl atribut použit.</span><span class="sxs-lookup"><span data-stu-id="939e6-234">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="939e6-235">Tokeny omezují počet řetězců Magic v projektu.</span><span class="sxs-lookup"><span data-stu-id="939e6-235">The tokens reduce the number of magic strings in the project.</span></span> <span data-ttu-id="939e6-236">Tokeny také zajistí, aby trasy zůstaly synchronizované s odpovídajícími kontroléry a akcemi při použití refaktoringu automatického přejmenování.</span><span class="sxs-lookup"><span data-stu-id="939e6-236">The tokens also ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="939e6-237">Nastavte režim kompatibility projektu na ASP.NET Core 2,2:</span><span class="sxs-lookup"><span data-stu-id="939e6-237">Set the project's compatibility mode to ASP.NET Core 2.2:</span></span>

    [!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    <span data-ttu-id="939e6-238">Předchozí změna:</span><span class="sxs-lookup"><span data-stu-id="939e6-238">The preceding change:</span></span>

    * <span data-ttu-id="939e6-239">Je vyžadován pro použití `[ApiController]` atributu na úrovni řadiče.</span><span class="sxs-lookup"><span data-stu-id="939e6-239">Is required to use the `[ApiController]` attribute at the controller level.</span></span>
    * <span data-ttu-id="939e6-240">Výslovný se na potenciálně odrušující chování zavedená v ASP.NET Core 2,2.</span><span class="sxs-lookup"><span data-stu-id="939e6-240">Opts in to potentially breaking behaviors introduced in ASP.NET Core 2.2.</span></span>
1. <span data-ttu-id="939e6-241">Povolit požadavky HTTP GET na `ProductController` akce:</span><span class="sxs-lookup"><span data-stu-id="939e6-241">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="939e6-242">Použijte [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atribut pro `GetAllProducts` akci.</span><span class="sxs-lookup"><span data-stu-id="939e6-242">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="939e6-243">Použijte `[HttpGet("{id}")]` atribut pro `GetProduct` akci.</span><span class="sxs-lookup"><span data-stu-id="939e6-243">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="939e6-244">Spusťte migrovaný projekt a přejděte na adresu `/api/products` .</span><span class="sxs-lookup"><span data-stu-id="939e6-244">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="939e6-245">Zobrazí se úplný seznam tří produktů.</span><span class="sxs-lookup"><span data-stu-id="939e6-245">A full list of three products appears.</span></span> <span data-ttu-id="939e6-246">Přejděte na adresu `/api/products/1`.</span><span class="sxs-lookup"><span data-stu-id="939e6-246">Browse to `/api/products/1`.</span></span> <span data-ttu-id="939e6-247">Zobrazí se první produkt.</span><span class="sxs-lookup"><span data-stu-id="939e6-247">The first product appears.</span></span>

## <a name="compatibility-shim"></a><span data-ttu-id="939e6-248">Překrytí kompatibility</span><span class="sxs-lookup"><span data-stu-id="939e6-248">Compatibility shim</span></span>

<span data-ttu-id="939e6-249">Knihovna [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) poskytuje překrytí kompatibility pro přesun projektů webového rozhraní API ASP.NET 4. x do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="939e6-249">The [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) library provides a compatibility shim to move ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="939e6-250">Překrytí kompatibility rozšiřuje ASP.NET Core o podporu řady konvencí z webového rozhraní API 2 pro ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="939e6-250">The compatibility shim extends ASP.NET Core to support a number of conventions from ASP.NET 4.x Web API 2.</span></span> <span data-ttu-id="939e6-251">Vzorový port dříve v tomto dokumentu je dostatečně základní, protože překrytí kompatibility bylo zbytečné.</span><span class="sxs-lookup"><span data-stu-id="939e6-251">The sample ported previously in this document is basic enough that the compatibility shim was unnecessary.</span></span> <span data-ttu-id="939e6-252">U větších projektů může použití překrytí kompatibility sloužit k dočasnému přemostění mezer rozhraní API mezi ASP.NET Core a webovým rozhraním API 2 ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="939e6-252">For larger projects, using the compatibility shim can be useful for temporarily bridging the API gap between ASP.NET Core and ASP.NET 4.x Web API 2.</span></span>

<span data-ttu-id="939e6-253">Překrytí kompatibility webového rozhraní API je určeno k použití jako dočasné opatření pro podporu migrace rozsáhlých projektů webového rozhraní API ASP.NET 4. x na ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="939e6-253">The Web API compatibility shim is meant to be used as a temporary measure to support migrating large ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="939e6-254">V průběhu času by se projekty měly aktualizovat tak, aby používaly ASP.NET Core vzorů, a nemusely se spoléhat na překrytí kompatibility.</span><span class="sxs-lookup"><span data-stu-id="939e6-254">Over time, projects should be updated to use ASP.NET Core patterns instead of relying on the compatibility shim.</span></span>

<span data-ttu-id="939e6-255">Mezi zahrnuté funkce kompatibility `Microsoft.AspNetCore.Mvc.WebApiCompatShim` patří:</span><span class="sxs-lookup"><span data-stu-id="939e6-255">Compatibility features included in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` include:</span></span>

* <span data-ttu-id="939e6-256">Přidá `ApiController` typ, aby se základní typy řadičů nemusely aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="939e6-256">Adds an `ApiController` type so that controllers' base types don't need to be updated.</span></span>
* <span data-ttu-id="939e6-257">Povoluje vazbu modelů ve stylu webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="939e6-257">Enables Web API-style model binding.</span></span> <span data-ttu-id="939e6-258">ASP.NET Core vázání modelů MVC funguje podobně jako ASP.NET 4. x MVC 5 ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="939e6-258">ASP.NET Core MVC model binding functions similarly to that of ASP.NET 4.x MVC 5, by default.</span></span> <span data-ttu-id="939e6-259">Překrytí kompatibility mění vazbu modelu tak, aby bylo lépe podobné konvencím vazby modelů ASP.NET 4. x webového rozhraní API 2.</span><span class="sxs-lookup"><span data-stu-id="939e6-259">The compatibility shim changes model binding to be more similar to ASP.NET 4.x Web API 2 model binding conventions.</span></span> <span data-ttu-id="939e6-260">Například komplexní typy jsou automaticky svázány z textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="939e6-260">For example, complex types are automatically bound from the request body.</span></span>
* <span data-ttu-id="939e6-261">Rozšiřuje vazbu modelu tak, aby akce kontroleru mohly přijímat parametry typu `HttpRequestMessage` .</span><span class="sxs-lookup"><span data-stu-id="939e6-261">Extends model binding so that controller actions can take parameters of type `HttpRequestMessage`.</span></span>
* <span data-ttu-id="939e6-262">Přidá formátovací moduly zpráv umožňující akce vracet výsledky typu `HttpResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="939e6-262">Adds message formatters allowing actions to return results of type `HttpResponseMessage`.</span></span>
* <span data-ttu-id="939e6-263">Přidá další metody odpovědi, které akce webového rozhraní API 2 mohly použít k obsluze odpovědí:</span><span class="sxs-lookup"><span data-stu-id="939e6-263">Adds additional response methods that Web API 2 actions may have used to serve responses:</span></span>
  * <span data-ttu-id="939e6-264">`HttpResponseMessage` elektřiny</span><span class="sxs-lookup"><span data-stu-id="939e6-264">`HttpResponseMessage` generators:</span></span>
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * <span data-ttu-id="939e6-265">Metody výsledku akce:</span><span class="sxs-lookup"><span data-stu-id="939e6-265">Action result methods:</span></span>
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* <span data-ttu-id="939e6-266">Přidá instanci `IContentNegotiator` do kontejneru injektáže (di) závislosti aplikace a zpřístupní typy související s vyjednáváním obsahu z [Microsoft. ASPNET. WebApi. Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span><span class="sxs-lookup"><span data-stu-id="939e6-266">Adds an instance of `IContentNegotiator` to the app's dependency injection (DI) container and makes available the content negotiation-related types from [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span></span> <span data-ttu-id="939e6-267">Příklady takových typů jsou `DefaultContentNegotiator` a `MediaTypeFormatter` .</span><span class="sxs-lookup"><span data-stu-id="939e6-267">Examples of such types include `DefaultContentNegotiator` and `MediaTypeFormatter`.</span></span>

<span data-ttu-id="939e6-268">Použití překrytí kompatibility:</span><span class="sxs-lookup"><span data-stu-id="939e6-268">To use the compatibility shim:</span></span>

1. <span data-ttu-id="939e6-269">Nainstalujte balíček NuGet [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) .</span><span class="sxs-lookup"><span data-stu-id="939e6-269">Install the [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet package.</span></span>
1. <span data-ttu-id="939e6-270">Zaregistrujte služby překrytí kompatibility s využitím kontejneru DI aplikace voláním `services.AddMvc().AddWebApiConventions()` v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="939e6-270">Register the compatibility shim's services with the app's DI container by calling `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="939e6-271">Definujte trasy specifické pro webové rozhraní API pomocí ve `MapWebApiRoute` `IRouteBuilder` `IApplicationBuilder.UseMvc` volání aplikace.</span><span class="sxs-lookup"><span data-stu-id="939e6-271">Define web API-specific routes using `MapWebApiRoute` on the `IRouteBuilder` in the app's `IApplicationBuilder.UseMvc` call.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="939e6-272">Další materiály</span><span class="sxs-lookup"><span data-stu-id="939e6-272">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
::: moniker-end

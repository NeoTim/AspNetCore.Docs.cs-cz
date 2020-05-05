---
title: Migrace z webového rozhraní API ASP.NET do ASP.NET Core
author: ardalis
description: Naučte se migrovat implementaci webového rozhraní API z webového rozhraní API ASP.NET 4. x na ASP.NET Core MVC.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/webapi
ms.openlocfilehash: dda457daa0cb8a59ccd4c326a601e375fe4a81bb
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766586"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a><span data-ttu-id="85c24-103">Migrace z webového rozhraní API ASP.NET do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="85c24-103">Migrate from ASP.NET Web API to ASP.NET Core</span></span>

<span data-ttu-id="85c24-104">[Scottem Addie](https://twitter.com/scott_addie) a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="85c24-104">By [Scott Addie](https://twitter.com/scott_addie) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="85c24-105">Webové rozhraní API ASP.NET 4. x je služba HTTP, která dosahuje široké škály klientů, včetně prohlížečů a mobilních zařízení.</span><span class="sxs-lookup"><span data-stu-id="85c24-105">An ASP.NET 4.x Web API is an HTTP service that reaches a broad range of clients, including browsers and mobile devices.</span></span> <span data-ttu-id="85c24-106">ASP.NET Core sjednocuje ASP.NET 4. x MVC a model aplikace webového rozhraní API do jednoduššího programovacího modelu označovaného jako ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="85c24-106">ASP.NET Core unifies ASP.NET 4.x's MVC and Web API app models into a simpler programming model known as ASP.NET Core MVC.</span></span> <span data-ttu-id="85c24-107">Tento článek popisuje kroky potřebné k migraci z webového rozhraní API ASP.NET 4. x na ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="85c24-107">This article demonstrates the steps required to migrate from ASP.NET 4.x Web API to ASP.NET Core MVC.</span></span>

<span data-ttu-id="85c24-108">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="85c24-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="85c24-109">Požadavky</span><span class="sxs-lookup"><span data-stu-id="85c24-109">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="85c24-110">Kontrola projektu webového rozhraní API ASP.NET 4. x</span><span class="sxs-lookup"><span data-stu-id="85c24-110">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="85c24-111">Jako výchozí bod Tento článek používá projekt *ProductsApp* vytvořený v [Začínáme s webovým rozhraním API 2 pro ASP.NET](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span><span class="sxs-lookup"><span data-stu-id="85c24-111">As a starting point, this article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="85c24-112">V tomto projektu je jednoduchý projekt webového rozhraní API ASP.NET 4. x nakonfigurovaný takto.</span><span class="sxs-lookup"><span data-stu-id="85c24-112">In that project, a simple ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="85c24-113">V *Global.asax.cs*je volání provedeno na `WebApiConfig.Register`:</span><span class="sxs-lookup"><span data-stu-id="85c24-113">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="85c24-114">Třída se nachází ve složce *app_start* a má statickou `Register` metodu: `WebApiConfig`</span><span class="sxs-lookup"><span data-stu-id="85c24-114">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="85c24-115">Tato třída konfiguruje [Směrování atributů](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), i když není ve skutečnosti použita v projektu.</span><span class="sxs-lookup"><span data-stu-id="85c24-115">This class configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used in the project.</span></span> <span data-ttu-id="85c24-116">Také nakonfiguruje směrovací tabulku, která je používána webovým rozhraním API ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="85c24-116">It also configures the routing table, which is used by ASP.NET Web API.</span></span> <span data-ttu-id="85c24-117">V takovém případě webový rozhraní API ASP.NET 4. x očekává, že adresy URL `/api/{controller}/{id}`odpovídají formátu `{id}` a jsou volitelné.</span><span class="sxs-lookup"><span data-stu-id="85c24-117">In this case, ASP.NET 4.x Web API expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="85c24-118">Projekt *ProductsApp* zahrnuje jeden kontroler.</span><span class="sxs-lookup"><span data-stu-id="85c24-118">The *ProductsApp* project includes one controller.</span></span> <span data-ttu-id="85c24-119">Kontroler dědí z `ApiController` a obsahuje dvě akce:</span><span class="sxs-lookup"><span data-stu-id="85c24-119">The controller inherits from `ApiController` and contains two actions:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Controllers/ProductsController.cs?highlight=28,33)]

<span data-ttu-id="85c24-120">`Product` Model používaný `ProductsController` je jednoduchou třídou:</span><span class="sxs-lookup"><span data-stu-id="85c24-120">The `Product` model used by `ProductsController` is a simple class:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Models/Product.cs)]

<span data-ttu-id="85c24-121">Následující části demonstrují migraci projektu webového rozhraní API do ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="85c24-121">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-destination-project"></a><span data-ttu-id="85c24-122">Vytvořit cílový projekt</span><span class="sxs-lookup"><span data-stu-id="85c24-122">Create destination project</span></span>

<span data-ttu-id="85c24-123">Proveďte následující kroky v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="85c24-123">Complete the following steps in Visual Studio:</span></span>

* <span data-ttu-id="85c24-124">Přejít na **soubor** > **Nový** > **projekt** > **Další typy** > projektů**řešení Visual Studio**.</span><span class="sxs-lookup"><span data-stu-id="85c24-124">Go to **File** > **New** > **Project** > **Other Project Types** > **Visual Studio Solutions**.</span></span> <span data-ttu-id="85c24-125">Vyberte **prázdné řešení**a pojmenujte řešení *WebAPIMigration*.</span><span class="sxs-lookup"><span data-stu-id="85c24-125">Select **Blank Solution**, and name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="85c24-126">Klikněte na tlačítko **OK** .</span><span class="sxs-lookup"><span data-stu-id="85c24-126">Click the **OK** button.</span></span>
* <span data-ttu-id="85c24-127">Přidejte existující projekt *ProductsApp* do řešení.</span><span class="sxs-lookup"><span data-stu-id="85c24-127">Add the existing *ProductsApp* project to the solution.</span></span>
* <span data-ttu-id="85c24-128">Přidejte do řešení nový projekt **webové aplikace ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="85c24-128">Add a new **ASP.NET Core Web Application** project to the solution.</span></span> <span data-ttu-id="85c24-129">V rozevíracím seznamu vyberte cílové rozhraní **.NET Core** a vyberte šablonu projektu **rozhraní API** .</span><span class="sxs-lookup"><span data-stu-id="85c24-129">Select the **.NET Core** target framework from the drop-down, and select the **API** project template.</span></span> <span data-ttu-id="85c24-130">Pojmenujte projekt *ProductsCore*a klikněte na tlačítko **OK** .</span><span class="sxs-lookup"><span data-stu-id="85c24-130">Name the project *ProductsCore*, and click the **OK** button.</span></span>

<span data-ttu-id="85c24-131">Řešení teď obsahuje dva projekty.</span><span class="sxs-lookup"><span data-stu-id="85c24-131">The solution now contains two projects.</span></span> <span data-ttu-id="85c24-132">Následující části vysvětlují migraci obsahu projektu *ProductsApp* do projektu *ProductsCore* .</span><span class="sxs-lookup"><span data-stu-id="85c24-132">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="85c24-133">Migrace konfigurace</span><span class="sxs-lookup"><span data-stu-id="85c24-133">Migrate configuration</span></span>

<span data-ttu-id="85c24-134">ASP.NET Core nepoužívá složku *app_start* nebo soubor *Global. asax* a v době publikování je přidán soubor *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="85c24-134">ASP.NET Core doesn't use the *App_Start* folder or the *Global.asax* file, and the *web.config* file is added at publish time.</span></span> <span data-ttu-id="85c24-135">*Startup.cs* je náhradou za *Global. asax* a nachází se v kořenovém adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="85c24-135">*Startup.cs* is the replacement for *Global.asax* and is located in the project root.</span></span> <span data-ttu-id="85c24-136">`Startup` Třída zpracovává všechny úlohy při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="85c24-136">The `Startup` class handles all app startup tasks.</span></span> <span data-ttu-id="85c24-137">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="85c24-137">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="85c24-138">Ve ASP.NET Core MVC je směrování atributů ve výchozím nastavení součástí, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> Pokud je volána `Startup.Configure`v.</span><span class="sxs-lookup"><span data-stu-id="85c24-138">In ASP.NET Core MVC, attribute routing is included by default when <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> is called in `Startup.Configure`.</span></span> <span data-ttu-id="85c24-139">Následující `UseMvc` volání nahrazuje soubor *app_start/webapiconfig.cs* projektu *ProductsApp* :</span><span class="sxs-lookup"><span data-stu-id="85c24-139">The following `UseMvc` call replaces the *ProductsApp* project's *App_Start/WebApiConfig.cs* file:</span></span>

[!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13])]

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="85c24-140">Migrace modelů a řadičů</span><span class="sxs-lookup"><span data-stu-id="85c24-140">Migrate models and controllers</span></span>

<span data-ttu-id="85c24-141">Zkopírujte přes kontroler projektu *ProductApp* a model, který používá.</span><span class="sxs-lookup"><span data-stu-id="85c24-141">Copy over the *ProductApp* project's controller and the model it uses.</span></span> <span data-ttu-id="85c24-142">Postupujte následovně:</span><span class="sxs-lookup"><span data-stu-id="85c24-142">Follow these steps:</span></span>

1. <span data-ttu-id="85c24-143">Zkopírujte *Controllers/ProductsController. cs* z původního projektu na nový.</span><span class="sxs-lookup"><span data-stu-id="85c24-143">Copy *Controllers/ProductsController.cs* from the original project to the new one.</span></span>
1. <span data-ttu-id="85c24-144">Zkopírujte celou složku *modely* z původního projektu do nového.</span><span class="sxs-lookup"><span data-stu-id="85c24-144">Copy the entire *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="85c24-145">Změňte obory názvů kopírovaných souborů tak, aby odpovídaly novému názvu projektu (*ProductsCore*).</span><span class="sxs-lookup"><span data-stu-id="85c24-145">Change the copied files' namespaces to match the new project name (*ProductsCore*).</span></span> <span data-ttu-id="85c24-146">Upravte `using ProductsApp.Models;` příkaz také v *ProductsController.cs* .</span><span class="sxs-lookup"><span data-stu-id="85c24-146">Adjust the `using ProductsApp.Models;` statement in *ProductsController.cs* too.</span></span>

<span data-ttu-id="85c24-147">V tomto okamžiku sestavíte aplikaci v důsledku řady chyb kompilace.</span><span class="sxs-lookup"><span data-stu-id="85c24-147">At this point, building the app results in a number of compilation errors.</span></span> <span data-ttu-id="85c24-148">K chybám dochází, protože následující komponenty v ASP.NET Core neexistují:</span><span class="sxs-lookup"><span data-stu-id="85c24-148">The errors occur because the following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="85c24-149">Třída `ApiController`</span><span class="sxs-lookup"><span data-stu-id="85c24-149">`ApiController` class</span></span>
* <span data-ttu-id="85c24-150">`System.Web.Http`hosting</span><span class="sxs-lookup"><span data-stu-id="85c24-150">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="85c24-151">`IHttpActionResult`prostředí</span><span class="sxs-lookup"><span data-stu-id="85c24-151">`IHttpActionResult` interface</span></span>

<span data-ttu-id="85c24-152">Opravte chyby následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="85c24-152">Fix the errors as follows:</span></span>

1. <span data-ttu-id="85c24-153">Změňte `ApiController` na <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="85c24-153">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="85c24-154">Přidejte `using Microsoft.AspNetCore.Mvc;` pro vyřešení `ControllerBase` odkazu.</span><span class="sxs-lookup"><span data-stu-id="85c24-154">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="85c24-155">Odstraňte `using System.Web.Http;`.</span><span class="sxs-lookup"><span data-stu-id="85c24-155">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="85c24-156">Změňte návratový `GetProduct` typ akce z `IHttpActionResult` na `ActionResult<Product>`.</span><span class="sxs-lookup"><span data-stu-id="85c24-156">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>

<span data-ttu-id="85c24-157">Zjednodušte `GetProduct` `return` příkaz akce následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="85c24-157">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

```csharp
return product;
```

## <a name="configure-routing"></a><span data-ttu-id="85c24-158">Konfigurace směrování</span><span class="sxs-lookup"><span data-stu-id="85c24-158">Configure routing</span></span>

<span data-ttu-id="85c24-159">Nakonfigurujte směrování následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="85c24-159">Configure routing as follows:</span></span>

1. <span data-ttu-id="85c24-160">Označte `ProductsController` třídu následujícími atributy:</span><span class="sxs-lookup"><span data-stu-id="85c24-160">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="85c24-161">Předchozí [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) atribut nakonfiguruje vzor směrování atributů řadiče.</span><span class="sxs-lookup"><span data-stu-id="85c24-161">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="85c24-162">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) Atribut dělá směrování požadavku u všech akcí v tomto kontroleru.</span><span class="sxs-lookup"><span data-stu-id="85c24-162">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="85c24-163">Směrování atributů podporuje tokeny, jako `[controller]` například `[action]`a.</span><span class="sxs-lookup"><span data-stu-id="85c24-163">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="85c24-164">Za běhu je každý token nahrazen názvem kontroleru nebo akce, v uvedeném pořadí, na který byl atribut použit.</span><span class="sxs-lookup"><span data-stu-id="85c24-164">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="85c24-165">Tokeny omezují počet řetězců Magic v projektu.</span><span class="sxs-lookup"><span data-stu-id="85c24-165">The tokens reduce the number of magic strings in the project.</span></span> <span data-ttu-id="85c24-166">Tokeny také zajistí, aby trasy zůstaly synchronizované s odpovídajícími kontroléry a akcemi při použití refaktoringu automatického přejmenování.</span><span class="sxs-lookup"><span data-stu-id="85c24-166">The tokens also ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="85c24-167">Nastavte režim kompatibility projektu na ASP.NET Core 2,2:</span><span class="sxs-lookup"><span data-stu-id="85c24-167">Set the project's compatibility mode to ASP.NET Core 2.2:</span></span>

    [!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    <span data-ttu-id="85c24-168">Předchozí změna:</span><span class="sxs-lookup"><span data-stu-id="85c24-168">The preceding change:</span></span>

    * <span data-ttu-id="85c24-169">Je vyžadován pro použití `[ApiController]` atributu na úrovni řadiče.</span><span class="sxs-lookup"><span data-stu-id="85c24-169">Is required to use the `[ApiController]` attribute at the controller level.</span></span>
    * <span data-ttu-id="85c24-170">Výslovný se na potenciálně odrušující chování zavedená v ASP.NET Core 2,2.</span><span class="sxs-lookup"><span data-stu-id="85c24-170">Opts in to potentially breaking behaviors introduced in ASP.NET Core 2.2.</span></span>
1. <span data-ttu-id="85c24-171">Povolit požadavky HTTP GET na `ProductController` akce:</span><span class="sxs-lookup"><span data-stu-id="85c24-171">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="85c24-172">Použijte [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atribut pro `GetAllProducts` akci.</span><span class="sxs-lookup"><span data-stu-id="85c24-172">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="85c24-173">Použijte `[HttpGet("{id}")]` atribut pro `GetProduct` akci.</span><span class="sxs-lookup"><span data-stu-id="85c24-173">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="85c24-174">Po předchozích změnách a odebrání nepoužívaných `using` příkazů vypadá soubor *ProductsController.cs* takto:</span><span class="sxs-lookup"><span data-stu-id="85c24-174">After the preceding changes and the removal of unused `using` statements, *ProductsController.cs* file looks like this:</span></span>

[!code-csharp[](webapi/sample/ProductsCore/Controllers/ProductsController.cs)]

<span data-ttu-id="85c24-175">Spusťte migrovaný projekt a přejděte na `/api/products`adresu.</span><span class="sxs-lookup"><span data-stu-id="85c24-175">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="85c24-176">Zobrazí se úplný seznam tří produktů.</span><span class="sxs-lookup"><span data-stu-id="85c24-176">A full list of three products appears.</span></span> <span data-ttu-id="85c24-177">Přejděte na `/api/products/1`.</span><span class="sxs-lookup"><span data-stu-id="85c24-177">Browse to `/api/products/1`.</span></span> <span data-ttu-id="85c24-178">Zobrazí se první produkt.</span><span class="sxs-lookup"><span data-stu-id="85c24-178">The first product appears.</span></span>

## <a name="compatibility-shim"></a><span data-ttu-id="85c24-179">Překrytí kompatibility</span><span class="sxs-lookup"><span data-stu-id="85c24-179">Compatibility shim</span></span>

<span data-ttu-id="85c24-180">Knihovna [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) poskytuje překrytí kompatibility pro přesun projektů webového rozhraní API ASP.NET 4. x do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="85c24-180">The [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) library provides a compatibility shim to move ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="85c24-181">Překrytí kompatibility rozšiřuje ASP.NET Core o podporu řady konvencí z webového rozhraní API 2 pro ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="85c24-181">The compatibility shim extends ASP.NET Core to support a number of conventions from ASP.NET 4.x Web API 2.</span></span> <span data-ttu-id="85c24-182">Vzorový port dříve v tomto dokumentu je dostatečně základní, protože překrytí kompatibility bylo zbytečné.</span><span class="sxs-lookup"><span data-stu-id="85c24-182">The sample ported previously in this document is basic enough that the compatibility shim was unnecessary.</span></span> <span data-ttu-id="85c24-183">U větších projektů může použití překrytí kompatibility sloužit k dočasnému přemostění mezer rozhraní API mezi ASP.NET Core a webovým rozhraním API 2 ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="85c24-183">For larger projects, using the compatibility shim can be useful for temporarily bridging the API gap between ASP.NET Core and ASP.NET 4.x Web API 2.</span></span>

<span data-ttu-id="85c24-184">Překrytí kompatibility webového rozhraní API je určeno k použití jako dočasné opatření pro podporu migrace rozsáhlých projektů webového rozhraní API ASP.NET 4. x na ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="85c24-184">The Web API compatibility shim is meant to be used as a temporary measure to support migrating large ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="85c24-185">V průběhu času by se projekty měly aktualizovat tak, aby používaly ASP.NET Core vzorů, a nemusely se spoléhat na překrytí kompatibility.</span><span class="sxs-lookup"><span data-stu-id="85c24-185">Over time, projects should be updated to use ASP.NET Core patterns instead of relying on the compatibility shim.</span></span>

<span data-ttu-id="85c24-186">`Microsoft.AspNetCore.Mvc.WebApiCompatShim` Mezi zahrnuté funkce kompatibility patří:</span><span class="sxs-lookup"><span data-stu-id="85c24-186">Compatibility features included in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` include:</span></span>

* <span data-ttu-id="85c24-187">Přidá `ApiController` typ, aby se základní typy řadičů nemusely aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="85c24-187">Adds an `ApiController` type so that controllers' base types don't need to be updated.</span></span>
* <span data-ttu-id="85c24-188">Povoluje vazbu modelů ve stylu webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="85c24-188">Enables Web API-style model binding.</span></span> <span data-ttu-id="85c24-189">ASP.NET Core vázání modelů MVC funguje podobně jako ASP.NET 4. x MVC 5 ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="85c24-189">ASP.NET Core MVC model binding functions similarly to that of ASP.NET 4.x MVC 5, by default.</span></span> <span data-ttu-id="85c24-190">Překrytí kompatibility mění vazbu modelu tak, aby bylo lépe podobné konvencím vazby modelů ASP.NET 4. x webového rozhraní API 2.</span><span class="sxs-lookup"><span data-stu-id="85c24-190">The compatibility shim changes model binding to be more similar to ASP.NET 4.x Web API 2 model binding conventions.</span></span> <span data-ttu-id="85c24-191">Například komplexní typy jsou automaticky svázány z textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="85c24-191">For example, complex types are automatically bound from the request body.</span></span>
* <span data-ttu-id="85c24-192">Rozšiřuje vazbu modelu tak, aby akce kontroleru mohly přijímat parametry typu `HttpRequestMessage`.</span><span class="sxs-lookup"><span data-stu-id="85c24-192">Extends model binding so that controller actions can take parameters of type `HttpRequestMessage`.</span></span>
* <span data-ttu-id="85c24-193">Přidá formátovací moduly zpráv umožňující akce vracet výsledky typu `HttpResponseMessage`.</span><span class="sxs-lookup"><span data-stu-id="85c24-193">Adds message formatters allowing actions to return results of type `HttpResponseMessage`.</span></span>
* <span data-ttu-id="85c24-194">Přidá další metody odpovědi, které akce webového rozhraní API 2 mohly použít k obsluze odpovědí:</span><span class="sxs-lookup"><span data-stu-id="85c24-194">Adds additional response methods that Web API 2 actions may have used to serve responses:</span></span>
  * <span data-ttu-id="85c24-195">`HttpResponseMessage`elektřiny</span><span class="sxs-lookup"><span data-stu-id="85c24-195">`HttpResponseMessage` generators:</span></span>
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * <span data-ttu-id="85c24-196">Metody výsledku akce:</span><span class="sxs-lookup"><span data-stu-id="85c24-196">Action result methods:</span></span>
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* <span data-ttu-id="85c24-197">Přidá instanci `IContentNegotiator` do kontejneru INJEKTÁŽE (di) závislosti aplikace a zpřístupní typy související s vyjednáváním obsahu z [Microsoft. ASPNET. WebApi. Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span><span class="sxs-lookup"><span data-stu-id="85c24-197">Adds an instance of `IContentNegotiator` to the app's dependency injection (DI) container and makes available the content negotiation-related types from [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span></span> <span data-ttu-id="85c24-198">Příklady takových typů jsou `DefaultContentNegotiator` a. `MediaTypeFormatter`</span><span class="sxs-lookup"><span data-stu-id="85c24-198">Examples of such types include `DefaultContentNegotiator` and `MediaTypeFormatter`.</span></span>

<span data-ttu-id="85c24-199">Použití překrytí kompatibility:</span><span class="sxs-lookup"><span data-stu-id="85c24-199">To use the compatibility shim:</span></span>

1. <span data-ttu-id="85c24-200">Nainstalujte balíček NuGet [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) .</span><span class="sxs-lookup"><span data-stu-id="85c24-200">Install the [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet package.</span></span>
1. <span data-ttu-id="85c24-201">Zaregistrujte služby překrytí kompatibility s využitím kontejneru DI aplikace `services.AddMvc().AddWebApiConventions()` voláním v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="85c24-201">Register the compatibility shim's services with the app's DI container by calling `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="85c24-202">Definujte trasy specifické pro `MapWebApiRoute` `IRouteBuilder` webové rozhraní API pomocí ve `IApplicationBuilder.UseMvc` volání aplikace.</span><span class="sxs-lookup"><span data-stu-id="85c24-202">Define web API-specific routes using `MapWebApiRoute` on the `IRouteBuilder` in the app's `IApplicationBuilder.UseMvc` call.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="85c24-203">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="85c24-203">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>

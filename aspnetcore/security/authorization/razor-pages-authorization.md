---
title: RazorAutorizační konvence stránek v ASP.NET Core
author: rick-anderson
description: Naučte se řídit přístup ke stránkám pomocí konvencí, které autorizují uživatele a umožňují anonymním uživatelům přístup k stránkám nebo složkám stránek.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: 0492dd3d9b2aee7e844e944bea96259c3ddf18d0
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408718"
---
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a>Razor<span data-ttu-id="ab146-103">Autorizační konvence stránek v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ab146-103"> Pages authorization conventions in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ab146-104">Jedním ze způsobů, jak řídit přístup v Razor aplikaci Pages, je použít při spuštění konvence autorizace.</span><span class="sxs-lookup"><span data-stu-id="ab146-104">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="ab146-105">Tyto konvence umožňují uživatelům ověřovat a povolit anonymním uživatelům přístup k jednotlivým stránkám nebo složkám stránek.</span><span class="sxs-lookup"><span data-stu-id="ab146-105">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="ab146-106">Konvence popsané v tomto tématu automaticky použijí [filtry pro autorizaci](xref:mvc/controllers/filters#authorization-filters) k řízení přístupu.</span><span class="sxs-lookup"><span data-stu-id="ab146-106">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="ab146-107">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ab146-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="ab146-108">Ukázková aplikace používá [ověřování souborem cookie bez Identity ASP.NET Core ](xref:security/authentication/cookie).</span><span class="sxs-lookup"><span data-stu-id="ab146-108">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="ab146-109">Koncepty a příklady uvedené v tomto tématu platí stejně jako aplikace, které používají ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="ab146-109">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="ab146-110">Pokud chcete použít ASP.NET Core Identity , postupujte podle pokynů v části <xref:security/authentication/identity> .</span><span class="sxs-lookup"><span data-stu-id="ab146-110">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="ab146-111">Vyžadovat autorizaci pro přístup ke stránce</span><span class="sxs-lookup"><span data-stu-id="ab146-111">Require authorization to access a page</span></span>

<span data-ttu-id="ab146-112">K <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Přidání <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> na stránku na zadané cestě použijte konvenci pomocí nástroje:</span><span class="sxs-lookup"><span data-stu-id="ab146-112">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="ab146-113">Zadaná cesta je cesta k modulu zobrazení, která je Razor relativní cestou ke kořenu stránek bez přípony a obsahuje pouze lomítka.</span><span class="sxs-lookup"><span data-stu-id="ab146-113">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="ab146-114">Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizePage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span><span class="sxs-lookup"><span data-stu-id="ab146-114">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="ab146-115"><xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>Lze použít na třídu modelu stránky s `[Authorize]` atributem Filter.</span><span class="sxs-lookup"><span data-stu-id="ab146-115">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="ab146-116">Další informace najdete v tématu [autorizace atributu Filter](xref:razor-pages/filter#authorize-filter-attribute).</span><span class="sxs-lookup"><span data-stu-id="ab146-116">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="ab146-117">Vyžadovat autorizaci pro přístup ke složce stránek</span><span class="sxs-lookup"><span data-stu-id="ab146-117">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="ab146-118">Použijte <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> konvenci pomocí nástroje <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> a přidejte <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> do všech stránek ve složce v zadané cestě:</span><span class="sxs-lookup"><span data-stu-id="ab146-118">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="ab146-119">Zadaná cesta je cesta k modulu zobrazení, což je Razor relativní cesta ke kořeni stránek.</span><span class="sxs-lookup"><span data-stu-id="ab146-119">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="ab146-120">Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span><span class="sxs-lookup"><span data-stu-id="ab146-120">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="ab146-121">Vyžadovat autorizaci pro přístup na stránku oblasti</span><span class="sxs-lookup"><span data-stu-id="ab146-121">Require authorization to access an area page</span></span>

<span data-ttu-id="ab146-122">Použijte <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> konvenci pomocí nástroje <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> a přidejte <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> na stránku oblasti v zadané cestě:</span><span class="sxs-lookup"><span data-stu-id="ab146-122">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="ab146-123">Název stránky je cesta k souboru bez přípony vzhledem k kořenovému adresáři stránek zadané oblasti.</span><span class="sxs-lookup"><span data-stu-id="ab146-123">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="ab146-124">Například název stránky pro *oblasti souboru/ Identity /Pages/Manage/accounts.cshtml* je */Manage/Accounts*.</span><span class="sxs-lookup"><span data-stu-id="ab146-124">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="ab146-125">Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeAreaPage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span><span class="sxs-lookup"><span data-stu-id="ab146-125">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="ab146-126">Vyžadovat autorizaci pro přístup ke složce oblastí</span><span class="sxs-lookup"><span data-stu-id="ab146-126">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="ab146-127">Použijte <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> konvenci pomocí nástroje <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> a přidejte <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> do všech oblastí ve složce v zadané cestě:</span><span class="sxs-lookup"><span data-stu-id="ab146-127">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="ab146-128">Cesta ke složce je cesta ke složce vzhledem k kořenovému adresáři stránek zadané oblasti.</span><span class="sxs-lookup"><span data-stu-id="ab146-128">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="ab146-129">Například cesta ke složce pro soubory v *oblasti oblasti/ Identity /Pages/Manage/* je */Manage*.</span><span class="sxs-lookup"><span data-stu-id="ab146-129">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="ab146-130">Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeAreaFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span><span class="sxs-lookup"><span data-stu-id="ab146-130">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="ab146-131">Povolení anonymního přístupu na stránku</span><span class="sxs-lookup"><span data-stu-id="ab146-131">Allow anonymous access to a page</span></span>

<span data-ttu-id="ab146-132">K <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Přidání <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> na stránku na zadané cestě použijte konvenci pomocí nástroje:</span><span class="sxs-lookup"><span data-stu-id="ab146-132">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="ab146-133">Zadaná cesta je cesta k modulu zobrazení, která je Razor relativní cestou ke kořenu stránek bez přípony a obsahuje pouze lomítka.</span><span class="sxs-lookup"><span data-stu-id="ab146-133">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="ab146-134">Povolení anonymního přístupu ke složce stránek</span><span class="sxs-lookup"><span data-stu-id="ab146-134">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="ab146-135">Použijte <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> konvenci pomocí nástroje <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> a přidejte <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> do všech stránek ve složce v zadané cestě:</span><span class="sxs-lookup"><span data-stu-id="ab146-135">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="ab146-136">Zadaná cesta je cesta k modulu zobrazení, což je Razor relativní cesta ke kořeni stránek.</span><span class="sxs-lookup"><span data-stu-id="ab146-136">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="ab146-137">Poznámka o kombinování autorizovaných a anonymních přístupů</span><span class="sxs-lookup"><span data-stu-id="ab146-137">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="ab146-138">Je platný, pokud chcete určit, že složka stránek vyžaduje autorizaci, a pak určit, že stránka v této složce umožňuje anonymní přístup:</span><span class="sxs-lookup"><span data-stu-id="ab146-138">It's valid to specify that a folder of pages requires authorization and then specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="ab146-139">Zpětný chod ale není platný.</span><span class="sxs-lookup"><span data-stu-id="ab146-139">The reverse, however, isn't valid.</span></span> <span data-ttu-id="ab146-140">Nemůžete deklarovat složku stránek pro anonymní přístup a pak zadat stránku v této složce, která vyžaduje autorizaci:</span><span class="sxs-lookup"><span data-stu-id="ab146-140">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="ab146-141">Požadavek na autorizaci na soukromé stránce se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="ab146-141">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="ab146-142">Pokud <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> jsou a aplikovány na stránku, má <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> přednost a řídí přístup.</span><span class="sxs-lookup"><span data-stu-id="ab146-142">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ab146-143">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ab146-143">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ab146-144">Jedním ze způsobů, jak řídit přístup v Razor aplikaci Pages, je použít při spuštění konvence autorizace.</span><span class="sxs-lookup"><span data-stu-id="ab146-144">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="ab146-145">Tyto konvence umožňují uživatelům ověřovat a povolit anonymním uživatelům přístup k jednotlivým stránkám nebo složkám stránek.</span><span class="sxs-lookup"><span data-stu-id="ab146-145">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="ab146-146">Konvence popsané v tomto tématu automaticky použijí [filtry pro autorizaci](xref:mvc/controllers/filters#authorization-filters) k řízení přístupu.</span><span class="sxs-lookup"><span data-stu-id="ab146-146">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="ab146-147">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ab146-147">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="ab146-148">Ukázková aplikace používá [ověřování souborem cookie bez Identity ASP.NET Core ](xref:security/authentication/cookie).</span><span class="sxs-lookup"><span data-stu-id="ab146-148">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="ab146-149">Koncepty a příklady uvedené v tomto tématu platí stejně jako aplikace, které používají ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="ab146-149">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="ab146-150">Pokud chcete použít ASP.NET Core Identity , postupujte podle pokynů v části <xref:security/authentication/identity> .</span><span class="sxs-lookup"><span data-stu-id="ab146-150">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="ab146-151">Vyžadovat autorizaci pro přístup ke stránce</span><span class="sxs-lookup"><span data-stu-id="ab146-151">Require authorization to access a page</span></span>

<span data-ttu-id="ab146-152">K <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Přidání <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> na stránku na zadané cestě použijte konvenci pomocí nástroje:</span><span class="sxs-lookup"><span data-stu-id="ab146-152">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="ab146-153">Zadaná cesta je cesta k modulu zobrazení, která je Razor relativní cestou ke kořenu stránek bez přípony a obsahuje pouze lomítka.</span><span class="sxs-lookup"><span data-stu-id="ab146-153">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="ab146-154">Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizePage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span><span class="sxs-lookup"><span data-stu-id="ab146-154">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="ab146-155"><xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>Lze použít na třídu modelu stránky s `[Authorize]` atributem Filter.</span><span class="sxs-lookup"><span data-stu-id="ab146-155">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="ab146-156">Další informace najdete v tématu [autorizace atributu Filter](xref:razor-pages/filter#authorize-filter-attribute).</span><span class="sxs-lookup"><span data-stu-id="ab146-156">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="ab146-157">Vyžadovat autorizaci pro přístup ke složce stránek</span><span class="sxs-lookup"><span data-stu-id="ab146-157">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="ab146-158">Použijte <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> konvenci pomocí nástroje <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> a přidejte <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> do všech stránek ve složce v zadané cestě:</span><span class="sxs-lookup"><span data-stu-id="ab146-158">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="ab146-159">Zadaná cesta je cesta k modulu zobrazení, což je Razor relativní cesta ke kořeni stránek.</span><span class="sxs-lookup"><span data-stu-id="ab146-159">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="ab146-160">Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span><span class="sxs-lookup"><span data-stu-id="ab146-160">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="ab146-161">Vyžadovat autorizaci pro přístup na stránku oblasti</span><span class="sxs-lookup"><span data-stu-id="ab146-161">Require authorization to access an area page</span></span>

<span data-ttu-id="ab146-162">Použijte <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> konvenci pomocí nástroje <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> a přidejte <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> na stránku oblasti v zadané cestě:</span><span class="sxs-lookup"><span data-stu-id="ab146-162">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="ab146-163">Název stránky je cesta k souboru bez přípony vzhledem k kořenovému adresáři stránek zadané oblasti.</span><span class="sxs-lookup"><span data-stu-id="ab146-163">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="ab146-164">Například název stránky pro *oblasti souboru/ Identity /Pages/Manage/accounts.cshtml* je */Manage/Accounts*.</span><span class="sxs-lookup"><span data-stu-id="ab146-164">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="ab146-165">Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeAreaPage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span><span class="sxs-lookup"><span data-stu-id="ab146-165">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="ab146-166">Vyžadovat autorizaci pro přístup ke složce oblastí</span><span class="sxs-lookup"><span data-stu-id="ab146-166">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="ab146-167">Použijte <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> konvenci pomocí nástroje <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> a přidejte <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> do všech oblastí ve složce v zadané cestě:</span><span class="sxs-lookup"><span data-stu-id="ab146-167">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="ab146-168">Cesta ke složce je cesta ke složce vzhledem k kořenovému adresáři stránek zadané oblasti.</span><span class="sxs-lookup"><span data-stu-id="ab146-168">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="ab146-169">Například cesta ke složce pro soubory v *oblasti oblasti/ Identity /Pages/Manage/* je */Manage*.</span><span class="sxs-lookup"><span data-stu-id="ab146-169">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="ab146-170">Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeAreaFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span><span class="sxs-lookup"><span data-stu-id="ab146-170">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="ab146-171">Povolení anonymního přístupu na stránku</span><span class="sxs-lookup"><span data-stu-id="ab146-171">Allow anonymous access to a page</span></span>

<span data-ttu-id="ab146-172">K <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Přidání <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> na stránku na zadané cestě použijte konvenci pomocí nástroje:</span><span class="sxs-lookup"><span data-stu-id="ab146-172">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="ab146-173">Zadaná cesta je cesta k modulu zobrazení, která je Razor relativní cestou ke kořenu stránek bez přípony a obsahuje pouze lomítka.</span><span class="sxs-lookup"><span data-stu-id="ab146-173">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="ab146-174">Povolení anonymního přístupu ke složce stránek</span><span class="sxs-lookup"><span data-stu-id="ab146-174">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="ab146-175">Použijte <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> konvenci pomocí nástroje <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> a přidejte <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> do všech stránek ve složce v zadané cestě:</span><span class="sxs-lookup"><span data-stu-id="ab146-175">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="ab146-176">Zadaná cesta je cesta k modulu zobrazení, což je Razor relativní cesta ke kořeni stránek.</span><span class="sxs-lookup"><span data-stu-id="ab146-176">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="ab146-177">Poznámka o kombinování autorizovaných a anonymních přístupů</span><span class="sxs-lookup"><span data-stu-id="ab146-177">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="ab146-178">Je platný, pokud chcete určit složku stránek, které vyžadují autorizaci, a než určit, že stránka v této složce povoluje anonymní přístup:</span><span class="sxs-lookup"><span data-stu-id="ab146-178">It's valid to specify that a folder of pages that require authorization and than specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="ab146-179">Zpětný chod ale není platný.</span><span class="sxs-lookup"><span data-stu-id="ab146-179">The reverse, however, isn't valid.</span></span> <span data-ttu-id="ab146-180">Nemůžete deklarovat složku stránek pro anonymní přístup a pak zadat stránku v této složce, která vyžaduje autorizaci:</span><span class="sxs-lookup"><span data-stu-id="ab146-180">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="ab146-181">Požadavek na autorizaci na soukromé stránce se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="ab146-181">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="ab146-182">Pokud <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> jsou a aplikovány na stránku, má <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> přednost a řídí přístup.</span><span class="sxs-lookup"><span data-stu-id="ab146-182">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ab146-183">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ab146-183">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

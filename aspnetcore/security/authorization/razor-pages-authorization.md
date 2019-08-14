---
title: Razor Pages autorizační konvence v ASP.NET Core
author: guardrex
description: Naučte se řídit přístup ke stránkám pomocí konvencí, které autorizují uživatele a umožňují anonymním uživatelům přístup k stránkám nebo složkám stránek.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: e0102ff64921a83f0330acb6f5d9bfd90f64ca7a
ms.sourcegitcommit: 89fcc6cb3e12790dca2b8b62f86609bed6335be9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68994038"
---
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a><span data-ttu-id="5305e-103">Razor Pages autorizační konvence v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5305e-103">Razor Pages authorization conventions in ASP.NET Core</span></span>

<span data-ttu-id="5305e-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5305e-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5305e-105">Jedním ze způsobů, jak řídit přístup v aplikaci Razor Pages, je použití autorizačních konvencí při spuštění.</span><span class="sxs-lookup"><span data-stu-id="5305e-105">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="5305e-106">Tyto konvence umožňují uživatelům ověřovat a povolit anonymním uživatelům přístup k jednotlivým stránkám nebo složkám stránek.</span><span class="sxs-lookup"><span data-stu-id="5305e-106">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="5305e-107">Konvence popsané v tomto tématu automaticky použijí [filtry](xref:mvc/controllers/filters#authorization-filters) pro autorizaci k řízení přístupu.</span><span class="sxs-lookup"><span data-stu-id="5305e-107">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="5305e-108">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5305e-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5305e-109">Ukázková aplikace používá [ověřování souborem cookie bez ASP.NET Core identity](xref:security/authentication/cookie).</span><span class="sxs-lookup"><span data-stu-id="5305e-109">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="5305e-110">Koncepty a příklady uvedené v tomto tématu platí stejně jako aplikace, které používají ASP.NET Core identity.</span><span class="sxs-lookup"><span data-stu-id="5305e-110">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="5305e-111">Pokud chcete použít ASP.NET Core identity, postupujte podle pokynů <xref:security/authentication/identity>v části.</span><span class="sxs-lookup"><span data-stu-id="5305e-111">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="5305e-112">Vyžadovat autorizaci pro přístup ke stránce</span><span class="sxs-lookup"><span data-stu-id="5305e-112">Require authorization to access a page</span></span>

<span data-ttu-id="5305e-113">K přidání <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> nastránkuna<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> zadané cestě použijte konvenci pomocí nástroje:</span><span class="sxs-lookup"><span data-stu-id="5305e-113">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="5305e-114">Zadaná cesta je cesta k modulu zobrazení, která je Razor Pages relativní cesta ke kořenu bez přípony a obsahuje pouze lomítka.</span><span class="sxs-lookup"><span data-stu-id="5305e-114">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="5305e-115">Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizePage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span><span class="sxs-lookup"><span data-stu-id="5305e-115">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="5305e-116">Lze použít na třídu modelu stránky `[Authorize]` s atributem Filter. <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter></span><span class="sxs-lookup"><span data-stu-id="5305e-116">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="5305e-117">Další informace najdete v tématu [autorizace atributu Filter](xref:razor-pages/filter#authorize-filter-attribute).</span><span class="sxs-lookup"><span data-stu-id="5305e-117">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="5305e-118">Vyžadovat autorizaci pro přístup ke složce stránek</span><span class="sxs-lookup"><span data-stu-id="5305e-118">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="5305e-119"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> Použijte konvenci<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> pomocí nástroje apřidejtedovšechstránekvesložcevzadanécestě:<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*></span><span class="sxs-lookup"><span data-stu-id="5305e-119">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="5305e-120">Zadaná cesta je cesta k modulu zobrazení, která je Razor Pages relativní cesta ke kořenu.</span><span class="sxs-lookup"><span data-stu-id="5305e-120">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="5305e-121">Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span><span class="sxs-lookup"><span data-stu-id="5305e-121">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="5305e-122">Vyžadovat autorizaci pro přístup na stránku oblasti</span><span class="sxs-lookup"><span data-stu-id="5305e-122">Require authorization to access an area page</span></span>

<span data-ttu-id="5305e-123"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> Použijte konvenci<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> pomocí nástroje apřidejtenastránkuoblastivzadanécestě:<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*></span><span class="sxs-lookup"><span data-stu-id="5305e-123">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="5305e-124">Název stránky je cesta k souboru bez přípony vzhledem k kořenovému adresáři stránek zadané oblasti.</span><span class="sxs-lookup"><span data-stu-id="5305e-124">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="5305e-125">Například název stránky pro *oblasti souboru/identita, stránky/Správa/účty. cshtml* je */Manage/Accounts*.</span><span class="sxs-lookup"><span data-stu-id="5305e-125">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="5305e-126">Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeAreaPage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span><span class="sxs-lookup"><span data-stu-id="5305e-126">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="5305e-127">Vyžadovat autorizaci pro přístup ke složce oblastí</span><span class="sxs-lookup"><span data-stu-id="5305e-127">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="5305e-128"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> Použijte konvenci<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> pomocí nástroje apřidejtedovšechoblastívesložcevzadanécestě:<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*></span><span class="sxs-lookup"><span data-stu-id="5305e-128">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="5305e-129">Cesta ke složce je cesta ke složce vzhledem k kořenovému adresáři stránek zadané oblasti.</span><span class="sxs-lookup"><span data-stu-id="5305e-129">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="5305e-130">Například cesta ke složce pro soubory v *oblasti/identity/stránky/spravovat/* je */Manage*.</span><span class="sxs-lookup"><span data-stu-id="5305e-130">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="5305e-131">Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeAreaFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span><span class="sxs-lookup"><span data-stu-id="5305e-131">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="5305e-132">Povolení anonymního přístupu na stránku</span><span class="sxs-lookup"><span data-stu-id="5305e-132">Allow anonymous access to a page</span></span>

<span data-ttu-id="5305e-133">K přidání <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> nastránkuna<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> zadané cestě použijte konvenci pomocí nástroje:</span><span class="sxs-lookup"><span data-stu-id="5305e-133">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="5305e-134">Zadaná cesta je cesta k modulu zobrazení, která je Razor Pages relativní cesta ke kořenu bez přípony a obsahuje pouze lomítka.</span><span class="sxs-lookup"><span data-stu-id="5305e-134">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="5305e-135">Povolení anonymního přístupu ke složce stránek</span><span class="sxs-lookup"><span data-stu-id="5305e-135">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="5305e-136"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> Použijte konvenci<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> pomocí nástroje apřidejtedovšechstránekvesložcevzadanécestě:<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*></span><span class="sxs-lookup"><span data-stu-id="5305e-136">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="5305e-137">Zadaná cesta je cesta k modulu zobrazení, která je Razor Pages relativní cesta ke kořenu.</span><span class="sxs-lookup"><span data-stu-id="5305e-137">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="5305e-138">Poznámka o kombinování autorizovaných a anonymních přístupů</span><span class="sxs-lookup"><span data-stu-id="5305e-138">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="5305e-139">Je platný, pokud chcete určit složku stránek, které vyžadují autorizaci, a než určit, že stránka v této složce povoluje anonymní přístup:</span><span class="sxs-lookup"><span data-stu-id="5305e-139">It's valid to specify that a folder of pages that require authorization and than specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="5305e-140">Zpětný chod ale není platný.</span><span class="sxs-lookup"><span data-stu-id="5305e-140">The reverse, however, isn't valid.</span></span> <span data-ttu-id="5305e-141">Nemůžete deklarovat složku stránek pro anonymní přístup a pak zadat stránku v této složce, která vyžaduje autorizaci:</span><span class="sxs-lookup"><span data-stu-id="5305e-141">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="5305e-142">Požadavek na autorizaci na soukromé stránce se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="5305e-142">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="5305e-143">Pokud jsou <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> a aplikovány na stránku, má přednost a řídí přístup. <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter></span><span class="sxs-lookup"><span data-stu-id="5305e-143">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5305e-144">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5305e-144">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5305e-145">Jedním ze způsobů, jak řídit přístup v aplikaci Razor Pages, je použití autorizačních konvencí při spuštění.</span><span class="sxs-lookup"><span data-stu-id="5305e-145">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="5305e-146">Tyto konvence umožňují uživatelům ověřovat a povolit anonymním uživatelům přístup k jednotlivým stránkám nebo složkám stránek.</span><span class="sxs-lookup"><span data-stu-id="5305e-146">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="5305e-147">Konvence popsané v tomto tématu automaticky použijí [filtry](xref:mvc/controllers/filters#authorization-filters) pro autorizaci k řízení přístupu.</span><span class="sxs-lookup"><span data-stu-id="5305e-147">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="5305e-148">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5305e-148">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5305e-149">Ukázková aplikace používá [ověřování souborem cookie bez ASP.NET Core identity](xref:security/authentication/cookie).</span><span class="sxs-lookup"><span data-stu-id="5305e-149">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="5305e-150">Koncepty a příklady uvedené v tomto tématu platí stejně jako aplikace, které používají ASP.NET Core identity.</span><span class="sxs-lookup"><span data-stu-id="5305e-150">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="5305e-151">Pokud chcete použít ASP.NET Core identity, postupujte podle pokynů <xref:security/authentication/identity>v části.</span><span class="sxs-lookup"><span data-stu-id="5305e-151">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="5305e-152">Vyžadovat autorizaci pro přístup ke stránce</span><span class="sxs-lookup"><span data-stu-id="5305e-152">Require authorization to access a page</span></span>

<span data-ttu-id="5305e-153">K přidání <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> nastránkuna<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> zadané cestě použijte konvenci pomocí nástroje:</span><span class="sxs-lookup"><span data-stu-id="5305e-153">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="5305e-154">Zadaná cesta je cesta k modulu zobrazení, která je Razor Pages relativní cesta ke kořenu bez přípony a obsahuje pouze lomítka.</span><span class="sxs-lookup"><span data-stu-id="5305e-154">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="5305e-155">Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizePage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span><span class="sxs-lookup"><span data-stu-id="5305e-155">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="5305e-156">Lze použít na třídu modelu stránky `[Authorize]` s atributem Filter. <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter></span><span class="sxs-lookup"><span data-stu-id="5305e-156">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="5305e-157">Další informace najdete v tématu [autorizace atributu Filter](xref:razor-pages/filter#authorize-filter-attribute).</span><span class="sxs-lookup"><span data-stu-id="5305e-157">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="5305e-158">Vyžadovat autorizaci pro přístup ke složce stránek</span><span class="sxs-lookup"><span data-stu-id="5305e-158">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="5305e-159"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> Použijte konvenci<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> pomocí nástroje apřidejtedovšechstránekvesložcevzadanécestě:<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*></span><span class="sxs-lookup"><span data-stu-id="5305e-159">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="5305e-160">Zadaná cesta je cesta k modulu zobrazení, která je Razor Pages relativní cesta ke kořenu.</span><span class="sxs-lookup"><span data-stu-id="5305e-160">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="5305e-161">Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span><span class="sxs-lookup"><span data-stu-id="5305e-161">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="5305e-162">Vyžadovat autorizaci pro přístup na stránku oblasti</span><span class="sxs-lookup"><span data-stu-id="5305e-162">Require authorization to access an area page</span></span>

<span data-ttu-id="5305e-163"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> Použijte konvenci<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> pomocí nástroje apřidejtenastránkuoblastivzadanécestě:<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*></span><span class="sxs-lookup"><span data-stu-id="5305e-163">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="5305e-164">Název stránky je cesta k souboru bez přípony vzhledem k kořenovému adresáři stránek zadané oblasti.</span><span class="sxs-lookup"><span data-stu-id="5305e-164">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="5305e-165">Například název stránky pro *oblasti souboru/identita, stránky/Správa/účty. cshtml* je */Manage/Accounts*.</span><span class="sxs-lookup"><span data-stu-id="5305e-165">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="5305e-166">Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeAreaPage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span><span class="sxs-lookup"><span data-stu-id="5305e-166">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="5305e-167">Vyžadovat autorizaci pro přístup ke složce oblastí</span><span class="sxs-lookup"><span data-stu-id="5305e-167">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="5305e-168"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> Použijte konvenci<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> pomocí nástroje apřidejtedovšechoblastívesložcevzadanécestě:<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*></span><span class="sxs-lookup"><span data-stu-id="5305e-168">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="5305e-169">Cesta ke složce je cesta ke složce vzhledem k kořenovému adresáři stránek zadané oblasti.</span><span class="sxs-lookup"><span data-stu-id="5305e-169">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="5305e-170">Například cesta ke složce pro soubory v *oblasti/identity/stránky/spravovat/* je */Manage*.</span><span class="sxs-lookup"><span data-stu-id="5305e-170">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="5305e-171">Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeAreaFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span><span class="sxs-lookup"><span data-stu-id="5305e-171">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="5305e-172">Povolení anonymního přístupu na stránku</span><span class="sxs-lookup"><span data-stu-id="5305e-172">Allow anonymous access to a page</span></span>

<span data-ttu-id="5305e-173">K přidání <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> nastránkuna<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> zadané cestě použijte konvenci pomocí nástroje:</span><span class="sxs-lookup"><span data-stu-id="5305e-173">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="5305e-174">Zadaná cesta je cesta k modulu zobrazení, která je Razor Pages relativní cesta ke kořenu bez přípony a obsahuje pouze lomítka.</span><span class="sxs-lookup"><span data-stu-id="5305e-174">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="5305e-175">Povolení anonymního přístupu ke složce stránek</span><span class="sxs-lookup"><span data-stu-id="5305e-175">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="5305e-176"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> Použijte konvenci<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> pomocí nástroje apřidejtedovšechstránekvesložcevzadanécestě:<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*></span><span class="sxs-lookup"><span data-stu-id="5305e-176">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="5305e-177">Zadaná cesta je cesta k modulu zobrazení, která je Razor Pages relativní cesta ke kořenu.</span><span class="sxs-lookup"><span data-stu-id="5305e-177">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="5305e-178">Poznámka o kombinování autorizovaných a anonymních přístupů</span><span class="sxs-lookup"><span data-stu-id="5305e-178">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="5305e-179">Je platný, pokud chcete určit složku stránek, které vyžadují autorizaci, a než určit, že stránka v této složce povoluje anonymní přístup:</span><span class="sxs-lookup"><span data-stu-id="5305e-179">It's valid to specify that a folder of pages that require authorization and than specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="5305e-180">Zpětný chod ale není platný.</span><span class="sxs-lookup"><span data-stu-id="5305e-180">The reverse, however, isn't valid.</span></span> <span data-ttu-id="5305e-181">Nemůžete deklarovat složku stránek pro anonymní přístup a pak zadat stránku v této složce, která vyžaduje autorizaci:</span><span class="sxs-lookup"><span data-stu-id="5305e-181">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="5305e-182">Požadavek na autorizaci na soukromé stránce se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="5305e-182">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="5305e-183">Pokud jsou <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> a aplikovány na stránku, má přednost a řídí přístup. <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter></span><span class="sxs-lookup"><span data-stu-id="5305e-183">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5305e-184">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5305e-184">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

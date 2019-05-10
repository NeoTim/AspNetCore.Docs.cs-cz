---
title: Vytváření názvů autorizace stránek Razor v ASP.NET Core
author: guardrex
description: Zjistěte, jak řídit přístup ke stránkám s vytváření názvů, které uživateli uživatele autorizovat a umožňoval anonymním uživatelům přístup k stránky nebo složky stránek.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2019
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: ff061f96f30cd893b903403de760a172c924cf06
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64901998"
---
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a><span data-ttu-id="dd4a3-103">Vytváření názvů autorizace stránek Razor v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dd4a3-103">Razor Pages authorization conventions in ASP.NET Core</span></span>

<span data-ttu-id="dd4a3-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="dd4a3-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="dd4a3-105">Jedním ze způsobů řízení přístupu v aplikaci pro stránky Razor je použití autorizace konvence při spuštění.</span><span class="sxs-lookup"><span data-stu-id="dd4a3-105">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="dd4a3-106">Tato konvence umožňují uživateli uživatele autorizovat a umožňoval anonymním uživatelům přístup k jednotlivé stránky nebo složky stránek.</span><span class="sxs-lookup"><span data-stu-id="dd4a3-106">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="dd4a3-107">Platí zásady popsané v tomto tématu automaticky [filtry autorizace](xref:mvc/controllers/filters#authorization-filters) pro řízení přístupu.</span><span class="sxs-lookup"><span data-stu-id="dd4a3-107">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="dd4a3-108">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="dd4a3-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="dd4a3-109">Tato ukázková aplikace používá [ověřování souborem cookie bez ASP.NET Core Identity](xref:security/authentication/cookie).</span><span class="sxs-lookup"><span data-stu-id="dd4a3-109">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="dd4a3-110">Koncepty a příkladů uvedených v tomto tématu platí stejně pro aplikace, které používají technologii ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="dd4a3-110">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="dd4a3-111">ASP.NET Core Identity, postupujte podle pokynů v <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="dd4a3-111">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="dd4a3-112">Vyžaduje povolení ke stránce</span><span class="sxs-lookup"><span data-stu-id="dd4a3-112">Require authorization to access a page</span></span>

<span data-ttu-id="dd4a3-113">Použití <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> konvence prostřednictvím <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> přidat <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> na stránku v zadané cestě:</span><span class="sxs-lookup"><span data-stu-id="dd4a3-113">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="dd4a3-114">Zadaná cesta je cesta modulu zobrazení, která je relativní cesta kořenového stránky Razor bez rozšíření a který obsahuje pouze lomítka.</span><span class="sxs-lookup"><span data-stu-id="dd4a3-114">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="dd4a3-115">K určení [zásad autorizace](xref:security/authorization/policies), použijte [AuthorizePage přetížení](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span><span class="sxs-lookup"><span data-stu-id="dd4a3-115">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="dd4a3-116"><xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> Lze použít u třídy modelu stránky s `[Authorize]` atribut filtru.</span><span class="sxs-lookup"><span data-stu-id="dd4a3-116">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="dd4a3-117">Další informace najdete v tématu [atribut filtru Authorize](xref:razor-pages/filter#authorize-filter-attribute).</span><span class="sxs-lookup"><span data-stu-id="dd4a3-117">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="dd4a3-118">Vyžaduje autorizaci pro přístup ke složce stránek</span><span class="sxs-lookup"><span data-stu-id="dd4a3-118">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="dd4a3-119">Použití <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> konvence prostřednictvím <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> přidat <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> na všechny stránky ve složce v zadané cestě:</span><span class="sxs-lookup"><span data-stu-id="dd4a3-119">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="dd4a3-120">Zadaná cesta je cesta modulu zobrazení, která je relativní cesta kořenového stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="dd4a3-120">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="dd4a3-121">K určení [zásad autorizace](xref:security/authorization/policies), použijte [AuthorizeFolder přetížení](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span><span class="sxs-lookup"><span data-stu-id="dd4a3-121">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="dd4a3-122">Vyžaduje autorizaci pro přístup k stránku oblasti</span><span class="sxs-lookup"><span data-stu-id="dd4a3-122">Require authorization to access an area page</span></span>

<span data-ttu-id="dd4a3-123">Použití <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> konvence prostřednictvím <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> přidat <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> stránku oblasti v zadané cestě:</span><span class="sxs-lookup"><span data-stu-id="dd4a3-123">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="dd4a3-124">Název stránky je cesta k souboru bez přípony vzhledem k stránky kořenový adresář pro zadanou oblast.</span><span class="sxs-lookup"><span data-stu-id="dd4a3-124">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="dd4a3-125">Například název stránky pro soubor *Areas/Identity/Pages/Manage/Accounts.cshtml* je */Správa nebo účty*.</span><span class="sxs-lookup"><span data-stu-id="dd4a3-125">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="dd4a3-126">K určení [zásad autorizace](xref:security/authorization/policies), použijte [AuthorizeAreaPage přetížení](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span><span class="sxs-lookup"><span data-stu-id="dd4a3-126">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="dd4a3-127">Vyžaduje autorizaci pro přístup ke složce oblastí</span><span class="sxs-lookup"><span data-stu-id="dd4a3-127">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="dd4a3-128">Použití <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> konvence prostřednictvím <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> přidat <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> pro všechny oblasti ve složce v zadané cestě:</span><span class="sxs-lookup"><span data-stu-id="dd4a3-128">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="dd4a3-129">Cesta ke složce je cesta ke složce relativní k stránky kořenový adresář pro zadanou oblast.</span><span class="sxs-lookup"><span data-stu-id="dd4a3-129">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="dd4a3-130">Například cesta ke složce pro soubory v rámci *oblasti/Identity/stránek/Správa nebo* je *a správa*.</span><span class="sxs-lookup"><span data-stu-id="dd4a3-130">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="dd4a3-131">K určení [zásad autorizace](xref:security/authorization/policies), použijte [AuthorizeAreaFolder přetížení](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span><span class="sxs-lookup"><span data-stu-id="dd4a3-131">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="dd4a3-132">Povolit anonymní přístup na stránku</span><span class="sxs-lookup"><span data-stu-id="dd4a3-132">Allow anonymous access to a page</span></span>

<span data-ttu-id="dd4a3-133">Použití <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> konvence prostřednictvím <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> přidat <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> na stránku v zadané cestě:</span><span class="sxs-lookup"><span data-stu-id="dd4a3-133">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="dd4a3-134">Zadaná cesta je cesta modulu zobrazení, která je relativní cesta kořenového stránky Razor bez rozšíření a který obsahuje pouze lomítka.</span><span class="sxs-lookup"><span data-stu-id="dd4a3-134">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="dd4a3-135">Povolit anonymní přístup do složky stránek</span><span class="sxs-lookup"><span data-stu-id="dd4a3-135">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="dd4a3-136">Použití <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> konvence prostřednictvím <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> přidat <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> na všechny stránky ve složce v zadané cestě:</span><span class="sxs-lookup"><span data-stu-id="dd4a3-136">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="dd4a3-137">Zadaná cesta je cesta modulu zobrazení, která je relativní cesta kořenového stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="dd4a3-137">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="dd4a3-138">Poznámky ke kombinování oprávnění a anonymní přístup</span><span class="sxs-lookup"><span data-stu-id="dd4a3-138">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="dd4a3-139">Je možné určit, že složka stránek, které vyžadují autorizace a, než určovat, jestli stránky v rámci dané složky umožňuje anonymní přístup:</span><span class="sxs-lookup"><span data-stu-id="dd4a3-139">It's valid to specify that a folder of pages that require authorization and than specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="dd4a3-140">Naopak, ale není platný.</span><span class="sxs-lookup"><span data-stu-id="dd4a3-140">The reverse, however, isn't valid.</span></span> <span data-ttu-id="dd4a3-141">Nelze deklarovat složku stránek pro anonymní přístup a pak zadejte na stránce v rámci dané složky, která vyžaduje ověření:</span><span class="sxs-lookup"><span data-stu-id="dd4a3-141">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="dd4a3-142">Vyžadování autorizace na stránce privátní nezdaří.</span><span class="sxs-lookup"><span data-stu-id="dd4a3-142">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="dd4a3-143">Když i <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> a <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> se použijí na stránce <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> má přednost a řídí přístup.</span><span class="sxs-lookup"><span data-stu-id="dd4a3-143">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dd4a3-144">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="dd4a3-144">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

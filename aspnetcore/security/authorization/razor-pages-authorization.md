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
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a>Razor Pages autorizační konvence v ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

Jedním ze způsobů, jak řídit přístup v aplikaci Razor Pages, je použití autorizačních konvencí při spuštění. Tyto konvence umožňují uživatelům ověřovat a povolit anonymním uživatelům přístup k jednotlivým stránkám nebo složkám stránek. Konvence popsané v tomto tématu automaticky použijí [filtry](xref:mvc/controllers/filters#authorization-filters) pro autorizaci k řízení přístupu.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([stažení](xref:index#how-to-download-a-sample))

Ukázková aplikace používá [ověřování souborem cookie bez ASP.NET Core identity](xref:security/authentication/cookie). Koncepty a příklady uvedené v tomto tématu platí stejně jako aplikace, které používají ASP.NET Core identity. Pokud chcete použít ASP.NET Core identity, postupujte podle pokynů <xref:security/authentication/identity>v části.

## <a name="require-authorization-to-access-a-page"></a>Vyžadovat autorizaci pro přístup ke stránce

K přidání <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> nastránkuna<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> zadané cestě použijte konvenci pomocí nástroje:

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

Zadaná cesta je cesta k modulu zobrazení, která je Razor Pages relativní cesta ke kořenu bez přípony a obsahuje pouze lomítka.

Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizePage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> Lze použít na třídu modelu stránky `[Authorize]` s atributem Filter. <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> Další informace najdete v tématu [autorizace atributu Filter](xref:razor-pages/filter#authorize-filter-attribute).

## <a name="require-authorization-to-access-a-folder-of-pages"></a>Vyžadovat autorizaci pro přístup ke složce stránek

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> Použijte konvenci<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> pomocí nástroje apřidejtedovšechstránekvesložcevzadanécestě:<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

Zadaná cesta je cesta k modulu zobrazení, která je Razor Pages relativní cesta ke kořenu.

Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a>Vyžadovat autorizaci pro přístup na stránku oblasti

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> Použijte konvenci<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> pomocí nástroje apřidejtenastránkuoblastivzadanécestě:<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

Název stránky je cesta k souboru bez přípony vzhledem k kořenovému adresáři stránek zadané oblasti. Například název stránky pro *oblasti souboru/identita, stránky/Správa/účty. cshtml* je */Manage/Accounts*.

Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeAreaPage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a>Vyžadovat autorizaci pro přístup ke složce oblastí

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> Použijte konvenci<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> pomocí nástroje apřidejtedovšechoblastívesložcevzadanécestě:<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

Cesta ke složce je cesta ke složce vzhledem k kořenovému adresáři stránek zadané oblasti. Například cesta ke složce pro soubory v *oblasti/identity/stránky/spravovat/* je */Manage*.

Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeAreaFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a>Povolení anonymního přístupu na stránku

K přidání <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> nastránkuna<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> zadané cestě použijte konvenci pomocí nástroje:

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

Zadaná cesta je cesta k modulu zobrazení, která je Razor Pages relativní cesta ke kořenu bez přípony a obsahuje pouze lomítka.

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>Povolení anonymního přístupu ke složce stránek

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> Použijte konvenci<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> pomocí nástroje apřidejtedovšechstránekvesložcevzadanécestě:<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

Zadaná cesta je cesta k modulu zobrazení, která je Razor Pages relativní cesta ke kořenu.

## <a name="note-on-combining-authorized-and-anonymous-access"></a>Poznámka o kombinování autorizovaných a anonymních přístupů

Je platný, pokud chcete určit složku stránek, které vyžadují autorizaci, a než určit, že stránka v této složce povoluje anonymní přístup:

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

Zpětný chod ale není platný. Nemůžete deklarovat složku stránek pro anonymní přístup a pak zadat stránku v této složce, která vyžaduje autorizaci:

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

Požadavek na autorizaci na soukromé stránce se nezdařil. Pokud jsou <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> a aplikovány na stránku, má přednost a řídí přístup. <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>

## <a name="additional-resources"></a>Další zdroje

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Jedním ze způsobů, jak řídit přístup v aplikaci Razor Pages, je použití autorizačních konvencí při spuštění. Tyto konvence umožňují uživatelům ověřovat a povolit anonymním uživatelům přístup k jednotlivým stránkám nebo složkám stránek. Konvence popsané v tomto tématu automaticky použijí [filtry](xref:mvc/controllers/filters#authorization-filters) pro autorizaci k řízení přístupu.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([stažení](xref:index#how-to-download-a-sample))

Ukázková aplikace používá [ověřování souborem cookie bez ASP.NET Core identity](xref:security/authentication/cookie). Koncepty a příklady uvedené v tomto tématu platí stejně jako aplikace, které používají ASP.NET Core identity. Pokud chcete použít ASP.NET Core identity, postupujte podle pokynů <xref:security/authentication/identity>v části.

## <a name="require-authorization-to-access-a-page"></a>Vyžadovat autorizaci pro přístup ke stránce

K přidání <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> nastránkuna<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> zadané cestě použijte konvenci pomocí nástroje:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

Zadaná cesta je cesta k modulu zobrazení, která je Razor Pages relativní cesta ke kořenu bez přípony a obsahuje pouze lomítka.

Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizePage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> Lze použít na třídu modelu stránky `[Authorize]` s atributem Filter. <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> Další informace najdete v tématu [autorizace atributu Filter](xref:razor-pages/filter#authorize-filter-attribute).

## <a name="require-authorization-to-access-a-folder-of-pages"></a>Vyžadovat autorizaci pro přístup ke složce stránek

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> Použijte konvenci<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> pomocí nástroje apřidejtedovšechstránekvesložcevzadanécestě:<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

Zadaná cesta je cesta k modulu zobrazení, která je Razor Pages relativní cesta ke kořenu.

Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a>Vyžadovat autorizaci pro přístup na stránku oblasti

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> Použijte konvenci<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> pomocí nástroje apřidejtenastránkuoblastivzadanécestě:<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

Název stránky je cesta k souboru bez přípony vzhledem k kořenovému adresáři stránek zadané oblasti. Například název stránky pro *oblasti souboru/identita, stránky/Správa/účty. cshtml* je */Manage/Accounts*.

Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeAreaPage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a>Vyžadovat autorizaci pro přístup ke složce oblastí

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> Použijte konvenci<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> pomocí nástroje apřidejtedovšechoblastívesložcevzadanécestě:<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

Cesta ke složce je cesta ke složce vzhledem k kořenovému adresáři stránek zadané oblasti. Například cesta ke složce pro soubory v *oblasti/identity/stránky/spravovat/* je */Manage*.

Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeAreaFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a>Povolení anonymního přístupu na stránku

K přidání <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> nastránkuna<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> zadané cestě použijte konvenci pomocí nástroje:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

Zadaná cesta je cesta k modulu zobrazení, která je Razor Pages relativní cesta ke kořenu bez přípony a obsahuje pouze lomítka.

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>Povolení anonymního přístupu ke složce stránek

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> Použijte konvenci<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> pomocí nástroje apřidejtedovšechstránekvesložcevzadanécestě:<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

Zadaná cesta je cesta k modulu zobrazení, která je Razor Pages relativní cesta ke kořenu.

## <a name="note-on-combining-authorized-and-anonymous-access"></a>Poznámka o kombinování autorizovaných a anonymních přístupů

Je platný, pokud chcete určit složku stránek, které vyžadují autorizaci, a než určit, že stránka v této složce povoluje anonymní přístup:

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

Zpětný chod ale není platný. Nemůžete deklarovat složku stránek pro anonymní přístup a pak zadat stránku v této složce, která vyžaduje autorizaci:

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

Požadavek na autorizaci na soukromé stránce se nezdařil. Pokud jsou <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> a aplikovány na stránku, má přednost a řídí přístup. <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>

## <a name="additional-resources"></a>Další zdroje

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

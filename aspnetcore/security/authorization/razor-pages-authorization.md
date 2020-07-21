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
ms.openlocfilehash: 0f4022e46993c6a957d3d0c133b3db56fa650edc
ms.sourcegitcommit: d9ae1f352d372a20534b57e23646c1a1d9171af1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/21/2020
ms.locfileid: "86568818"
---
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a>RazorAutorizační konvence stránek v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Jedním ze způsobů, jak řídit přístup v Razor aplikaci Pages, je použít při spuštění konvence autorizace. Tyto konvence umožňují uživatelům ověřovat a povolit anonymním uživatelům přístup k jednotlivým stránkám nebo složkám stránek. Konvence popsané v tomto tématu automaticky použijí [filtry pro autorizaci](xref:mvc/controllers/filters#authorization-filters) k řízení přístupu.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Ukázková aplikace používá [ověřování souborem cookie bez Identity ASP.NET Core ](xref:security/authentication/cookie). Koncepty a příklady uvedené v tomto tématu platí stejně jako aplikace, které používají ASP.NET Core Identity . Pokud chcete použít ASP.NET Core Identity , postupujte podle pokynů v části <xref:security/authentication/identity> .

## <a name="require-authorization-to-access-a-page"></a>Vyžadovat autorizaci pro přístup ke stránce

K <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> Přidání <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> na stránku na zadané cestě použijte konvenci:

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=3)]

Zadaná cesta je cesta k modulu zobrazení, která je Razor relativní cestou ke kořenu stránek bez přípony a obsahuje pouze lomítka.

Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizePage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>Lze použít na třídu modelu stránky s `[Authorize]` atributem Filter. Další informace najdete v tématu [autorizace atributu Filter](xref:razor-pages/filter#authorize-filter-attribute).

## <a name="require-authorization-to-access-a-folder-of-pages"></a>Vyžadovat autorizaci pro přístup ke složce stránek

Pomocí <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> konvence přidejte <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> do všech stránek ve složce v zadané cestě:

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=4)]

Zadaná cesta je cesta k modulu zobrazení, což je Razor relativní cesta ke kořeni stránek.

Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a>Vyžadovat autorizaci pro přístup na stránku oblasti

Pomocí <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> konvence přidejte <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> na stránku oblasti v zadané cestě:

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

Název stránky je cesta k souboru bez přípony vzhledem k kořenovému adresáři stránek zadané oblasti. Například název stránky pro *oblasti souboru/ Identity /Pages/Manage/accounts.cshtml* je */Manage/Accounts*.

Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeAreaPage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a>Vyžadovat autorizaci pro přístup ke složce oblastí

K <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> Přidání <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> do všech oblastí ve složce v zadané cestě použijte konvenci:

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

Cesta ke složce je cesta ke složce vzhledem k kořenovému adresáři stránek zadané oblasti. Například cesta ke složce pro soubory v *oblasti oblasti/ Identity /Pages/Manage/* je */Manage*.

Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeAreaFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a>Povolení anonymního přístupu na stránku

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> K přidání na stránku na zadané cestě použijte konvenci:

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=5)]

Zadaná cesta je cesta k modulu zobrazení, která je Razor relativní cestou ke kořenu stránek bez přípony a obsahuje pouze lomítka.

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>Povolení anonymního přístupu ke složce stránek

Pomocí <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> konvence přidejte <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> do všech stránek ve složce v zadané cestě:

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=6)]

Zadaná cesta je cesta k modulu zobrazení, což je Razor relativní cesta ke kořeni stránek.

## <a name="note-on-combining-authorized-and-anonymous-access"></a>Poznámka o kombinování autorizovaných a anonymních přístupů

Je platný, pokud chcete určit, že složka stránek vyžaduje autorizaci, a pak určit, že stránka v této složce umožňuje anonymní přístup:

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

Zpětný chod ale není platný. Nemůžete deklarovat složku stránek pro anonymní přístup a pak zadat stránku v této složce, která vyžaduje autorizaci:

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

Požadavek na autorizaci na soukromé stránce se nezdařil. Pokud <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> jsou a aplikovány na stránku, má <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> přednost a řídí přístup.

## <a name="additional-resources"></a>Další zdroje

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Jedním ze způsobů, jak řídit přístup v Razor aplikaci Pages, je použít při spuštění konvence autorizace. Tyto konvence umožňují uživatelům ověřovat a povolit anonymním uživatelům přístup k jednotlivým stránkám nebo složkám stránek. Konvence popsané v tomto tématu automaticky použijí [filtry pro autorizaci](xref:mvc/controllers/filters#authorization-filters) k řízení přístupu.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Ukázková aplikace používá [ověřování souborem cookie bez Identity ASP.NET Core ](xref:security/authentication/cookie). Koncepty a příklady uvedené v tomto tématu platí stejně jako aplikace, které používají ASP.NET Core Identity . Pokud chcete použít ASP.NET Core Identity , postupujte podle pokynů v části <xref:security/authentication/identity> .

## <a name="require-authorization-to-access-a-page"></a>Vyžadovat autorizaci pro přístup ke stránce

K <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Přidání <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> na stránku na zadané cestě použijte konvenci pomocí nástroje:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

Zadaná cesta je cesta k modulu zobrazení, která je Razor relativní cestou ke kořenu stránek bez přípony a obsahuje pouze lomítka.

Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizePage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>Lze použít na třídu modelu stránky s `[Authorize]` atributem Filter. Další informace najdete v tématu [autorizace atributu Filter](xref:razor-pages/filter#authorize-filter-attribute).

## <a name="require-authorization-to-access-a-folder-of-pages"></a>Vyžadovat autorizaci pro přístup ke složce stránek

Použijte <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> konvenci pomocí nástroje <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> a přidejte <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> do všech stránek ve složce v zadané cestě:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

Zadaná cesta je cesta k modulu zobrazení, což je Razor relativní cesta ke kořeni stránek.

Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a>Vyžadovat autorizaci pro přístup na stránku oblasti

Použijte <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> konvenci pomocí nástroje <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> a přidejte <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> na stránku oblasti v zadané cestě:

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

Název stránky je cesta k souboru bez přípony vzhledem k kořenovému adresáři stránek zadané oblasti. Například název stránky pro *oblasti souboru/ Identity /Pages/Manage/accounts.cshtml* je */Manage/Accounts*.

Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeAreaPage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a>Vyžadovat autorizaci pro přístup ke složce oblastí

Použijte <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> konvenci pomocí nástroje <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> a přidejte <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> do všech oblastí ve složce v zadané cestě:

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

Cesta ke složce je cesta ke složce vzhledem k kořenovému adresáři stránek zadané oblasti. Například cesta ke složce pro soubory v *oblasti oblasti/ Identity /Pages/Manage/* je */Manage*.

Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeAreaFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a>Povolení anonymního přístupu na stránku

K <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Přidání <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> na stránku na zadané cestě použijte konvenci pomocí nástroje:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

Zadaná cesta je cesta k modulu zobrazení, která je Razor relativní cestou ke kořenu stránek bez přípony a obsahuje pouze lomítka.

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>Povolení anonymního přístupu ke složce stránek

Použijte <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> konvenci pomocí nástroje <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> a přidejte <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> do všech stránek ve složce v zadané cestě:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

Zadaná cesta je cesta k modulu zobrazení, což je Razor relativní cesta ke kořeni stránek.

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

Požadavek na autorizaci na soukromé stránce se nezdařil. Pokud <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> jsou a aplikovány na stránku, má <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> přednost a řídí přístup.

## <a name="additional-resources"></a>Další materiály

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

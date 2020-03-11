---
title: Razor Pages autorizační konvence v ASP.NET Core
author: rick-anderson
description: Naučte se řídit přístup ke stránkám pomocí konvencí, které autorizují uživatele a umožňují anonymním uživatelům přístup k stránkám nebo složkám stránek.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: 00fc487c6ac802f213bcf83994ecc2b1a1468589
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78662055"
---
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a>Razor Pages autorizační konvence v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Jedním ze způsobů, jak řídit přístup v aplikaci Razor Pages, je použití autorizačních konvencí při spuštění. Tyto konvence umožňují uživatelům ověřovat a povolit anonymním uživatelům přístup k jednotlivým stránkám nebo složkám stránek. Konvence popsané v tomto tématu automaticky použijí [filtry pro autorizaci](xref:mvc/controllers/filters#authorization-filters) k řízení přístupu.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Ukázková aplikace používá [ověřování souborem cookie bez ASP.NET Core identity](xref:security/authentication/cookie). Koncepty a příklady uvedené v tomto tématu platí stejně jako aplikace, které používají ASP.NET Core identity. Pokud chcete použít ASP.NET Core identity, postupujte podle pokynů v <xref:security/authentication/identity>.

## <a name="require-authorization-to-access-a-page"></a>Vyžadovat autorizaci pro přístup ke stránce

Pomocí <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> konvence prostřednictvím <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> přidejte do stránky <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> na zadané cestě:

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

Zadaná cesta je cesta k modulu zobrazení, která je Razor Pages relativní cesta ke kořenu bez přípony a obsahuje pouze lomítka.

Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizePage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> lze použít pro třídu modelu stránky s atributem filtru `[Authorize]`. Další informace najdete v tématu [autorizace atributu Filter](xref:razor-pages/filter#authorize-filter-attribute).

## <a name="require-authorization-to-access-a-folder-of-pages"></a>Vyžadovat autorizaci pro přístup ke složce stránek

Pomocí <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> konvence prostřednictvím <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> přidejte <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> na všechny stránky ve složce v zadané cestě:

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

Zadaná cesta je cesta k modulu zobrazení, která je Razor Pages relativní cesta ke kořenu.

Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a>Vyžadovat autorizaci pro přístup na stránku oblasti

Pomocí <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> konvence prostřednictvím <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> přidejte <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> na stránku oblasti v zadané cestě:

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

Název stránky je cesta k souboru bez přípony vzhledem k kořenovému adresáři stránek zadané oblasti. Například název stránky pro *oblasti souboru/identita, stránky/Správa/účty. cshtml* je */Manage/Accounts*.

Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeAreaPage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a>Vyžadovat autorizaci pro přístup ke složce oblastí

Pomocí <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> konvence prostřednictvím <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> přidejte <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> ke všem oblastem ve složce v zadané cestě:

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

Cesta ke složce je cesta ke složce vzhledem k kořenovému adresáři stránek zadané oblasti. Například cesta ke složce pro soubory v *oblasti/identity/stránky/spravovat/* je */Manage*.

Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeAreaFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a>Povolení anonymního přístupu na stránku

Pomocí <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> konvence prostřednictvím <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> přidejte <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> na stránku v zadané cestě:

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

Zadaná cesta je cesta k modulu zobrazení, která je Razor Pages relativní cesta ke kořenu bez přípony a obsahuje pouze lomítka.

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>Povolení anonymního přístupu ke složce stránek

Pomocí <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> konvence prostřednictvím <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> přidejte <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> na všechny stránky ve složce v zadané cestě:

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

Zadaná cesta je cesta k modulu zobrazení, která je Razor Pages relativní cesta ke kořenu.

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

Požadavek na autorizaci na soukromé stránce se nezdařil. Když se na stránku aplikují <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> i <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>, má <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> přednost a řídí přístup.

## <a name="additional-resources"></a>Další zdroje

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Jedním ze způsobů, jak řídit přístup v aplikaci Razor Pages, je použití autorizačních konvencí při spuštění. Tyto konvence umožňují uživatelům ověřovat a povolit anonymním uživatelům přístup k jednotlivým stránkám nebo složkám stránek. Konvence popsané v tomto tématu automaticky použijí [filtry pro autorizaci](xref:mvc/controllers/filters#authorization-filters) k řízení přístupu.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Ukázková aplikace používá [ověřování souborem cookie bez ASP.NET Core identity](xref:security/authentication/cookie). Koncepty a příklady uvedené v tomto tématu platí stejně jako aplikace, které používají ASP.NET Core identity. Pokud chcete použít ASP.NET Core identity, postupujte podle pokynů v <xref:security/authentication/identity>.

## <a name="require-authorization-to-access-a-page"></a>Vyžadovat autorizaci pro přístup ke stránce

Pomocí <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> konvence prostřednictvím <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> přidejte do stránky <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> na zadané cestě:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

Zadaná cesta je cesta k modulu zobrazení, která je Razor Pages relativní cesta ke kořenu bez přípony a obsahuje pouze lomítka.

Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizePage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> lze použít pro třídu modelu stránky s atributem filtru `[Authorize]`. Další informace najdete v tématu [autorizace atributu Filter](xref:razor-pages/filter#authorize-filter-attribute).

## <a name="require-authorization-to-access-a-folder-of-pages"></a>Vyžadovat autorizaci pro přístup ke složce stránek

Pomocí <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> konvence prostřednictvím <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> přidejte <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> na všechny stránky ve složce v zadané cestě:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

Zadaná cesta je cesta k modulu zobrazení, která je Razor Pages relativní cesta ke kořenu.

Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a>Vyžadovat autorizaci pro přístup na stránku oblasti

Pomocí <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> konvence prostřednictvím <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> přidejte <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> na stránku oblasti v zadané cestě:

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

Název stránky je cesta k souboru bez přípony vzhledem k kořenovému adresáři stránek zadané oblasti. Například název stránky pro *oblasti souboru/identita, stránky/Správa/účty. cshtml* je */Manage/Accounts*.

Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeAreaPage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a>Vyžadovat autorizaci pro přístup ke složce oblastí

Pomocí <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> konvence prostřednictvím <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> přidejte <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> ke všem oblastem ve složce v zadané cestě:

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

Cesta ke složce je cesta ke složce vzhledem k kořenovému adresáři stránek zadané oblasti. Například cesta ke složce pro soubory v *oblasti/identity/stránky/spravovat/* je */Manage*.

Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeAreaFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a>Povolení anonymního přístupu na stránku

Pomocí <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> konvence prostřednictvím <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> přidejte <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> na stránku v zadané cestě:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

Zadaná cesta je cesta k modulu zobrazení, která je Razor Pages relativní cesta ke kořenu bez přípony a obsahuje pouze lomítka.

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>Povolení anonymního přístupu ke složce stránek

Pomocí <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> konvence prostřednictvím <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> přidejte <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> na všechny stránky ve složce v zadané cestě:

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

Požadavek na autorizaci na soukromé stránce se nezdařil. Když se na stránku aplikují <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> i <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>, má <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> přednost a řídí přístup.

## <a name="additional-resources"></a>Další zdroje

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

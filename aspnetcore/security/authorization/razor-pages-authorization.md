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
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a>Vytváření názvů autorizace stránek Razor v ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex)

Jedním ze způsobů řízení přístupu v aplikaci pro stránky Razor je použití autorizace konvence při spuštění. Tato konvence umožňují uživateli uživatele autorizovat a umožňoval anonymním uživatelům přístup k jednotlivé stránky nebo složky stránek. Platí zásady popsané v tomto tématu automaticky [filtry autorizace](xref:mvc/controllers/filters#authorization-filters) pro řízení přístupu.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([stažení](xref:index#how-to-download-a-sample))

Tato ukázková aplikace používá [ověřování souborem cookie bez ASP.NET Core Identity](xref:security/authentication/cookie). Koncepty a příkladů uvedených v tomto tématu platí stejně pro aplikace, které používají technologii ASP.NET Core Identity. ASP.NET Core Identity, postupujte podle pokynů v <xref:security/authentication/identity>.

## <a name="require-authorization-to-access-a-page"></a>Vyžaduje povolení ke stránce

Použití <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> konvence prostřednictvím <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> přidat <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> na stránku v zadané cestě:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

Zadaná cesta je cesta modulu zobrazení, která je relativní cesta kořenového stránky Razor bez rozšíření a který obsahuje pouze lomítka.

K určení [zásad autorizace](xref:security/authorization/policies), použijte [AuthorizePage přetížení](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> Lze použít u třídy modelu stránky s `[Authorize]` atribut filtru. Další informace najdete v tématu [atribut filtru Authorize](xref:razor-pages/filter#authorize-filter-attribute).

## <a name="require-authorization-to-access-a-folder-of-pages"></a>Vyžaduje autorizaci pro přístup ke složce stránek

Použití <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> konvence prostřednictvím <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> přidat <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> na všechny stránky ve složce v zadané cestě:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

Zadaná cesta je cesta modulu zobrazení, která je relativní cesta kořenového stránky Razor.

K určení [zásad autorizace](xref:security/authorization/policies), použijte [AuthorizeFolder přetížení](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a>Vyžaduje autorizaci pro přístup k stránku oblasti

Použití <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> konvence prostřednictvím <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> přidat <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> stránku oblasti v zadané cestě:

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

Název stránky je cesta k souboru bez přípony vzhledem k stránky kořenový adresář pro zadanou oblast. Například název stránky pro soubor *Areas/Identity/Pages/Manage/Accounts.cshtml* je */Správa nebo účty*.

K určení [zásad autorizace](xref:security/authorization/policies), použijte [AuthorizeAreaPage přetížení](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a>Vyžaduje autorizaci pro přístup ke složce oblastí

Použití <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> konvence prostřednictvím <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> přidat <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> pro všechny oblasti ve složce v zadané cestě:

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

Cesta ke složce je cesta ke složce relativní k stránky kořenový adresář pro zadanou oblast. Například cesta ke složce pro soubory v rámci *oblasti/Identity/stránek/Správa nebo* je *a správa*.

K určení [zásad autorizace](xref:security/authorization/policies), použijte [AuthorizeAreaFolder přetížení](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a>Povolit anonymní přístup na stránku

Použití <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> konvence prostřednictvím <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> přidat <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> na stránku v zadané cestě:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

Zadaná cesta je cesta modulu zobrazení, která je relativní cesta kořenového stránky Razor bez rozšíření a který obsahuje pouze lomítka.

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>Povolit anonymní přístup do složky stránek

Použití <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> konvence prostřednictvím <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> přidat <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> na všechny stránky ve složce v zadané cestě:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

Zadaná cesta je cesta modulu zobrazení, která je relativní cesta kořenového stránky Razor.

## <a name="note-on-combining-authorized-and-anonymous-access"></a>Poznámky ke kombinování oprávnění a anonymní přístup

Je možné určit, že složka stránek, které vyžadují autorizace a, než určovat, jestli stránky v rámci dané složky umožňuje anonymní přístup:

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

Naopak, ale není platný. Nelze deklarovat složku stránek pro anonymní přístup a pak zadejte na stránce v rámci dané složky, která vyžaduje ověření:

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

Vyžadování autorizace na stránce privátní nezdaří. Když i <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> a <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> se použijí na stránce <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> má přednost a řídí přístup.

## <a name="additional-resources"></a>Další zdroje

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

---
title: Články založené na ASP.NET Core projektech vytvořených pomocí individuálních uživatelských účtů
author: rick-anderson
description: Objevte články na základě ASP.NET Core projektů vytvořených pomocí individuálních uživatelských účtů.
ms.author: riande
ms.date: 11/30/2017
uid: security/authentication/individual
ms.openlocfilehash: cf548417268a8587787471b9ed91c0ed109fbee9
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080705"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a><span data-ttu-id="0ac4e-103">Články založené na ASP.NET Core projektech vytvořených pomocí individuálních uživatelských účtů</span><span class="sxs-lookup"><span data-stu-id="0ac4e-103">Articles based on ASP.NET Core projects created with individual user accounts</span></span>

<span data-ttu-id="0ac4e-104">ASP.NET Core identita je zahrnuta v šablonách projektů v aplikaci Visual Studio s možností jednotlivé uživatelské účty.</span><span class="sxs-lookup"><span data-stu-id="0ac4e-104">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="0ac4e-105">Šablony ověřování jsou dostupné v .NET Core CLI s `-au Individual`:</span><span class="sxs-lookup"><span data-stu-id="0ac4e-105">The authentication templates are available in .NET Core CLI with `-au Individual`:</span></span>

::: moniker range=">= aspnetcore-2.1"

```dotnetcli
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```dotnetcli
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

<span data-ttu-id="0ac4e-106">Podívejte se na [Tento problém GitHubu](https://github.com/aspnet/AspNetCore/issues/5833) pro ověřování webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="0ac4e-106">See [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/5833) for web API authentication.</span></span>

<a name="no"></a>

## <a name="no-authentication"></a><span data-ttu-id="0ac4e-107">Bez ověřování</span><span class="sxs-lookup"><span data-stu-id="0ac4e-107">No Authentication</span></span>

<span data-ttu-id="0ac4e-108">Ověřování je zadáno v .NET Core CLI s `-au` možností.</span><span class="sxs-lookup"><span data-stu-id="0ac4e-108">Authentication is specified in the .NET Core CLI with the `-au` option.</span></span> <span data-ttu-id="0ac4e-109">V aplikaci Visual Studio je k dispozici dialog **Změna ověřování** pro nové webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="0ac4e-109">In Visual Studio, the **Change Authentication** dialog is available for new web applications.</span></span> <span data-ttu-id="0ac4e-110">Výchozí pro nové webové aplikace v aplikaci Visual Studio není **ověřování**.</span><span class="sxs-lookup"><span data-stu-id="0ac4e-110">The default for new web apps in Visual Studio is **No Authentication**.</span></span>

<span data-ttu-id="0ac4e-111">Projekty vytvořené bez ověřování:</span><span class="sxs-lookup"><span data-stu-id="0ac4e-111">Projects created with no authentication:</span></span>

* <span data-ttu-id="0ac4e-112">Neobsahujte webové stránky a uživatelské rozhraní pro přihlášení a odhlášení.</span><span class="sxs-lookup"><span data-stu-id="0ac4e-112">Don't contain web pages and UI to sign in and sign out.</span></span>
* <span data-ttu-id="0ac4e-113">Neobsahujte ověřovací kód.</span><span class="sxs-lookup"><span data-stu-id="0ac4e-113">Don't contain authentication code.</span></span>

<a name="win"></a>

## <a name="windows-authentication"></a><span data-ttu-id="0ac4e-114">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="0ac4e-114">Windows Authentication</span></span>

<span data-ttu-id="0ac4e-115">Ověřování systému Windows je zadáno pro nové webové aplikace v .NET Core CLI s `-au Windows` možností.</span><span class="sxs-lookup"><span data-stu-id="0ac4e-115">Windows Authentication is specified for new web apps in the .NET Core CLI with the `-au Windows` option.</span></span> <span data-ttu-id="0ac4e-116">V aplikaci Visual Studio je v dialogovém okně pro **změnu ověřování** k dispozici možnost **ověřování systému Windows** .</span><span class="sxs-lookup"><span data-stu-id="0ac4e-116">In Visual Studio, the **Change Authentication** dialog provides the **Windows Authentication** options.</span></span>

<span data-ttu-id="0ac4e-117">Pokud je vybraná možnost ověřování systému Windows, aplikace je nakonfigurovaná tak, aby používala [modul služby IIS pro ověřování systému Windows](xref:host-and-deploy/iis/modules).</span><span class="sxs-lookup"><span data-stu-id="0ac4e-117">If Windows Authentication is selected, the app is configured to use the [Windows Authentication IIS module](xref:host-and-deploy/iis/modules).</span></span> <span data-ttu-id="0ac4e-118">Ověřování systému Windows je určeno pro intranetové weby.</span><span class="sxs-lookup"><span data-stu-id="0ac4e-118">Windows Authentication is intended for Intranet web sites.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0ac4e-119">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="0ac4e-119">Additional resources</span></span>

<span data-ttu-id="0ac4e-120">V následujících článcích se dozvíte, jak používat kód vygenerovaný v ASP.NET Core šablonách, které používají jednotlivé uživatelské účty:</span><span class="sxs-lookup"><span data-stu-id="0ac4e-120">The following articles show how to use the code generated in ASP.NET Core templates that use individual user accounts:</span></span>

* [<span data-ttu-id="0ac4e-121">Dvoufaktorové ověřování přes SMS</span><span class="sxs-lookup"><span data-stu-id="0ac4e-121">Two-factor authentication with SMS</span></span>](xref:security/authentication/2fa)
* [<span data-ttu-id="0ac4e-122">Potvrzení účtu a obnovení hesla v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0ac4e-122">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="0ac4e-123">Vytvoření aplikace ASP.NET Core s uživatelskými daty chráněnými autorizací</span><span class="sxs-lookup"><span data-stu-id="0ac4e-123">Create an ASP.NET Core app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)

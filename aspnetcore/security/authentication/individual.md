---
title: Články založené na ASP.NET Core projektech vytvořených pomocí individuálních uživatelských účtů
author: rick-anderson
description: Objevte články na základě ASP.NET Core projektů vytvořených pomocí individuálních uživatelských účtů.
ms.author: riande
ms.date: 11/30/2017
uid: security/authentication/individual
ms.openlocfilehash: 91c5665dc50124b3ba09bdcfbf3ba501f684c604
ms.sourcegitcommit: 9e85c2562df5e108d7933635c830297f484bb775
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73463033"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a><span data-ttu-id="bd108-103">Články založené na ASP.NET Core projektech vytvořených pomocí individuálních uživatelských účtů</span><span class="sxs-lookup"><span data-stu-id="bd108-103">Articles based on ASP.NET Core projects created with individual user accounts</span></span>

<span data-ttu-id="bd108-104">ASP.NET Core identita je zahrnuta v šablonách projektů v aplikaci Visual Studio s možností jednotlivé uživatelské účty.</span><span class="sxs-lookup"><span data-stu-id="bd108-104">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="bd108-105">Šablony ověřování jsou dostupné v .NET Core CLI s `-au Individual`:</span><span class="sxs-lookup"><span data-stu-id="bd108-105">The authentication templates are available in .NET Core CLI with `-au Individual`:</span></span>

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

<span data-ttu-id="bd108-106">Podívejte se na [Tento problém GitHubu](https://github.com/aspnet/AspNetCore/issues/5833) pro ověřování webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="bd108-106">See [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/5833) for web API authentication.</span></span>

<a name="no"></a>

## <a name="no-authentication"></a><span data-ttu-id="bd108-107">Bez ověřování</span><span class="sxs-lookup"><span data-stu-id="bd108-107">No Authentication</span></span>

<span data-ttu-id="bd108-108">Ověřování je zadáno v .NET Core CLI s možností `-au`.</span><span class="sxs-lookup"><span data-stu-id="bd108-108">Authentication is specified in the .NET Core CLI with the `-au` option.</span></span> <span data-ttu-id="bd108-109">V aplikaci Visual Studio je k dispozici dialog **Změna ověřování** pro nové webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="bd108-109">In Visual Studio, the **Change Authentication** dialog is available for new web applications.</span></span> <span data-ttu-id="bd108-110">Výchozí pro nové webové aplikace v aplikaci Visual Studio není **ověřování**.</span><span class="sxs-lookup"><span data-stu-id="bd108-110">The default for new web apps in Visual Studio is **No Authentication**.</span></span>

<span data-ttu-id="bd108-111">Projekty vytvořené bez ověřování:</span><span class="sxs-lookup"><span data-stu-id="bd108-111">Projects created with no authentication:</span></span>

* <span data-ttu-id="bd108-112">Neobsahujte webové stránky a uživatelské rozhraní pro přihlášení a odhlášení.</span><span class="sxs-lookup"><span data-stu-id="bd108-112">Don't contain web pages and UI to sign in and sign out.</span></span>
* <span data-ttu-id="bd108-113">Neobsahujte ověřovací kód.</span><span class="sxs-lookup"><span data-stu-id="bd108-113">Don't contain authentication code.</span></span>

<a name="win"></a>

## <a name="windows-authentication"></a><span data-ttu-id="bd108-114">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="bd108-114">Windows Authentication</span></span>

<span data-ttu-id="bd108-115">Ověřování systému Windows je zadáno pro nové webové aplikace v .NET Core CLI s možností `-au Windows`.</span><span class="sxs-lookup"><span data-stu-id="bd108-115">Windows Authentication is specified for new web apps in the .NET Core CLI with the `-au Windows` option.</span></span> <span data-ttu-id="bd108-116">V aplikaci Visual Studio je v dialogovém okně pro **změnu ověřování** k dispozici možnost **ověřování systému Windows** .</span><span class="sxs-lookup"><span data-stu-id="bd108-116">In Visual Studio, the **Change Authentication** dialog provides the **Windows Authentication** options.</span></span>

<span data-ttu-id="bd108-117">Pokud je vybraná možnost ověřování systému Windows, aplikace je nakonfigurovaná tak, aby používala [modul služby IIS pro ověřování systému Windows](xref:host-and-deploy/iis/modules).</span><span class="sxs-lookup"><span data-stu-id="bd108-117">If Windows Authentication is selected, the app is configured to use the [Windows Authentication IIS module](xref:host-and-deploy/iis/modules).</span></span> <span data-ttu-id="bd108-118">Ověřování systému Windows je určeno pro intranetové weby.</span><span class="sxs-lookup"><span data-stu-id="bd108-118">Windows Authentication is intended for Intranet web sites.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bd108-119">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="bd108-119">Additional resources</span></span>

<span data-ttu-id="bd108-120">V následujících článcích se dozvíte, jak používat kód vygenerovaný v ASP.NET Core šablonách, které používají jednotlivé uživatelské účty:</span><span class="sxs-lookup"><span data-stu-id="bd108-120">The following articles show how to use the code generated in ASP.NET Core templates that use individual user accounts:</span></span>

* [<span data-ttu-id="bd108-121">Potvrzení účtu a obnovení hesla v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bd108-121">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="bd108-122">Vytvoření aplikace ASP.NET Core s uživatelskými daty chráněnými autorizací</span><span class="sxs-lookup"><span data-stu-id="bd108-122">Create an ASP.NET Core app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)

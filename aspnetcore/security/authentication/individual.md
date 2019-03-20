---
title: Články podle projekty ASP.NET Core, které jsou vytvořené pomocí jednotlivých uživatelských účtů
author: rick-anderson
description: Vyhledat články založené na projekty ASP.NET Core, které jsou vytvořené pomocí jednotlivých uživatelských účtů.
ms.author: riande
ms.date: 11/30/2017
uid: security/authentication/individual
ms.openlocfilehash: f9c1be16386da935382275815bb5fd5c72894b1c
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58265425"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a><span data-ttu-id="1b37b-103">Články podle projekty ASP.NET Core, které jsou vytvořené pomocí jednotlivých uživatelských účtů</span><span class="sxs-lookup"><span data-stu-id="1b37b-103">Articles based on ASP.NET Core projects created with individual user accounts</span></span>

<span data-ttu-id="1b37b-104">ASP.NET Core Identity je součástí šablony projektů v sadě Visual Studio s parametrem "Jednotlivých uživatelských účtů".</span><span class="sxs-lookup"><span data-stu-id="1b37b-104">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="1b37b-105">Ověřování šablony jsou k dispozici v rozhraní .NET Core CLI s `-au Individual`:</span><span class="sxs-lookup"><span data-stu-id="1b37b-105">The authentication templates are available in .NET Core CLI with `-au Individual`:</span></span>

::: moniker range=">= aspnetcore-2.1"

```console
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```console
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

<span data-ttu-id="1b37b-106">Zobrazit [tento problém Githubu](https://github.com/aspnet/AspNetCore/issues/5833) pro ověřování webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="1b37b-106">See [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/5833) for web API authentication.</span></span>

<a name="no"></a>

## <a name="no-authentication"></a><span data-ttu-id="1b37b-107">Bez ověřování</span><span class="sxs-lookup"><span data-stu-id="1b37b-107">No Authentication</span></span>

<span data-ttu-id="1b37b-108">Ověřování je zadaný v rozhraní příkazového řádku .NET Core s `-au` možnost.</span><span class="sxs-lookup"><span data-stu-id="1b37b-108">Authentication is specified in the .NET Core CLI with the `-au` option.</span></span> <span data-ttu-id="1b37b-109">V sadě Visual Studio **změna ověřování** dialogové okno je k dispozici pro nové webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="1b37b-109">In Visual Studio, the **Change Authentication** dialog is available for new web applications.</span></span> <span data-ttu-id="1b37b-110">Výchozí pro nové webové aplikace v sadě Visual Studio je **bez ověřování**.</span><span class="sxs-lookup"><span data-stu-id="1b37b-110">The default for new web apps in Visual Studio is **No Authentication**.</span></span>

<span data-ttu-id="1b37b-111">Projekty vytvořené bez ověřování:</span><span class="sxs-lookup"><span data-stu-id="1b37b-111">Projects created with no authentication:</span></span>

* <span data-ttu-id="1b37b-112">Neobsahují webových stránek a uživatelského rozhraní pro přihlášení a odhlášení.</span><span class="sxs-lookup"><span data-stu-id="1b37b-112">Don't contain web pages and UI to sign in and sign out.</span></span>
* <span data-ttu-id="1b37b-113">Neobsahují ověřovacího kódu.</span><span class="sxs-lookup"><span data-stu-id="1b37b-113">Don't contain authentication code.</span></span>

<a name="win"></a>

## <a name="windows-authentication"></a><span data-ttu-id="1b37b-114">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="1b37b-114">Windows Authentication</span></span>

<span data-ttu-id="1b37b-115">Ověřování Windows je určená pro nové webové aplikace v rozhraní příkazového řádku .NET Core s `-au Windows` možnost.</span><span class="sxs-lookup"><span data-stu-id="1b37b-115">Windows Authentication is specified for new web apps in the .NET Core CLI with the `-au Windows` option.</span></span> <span data-ttu-id="1b37b-116">V sadě Visual Studio **změna ověřování** dialogové okno obsahuje **ověřování Windows** možnosti.</span><span class="sxs-lookup"><span data-stu-id="1b37b-116">In Visual Studio, the **Change Authentication** dialog provides the **Windows Authentication** options.</span></span>

<span data-ttu-id="1b37b-117">Pokud je vybrána možnost ověření Windows, aplikace je nakonfigurovaná pro používání [modul IIS ověřování Windows](xref:host-and-deploy/iis/modules).</span><span class="sxs-lookup"><span data-stu-id="1b37b-117">If Windows Authentication is selected, the app is configured to use the [Windows Authentication IIS module](xref:host-and-deploy/iis/modules).</span></span> <span data-ttu-id="1b37b-118">Ověřování Windows je určená pro intranetové weby.</span><span class="sxs-lookup"><span data-stu-id="1b37b-118">Windows Authentication is intended for Intranet web sites.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1b37b-119">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="1b37b-119">Additional resources</span></span>

<span data-ttu-id="1b37b-120">Následující články popisují, jak používat kód vygenerovaný v šablony ASP.NET Core, které používají jednotlivých uživatelských účtů:</span><span class="sxs-lookup"><span data-stu-id="1b37b-120">The following articles show how to use the code generated in ASP.NET Core templates that use individual user accounts:</span></span>

* [<span data-ttu-id="1b37b-121">Dvoufaktorové ověřování přes SMS</span><span class="sxs-lookup"><span data-stu-id="1b37b-121">Two-factor authentication with SMS</span></span>](xref:security/authentication/2fa)
* [<span data-ttu-id="1b37b-122">Potvrzení účtu a obnovení hesla v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1b37b-122">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="1b37b-123">Vytvoření aplikace ASP.NET Core s uživatelskými daty chráněnými autorizací</span><span class="sxs-lookup"><span data-stu-id="1b37b-123">Create an ASP.NET Core app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)

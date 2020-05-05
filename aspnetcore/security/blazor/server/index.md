---
title: Zabezpečené aplikace Blazor ASP.NET Core serveru
author: guardrex
description: Naučte se zabezpečit Blazor serverové aplikace jako aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/server/index
ms.openlocfilehash: bbd8b6fcd357b8929bf097450854d98fbea2570e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772632"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="8375c-103">Zabezpečené ASP.NET Core aplikace serveru Blazor</span><span class="sxs-lookup"><span data-stu-id="8375c-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="8375c-104">Od [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8375c-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="blazor-server-project-template"></a><span data-ttu-id="8375c-105">Šablona projektu serveru Blazor</span><span class="sxs-lookup"><span data-stu-id="8375c-105">Blazor Server project template</span></span>

<span data-ttu-id="8375c-106">Šablonu projektu serveru Blazor lze nakonfigurovat pro ověřování při vytvoření projektu.</span><span class="sxs-lookup"><span data-stu-id="8375c-106">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8375c-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8375c-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8375c-108">Pokud chcete vytvořit nový projekt serveru Blazor <xref:blazor/get-started> s ověřovacím mechanismem, postupujte podle pokynů pro Visual Studio v článku.</span><span class="sxs-lookup"><span data-stu-id="8375c-108">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="8375c-109">Po výběru šablony **aplikace Blazor serveru** v dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** vyberte v části **ověřování**možnost **změnit** .</span><span class="sxs-lookup"><span data-stu-id="8375c-109">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="8375c-110">Otevře se dialogové okno s nabídkou stejné sady mechanismů ověřování pro jiné projekty ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="8375c-110">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="8375c-111">**Bez ověřování**</span><span class="sxs-lookup"><span data-stu-id="8375c-111">**No Authentication**</span></span>
* <span data-ttu-id="8375c-112">Uživatelské účty **jednotlivých uživatelských účtů** &ndash; mohou být uloženy:</span><span class="sxs-lookup"><span data-stu-id="8375c-112">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="8375c-113">V aplikaci, která používá systém [identit](xref:security/authentication/identity) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8375c-113">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="8375c-114">S [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="8375c-114">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="8375c-115">**Pracovní nebo školní účty**</span><span class="sxs-lookup"><span data-stu-id="8375c-115">**Work or School Accounts**</span></span>
* <span data-ttu-id="8375c-116">**Ověřování systému Windows**</span><span class="sxs-lookup"><span data-stu-id="8375c-116">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8375c-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8375c-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8375c-118">Postupujte podle pokynů Visual Studio Code v <xref:blazor/get-started> článku Vytvoření nového projektu serveru Blazor s mechanismem ověřování:</span><span class="sxs-lookup"><span data-stu-id="8375c-118">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="8375c-119">Přípustné hodnoty ověřování (`{AUTHENTICATION}`) jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="8375c-119">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="8375c-120">Mechanismus ověřování</span><span class="sxs-lookup"><span data-stu-id="8375c-120">Authentication mechanism</span></span> | <span data-ttu-id="8375c-121">Popis</span><span class="sxs-lookup"><span data-stu-id="8375c-121">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="8375c-122">`None`výchozí</span><span class="sxs-lookup"><span data-stu-id="8375c-122">`None` (default)</span></span>         | <span data-ttu-id="8375c-123">Bez ověřování</span><span class="sxs-lookup"><span data-stu-id="8375c-123">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="8375c-124">Uživatelé uložení v aplikaci pomocí ASP.NET Core identity</span><span class="sxs-lookup"><span data-stu-id="8375c-124">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="8375c-125">Uživatelé uložení v [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="8375c-125">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="8375c-126">Ověřování organizace pro jednoho tenanta</span><span class="sxs-lookup"><span data-stu-id="8375c-126">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="8375c-127">Ověřování organizace pro více tenantů</span><span class="sxs-lookup"><span data-stu-id="8375c-127">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="8375c-128">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="8375c-128">Windows Authentication</span></span> |

<span data-ttu-id="8375c-129">Pomocí `-o|--output` možnosti příkaz používá hodnotu poskytnutou pro `{APP NAME}` zástupný text pro:</span><span class="sxs-lookup"><span data-stu-id="8375c-129">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="8375c-130">Vytvořte složku pro projekt.</span><span class="sxs-lookup"><span data-stu-id="8375c-130">Create a folder for the project.</span></span>
* <span data-ttu-id="8375c-131">Pojmenujte projekt.</span><span class="sxs-lookup"><span data-stu-id="8375c-131">Name the project.</span></span>

<span data-ttu-id="8375c-132">Další informace najdete v tématu [dotnet New](/dotnet/core/tools/dotnet-new) v příručce .NET Core.</span><span class="sxs-lookup"><span data-stu-id="8375c-132">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8375c-133">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8375c-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="8375c-134">Postupujte podle pokynů Visual Studio pro Mac v <xref:blazor/get-started> článku.</span><span class="sxs-lookup"><span data-stu-id="8375c-134">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="8375c-135">V kroku **Konfigurace nového aplikace Blazor serveru** vyberte v rozevíracím seznamu **ověřování** možnost **individuální ověřování (v aplikaci)** .</span><span class="sxs-lookup"><span data-stu-id="8375c-135">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="8375c-136">Aplikace se vytvoří pro jednotlivé uživatele uložené v aplikaci s ASP.NET Core identitou.</span><span class="sxs-lookup"><span data-stu-id="8375c-136">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="8375c-137">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="8375c-137">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="8375c-138">Postupujte podle pokynů .NET Core CLI v <xref:blazor/get-started> článku Vytvoření nového projektu serveru Blazor s mechanismem ověřování:</span><span class="sxs-lookup"><span data-stu-id="8375c-138">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="8375c-139">Přípustné hodnoty ověřování (`{AUTHENTICATION}`) jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="8375c-139">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="8375c-140">Mechanismus ověřování</span><span class="sxs-lookup"><span data-stu-id="8375c-140">Authentication mechanism</span></span> | <span data-ttu-id="8375c-141">Popis</span><span class="sxs-lookup"><span data-stu-id="8375c-141">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="8375c-142">`None`výchozí</span><span class="sxs-lookup"><span data-stu-id="8375c-142">`None` (default)</span></span>         | <span data-ttu-id="8375c-143">Bez ověřování</span><span class="sxs-lookup"><span data-stu-id="8375c-143">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="8375c-144">Uživatelé uložení v aplikaci pomocí ASP.NET Core identity</span><span class="sxs-lookup"><span data-stu-id="8375c-144">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="8375c-145">Uživatelé uložení v [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="8375c-145">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="8375c-146">Ověřování organizace pro jednoho tenanta</span><span class="sxs-lookup"><span data-stu-id="8375c-146">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="8375c-147">Ověřování organizace pro více tenantů</span><span class="sxs-lookup"><span data-stu-id="8375c-147">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="8375c-148">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="8375c-148">Windows Authentication</span></span> |

<span data-ttu-id="8375c-149">Pomocí `-o|--output` možnosti příkaz používá hodnotu poskytnutou pro `{APP NAME}` zástupný text pro:</span><span class="sxs-lookup"><span data-stu-id="8375c-149">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="8375c-150">Vytvořte složku pro projekt.</span><span class="sxs-lookup"><span data-stu-id="8375c-150">Create a folder for the project.</span></span>
* <span data-ttu-id="8375c-151">Pojmenujte projekt.</span><span class="sxs-lookup"><span data-stu-id="8375c-151">Name the project.</span></span>

<span data-ttu-id="8375c-152">Další informace najdete v tématu [dotnet New](/dotnet/core/tools/dotnet-new) v příručce .NET Core.</span><span class="sxs-lookup"><span data-stu-id="8375c-152">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="secure-an-existing-app"></a><span data-ttu-id="8375c-153">Zabezpečení existující aplikace</span><span class="sxs-lookup"><span data-stu-id="8375c-153">Secure an existing app</span></span>

Blazor<span data-ttu-id="8375c-154">Serverové aplikace jsou konfigurovány pro zabezpečení stejným způsobem jako aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8375c-154"> Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="8375c-155">Další informace najdete v článcích v části <xref:security/index>.</span><span class="sxs-lookup"><span data-stu-id="8375c-155">For more information, see the articles under <xref:security/index>.</span></span>

---
title: Zabezpečení Blazor Server aplikací ASP.NET Core
author: guardrex
description: Naučte se zabezpečit Blazor Server aplikace jako aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/server/index
ms.openlocfilehash: ab3baad30f78c5d5e2f969b3292d4886fcd0406d
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402309"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="b728c-103">Zabezpečení Blazor Server aplikací ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b728c-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="b728c-104">Od [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b728c-104">By [Luke Latham](https://github.com/guardrex)</span></span>

Blazor Server<span data-ttu-id="b728c-105">aplikace jsou konfigurovány pro zabezpečení stejným způsobem jako aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b728c-105"> apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="b728c-106">Další informace najdete v článcích v části <xref:security/index> .</span><span class="sxs-lookup"><span data-stu-id="b728c-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="b728c-107">Témata v tomto přehledu se vztahují konkrétně na Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="b728c-107">Topics under this overview apply specifically to Blazor Server.</span></span> 

## <a name="blazor-server-project-template"></a>Blazor Server<span data-ttu-id="b728c-108">Šablona projektu</span><span class="sxs-lookup"><span data-stu-id="b728c-108"> project template</span></span>

<span data-ttu-id="b728c-109">Blazor ServerŠablonu projektu lze nakonfigurovat pro ověřování při vytvoření projektu.</span><span class="sxs-lookup"><span data-stu-id="b728c-109">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b728c-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b728c-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b728c-111">Pokud <xref:blazor/get-started> chcete vytvořit nový Blazor Server projekt s ověřovacím mechanismem, postupujte podle pokynů pro Visual Studio v článku.</span><span class="sxs-lookup"><span data-stu-id="b728c-111">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="b728c-112">Po výběru šablony \*\* Blazor Server aplikace\*\* v dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** vyberte v části **ověřování**možnost **změnit** .</span><span class="sxs-lookup"><span data-stu-id="b728c-112">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="b728c-113">Otevře se dialogové okno s nabídkou stejné sady mechanismů ověřování pro jiné projekty ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b728c-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="b728c-114">**Bez ověřování**</span><span class="sxs-lookup"><span data-stu-id="b728c-114">**No Authentication**</span></span>
* <span data-ttu-id="b728c-115">**Jednotlivé uživatelské účty**: uživatelské účty je možné uložit:</span><span class="sxs-lookup"><span data-stu-id="b728c-115">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="b728c-116">V aplikaci, která používá [Identity](xref:security/authentication/identity) systém ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b728c-116">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="b728c-117">S [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="b728c-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="b728c-118">**Pracovní nebo školní účty**</span><span class="sxs-lookup"><span data-stu-id="b728c-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="b728c-119">**Ověřování systému Windows**</span><span class="sxs-lookup"><span data-stu-id="b728c-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b728c-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b728c-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b728c-121">Postupujte podle pokynů Visual Studio Code v <xref:blazor/get-started> článku Vytvoření nového Blazor Server projektu s mechanismem ověřování:</span><span class="sxs-lookup"><span data-stu-id="b728c-121">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="b728c-122">Přípustné hodnoty ověřování ( `{AUTHENTICATION}` ) jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="b728c-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="b728c-123">Mechanismus ověřování</span><span class="sxs-lookup"><span data-stu-id="b728c-123">Authentication mechanism</span></span> | <span data-ttu-id="b728c-124">Description</span><span class="sxs-lookup"><span data-stu-id="b728c-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="b728c-125">`None`výchozí</span><span class="sxs-lookup"><span data-stu-id="b728c-125">`None` (default)</span></span>         | <span data-ttu-id="b728c-126">Bez ověřování</span><span class="sxs-lookup"><span data-stu-id="b728c-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="b728c-127">Uživatelé uložení v aplikaci pomocí ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="b728c-127">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="b728c-128">Uživatelé uložení v [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="b728c-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="b728c-129">Ověřování organizace pro jednoho tenanta</span><span class="sxs-lookup"><span data-stu-id="b728c-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="b728c-130">Ověřování organizace pro více tenantů</span><span class="sxs-lookup"><span data-stu-id="b728c-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="b728c-131">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="b728c-131">Windows Authentication</span></span> |

<span data-ttu-id="b728c-132">Pomocí `-o|--output` Možnosti příkaz používá hodnotu poskytnutou pro `{APP NAME}` zástupný text pro:</span><span class="sxs-lookup"><span data-stu-id="b728c-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="b728c-133">Vytvořte složku pro projekt.</span><span class="sxs-lookup"><span data-stu-id="b728c-133">Create a folder for the project.</span></span>
* <span data-ttu-id="b728c-134">Pojmenujte projekt.</span><span class="sxs-lookup"><span data-stu-id="b728c-134">Name the project.</span></span>

<span data-ttu-id="b728c-135">Další informace najdete v tématu [`dotnet new`](/dotnet/core/tools/dotnet-new) Průvodce .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b728c-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b728c-136">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="b728c-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="b728c-137">Postupujte podle pokynů Visual Studio pro Mac v <xref:blazor/get-started> článku.</span><span class="sxs-lookup"><span data-stu-id="b728c-137">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="b728c-138">V kroku **Konfigurace nového Blazor Server aplikace** vyberte v rozevíracím seznamu **ověřování** možnost **individuální ověřování (v aplikaci)** .</span><span class="sxs-lookup"><span data-stu-id="b728c-138">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="b728c-139">Aplikace se vytvoří pro jednotlivé uživatele uložené v aplikaci pomocí ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="b728c-139">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b728c-140">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b728c-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="b728c-141">Postupujte podle pokynů .NET Core CLI v <xref:blazor/get-started> článku Vytvoření nového Blazor Server projektu s mechanismem ověřování:</span><span class="sxs-lookup"><span data-stu-id="b728c-141">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="b728c-142">Přípustné hodnoty ověřování ( `{AUTHENTICATION}` ) jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="b728c-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="b728c-143">Mechanismus ověřování</span><span class="sxs-lookup"><span data-stu-id="b728c-143">Authentication mechanism</span></span> | <span data-ttu-id="b728c-144">Description</span><span class="sxs-lookup"><span data-stu-id="b728c-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="b728c-145">`None`výchozí</span><span class="sxs-lookup"><span data-stu-id="b728c-145">`None` (default)</span></span>         | <span data-ttu-id="b728c-146">Bez ověřování</span><span class="sxs-lookup"><span data-stu-id="b728c-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="b728c-147">Uživatelé uložení v aplikaci pomocí ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="b728c-147">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="b728c-148">Uživatelé uložení v [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="b728c-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="b728c-149">Ověřování organizace pro jednoho tenanta</span><span class="sxs-lookup"><span data-stu-id="b728c-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="b728c-150">Ověřování organizace pro více tenantů</span><span class="sxs-lookup"><span data-stu-id="b728c-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="b728c-151">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="b728c-151">Windows Authentication</span></span> |

<span data-ttu-id="b728c-152">Pomocí `-o|--output` Možnosti příkaz používá hodnotu poskytnutou pro `{APP NAME}` zástupný text pro:</span><span class="sxs-lookup"><span data-stu-id="b728c-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="b728c-153">Vytvořte složku pro projekt.</span><span class="sxs-lookup"><span data-stu-id="b728c-153">Create a folder for the project.</span></span>
* <span data-ttu-id="b728c-154">Pojmenujte projekt.</span><span class="sxs-lookup"><span data-stu-id="b728c-154">Name the project.</span></span>

<span data-ttu-id="b728c-155">Další informace najdete v tématu [`dotnet new`](/dotnet/core/tools/dotnet-new) Průvodce .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b728c-155">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="scaffold-identity"></a><span data-ttu-id="b728c-156">GenerováníIdentity</span><span class="sxs-lookup"><span data-stu-id="b728c-156">Scaffold Identity</span></span>

<span data-ttu-id="b728c-157">Uživatelské rozhraní Identity do Blazor Server projektu:</span><span class="sxs-lookup"><span data-stu-id="b728c-157">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="b728c-158">[Bez existující autorizace](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="b728c-158">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="b728c-159">[S autorizací](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="b728c-159">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

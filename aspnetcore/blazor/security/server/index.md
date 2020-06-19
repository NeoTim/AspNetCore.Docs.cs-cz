---
title: Zabezpečené Blazor aplikace ASP.NET Core serveru
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
uid: blazor/security/server/index
ms.openlocfilehash: a8604ca6ea60386bb3c54c950205ee695d37c689
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103660"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="25f65-103">Zabezpečené Blazor aplikace ASP.NET Core serveru</span><span class="sxs-lookup"><span data-stu-id="25f65-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="25f65-104">Od [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="25f65-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="blazor-server-project-template"></a>Blazor<span data-ttu-id="25f65-105">Šablona projektu serveru</span><span class="sxs-lookup"><span data-stu-id="25f65-105"> Server project template</span></span>

<span data-ttu-id="25f65-106">BlazorŠablonu projektu serveru lze nakonfigurovat pro ověřování při vytvoření projektu.</span><span class="sxs-lookup"><span data-stu-id="25f65-106">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="25f65-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="25f65-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="25f65-108">Pokud <xref:blazor/get-started> chcete vytvořit nový Blazor serverový projekt s ověřovacím mechanismem, postupujte podle pokynů pro Visual Studio v článku.</span><span class="sxs-lookup"><span data-stu-id="25f65-108">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="25f65-109">Po výběru šablony \*\* Blazor aplikace serveru\*\* v dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** vyberte v části **ověřování**možnost **změnit** .</span><span class="sxs-lookup"><span data-stu-id="25f65-109">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="25f65-110">Otevře se dialogové okno s nabídkou stejné sady mechanismů ověřování pro jiné projekty ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="25f65-110">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="25f65-111">**Bez ověřování**</span><span class="sxs-lookup"><span data-stu-id="25f65-111">**No Authentication**</span></span>
* <span data-ttu-id="25f65-112">**Jednotlivé uživatelské účty**: uživatelské účty je možné uložit:</span><span class="sxs-lookup"><span data-stu-id="25f65-112">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="25f65-113">V aplikaci, která používá [Identity](xref:security/authentication/identity) systém ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="25f65-113">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="25f65-114">S [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="25f65-114">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="25f65-115">**Pracovní nebo školní účty**</span><span class="sxs-lookup"><span data-stu-id="25f65-115">**Work or School Accounts**</span></span>
* <span data-ttu-id="25f65-116">**Ověřování systému Windows**</span><span class="sxs-lookup"><span data-stu-id="25f65-116">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="25f65-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="25f65-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="25f65-118">Podle pokynů Visual Studio Code v <xref:blazor/get-started> článku vytvořte nový Blazor serverový projekt s mechanismem ověřování:</span><span class="sxs-lookup"><span data-stu-id="25f65-118">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="25f65-119">Přípustné hodnoty ověřování ( `{AUTHENTICATION}` ) jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="25f65-119">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="25f65-120">Mechanismus ověřování</span><span class="sxs-lookup"><span data-stu-id="25f65-120">Authentication mechanism</span></span> | <span data-ttu-id="25f65-121">Popis</span><span class="sxs-lookup"><span data-stu-id="25f65-121">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="25f65-122">`None`výchozí</span><span class="sxs-lookup"><span data-stu-id="25f65-122">`None` (default)</span></span>         | <span data-ttu-id="25f65-123">Bez ověřování</span><span class="sxs-lookup"><span data-stu-id="25f65-123">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="25f65-124">Uživatelé uložení v aplikaci pomocí ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="25f65-124">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="25f65-125">Uživatelé uložení v [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="25f65-125">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="25f65-126">Ověřování organizace pro jednoho tenanta</span><span class="sxs-lookup"><span data-stu-id="25f65-126">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="25f65-127">Ověřování organizace pro více tenantů</span><span class="sxs-lookup"><span data-stu-id="25f65-127">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="25f65-128">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="25f65-128">Windows Authentication</span></span> |

<span data-ttu-id="25f65-129">Pomocí `-o|--output` Možnosti příkaz používá hodnotu poskytnutou pro `{APP NAME}` zástupný text pro:</span><span class="sxs-lookup"><span data-stu-id="25f65-129">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="25f65-130">Vytvořte složku pro projekt.</span><span class="sxs-lookup"><span data-stu-id="25f65-130">Create a folder for the project.</span></span>
* <span data-ttu-id="25f65-131">Pojmenujte projekt.</span><span class="sxs-lookup"><span data-stu-id="25f65-131">Name the project.</span></span>

<span data-ttu-id="25f65-132">Další informace najdete v tématu [dotnet New](/dotnet/core/tools/dotnet-new) v příručce .NET Core.</span><span class="sxs-lookup"><span data-stu-id="25f65-132">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="25f65-133">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="25f65-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="25f65-134">Postupujte podle pokynů Visual Studio pro Mac v <xref:blazor/get-started> článku.</span><span class="sxs-lookup"><span data-stu-id="25f65-134">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="25f65-135">V kroku **Konfigurace nového Blazor serveru aplikace** vyberte v rozevíracím seznamu **ověřování** možnost **individuální ověřování (v aplikaci)** .</span><span class="sxs-lookup"><span data-stu-id="25f65-135">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="25f65-136">Aplikace se vytvoří pro jednotlivé uživatele uložené v aplikaci pomocí ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="25f65-136">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="25f65-137">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="25f65-137">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="25f65-138">Podle pokynů .NET Core CLI v <xref:blazor/get-started> článku vytvořte nový Blazor serverový projekt s mechanismem ověřování:</span><span class="sxs-lookup"><span data-stu-id="25f65-138">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="25f65-139">Přípustné hodnoty ověřování ( `{AUTHENTICATION}` ) jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="25f65-139">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="25f65-140">Mechanismus ověřování</span><span class="sxs-lookup"><span data-stu-id="25f65-140">Authentication mechanism</span></span> | <span data-ttu-id="25f65-141">Popis</span><span class="sxs-lookup"><span data-stu-id="25f65-141">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="25f65-142">`None`výchozí</span><span class="sxs-lookup"><span data-stu-id="25f65-142">`None` (default)</span></span>         | <span data-ttu-id="25f65-143">Bez ověřování</span><span class="sxs-lookup"><span data-stu-id="25f65-143">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="25f65-144">Uživatelé uložení v aplikaci pomocí ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="25f65-144">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="25f65-145">Uživatelé uložení v [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="25f65-145">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="25f65-146">Ověřování organizace pro jednoho tenanta</span><span class="sxs-lookup"><span data-stu-id="25f65-146">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="25f65-147">Ověřování organizace pro více tenantů</span><span class="sxs-lookup"><span data-stu-id="25f65-147">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="25f65-148">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="25f65-148">Windows Authentication</span></span> |

<span data-ttu-id="25f65-149">Pomocí `-o|--output` Možnosti příkaz používá hodnotu poskytnutou pro `{APP NAME}` zástupný text pro:</span><span class="sxs-lookup"><span data-stu-id="25f65-149">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="25f65-150">Vytvořte složku pro projekt.</span><span class="sxs-lookup"><span data-stu-id="25f65-150">Create a folder for the project.</span></span>
* <span data-ttu-id="25f65-151">Pojmenujte projekt.</span><span class="sxs-lookup"><span data-stu-id="25f65-151">Name the project.</span></span>

<span data-ttu-id="25f65-152">Další informace najdete v tématu [dotnet New](/dotnet/core/tools/dotnet-new) v příručce .NET Core.</span><span class="sxs-lookup"><span data-stu-id="25f65-152">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="secure-an-existing-app"></a><span data-ttu-id="25f65-153">Zabezpečení existující aplikace</span><span class="sxs-lookup"><span data-stu-id="25f65-153">Secure an existing app</span></span>

Blazor<span data-ttu-id="25f65-154">Serverové aplikace jsou konfigurovány pro zabezpečení stejným způsobem jako aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="25f65-154"> Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="25f65-155">Další informace najdete v článcích v části <xref:security/index> .</span><span class="sxs-lookup"><span data-stu-id="25f65-155">For more information, see the articles under <xref:security/index>.</span></span>

## <a name="scaffold-identity"></a><span data-ttu-id="25f65-156">GenerováníIdentity</span><span class="sxs-lookup"><span data-stu-id="25f65-156">Scaffold Identity</span></span>

<span data-ttu-id="25f65-157">Uživatelské rozhraní Identity na Blazor serverový projekt:</span><span class="sxs-lookup"><span data-stu-id="25f65-157">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="25f65-158">[Bez existující autorizace](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="25f65-158">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="25f65-159">[S autorizací](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="25f65-159">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

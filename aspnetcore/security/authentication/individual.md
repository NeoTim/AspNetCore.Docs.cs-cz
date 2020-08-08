---
title: Články založené na ASP.NET Core projektech vytvořených pomocí individuálních uživatelských účtů
author: rick-anderson
description: Objevte články na základě ASP.NET Core projektů vytvořených pomocí individuálních uživatelských účtů.
ms.author: riande
ms.date: 12/11/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/individual
ms.openlocfilehash: 6b72612c12d52cfc1736c141bdad95ace6c84546
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022287"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a><span data-ttu-id="94e60-103">Články založené na ASP.NET Core projektech vytvořených pomocí individuálních uživatelských účtů</span><span class="sxs-lookup"><span data-stu-id="94e60-103">Articles based on ASP.NET Core projects created with individual user accounts</span></span>

<span data-ttu-id="94e60-104">ASP.NET Core Identity je součástí šablon projektů v aplikaci Visual Studio s možností jednotlivé uživatelské účty.</span><span class="sxs-lookup"><span data-stu-id="94e60-104">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="94e60-105">Šablony ověřování jsou dostupné v .NET Core CLI s `-au Individual` :</span><span class="sxs-lookup"><span data-stu-id="94e60-105">The authentication templates are available in .NET Core CLI with `-au Individual`:</span></span>

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

<span data-ttu-id="94e60-106">Podívejte se na [Tento problém GitHubu](https://github.com/dotnet/AspNetCore/issues/5833) pro ověřování webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="94e60-106">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5833) for web API authentication.</span></span>

<a name="no"></a>

## <a name="no-authentication"></a><span data-ttu-id="94e60-107">Bez ověřování</span><span class="sxs-lookup"><span data-stu-id="94e60-107">No Authentication</span></span>

<span data-ttu-id="94e60-108">Ověřování je zadáno v .NET Core CLI s `-au` možností.</span><span class="sxs-lookup"><span data-stu-id="94e60-108">Authentication is specified in the .NET Core CLI with the `-au` option.</span></span> <span data-ttu-id="94e60-109">V aplikaci Visual Studio je k dispozici dialog **Změna ověřování** pro nové webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="94e60-109">In Visual Studio, the **Change Authentication** dialog is available for new web applications.</span></span> <span data-ttu-id="94e60-110">Výchozí pro nové webové aplikace v aplikaci Visual Studio není **ověřování**.</span><span class="sxs-lookup"><span data-stu-id="94e60-110">The default for new web apps in Visual Studio is **No Authentication**.</span></span>

<span data-ttu-id="94e60-111">Projekty vytvořené bez ověřování:</span><span class="sxs-lookup"><span data-stu-id="94e60-111">Projects created with no authentication:</span></span>

* <span data-ttu-id="94e60-112">Neobsahujte webové stránky a uživatelské rozhraní pro přihlášení a odhlášení.</span><span class="sxs-lookup"><span data-stu-id="94e60-112">Don't contain web pages and UI to sign in and sign out.</span></span>
* <span data-ttu-id="94e60-113">Neobsahujte ověřovací kód.</span><span class="sxs-lookup"><span data-stu-id="94e60-113">Don't contain authentication code.</span></span>

<a name="win"></a>

## <a name="windows-authentication"></a><span data-ttu-id="94e60-114">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="94e60-114">Windows Authentication</span></span>

<span data-ttu-id="94e60-115">Ověřování systému Windows je zadáno pro nové webové aplikace v .NET Core CLI s `-au Windows` možností.</span><span class="sxs-lookup"><span data-stu-id="94e60-115">Windows Authentication is specified for new web apps in the .NET Core CLI with the `-au Windows` option.</span></span> <span data-ttu-id="94e60-116">V aplikaci Visual Studio je v dialogovém okně pro **změnu ověřování** k dispozici možnost **ověřování systému Windows** .</span><span class="sxs-lookup"><span data-stu-id="94e60-116">In Visual Studio, the **Change Authentication** dialog provides the **Windows Authentication** options.</span></span>

<span data-ttu-id="94e60-117">Pokud je vybraná možnost ověřování systému Windows, aplikace je nakonfigurovaná tak, aby používala [modul služby IIS pro ověřování systému Windows](xref:host-and-deploy/iis/modules).</span><span class="sxs-lookup"><span data-stu-id="94e60-117">If Windows Authentication is selected, the app is configured to use the [Windows Authentication IIS module](xref:host-and-deploy/iis/modules).</span></span> <span data-ttu-id="94e60-118">Ověřování systému Windows je určeno pro intranetové weby.</span><span class="sxs-lookup"><span data-stu-id="94e60-118">Windows Authentication is intended for Intranet web sites.</span></span>

## <a name="dotnet-new-webapp-authentication-options"></a><span data-ttu-id="94e60-119">dotnet – nové možnosti ověřování WebApp</span><span class="sxs-lookup"><span data-stu-id="94e60-119">dotnet new webapp authentication options</span></span>

<span data-ttu-id="94e60-120">V následující tabulce jsou uvedené možnosti ověřování pro nové webové aplikace:</span><span class="sxs-lookup"><span data-stu-id="94e60-120">The following table shows the authentication options available for new web apps:</span></span>

| <span data-ttu-id="94e60-121">Možnost</span><span class="sxs-lookup"><span data-stu-id="94e60-121">Option</span></span> | <span data-ttu-id="94e60-122">Typ ověřování</span><span class="sxs-lookup"><span data-stu-id="94e60-122">Type of authentication</span></span> | <span data-ttu-id="94e60-123">Odkaz na další informace</span><span class="sxs-lookup"><span data-stu-id="94e60-123">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="94e60-124">Žádné</span><span class="sxs-lookup"><span data-stu-id="94e60-124">None</span></span>            |  <span data-ttu-id="94e60-125">Bez ověřování</span><span class="sxs-lookup"><span data-stu-id="94e60-125">No authentication</span></span> | | 
| <span data-ttu-id="94e60-126">Jednoho</span><span class="sxs-lookup"><span data-stu-id="94e60-126">Individual</span></span>      |  <span data-ttu-id="94e60-127">Individuální ověřování</span><span class="sxs-lookup"><span data-stu-id="94e60-127">Individual authentication</span></span> | <xref:security/authentication/identity>
| <span data-ttu-id="94e60-128">IndividualB2C</span><span class="sxs-lookup"><span data-stu-id="94e60-128">IndividualB2C</span></span>   |  <span data-ttu-id="94e60-129">Individuální ověřování hostované v cloudu s Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="94e60-129">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="94e60-130">Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="94e60-130">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="94e60-131">SingleOrg</span><span class="sxs-lookup"><span data-stu-id="94e60-131">SingleOrg</span></span>       |  <span data-ttu-id="94e60-132">Ověřování organizace pro jednoho tenanta</span><span class="sxs-lookup"><span data-stu-id="94e60-132">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="94e60-133">Azure AD</span><span class="sxs-lookup"><span data-stu-id="94e60-133">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="94e60-134">MultiOrg</span><span class="sxs-lookup"><span data-stu-id="94e60-134">MultiOrg</span></span>        |  <span data-ttu-id="94e60-135">Ověřování organizace pro více tenantů</span><span class="sxs-lookup"><span data-stu-id="94e60-135">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="94e60-136">Azure AD</span><span class="sxs-lookup"><span data-stu-id="94e60-136">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="94e60-137">Windows</span><span class="sxs-lookup"><span data-stu-id="94e60-137">Windows</span></span>         |  <span data-ttu-id="94e60-138">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="94e60-138">Windows authentication</span></span> | [<span data-ttu-id="94e60-139">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="94e60-139">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="visual-studio-new-webapp-authentication-options"></a><span data-ttu-id="94e60-140">Nové možnosti ověřování WebApp sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94e60-140">Visual Studio new webapp authentication options</span></span>

<span data-ttu-id="94e60-141">V následující tabulce jsou uvedeny možnosti ověřování, které jsou k dispozici při vytváření nové webové aplikace pomocí sady Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="94e60-141">The following table shows the authentication options available when creating a new web app with Visual Studio:</span></span>

| <span data-ttu-id="94e60-142">Možnost</span><span class="sxs-lookup"><span data-stu-id="94e60-142">Option</span></span> | <span data-ttu-id="94e60-143">Typ ověřování</span><span class="sxs-lookup"><span data-stu-id="94e60-143">Type of authentication</span></span> | <span data-ttu-id="94e60-144">Odkaz na další informace</span><span class="sxs-lookup"><span data-stu-id="94e60-144">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="94e60-145">Žádné</span><span class="sxs-lookup"><span data-stu-id="94e60-145">None</span></span>            |  <span data-ttu-id="94e60-146">Bez ověřování</span><span class="sxs-lookup"><span data-stu-id="94e60-146">No authentication</span></span> | | 
| <span data-ttu-id="94e60-147">Individuální uživatelské účty/úložiště uživatelských účtů v aplikaci</span><span class="sxs-lookup"><span data-stu-id="94e60-147">Individual User Accounts / Store user accounts in-app</span></span> |  <span data-ttu-id="94e60-148">Individuální ověřování</span><span class="sxs-lookup"><span data-stu-id="94e60-148">Individual authentication</span></span> | <xref:security/authentication/identity> |
| <span data-ttu-id="94e60-149">Individuální uživatelské účty/připojení k existujícímu úložišti uživatelů v cloudu</span><span class="sxs-lookup"><span data-stu-id="94e60-149">Individual User Accounts / Connect to an existing user store in the cloud</span></span> |  <span data-ttu-id="94e60-150">Individuální ověřování hostované v cloudu s Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="94e60-150">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="94e60-151">Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="94e60-151">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="94e60-152">Pracovní nebo školní Cloud/jedna organizace</span><span class="sxs-lookup"><span data-stu-id="94e60-152">Work or School Cloud / Single Org</span></span>  |  <span data-ttu-id="94e60-153">Ověřování organizace pro jednoho tenanta</span><span class="sxs-lookup"><span data-stu-id="94e60-153">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="94e60-154">Azure AD</span><span class="sxs-lookup"><span data-stu-id="94e60-154">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="94e60-155">Pracovní nebo školní Cloud/několik organizací</span><span class="sxs-lookup"><span data-stu-id="94e60-155">Work or School Cloud / Multiple Org</span></span> |  <span data-ttu-id="94e60-156">Ověřování organizace pro více tenantů</span><span class="sxs-lookup"><span data-stu-id="94e60-156">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="94e60-157">Azure AD</span><span class="sxs-lookup"><span data-stu-id="94e60-157">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="94e60-158">Windows</span><span class="sxs-lookup"><span data-stu-id="94e60-158">Windows</span></span>         |  <span data-ttu-id="94e60-159">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="94e60-159">Windows authentication</span></span> | [<span data-ttu-id="94e60-160">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="94e60-160">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="additional-resources"></a><span data-ttu-id="94e60-161">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="94e60-161">Additional resources</span></span>

<span data-ttu-id="94e60-162">V následujících článcích se dozvíte, jak používat kód vygenerovaný v ASP.NET Core šablonách, které používají jednotlivé uživatelské účty:</span><span class="sxs-lookup"><span data-stu-id="94e60-162">The following articles show how to use the code generated in ASP.NET Core templates that use individual user accounts:</span></span>

* [<span data-ttu-id="94e60-163">Potvrzení účtu a obnovení hesla v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="94e60-163">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="94e60-164">Vytvoření aplikace ASP.NET Core s uživatelskými daty chráněnými autorizací</span><span class="sxs-lookup"><span data-stu-id="94e60-164">Create an ASP.NET Core app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)

---
title: Články založené na ASP.NET Core projektech vytvořených pomocí individuálních uživatelských účtů
author: rick-anderson
description: Objevte články na základě ASP.NET Core projektů vytvořených pomocí individuálních uživatelských účtů.
ms.author: riande
ms.date: 12/11/2019
uid: security/authentication/individual
ms.openlocfilehash: 7ef0d5eabded61d04fb9fe7be384a663ad7ea5f4
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828708"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a><span data-ttu-id="553fb-103">Články založené na ASP.NET Core projektech vytvořených pomocí individuálních uživatelských účtů</span><span class="sxs-lookup"><span data-stu-id="553fb-103">Articles based on ASP.NET Core projects created with individual user accounts</span></span>

<span data-ttu-id="553fb-104">ASP.NET Core identita je zahrnuta v šablonách projektů v aplikaci Visual Studio s možností jednotlivé uživatelské účty.</span><span class="sxs-lookup"><span data-stu-id="553fb-104">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="553fb-105">Šablony ověřování jsou dostupné v .NET Core CLI s `-au Individual`:</span><span class="sxs-lookup"><span data-stu-id="553fb-105">The authentication templates are available in .NET Core CLI with `-au Individual`:</span></span>

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

<span data-ttu-id="553fb-106">Podívejte se na [Tento problém GitHubu](https://github.com/dotnet/AspNetCore/issues/5833) pro ověřování webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="553fb-106">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5833) for web API authentication.</span></span>

<a name="no"></a>

## <a name="no-authentication"></a><span data-ttu-id="553fb-107">Bez ověřování</span><span class="sxs-lookup"><span data-stu-id="553fb-107">No Authentication</span></span>

<span data-ttu-id="553fb-108">Ověřování je zadáno v .NET Core CLI s možností `-au`.</span><span class="sxs-lookup"><span data-stu-id="553fb-108">Authentication is specified in the .NET Core CLI with the `-au` option.</span></span> <span data-ttu-id="553fb-109">V aplikaci Visual Studio je k dispozici dialog **Změna ověřování** pro nové webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="553fb-109">In Visual Studio, the **Change Authentication** dialog is available for new web applications.</span></span> <span data-ttu-id="553fb-110">Výchozí pro nové webové aplikace v aplikaci Visual Studio není **ověřování**.</span><span class="sxs-lookup"><span data-stu-id="553fb-110">The default for new web apps in Visual Studio is **No Authentication**.</span></span>

<span data-ttu-id="553fb-111">Projekty vytvořené bez ověřování:</span><span class="sxs-lookup"><span data-stu-id="553fb-111">Projects created with no authentication:</span></span>

* <span data-ttu-id="553fb-112">Neobsahujte webové stránky a uživatelské rozhraní pro přihlášení a odhlášení.</span><span class="sxs-lookup"><span data-stu-id="553fb-112">Don't contain web pages and UI to sign in and sign out.</span></span>
* <span data-ttu-id="553fb-113">Neobsahujte ověřovací kód.</span><span class="sxs-lookup"><span data-stu-id="553fb-113">Don't contain authentication code.</span></span>

<a name="win"></a>

## <a name="windows-authentication"></a><span data-ttu-id="553fb-114">Ověřování Windows</span><span class="sxs-lookup"><span data-stu-id="553fb-114">Windows Authentication</span></span>

<span data-ttu-id="553fb-115">Ověřování systému Windows je zadáno pro nové webové aplikace v .NET Core CLI s možností `-au Windows`.</span><span class="sxs-lookup"><span data-stu-id="553fb-115">Windows Authentication is specified for new web apps in the .NET Core CLI with the `-au Windows` option.</span></span> <span data-ttu-id="553fb-116">V aplikaci Visual Studio je v dialogovém okně pro **změnu ověřování** k dispozici možnost **ověřování systému Windows** .</span><span class="sxs-lookup"><span data-stu-id="553fb-116">In Visual Studio, the **Change Authentication** dialog provides the **Windows Authentication** options.</span></span>

<span data-ttu-id="553fb-117">Pokud je vybraná možnost ověřování systému Windows, aplikace je nakonfigurovaná tak, aby používala [modul služby IIS pro ověřování systému Windows](xref:host-and-deploy/iis/modules).</span><span class="sxs-lookup"><span data-stu-id="553fb-117">If Windows Authentication is selected, the app is configured to use the [Windows Authentication IIS module](xref:host-and-deploy/iis/modules).</span></span> <span data-ttu-id="553fb-118">Ověřování systému Windows je určeno pro intranetové weby.</span><span class="sxs-lookup"><span data-stu-id="553fb-118">Windows Authentication is intended for Intranet web sites.</span></span>

## <a name="dotnet-new-webapp-authentication-options"></a><span data-ttu-id="553fb-119">dotnet – nové možnosti ověřování WebApp</span><span class="sxs-lookup"><span data-stu-id="553fb-119">dotnet new webapp authentication options</span></span>

<span data-ttu-id="553fb-120">V následující tabulce jsou uvedené možnosti ověřování pro nové webové aplikace:</span><span class="sxs-lookup"><span data-stu-id="553fb-120">The following table shows the authentication options available for new web apps:</span></span>

| <span data-ttu-id="553fb-121">Možnost</span><span class="sxs-lookup"><span data-stu-id="553fb-121">Option</span></span> | <span data-ttu-id="553fb-122">Typ ověřování</span><span class="sxs-lookup"><span data-stu-id="553fb-122">Type of authentication</span></span> | <span data-ttu-id="553fb-123">Odkaz na další informace</span><span class="sxs-lookup"><span data-stu-id="553fb-123">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="553fb-124">Žádné</span><span class="sxs-lookup"><span data-stu-id="553fb-124">None</span></span>            |  <span data-ttu-id="553fb-125">Žádné ověřování</span><span class="sxs-lookup"><span data-stu-id="553fb-125">No authentication</span></span> | | 
| <span data-ttu-id="553fb-126">Jednotlivec</span><span class="sxs-lookup"><span data-stu-id="553fb-126">Individual</span></span>      |  <span data-ttu-id="553fb-127">Individuální ověřování</span><span class="sxs-lookup"><span data-stu-id="553fb-127">Individual authentication</span></span> | <xref:security/authentication/identity>
| <span data-ttu-id="553fb-128">IndividualB2C</span><span class="sxs-lookup"><span data-stu-id="553fb-128">IndividualB2C</span></span>   |  <span data-ttu-id="553fb-129">Individuální ověřování hostované v cloudu s Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="553fb-129">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="553fb-130">Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="553fb-130">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="553fb-131">SingleOrg</span><span class="sxs-lookup"><span data-stu-id="553fb-131">SingleOrg</span></span>       |  <span data-ttu-id="553fb-132">Ověřování organizace pro jednoho tenanta</span><span class="sxs-lookup"><span data-stu-id="553fb-132">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="553fb-133">Azure AD</span><span class="sxs-lookup"><span data-stu-id="553fb-133">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="553fb-134">MultiOrg</span><span class="sxs-lookup"><span data-stu-id="553fb-134">MultiOrg</span></span>        |  <span data-ttu-id="553fb-135">Ověřování organizace pro více tenantů</span><span class="sxs-lookup"><span data-stu-id="553fb-135">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="553fb-136">Azure AD</span><span class="sxs-lookup"><span data-stu-id="553fb-136">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="553fb-137">Windows</span><span class="sxs-lookup"><span data-stu-id="553fb-137">Windows</span></span>         |  <span data-ttu-id="553fb-138">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="553fb-138">Windows authentication</span></span> | [<span data-ttu-id="553fb-139">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="553fb-139">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="visual-studio-new-webapp-authentication-options"></a><span data-ttu-id="553fb-140">Nové možnosti ověřování WebApp sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="553fb-140">Visual Studio new webapp authentication options</span></span>

<span data-ttu-id="553fb-141">V následující tabulce jsou uvedeny možnosti ověřování, které jsou k dispozici při vytváření nové webové aplikace pomocí sady Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="553fb-141">The following table shows the authentication options available when creating a new web app with Visual Studio:</span></span>

| <span data-ttu-id="553fb-142">Možnost</span><span class="sxs-lookup"><span data-stu-id="553fb-142">Option</span></span> | <span data-ttu-id="553fb-143">Typ ověřování</span><span class="sxs-lookup"><span data-stu-id="553fb-143">Type of authentication</span></span> | <span data-ttu-id="553fb-144">Odkaz na další informace</span><span class="sxs-lookup"><span data-stu-id="553fb-144">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="553fb-145">Žádné</span><span class="sxs-lookup"><span data-stu-id="553fb-145">None</span></span>            |  <span data-ttu-id="553fb-146">Žádné ověřování</span><span class="sxs-lookup"><span data-stu-id="553fb-146">No authentication</span></span> | | 
| <span data-ttu-id="553fb-147">Individuální uživatelské účty/úložiště uživatelských účtů v aplikaci</span><span class="sxs-lookup"><span data-stu-id="553fb-147">Individual User Accounts / Store user accounts in-app</span></span> |  <span data-ttu-id="553fb-148">Individuální ověřování</span><span class="sxs-lookup"><span data-stu-id="553fb-148">Individual authentication</span></span> | <xref:security/authentication/identity> |
| <span data-ttu-id="553fb-149">Individuální uživatelské účty/připojení k existujícímu úložišti uživatelů v cloudu</span><span class="sxs-lookup"><span data-stu-id="553fb-149">Individual User Accounts / Connect to an existing user store in the cloud</span></span> |  <span data-ttu-id="553fb-150">Individuální ověřování hostované v cloudu s Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="553fb-150">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="553fb-151">Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="553fb-151">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="553fb-152">Pracovní nebo školní Cloud/jedna organizace</span><span class="sxs-lookup"><span data-stu-id="553fb-152">Work or School Cloud / Single Org</span></span>  |  <span data-ttu-id="553fb-153">Ověřování organizace pro jednoho tenanta</span><span class="sxs-lookup"><span data-stu-id="553fb-153">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="553fb-154">Azure AD</span><span class="sxs-lookup"><span data-stu-id="553fb-154">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="553fb-155">Pracovní nebo školní Cloud/několik organizací</span><span class="sxs-lookup"><span data-stu-id="553fb-155">Work or School Cloud / Multiple Org</span></span> |  <span data-ttu-id="553fb-156">Ověřování organizace pro více tenantů</span><span class="sxs-lookup"><span data-stu-id="553fb-156">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="553fb-157">Azure AD</span><span class="sxs-lookup"><span data-stu-id="553fb-157">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="553fb-158">Windows</span><span class="sxs-lookup"><span data-stu-id="553fb-158">Windows</span></span>         |  <span data-ttu-id="553fb-159">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="553fb-159">Windows authentication</span></span> | [<span data-ttu-id="553fb-160">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="553fb-160">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="additional-resources"></a><span data-ttu-id="553fb-161">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="553fb-161">Additional resources</span></span>

<span data-ttu-id="553fb-162">V následujících článcích se dozvíte, jak používat kód vygenerovaný v ASP.NET Core šablonách, které používají jednotlivé uživatelské účty:</span><span class="sxs-lookup"><span data-stu-id="553fb-162">The following articles show how to use the code generated in ASP.NET Core templates that use individual user accounts:</span></span>

* [<span data-ttu-id="553fb-163">Potvrzení účtu a obnovení hesla v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="553fb-163">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="553fb-164">Vytvoření aplikace ASP.NET Core s uživatelskými daty chráněnými autorizací</span><span class="sxs-lookup"><span data-stu-id="553fb-164">Create an ASP.NET Core app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)

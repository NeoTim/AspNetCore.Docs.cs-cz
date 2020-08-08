---
title: Cloudové ověřování pomocí Azure Active Directory B2C v ASP.NET Core
author: camsoper
description: Zjistěte, jak nastavit Azure Active Directory B2C ověřování pomocí ASP.NET Core.
ms.author: casoper
ms.custom: mvc
ms.date: 01/21/2019
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
uid: security/authentication/azure-ad-b2c
ms.openlocfilehash: 78fe4d5dd9e3f64789956e58a4490bef6bdbca1e
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021702"
---
# <a name="cloud-authentication-with-azure-active-directory-b2c-in-aspnet-core"></a><span data-ttu-id="19ae2-103">Cloudové ověřování pomocí Azure Active Directory B2C v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="19ae2-103">Cloud authentication with Azure Active Directory B2C in ASP.NET Core</span></span>

<span data-ttu-id="19ae2-104">[Soper vačky](https://twitter.com/camsoper)</span><span class="sxs-lookup"><span data-stu-id="19ae2-104">By [Cam Soper](https://twitter.com/camsoper)</span></span>

<span data-ttu-id="19ae2-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) je cloudové řešení pro správu identit pro webové a mobilní aplikace.</span><span class="sxs-lookup"><span data-stu-id="19ae2-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) is a cloud identity management solution for web and mobile apps.</span></span> <span data-ttu-id="19ae2-106">Služba zajišťuje ověřování pro aplikace hostované v cloudu i v místním prostředí.</span><span class="sxs-lookup"><span data-stu-id="19ae2-106">The service provides authentication for apps hosted in the cloud and on-premises.</span></span> <span data-ttu-id="19ae2-107">Mezi typy ověřování patří jednotlivé účty, účty sociálních sítí a federované podnikové účty.</span><span class="sxs-lookup"><span data-stu-id="19ae2-107">Authentication types include individual accounts, social network accounts, and federated enterprise accounts.</span></span> <span data-ttu-id="19ae2-108">Kromě toho Azure AD B2C možné poskytovat vícefaktorové ověřování s minimální konfigurací.</span><span class="sxs-lookup"><span data-stu-id="19ae2-108">Additionally, Azure AD B2C can provide multi-factor authentication with minimal configuration.</span></span>

> [!TIP]
> <span data-ttu-id="19ae2-109">Azure Active Directory (Azure AD) a Azure AD B2C jsou samostatné nabídky produktů.</span><span class="sxs-lookup"><span data-stu-id="19ae2-109">Azure Active Directory (Azure AD) and Azure AD B2C are separate product offerings.</span></span> <span data-ttu-id="19ae2-110">Tenant Azure AD představuje organizaci, zatímco tenant Azure AD B2C představuje kolekci identit, které se mají použít s aplikacemi předávající strany.</span><span class="sxs-lookup"><span data-stu-id="19ae2-110">An Azure AD tenant represents an organization, while an Azure AD B2C tenant represents a collection of identities to be used with relying party applications.</span></span> <span data-ttu-id="19ae2-111">Další informace najdete v tématu [Azure AD B2C: nejčastější dotazy (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).</span><span class="sxs-lookup"><span data-stu-id="19ae2-111">To learn more, see [Azure AD B2C: Frequently asked questions (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).</span></span>

<span data-ttu-id="19ae2-112">V tomto kurzu získáte informace o těchto tématech:</span><span class="sxs-lookup"><span data-stu-id="19ae2-112">In this tutorial, learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="19ae2-113">Vytvoření tenanta Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="19ae2-113">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="19ae2-114">Registrace aplikace v Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="19ae2-114">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="19ae2-115">Pomocí sady Visual Studio vytvořit webovou aplikaci ASP.NET Core nakonfigurovanou tak, aby pro ověřování používala klienta Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="19ae2-115">Use Visual Studio to create an ASP.NET Core web app configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="19ae2-116">Konfigurace zásad řídících chování klienta Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="19ae2-116">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

## <a name="prerequisites"></a><span data-ttu-id="19ae2-117">Požadavky</span><span class="sxs-lookup"><span data-stu-id="19ae2-117">Prerequisites</span></span>

<span data-ttu-id="19ae2-118">V tomto návodu jsou vyžadovány následující:</span><span class="sxs-lookup"><span data-stu-id="19ae2-118">The following are required for this walkthrough:</span></span>

* [<span data-ttu-id="19ae2-119">Předplatné Microsoft Azure</span><span class="sxs-lookup"><span data-stu-id="19ae2-119">Microsoft Azure subscription</span></span>](https://azure.microsoft.com/free/dotnet/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [<span data-ttu-id="19ae2-120">Visual Studio 2019</span><span class="sxs-lookup"><span data-stu-id="19ae2-120">Visual Studio 2019</span></span>](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

## <a name="create-the-azure-active-directory-b2c-tenant"></a><span data-ttu-id="19ae2-121">Vytvoření tenanta Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="19ae2-121">Create the Azure Active Directory B2C tenant</span></span>

<span data-ttu-id="19ae2-122">Vytvořte tenanta Azure Active Directory B2C [, jak je popsáno v dokumentaci](/azure/active-directory-b2c/active-directory-b2c-get-started).</span><span class="sxs-lookup"><span data-stu-id="19ae2-122">Create an Azure Active Directory B2C tenant [as described in the documentation](/azure/active-directory-b2c/active-directory-b2c-get-started).</span></span> <span data-ttu-id="19ae2-123">Po zobrazení výzvy přiřadíte tenanta k předplatnému Azure, které je pro tento kurz volitelné.</span><span class="sxs-lookup"><span data-stu-id="19ae2-123">When prompted, associating the tenant with an Azure subscription is optional for this tutorial.</span></span>

## <a name="register-the-app-in-azure-ad-b2c"></a><span data-ttu-id="19ae2-124">Registrace aplikace v Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="19ae2-124">Register the app in Azure AD B2C</span></span>

<span data-ttu-id="19ae2-125">V nově vytvořeném tenantovi Azure AD B2C Zaregistrujte svoji aplikaci pomocí [kroků v dokumentaci](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) v části **zaregistrovat webovou aplikaci** .</span><span class="sxs-lookup"><span data-stu-id="19ae2-125">In the newly created Azure AD B2C tenant, register your app using [the steps in the documentation](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) under the **Register a web app** section.</span></span> <span data-ttu-id="19ae2-126">Zastavte se v části **Vytvoření tajného klíče klienta webové aplikace** .</span><span class="sxs-lookup"><span data-stu-id="19ae2-126">Stop at the **Create a web app client secret** section.</span></span> <span data-ttu-id="19ae2-127">Pro tento kurz není nutný tajný kód klienta.</span><span class="sxs-lookup"><span data-stu-id="19ae2-127">A client secret isn't required for this tutorial.</span></span> 

<span data-ttu-id="19ae2-128">Použijte následující hodnoty:</span><span class="sxs-lookup"><span data-stu-id="19ae2-128">Use the following values:</span></span>

| <span data-ttu-id="19ae2-129">Nastavení</span><span class="sxs-lookup"><span data-stu-id="19ae2-129">Setting</span></span>                       | <span data-ttu-id="19ae2-130">Hodnota</span><span class="sxs-lookup"><span data-stu-id="19ae2-130">Value</span></span>                     | <span data-ttu-id="19ae2-131">Poznámky</span><span class="sxs-lookup"><span data-stu-id="19ae2-131">Notes</span></span>                                                                                                                                                                                              |
|-------------------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="19ae2-132">**Název**</span><span class="sxs-lookup"><span data-stu-id="19ae2-132">**Name**</span></span>                      | <span data-ttu-id="19ae2-133">*&lt;název aplikace&gt;*</span><span class="sxs-lookup"><span data-stu-id="19ae2-133">*&lt;app name&gt;*</span></span>        | <span data-ttu-id="19ae2-134">Zadejte **název** aplikace, který popíše vaši aplikaci pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="19ae2-134">Enter a **Name** for the app that describes your app to consumers.</span></span>                                                                                                                                 |
| <span data-ttu-id="19ae2-135">**Zahrnout webovou aplikaci nebo webové rozhraní API**</span><span class="sxs-lookup"><span data-stu-id="19ae2-135">**Include web app / web API**</span></span> | <span data-ttu-id="19ae2-136">Ano</span><span class="sxs-lookup"><span data-stu-id="19ae2-136">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="19ae2-137">**Povolit implicitní tok**</span><span class="sxs-lookup"><span data-stu-id="19ae2-137">**Allow implicit flow**</span></span>       | <span data-ttu-id="19ae2-138">Ano</span><span class="sxs-lookup"><span data-stu-id="19ae2-138">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="19ae2-139">**Adresa URL odpovědi**</span><span class="sxs-lookup"><span data-stu-id="19ae2-139">**Reply URL**</span></span>                 | `https://localhost:44300/signin-oidc` | <span data-ttu-id="19ae2-140">Adresy URL odpovědí jsou koncové body, kam Azure AD B2C vrací všechny tokeny, které vaše aplikace požaduje.</span><span class="sxs-lookup"><span data-stu-id="19ae2-140">Reply URLs are endpoints where Azure AD B2C returns any tokens that your app requests.</span></span> <span data-ttu-id="19ae2-141">Visual Studio poskytuje adresu URL odpovědi, která se má použít.</span><span class="sxs-lookup"><span data-stu-id="19ae2-141">Visual Studio provides the Reply URL to use.</span></span> <span data-ttu-id="19ae2-142">Prozatím zadejte `https://localhost:44300/signin-oidc` pro vyplnění formuláře.</span><span class="sxs-lookup"><span data-stu-id="19ae2-142">For now, enter `https://localhost:44300/signin-oidc` to complete the form.</span></span> |
| <span data-ttu-id="19ae2-143">**Identifikátor URI ID aplikace**</span><span class="sxs-lookup"><span data-stu-id="19ae2-143">**App ID URI**</span></span>                | <span data-ttu-id="19ae2-144">Ponechte prázdné</span><span class="sxs-lookup"><span data-stu-id="19ae2-144">Leave blank</span></span>               | <span data-ttu-id="19ae2-145">Pro tento kurz není vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="19ae2-145">Not required for this tutorial.</span></span>                                                                                                                                                                    |
| <span data-ttu-id="19ae2-146">**Zahrnout nativního klienta**</span><span class="sxs-lookup"><span data-stu-id="19ae2-146">**Include native client**</span></span>     | <span data-ttu-id="19ae2-147">Ne</span><span class="sxs-lookup"><span data-stu-id="19ae2-147">No</span></span>                        |                                                                                                                                                                                                    |

> [!WARNING]
> <span data-ttu-id="19ae2-148">Pokud nastavili adresu URL odpovědi, která není localhost, pamatujte na [omezení, co je v seznamu Adresa URL odpovědi povolené](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application).</span><span class="sxs-lookup"><span data-stu-id="19ae2-148">If setting up a non-localhost Reply URL, be aware of the [constraints on what is allowed in the Reply URL list](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application).</span></span> 

<span data-ttu-id="19ae2-149">Po registraci aplikace se zobrazí seznam aplikací v tenantovi.</span><span class="sxs-lookup"><span data-stu-id="19ae2-149">After the app is registered, the list of apps in the tenant is displayed.</span></span> <span data-ttu-id="19ae2-150">Vyberte aplikaci, která byla právě zaregistrována.</span><span class="sxs-lookup"><span data-stu-id="19ae2-150">Select the app that was just registered.</span></span> <span data-ttu-id="19ae2-151">Vyberte ikonu **kopírování** napravo od pole **ID aplikace** a zkopírujte ji do schránky.</span><span class="sxs-lookup"><span data-stu-id="19ae2-151">Select the **Copy** icon to the right of the **Application ID** field to copy it to the clipboard.</span></span>

<span data-ttu-id="19ae2-152">V tuto chvíli není možné v tenantovi Azure AD B2C nakonfigurovat nic dalšího, ale okno prohlížeče zůstane otevřené.</span><span class="sxs-lookup"><span data-stu-id="19ae2-152">Nothing more can be configured in the Azure AD B2C tenant at this time, but leave the browser window open.</span></span> <span data-ttu-id="19ae2-153">Po vytvoření ASP.NET Core aplikace je k dispozici více konfigurací.</span><span class="sxs-lookup"><span data-stu-id="19ae2-153">There is more configuration after the ASP.NET Core app is created.</span></span>

## <a name="create-an-aspnet-core-app-in-visual-studio"></a><span data-ttu-id="19ae2-154">Vytvoření aplikace ASP.NET Core v aplikaci Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19ae2-154">Create an ASP.NET Core app in Visual Studio</span></span>

<span data-ttu-id="19ae2-155">Šablonu webové aplikace Visual Studio je možné nakonfigurovat tak, aby pro ověřování používala klienta Azure AD B2C.</span><span class="sxs-lookup"><span data-stu-id="19ae2-155">The Visual Studio Web Application template can be configured to use the Azure AD B2C tenant for authentication.</span></span>

<span data-ttu-id="19ae2-156">V aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="19ae2-156">In Visual Studio:</span></span>

1. <span data-ttu-id="19ae2-157">Vytvořte novou ASP.NET Core webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="19ae2-157">Create a new ASP.NET Core Web Application.</span></span> 
2. <span data-ttu-id="19ae2-158">V seznamu šablon vyberte možnost **Webová aplikace** .</span><span class="sxs-lookup"><span data-stu-id="19ae2-158">Select **Web Application** from the list of templates.</span></span>
3. <span data-ttu-id="19ae2-159">Vyberte tlačítko **změnit ověřování** .</span><span class="sxs-lookup"><span data-stu-id="19ae2-159">Select the **Change Authentication** button.</span></span>
    
    ![Tlačítko změnit ověřování](./azure-ad-b2c/_static/changeauth.png)

4. <span data-ttu-id="19ae2-161">V dialogu **změnit ověřování** vyberte **jednotlivé uživatelské účty**a potom v rozevíracím seznamu vyberte **připojit k existujícímu úložišti uživatelů v cloudu** .</span><span class="sxs-lookup"><span data-stu-id="19ae2-161">In the **Change Authentication** dialog, select **Individual User Accounts**, and then select **Connect to an existing user store in the cloud** in the dropdown.</span></span> 
    
    ![Dialogové okno Změnit ověřování](./azure-ad-b2c/_static/changeauthdialog.png)

5. <span data-ttu-id="19ae2-163">Vyplňte formulář s následujícími hodnotami:</span><span class="sxs-lookup"><span data-stu-id="19ae2-163">Complete the form with the following values:</span></span>
    
    | <span data-ttu-id="19ae2-164">Nastavení</span><span class="sxs-lookup"><span data-stu-id="19ae2-164">Setting</span></span>                       | <span data-ttu-id="19ae2-165">Hodnota</span><span class="sxs-lookup"><span data-stu-id="19ae2-165">Value</span></span>                                                 |
    |-------------------------------|-------------------------------------------------------|
    | <span data-ttu-id="19ae2-166">**Název domény**</span><span class="sxs-lookup"><span data-stu-id="19ae2-166">**Domain Name**</span></span>               | <span data-ttu-id="19ae2-167">*&lt;název domény vašeho tenanta B2C&gt;*</span><span class="sxs-lookup"><span data-stu-id="19ae2-167">*&lt;the domain name of your B2C tenant&gt;*</span></span>          |
    | <span data-ttu-id="19ae2-168">**ID aplikace**</span><span class="sxs-lookup"><span data-stu-id="19ae2-168">**Application ID**</span></span>            | <span data-ttu-id="19ae2-169">*&lt;Vložit ID aplikace ze schránky&gt;*</span><span class="sxs-lookup"><span data-stu-id="19ae2-169">*&lt;paste the Application ID from the clipboard&gt;*</span></span> |
    | <span data-ttu-id="19ae2-170">**Cesta zpětného volání**</span><span class="sxs-lookup"><span data-stu-id="19ae2-170">**Callback Path**</span></span>             | <span data-ttu-id="19ae2-171">*&lt;použít výchozí hodnotu&gt;*</span><span class="sxs-lookup"><span data-stu-id="19ae2-171">*&lt;use the default value&gt;*</span></span>                       |
    | <span data-ttu-id="19ae2-172">**Zásady registrace nebo přihlašování**</span><span class="sxs-lookup"><span data-stu-id="19ae2-172">**Sign-up or sign-in policy**</span></span> | `B2C_1_SiUpIn`                                        |
    | <span data-ttu-id="19ae2-173">**Resetovat zásady hesel**</span><span class="sxs-lookup"><span data-stu-id="19ae2-173">**Reset password policy**</span></span>     | `B2C_1_SSPR`                                          |
    | <span data-ttu-id="19ae2-174">**Upravit zásady profilu**</span><span class="sxs-lookup"><span data-stu-id="19ae2-174">**Edit profile policy**</span></span>       | <span data-ttu-id="19ae2-175">*&lt;ponechat prázdné&gt;*</span><span class="sxs-lookup"><span data-stu-id="19ae2-175">*&lt;leave blank&gt;*</span></span>                                 |
    
    <span data-ttu-id="19ae2-176">Vyberte odkaz **Kopírovat** vedle **identifikátoru URI odpovědi** pro zkopírování identifikátoru URI odpovědi do schránky.</span><span class="sxs-lookup"><span data-stu-id="19ae2-176">Select the **Copy** link next to **Reply URI** to copy the Reply URI to the clipboard.</span></span> <span data-ttu-id="19ae2-177">Kliknutím na **tlačítko OK** zavřete dialogové okno **změnit ověřování** .</span><span class="sxs-lookup"><span data-stu-id="19ae2-177">Select **OK** to close the **Change Authentication** dialog.</span></span> <span data-ttu-id="19ae2-178">Vyberte **OK** a vytvořte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="19ae2-178">Select **OK** to create the web app.</span></span>

## <a name="finish-the-b2c-app-registration"></a><span data-ttu-id="19ae2-179">Dokončení registrace aplikace B2C</span><span class="sxs-lookup"><span data-stu-id="19ae2-179">Finish the B2C app registration</span></span>

<span data-ttu-id="19ae2-180">Vraťte se do okna prohlížeče, ve kterém jsou pořád otevřené vlastnosti aplikace B2C.</span><span class="sxs-lookup"><span data-stu-id="19ae2-180">Return to the browser window with the B2C app properties still open.</span></span> <span data-ttu-id="19ae2-181">Změňte zadanou **adresu URL dočasné odpovědi** na hodnotu zkopírovanou ze sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="19ae2-181">Change the temporary **Reply URL** specified earlier to the value copied from Visual Studio.</span></span> <span data-ttu-id="19ae2-182">V horní části okna vyberte **Uložit** .</span><span class="sxs-lookup"><span data-stu-id="19ae2-182">Select **Save** at the top of the window.</span></span>

> [!TIP]
> <span data-ttu-id="19ae2-183">Pokud jste nezkopírovali adresu URL odpovědi, použijte adresu HTTPS z karty ladění ve vlastnostech webového projektu a přidejte hodnotu **CallbackPath** z *appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="19ae2-183">If you didn't copy the Reply URL, use the HTTPS address from the Debug tab in the web project properties, and append the **CallbackPath** value from *appsettings.json*.</span></span>

## <a name="configure-policies"></a><span data-ttu-id="19ae2-184">Konfigurace zásad</span><span class="sxs-lookup"><span data-stu-id="19ae2-184">Configure policies</span></span>

<span data-ttu-id="19ae2-185">Pomocí kroků v dokumentaci Azure AD B2C [vytvořte zásadu registrace nebo přihlašování](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions)a pak [Vytvořte zásady resetování hesel](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions).</span><span class="sxs-lookup"><span data-stu-id="19ae2-185">Use the steps in the Azure AD B2C documentation to [create a sign-up or sign-in policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions), and then [create a password reset policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions).</span></span> <span data-ttu-id="19ae2-186">Použijte ukázkové hodnoty uvedené v dokumentaci pro \*\* Identity poskytovatele\*\*, **atributy registrace**a **deklarace aplikací**.</span><span class="sxs-lookup"><span data-stu-id="19ae2-186">Use the example values provided in the documentation for **Identity providers**, **Sign-up attributes**, and **Application claims**.</span></span> <span data-ttu-id="19ae2-187">Použití tlačítka **Spustit nyní** k otestování zásad, jak je popsáno v dokumentaci, je volitelné.</span><span class="sxs-lookup"><span data-stu-id="19ae2-187">Using the **Run now** button to test the policies as described in the documentation is optional.</span></span>

> [!WARNING]
> <span data-ttu-id="19ae2-188">Zajistěte, aby názvy zásad byly přesně popsané v dokumentaci, protože se tyto zásady používaly v dialogovém okně **změnit ověřování** v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="19ae2-188">Ensure the policy names are exactly as described in the documentation, as those policies were used in the **Change Authentication** dialog in Visual Studio.</span></span> <span data-ttu-id="19ae2-189">Názvy zásad lze ověřit v *appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="19ae2-189">The policy names can be verified in *appsettings.json*.</span></span>

## <a name="configure-the-underlying-openidconnectoptionsjwtbearerno-loccookie-options"></a><span data-ttu-id="19ae2-190">Konfigurovat základní OpenIdConnectOptions/JwtBearer/ Cookie Možnosti</span><span class="sxs-lookup"><span data-stu-id="19ae2-190">Configure the underlying OpenIdConnectOptions/JwtBearer/Cookie options</span></span>

<span data-ttu-id="19ae2-191">Chcete-li konfigurovat základní možnosti přímo, použijte odpovídající konstanty schématu v nástroji `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="19ae2-191">To configure the underlying options directly, use the appropriate scheme constant in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(
    AzureAD[B2C]Defaults.OpenIdScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<CookieAuthenticationOptions>(
    AzureAD[B2C]Defaults.CookieScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<JwtBearerOptions>(
    AzureAD[B2C]Defaults.JwtBearerAuthenticationScheme, options => 
    {
        // Omitted for brevity
    });
```

## <a name="run-the-app"></a><span data-ttu-id="19ae2-192">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="19ae2-192">Run the app</span></span>

<span data-ttu-id="19ae2-193">V aplikaci Visual Studio stiskněte klávesu **F5** a sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="19ae2-193">In Visual Studio, press **F5** to build and run the app.</span></span> <span data-ttu-id="19ae2-194">Po spuštění webové aplikace vyberte **přijmout** , aby se přijměte použití cookie s (Pokud se zobrazí výzva), a pak vyberte **Přihlásit**se.</span><span class="sxs-lookup"><span data-stu-id="19ae2-194">After the web app launches, select **Accept** to accept the use of cookies (if prompted), and then select **Sign in**.</span></span>

![Přihlášení k aplikaci](./azure-ad-b2c/_static/signin.png)

<span data-ttu-id="19ae2-196">Prohlížeč přesměruje na tenanta Azure AD B2C.</span><span class="sxs-lookup"><span data-stu-id="19ae2-196">The browser redirects to the Azure AD B2C tenant.</span></span> <span data-ttu-id="19ae2-197">Přihlaste se pomocí existujícího účtu (Pokud se vytvořila možnost testování zásad), nebo vyberte **zaregistrovat** se a vytvořte nový účet.</span><span class="sxs-lookup"><span data-stu-id="19ae2-197">Sign in with an existing account (if one was created testing the policies) or select **Sign up now** to create a new account.</span></span> <span data-ttu-id="19ae2-198">Odkaz **zapomenuté heslo** slouží k resetování zapomenutého hesla.</span><span class="sxs-lookup"><span data-stu-id="19ae2-198">The **Forgot your password?** link is used to reset a forgotten password.</span></span>

![Přihlášení do Azure AD B2C](./azure-ad-b2c/_static/b2csts.png)

<span data-ttu-id="19ae2-200">Po úspěšném přihlášení se prohlížeč přesměruje na webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="19ae2-200">After successfully signing in, the browser redirects to the web app.</span></span>

![Success](./azure-ad-b2c/_static/success.png)

## <a name="next-steps"></a><span data-ttu-id="19ae2-202">Další kroky</span><span class="sxs-lookup"><span data-stu-id="19ae2-202">Next steps</span></span>

<span data-ttu-id="19ae2-203">V tomto kurzu jste se naučili:</span><span class="sxs-lookup"><span data-stu-id="19ae2-203">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="19ae2-204">Vytvoření tenanta Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="19ae2-204">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="19ae2-205">Registrace aplikace v Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="19ae2-205">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="19ae2-206">Použijte Visual Studio k vytvoření ASP.NET Core webové aplikace, která je nakonfigurovaná tak, aby používala klienta Azure AD B2C pro ověřování.</span><span class="sxs-lookup"><span data-stu-id="19ae2-206">Use Visual Studio to create an ASP.NET Core Web Application configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="19ae2-207">Konfigurace zásad řídících chování klienta Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="19ae2-207">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

<span data-ttu-id="19ae2-208">Teď, když je aplikace ASP.NET Core nakonfigurovaná tak, aby používala Azure AD B2C k ověřování, můžete k zabezpečení vaší aplikace použít [atribut autorizovat](xref:security/authorization/simple) .</span><span class="sxs-lookup"><span data-stu-id="19ae2-208">Now that the ASP.NET Core app is configured to use Azure AD B2C for authentication, the [Authorize attribute](xref:security/authorization/simple) can be used to secure your app.</span></span> <span data-ttu-id="19ae2-209">Pokračujte v vývoji aplikace pomocí učení:</span><span class="sxs-lookup"><span data-stu-id="19ae2-209">Continue developing your app by learning to:</span></span>

* <span data-ttu-id="19ae2-210">[Přizpůsobení Azure AD B2Cho uživatelského rozhraní](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).</span><span class="sxs-lookup"><span data-stu-id="19ae2-210">[Customize the Azure AD B2C user interface](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).</span></span>
* <span data-ttu-id="19ae2-211">[Konfigurace požadavků na složitost hesla](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity)</span><span class="sxs-lookup"><span data-stu-id="19ae2-211">[Configure password complexity requirements](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).</span></span>
* <span data-ttu-id="19ae2-212">[Povolte službu Multi-Factor Authentication](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).</span><span class="sxs-lookup"><span data-stu-id="19ae2-212">[Enable multi-factor authentication](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).</span></span>
* <span data-ttu-id="19ae2-213">Nakonfigurujte další zprostředkovatele identity, jako je [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app)a další.</span><span class="sxs-lookup"><span data-stu-id="19ae2-213">Configure additional identity providers, such as [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app), and others.</span></span>
* <span data-ttu-id="19ae2-214">[Pomocí Graph API Azure AD](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) načtěte Další informace o uživateli, například členství ve skupině, z tenanta Azure AD B2C.</span><span class="sxs-lookup"><span data-stu-id="19ae2-214">[Use the Azure AD Graph API](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) to retrieve additional user information, such as group membership, from the Azure AD B2C tenant.</span></span>
* <span data-ttu-id="19ae2-215">[Zabezpečte ASP.NET Core webové rozhraní API pomocí Azure AD B2C](https://azure.microsoft.com/resources/samples/active-directory-b2c-dotnetcore-webapi/).</span><span class="sxs-lookup"><span data-stu-id="19ae2-215">[Secure an ASP.NET Core web API using Azure AD B2C](https://azure.microsoft.com/resources/samples/active-directory-b2c-dotnetcore-webapi/).</span></span>
* <span data-ttu-id="19ae2-216">[Kurz: poskytnutí přístupu k webovému rozhraní API ASP.NET pomocí Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-web-api-dotnet).</span><span class="sxs-lookup"><span data-stu-id="19ae2-216">[Tutorial: Grant access to an ASP.NET web API using Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-web-api-dotnet).</span></span>

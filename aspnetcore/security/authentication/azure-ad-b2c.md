---
title: Ověření cloudu s Azure Active Directory B2C v ASP.NET Core
author: camsoper
description: Zjistěte, jak nastavit ověřování Azure Active Directory B2C s ASP.NET Core.
ms.date: 02/27/2019
ms.custom: mvc
uid: security/authentication/azure-ad-b2c
ms.openlocfilehash: 46654e38c780cfc9c44e7ba479419320ee684b76
ms.sourcegitcommit: 6afe57fb8d9055f88fedb92b16470398c4b9b24a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65610695"
---
# <a name="cloud-authentication-with-azure-active-directory-b2c-in-aspnet-core"></a><span data-ttu-id="250f0-103">Ověření cloudu s Azure Active Directory B2C v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="250f0-103">Cloud authentication with Azure Active Directory B2C in ASP.NET Core</span></span>

<span data-ttu-id="250f0-104">Podle [kamera Soper](https://twitter.com/camsoper)</span><span class="sxs-lookup"><span data-stu-id="250f0-104">By [Cam Soper](https://twitter.com/camsoper)</span></span>

<span data-ttu-id="250f0-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) je cloudové řešení správy identit pro webové a mobilní aplikace.</span><span class="sxs-lookup"><span data-stu-id="250f0-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) is a cloud identity management solution for web and mobile apps.</span></span> <span data-ttu-id="250f0-106">Tato služba poskytuje ověřování pro aplikace hostované v cloudu i lokálně.</span><span class="sxs-lookup"><span data-stu-id="250f0-106">The service provides authentication for apps hosted in the cloud and on-premises.</span></span> <span data-ttu-id="250f0-107">Typy ověřování zahrnují jednotlivé účty, účty sociálních sítí a podnikových účtů federovaných.</span><span class="sxs-lookup"><span data-stu-id="250f0-107">Authentication types include individual accounts, social network accounts, and federated enterprise accounts.</span></span> <span data-ttu-id="250f0-108">Kromě toho Azure AD B2C umožňuje ověřování službou Multi-Factor Authentication s minimální konfigurací.</span><span class="sxs-lookup"><span data-stu-id="250f0-108">Additionally, Azure AD B2C can provide multi-factor authentication with minimal configuration.</span></span>

> [!TIP]
> <span data-ttu-id="250f0-109">Azure Active Directory (Azure AD) a Azure AD B2C jsou nabídky samostatných produktů.</span><span class="sxs-lookup"><span data-stu-id="250f0-109">Azure Active Directory (Azure AD) and Azure AD B2C are separate product offerings.</span></span> <span data-ttu-id="250f0-110">Klient služby Azure AD představuje organizace, zatímco tenanta služby Azure AD B2C představuje kolekci identit pro použití s aplikacemi předávajících stran.</span><span class="sxs-lookup"><span data-stu-id="250f0-110">An Azure AD tenant represents an organization, while an Azure AD B2C tenant represents a collection of identities to be used with relying party applications.</span></span> <span data-ttu-id="250f0-111">Další informace najdete v tématu [Azure AD B2C: Nejčastější dotazy (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).</span><span class="sxs-lookup"><span data-stu-id="250f0-111">To learn more, see [Azure AD B2C: Frequently asked questions (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).</span></span>

<span data-ttu-id="250f0-112">V tomto kurzu se dozvíte, jak:</span><span class="sxs-lookup"><span data-stu-id="250f0-112">In this tutorial, learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="250f0-113">Vytvoření tenanta Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="250f0-113">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="250f0-114">Registrace aplikace v Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="250f0-114">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="250f0-115">Vytvoření webové aplikace ASP.NET Core umožňují použít tenanta Azure AD B2C k ověřování pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="250f0-115">Use Visual Studio to create an ASP.NET Core web app configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="250f0-116">Konfigurace zásad řízení chování tenanta Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="250f0-116">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

## <a name="prerequisites"></a><span data-ttu-id="250f0-117">Požadavky</span><span class="sxs-lookup"><span data-stu-id="250f0-117">Prerequisites</span></span>

<span data-ttu-id="250f0-118">Vyžadují splnění následujících předpokladů pro Tento názorný postup:</span><span class="sxs-lookup"><span data-stu-id="250f0-118">The following are required for this walkthrough:</span></span>

* [<span data-ttu-id="250f0-119">Předplatné Microsoft Azure</span><span class="sxs-lookup"><span data-stu-id="250f0-119">Microsoft Azure subscription</span></span>](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [<span data-ttu-id="250f0-120">Visual Studio 2019</span><span class="sxs-lookup"><span data-stu-id="250f0-120">Visual Studio 2019</span></span>](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

## <a name="create-the-azure-active-directory-b2c-tenant"></a><span data-ttu-id="250f0-121">Vytvoření tenanta Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="250f0-121">Create the Azure Active Directory B2C tenant</span></span>

<span data-ttu-id="250f0-122">Vytvoření tenanta Azure Active Directory B2C [jak je popsáno v dokumentaci k](/azure/active-directory-b2c/active-directory-b2c-get-started).</span><span class="sxs-lookup"><span data-stu-id="250f0-122">Create an Azure Active Directory B2C tenant [as described in the documentation](/azure/active-directory-b2c/active-directory-b2c-get-started).</span></span> <span data-ttu-id="250f0-123">Po zobrazení výzvy přidružení tenanta s předplatným Azure je pro účely tohoto kurzu volitelný.</span><span class="sxs-lookup"><span data-stu-id="250f0-123">When prompted, associating the tenant with an Azure subscription is optional for this tutorial.</span></span>

## <a name="register-the-app-in-azure-ad-b2c"></a><span data-ttu-id="250f0-124">Zaregistrovat aplikaci v Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="250f0-124">Register the app in Azure AD B2C</span></span>

<span data-ttu-id="250f0-125">V nově vytvořeného tenanta Azure AD B2C registrovat vaši aplikaci s použitím [kroky v dokumentaci k](/azure/active-directory-b2c/active-directory-b2c-app-registration#register-a-web-app) pod **zaregistrovat webovou aplikaci** oddílu.</span><span class="sxs-lookup"><span data-stu-id="250f0-125">In the newly created Azure AD B2C tenant, register your app using [the steps in the documentation](/azure/active-directory-b2c/active-directory-b2c-app-registration#register-a-web-app) under the **Register a web app** section.</span></span> <span data-ttu-id="250f0-126">Zastavení při **vytvořit tajný kód klienta aplikace webového** oddílu.</span><span class="sxs-lookup"><span data-stu-id="250f0-126">Stop at the **Create a web app client secret** section.</span></span> <span data-ttu-id="250f0-127">Pro účely tohoto kurzu není nutné tajný kód klienta.</span><span class="sxs-lookup"><span data-stu-id="250f0-127">A client secret isn't required for this tutorial.</span></span> 

<span data-ttu-id="250f0-128">Použijte následující hodnoty:</span><span class="sxs-lookup"><span data-stu-id="250f0-128">Use the following values:</span></span>

| <span data-ttu-id="250f0-129">Nastavení</span><span class="sxs-lookup"><span data-stu-id="250f0-129">Setting</span></span>                       | <span data-ttu-id="250f0-130">Hodnota</span><span class="sxs-lookup"><span data-stu-id="250f0-130">Value</span></span>                     | <span data-ttu-id="250f0-131">Poznámky</span><span class="sxs-lookup"><span data-stu-id="250f0-131">Notes</span></span>                                                                                                                                                                                              |
|-------------------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="250f0-132">**Název**</span><span class="sxs-lookup"><span data-stu-id="250f0-132">**Name**</span></span>                      | <span data-ttu-id="250f0-133">*&lt;Název aplikace&gt;*</span><span class="sxs-lookup"><span data-stu-id="250f0-133">*&lt;app name&gt;*</span></span>        | <span data-ttu-id="250f0-134">Zadejte **název** pro aplikace, který popisuje vaši aplikaci pro zákazníky.</span><span class="sxs-lookup"><span data-stu-id="250f0-134">Enter a **Name** for the app that describes your app to consumers.</span></span>                                                                                                                                 |
| <span data-ttu-id="250f0-135">**Zahrnout webovou aplikaci / webové rozhraní API**</span><span class="sxs-lookup"><span data-stu-id="250f0-135">**Include web app / web API**</span></span> | <span data-ttu-id="250f0-136">Ano</span><span class="sxs-lookup"><span data-stu-id="250f0-136">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="250f0-137">**Povolit implicitní tok**</span><span class="sxs-lookup"><span data-stu-id="250f0-137">**Allow implicit flow**</span></span>       | <span data-ttu-id="250f0-138">Ano</span><span class="sxs-lookup"><span data-stu-id="250f0-138">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="250f0-139">**Adresa URL odpovědi**</span><span class="sxs-lookup"><span data-stu-id="250f0-139">**Reply URL**</span></span>                 | `https://localhost:44300/signin-oidc` | <span data-ttu-id="250f0-140">Adresy URL odpovědí jsou koncové body, kam Azure AD B2C vrací všechny tokeny, které vaše aplikace požaduje.</span><span class="sxs-lookup"><span data-stu-id="250f0-140">Reply URLs are endpoints where Azure AD B2C returns any tokens that your app requests.</span></span> <span data-ttu-id="250f0-141">Visual Studio poskytuje pomocí adresy URL odpovědi.</span><span class="sxs-lookup"><span data-stu-id="250f0-141">Visual Studio provides the Reply URL to use.</span></span> <span data-ttu-id="250f0-142">Teď zadejte `https://localhost:44300/signin-oidc` k vyplnění formuláře.</span><span class="sxs-lookup"><span data-stu-id="250f0-142">For now, enter `https://localhost:44300/signin-oidc` to complete the form.</span></span> |
| <span data-ttu-id="250f0-143">**Identifikátor URI ID aplikace**</span><span class="sxs-lookup"><span data-stu-id="250f0-143">**App ID URI**</span></span>                | <span data-ttu-id="250f0-144">Ponechte prázdné</span><span class="sxs-lookup"><span data-stu-id="250f0-144">Leave blank</span></span>               | <span data-ttu-id="250f0-145">Pro účely tohoto kurzu není nutné.</span><span class="sxs-lookup"><span data-stu-id="250f0-145">Not required for this tutorial.</span></span>                                                                                                                                                                    |
| <span data-ttu-id="250f0-146">**Zahrnout nativního klienta**</span><span class="sxs-lookup"><span data-stu-id="250f0-146">**Include native client**</span></span>     | <span data-ttu-id="250f0-147">Ne</span><span class="sxs-lookup"><span data-stu-id="250f0-147">No</span></span>                        |                                                                                                                                                                                                    |

> [!WARNING]
> <span data-ttu-id="250f0-148">Pokud nastavení adresy URL odpovědi jiných localhost, nezapomínejte [omezení v seznamu adresy URL odpovědi je povolené](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-web-app-or-api-reply-url).</span><span class="sxs-lookup"><span data-stu-id="250f0-148">If setting up a non-localhost Reply URL, be aware of the [constraints on what is allowed in the Reply URL list](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-web-app-or-api-reply-url).</span></span> 

<span data-ttu-id="250f0-149">Po registraci aplikace, zobrazí se seznam aplikací v tenantovi.</span><span class="sxs-lookup"><span data-stu-id="250f0-149">After the app is registered, the list of apps in the tenant is displayed.</span></span> <span data-ttu-id="250f0-150">Vyberte aplikaci, která byla právě zaregistrováno.</span><span class="sxs-lookup"><span data-stu-id="250f0-150">Select the app that was just registered.</span></span> <span data-ttu-id="250f0-151">Vyberte **kopírování** ikony napravo **ID aplikace** pole, které chcete zkopírovat do schránky.</span><span class="sxs-lookup"><span data-stu-id="250f0-151">Select the **Copy** icon to the right of the **Application ID** field to copy it to the clipboard.</span></span>

<span data-ttu-id="250f0-152">Žádné informace se dá nakonfigurovat v tenantovi Azure AD B2C v tuto chvíli, ale ponechte otevřené okno prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="250f0-152">Nothing more can be configured in the Azure AD B2C tenant at this time, but leave the browser window open.</span></span> <span data-ttu-id="250f0-153">Po vytvoření aplikace ASP.NET Core je další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="250f0-153">There is more configuration after the ASP.NET Core app is created.</span></span>

## <a name="create-an-aspnet-core-app-in-visual-studio"></a><span data-ttu-id="250f0-154">Vytvoření aplikace ASP.NET Core v sadě Visual Studio</span><span class="sxs-lookup"><span data-stu-id="250f0-154">Create an ASP.NET Core app in Visual Studio</span></span>

<span data-ttu-id="250f0-155">Šablony Visual Studio webové aplikace můžete nakonfigurovat pro účely ověření tenanta Azure AD B2C.</span><span class="sxs-lookup"><span data-stu-id="250f0-155">The Visual Studio Web Application template can be configured to use the Azure AD B2C tenant for authentication.</span></span>

<span data-ttu-id="250f0-156">V sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="250f0-156">In Visual Studio:</span></span>

1. <span data-ttu-id="250f0-157">Vytvořte novou webovou aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="250f0-157">Create a new ASP.NET Core Web Application.</span></span> 
2. <span data-ttu-id="250f0-158">Vyberte **webovou aplikaci** ze seznamu šablon.</span><span class="sxs-lookup"><span data-stu-id="250f0-158">Select **Web Application** from the list of templates.</span></span>
3. <span data-ttu-id="250f0-159">Vyberte **změna ověřování** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="250f0-159">Select the **Change Authentication** button.</span></span>
    
    ![Tlačítko Změnit ověřování](./azure-ad-b2c/_static/changeauth.png)

4. <span data-ttu-id="250f0-161">V **změna ověřování** dialogového okna, vyberte **jednotlivé uživatelské účty**a pak vyberte **připojit k existujícímu úložišti uživatelů v cloudu** v rozevírací nabídce.</span><span class="sxs-lookup"><span data-stu-id="250f0-161">In the **Change Authentication** dialog, select **Individual User Accounts**, and then select **Connect to an existing user store in the cloud** in the dropdown.</span></span> 
    
    ![Dialogové okno Změnit ověřování](./azure-ad-b2c/_static/changeauthdialog.png)

5. <span data-ttu-id="250f0-163">Vyplňte formulář následujícími hodnotami:</span><span class="sxs-lookup"><span data-stu-id="250f0-163">Complete the form with the following values:</span></span>
    
    | <span data-ttu-id="250f0-164">Nastavení</span><span class="sxs-lookup"><span data-stu-id="250f0-164">Setting</span></span>                       | <span data-ttu-id="250f0-165">Hodnota</span><span class="sxs-lookup"><span data-stu-id="250f0-165">Value</span></span>                                                 |
    |-------------------------------|-------------------------------------------------------|
    | <span data-ttu-id="250f0-166">**Název domény**</span><span class="sxs-lookup"><span data-stu-id="250f0-166">**Domain Name**</span></span>               | <span data-ttu-id="250f0-167">*&lt;název domény tenanta B2C&gt;*</span><span class="sxs-lookup"><span data-stu-id="250f0-167">*&lt;the domain name of your B2C tenant&gt;*</span></span>          |
    | <span data-ttu-id="250f0-168">**ID aplikace**</span><span class="sxs-lookup"><span data-stu-id="250f0-168">**Application ID**</span></span>            | <span data-ttu-id="250f0-169">*&lt;Vložte ID aplikace ze schránky&gt;*</span><span class="sxs-lookup"><span data-stu-id="250f0-169">*&lt;paste the Application ID from the clipboard&gt;*</span></span> |
    | <span data-ttu-id="250f0-170">**Cestu zpětného volání**</span><span class="sxs-lookup"><span data-stu-id="250f0-170">**Callback Path**</span></span>             | <span data-ttu-id="250f0-171">*&lt;Použijte výchozí hodnotu&gt;*</span><span class="sxs-lookup"><span data-stu-id="250f0-171">*&lt;use the default value&gt;*</span></span>                       |
    | <span data-ttu-id="250f0-172">**Zásady registrace / přihlášení**</span><span class="sxs-lookup"><span data-stu-id="250f0-172">**Sign-up or sign-in policy**</span></span> | `B2C_1_SiUpIn`                                        |
    | <span data-ttu-id="250f0-173">**Zásady resetování hesla**</span><span class="sxs-lookup"><span data-stu-id="250f0-173">**Reset password policy**</span></span>     | `B2C_1_SSPR`                                          |
    | <span data-ttu-id="250f0-174">**Upravit profil zásad**</span><span class="sxs-lookup"><span data-stu-id="250f0-174">**Edit profile policy**</span></span>       | <span data-ttu-id="250f0-175">*&lt;Ponechte prázdné&gt;*</span><span class="sxs-lookup"><span data-stu-id="250f0-175">*&lt;leave blank&gt;*</span></span>                                 |
    
    <span data-ttu-id="250f0-176">Vyberte **kopírování** odkaz **identifikátor URI odpovědi** zkopírujte identifikátor URI odpovědi do schránky.</span><span class="sxs-lookup"><span data-stu-id="250f0-176">Select the **Copy** link next to **Reply URI** to copy the Reply URI to the clipboard.</span></span> <span data-ttu-id="250f0-177">Vyberte **OK** zavřete **změna ověřování** dialogového okna.</span><span class="sxs-lookup"><span data-stu-id="250f0-177">Select **OK** to close the **Change Authentication** dialog.</span></span> <span data-ttu-id="250f0-178">Vyberte **OK** k vytvoření webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="250f0-178">Select **OK** to create the web app.</span></span>

## <a name="finish-the-b2c-app-registration"></a><span data-ttu-id="250f0-179">Dokončit registraci aplikace B2C</span><span class="sxs-lookup"><span data-stu-id="250f0-179">Finish the B2C app registration</span></span>

<span data-ttu-id="250f0-180">Vraťte se do okna prohlížeče s vlastností aplikace B2C stále otevřen.</span><span class="sxs-lookup"><span data-stu-id="250f0-180">Return to the browser window with the B2C app properties still open.</span></span> <span data-ttu-id="250f0-181">Změnit dočasnou **adresy URL odpovědi** zadaný starší hodnota zkopírovat ze sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="250f0-181">Change the temporary **Reply URL** specified earlier to the value copied from Visual Studio.</span></span> <span data-ttu-id="250f0-182">Vyberte **Uložit** v horní části okna.</span><span class="sxs-lookup"><span data-stu-id="250f0-182">Select **Save** at the top of the window.</span></span>

> [!TIP]
> <span data-ttu-id="250f0-183">Pokud nezkopírovali příslušnou odpovědní adresu URL, použijte adresu HTTPS z karty ladění ve vlastnostech projektu webové a připojit **CallbackPath** hodnotu *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="250f0-183">If you didn't copy the Reply URL, use the HTTPS address from the Debug tab in the web project properties, and append the **CallbackPath** value from *appsettings.json*.</span></span>

## <a name="configure-policies"></a><span data-ttu-id="250f0-184">Konfigurace zásad</span><span class="sxs-lookup"><span data-stu-id="250f0-184">Configure policies</span></span>

<span data-ttu-id="250f0-185">Použijte postup v dokumentaci k Azure AD B2C do [vytvořit zásadu registrace nebo přihlašování](/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-sign-up-or-sign-in-policy)a potom [vytvořit zásady pro resetování hesla](/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-password-reset-policy).</span><span class="sxs-lookup"><span data-stu-id="250f0-185">Use the steps in the Azure AD B2C documentation to [create a sign-up or sign-in policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-sign-up-or-sign-in-policy), and then [create a password reset policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-password-reset-policy).</span></span> <span data-ttu-id="250f0-186">Použít ukázkové hodnoty uvedeny v dokumentaci pro **zprostředkovatelé Identity**, **atributy registrace**, a **deklarace identit aplikace**.</span><span class="sxs-lookup"><span data-stu-id="250f0-186">Use the example values provided in the documentation for **Identity providers**, **Sign-up attributes**, and **Application claims**.</span></span> <span data-ttu-id="250f0-187">Použití **spustit nyní** tlačítko a otestujte zásady, jak je popsáno v dokumentaci je volitelný.</span><span class="sxs-lookup"><span data-stu-id="250f0-187">Using the **Run now** button to test the policies as described in the documentation is optional.</span></span>

> [!WARNING]
> <span data-ttu-id="250f0-188">Zkontrolujte názvy zásad jsou přesně tak, jak popisuje dokumentace, jak tyto zásady, které byly používány v **změna ověřování** dialogového okna v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="250f0-188">Ensure the policy names are exactly as described in the documentation, as those policies were used in the **Change Authentication** dialog in Visual Studio.</span></span> <span data-ttu-id="250f0-189">Názvy zásad se dá ověřit v *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="250f0-189">The policy names can be verified in *appsettings.json*.</span></span>

## <a name="configure-the-underlying-openidconnectoptionsjwtbearercookie-options"></a><span data-ttu-id="250f0-190">Nakonfigurujte základní možnosti OpenIdConnectOptions/JwtBearer/souborů Cookie</span><span class="sxs-lookup"><span data-stu-id="250f0-190">Configure the underlying OpenIdConnectOptions/JwtBearer/Cookie options</span></span>

<span data-ttu-id="250f0-191">Základní možnosti konfigurace přímo, použijte konstantu odpovídající schéma v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="250f0-191">To configure the underlying options directly, use the appropriate scheme constant in `Startup.ConfigureServices`:</span></span>

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

## <a name="run-the-app"></a><span data-ttu-id="250f0-192">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="250f0-192">Run the app</span></span>

<span data-ttu-id="250f0-193">V sadě Visual Studio, stiskněte klávesu **F5** sestavíte a spustíte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="250f0-193">In Visual Studio, press **F5** to build and run the app.</span></span> <span data-ttu-id="250f0-194">Po spuštění webové aplikace, vyberte **přijmout** přijmout a použití souborů cookie (Pokud se zobrazí výzva) a pak vyberte **přihlášení**.</span><span class="sxs-lookup"><span data-stu-id="250f0-194">After the web app launches, select **Accept** to accept the use of cookies (if prompted), and then select **Sign in**.</span></span>

![Přihlaste se do aplikace](./azure-ad-b2c/_static/signin.png)

<span data-ttu-id="250f0-196">Prohlížeč přesměruje na tenanta Azure AD B2C.</span><span class="sxs-lookup"><span data-stu-id="250f0-196">The browser redirects to the Azure AD B2C tenant.</span></span> <span data-ttu-id="250f0-197">Přihlaste se pomocí existujícího účtu (Pokud se jeden vytvořil testování zásad) nebo vyberte **zaregistrujte se hned teď** vytvořte nový účet.</span><span class="sxs-lookup"><span data-stu-id="250f0-197">Sign in with an existing account (if one was created testing the policies) or select **Sign up now** to create a new account.</span></span> <span data-ttu-id="250f0-198">**Zapomněli jste heslo?** odkaz se používá k obnovení zapomenutého hesla.</span><span class="sxs-lookup"><span data-stu-id="250f0-198">The **Forgot your password?** link is used to reset a forgotten password.</span></span>

![Přihlášení k Azure AD B2C](./azure-ad-b2c/_static/b2csts.png)

<span data-ttu-id="250f0-200">Po úspěšném přihlášení, prohlížeč přesměruje do webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="250f0-200">After successfully signing in, the browser redirects to the web app.</span></span>

![Úspěch](./azure-ad-b2c/_static/success.png)

## <a name="next-steps"></a><span data-ttu-id="250f0-202">Další kroky</span><span class="sxs-lookup"><span data-stu-id="250f0-202">Next steps</span></span>

<span data-ttu-id="250f0-203">V tomto kurzu jste se naučili:</span><span class="sxs-lookup"><span data-stu-id="250f0-203">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="250f0-204">Vytvoření tenanta Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="250f0-204">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="250f0-205">Registrace aplikace v Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="250f0-205">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="250f0-206">Vytvoření webové aplikace ASP.NET Core umožňují použít tenanta Azure AD B2C k ověřování pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="250f0-206">Use Visual Studio to create an ASP.NET Core Web Application configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="250f0-207">Konfigurace zásad řízení chování tenanta Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="250f0-207">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

<span data-ttu-id="250f0-208">Teď, když aplikace ASP.NET Core je nakonfigurován na použití Azure AD B2C k ověřování, [Authorize atribut](xref:security/authorization/simple) umožňuje zabezpečit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="250f0-208">Now that the ASP.NET Core app is configured to use Azure AD B2C for authentication, the [Authorize attribute](xref:security/authorization/simple) can be used to secure your app.</span></span> <span data-ttu-id="250f0-209">Pokračujte ve vývoji vaší aplikace tím:</span><span class="sxs-lookup"><span data-stu-id="250f0-209">Continue developing your app by learning to:</span></span>

* <span data-ttu-id="250f0-210">[Přizpůsobení uživatelského rozhraní Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).</span><span class="sxs-lookup"><span data-stu-id="250f0-210">[Customize the Azure AD B2C user interface](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).</span></span>
* <span data-ttu-id="250f0-211">[Konfigurovat požadavky na složitost hesla](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).</span><span class="sxs-lookup"><span data-stu-id="250f0-211">[Configure password complexity requirements](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).</span></span>
* <span data-ttu-id="250f0-212">[Povolení služby Multi-Factor authentication](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).</span><span class="sxs-lookup"><span data-stu-id="250f0-212">[Enable multi-factor authentication](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).</span></span>
* <span data-ttu-id="250f0-213">Nakonfigurovat zprostředkovatelé identity další, jako jsou například [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [na Twitteru ](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app)a další.</span><span class="sxs-lookup"><span data-stu-id="250f0-213">Configure additional identity providers, such as [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app), and others.</span></span>
* <span data-ttu-id="250f0-214">[Použijte Azure AD Graph API](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) k získání dalších informací o uživatelích, jako je členství ve skupině z tenanta Azure AD B2C.</span><span class="sxs-lookup"><span data-stu-id="250f0-214">[Use the Azure AD Graph API](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) to retrieve additional user information, such as group membership, from the Azure AD B2C tenant.</span></span>
* <span data-ttu-id="250f0-215">[Zabezpečení webového rozhraní API pomocí Azure AD B2C v ASP.NET Core](xref:security/authentication/azure-ad-b2c-webapi).</span><span class="sxs-lookup"><span data-stu-id="250f0-215">[Secure an ASP.NET Core web API using Azure AD B2C](xref:security/authentication/azure-ad-b2c-webapi).</span></span>
* <span data-ttu-id="250f0-216">[Volání webového rozhraní API .NET z webové aplikace .NET pomocí Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet).</span><span class="sxs-lookup"><span data-stu-id="250f0-216">[Call a .NET web API from a .NET web app using Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet).</span></span>

---
title: Zabezpečení Blazor WebAssembly hostované aplikace v ASP.NET Core s využitím Azure Active Directory
author: guardrex
description: Naučte se zabezpečit aplikaci ASP.NET Core Blazor WebAssembly Hosted pomocí Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/08/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 12a2509998bb9b4d56e250518b2db91f73dd0e67
ms.sourcegitcommit: 9a90b956af8d8584d597f1e5c1dbfb0ea9bb8454
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2020
ms.locfileid: "88712412"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="4be62-103">Zabezpečení Blazor WebAssembly hostované aplikace v ASP.NET Core s využitím Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="4be62-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="4be62-104">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4be62-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="4be62-105">Tento článek popisuje, jak vytvořit [hostovanou Blazor WebAssembly aplikaci](xref:blazor/hosting-models#blazor-webassembly) , která pro ověřování používá [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) .</span><span class="sxs-lookup"><span data-stu-id="4be62-105">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="4be62-106">Registrace aplikací v AAD a vytvoření řešení</span><span class="sxs-lookup"><span data-stu-id="4be62-106">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="4be62-107">Vytvoření tenanta</span><span class="sxs-lookup"><span data-stu-id="4be62-107">Create a tenant</span></span>

<span data-ttu-id="4be62-108">Postupujte podle pokynů v [rychlém startu: nastavení tenanta](/azure/active-directory/develop/quickstart-create-new-tenant) pro vytvoření TENANTA v AAD.</span><span class="sxs-lookup"><span data-stu-id="4be62-108">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="4be62-109">Registrace aplikace API serveru</span><span class="sxs-lookup"><span data-stu-id="4be62-109">Register a server API app</span></span>

<span data-ttu-id="4be62-110">Postupujte podle pokynů v [rychlém startu: registrace aplikace pomocí platformy Microsoft Identity Platform](/azure/active-directory/develop/quickstart-register-app) a dalších témat služby Azure AAD k registraci aplikace AAD pro *aplikaci API serveru* a následnému provedení následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="4be62-110">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="4be62-111">V **Azure Active Directory**  >  **Registrace aplikací**vyberte možnost **Nová registrace**.</span><span class="sxs-lookup"><span data-stu-id="4be62-111">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="4be62-112">Zadejte **název** aplikace (například \*\* Blazor Server AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="4be62-112">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="4be62-113">Vyberte **podporované typy účtů**.</span><span class="sxs-lookup"><span data-stu-id="4be62-113">Choose a **Supported account types**.</span></span> <span data-ttu-id="4be62-114">Pro toto prostředí můžete vybrat **účty pouze v tomto organizačním adresáři** (jeden tenant).</span><span class="sxs-lookup"><span data-stu-id="4be62-114">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="4be62-115">*Aplikace API serveru* v tomto scénáři nevyžaduje **identifikátor URI přesměrování** , proto nechejte rozevírací seznam nastavený na **Web** a nezadávejte identifikátor URI přesměrování.</span><span class="sxs-lookup"><span data-stu-id="4be62-115">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="4be62-116">Zakažte **oprávnění**  >  **udělení souhlasu správce OpenID a offline_access oprávnění** .</span><span class="sxs-lookup"><span data-stu-id="4be62-116">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="4be62-117">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="4be62-117">Select **Register**.</span></span>

<span data-ttu-id="4be62-118">Zaznamenejte následující informace:</span><span class="sxs-lookup"><span data-stu-id="4be62-118">Record the following information:</span></span>

* <span data-ttu-id="4be62-119">*Aplikace API serveru* ID aplikace (klienta) (například `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="4be62-119">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="4be62-120">ID adresáře (tenanta) (například `e86c78e2-8bb4-4c41-aefd-918e0565a45e` )</span><span class="sxs-lookup"><span data-stu-id="4be62-120">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>
* <span data-ttu-id="4be62-121">Primární/Vydavatel nebo doména klienta služby AAD (například `contoso.onmicrosoft.com` ): doména je k dispozici jako **doména vydavatele** v okně **značky** Azure Portal pro registrovanou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4be62-121">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="4be62-122">V okně **oprávnění rozhraní API**odeberte **Microsoft Graph**  >  **uživatel. číst** , protože aplikace nevyžaduje přihlášení ani přístup k profilu uživatele.</span><span class="sxs-lookup"><span data-stu-id="4be62-122">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="4be62-123">Ve **vystavení rozhraní API**:</span><span class="sxs-lookup"><span data-stu-id="4be62-123">In **Expose an API**:</span></span>

1. <span data-ttu-id="4be62-124">Vyberte **Přidat obor**.</span><span class="sxs-lookup"><span data-stu-id="4be62-124">Select **Add a scope**.</span></span>
1. <span data-ttu-id="4be62-125">Vyberte **Uložit a pokračovat**.</span><span class="sxs-lookup"><span data-stu-id="4be62-125">Select **Save and continue**.</span></span>
1. <span data-ttu-id="4be62-126">Zadejte **název oboru** (například `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="4be62-126">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="4be62-127">Zadejte **Zobrazovaný název souhlasu správce** (například `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="4be62-127">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="4be62-128">Zadejte **Popis souhlasu správce** (například `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="4be62-128">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="4be62-129">Potvrďte, že je **stav** nastavený na **povoleno**.</span><span class="sxs-lookup"><span data-stu-id="4be62-129">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="4be62-130">Vyberte **Přidat obor**.</span><span class="sxs-lookup"><span data-stu-id="4be62-130">Select **Add scope**.</span></span>

<span data-ttu-id="4be62-131">Zaznamenejte následující informace:</span><span class="sxs-lookup"><span data-stu-id="4be62-131">Record the following information:</span></span>

* <span data-ttu-id="4be62-132">Identifikátor URI ID aplikace (například, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` , `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` nebo vlastní hodnota, kterou jste zadali)</span><span class="sxs-lookup"><span data-stu-id="4be62-132">App ID URI (for example, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provided)</span></span>
* <span data-ttu-id="4be62-133">Název oboru (například `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="4be62-133">Scope name (for example, `API.Access`)</span></span>

<span data-ttu-id="4be62-134">Identifikátor URI ID aplikace může vyžadovat zvláštní konfiguraci v klientské aplikaci, která je popsaná v části [obory přístupového tokenu](#access-token-scopes) dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="4be62-134">The App ID URI might require a special configuration in the client app, which is described in the [Access token scopes](#access-token-scopes) section later in this topic.</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="4be62-135">Registrace klientské aplikace</span><span class="sxs-lookup"><span data-stu-id="4be62-135">Register a client app</span></span>

<span data-ttu-id="4be62-136">Postupujte podle pokynů v [rychlém startu: registrace aplikace s využitím platformy Microsoft Identity Platform](/azure/active-directory/develop/quickstart-register-app) a dalších témat Azure AAD k registraci aplikace AAD pro *klientskou aplikaci* a následnému provedení následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="4be62-136">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="4be62-137">V **Azure Active Directory**  >  **Registrace aplikací**vyberte možnost **Nová registrace**.</span><span class="sxs-lookup"><span data-stu-id="4be62-137">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="4be62-138">Zadejte **název** aplikace (například \*\* Blazor AAD klienta\*\*).</span><span class="sxs-lookup"><span data-stu-id="4be62-138">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="4be62-139">Vyberte **podporované typy účtů**.</span><span class="sxs-lookup"><span data-stu-id="4be62-139">Choose a **Supported account types**.</span></span> <span data-ttu-id="4be62-140">Pro toto prostředí můžete vybrat **účty pouze v tomto organizačním adresáři** (jeden tenant).</span><span class="sxs-lookup"><span data-stu-id="4be62-140">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="4be62-141">Ponechte rozevírací seznam **URI přesměrování** nastavenou na **Web** a zadejte následující identifikátor URI pro přesměrování: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="4be62-141">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="4be62-142">Výchozí port pro aplikaci běžící na Kestrel je 5001.</span><span class="sxs-lookup"><span data-stu-id="4be62-142">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="4be62-143">Pokud je aplikace spuštěná na jiném Kestrel portu, použijte port aplikace.</span><span class="sxs-lookup"><span data-stu-id="4be62-143">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="4be62-144">Pro IIS Express se náhodně generovaný port pro aplikaci dá najít ve vlastnostech serverové aplikace na panelu **ladění** .</span><span class="sxs-lookup"><span data-stu-id="4be62-144">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="4be62-145">Vzhledem k tomu, že aplikace v tomto okamžiku neexistuje a port IIS Express není znám, vraťte se k tomuto kroku po vytvoření aplikace a aktualizaci identifikátoru URI přesměrování.</span><span class="sxs-lookup"><span data-stu-id="4be62-145">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="4be62-146">V části [Vytvoření aplikace](#create-the-app) se zobrazí zpráva s upozorněním, že IIS Express uživatelé chtějí aktualizovat identifikátor URI přesměrování.</span><span class="sxs-lookup"><span data-stu-id="4be62-146">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="4be62-147">Zakažte **oprávnění**  >  **udělení souhlasu správce OpenID a offline_access oprávnění** .</span><span class="sxs-lookup"><span data-stu-id="4be62-147">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="4be62-148">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="4be62-148">Select **Register**.</span></span>

<span data-ttu-id="4be62-149">Poznamenejte si ID klientské aplikace *klienta* (například `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="4be62-149">Record the *Client app* Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="4be62-150">Na webu konfigurace **ověřovacích**  >  **platforem**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="4be62-150">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="4be62-151">Ověřte, zda je **identifikátor URI přesměrování** k `https://localhost:{PORT}/authentication/login-callback` dispozici.</span><span class="sxs-lookup"><span data-stu-id="4be62-151">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="4be62-152">V případě **implicitního udělení**zaškrtněte políčka pro **přístupové tokeny** a **tokeny ID**.</span><span class="sxs-lookup"><span data-stu-id="4be62-152">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="4be62-153">Zbývající výchozí hodnoty pro aplikaci jsou pro toto prostředí přijatelné.</span><span class="sxs-lookup"><span data-stu-id="4be62-153">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="4be62-154">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="4be62-154">Select the **Save** button.</span></span>

<span data-ttu-id="4be62-155">V **oprávněních rozhraní API**:</span><span class="sxs-lookup"><span data-stu-id="4be62-155">In **API permissions**:</span></span>

1. <span data-ttu-id="4be62-156">Potvrďte, že aplikace má **Microsoft Graph**  >  oprávnění**uživatel. číst** .</span><span class="sxs-lookup"><span data-stu-id="4be62-156">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="4be62-157">Vyberte **Přidat oprávnění** a potom **Moje rozhraní API**.</span><span class="sxs-lookup"><span data-stu-id="4be62-157">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="4be62-158">Vyberte *aplikaci API serveru* ze sloupce **název** (například \*\* Blazor Server AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="4be62-158">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="4be62-159">Otevřete seznam **rozhraní API** .</span><span class="sxs-lookup"><span data-stu-id="4be62-159">Open the **API** list.</span></span>
1. <span data-ttu-id="4be62-160">Povolte přístup k rozhraní API (například `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="4be62-160">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="4be62-161">Vyberte **Přidat oprávnění**.</span><span class="sxs-lookup"><span data-stu-id="4be62-161">Select **Add permissions**.</span></span>
1. <span data-ttu-id="4be62-162">Klikněte na tlačítko **udělení souhlasu správce pro {Name}** .</span><span class="sxs-lookup"><span data-stu-id="4be62-162">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="4be62-163">Akci potvrďte výběrem **Ano**.</span><span class="sxs-lookup"><span data-stu-id="4be62-163">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="4be62-164">Vytvoření aplikace</span><span class="sxs-lookup"><span data-stu-id="4be62-164">Create the app</span></span>

<span data-ttu-id="4be62-165">V prázdné složce Nahraďte zástupné symboly v následujícím příkazu informacemi zaznamenanými dříve a spusťte příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="4be62-165">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="4be62-166">Zástupný symbol</span><span class="sxs-lookup"><span data-stu-id="4be62-166">Placeholder</span></span>                  | <span data-ttu-id="4be62-167">Název Azure Portal</span><span class="sxs-lookup"><span data-stu-id="4be62-167">Azure portal name</span></span>                                     | <span data-ttu-id="4be62-168">Příklad</span><span class="sxs-lookup"><span data-stu-id="4be62-168">Example</span></span>                                |
| ---------------------------- | ----------------------------------------------------- | -------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `BlazorSample`                         |
| `{CLIENT APP CLIENT ID}`     | <span data-ttu-id="4be62-169">ID aplikace (klienta) pro *klientskou aplikaci*</span><span class="sxs-lookup"><span data-stu-id="4be62-169">Application (client) ID for the *Client app*</span></span>          | `4369008b-21fa-427c-abaa-9b53bf58e538` |
| `{DEFAULT SCOPE}`            | <span data-ttu-id="4be62-170">Název oboru</span><span class="sxs-lookup"><span data-stu-id="4be62-170">Scope name</span></span>                                            | `API.Access`                           |
| `{SERVER API APP CLIENT ID}` | <span data-ttu-id="4be62-171">ID aplikace (klienta) pro *aplikaci Server API*</span><span class="sxs-lookup"><span data-stu-id="4be62-171">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SERVER API APP ID URI}`    | <span data-ttu-id="4be62-172">Identifikátor URI ID aplikace ([Viz Poznámka](#access-token-scopes))</span><span class="sxs-lookup"><span data-stu-id="4be62-172">Application ID URI ([see note](#access-token-scopes))</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT DOMAIN}`            | <span data-ttu-id="4be62-173">Primární/Vydavatel/doména tenanta</span><span class="sxs-lookup"><span data-stu-id="4be62-173">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`              |
| `{TENANT ID}`                | <span data-ttu-id="4be62-174">ID adresáře (tenanta)</span><span class="sxs-lookup"><span data-stu-id="4be62-174">Directory (tenant) ID</span></span>                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

<span data-ttu-id="4be62-175">Umístění výstupu zadané s `-o|--output` možností vytvoří složku projektu, pokud neexistuje a bude součástí názvu aplikace.</span><span class="sxs-lookup"><span data-stu-id="4be62-175">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="4be62-176">Předejte tomuto parametru identifikátor URI ID aplikace `app-id-uri` , ale Všimněte si, že se v klientské aplikaci může vyžadovat Změna konfigurace, která je popsaná v části [obory přístupového tokenu](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="4be62-176">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

> [!NOTE]
> <span data-ttu-id="4be62-177">V Azure Portal **Authentication** *Client app's*  >  **Platform configurations**  >  **Web**  >  pro aplikace, které běží na serveru Kestrel s výchozími nastaveními, je 5001 nakonfigurován**identifikátor URI** webu klientské aplikace konfigurace platformy ověřování.</span><span class="sxs-lookup"><span data-stu-id="4be62-177">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="4be62-178">Pokud *klientská aplikace* běží na náhodném IIS Express portu, můžete port pro aplikaci najít ve vlastnostech *aplikace API serveru* na panelu **ladění** .</span><span class="sxs-lookup"><span data-stu-id="4be62-178">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="4be62-179">Pokud port nebyl dříve nakonfigurovaný se známým portem *klientské aplikace* , vraťte se k registraci *klientské aplikace* v Azure Portal a aktualizujte identifikátor URI přesměrování pomocí správného portu.</span><span class="sxs-lookup"><span data-stu-id="4be62-179">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="4be62-180">Konfigurace aplikace serveru</span><span class="sxs-lookup"><span data-stu-id="4be62-180">Server app configuration</span></span>

<span data-ttu-id="4be62-181">*Tato část se týká **`Server`** aplikace řešení.*</span><span class="sxs-lookup"><span data-stu-id="4be62-181">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="4be62-182">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="4be62-182">Authentication package</span></span>

<span data-ttu-id="4be62-183">Podpora ověřování a autorizace volání ASP.NET Core webových rozhraní API je zajištěna [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) balíčkem:</span><span class="sxs-lookup"><span data-stu-id="4be62-183">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="4be62-184">Pro zástupný text je `{VERSION}` nejnovější stabilní verze balíčku, která odpovídá verzi sdílené architektury aplikace, nalezena v **historii verzí** balíčku na adrese [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span><span class="sxs-lookup"><span data-stu-id="4be62-184">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="4be62-185">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="4be62-185">Authentication service support</span></span>

<span data-ttu-id="4be62-186">`AddAuthentication`Metoda nastaví služby ověřování v rámci aplikace a nakonfiguruje obslužnou rutinu JWT nosiče jako výchozí metodu ověřování.</span><span class="sxs-lookup"><span data-stu-id="4be62-186">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="4be62-187"><xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A>Metoda nastavuje konkrétní parametry v obslužné rutině JWT nosiče vyžadované k ověření tokenů vygenerovaných Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="4be62-187">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="4be62-188"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> a <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> Ujistěte se, že:</span><span class="sxs-lookup"><span data-stu-id="4be62-188"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="4be62-189">Aplikace se pokusí analyzovat a ověřit tokeny příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="4be62-189">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="4be62-190">Všechny žádosti o přístup k chráněnému prostředku bez správných přihlašovacích údajů selžou.</span><span class="sxs-lookup"><span data-stu-id="4be62-190">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="4be62-191">Uživatel. Identity . Jméno</span><span class="sxs-lookup"><span data-stu-id="4be62-191">User.Identity.Name</span></span>

<span data-ttu-id="4be62-192">Ve výchozím nastavení rozhraní API serverové aplikace naplní `User.Identity.Name` hodnotu z `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` typu deklarace (například `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` ).</span><span class="sxs-lookup"><span data-stu-id="4be62-192">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="4be62-193">Pokud chcete aplikaci nakonfigurovat tak, aby přijímala hodnotu z `name` typu deklarace, nakonfigurujte <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="4be62-193">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="4be62-194">Nastavení aplikace</span><span class="sxs-lookup"><span data-stu-id="4be62-194">App settings</span></span>

<span data-ttu-id="4be62-195">`appsettings.json`Soubor obsahuje možnosti pro konfiguraci obslužné rutiny nosiče JWT používané k ověření přístupových tokenů:</span><span class="sxs-lookup"><span data-stu-id="4be62-195">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

<span data-ttu-id="4be62-196">Příklad:</span><span class="sxs-lookup"><span data-stu-id="4be62-196">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="4be62-197">Kontroler WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="4be62-197">WeatherForecast controller</span></span>

<span data-ttu-id="4be62-198">Řadič WeatherForecast (*Controllers/WeatherForecastController. cs*) zpřístupňuje chráněné rozhraní API s [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributem použitým pro kontroler.</span><span class="sxs-lookup"><span data-stu-id="4be62-198">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="4be62-199">Je **důležité** si uvědomit, že:</span><span class="sxs-lookup"><span data-stu-id="4be62-199">It's **important** to understand that:</span></span>

* <span data-ttu-id="4be62-200">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Atribut v tomto řadiči rozhraní API je jediná věc, která chrání toto rozhraní API před neoprávněným přístupem.</span><span class="sxs-lookup"><span data-stu-id="4be62-200">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="4be62-201">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Atribut použitý v Blazor WebAssembly aplikaci slouží pouze jako pomocný parametr aplikace, který by měl být uživatel autorizován, aby mohla aplikace správně fungovat.</span><span class="sxs-lookup"><span data-stu-id="4be62-201">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="4be62-202">Konfigurace klientské aplikace</span><span class="sxs-lookup"><span data-stu-id="4be62-202">Client app configuration</span></span>

<span data-ttu-id="4be62-203">*Tato část se týká **`Client`** aplikace řešení.*</span><span class="sxs-lookup"><span data-stu-id="4be62-203">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="4be62-204">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="4be62-204">Authentication package</span></span>

<span data-ttu-id="4be62-205">Když je aplikace vytvořená tak, aby používala pracovní nebo školní účty ( `SingleOrg` ), aplikace automaticky obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ).</span><span class="sxs-lookup"><span data-stu-id="4be62-205">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="4be62-206">Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="4be62-206">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="4be62-207">Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:</span><span class="sxs-lookup"><span data-stu-id="4be62-207">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="4be62-208">Pro zástupný text je `{VERSION}` nejnovější stabilní verze balíčku, která odpovídá verzi sdílené architektury aplikace, nalezena v **historii verzí** balíčku na adrese [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="4be62-208">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="4be62-209">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)Balíček [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) do této aplikace přidá balíček.</span><span class="sxs-lookup"><span data-stu-id="4be62-209">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="4be62-210">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="4be62-210">Authentication service support</span></span>

<span data-ttu-id="4be62-211">Přidávají <xref:System.Net.Http.HttpClient> se podpory pro instance, které zahrnují přístupové tokeny při vytváření žádostí na serverový projekt.</span><span class="sxs-lookup"><span data-stu-id="4be62-211">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="4be62-212">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="4be62-212">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="4be62-213">Zástupný symbol `{APP ASSEMBLY}` je název sestavení aplikace (například `BlazorSample.ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="4be62-213">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="4be62-214">Podpora ověřování uživatelů je registrovaná v kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metodou rozšíření poskytovanou [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) balíčkem.</span><span class="sxs-lookup"><span data-stu-id="4be62-214">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="4be62-215">Tato metoda nastavuje služby, které aplikace potřebuje k interakci se Identity zprostředkovatelem (IP).</span><span class="sxs-lookup"><span data-stu-id="4be62-215">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="4be62-216">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="4be62-216">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="4be62-217"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Metoda přijímá zpětné volání ke konfiguraci parametrů požadovaných k ověření aplikace.</span><span class="sxs-lookup"><span data-stu-id="4be62-217">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="4be62-218">Hodnoty požadované pro konfiguraci aplikace lze získat z konfigurace AAD webu Azure Portal při registraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="4be62-218">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="4be62-219">Soubor zadal konfiguraci `wwwroot/appsettings.json` :</span><span class="sxs-lookup"><span data-stu-id="4be62-219">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="4be62-220">Příklad:</span><span class="sxs-lookup"><span data-stu-id="4be62-220">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="4be62-221">Obory přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="4be62-221">Access token scopes</span></span>

<span data-ttu-id="4be62-222">Výchozí obory přístupového tokenu představují seznam oborů přístupového tokenu, které jsou:</span><span class="sxs-lookup"><span data-stu-id="4be62-222">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="4be62-223">Ve výchozím nastavení zahrnuty v žádosti o přihlášení.</span><span class="sxs-lookup"><span data-stu-id="4be62-223">Included by default in the sign in request.</span></span>
* <span data-ttu-id="4be62-224">Slouží ke zřízení přístupového tokenu hned po ověření.</span><span class="sxs-lookup"><span data-stu-id="4be62-224">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="4be62-225">Všechny obory musí patřit do stejné aplikace na pravidla Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="4be62-225">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="4be62-226">Další obory je možné přidat pro další aplikace API podle potřeby:</span><span class="sxs-lookup"><span data-stu-id="4be62-226">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="4be62-227">Další informace najdete v následujících částech článku o *dalších scénářích* :</span><span class="sxs-lookup"><span data-stu-id="4be62-227">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="4be62-228">Vyžádání dalších přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="4be62-228">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="4be62-229">Připojit tokeny k odchozím žádostem</span><span class="sxs-lookup"><span data-stu-id="4be62-229">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="4be62-230">Režim přihlášení</span><span class="sxs-lookup"><span data-stu-id="4be62-230">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="4be62-231">Importovat soubor</span><span class="sxs-lookup"><span data-stu-id="4be62-231">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="4be62-232">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="4be62-232">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="4be62-233">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="4be62-233">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="4be62-234">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="4be62-234">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="4be62-235">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="4be62-235">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="4be62-236">Součást ověřování</span><span class="sxs-lookup"><span data-stu-id="4be62-236">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="4be62-237">Komponenta FetchData</span><span class="sxs-lookup"><span data-stu-id="4be62-237">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="4be62-238">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="4be62-238">Run the app</span></span>

<span data-ttu-id="4be62-239">Spusťte aplikaci z projektu serveru.</span><span class="sxs-lookup"><span data-stu-id="4be62-239">Run the app from the Server project.</span></span> <span data-ttu-id="4be62-240">Při použití sady Visual Studio buď:</span><span class="sxs-lookup"><span data-stu-id="4be62-240">When using Visual Studio, either:</span></span>

* <span data-ttu-id="4be62-241">Nastavte rozevírací seznam **projekty po spuštění** na panelu nástrojů na *aplikaci API serveru* a vyberte tlačítko **Spustit** .</span><span class="sxs-lookup"><span data-stu-id="4be62-241">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="4be62-242">Vyberte projekt serveru v **Průzkumník řešení** a na panelu nástrojů vyberte tlačítko **Spustit** nebo spusťte aplikaci z nabídky **ladění** .</span><span class="sxs-lookup"><span data-stu-id="4be62-242">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="4be62-243">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4be62-243">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="4be62-244">Neověřené nebo neautorizované požadavky webového rozhraní API v aplikaci s zabezpečeným výchozím klientem</span><span class="sxs-lookup"><span data-stu-id="4be62-244">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="4be62-245">Dokumentace k platformě Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="4be62-245">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)

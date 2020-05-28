---
<span data-ttu-id="b2d9b-101">title: ' zabezpečení Blazor hostované aplikace ASP.NET Core WebAssembly pomocí Azure Active Directory B2C ' Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="b2d9b-101">title: 'Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b2d9b-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b2d9b-102">'Blazor'</span></span>
- <span data-ttu-id="b2d9b-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b2d9b-103">'Identity'</span></span>
- <span data-ttu-id="b2d9b-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b2d9b-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="b2d9b-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b2d9b-105">'Razor'</span></span>
- <span data-ttu-id="b2d9b-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="b2d9b-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="b2d9b-107">Zabezpečení Blazor hostované aplikace ASP.NET Core WebAssembly pomocí Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="b2d9b-107">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="b2d9b-108">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b2d9b-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b2d9b-109">Tento článek popisuje, jak vytvořit Blazor samostatnou aplikaci WebAssembly, která pro ověřování používá [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) .</span><span class="sxs-lookup"><span data-stu-id="b2d9b-109">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="b2d9b-110">Registrace aplikací v AAD B2C a vytvoření řešení</span><span class="sxs-lookup"><span data-stu-id="b2d9b-110">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="b2d9b-111">Vytvoření tenanta</span><span class="sxs-lookup"><span data-stu-id="b2d9b-111">Create a tenant</span></span>

<span data-ttu-id="b2d9b-112">Postupujte podle pokynů v [kurzu: vytvoření tenanta Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) pro vytvoření tenanta AAD B2C.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-112">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span>

<span data-ttu-id="b2d9b-113">Zaznamenejte následující informace:</span><span class="sxs-lookup"><span data-stu-id="b2d9b-113">Record the following information:</span></span>

* <span data-ttu-id="b2d9b-114">Instance AAD B2C (například `https://contoso.b2clogin.com/` , která zahrnuje koncové lomítko)</span><span class="sxs-lookup"><span data-stu-id="b2d9b-114">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="b2d9b-115">AAD B2C domény klienta (například `contoso.onmicrosoft.com` )</span><span class="sxs-lookup"><span data-stu-id="b2d9b-115">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="b2d9b-116">Registrace aplikace API serveru</span><span class="sxs-lookup"><span data-stu-id="b2d9b-116">Register a server API app</span></span>

<span data-ttu-id="b2d9b-117">Postupujte podle pokynů v [kurzu: Zaregistrujte aplikaci v Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) k registraci aplikace AAD pro *aplikaci API serveru*:</span><span class="sxs-lookup"><span data-stu-id="b2d9b-117">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app*:</span></span>

1. <span data-ttu-id="b2d9b-118">V **Azure Active Directory**  >  **Registrace aplikací**vyberte možnost **Nová registrace**.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-118">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="b2d9b-119">Zadejte **název** aplikace (například \*\* Blazor AAD B2C serveru\*\*).</span><span class="sxs-lookup"><span data-stu-id="b2d9b-119">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="b2d9b-120">U **podporovaných typů účtů**vyberte možnost více tenantů: **účty v libovolném organizačním adresáři nebo jakémkoli poskytovateli identity. Pro ověřování uživatelů pomocí Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="b2d9b-120">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="b2d9b-121">*Aplikace API serveru* v tomto scénáři nevyžaduje **identifikátor URI přesměrování** , proto nechejte rozevírací seznam nastavený na **Web** a nezadávejte identifikátor URI přesměrování.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-121">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="b2d9b-122">Potvrďte, že **oprávnění**  >  **udělují správcům oprávnění k OpenID a offline_access** jsou povolená.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-122">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="b2d9b-123">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-123">Select **Register**.</span></span>

<span data-ttu-id="b2d9b-124">Zaznamenejte následující informace:</span><span class="sxs-lookup"><span data-stu-id="b2d9b-124">Record the following information:</span></span>

* <span data-ttu-id="b2d9b-125">*Aplikace API serveru* ID aplikace (ID klienta) (například `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="b2d9b-125">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="b2d9b-126">ID adresáře (ID klienta) (například `222222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="b2d9b-126">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="b2d9b-127">Doména tenanta AAD (například `contoso.onmicrosoft.com` ): doména je k dispozici jako **doména vydavatele** v okně **značky** Azure Portal pro registrovanou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-127">AAD Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="b2d9b-128">Ve **vystavení rozhraní API**:</span><span class="sxs-lookup"><span data-stu-id="b2d9b-128">In **Expose an API**:</span></span>

1. <span data-ttu-id="b2d9b-129">Vyberte **Přidat obor**.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-129">Select **Add a scope**.</span></span>
1. <span data-ttu-id="b2d9b-130">Vyberte **Uložit a pokračovat**.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-130">Select **Save and continue**.</span></span>
1. <span data-ttu-id="b2d9b-131">Zadejte **název oboru** (například `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="b2d9b-131">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="b2d9b-132">Zadejte **Zobrazovaný název souhlasu správce** (například `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="b2d9b-132">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="b2d9b-133">Zadejte **Popis souhlasu správce** (například `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="b2d9b-133">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="b2d9b-134">Potvrďte, že je **stav** nastavený na **povoleno**.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-134">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="b2d9b-135">Vyberte **Přidat obor**.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-135">Select **Add scope**.</span></span>

<span data-ttu-id="b2d9b-136">Zaznamenejte následující informace:</span><span class="sxs-lookup"><span data-stu-id="b2d9b-136">Record the following information:</span></span>

* <span data-ttu-id="b2d9b-137">Identifikátor URI ID aplikace (například, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` , `api://11111111-1111-1111-1111-111111111111` nebo vlastní hodnota, kterou jste zadali)</span><span class="sxs-lookup"><span data-stu-id="b2d9b-137">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="b2d9b-138">Výchozí obor (například `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="b2d9b-138">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="b2d9b-139">Registrace klientské aplikace</span><span class="sxs-lookup"><span data-stu-id="b2d9b-139">Register a client app</span></span>

<span data-ttu-id="b2d9b-140">Postupujte podle pokynů v [kurzu: registrace aplikace v Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) znovu k registraci aplikace AAD pro *klientskou aplikaci*:</span><span class="sxs-lookup"><span data-stu-id="b2d9b-140">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app*:</span></span>

1. <span data-ttu-id="b2d9b-141">V **Azure Active Directory**  >  **Registrace aplikací**vyberte možnost **Nová registrace**.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-141">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="b2d9b-142">Zadejte **název** aplikace (například \*\* Blazor AAD B2C klienta\*\*).</span><span class="sxs-lookup"><span data-stu-id="b2d9b-142">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="b2d9b-143">U **podporovaných typů účtů**vyberte možnost více tenantů: **účty v libovolném organizačním adresáři nebo jakémkoli poskytovateli identity. Pro ověřování uživatelů pomocí Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="b2d9b-143">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="b2d9b-144">Ponechte rozevírací seznam **URI přesměrování** nastavenou na **Web** a zadejte následující identifikátor URI pro přesměrování: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="b2d9b-144">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="b2d9b-145">Výchozí port pro aplikaci běžící na Kestrel je 5001.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-145">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="b2d9b-146">Pokud je aplikace spuštěná na jiném Kestrel portu, použijte port aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-146">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="b2d9b-147">Pro IIS Express se náhodně generovaný port pro aplikaci dá najít ve vlastnostech serverové aplikace na panelu **ladění** .</span><span class="sxs-lookup"><span data-stu-id="b2d9b-147">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="b2d9b-148">Vzhledem k tomu, že aplikace v tomto okamžiku neexistuje a port IIS Express není znám, vraťte se k tomuto kroku po vytvoření aplikace a aktualizaci identifikátoru URI přesměrování.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-148">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="b2d9b-149">V části [Vytvoření aplikace](#create-the-app) se zobrazí zpráva s upozorněním, že IIS Express uživatelé chtějí aktualizovat identifikátor URI přesměrování.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-149">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="b2d9b-150">Potvrďte, že **oprávnění**  >  **udělují správcům oprávnění k OpenID a offline_access** jsou povolená.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-150">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="b2d9b-151">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-151">Select **Register**.</span></span>

<span data-ttu-id="b2d9b-152">Poznamenejte si ID aplikace (ID klienta) (například `11111111-1111-1111-1111-111111111111` ).</span><span class="sxs-lookup"><span data-stu-id="b2d9b-152">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="b2d9b-153">Na webu konfigurace **ověřovacích**  >  **platforem**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="b2d9b-153">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="b2d9b-154">Ověřte, zda je **identifikátor URI přesměrování** k `https://localhost:{PORT}/authentication/login-callback` dispozici.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-154">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="b2d9b-155">V případě **implicitního udělení**zaškrtněte políčka pro **přístupové tokeny** a **tokeny ID**.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-155">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="b2d9b-156">Zbývající výchozí hodnoty pro aplikaci jsou pro toto prostředí přijatelné.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-156">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="b2d9b-157">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-157">Select the **Save** button.</span></span>

<span data-ttu-id="b2d9b-158">V **oprávněních rozhraní API**:</span><span class="sxs-lookup"><span data-stu-id="b2d9b-158">In **API permissions**:</span></span>

1. <span data-ttu-id="b2d9b-159">Vyberte **Přidat oprávnění** a potom **Moje rozhraní API**.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-159">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="b2d9b-160">Ve sloupci **název** vyberte *aplikace API serveru* (například \*\* Blazor AAD B2C serveru\*\*).</span><span class="sxs-lookup"><span data-stu-id="b2d9b-160">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="b2d9b-161">Otevřete seznam **rozhraní API** .</span><span class="sxs-lookup"><span data-stu-id="b2d9b-161">Open the **API** list.</span></span>
1. <span data-ttu-id="b2d9b-162">Povolte přístup k rozhraní API (například `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="b2d9b-162">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="b2d9b-163">Vyberte **Přidat oprávnění**.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-163">Select **Add permissions**.</span></span>
1. <span data-ttu-id="b2d9b-164">Vyberte tlačítko **pro udělení obsahu správce pro {TENANT}** .</span><span class="sxs-lookup"><span data-stu-id="b2d9b-164">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="b2d9b-165">Výběrem **Ano** potvrďte.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-165">Select **Yes** to confirm.</span></span>

<span data-ttu-id="b2d9b-166">V **Home**  >  **Azure AD B2C**  >  **toky pro uživatele**doma:</span><span class="sxs-lookup"><span data-stu-id="b2d9b-166">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="b2d9b-167">Vytvoření uživatelského toku pro registraci a přihlašování</span><span class="sxs-lookup"><span data-stu-id="b2d9b-167">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="b2d9b-168">Aby bylo **Application claims**  >  **Display Name** možné naplnit `context.User.Identity.Name` `LoginDisplay` součást (*Shared/LoginDisplay. Razor*), vyberte alespoň atribut uživatele zobrazovaný název deklarací identity aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-168">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="b2d9b-169">Zaznamenejte si název uživatelského toku pro registraci a přihlašování vytvořený pro aplikaci (například `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="b2d9b-169">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="b2d9b-170">Vytvoření aplikace</span><span class="sxs-lookup"><span data-stu-id="b2d9b-170">Create the app</span></span>

<span data-ttu-id="b2d9b-171">Zástupné symboly v následujícím příkazu nahraďte dříve zaznamenanými informacemi a spusťte příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="b2d9b-171">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="b2d9b-172">Chcete-li určit umístění výstupu, které vytvoří složku projektu, pokud neexistuje, zahrňte možnost výstup do příkazu s cestou (například `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="b2d9b-172">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="b2d9b-173">Název složky se také stal součástí názvu projektu.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-173">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="b2d9b-174">Předejte tomuto parametru identifikátor URI ID aplikace `app-id-uri` , ale Všimněte si, že se v klientské aplikaci může vyžadovat Změna konfigurace, která je popsaná v části [obory přístupového tokenu](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="b2d9b-174">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>
>
> <span data-ttu-id="b2d9b-175">Kromě toho obor nastavený podle hostované Blazor šablony může mít opakovaný hostitel identifikátoru URI ID aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-175">Additionally, the scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="b2d9b-176">Ověřte, že je obor nakonfigurovaný pro `DefaultAccessTokenScopes` kolekci v `Program.Main` (*program.cs*) *klientské aplikace*správný.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-176">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (*Program.cs*) of the *Client app*.</span></span>

> [!NOTE]
> <span data-ttu-id="b2d9b-177">V Azure Portal **Authentication** *Client app's*  >  **Platform configurations**  >  **Web**  >  pro aplikace, které běží na serveru Kestrel s výchozími nastaveními, je 5001 nakonfigurován**identifikátor URI** webu klientské aplikace konfigurace platformy ověřování.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-177">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="b2d9b-178">Pokud *klientská aplikace* běží na náhodném IIS Express portu, port pro aplikaci najdete ve vlastnostech *serverové aplikace* na panelu **ladění** .</span><span class="sxs-lookup"><span data-stu-id="b2d9b-178">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="b2d9b-179">Pokud port nebyl dříve nakonfigurovaný se známým portem *klientské aplikace* , vraťte se k registraci *klientské aplikace* v Azure Portal a aktualizujte identifikátor URI přesměrování pomocí správného portu.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-179">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="b2d9b-180">Konfigurace aplikace serveru</span><span class="sxs-lookup"><span data-stu-id="b2d9b-180">Server app configuration</span></span>

<span data-ttu-id="b2d9b-181">*Tato část se vztahuje k **serverové** aplikaci řešení.*</span><span class="sxs-lookup"><span data-stu-id="b2d9b-181">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="b2d9b-182">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="b2d9b-182">Authentication package</span></span>

<span data-ttu-id="b2d9b-183">Podpora ověřování a autorizace volání ASP.NET Core webových rozhraní API je poskytována balíčkem [Microsoft. AspNetCore. Authentication. AzureADB2C. UI](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI/) :</span><span class="sxs-lookup"><span data-stu-id="b2d9b-183">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [Microsoft.AspNetCore.Authentication.AzureADB2C.UI](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI/) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="b2d9b-184">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="b2d9b-184">Authentication service support</span></span>

<span data-ttu-id="b2d9b-185">`AddAuthentication`Metoda nastaví služby ověřování v rámci aplikace a nakonfiguruje obslužnou rutinu JWT nosiče jako výchozí metodu ověřování.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-185">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="b2d9b-186"><xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A>Metoda nastavuje konkrétní parametry v obslužné rutině JWT nosiče vyžadované k ověření tokenů vygenerovaných Azure Active Directory B2C:</span><span class="sxs-lookup"><span data-stu-id="b2d9b-186">The <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="b2d9b-187"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>a <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> Ujistěte se, že:</span><span class="sxs-lookup"><span data-stu-id="b2d9b-187"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="b2d9b-188">Aplikace se pokusí analyzovat a ověřit tokeny příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-188">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="b2d9b-189">Všechny žádosti o přístup k chráněnému prostředku bez správných přihlašovacích údajů selžou.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-189">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="b2d9b-190">Uživatel. Identity . Jméno</span><span class="sxs-lookup"><span data-stu-id="b2d9b-190">User.Identity.Name</span></span>

<span data-ttu-id="b2d9b-191">Ve výchozím nastavení `User.Identity.Name` není naplněno.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-191">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="b2d9b-192">Pokud chcete aplikaci nakonfigurovat tak, aby přijímala hodnotu z `name` typu deklarace, nakonfigurujte [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b2d9b-192">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="b2d9b-193">Nastavení aplikace</span><span class="sxs-lookup"><span data-stu-id="b2d9b-193">App settings</span></span>

<span data-ttu-id="b2d9b-194">Soubor *appSettings. JSON* obsahuje možnosti konfigurace obslužné rutiny nosiče JWT používané k ověření přístupových tokenů.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-194">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://{TENANT}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{TENANT DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

<span data-ttu-id="b2d9b-195">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b2d9b-195">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="b2d9b-196">Kontroler WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="b2d9b-196">WeatherForecast controller</span></span>

<span data-ttu-id="b2d9b-197">Řadič WeatherForecast (*Controllers/WeatherForecastController. cs*) zpřístupňuje chráněné rozhraní API s [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributem použitým pro kontroler.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-197">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="b2d9b-198">Je **důležité** si uvědomit, že:</span><span class="sxs-lookup"><span data-stu-id="b2d9b-198">It's **important** to understand that:</span></span>

* <span data-ttu-id="b2d9b-199">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Atribut v tomto řadiči rozhraní API je jediná věc, která chrání toto rozhraní API před neoprávněným přístupem.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-199">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="b2d9b-200">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Atribut použitý v Blazor aplikaci WebAssembly slouží pouze jako pomocný parametr aplikace, který by měl být uživatelem autorizován, aby mohla aplikace správně fungovat.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-200">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="b2d9b-201">Konfigurace klientské aplikace</span><span class="sxs-lookup"><span data-stu-id="b2d9b-201">Client app configuration</span></span>

<span data-ttu-id="b2d9b-202">*Tato část se vztahuje k **klientské** aplikaci řešení.*</span><span class="sxs-lookup"><span data-stu-id="b2d9b-202">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="b2d9b-203">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="b2d9b-203">Authentication package</span></span>

<span data-ttu-id="b2d9b-204">Když je aplikace vytvořená tak, aby používala individuální účet B2C ( `IndividualB2C` ), aplikace automaticky obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft. Authentication. WebAssembly. Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="b2d9b-204">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="b2d9b-205">Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-205">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="b2d9b-206">Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:</span><span class="sxs-lookup"><span data-stu-id="b2d9b-206">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="b2d9b-207">Balíček [Microsoft. Authentication. WebAssembly. Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) po přechodu do aplikace přidá balíček [Microsoft. AspNetCore. Components. WebAssembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) .</span><span class="sxs-lookup"><span data-stu-id="b2d9b-207">The [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="b2d9b-208">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="b2d9b-208">Authentication service support</span></span>

<span data-ttu-id="b2d9b-209">Přidávají <xref:System.Net.Http.HttpClient> se podpory pro instance, které zahrnují přístupové tokeny při vytváření žádostí na serverový projekt.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-209">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="b2d9b-210">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b2d9b-210">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="b2d9b-211">Podpora ověřování uživatelů je zaregistrovaná v kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metodou rozšíření poskytovanou balíčkem [Microsoft. Authentication. WebAssembly. Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) .</span><span class="sxs-lookup"><span data-stu-id="b2d9b-211">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="b2d9b-212">Tato metoda nastavuje služby, které aplikace potřebuje k interakci se Identity zprostředkovatelem (IP).</span><span class="sxs-lookup"><span data-stu-id="b2d9b-212">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="b2d9b-213">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b2d9b-213">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="b2d9b-214"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Metoda přijímá zpětné volání ke konfiguraci parametrů požadovaných k ověření aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-214">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="b2d9b-215">Hodnoty požadované pro konfiguraci aplikace lze získat z konfigurace AAD webu Azure Portal při registraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-215">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="b2d9b-216">Konfigurace je dodána souborem *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="b2d9b-216">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="b2d9b-217">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b2d9b-217">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="b2d9b-218">Obory přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="b2d9b-218">Access token scopes</span></span>

<span data-ttu-id="b2d9b-219">Výchozí obory přístupového tokenu představují seznam oborů přístupového tokenu, které jsou:</span><span class="sxs-lookup"><span data-stu-id="b2d9b-219">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="b2d9b-220">Ve výchozím nastavení zahrnuty v žádosti o přihlášení.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-220">Included by default in the sign in request.</span></span>
* <span data-ttu-id="b2d9b-221">Slouží ke zřízení přístupového tokenu hned po ověření.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-221">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="b2d9b-222">Všechny obory musí patřit do stejné aplikace na pravidla Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-222">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="b2d9b-223">Další obory je možné přidat pro další aplikace API podle potřeby:</span><span class="sxs-lookup"><span data-stu-id="b2d9b-223">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="b2d9b-224">Další informace najdete v následujících částech článku o *dalších scénářích* :</span><span class="sxs-lookup"><span data-stu-id="b2d9b-224">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="b2d9b-225">Vyžádání dalších přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="b2d9b-225">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="b2d9b-226">Připojit tokeny k odchozím žádostem</span><span class="sxs-lookup"><span data-stu-id="b2d9b-226">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="b2d9b-227">Importovat soubor</span><span class="sxs-lookup"><span data-stu-id="b2d9b-227">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="b2d9b-228">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="b2d9b-228">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="b2d9b-229">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="b2d9b-229">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="b2d9b-230">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="b2d9b-230">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="b2d9b-231">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="b2d9b-231">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="b2d9b-232">Součást ověřování</span><span class="sxs-lookup"><span data-stu-id="b2d9b-232">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="b2d9b-233">Komponenta FetchData</span><span class="sxs-lookup"><span data-stu-id="b2d9b-233">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="b2d9b-234">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="b2d9b-234">Run the app</span></span>

<span data-ttu-id="b2d9b-235">Spusťte aplikaci z projektu serveru.</span><span class="sxs-lookup"><span data-stu-id="b2d9b-235">Run the app from the Server project.</span></span> <span data-ttu-id="b2d9b-236">Při použití sady Visual Studio buď:</span><span class="sxs-lookup"><span data-stu-id="b2d9b-236">When using Visual Studio, either:</span></span>

* <span data-ttu-id="b2d9b-237">Nastavte rozevírací seznam **projekty po spuštění** na panelu nástrojů na *aplikaci API serveru* a vyberte tlačítko **Spustit** .</span><span class="sxs-lookup"><span data-stu-id="b2d9b-237">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="b2d9b-238">Vyberte projekt serveru v **Průzkumník řešení** a na panelu nástrojů vyberte tlačítko **Spustit** nebo spusťte aplikaci z nabídky **ladění** .</span><span class="sxs-lookup"><span data-stu-id="b2d9b-238">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="b2d9b-239">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b2d9b-239">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="b2d9b-240">Neověřené nebo neautorizované požadavky webového rozhraní API v aplikaci s zabezpečeným výchozím klientem</span><span class="sxs-lookup"><span data-stu-id="b2d9b-240">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="b2d9b-241">Kurz: Vytvoření tenanta Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="b2d9b-241">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="b2d9b-242">Dokumentace k platformě Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="b2d9b-242">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)

---
title: Úvod do Identity ASP.NET Core
author: rick-anderson
description: Použijte Identity s aplikací ASP.NET Core. Přečtěte si, jak nastavit požadavky na heslo (RequireDigit, RequiredLength, RequiredUniqueChars a další).
ms.author: riande
ms.date: 01/15/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity
ms.openlocfilehash: 6ac565bfa4862168fa143417ab5a81c51b620f16
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212448"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="4b3a2-104">Úvod do Identity ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4b3a2-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4b3a2-105">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4b3a2-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4b3a2-106">ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="4b3a2-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="4b3a2-107">Je rozhraní API, které podporuje funkce přihlášení uživatelského rozhraní (UI).</span><span class="sxs-lookup"><span data-stu-id="4b3a2-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="4b3a2-108">Spravuje uživatele, hesla, data profilu, role, deklarace identity, tokeny, potvrzení e-mailu a další.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="4b3a2-109">Uživatelé můžou vytvořit účet s přihlašovacími informacemi uloženými v Identity nebo můžou použít externího poskytovatele přihlášení.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="4b3a2-110">Mezi podporované externí zprostředkovatelé přihlášení patří [Facebook, Google, účet Microsoft a Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="4b3a2-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="4b3a2-111">[ Identity Zdrojový kód](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) je k dispozici na GitHubu.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="4b3a2-112">[Generování Identity uživatelského rozhraní](xref:security/authentication/scaffold-identity) a zobrazit generované soubory pro kontrolu interakce šablony s Identity .</span><span class="sxs-lookup"><span data-stu-id="4b3a2-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

Identity<span data-ttu-id="4b3a2-113">je obvykle nakonfigurovaný pomocí SQL Server databáze pro ukládání uživatelských jmen, hesel a profilů.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-113"> is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="4b3a2-114">Případně můžete použít jiné trvalé úložiště, například Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="4b3a2-115">V tomto tématu se dozvíte, jak používat Identity k registraci, přihlášení a odhlášení uživatele.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="4b3a2-116">Poznámka: šablony považují uživatelské jméno a e-mail za stejné pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="4b3a2-117">Podrobnější pokyny k vytváření aplikací, které používají Identity , najdete v části Další kroky na konci tohoto článku.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-117">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<span data-ttu-id="4b3a2-118">[Platforma Microsoft Identity](/azure/active-directory/develop/) je:</span><span class="sxs-lookup"><span data-stu-id="4b3a2-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="4b3a2-119">Vývoj platformy pro vývojáře Azure Active Directory (Azure AD).</span><span class="sxs-lookup"><span data-stu-id="4b3a2-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="4b3a2-120">Nesouvisí s ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="4b3a2-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/Identity<span data-ttu-id="4b3a2-121">Server4.md)]</span><span class="sxs-lookup"><span data-stu-id="4b3a2-121">Server4.md)]</span></span>

<span data-ttu-id="4b3a2-122">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([Jak stáhnout)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="4b3a2-122">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="4b3a2-123">Vytvoření webové aplikace s ověřováním</span><span class="sxs-lookup"><span data-stu-id="4b3a2-123">Create a Web app with authentication</span></span>

<span data-ttu-id="4b3a2-124">Vytvořte ASP.NET Core projekt webové aplikace s jednotlivými uživatelskými účty.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-124">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4b3a2-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b3a2-125">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4b3a2-126">Vyberte **soubor** > **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-126">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="4b3a2-127">Vyberte **ASP.NET Core webové aplikace**.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-127">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="4b3a2-128">Pojmenujte projekt **WebApp1** tak, aby měl stejný obor názvů jako projekt ke stažení.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-128">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="4b3a2-129">Klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-129">Click **OK**.</span></span>
* <span data-ttu-id="4b3a2-130">Vyberte **webovou aplikaci**ASP.NET Core a pak vyberte **změnit ověřování**.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-130">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="4b3a2-131">Vyberte **jednotlivé uživatelské účty** a klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-131">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4b3a2-132">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4b3a2-132">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="4b3a2-133">Předchozí příkaz vytvoří Razor webovou aplikaci pomocí sqlite.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-133">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="4b3a2-134">Pokud chcete vytvořit webovou aplikaci pomocí LocalDB, spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="4b3a2-134">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="4b3a2-135">Vygenerovaný projekt poskytuje [ASP.NET Core Identity ](xref:security/authentication/identity) jako [ Razor knihovnu tříd](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="4b3a2-135">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="4b3a2-136">Identity Razor Knihovna tříd zpřístupňuje koncové body s `Identity` oblastí.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-136">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="4b3a2-137">Zde je příklad:</span><span class="sxs-lookup"><span data-stu-id="4b3a2-137">For example:</span></span>

* <span data-ttu-id="4b3a2-138">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="4b3a2-138">/Identity/Account/Login</span></span>
* <span data-ttu-id="4b3a2-139">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="4b3a2-139">/Identity/Account/Logout</span></span>
* <span data-ttu-id="4b3a2-140">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="4b3a2-140">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="4b3a2-141">Použít migrace</span><span class="sxs-lookup"><span data-stu-id="4b3a2-141">Apply migrations</span></span>

<span data-ttu-id="4b3a2-142">Použijte migrace k inicializaci databáze.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-142">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4b3a2-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b3a2-143">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4b3a2-144">Spusťte následující příkaz v konzole správce balíčků (PMC):</span><span class="sxs-lookup"><span data-stu-id="4b3a2-144">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="4b3a2-145">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4b3a2-145">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="4b3a2-146">Migrace nejsou v tomto kroku nutné při použití SQLite.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-146">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="4b3a2-147">V případě LocalDB spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="4b3a2-147">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="4b3a2-148">Registrace a přihlášení testu</span><span class="sxs-lookup"><span data-stu-id="4b3a2-148">Test Register and Login</span></span>

<span data-ttu-id="4b3a2-149">Spusťte aplikaci a zaregistrujte uživatele.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-149">Run the app and register a user.</span></span> <span data-ttu-id="4b3a2-150">V závislosti na velikosti obrazovky možná budete muset vybrat přepínač navigace a zobrazit tak odkazy na **registraci** a **přihlášení** .</span><span class="sxs-lookup"><span data-stu-id="4b3a2-150">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="4b3a2-151">Konfigurace Identity služeb</span><span class="sxs-lookup"><span data-stu-id="4b3a2-151">Configure Identity services</span></span>

<span data-ttu-id="4b3a2-152">Služby jsou přidány do `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="4b3a2-152">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="4b3a2-153">Typický vzor je zavolat všechny `Add{Service}` metody a pak zavolat všechny `services.Configure{Service}` metody.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-153">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="4b3a2-154">Předchozí zvýrazněný kód nakonfiguruje Identity s výchozími hodnotami možností.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-154">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="4b3a2-155">Služby jsou zpřístupněny aplikaci prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="4b3a2-155">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

Identity<span data-ttu-id="4b3a2-156">je povoleno voláním <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> .</span><span class="sxs-lookup"><span data-stu-id="4b3a2-156"> is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="4b3a2-157">`UseAuthentication`přidá [middleware](xref:fundamentals/middleware/index) ověřování do kanálu požadavků.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-157">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="4b3a2-158">Aplikace generovaná šablonou nepoužívá [autorizaci](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="4b3a2-158">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="4b3a2-159">`app.UseAuthorization`je součástí, abyste zajistili, že je přidaný do správného pořadí, aby aplikace přidala autorizaci.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-159">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="4b3a2-160">`UseRouting`, `UseAuthentication` , `UseAuthorization` , a `UseEndpoints` musí být volány v pořadí uvedeném v předchozím kódu.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-160">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="4b3a2-161">Další informace o systémech `IdentityOptions` a `Startup` najdete v tématu <xref:Microsoft.AspNetCore.Identity.IdentityOptions> a [spuštění aplikace](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="4b3a2-161">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="4b3a2-162">Registrace, přihlášení a odhlášení uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="4b3a2-162">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4b3a2-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b3a2-163">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4b3a2-164">Přidejte soubory registru, přihlášení a odhlášení.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-164">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="4b3a2-165">Použijte [identitu uživatelského rozhraní do Razor projektu s pokyny pro autorizaci](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) a vygenerujte kód uvedený v této části.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-165">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4b3a2-166">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4b3a2-166">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="4b3a2-167">Pokud jste vytvořili projekt s názvem **WebApp1**, spusťte následující příkazy.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-167">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="4b3a2-168">Jinak použijte správný obor názvů pro `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="4b3a2-168">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="4b3a2-169">PowerShell používá jako oddělovač příkazů středník.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-169">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="4b3a2-170">Při použití prostředí PowerShell, oddělte středníky v seznamu souborů nebo vložte seznam souborů do dvojitých uvozovek, jak ukazuje předchozí příklad.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-170">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="4b3a2-171">Další informace o generování uživatelského rozhraní Identity najdete v tématu [Identita uživatelského rozhraní do Razor projektu s autorizací](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="4b3a2-171">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="4b3a2-172">Ověřit registraci</span><span class="sxs-lookup"><span data-stu-id="4b3a2-172">Examine Register</span></span>

<span data-ttu-id="4b3a2-173">Když uživatel klikne na odkaz **zaregistrovat** , `RegisterModel.OnPostAsync` je akce vyvolána.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-173">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="4b3a2-174">Uživatel je vytvořen pomocí [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) na `_userManager` objekt:</span><span class="sxs-lookup"><span data-stu-id="4b3a2-174">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="4b3a2-175">Pokud byl uživatel vytvořen úspěšně, uživatel je přihlášen voláním `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="4b3a2-175">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="4b3a2-176">Postup, jak zabránit okamžitému přihlášení při registraci, najdete v tématu [potvrzení účtu](xref:security/authentication/accconfirm#prevent-login-at-registration) .</span><span class="sxs-lookup"><span data-stu-id="4b3a2-176">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="4b3a2-177">Přihlášení</span><span class="sxs-lookup"><span data-stu-id="4b3a2-177">Log in</span></span>

<span data-ttu-id="4b3a2-178">Přihlašovací formulář se zobrazí v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="4b3a2-178">The Login form is displayed when:</span></span>

* <span data-ttu-id="4b3a2-179">Je vybrán odkaz **Přihlásit** se.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-179">The **Log in** link is selected.</span></span>
* <span data-ttu-id="4b3a2-180">Uživatel se pokusí získat přístup ke stránce s omezením, že nemá oprávnění k přístupu, **nebo** když ho systém neověřil.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-180">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="4b3a2-181">Při odeslání formuláře na přihlašovací stránce se `OnPostAsync` zavolá akce.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-181">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="4b3a2-182">`PasswordSignInAsync`je volána u `_signInManager` objektu.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-182">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="4b3a2-183">Informace o tom, jak provádět autorizační rozhodnutí, najdete v tématu <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="4b3a2-183">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="4b3a2-184">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="4b3a2-184">Log out</span></span>

<span data-ttu-id="4b3a2-185">Odkaz **odhlášení** vyvolá `LogoutModel.OnPost` akci.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-185">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="4b3a2-186">V předchozím kódu `return RedirectToPage();` musí být kód přesměrování, aby prohlížeč prováděl novou žádost a identita pro uživatele byla aktualizována.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-186">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="4b3a2-187">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) vymaže deklarace identity uživatele uložené v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-187">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="4b3a2-188">Příspěvek je zadán ve *stránkách/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="4b3a2-188">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="4b3a2-189">NapadenIdentity</span><span class="sxs-lookup"><span data-stu-id="4b3a2-189">Test Identity</span></span>

<span data-ttu-id="4b3a2-190">Výchozí šablony webového projektu umožňují anonymní přístup k domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-190">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="4b3a2-191">Chcete-li otestovat Identity , přidejte [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :</span><span class="sxs-lookup"><span data-stu-id="4b3a2-191">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="4b3a2-192">Pokud jste přihlášeni, odhlaste se. Spusťte aplikaci a vyberte odkaz na **ochranu osobních údajů** .</span><span class="sxs-lookup"><span data-stu-id="4b3a2-192">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="4b3a2-193">Budete přesměrováni na přihlašovací stránku.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-193">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="4b3a2-194">OknoIdentity</span><span class="sxs-lookup"><span data-stu-id="4b3a2-194">Explore Identity</span></span>

<span data-ttu-id="4b3a2-195">Podrobněji prozkoumat Identity :</span><span class="sxs-lookup"><span data-stu-id="4b3a2-195">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="4b3a2-196">Vytvořit úplný zdroj uživatelského rozhraní identity</span><span class="sxs-lookup"><span data-stu-id="4b3a2-196">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="4b3a2-197">Projděte si zdroj každé stránky a proveďte krok pomocí ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-197">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="4b3a2-198">Konstrukční</span><span class="sxs-lookup"><span data-stu-id="4b3a2-198"> Components</span></span>

<span data-ttu-id="4b3a2-199">Všechny Identity balíčky NuGet závislé na rozhraních jsou součástí [ASP.NET Core sdílené rozhraní](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="4b3a2-199">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="4b3a2-200">Primární balíček pro Identity je [Microsoft. AspNetCore Identity .](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="4b3a2-200">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="4b3a2-201">Tento balíček obsahuje základní sadu rozhraní pro ASP.NET Core Identity a je součástí nástroje `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="4b3a2-201">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="4b3a2-202">Migrace na ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="4b3a2-202">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="4b3a2-203">Další informace a pokyny k migraci stávajícího Identity úložiště najdete v tématu [migrace ověřování a Identity ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="4b3a2-203">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="4b3a2-204">Nastavení síly hesla</span><span class="sxs-lookup"><span data-stu-id="4b3a2-204">Setting password strength</span></span>

<span data-ttu-id="4b3a2-205">V části [Konfigurace](#pw) najdete ukázku, která nastavuje minimální požadavky na heslo.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-205">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="4b3a2-206">AddDefault Identity a přidatIdentity</span><span class="sxs-lookup"><span data-stu-id="4b3a2-206">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="4b3a2-207"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>Služba WAS byla představena v ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-207"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="4b3a2-208">Volání `AddDefaultIdentity` je podobné volání následujícího:</span><span class="sxs-lookup"><span data-stu-id="4b3a2-208">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="4b3a2-209">Další informace najdete v tématu [AddDefault Identity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="4b3a2-209">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="4b3a2-210">Zabránit publikování statických Identity prostředků</span><span class="sxs-lookup"><span data-stu-id="4b3a2-210">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="4b3a2-211">Chcete-li zabránit publikování statických Identity prostředků (šablon stylů a souborů JavaScriptu pro Identity uživatelské rozhraní) do kořenového adresáře webu, přidejte následující `ResolveStaticWebAssetsInputsDependsOn` vlastnost a `RemoveIdentityAssets` cíl do souboru projektu aplikace:</span><span class="sxs-lookup"><span data-stu-id="4b3a2-211">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>RemoveIdentityAssets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="RemoveIdentityAssets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.Identity.UI'" />
  </ItemGroup>
</Target>
```

## <a name="next-steps"></a><span data-ttu-id="4b3a2-212">Další kroky</span><span class="sxs-lookup"><span data-stu-id="4b3a2-212">Next Steps</span></span>

* <span data-ttu-id="4b3a2-213">[IdentityZdrojový kód ASP.NET Core](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span><span class="sxs-lookup"><span data-stu-id="4b3a2-213">[ASP.NET Core Identity source code](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span></span>
* <span data-ttu-id="4b3a2-214">Informace o konfiguraci pomocí SQLite najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity .</span><span class="sxs-lookup"><span data-stu-id="4b3a2-214">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="4b3a2-215">[KonfiguraceIdentity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="4b3a2-215">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4b3a2-216">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4b3a2-216">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4b3a2-217">ASP.NET Core Identity je systém členství, který do ASP.NET Core aplikací přidává funkce přihlášení.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-217">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="4b3a2-218">Uživatelé můžou vytvořit účet s přihlašovacími informacemi uloženými v Identity nebo můžou použít externího poskytovatele přihlášení.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-218">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="4b3a2-219">Mezi podporované externí zprostředkovatelé přihlášení patří [Facebook, Google, účet Microsoft a Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="4b3a2-219">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

Identity<span data-ttu-id="4b3a2-220">dá se nakonfigurovat pomocí SQL Server databáze, aby se ukládala uživatelská jména, hesla a data profilu.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-220"> can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="4b3a2-221">Případně můžete použít jiné trvalé úložiště, například Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-221">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="4b3a2-222">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([Jak stáhnout)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="4b3a2-222">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="4b3a2-223">V tomto tématu se dozvíte, jak používat Identity k registraci, přihlášení a odhlášení uživatele.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-223">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="4b3a2-224">Podrobnější pokyny k vytváření aplikací, které používají Identity , najdete v části Další kroky na konci tohoto článku.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-224">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="4b3a2-225">AddDefault Identity a přidatIdentity</span><span class="sxs-lookup"><span data-stu-id="4b3a2-225">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="4b3a2-226"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>Služba WAS byla představena v ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-226"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="4b3a2-227">Volání `AddDefaultIdentity` je podobné volání následujícího:</span><span class="sxs-lookup"><span data-stu-id="4b3a2-227">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="4b3a2-228">Další informace najdete v tématu [AddDefault Identity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="4b3a2-228">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="4b3a2-229">Vytvoření webové aplikace s ověřováním</span><span class="sxs-lookup"><span data-stu-id="4b3a2-229">Create a Web app with authentication</span></span>

<span data-ttu-id="4b3a2-230">Vytvořte ASP.NET Core projekt webové aplikace s jednotlivými uživatelskými účty.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-230">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4b3a2-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b3a2-231">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4b3a2-232">Vyberte **soubor** > **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-232">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="4b3a2-233">Vyberte **ASP.NET Core webové aplikace**.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-233">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="4b3a2-234">Pojmenujte projekt **WebApp1** tak, aby měl stejný obor názvů jako projekt ke stažení.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-234">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="4b3a2-235">Klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-235">Click **OK**.</span></span>
* <span data-ttu-id="4b3a2-236">Vyberte **webovou aplikaci**ASP.NET Core a pak vyberte **změnit ověřování**.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-236">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="4b3a2-237">Vyberte **jednotlivé uživatelské účty** a klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-237">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4b3a2-238">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4b3a2-238">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="4b3a2-239">Vygenerovaný projekt poskytuje [ASP.NET Core Identity ](xref:security/authentication/identity) jako [ Razor knihovnu tříd](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="4b3a2-239">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="4b3a2-240">Identity Razor Knihovna tříd zpřístupňuje koncové body s `Identity` oblastí.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-240">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="4b3a2-241">Zde je příklad:</span><span class="sxs-lookup"><span data-stu-id="4b3a2-241">For example:</span></span>

* <span data-ttu-id="4b3a2-242">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="4b3a2-242">/Identity/Account/Login</span></span>
* <span data-ttu-id="4b3a2-243">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="4b3a2-243">/Identity/Account/Logout</span></span>
* <span data-ttu-id="4b3a2-244">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="4b3a2-244">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="4b3a2-245">Použít migrace</span><span class="sxs-lookup"><span data-stu-id="4b3a2-245">Apply migrations</span></span>

<span data-ttu-id="4b3a2-246">Použijte migrace k inicializaci databáze.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-246">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4b3a2-247">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b3a2-247">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4b3a2-248">Spusťte následující příkaz v konzole správce balíčků (PMC):</span><span class="sxs-lookup"><span data-stu-id="4b3a2-248">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="4b3a2-249">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4b3a2-249">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="4b3a2-250">Registrace a přihlášení testu</span><span class="sxs-lookup"><span data-stu-id="4b3a2-250">Test Register and Login</span></span>

<span data-ttu-id="4b3a2-251">Spusťte aplikaci a zaregistrujte uživatele.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-251">Run the app and register a user.</span></span> <span data-ttu-id="4b3a2-252">V závislosti na velikosti obrazovky možná budete muset vybrat přepínač navigace a zobrazit tak odkazy na **registraci** a **přihlášení** .</span><span class="sxs-lookup"><span data-stu-id="4b3a2-252">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="4b3a2-253">Konfigurace Identity služeb</span><span class="sxs-lookup"><span data-stu-id="4b3a2-253">Configure Identity services</span></span>

<span data-ttu-id="4b3a2-254">Služby jsou přidány do `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="4b3a2-254">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="4b3a2-255">Typický vzor je zavolat všechny `Add{Service}` metody a pak zavolat všechny `services.Configure{Service}` metody.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-255">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="4b3a2-256">Předchozí kód nakonfiguruje Identity s výchozími hodnotami možností.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-256">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="4b3a2-257">Služby jsou zpřístupněny aplikaci prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="4b3a2-257">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

Identity<span data-ttu-id="4b3a2-258">je povolen voláním [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="4b3a2-258"> is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="4b3a2-259">`UseAuthentication`přidá [middleware](xref:fundamentals/middleware/index) ověřování do kanálu požadavků.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-259">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="4b3a2-260">Další informace naleznete v tématu [ Identity třídy možností](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) a [spuštění aplikace](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="4b3a2-260">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="4b3a2-261">Registrace, přihlášení a odhlášení uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="4b3a2-261">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="4b3a2-262">Použijte [identitu uživatelského rozhraní do Razor projektu s pokyny pro autorizaci](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) a vygenerujte kód uvedený v této části.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-262">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4b3a2-263">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b3a2-263">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4b3a2-264">Přidejte soubory registru, přihlášení a odhlášení.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-264">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4b3a2-265">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4b3a2-265">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="4b3a2-266">Pokud jste vytvořili projekt s názvem **WebApp1**, spusťte následující příkazy.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-266">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="4b3a2-267">Jinak použijte správný obor názvů pro `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="4b3a2-267">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="4b3a2-268">PowerShell používá jako oddělovač příkazů středník.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-268">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="4b3a2-269">Při použití prostředí PowerShell, oddělte středníky v seznamu souborů nebo vložte seznam souborů do dvojitých uvozovek, jak ukazuje předchozí příklad.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-269">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="4b3a2-270">Ověřit registraci</span><span class="sxs-lookup"><span data-stu-id="4b3a2-270">Examine Register</span></span>

<span data-ttu-id="4b3a2-271">Když uživatel klikne na odkaz **zaregistrovat** , `RegisterModel.OnPostAsync` je akce vyvolána.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-271">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="4b3a2-272">Uživatel je vytvořen pomocí [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) na `_userManager` objekt:</span><span class="sxs-lookup"><span data-stu-id="4b3a2-272">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="4b3a2-273">Pokud byl uživatel vytvořen úspěšně, uživatel je přihlášen voláním `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="4b3a2-273">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="4b3a2-274">**Poznámka:** Postup, jak zabránit okamžitému přihlášení při registraci, najdete v tématu [potvrzení účtu](xref:security/authentication/accconfirm#prevent-login-at-registration) .</span><span class="sxs-lookup"><span data-stu-id="4b3a2-274">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="4b3a2-275">Přihlášení</span><span class="sxs-lookup"><span data-stu-id="4b3a2-275">Log in</span></span>

<span data-ttu-id="4b3a2-276">Přihlašovací formulář se zobrazí v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="4b3a2-276">The Login form is displayed when:</span></span>

* <span data-ttu-id="4b3a2-277">Je vybrán odkaz **Přihlásit** se.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-277">The **Log in** link is selected.</span></span>
* <span data-ttu-id="4b3a2-278">Uživatel se pokusí získat přístup ke stránce s omezením, že nemá oprávnění k přístupu, **nebo** když ho systém neověřil.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-278">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="4b3a2-279">Při odeslání formuláře na přihlašovací stránce se `OnPostAsync` zavolá akce.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-279">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="4b3a2-280">`PasswordSignInAsync`je volána u `_signInManager` objektu.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-280">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="4b3a2-281">Informace o tom, jak provádět autorizační rozhodnutí, najdete v tématu <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="4b3a2-281">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="4b3a2-282">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="4b3a2-282">Log out</span></span>

<span data-ttu-id="4b3a2-283">Odkaz **odhlášení** vyvolá `LogoutModel.OnPost` akci.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-283">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="4b3a2-284">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) vymaže deklarace identity uživatele uložené v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-284">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="4b3a2-285">Příspěvek je zadán ve *stránkách/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="4b3a2-285">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="4b3a2-286">NapadenIdentity</span><span class="sxs-lookup"><span data-stu-id="4b3a2-286">Test Identity</span></span>

<span data-ttu-id="4b3a2-287">Výchozí šablony webového projektu umožňují anonymní přístup k domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-287">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="4b3a2-288">Chcete-li otestovat Identity , přidejte [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) na stránku soukromí.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-288">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="4b3a2-289">Pokud jste přihlášeni, odhlaste se. Spusťte aplikaci a vyberte odkaz na **ochranu osobních údajů** .</span><span class="sxs-lookup"><span data-stu-id="4b3a2-289">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="4b3a2-290">Budete přesměrováni na přihlašovací stránku.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-290">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="4b3a2-291">OknoIdentity</span><span class="sxs-lookup"><span data-stu-id="4b3a2-291">Explore Identity</span></span>

<span data-ttu-id="4b3a2-292">Podrobněji prozkoumat Identity :</span><span class="sxs-lookup"><span data-stu-id="4b3a2-292">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="4b3a2-293">Vytvořit úplný zdroj uživatelského rozhraní identity</span><span class="sxs-lookup"><span data-stu-id="4b3a2-293">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="4b3a2-294">Projděte si zdroj každé stránky a proveďte krok pomocí ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-294">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="4b3a2-295">Konstrukční</span><span class="sxs-lookup"><span data-stu-id="4b3a2-295"> Components</span></span>

<span data-ttu-id="4b3a2-296">Všechny Identity závislé balíčky NuGet jsou součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="4b3a2-296">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="4b3a2-297">Primární balíček pro Identity je [Microsoft. AspNetCore Identity .](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="4b3a2-297">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="4b3a2-298">Tento balíček obsahuje základní sadu rozhraní pro ASP.NET Core Identity a je součástí nástroje `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="4b3a2-298">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="4b3a2-299">Migrace na ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="4b3a2-299">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="4b3a2-300">Další informace a pokyny k migraci stávajícího Identity úložiště najdete v tématu [migrace ověřování a Identity ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="4b3a2-300">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="4b3a2-301">Nastavení síly hesla</span><span class="sxs-lookup"><span data-stu-id="4b3a2-301">Setting password strength</span></span>

<span data-ttu-id="4b3a2-302">V části [Konfigurace](#pw) najdete ukázku, která nastavuje minimální požadavky na heslo.</span><span class="sxs-lookup"><span data-stu-id="4b3a2-302">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="4b3a2-303">Další kroky</span><span class="sxs-lookup"><span data-stu-id="4b3a2-303">Next Steps</span></span>

* <span data-ttu-id="4b3a2-304">Informace o konfiguraci pomocí SQLite najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity .</span><span class="sxs-lookup"><span data-stu-id="4b3a2-304">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="4b3a2-305">[KonfiguraceIdentity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="4b3a2-305">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

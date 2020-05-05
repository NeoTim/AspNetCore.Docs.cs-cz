---
title: Úvod do Identity ASP.NET Core
author: rick-anderson
description: Použijte Identity s aplikací ASP.NET Core. Přečtěte si, jak nastavit požadavky na heslo (RequireDigit, RequiredLength, RequiredUniqueChars a další).
ms.author: riande
ms.date: 01/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity
ms.openlocfilehash: d596a8357c5c812b94950809eedf35718328747c
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777004"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="cc98d-104">Úvod do identity na ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cc98d-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cc98d-105">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cc98d-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="cc98d-106">ASP.NET Core identita:</span><span class="sxs-lookup"><span data-stu-id="cc98d-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="cc98d-107">Je rozhraní API, které podporuje funkce přihlášení uživatelského rozhraní (UI).</span><span class="sxs-lookup"><span data-stu-id="cc98d-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="cc98d-108">Spravuje uživatele, hesla, data profilu, role, deklarace identity, tokeny, potvrzení e-mailu a další.</span><span class="sxs-lookup"><span data-stu-id="cc98d-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="cc98d-109">Uživatelé můžou vytvořit účet s přihlašovacími informacemi uloženými v identitě nebo můžou použít externího poskytovatele přihlášení.</span><span class="sxs-lookup"><span data-stu-id="cc98d-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="cc98d-110">Mezi podporované externí zprostředkovatelé přihlášení patří [Facebook, Google, účet Microsoft a Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="cc98d-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="cc98d-111">[Zdrojový kód identity](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) je k dispozici na GitHubu.</span><span class="sxs-lookup"><span data-stu-id="cc98d-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="cc98d-112">[Identita uživatelského rozhraní](xref:security/authentication/scaffold-identity) a zobrazení vygenerovaných souborů pro kontrolu interakce šablony s identitou.</span><span class="sxs-lookup"><span data-stu-id="cc98d-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="cc98d-113">Identita je obvykle nakonfigurovaná pomocí SQL Server databáze pro ukládání uživatelských jmen, hesel a profilových dat.</span><span class="sxs-lookup"><span data-stu-id="cc98d-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="cc98d-114">Případně můžete použít jiné trvalé úložiště, například Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="cc98d-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="cc98d-115">V tomto tématu se dozvíte, jak používat identitu k registraci, přihlášení a odhlášení uživatele.</span><span class="sxs-lookup"><span data-stu-id="cc98d-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="cc98d-116">Poznámka: šablony považují uživatelské jméno a e-mail za stejné pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="cc98d-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="cc98d-117">Podrobnější pokyny k vytváření aplikací, které používají identitu, najdete v části Další kroky na konci tohoto článku.</span><span class="sxs-lookup"><span data-stu-id="cc98d-117">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<span data-ttu-id="cc98d-118">[Platforma Microsoft Identity](/azure/active-directory/develop/) je:</span><span class="sxs-lookup"><span data-stu-id="cc98d-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="cc98d-119">Vývoj platformy pro vývojáře Azure Active Directory (Azure AD).</span><span class="sxs-lookup"><span data-stu-id="cc98d-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="cc98d-120">Nesouvisí se ASP.NET Core identitou.</span><span class="sxs-lookup"><span data-stu-id="cc98d-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="cc98d-121">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([Jak stáhnout)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="cc98d-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="cc98d-122">Vytvoření webové aplikace s ověřováním</span><span class="sxs-lookup"><span data-stu-id="cc98d-122">Create a Web app with authentication</span></span>

<span data-ttu-id="cc98d-123">Vytvořte ASP.NET Core projekt webové aplikace s jednotlivými uživatelskými účty.</span><span class="sxs-lookup"><span data-stu-id="cc98d-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cc98d-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc98d-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cc98d-125">Vyberte **soubor** > **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="cc98d-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="cc98d-126">Vyberte **ASP.NET Core webové aplikace**.</span><span class="sxs-lookup"><span data-stu-id="cc98d-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="cc98d-127">Pojmenujte projekt **WebApp1** tak, aby měl stejný obor názvů jako projekt ke stažení.</span><span class="sxs-lookup"><span data-stu-id="cc98d-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="cc98d-128">Klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="cc98d-128">Click **OK**.</span></span>
* <span data-ttu-id="cc98d-129">Vyberte **webovou aplikaci**ASP.NET Core a pak vyberte **změnit ověřování**.</span><span class="sxs-lookup"><span data-stu-id="cc98d-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="cc98d-130">Vyberte **jednotlivé uživatelské účty** a klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="cc98d-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cc98d-131">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="cc98d-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="cc98d-132">Předchozí příkaz vytvoří webovou aplikaci Razor pomocí SQLite.</span><span class="sxs-lookup"><span data-stu-id="cc98d-132">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="cc98d-133">Pokud chcete vytvořit webovou aplikaci pomocí LocalDB, spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="cc98d-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="cc98d-134">Vygenerovaný projekt poskytuje [ASP.NET Coreou identitu](xref:security/authentication/identity) jako [knihovnu tříd Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="cc98d-134">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="cc98d-135">Knihovna tříd identity Razor zpřístupňuje koncové body s `Identity` oblastí.</span><span class="sxs-lookup"><span data-stu-id="cc98d-135">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="cc98d-136">Příklad:</span><span class="sxs-lookup"><span data-stu-id="cc98d-136">For example:</span></span>

* <span data-ttu-id="cc98d-137">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="cc98d-137">/Identity/Account/Login</span></span>
* <span data-ttu-id="cc98d-138">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="cc98d-138">/Identity/Account/Logout</span></span>
* <span data-ttu-id="cc98d-139">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="cc98d-139">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="cc98d-140">Použít migrace</span><span class="sxs-lookup"><span data-stu-id="cc98d-140">Apply migrations</span></span>

<span data-ttu-id="cc98d-141">Použijte migrace k inicializaci databáze.</span><span class="sxs-lookup"><span data-stu-id="cc98d-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cc98d-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc98d-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cc98d-143">Spusťte následující příkaz v konzole správce balíčků (PMC):</span><span class="sxs-lookup"><span data-stu-id="cc98d-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="cc98d-144">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="cc98d-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="cc98d-145">Migrace nejsou v tomto kroku nutné při použití SQLite.</span><span class="sxs-lookup"><span data-stu-id="cc98d-145">Migrations are not necessary at this step when using SQLite.</span></span> <span data-ttu-id="cc98d-146">V případě LocalDB spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="cc98d-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="cc98d-147">Registrace a přihlášení testu</span><span class="sxs-lookup"><span data-stu-id="cc98d-147">Test Register and Login</span></span>

<span data-ttu-id="cc98d-148">Spusťte aplikaci a zaregistrujte uživatele.</span><span class="sxs-lookup"><span data-stu-id="cc98d-148">Run the app and register a user.</span></span> <span data-ttu-id="cc98d-149">V závislosti na velikosti obrazovky možná budete muset vybrat přepínač navigace a zobrazit tak odkazy na **registraci** a **přihlášení** .</span><span class="sxs-lookup"><span data-stu-id="cc98d-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="cc98d-150">Konfigurace služby identity</span><span class="sxs-lookup"><span data-stu-id="cc98d-150">Configure Identity services</span></span>

<span data-ttu-id="cc98d-151">Služby jsou přidány do `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="cc98d-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="cc98d-152">Typický vzor je zavolat všechny `Add{Service}` metody a pak zavolat všechny `services.Configure{Service}` metody.</span><span class="sxs-lookup"><span data-stu-id="cc98d-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="cc98d-153">Předchozí zvýrazněný kód nakonfiguruje identitu s výchozími hodnotami možností.</span><span class="sxs-lookup"><span data-stu-id="cc98d-153">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="cc98d-154">Služby jsou zpřístupněny aplikaci prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cc98d-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="cc98d-155">Identita je povolena voláním <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span><span class="sxs-lookup"><span data-stu-id="cc98d-155">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="cc98d-156">`UseAuthentication`přidá [middleware](xref:fundamentals/middleware/index) ověřování do kanálu požadavků.</span><span class="sxs-lookup"><span data-stu-id="cc98d-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="cc98d-157">Aplikace generovaná šablonou nepoužívá [autorizaci](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="cc98d-157">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="cc98d-158">`app.UseAuthorization`je součástí, abyste zajistili, že je přidaný do správného pořadí, aby aplikace přidala autorizaci.</span><span class="sxs-lookup"><span data-stu-id="cc98d-158">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="cc98d-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, a `UseEndpoints` musí být volány v pořadí uvedeném v předchozím kódu.</span><span class="sxs-lookup"><span data-stu-id="cc98d-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="cc98d-160">Další informace o systémech `IdentityOptions` a `Startup`najdete v <xref:Microsoft.AspNetCore.Identity.IdentityOptions> tématu a [spuštění aplikace](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="cc98d-160">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="cc98d-161">Registrace, přihlášení a odhlášení uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="cc98d-161">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cc98d-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc98d-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cc98d-163">Přidejte soubory registru, přihlášení a odhlášení.</span><span class="sxs-lookup"><span data-stu-id="cc98d-163">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="cc98d-164">Použijte [identitu uživatelského rozhraní do projektu Razor s pokyny k autorizaci](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) a vygenerujte kód uvedený v této části.</span><span class="sxs-lookup"><span data-stu-id="cc98d-164">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cc98d-165">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="cc98d-165">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="cc98d-166">Pokud jste vytvořili projekt s názvem **WebApp1**, spusťte následující příkazy.</span><span class="sxs-lookup"><span data-stu-id="cc98d-166">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="cc98d-167">Jinak použijte správný obor názvů pro `ApplicationDbContext`:</span><span class="sxs-lookup"><span data-stu-id="cc98d-167">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="cc98d-168">PowerShell používá jako oddělovač příkazů středník.</span><span class="sxs-lookup"><span data-stu-id="cc98d-168">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="cc98d-169">Při použití prostředí PowerShell, oddělte středníky v seznamu souborů nebo vložte seznam souborů do dvojitých uvozovek, jak ukazuje předchozí příklad.</span><span class="sxs-lookup"><span data-stu-id="cc98d-169">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="cc98d-170">Další informace o identitě uživatelského rozhraní naleznete v tématu [Identita uživatelského rozhraní do projektu Razor s autorizací](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="cc98d-170">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="cc98d-171">Ověřit registraci</span><span class="sxs-lookup"><span data-stu-id="cc98d-171">Examine Register</span></span>

<span data-ttu-id="cc98d-172">Když uživatel klikne na odkaz **zaregistrovat** , je `RegisterModel.OnPostAsync` akce vyvolána.</span><span class="sxs-lookup"><span data-stu-id="cc98d-172">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="cc98d-173">Uživatel je vytvořen pomocí [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) na `_userManager` objektu.</span><span class="sxs-lookup"><span data-stu-id="cc98d-173">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="cc98d-174">`_userManager`je poskytována vkládáním závislostí):</span><span class="sxs-lookup"><span data-stu-id="cc98d-174">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="cc98d-175">Pokud byl uživatel vytvořen úspěšně, uživatel je přihlášen voláním `_signInManager.SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="cc98d-175">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="cc98d-176">Postup, jak zabránit okamžitému přihlášení při registraci, najdete v tématu [potvrzení účtu](xref:security/authentication/accconfirm#prevent-login-at-registration) .</span><span class="sxs-lookup"><span data-stu-id="cc98d-176">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="cc98d-177">Přihlášení</span><span class="sxs-lookup"><span data-stu-id="cc98d-177">Log in</span></span>

<span data-ttu-id="cc98d-178">Přihlašovací formulář se zobrazí v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="cc98d-178">The Login form is displayed when:</span></span>

* <span data-ttu-id="cc98d-179">Je vybrán odkaz **Přihlásit** se.</span><span class="sxs-lookup"><span data-stu-id="cc98d-179">The **Log in** link is selected.</span></span>
* <span data-ttu-id="cc98d-180">Uživatel se pokusí získat přístup ke stránce s omezením, že nemá oprávnění k přístupu, **nebo** když ho systém neověřil.</span><span class="sxs-lookup"><span data-stu-id="cc98d-180">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="cc98d-181">Při odeslání formuláře na přihlašovací stránce se zavolá `OnPostAsync` akce.</span><span class="sxs-lookup"><span data-stu-id="cc98d-181">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="cc98d-182">`PasswordSignInAsync`je volána u `_signInManager` objektu (poskytovaného vkládáním závislostí).</span><span class="sxs-lookup"><span data-stu-id="cc98d-182">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="cc98d-183">Základní `Controller` třída zpřístupňuje `User` vlastnost, která je k dispozici z metod kontroleru.</span><span class="sxs-lookup"><span data-stu-id="cc98d-183">The base `Controller` class exposes a `User` property that can be accessed from controller methods.</span></span> <span data-ttu-id="cc98d-184">Můžete například vytvořit výčet `User.Claims` a provést rozhodnutí o autorizaci.</span><span class="sxs-lookup"><span data-stu-id="cc98d-184">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="cc98d-185">Další informace naleznete v tématu <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="cc98d-185">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="cc98d-186">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="cc98d-186">Log out</span></span>

<span data-ttu-id="cc98d-187">Odkaz **odhlášení** vyvolá `LogoutModel.OnPost` akci.</span><span class="sxs-lookup"><span data-stu-id="cc98d-187">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="cc98d-188">V předchozím kódu musí být kód `return RedirectToPage();` přesměrování, aby prohlížeč prováděl novou žádost a identita pro uživatele byla aktualizována.</span><span class="sxs-lookup"><span data-stu-id="cc98d-188">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="cc98d-189">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) vymaže deklarace identity uživatele uložené v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="cc98d-189">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="cc98d-190">Příspěvek je zadán ve *stránkách/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="cc98d-190">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="cc98d-191">Test identity</span><span class="sxs-lookup"><span data-stu-id="cc98d-191">Test Identity</span></span>

<span data-ttu-id="cc98d-192">Výchozí šablony webového projektu umožňují anonymní přístup k domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="cc98d-192">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="cc98d-193">Chcete-li otestovat identitu [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute), přidejte:</span><span class="sxs-lookup"><span data-stu-id="cc98d-193">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="cc98d-194">Pokud jste přihlášeni, odhlaste se. Spusťte aplikaci a vyberte odkaz na **ochranu osobních údajů** .</span><span class="sxs-lookup"><span data-stu-id="cc98d-194">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="cc98d-195">Budete přesměrováni na přihlašovací stránku.</span><span class="sxs-lookup"><span data-stu-id="cc98d-195">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="cc98d-196">Prozkoumat identitu</span><span class="sxs-lookup"><span data-stu-id="cc98d-196">Explore Identity</span></span>

<span data-ttu-id="cc98d-197">Podrobněji prozkoumat identitu:</span><span class="sxs-lookup"><span data-stu-id="cc98d-197">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="cc98d-198">Vytvořit úplný zdroj uživatelského rozhraní identity</span><span class="sxs-lookup"><span data-stu-id="cc98d-198">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="cc98d-199">Projděte si zdroj každé stránky a proveďte krok pomocí ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="cc98d-199">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="cc98d-200">Komponenty identity</span><span class="sxs-lookup"><span data-stu-id="cc98d-200">Identity Components</span></span>

<span data-ttu-id="cc98d-201">Všechny balíčky NuGet závislé na identitách jsou součástí [ASP.NET Core sdílené rozhraní](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="cc98d-201">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="cc98d-202">Primárním balíčkem identity je [Microsoft. AspNetCore. identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="cc98d-202">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="cc98d-203">Tento balíček obsahuje základní sadu rozhraní pro ASP.NET Core identitu a je zahrnutý v `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="cc98d-203">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="cc98d-204">Migrace na ASP.NET Core identity</span><span class="sxs-lookup"><span data-stu-id="cc98d-204">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="cc98d-205">Další informace a pokyny k migraci stávajícího úložiště identit najdete v tématu [migrace ověřování a identity](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="cc98d-205">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="cc98d-206">Nastavení síly hesla</span><span class="sxs-lookup"><span data-stu-id="cc98d-206">Setting password strength</span></span>

<span data-ttu-id="cc98d-207">V části [Konfigurace](#pw) najdete ukázku, která nastavuje minimální požadavky na heslo.</span><span class="sxs-lookup"><span data-stu-id="cc98d-207">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="cc98d-208">AddDefaultIdentity a AddIdentity</span><span class="sxs-lookup"><span data-stu-id="cc98d-208">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="cc98d-209"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>Služba WAS byla představena v ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="cc98d-209"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="cc98d-210">Volání `AddDefaultIdentity` je podobné volání následujícího:</span><span class="sxs-lookup"><span data-stu-id="cc98d-210">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="cc98d-211">Další informace najdete v tématu [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="cc98d-211">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="cc98d-212">Zabránit publikování prostředků statické identity</span><span class="sxs-lookup"><span data-stu-id="cc98d-212">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="cc98d-213">Pokud chcete zabránit publikování statických prostředků identity (šablony stylů a souborů JavaScriptu pro uživatelské rozhraní identity) do kořenového adresáře webu `ResolveStaticWebAssetsInputsDependsOn` , přidejte `RemoveIdentityAssets` do souboru projektu aplikace následující vlastnost a cíl:</span><span class="sxs-lookup"><span data-stu-id="cc98d-213">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

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

## <a name="next-steps"></a><span data-ttu-id="cc98d-214">Další kroky</span><span class="sxs-lookup"><span data-stu-id="cc98d-214">Next Steps</span></span>

* <span data-ttu-id="cc98d-215">Informace o konfiguraci identity pomocí SQLite najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/5131) .</span><span class="sxs-lookup"><span data-stu-id="cc98d-215">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="cc98d-216">Konfigurace systému Identity</span><span class="sxs-lookup"><span data-stu-id="cc98d-216">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cc98d-217">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cc98d-217">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="cc98d-218">ASP.NET Core identity je systém členství, který do ASP.NET Core aplikací přidává funkce přihlášení.</span><span class="sxs-lookup"><span data-stu-id="cc98d-218">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="cc98d-219">Uživatelé můžou vytvořit účet s přihlašovacími informacemi uloženými v identitě nebo můžou použít externího poskytovatele přihlášení.</span><span class="sxs-lookup"><span data-stu-id="cc98d-219">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="cc98d-220">Mezi podporované externí zprostředkovatelé přihlášení patří [Facebook, Google, účet Microsoft a Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="cc98d-220">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="cc98d-221">Identitu můžete nakonfigurovat pomocí SQL Server databáze pro ukládání uživatelských jmen, hesel a profilových dat.</span><span class="sxs-lookup"><span data-stu-id="cc98d-221">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="cc98d-222">Případně můžete použít jiné trvalé úložiště, například Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="cc98d-222">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="cc98d-223">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([Jak stáhnout)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="cc98d-223">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="cc98d-224">V tomto tématu se dozvíte, jak používat identitu k registraci, přihlášení a odhlášení uživatele.</span><span class="sxs-lookup"><span data-stu-id="cc98d-224">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="cc98d-225">Podrobnější pokyny k vytváření aplikací, které používají identitu, najdete v části Další kroky na konci tohoto článku.</span><span class="sxs-lookup"><span data-stu-id="cc98d-225">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="cc98d-226">AddDefaultIdentity a AddIdentity</span><span class="sxs-lookup"><span data-stu-id="cc98d-226">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="cc98d-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>Služba WAS byla představena v ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="cc98d-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="cc98d-228">Volání `AddDefaultIdentity` je podobné volání následujícího:</span><span class="sxs-lookup"><span data-stu-id="cc98d-228">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="cc98d-229">Další informace najdete v tématu [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="cc98d-229">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="cc98d-230">Vytvoření webové aplikace s ověřováním</span><span class="sxs-lookup"><span data-stu-id="cc98d-230">Create a Web app with authentication</span></span>

<span data-ttu-id="cc98d-231">Vytvořte ASP.NET Core projekt webové aplikace s jednotlivými uživatelskými účty.</span><span class="sxs-lookup"><span data-stu-id="cc98d-231">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cc98d-232">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc98d-232">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cc98d-233">Vyberte **soubor** > **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="cc98d-233">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="cc98d-234">Vyberte **ASP.NET Core webové aplikace**.</span><span class="sxs-lookup"><span data-stu-id="cc98d-234">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="cc98d-235">Pojmenujte projekt **WebApp1** tak, aby měl stejný obor názvů jako projekt ke stažení.</span><span class="sxs-lookup"><span data-stu-id="cc98d-235">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="cc98d-236">Klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="cc98d-236">Click **OK**.</span></span>
* <span data-ttu-id="cc98d-237">Vyberte **webovou aplikaci**ASP.NET Core a pak vyberte **změnit ověřování**.</span><span class="sxs-lookup"><span data-stu-id="cc98d-237">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="cc98d-238">Vyberte **jednotlivé uživatelské účty** a klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="cc98d-238">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cc98d-239">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="cc98d-239">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="cc98d-240">Vygenerovaný projekt poskytuje [ASP.NET Coreou identitu](xref:security/authentication/identity) jako [knihovnu tříd Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="cc98d-240">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="cc98d-241">Knihovna tříd identity Razor zpřístupňuje koncové body s `Identity` oblastí.</span><span class="sxs-lookup"><span data-stu-id="cc98d-241">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="cc98d-242">Příklad:</span><span class="sxs-lookup"><span data-stu-id="cc98d-242">For example:</span></span>

* <span data-ttu-id="cc98d-243">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="cc98d-243">/Identity/Account/Login</span></span>
* <span data-ttu-id="cc98d-244">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="cc98d-244">/Identity/Account/Logout</span></span>
* <span data-ttu-id="cc98d-245">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="cc98d-245">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="cc98d-246">Použít migrace</span><span class="sxs-lookup"><span data-stu-id="cc98d-246">Apply migrations</span></span>

<span data-ttu-id="cc98d-247">Použijte migrace k inicializaci databáze.</span><span class="sxs-lookup"><span data-stu-id="cc98d-247">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cc98d-248">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc98d-248">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cc98d-249">Spusťte následující příkaz v konzole správce balíčků (PMC):</span><span class="sxs-lookup"><span data-stu-id="cc98d-249">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="cc98d-250">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="cc98d-250">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="cc98d-251">Registrace a přihlášení testu</span><span class="sxs-lookup"><span data-stu-id="cc98d-251">Test Register and Login</span></span>

<span data-ttu-id="cc98d-252">Spusťte aplikaci a zaregistrujte uživatele.</span><span class="sxs-lookup"><span data-stu-id="cc98d-252">Run the app and register a user.</span></span> <span data-ttu-id="cc98d-253">V závislosti na velikosti obrazovky možná budete muset vybrat přepínač navigace a zobrazit tak odkazy na **registraci** a **přihlášení** .</span><span class="sxs-lookup"><span data-stu-id="cc98d-253">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="cc98d-254">Konfigurace služby identity</span><span class="sxs-lookup"><span data-stu-id="cc98d-254">Configure Identity services</span></span>

<span data-ttu-id="cc98d-255">Služby jsou přidány do `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="cc98d-255">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="cc98d-256">Typický vzor je zavolat všechny `Add{Service}` metody a pak zavolat všechny `services.Configure{Service}` metody.</span><span class="sxs-lookup"><span data-stu-id="cc98d-256">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="cc98d-257">Předchozí kód nakonfiguruje identitu s výchozími hodnotami možností.</span><span class="sxs-lookup"><span data-stu-id="cc98d-257">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="cc98d-258">Služby jsou zpřístupněny aplikaci prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cc98d-258">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="cc98d-259">Identita je povolena voláním [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="cc98d-259">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="cc98d-260">`UseAuthentication`přidá [middleware](xref:fundamentals/middleware/index) ověřování do kanálu požadavků.</span><span class="sxs-lookup"><span data-stu-id="cc98d-260">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="cc98d-261">Další informace naleznete v tématu [Třída IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) a [spuštění aplikace](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="cc98d-261">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="cc98d-262">Registrace, přihlášení a odhlášení uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="cc98d-262">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="cc98d-263">Použijte [identitu uživatelského rozhraní do projektu Razor s pokyny k autorizaci](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) a vygenerujte kód uvedený v této části.</span><span class="sxs-lookup"><span data-stu-id="cc98d-263">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cc98d-264">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc98d-264">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cc98d-265">Přidejte soubory registru, přihlášení a odhlášení.</span><span class="sxs-lookup"><span data-stu-id="cc98d-265">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cc98d-266">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="cc98d-266">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="cc98d-267">Pokud jste vytvořili projekt s názvem **WebApp1**, spusťte následující příkazy.</span><span class="sxs-lookup"><span data-stu-id="cc98d-267">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="cc98d-268">Jinak použijte správný obor názvů pro `ApplicationDbContext`:</span><span class="sxs-lookup"><span data-stu-id="cc98d-268">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="cc98d-269">PowerShell používá jako oddělovač příkazů středník.</span><span class="sxs-lookup"><span data-stu-id="cc98d-269">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="cc98d-270">Při použití prostředí PowerShell, oddělte středníky v seznamu souborů nebo vložte seznam souborů do dvojitých uvozovek, jak ukazuje předchozí příklad.</span><span class="sxs-lookup"><span data-stu-id="cc98d-270">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="cc98d-271">Ověřit registraci</span><span class="sxs-lookup"><span data-stu-id="cc98d-271">Examine Register</span></span>

<span data-ttu-id="cc98d-272">Když uživatel klikne na odkaz **zaregistrovat** , je `RegisterModel.OnPostAsync` akce vyvolána.</span><span class="sxs-lookup"><span data-stu-id="cc98d-272">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="cc98d-273">Uživatel je vytvořen pomocí [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) na `_userManager` objektu.</span><span class="sxs-lookup"><span data-stu-id="cc98d-273">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="cc98d-274">`_userManager`je poskytována vkládáním závislostí):</span><span class="sxs-lookup"><span data-stu-id="cc98d-274">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="cc98d-275">Pokud byl uživatel vytvořen úspěšně, uživatel je přihlášen voláním `_signInManager.SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="cc98d-275">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="cc98d-276">**Poznámka:** Postup, jak zabránit okamžitému přihlášení při registraci, najdete v tématu [potvrzení účtu](xref:security/authentication/accconfirm#prevent-login-at-registration) .</span><span class="sxs-lookup"><span data-stu-id="cc98d-276">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="cc98d-277">Přihlášení</span><span class="sxs-lookup"><span data-stu-id="cc98d-277">Log in</span></span>

<span data-ttu-id="cc98d-278">Přihlašovací formulář se zobrazí v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="cc98d-278">The Login form is displayed when:</span></span>

* <span data-ttu-id="cc98d-279">Je vybrán odkaz **Přihlásit** se.</span><span class="sxs-lookup"><span data-stu-id="cc98d-279">The **Log in** link is selected.</span></span>
* <span data-ttu-id="cc98d-280">Uživatel se pokusí získat přístup ke stránce s omezením, že nemá oprávnění k přístupu, **nebo** když ho systém neověřil.</span><span class="sxs-lookup"><span data-stu-id="cc98d-280">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="cc98d-281">Při odeslání formuláře na přihlašovací stránce se zavolá `OnPostAsync` akce.</span><span class="sxs-lookup"><span data-stu-id="cc98d-281">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="cc98d-282">`PasswordSignInAsync`je volána u `_signInManager` objektu (poskytovaného vkládáním závislostí).</span><span class="sxs-lookup"><span data-stu-id="cc98d-282">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="cc98d-283">Základní `Controller` třída zpřístupňuje `User` vlastnost, ke které máte přístup z metod kontroleru.</span><span class="sxs-lookup"><span data-stu-id="cc98d-283">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="cc98d-284">Můžete například vytvořit výčet `User.Claims` a provést rozhodnutí o autorizaci.</span><span class="sxs-lookup"><span data-stu-id="cc98d-284">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="cc98d-285">Další informace naleznete v tématu <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="cc98d-285">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="cc98d-286">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="cc98d-286">Log out</span></span>

<span data-ttu-id="cc98d-287">Odkaz **odhlášení** vyvolá `LogoutModel.OnPost` akci.</span><span class="sxs-lookup"><span data-stu-id="cc98d-287">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="cc98d-288">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) vymaže deklarace identity uživatele uložené v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="cc98d-288">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="cc98d-289">Příspěvek je zadán ve *stránkách/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="cc98d-289">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="cc98d-290">NapadenIdentity</span><span class="sxs-lookup"><span data-stu-id="cc98d-290">Test Identity</span></span>

<span data-ttu-id="cc98d-291">Výchozí šablony webového projektu umožňují anonymní přístup k domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="cc98d-291">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="cc98d-292">Chcete- Identityli otestovat [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) , přidejte na stránku soukromí.</span><span class="sxs-lookup"><span data-stu-id="cc98d-292">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="cc98d-293">Pokud jste přihlášeni, odhlaste se. Spusťte aplikaci a vyberte odkaz na **ochranu osobních údajů** .</span><span class="sxs-lookup"><span data-stu-id="cc98d-293">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="cc98d-294">Budete přesměrováni na přihlašovací stránku.</span><span class="sxs-lookup"><span data-stu-id="cc98d-294">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="cc98d-295">OknoIdentity</span><span class="sxs-lookup"><span data-stu-id="cc98d-295">Explore Identity</span></span>

<span data-ttu-id="cc98d-296">Podrobněji prozkoumat Identity :</span><span class="sxs-lookup"><span data-stu-id="cc98d-296">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="cc98d-297">Vytvořit úplný zdroj uživatelského rozhraní identity</span><span class="sxs-lookup"><span data-stu-id="cc98d-297">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="cc98d-298">Projděte si zdroj každé stránky a proveďte krok pomocí ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="cc98d-298">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="cc98d-299">Konstrukční</span><span class="sxs-lookup"><span data-stu-id="cc98d-299"> Components</span></span>

<span data-ttu-id="cc98d-300">Všechny Identity závislé balíčky NuGet jsou součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="cc98d-300">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="cc98d-301">Primární balíček pro Identity je [Microsoft. AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="cc98d-301">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="cc98d-302">Tento balíček obsahuje základní sadu rozhraní pro ASP.NET Core Identitya je součástí nástroje. `Microsoft.AspNetCore.Identity.EntityFrameworkCore`</span><span class="sxs-lookup"><span data-stu-id="cc98d-302">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="cc98d-303">Migrace na ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="cc98d-303">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="cc98d-304">Další informace a pokyny k migraci stávajícího Identity úložiště najdete v tématu [migrace ověřování a Identity ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="cc98d-304">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="cc98d-305">Nastavení síly hesla</span><span class="sxs-lookup"><span data-stu-id="cc98d-305">Setting password strength</span></span>

<span data-ttu-id="cc98d-306">V části [Konfigurace](#pw) najdete ukázku, která nastavuje minimální požadavky na heslo.</span><span class="sxs-lookup"><span data-stu-id="cc98d-306">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="cc98d-307">Další kroky</span><span class="sxs-lookup"><span data-stu-id="cc98d-307">Next Steps</span></span>

* <span data-ttu-id="cc98d-308">Informace o konfiguraci Identity pomocí SQLite najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/5131) .</span><span class="sxs-lookup"><span data-stu-id="cc98d-308">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="cc98d-309">[KonfiguraceIdentity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="cc98d-309">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

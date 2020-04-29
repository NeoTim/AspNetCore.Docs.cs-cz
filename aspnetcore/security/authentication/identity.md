---
title: Úvod do identity na ASP.NET Core
author: rick-anderson
description: Použijte identitu s aplikací ASP.NET Core. Přečtěte si, jak nastavit požadavky na heslo (RequireDigit, RequiredLength, RequiredUniqueChars a další).
ms.author: riande
ms.date: 01/15/2020
uid: security/authentication/identity
ms.openlocfilehash: 4bc5f206b3aee7c2d34055703acc5b6c5218f964
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205940"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="92949-104">Úvod do identity na ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="92949-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="92949-105">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="92949-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="92949-106">ASP.NET Core identita:</span><span class="sxs-lookup"><span data-stu-id="92949-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="92949-107">Je rozhraní API, které podporuje funkce přihlášení uživatelského rozhraní (UI).</span><span class="sxs-lookup"><span data-stu-id="92949-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="92949-108">Spravuje uživatele, hesla, data profilu, role, deklarace identity, tokeny, potvrzení e-mailu a další.</span><span class="sxs-lookup"><span data-stu-id="92949-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="92949-109">Uživatelé můžou vytvořit účet s přihlašovacími informacemi uloženými v identitě nebo můžou použít externího poskytovatele přihlášení.</span><span class="sxs-lookup"><span data-stu-id="92949-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="92949-110">Mezi podporované externí zprostředkovatelé přihlášení patří [Facebook, Google, účet Microsoft a Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="92949-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="92949-111">[Zdrojový kód identity](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) je k dispozici na GitHubu.</span><span class="sxs-lookup"><span data-stu-id="92949-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="92949-112">[Identita uživatelského rozhraní](xref:security/authentication/scaffold-identity) a zobrazení vygenerovaných souborů pro kontrolu interakce šablony s identitou.</span><span class="sxs-lookup"><span data-stu-id="92949-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="92949-113">Identita je obvykle nakonfigurovaná pomocí SQL Server databáze pro ukládání uživatelských jmen, hesel a profilových dat.</span><span class="sxs-lookup"><span data-stu-id="92949-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="92949-114">Případně můžete použít jiné trvalé úložiště, například Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="92949-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="92949-115">V tomto tématu se dozvíte, jak používat identitu k registraci, přihlášení a odhlášení uživatele.</span><span class="sxs-lookup"><span data-stu-id="92949-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="92949-116">Poznámka: šablony považují uživatelské jméno a e-mail za stejné pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="92949-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="92949-117">Podrobnější pokyny k vytváření aplikací, které používají identitu, najdete v části Další kroky na konci tohoto článku.</span><span class="sxs-lookup"><span data-stu-id="92949-117">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<span data-ttu-id="92949-118">[Platforma Microsoft Identity](/azure/active-directory/develop/) je:</span><span class="sxs-lookup"><span data-stu-id="92949-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="92949-119">Vývoj platformy pro vývojáře Azure Active Directory (Azure AD).</span><span class="sxs-lookup"><span data-stu-id="92949-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="92949-120">Nesouvisí se ASP.NET Core identitou.</span><span class="sxs-lookup"><span data-stu-id="92949-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="92949-121">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([Jak stáhnout)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="92949-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="92949-122">Vytvoření webové aplikace s ověřováním</span><span class="sxs-lookup"><span data-stu-id="92949-122">Create a Web app with authentication</span></span>

<span data-ttu-id="92949-123">Vytvořte ASP.NET Core projekt webové aplikace s jednotlivými uživatelskými účty.</span><span class="sxs-lookup"><span data-stu-id="92949-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="92949-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92949-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="92949-125">Vyberte **soubor** > **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="92949-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="92949-126">Vyberte **ASP.NET Core webové aplikace**.</span><span class="sxs-lookup"><span data-stu-id="92949-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="92949-127">Pojmenujte projekt **WebApp1** tak, aby měl stejný obor názvů jako projekt ke stažení.</span><span class="sxs-lookup"><span data-stu-id="92949-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="92949-128">Klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="92949-128">Click **OK**.</span></span>
* <span data-ttu-id="92949-129">Vyberte **webovou aplikaci**ASP.NET Core a pak vyberte **změnit ověřování**.</span><span class="sxs-lookup"><span data-stu-id="92949-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="92949-130">Vyberte **jednotlivé uživatelské účty** a klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="92949-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="92949-131">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="92949-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="92949-132">Předchozí příkaz vytvoří webovou aplikaci Razor pomocí SQLite.</span><span class="sxs-lookup"><span data-stu-id="92949-132">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="92949-133">Pokud chcete vytvořit webovou aplikaci pomocí LocalDB, spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="92949-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="92949-134">Vygenerovaný projekt poskytuje [ASP.NET Coreou identitu](xref:security/authentication/identity) jako [knihovnu tříd Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="92949-134">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="92949-135">Knihovna tříd identity Razor zpřístupňuje koncové body s `Identity` oblastí.</span><span class="sxs-lookup"><span data-stu-id="92949-135">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="92949-136">Příklad:</span><span class="sxs-lookup"><span data-stu-id="92949-136">For example:</span></span>

* <span data-ttu-id="92949-137">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="92949-137">/Identity/Account/Login</span></span>
* <span data-ttu-id="92949-138">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="92949-138">/Identity/Account/Logout</span></span>
* <span data-ttu-id="92949-139">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="92949-139">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="92949-140">Použít migrace</span><span class="sxs-lookup"><span data-stu-id="92949-140">Apply migrations</span></span>

<span data-ttu-id="92949-141">Použijte migrace k inicializaci databáze.</span><span class="sxs-lookup"><span data-stu-id="92949-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="92949-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92949-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="92949-143">Spusťte následující příkaz v konzole správce balíčků (PMC):</span><span class="sxs-lookup"><span data-stu-id="92949-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="92949-144">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="92949-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="92949-145">Migrace nejsou v tomto kroku nutné při použití SQLite.</span><span class="sxs-lookup"><span data-stu-id="92949-145">Migrations are not necessary at this step when using SQLite.</span></span> <span data-ttu-id="92949-146">V případě LocalDB spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="92949-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="92949-147">Registrace a přihlášení testu</span><span class="sxs-lookup"><span data-stu-id="92949-147">Test Register and Login</span></span>

<span data-ttu-id="92949-148">Spusťte aplikaci a zaregistrujte uživatele.</span><span class="sxs-lookup"><span data-stu-id="92949-148">Run the app and register a user.</span></span> <span data-ttu-id="92949-149">V závislosti na velikosti obrazovky možná budete muset vybrat přepínač navigace a zobrazit tak odkazy na **registraci** a **přihlášení** .</span><span class="sxs-lookup"><span data-stu-id="92949-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="92949-150">Konfigurace služby identity</span><span class="sxs-lookup"><span data-stu-id="92949-150">Configure Identity services</span></span>

<span data-ttu-id="92949-151">Služby jsou přidány do `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="92949-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="92949-152">Typický vzor je zavolat všechny `Add{Service}` metody a pak zavolat všechny `services.Configure{Service}` metody.</span><span class="sxs-lookup"><span data-stu-id="92949-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="92949-153">Předchozí zvýrazněný kód nakonfiguruje identitu s výchozími hodnotami možností.</span><span class="sxs-lookup"><span data-stu-id="92949-153">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="92949-154">Služby jsou zpřístupněny aplikaci prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="92949-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="92949-155">Identita je povolena voláním <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span><span class="sxs-lookup"><span data-stu-id="92949-155">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="92949-156">`UseAuthentication`přidá [middleware](xref:fundamentals/middleware/index) ověřování do kanálu požadavků.</span><span class="sxs-lookup"><span data-stu-id="92949-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="92949-157">Aplikace generovaná šablonou nepoužívá [autorizaci](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="92949-157">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="92949-158">`app.UseAuthorization`je součástí, abyste zajistili, že je přidaný do správného pořadí, aby aplikace přidala autorizaci.</span><span class="sxs-lookup"><span data-stu-id="92949-158">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="92949-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, a `UseEndpoints` musí být volány v pořadí uvedeném v předchozím kódu.</span><span class="sxs-lookup"><span data-stu-id="92949-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="92949-160">Další informace o systémech `IdentityOptions` a `Startup`najdete v <xref:Microsoft.AspNetCore.Identity.IdentityOptions> tématu a [spuštění aplikace](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="92949-160">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="92949-161">Registrace, přihlášení a odhlášení uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="92949-161">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="92949-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92949-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="92949-163">Přidejte soubory registru, přihlášení a odhlášení.</span><span class="sxs-lookup"><span data-stu-id="92949-163">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="92949-164">Použijte [identitu uživatelského rozhraní do projektu Razor s pokyny k autorizaci](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) a vygenerujte kód uvedený v této části.</span><span class="sxs-lookup"><span data-stu-id="92949-164">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="92949-165">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="92949-165">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="92949-166">Pokud jste vytvořili projekt s názvem **WebApp1**, spusťte následující příkazy.</span><span class="sxs-lookup"><span data-stu-id="92949-166">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="92949-167">Jinak použijte správný obor názvů pro `ApplicationDbContext`:</span><span class="sxs-lookup"><span data-stu-id="92949-167">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="92949-168">PowerShell používá jako oddělovač příkazů středník.</span><span class="sxs-lookup"><span data-stu-id="92949-168">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="92949-169">Při použití prostředí PowerShell, oddělte středníky v seznamu souborů nebo vložte seznam souborů do dvojitých uvozovek, jak ukazuje předchozí příklad.</span><span class="sxs-lookup"><span data-stu-id="92949-169">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="92949-170">Další informace o identitě uživatelského rozhraní naleznete v tématu [Identita uživatelského rozhraní do projektu Razor s autorizací](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="92949-170">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="92949-171">Ověřit registraci</span><span class="sxs-lookup"><span data-stu-id="92949-171">Examine Register</span></span>

<span data-ttu-id="92949-172">Když uživatel klikne na odkaz **zaregistrovat** , je `RegisterModel.OnPostAsync` akce vyvolána.</span><span class="sxs-lookup"><span data-stu-id="92949-172">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="92949-173">Uživatel je vytvořen pomocí [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) na `_userManager` objektu.</span><span class="sxs-lookup"><span data-stu-id="92949-173">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="92949-174">`_userManager`je poskytována vkládáním závislostí):</span><span class="sxs-lookup"><span data-stu-id="92949-174">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="92949-175">Pokud byl uživatel vytvořen úspěšně, uživatel je přihlášen voláním `_signInManager.SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="92949-175">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="92949-176">Postup, jak zabránit okamžitému přihlášení při registraci, najdete v tématu [potvrzení účtu](xref:security/authentication/accconfirm#prevent-login-at-registration) .</span><span class="sxs-lookup"><span data-stu-id="92949-176">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="92949-177">Přihlášení</span><span class="sxs-lookup"><span data-stu-id="92949-177">Log in</span></span>

<span data-ttu-id="92949-178">Přihlašovací formulář se zobrazí v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="92949-178">The Login form is displayed when:</span></span>

* <span data-ttu-id="92949-179">Je vybrán odkaz **Přihlásit** se.</span><span class="sxs-lookup"><span data-stu-id="92949-179">The **Log in** link is selected.</span></span>
* <span data-ttu-id="92949-180">Uživatel se pokusí získat přístup ke stránce s omezením, že nemá oprávnění k přístupu, **nebo** když ho systém neověřil.</span><span class="sxs-lookup"><span data-stu-id="92949-180">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="92949-181">Při odeslání formuláře na přihlašovací stránce se zavolá `OnPostAsync` akce.</span><span class="sxs-lookup"><span data-stu-id="92949-181">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="92949-182">`PasswordSignInAsync`je volána u `_signInManager` objektu (poskytovaného vkládáním závislostí).</span><span class="sxs-lookup"><span data-stu-id="92949-182">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="92949-183">Základní `Controller` třída zpřístupňuje `User` vlastnost, která je k dispozici z metod kontroleru.</span><span class="sxs-lookup"><span data-stu-id="92949-183">The base `Controller` class exposes a `User` property that can be accessed from controller methods.</span></span> <span data-ttu-id="92949-184">Můžete například vytvořit výčet `User.Claims` a provést rozhodnutí o autorizaci.</span><span class="sxs-lookup"><span data-stu-id="92949-184">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="92949-185">Další informace naleznete v tématu <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="92949-185">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="92949-186">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="92949-186">Log out</span></span>

<span data-ttu-id="92949-187">Odkaz **odhlášení** vyvolá `LogoutModel.OnPost` akci.</span><span class="sxs-lookup"><span data-stu-id="92949-187">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="92949-188">V předchozím kódu musí být kód `return RedirectToPage();` přesměrování, aby prohlížeč prováděl novou žádost a identita pro uživatele byla aktualizována.</span><span class="sxs-lookup"><span data-stu-id="92949-188">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="92949-189">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) vymaže deklarace identity uživatele uložené v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="92949-189">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="92949-190">Příspěvek je zadán ve *stránkách/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="92949-190">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="92949-191">Test identity</span><span class="sxs-lookup"><span data-stu-id="92949-191">Test Identity</span></span>

<span data-ttu-id="92949-192">Výchozí šablony webového projektu umožňují anonymní přístup k domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="92949-192">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="92949-193">Chcete-li otestovat identitu [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute), přidejte:</span><span class="sxs-lookup"><span data-stu-id="92949-193">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="92949-194">Pokud jste přihlášeni, odhlaste se. Spusťte aplikaci a vyberte odkaz na **ochranu osobních údajů** .</span><span class="sxs-lookup"><span data-stu-id="92949-194">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="92949-195">Budete přesměrováni na přihlašovací stránku.</span><span class="sxs-lookup"><span data-stu-id="92949-195">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="92949-196">Prozkoumat identitu</span><span class="sxs-lookup"><span data-stu-id="92949-196">Explore Identity</span></span>

<span data-ttu-id="92949-197">Podrobněji prozkoumat identitu:</span><span class="sxs-lookup"><span data-stu-id="92949-197">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="92949-198">Vytvořit úplný zdroj uživatelského rozhraní identity</span><span class="sxs-lookup"><span data-stu-id="92949-198">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="92949-199">Projděte si zdroj každé stránky a proveďte krok pomocí ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="92949-199">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="92949-200">Komponenty identity</span><span class="sxs-lookup"><span data-stu-id="92949-200">Identity Components</span></span>

<span data-ttu-id="92949-201">Všechny balíčky NuGet závislé na identitách jsou součástí [ASP.NET Core sdílené rozhraní](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="92949-201">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="92949-202">Primárním balíčkem identity je [Microsoft. AspNetCore. identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="92949-202">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="92949-203">Tento balíček obsahuje základní sadu rozhraní pro ASP.NET Core identitu a je zahrnutý v `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="92949-203">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="92949-204">Migrace na ASP.NET Core identity</span><span class="sxs-lookup"><span data-stu-id="92949-204">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="92949-205">Další informace a pokyny k migraci stávajícího úložiště identit najdete v tématu [migrace ověřování a identity](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="92949-205">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="92949-206">Nastavení síly hesla</span><span class="sxs-lookup"><span data-stu-id="92949-206">Setting password strength</span></span>

<span data-ttu-id="92949-207">V části [Konfigurace](#pw) najdete ukázku, která nastavuje minimální požadavky na heslo.</span><span class="sxs-lookup"><span data-stu-id="92949-207">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="92949-208">AddDefaultIdentity a AddIdentity</span><span class="sxs-lookup"><span data-stu-id="92949-208">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="92949-209"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>Služba WAS byla představena v ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="92949-209"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="92949-210">Volání `AddDefaultIdentity` je podobné volání následujícího:</span><span class="sxs-lookup"><span data-stu-id="92949-210">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="92949-211">Další informace najdete v tématu [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="92949-211">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="92949-212">Zabránit publikování prostředků statické identity</span><span class="sxs-lookup"><span data-stu-id="92949-212">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="92949-213">Pokud chcete zabránit publikování statických prostředků identity (šablony stylů a souborů JavaScriptu pro uživatelské rozhraní identity) do kořenového adresáře webu `ResolveStaticWebAssetsInputsDependsOn` , přidejte `RemoveIdentityAssets` do souboru projektu aplikace následující vlastnost a cíl:</span><span class="sxs-lookup"><span data-stu-id="92949-213">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

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

## <a name="next-steps"></a><span data-ttu-id="92949-214">Další kroky</span><span class="sxs-lookup"><span data-stu-id="92949-214">Next Steps</span></span>

* <span data-ttu-id="92949-215">Informace o konfiguraci identity pomocí SQLite najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/5131) .</span><span class="sxs-lookup"><span data-stu-id="92949-215">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="92949-216">Konfigurace systému Identity</span><span class="sxs-lookup"><span data-stu-id="92949-216">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="92949-217">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="92949-217">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="92949-218">ASP.NET Core identity je systém členství, který do ASP.NET Core aplikací přidává funkce přihlášení.</span><span class="sxs-lookup"><span data-stu-id="92949-218">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="92949-219">Uživatelé můžou vytvořit účet s přihlašovacími informacemi uloženými v identitě nebo můžou použít externího poskytovatele přihlášení.</span><span class="sxs-lookup"><span data-stu-id="92949-219">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="92949-220">Mezi podporované externí zprostředkovatelé přihlášení patří [Facebook, Google, účet Microsoft a Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="92949-220">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="92949-221">Identitu můžete nakonfigurovat pomocí SQL Server databáze pro ukládání uživatelských jmen, hesel a profilových dat.</span><span class="sxs-lookup"><span data-stu-id="92949-221">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="92949-222">Případně můžete použít jiné trvalé úložiště, například Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="92949-222">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="92949-223">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([Jak stáhnout)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="92949-223">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="92949-224">V tomto tématu se dozvíte, jak používat identitu k registraci, přihlášení a odhlášení uživatele.</span><span class="sxs-lookup"><span data-stu-id="92949-224">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="92949-225">Podrobnější pokyny k vytváření aplikací, které používají identitu, najdete v části Další kroky na konci tohoto článku.</span><span class="sxs-lookup"><span data-stu-id="92949-225">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="92949-226">AddDefaultIdentity a AddIdentity</span><span class="sxs-lookup"><span data-stu-id="92949-226">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="92949-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>Služba WAS byla představena v ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="92949-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="92949-228">Volání `AddDefaultIdentity` je podobné volání následujícího:</span><span class="sxs-lookup"><span data-stu-id="92949-228">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="92949-229">Další informace najdete v tématu [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="92949-229">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="92949-230">Vytvoření webové aplikace s ověřováním</span><span class="sxs-lookup"><span data-stu-id="92949-230">Create a Web app with authentication</span></span>

<span data-ttu-id="92949-231">Vytvořte ASP.NET Core projekt webové aplikace s jednotlivými uživatelskými účty.</span><span class="sxs-lookup"><span data-stu-id="92949-231">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="92949-232">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92949-232">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="92949-233">Vyberte **soubor** > **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="92949-233">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="92949-234">Vyberte **ASP.NET Core webové aplikace**.</span><span class="sxs-lookup"><span data-stu-id="92949-234">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="92949-235">Pojmenujte projekt **WebApp1** tak, aby měl stejný obor názvů jako projekt ke stažení.</span><span class="sxs-lookup"><span data-stu-id="92949-235">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="92949-236">Klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="92949-236">Click **OK**.</span></span>
* <span data-ttu-id="92949-237">Vyberte **webovou aplikaci**ASP.NET Core a pak vyberte **změnit ověřování**.</span><span class="sxs-lookup"><span data-stu-id="92949-237">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="92949-238">Vyberte **jednotlivé uživatelské účty** a klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="92949-238">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="92949-239">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="92949-239">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="92949-240">Vygenerovaný projekt poskytuje [ASP.NET Coreou identitu](xref:security/authentication/identity) jako [knihovnu tříd Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="92949-240">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="92949-241">Knihovna tříd identity Razor zpřístupňuje koncové body s `Identity` oblastí.</span><span class="sxs-lookup"><span data-stu-id="92949-241">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="92949-242">Příklad:</span><span class="sxs-lookup"><span data-stu-id="92949-242">For example:</span></span>

* <span data-ttu-id="92949-243">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="92949-243">/Identity/Account/Login</span></span>
* <span data-ttu-id="92949-244">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="92949-244">/Identity/Account/Logout</span></span>
* <span data-ttu-id="92949-245">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="92949-245">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="92949-246">Použít migrace</span><span class="sxs-lookup"><span data-stu-id="92949-246">Apply migrations</span></span>

<span data-ttu-id="92949-247">Použijte migrace k inicializaci databáze.</span><span class="sxs-lookup"><span data-stu-id="92949-247">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="92949-248">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92949-248">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="92949-249">Spusťte následující příkaz v konzole správce balíčků (PMC):</span><span class="sxs-lookup"><span data-stu-id="92949-249">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="92949-250">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="92949-250">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="92949-251">Registrace a přihlášení testu</span><span class="sxs-lookup"><span data-stu-id="92949-251">Test Register and Login</span></span>

<span data-ttu-id="92949-252">Spusťte aplikaci a zaregistrujte uživatele.</span><span class="sxs-lookup"><span data-stu-id="92949-252">Run the app and register a user.</span></span> <span data-ttu-id="92949-253">V závislosti na velikosti obrazovky možná budete muset vybrat přepínač navigace a zobrazit tak odkazy na **registraci** a **přihlášení** .</span><span class="sxs-lookup"><span data-stu-id="92949-253">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="92949-254">Konfigurace služby identity</span><span class="sxs-lookup"><span data-stu-id="92949-254">Configure Identity services</span></span>

<span data-ttu-id="92949-255">Služby jsou přidány do `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="92949-255">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="92949-256">Typický vzor je zavolat všechny `Add{Service}` metody a pak zavolat všechny `services.Configure{Service}` metody.</span><span class="sxs-lookup"><span data-stu-id="92949-256">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="92949-257">Předchozí kód nakonfiguruje identitu s výchozími hodnotami možností.</span><span class="sxs-lookup"><span data-stu-id="92949-257">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="92949-258">Služby jsou zpřístupněny aplikaci prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="92949-258">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="92949-259">Identita je povolena voláním [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="92949-259">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="92949-260">`UseAuthentication`přidá [middleware](xref:fundamentals/middleware/index) ověřování do kanálu požadavků.</span><span class="sxs-lookup"><span data-stu-id="92949-260">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="92949-261">Další informace naleznete v tématu [Třída IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) a [spuštění aplikace](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="92949-261">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="92949-262">Registrace, přihlášení a odhlášení uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="92949-262">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="92949-263">Použijte [identitu uživatelského rozhraní do projektu Razor s pokyny k autorizaci](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) a vygenerujte kód uvedený v této části.</span><span class="sxs-lookup"><span data-stu-id="92949-263">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="92949-264">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92949-264">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="92949-265">Přidejte soubory registru, přihlášení a odhlášení.</span><span class="sxs-lookup"><span data-stu-id="92949-265">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="92949-266">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="92949-266">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="92949-267">Pokud jste vytvořili projekt s názvem **WebApp1**, spusťte následující příkazy.</span><span class="sxs-lookup"><span data-stu-id="92949-267">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="92949-268">Jinak použijte správný obor názvů pro `ApplicationDbContext`:</span><span class="sxs-lookup"><span data-stu-id="92949-268">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="92949-269">PowerShell používá jako oddělovač příkazů středník.</span><span class="sxs-lookup"><span data-stu-id="92949-269">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="92949-270">Při použití prostředí PowerShell, oddělte středníky v seznamu souborů nebo vložte seznam souborů do dvojitých uvozovek, jak ukazuje předchozí příklad.</span><span class="sxs-lookup"><span data-stu-id="92949-270">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="92949-271">Ověřit registraci</span><span class="sxs-lookup"><span data-stu-id="92949-271">Examine Register</span></span>

<span data-ttu-id="92949-272">Když uživatel klikne na odkaz **zaregistrovat** , je `RegisterModel.OnPostAsync` akce vyvolána.</span><span class="sxs-lookup"><span data-stu-id="92949-272">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="92949-273">Uživatel je vytvořen pomocí [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) na `_userManager` objektu.</span><span class="sxs-lookup"><span data-stu-id="92949-273">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="92949-274">`_userManager`je poskytována vkládáním závislostí):</span><span class="sxs-lookup"><span data-stu-id="92949-274">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="92949-275">Pokud byl uživatel vytvořen úspěšně, uživatel je přihlášen voláním `_signInManager.SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="92949-275">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="92949-276">**Poznámka:** Postup, jak zabránit okamžitému přihlášení při registraci, najdete v tématu [potvrzení účtu](xref:security/authentication/accconfirm#prevent-login-at-registration) .</span><span class="sxs-lookup"><span data-stu-id="92949-276">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="92949-277">Přihlášení</span><span class="sxs-lookup"><span data-stu-id="92949-277">Log in</span></span>

<span data-ttu-id="92949-278">Přihlašovací formulář se zobrazí v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="92949-278">The Login form is displayed when:</span></span>

* <span data-ttu-id="92949-279">Je vybrán odkaz **Přihlásit** se.</span><span class="sxs-lookup"><span data-stu-id="92949-279">The **Log in** link is selected.</span></span>
* <span data-ttu-id="92949-280">Uživatel se pokusí získat přístup ke stránce s omezením, že nemá oprávnění k přístupu, **nebo** když ho systém neověřil.</span><span class="sxs-lookup"><span data-stu-id="92949-280">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="92949-281">Při odeslání formuláře na přihlašovací stránce se zavolá `OnPostAsync` akce.</span><span class="sxs-lookup"><span data-stu-id="92949-281">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="92949-282">`PasswordSignInAsync`je volána u `_signInManager` objektu (poskytovaného vkládáním závislostí).</span><span class="sxs-lookup"><span data-stu-id="92949-282">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="92949-283">Základní `Controller` třída zpřístupňuje `User` vlastnost, ke které máte přístup z metod kontroleru.</span><span class="sxs-lookup"><span data-stu-id="92949-283">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="92949-284">Můžete například vytvořit výčet `User.Claims` a provést rozhodnutí o autorizaci.</span><span class="sxs-lookup"><span data-stu-id="92949-284">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="92949-285">Další informace naleznete v tématu <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="92949-285">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="92949-286">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="92949-286">Log out</span></span>

<span data-ttu-id="92949-287">Odkaz **odhlášení** vyvolá `LogoutModel.OnPost` akci.</span><span class="sxs-lookup"><span data-stu-id="92949-287">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="92949-288">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) vymaže deklarace identity uživatele uložené v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="92949-288">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="92949-289">Příspěvek je zadán ve *stránkách/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="92949-289">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="92949-290">Test identity</span><span class="sxs-lookup"><span data-stu-id="92949-290">Test Identity</span></span>

<span data-ttu-id="92949-291">Výchozí šablony webového projektu umožňují anonymní přístup k domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="92949-291">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="92949-292">Chcete-li otestovat identitu [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) , přidejte na stránku soukromí.</span><span class="sxs-lookup"><span data-stu-id="92949-292">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="92949-293">Pokud jste přihlášeni, odhlaste se. Spusťte aplikaci a vyberte odkaz na **ochranu osobních údajů** .</span><span class="sxs-lookup"><span data-stu-id="92949-293">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="92949-294">Budete přesměrováni na přihlašovací stránku.</span><span class="sxs-lookup"><span data-stu-id="92949-294">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="92949-295">Prozkoumat identitu</span><span class="sxs-lookup"><span data-stu-id="92949-295">Explore Identity</span></span>

<span data-ttu-id="92949-296">Podrobněji prozkoumat identitu:</span><span class="sxs-lookup"><span data-stu-id="92949-296">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="92949-297">Vytvořit úplný zdroj uživatelského rozhraní identity</span><span class="sxs-lookup"><span data-stu-id="92949-297">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="92949-298">Projděte si zdroj každé stránky a proveďte krok pomocí ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="92949-298">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="92949-299">Komponenty identity</span><span class="sxs-lookup"><span data-stu-id="92949-299">Identity Components</span></span>

<span data-ttu-id="92949-300">Všechny balíčky NuGet závislé na identitě jsou zahrnuté ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="92949-300">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="92949-301">Primárním balíčkem identity je [Microsoft. AspNetCore. identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="92949-301">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="92949-302">Tento balíček obsahuje základní sadu rozhraní pro ASP.NET Core identitu a je zahrnutý v `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="92949-302">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="92949-303">Migrace na ASP.NET Core identity</span><span class="sxs-lookup"><span data-stu-id="92949-303">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="92949-304">Další informace a pokyny k migraci stávajícího úložiště identit najdete v tématu [migrace ověřování a identity](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="92949-304">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="92949-305">Nastavení síly hesla</span><span class="sxs-lookup"><span data-stu-id="92949-305">Setting password strength</span></span>

<span data-ttu-id="92949-306">V části [Konfigurace](#pw) najdete ukázku, která nastavuje minimální požadavky na heslo.</span><span class="sxs-lookup"><span data-stu-id="92949-306">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="92949-307">Další kroky</span><span class="sxs-lookup"><span data-stu-id="92949-307">Next Steps</span></span>

* <span data-ttu-id="92949-308">Informace o konfiguraci identity pomocí SQLite najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/5131) .</span><span class="sxs-lookup"><span data-stu-id="92949-308">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="92949-309">Konfigurace systému Identity</span><span class="sxs-lookup"><span data-stu-id="92949-309">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

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
ms.openlocfilehash: 6d9532ed8ff89735f41c362b27826436c37c6cb5
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452132"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="cb92d-104">Úvod do Identity ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cb92d-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cb92d-105">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cb92d-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="cb92d-106">ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="cb92d-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="cb92d-107">Je rozhraní API, které podporuje funkce přihlášení uživatelského rozhraní (UI).</span><span class="sxs-lookup"><span data-stu-id="cb92d-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="cb92d-108">Spravuje uživatele, hesla, data profilu, role, deklarace identity, tokeny, potvrzení e-mailu a další.</span><span class="sxs-lookup"><span data-stu-id="cb92d-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="cb92d-109">Uživatelé můžou vytvořit účet s přihlašovacími informacemi uloženými v Identity nebo můžou použít externího poskytovatele přihlášení.</span><span class="sxs-lookup"><span data-stu-id="cb92d-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="cb92d-110">Mezi podporované externí zprostředkovatelé přihlášení patří [Facebook, Google, účet Microsoft a Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="cb92d-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="cb92d-111">[ Identity Zdrojový kód](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) je k dispozici na GitHubu.</span><span class="sxs-lookup"><span data-stu-id="cb92d-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="cb92d-112">[Generování Identity uživatelského rozhraní](xref:security/authentication/scaffold-identity) a zobrazit generované soubory pro kontrolu interakce šablony s Identity .</span><span class="sxs-lookup"><span data-stu-id="cb92d-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

Identity<span data-ttu-id="cb92d-113">je obvykle nakonfigurovaný pomocí SQL Server databáze pro ukládání uživatelských jmen, hesel a profilů.</span><span class="sxs-lookup"><span data-stu-id="cb92d-113"> is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="cb92d-114">Případně můžete použít jiné trvalé úložiště, například Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="cb92d-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="cb92d-115">V tomto tématu se dozvíte, jak používat Identity k registraci, přihlášení a odhlášení uživatele.</span><span class="sxs-lookup"><span data-stu-id="cb92d-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="cb92d-116">Poznámka: šablony považují uživatelské jméno a e-mail za stejné pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="cb92d-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="cb92d-117">Podrobnější pokyny k vytváření aplikací, které používají Identity , najdete v části Další kroky na konci tohoto článku.</span><span class="sxs-lookup"><span data-stu-id="cb92d-117">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<span data-ttu-id="cb92d-118">[Platforma Microsoft Identity](/azure/active-directory/develop/) je:</span><span class="sxs-lookup"><span data-stu-id="cb92d-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="cb92d-119">Vývoj platformy pro vývojáře Azure Active Directory (Azure AD).</span><span class="sxs-lookup"><span data-stu-id="cb92d-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="cb92d-120">Nesouvisí s ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="cb92d-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="cb92d-121">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([Jak stáhnout)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="cb92d-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="cb92d-122">Vytvoření webové aplikace s ověřováním</span><span class="sxs-lookup"><span data-stu-id="cb92d-122">Create a Web app with authentication</span></span>

<span data-ttu-id="cb92d-123">Vytvořte ASP.NET Core projekt webové aplikace s jednotlivými uživatelskými účty.</span><span class="sxs-lookup"><span data-stu-id="cb92d-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cb92d-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb92d-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cb92d-125">Vyberte **soubor** > **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="cb92d-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="cb92d-126">Vyberte **ASP.NET Core webové aplikace**.</span><span class="sxs-lookup"><span data-stu-id="cb92d-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="cb92d-127">Pojmenujte projekt **WebApp1** tak, aby měl stejný obor názvů jako projekt ke stažení.</span><span class="sxs-lookup"><span data-stu-id="cb92d-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="cb92d-128">Klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="cb92d-128">Click **OK**.</span></span>
* <span data-ttu-id="cb92d-129">Vyberte **webovou aplikaci**ASP.NET Core a pak vyberte **změnit ověřování**.</span><span class="sxs-lookup"><span data-stu-id="cb92d-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="cb92d-130">Vyberte **jednotlivé uživatelské účty** a klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="cb92d-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cb92d-131">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cb92d-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="cb92d-132">Předchozí příkaz vytvoří Razor webovou aplikaci pomocí sqlite.</span><span class="sxs-lookup"><span data-stu-id="cb92d-132">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="cb92d-133">Pokud chcete vytvořit webovou aplikaci pomocí LocalDB, spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="cb92d-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="cb92d-134">Vygenerovaný projekt poskytuje [ASP.NET Core Identity ](xref:security/authentication/identity) jako [ Razor knihovnu tříd](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="cb92d-134">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="cb92d-135">Identity Razor Knihovna tříd zpřístupňuje koncové body s `Identity` oblastí.</span><span class="sxs-lookup"><span data-stu-id="cb92d-135">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="cb92d-136">Příklad:</span><span class="sxs-lookup"><span data-stu-id="cb92d-136">For example:</span></span>

* <span data-ttu-id="cb92d-137">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="cb92d-137">/Identity/Account/Login</span></span>
* <span data-ttu-id="cb92d-138">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="cb92d-138">/Identity/Account/Logout</span></span>
* <span data-ttu-id="cb92d-139">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="cb92d-139">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="cb92d-140">Použít migrace</span><span class="sxs-lookup"><span data-stu-id="cb92d-140">Apply migrations</span></span>

<span data-ttu-id="cb92d-141">Použijte migrace k inicializaci databáze.</span><span class="sxs-lookup"><span data-stu-id="cb92d-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cb92d-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb92d-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cb92d-143">Spusťte následující příkaz v konzole správce balíčků (PMC):</span><span class="sxs-lookup"><span data-stu-id="cb92d-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="cb92d-144">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cb92d-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="cb92d-145">Migrace nejsou v tomto kroku nutné při použití SQLite.</span><span class="sxs-lookup"><span data-stu-id="cb92d-145">Migrations are not necessary at this step when using SQLite.</span></span> <span data-ttu-id="cb92d-146">V případě LocalDB spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="cb92d-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="cb92d-147">Registrace a přihlášení testu</span><span class="sxs-lookup"><span data-stu-id="cb92d-147">Test Register and Login</span></span>

<span data-ttu-id="cb92d-148">Spusťte aplikaci a zaregistrujte uživatele.</span><span class="sxs-lookup"><span data-stu-id="cb92d-148">Run the app and register a user.</span></span> <span data-ttu-id="cb92d-149">V závislosti na velikosti obrazovky možná budete muset vybrat přepínač navigace a zobrazit tak odkazy na **registraci** a **přihlášení** .</span><span class="sxs-lookup"><span data-stu-id="cb92d-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="cb92d-150">Konfigurace Identity služeb</span><span class="sxs-lookup"><span data-stu-id="cb92d-150">Configure Identity services</span></span>

<span data-ttu-id="cb92d-151">Služby jsou přidány do `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cb92d-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="cb92d-152">Typický vzor je zavolat všechny `Add{Service}` metody a pak zavolat všechny `services.Configure{Service}` metody.</span><span class="sxs-lookup"><span data-stu-id="cb92d-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="cb92d-153">Předchozí zvýrazněný kód nakonfiguruje Identity s výchozími hodnotami možností.</span><span class="sxs-lookup"><span data-stu-id="cb92d-153">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="cb92d-154">Služby jsou zpřístupněny aplikaci prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cb92d-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

Identity<span data-ttu-id="cb92d-155">je povoleno voláním <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> .</span><span class="sxs-lookup"><span data-stu-id="cb92d-155"> is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="cb92d-156">`UseAuthentication`přidá [middleware](xref:fundamentals/middleware/index) ověřování do kanálu požadavků.</span><span class="sxs-lookup"><span data-stu-id="cb92d-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="cb92d-157">Aplikace generovaná šablonou nepoužívá [autorizaci](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="cb92d-157">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="cb92d-158">`app.UseAuthorization`je součástí, abyste zajistili, že je přidaný do správného pořadí, aby aplikace přidala autorizaci.</span><span class="sxs-lookup"><span data-stu-id="cb92d-158">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="cb92d-159">`UseRouting`, `UseAuthentication` , `UseAuthorization` , a `UseEndpoints` musí být volány v pořadí uvedeném v předchozím kódu.</span><span class="sxs-lookup"><span data-stu-id="cb92d-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="cb92d-160">Další informace o systémech `IdentityOptions` a `Startup` najdete v tématu <xref:Microsoft.AspNetCore.Identity.IdentityOptions> a [spuštění aplikace](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="cb92d-160">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="cb92d-161">Registrace, přihlášení a odhlášení uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="cb92d-161">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cb92d-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb92d-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cb92d-163">Přidejte soubory registru, přihlášení a odhlášení.</span><span class="sxs-lookup"><span data-stu-id="cb92d-163">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="cb92d-164">Použijte [identitu uživatelského rozhraní do Razor projektu s pokyny pro autorizaci](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) a vygenerujte kód uvedený v této části.</span><span class="sxs-lookup"><span data-stu-id="cb92d-164">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cb92d-165">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cb92d-165">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="cb92d-166">Pokud jste vytvořili projekt s názvem **WebApp1**, spusťte následující příkazy.</span><span class="sxs-lookup"><span data-stu-id="cb92d-166">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="cb92d-167">Jinak použijte správný obor názvů pro `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="cb92d-167">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="cb92d-168">PowerShell používá jako oddělovač příkazů středník.</span><span class="sxs-lookup"><span data-stu-id="cb92d-168">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="cb92d-169">Při použití prostředí PowerShell, oddělte středníky v seznamu souborů nebo vložte seznam souborů do dvojitých uvozovek, jak ukazuje předchozí příklad.</span><span class="sxs-lookup"><span data-stu-id="cb92d-169">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="cb92d-170">Další informace o generování uživatelského rozhraní Identity najdete v tématu [Identita uživatelského rozhraní do Razor projektu s autorizací](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="cb92d-170">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="cb92d-171">Ověřit registraci</span><span class="sxs-lookup"><span data-stu-id="cb92d-171">Examine Register</span></span>

<span data-ttu-id="cb92d-172">Když uživatel klikne na odkaz **zaregistrovat** , `RegisterModel.OnPostAsync` je akce vyvolána.</span><span class="sxs-lookup"><span data-stu-id="cb92d-172">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="cb92d-173">Uživatel je vytvořen pomocí [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) na `_userManager` objekt:</span><span class="sxs-lookup"><span data-stu-id="cb92d-173">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="cb92d-174">Pokud byl uživatel vytvořen úspěšně, uživatel je přihlášen voláním `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="cb92d-174">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="cb92d-175">Postup, jak zabránit okamžitému přihlášení při registraci, najdete v tématu [potvrzení účtu](xref:security/authentication/accconfirm#prevent-login-at-registration) .</span><span class="sxs-lookup"><span data-stu-id="cb92d-175">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="cb92d-176">Přihlášení</span><span class="sxs-lookup"><span data-stu-id="cb92d-176">Log in</span></span>

<span data-ttu-id="cb92d-177">Přihlašovací formulář se zobrazí v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="cb92d-177">The Login form is displayed when:</span></span>

* <span data-ttu-id="cb92d-178">Je vybrán odkaz **Přihlásit** se.</span><span class="sxs-lookup"><span data-stu-id="cb92d-178">The **Log in** link is selected.</span></span>
* <span data-ttu-id="cb92d-179">Uživatel se pokusí získat přístup ke stránce s omezením, že nemá oprávnění k přístupu, **nebo** když ho systém neověřil.</span><span class="sxs-lookup"><span data-stu-id="cb92d-179">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="cb92d-180">Při odeslání formuláře na přihlašovací stránce se `OnPostAsync` zavolá akce.</span><span class="sxs-lookup"><span data-stu-id="cb92d-180">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="cb92d-181">`PasswordSignInAsync`je volána u `_signInManager` objektu.</span><span class="sxs-lookup"><span data-stu-id="cb92d-181">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="cb92d-182">Informace o tom, jak provádět autorizační rozhodnutí, najdete v tématu <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="cb92d-182">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="cb92d-183">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="cb92d-183">Log out</span></span>

<span data-ttu-id="cb92d-184">Odkaz **odhlášení** vyvolá `LogoutModel.OnPost` akci.</span><span class="sxs-lookup"><span data-stu-id="cb92d-184">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="cb92d-185">V předchozím kódu `return RedirectToPage();` musí být kód přesměrování, aby prohlížeč prováděl novou žádost a identita pro uživatele byla aktualizována.</span><span class="sxs-lookup"><span data-stu-id="cb92d-185">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="cb92d-186">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) vymaže deklarace identity uživatele uložené v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="cb92d-186">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="cb92d-187">Příspěvek je zadán ve *stránkách/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="cb92d-187">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="cb92d-188">NapadenIdentity</span><span class="sxs-lookup"><span data-stu-id="cb92d-188">Test Identity</span></span>

<span data-ttu-id="cb92d-189">Výchozí šablony webového projektu umožňují anonymní přístup k domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="cb92d-189">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="cb92d-190">Chcete-li otestovat Identity , přidejte [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :</span><span class="sxs-lookup"><span data-stu-id="cb92d-190">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="cb92d-191">Pokud jste přihlášeni, odhlaste se. Spusťte aplikaci a vyberte odkaz na **ochranu osobních údajů** .</span><span class="sxs-lookup"><span data-stu-id="cb92d-191">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="cb92d-192">Budete přesměrováni na přihlašovací stránku.</span><span class="sxs-lookup"><span data-stu-id="cb92d-192">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="cb92d-193">OknoIdentity</span><span class="sxs-lookup"><span data-stu-id="cb92d-193">Explore Identity</span></span>

<span data-ttu-id="cb92d-194">Podrobněji prozkoumat Identity :</span><span class="sxs-lookup"><span data-stu-id="cb92d-194">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="cb92d-195">Vytvořit úplný zdroj uživatelského rozhraní identity</span><span class="sxs-lookup"><span data-stu-id="cb92d-195">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="cb92d-196">Projděte si zdroj každé stránky a proveďte krok pomocí ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="cb92d-196">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="cb92d-197">Konstrukční</span><span class="sxs-lookup"><span data-stu-id="cb92d-197"> Components</span></span>

<span data-ttu-id="cb92d-198">Všechny Identity balíčky NuGet závislé na rozhraních jsou součástí [ASP.NET Core sdílené rozhraní](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="cb92d-198">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="cb92d-199">Primární balíček pro Identity je [Microsoft. AspNetCore Identity .](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="cb92d-199">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="cb92d-200">Tento balíček obsahuje základní sadu rozhraní pro ASP.NET Core Identity a je součástí nástroje `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="cb92d-200">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="cb92d-201">Migrace na ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="cb92d-201">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="cb92d-202">Další informace a pokyny k migraci stávajícího Identity úložiště najdete v tématu [migrace ověřování a Identity ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="cb92d-202">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="cb92d-203">Nastavení síly hesla</span><span class="sxs-lookup"><span data-stu-id="cb92d-203">Setting password strength</span></span>

<span data-ttu-id="cb92d-204">V části [Konfigurace](#pw) najdete ukázku, která nastavuje minimální požadavky na heslo.</span><span class="sxs-lookup"><span data-stu-id="cb92d-204">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="cb92d-205">AddDefaultIdentity a AddIdentity</span><span class="sxs-lookup"><span data-stu-id="cb92d-205">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="cb92d-206"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>Služba WAS byla představena v ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="cb92d-206"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="cb92d-207">Volání `AddDefaultIdentity` je podobné volání následujícího:</span><span class="sxs-lookup"><span data-stu-id="cb92d-207">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="cb92d-208">Další informace najdete v tématu [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="cb92d-208">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="cb92d-209">Zabránit publikování statických Identity prostředků</span><span class="sxs-lookup"><span data-stu-id="cb92d-209">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="cb92d-210">Chcete-li zabránit publikování statických Identity prostředků (šablon stylů a souborů JavaScriptu pro Identity uživatelské rozhraní) do kořenového adresáře webu, přidejte následující `ResolveStaticWebAssetsInputsDependsOn` vlastnost a `RemoveIdentityAssets` cíl do souboru projektu aplikace:</span><span class="sxs-lookup"><span data-stu-id="cb92d-210">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

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

## <a name="next-steps"></a><span data-ttu-id="cb92d-211">Další kroky</span><span class="sxs-lookup"><span data-stu-id="cb92d-211">Next Steps</span></span>

* <span data-ttu-id="cb92d-212">Informace o konfiguraci pomocí SQLite najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity .</span><span class="sxs-lookup"><span data-stu-id="cb92d-212">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="cb92d-213">[KonfiguraceIdentity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="cb92d-213">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cb92d-214">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cb92d-214">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="cb92d-215">ASP.NET Core Identity je systém členství, který do ASP.NET Core aplikací přidává funkce přihlášení.</span><span class="sxs-lookup"><span data-stu-id="cb92d-215">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="cb92d-216">Uživatelé můžou vytvořit účet s přihlašovacími informacemi uloženými v Identity nebo můžou použít externího poskytovatele přihlášení.</span><span class="sxs-lookup"><span data-stu-id="cb92d-216">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="cb92d-217">Mezi podporované externí zprostředkovatelé přihlášení patří [Facebook, Google, účet Microsoft a Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="cb92d-217">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

Identity<span data-ttu-id="cb92d-218">dá se nakonfigurovat pomocí SQL Server databáze, aby se ukládala uživatelská jména, hesla a data profilu.</span><span class="sxs-lookup"><span data-stu-id="cb92d-218"> can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="cb92d-219">Případně můžete použít jiné trvalé úložiště, například Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="cb92d-219">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="cb92d-220">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([Jak stáhnout)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="cb92d-220">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="cb92d-221">V tomto tématu se dozvíte, jak používat Identity k registraci, přihlášení a odhlášení uživatele.</span><span class="sxs-lookup"><span data-stu-id="cb92d-221">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="cb92d-222">Podrobnější pokyny k vytváření aplikací, které používají Identity , najdete v části Další kroky na konci tohoto článku.</span><span class="sxs-lookup"><span data-stu-id="cb92d-222">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="cb92d-223">AddDefaultIdentity a AddIdentity</span><span class="sxs-lookup"><span data-stu-id="cb92d-223">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="cb92d-224"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>Služba WAS byla představena v ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="cb92d-224"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="cb92d-225">Volání `AddDefaultIdentity` je podobné volání následujícího:</span><span class="sxs-lookup"><span data-stu-id="cb92d-225">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="cb92d-226">Další informace najdete v tématu [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="cb92d-226">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="cb92d-227">Vytvoření webové aplikace s ověřováním</span><span class="sxs-lookup"><span data-stu-id="cb92d-227">Create a Web app with authentication</span></span>

<span data-ttu-id="cb92d-228">Vytvořte ASP.NET Core projekt webové aplikace s jednotlivými uživatelskými účty.</span><span class="sxs-lookup"><span data-stu-id="cb92d-228">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cb92d-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb92d-229">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cb92d-230">Vyberte **soubor** > **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="cb92d-230">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="cb92d-231">Vyberte **ASP.NET Core webové aplikace**.</span><span class="sxs-lookup"><span data-stu-id="cb92d-231">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="cb92d-232">Pojmenujte projekt **WebApp1** tak, aby měl stejný obor názvů jako projekt ke stažení.</span><span class="sxs-lookup"><span data-stu-id="cb92d-232">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="cb92d-233">Klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="cb92d-233">Click **OK**.</span></span>
* <span data-ttu-id="cb92d-234">Vyberte **webovou aplikaci**ASP.NET Core a pak vyberte **změnit ověřování**.</span><span class="sxs-lookup"><span data-stu-id="cb92d-234">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="cb92d-235">Vyberte **jednotlivé uživatelské účty** a klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="cb92d-235">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cb92d-236">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cb92d-236">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="cb92d-237">Vygenerovaný projekt poskytuje [ASP.NET Core Identity ](xref:security/authentication/identity) jako [ Razor knihovnu tříd](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="cb92d-237">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="cb92d-238">Identity Razor Knihovna tříd zpřístupňuje koncové body s `Identity` oblastí.</span><span class="sxs-lookup"><span data-stu-id="cb92d-238">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="cb92d-239">Příklad:</span><span class="sxs-lookup"><span data-stu-id="cb92d-239">For example:</span></span>

* <span data-ttu-id="cb92d-240">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="cb92d-240">/Identity/Account/Login</span></span>
* <span data-ttu-id="cb92d-241">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="cb92d-241">/Identity/Account/Logout</span></span>
* <span data-ttu-id="cb92d-242">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="cb92d-242">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="cb92d-243">Použít migrace</span><span class="sxs-lookup"><span data-stu-id="cb92d-243">Apply migrations</span></span>

<span data-ttu-id="cb92d-244">Použijte migrace k inicializaci databáze.</span><span class="sxs-lookup"><span data-stu-id="cb92d-244">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cb92d-245">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb92d-245">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cb92d-246">Spusťte následující příkaz v konzole správce balíčků (PMC):</span><span class="sxs-lookup"><span data-stu-id="cb92d-246">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="cb92d-247">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cb92d-247">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="cb92d-248">Registrace a přihlášení testu</span><span class="sxs-lookup"><span data-stu-id="cb92d-248">Test Register and Login</span></span>

<span data-ttu-id="cb92d-249">Spusťte aplikaci a zaregistrujte uživatele.</span><span class="sxs-lookup"><span data-stu-id="cb92d-249">Run the app and register a user.</span></span> <span data-ttu-id="cb92d-250">V závislosti na velikosti obrazovky možná budete muset vybrat přepínač navigace a zobrazit tak odkazy na **registraci** a **přihlášení** .</span><span class="sxs-lookup"><span data-stu-id="cb92d-250">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="cb92d-251">Konfigurace Identity služeb</span><span class="sxs-lookup"><span data-stu-id="cb92d-251">Configure Identity services</span></span>

<span data-ttu-id="cb92d-252">Služby jsou přidány do `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cb92d-252">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="cb92d-253">Typický vzor je zavolat všechny `Add{Service}` metody a pak zavolat všechny `services.Configure{Service}` metody.</span><span class="sxs-lookup"><span data-stu-id="cb92d-253">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="cb92d-254">Předchozí kód nakonfiguruje Identity s výchozími hodnotami možností.</span><span class="sxs-lookup"><span data-stu-id="cb92d-254">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="cb92d-255">Služby jsou zpřístupněny aplikaci prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cb92d-255">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

Identity<span data-ttu-id="cb92d-256">je povolen voláním [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="cb92d-256"> is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="cb92d-257">`UseAuthentication`přidá [middleware](xref:fundamentals/middleware/index) ověřování do kanálu požadavků.</span><span class="sxs-lookup"><span data-stu-id="cb92d-257">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="cb92d-258">Další informace naleznete v tématu [Třída IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) a [spuštění aplikace](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="cb92d-258">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="cb92d-259">Registrace, přihlášení a odhlášení uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="cb92d-259">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="cb92d-260">Použijte [identitu uživatelského rozhraní do Razor projektu s pokyny pro autorizaci](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) a vygenerujte kód uvedený v této části.</span><span class="sxs-lookup"><span data-stu-id="cb92d-260">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cb92d-261">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb92d-261">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cb92d-262">Přidejte soubory registru, přihlášení a odhlášení.</span><span class="sxs-lookup"><span data-stu-id="cb92d-262">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cb92d-263">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cb92d-263">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="cb92d-264">Pokud jste vytvořili projekt s názvem **WebApp1**, spusťte následující příkazy.</span><span class="sxs-lookup"><span data-stu-id="cb92d-264">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="cb92d-265">Jinak použijte správný obor názvů pro `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="cb92d-265">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="cb92d-266">PowerShell používá jako oddělovač příkazů středník.</span><span class="sxs-lookup"><span data-stu-id="cb92d-266">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="cb92d-267">Při použití prostředí PowerShell, oddělte středníky v seznamu souborů nebo vložte seznam souborů do dvojitých uvozovek, jak ukazuje předchozí příklad.</span><span class="sxs-lookup"><span data-stu-id="cb92d-267">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="cb92d-268">Ověřit registraci</span><span class="sxs-lookup"><span data-stu-id="cb92d-268">Examine Register</span></span>

<span data-ttu-id="cb92d-269">Když uživatel klikne na odkaz **zaregistrovat** , `RegisterModel.OnPostAsync` je akce vyvolána.</span><span class="sxs-lookup"><span data-stu-id="cb92d-269">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="cb92d-270">Uživatel je vytvořen pomocí [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) na `_userManager` objekt:</span><span class="sxs-lookup"><span data-stu-id="cb92d-270">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="cb92d-271">Pokud byl uživatel vytvořen úspěšně, uživatel je přihlášen voláním `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="cb92d-271">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="cb92d-272">**Poznámka:** Postup, jak zabránit okamžitému přihlášení při registraci, najdete v tématu [potvrzení účtu](xref:security/authentication/accconfirm#prevent-login-at-registration) .</span><span class="sxs-lookup"><span data-stu-id="cb92d-272">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="cb92d-273">Přihlášení</span><span class="sxs-lookup"><span data-stu-id="cb92d-273">Log in</span></span>

<span data-ttu-id="cb92d-274">Přihlašovací formulář se zobrazí v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="cb92d-274">The Login form is displayed when:</span></span>

* <span data-ttu-id="cb92d-275">Je vybrán odkaz **Přihlásit** se.</span><span class="sxs-lookup"><span data-stu-id="cb92d-275">The **Log in** link is selected.</span></span>
* <span data-ttu-id="cb92d-276">Uživatel se pokusí získat přístup ke stránce s omezením, že nemá oprávnění k přístupu, **nebo** když ho systém neověřil.</span><span class="sxs-lookup"><span data-stu-id="cb92d-276">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="cb92d-277">Při odeslání formuláře na přihlašovací stránce se `OnPostAsync` zavolá akce.</span><span class="sxs-lookup"><span data-stu-id="cb92d-277">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="cb92d-278">`PasswordSignInAsync`je volána u `_signInManager` objektu.</span><span class="sxs-lookup"><span data-stu-id="cb92d-278">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="cb92d-279">Informace o tom, jak provádět autorizační rozhodnutí, najdete v tématu <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="cb92d-279">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="cb92d-280">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="cb92d-280">Log out</span></span>

<span data-ttu-id="cb92d-281">Odkaz **odhlášení** vyvolá `LogoutModel.OnPost` akci.</span><span class="sxs-lookup"><span data-stu-id="cb92d-281">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="cb92d-282">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) vymaže deklarace identity uživatele uložené v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="cb92d-282">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="cb92d-283">Příspěvek je zadán ve *stránkách/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="cb92d-283">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="cb92d-284">NapadenIdentity</span><span class="sxs-lookup"><span data-stu-id="cb92d-284">Test Identity</span></span>

<span data-ttu-id="cb92d-285">Výchozí šablony webového projektu umožňují anonymní přístup k domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="cb92d-285">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="cb92d-286">Chcete-li otestovat Identity , přidejte [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) na stránku soukromí.</span><span class="sxs-lookup"><span data-stu-id="cb92d-286">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="cb92d-287">Pokud jste přihlášeni, odhlaste se. Spusťte aplikaci a vyberte odkaz na **ochranu osobních údajů** .</span><span class="sxs-lookup"><span data-stu-id="cb92d-287">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="cb92d-288">Budete přesměrováni na přihlašovací stránku.</span><span class="sxs-lookup"><span data-stu-id="cb92d-288">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="cb92d-289">OknoIdentity</span><span class="sxs-lookup"><span data-stu-id="cb92d-289">Explore Identity</span></span>

<span data-ttu-id="cb92d-290">Podrobněji prozkoumat Identity :</span><span class="sxs-lookup"><span data-stu-id="cb92d-290">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="cb92d-291">Vytvořit úplný zdroj uživatelského rozhraní identity</span><span class="sxs-lookup"><span data-stu-id="cb92d-291">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="cb92d-292">Projděte si zdroj každé stránky a proveďte krok pomocí ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="cb92d-292">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="cb92d-293">Konstrukční</span><span class="sxs-lookup"><span data-stu-id="cb92d-293"> Components</span></span>

<span data-ttu-id="cb92d-294">Všechny Identity závislé balíčky NuGet jsou součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="cb92d-294">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="cb92d-295">Primární balíček pro Identity je [Microsoft. AspNetCore Identity .](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="cb92d-295">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="cb92d-296">Tento balíček obsahuje základní sadu rozhraní pro ASP.NET Core Identity a je součástí nástroje `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="cb92d-296">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="cb92d-297">Migrace na ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="cb92d-297">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="cb92d-298">Další informace a pokyny k migraci stávajícího Identity úložiště najdete v tématu [migrace ověřování a Identity ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="cb92d-298">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="cb92d-299">Nastavení síly hesla</span><span class="sxs-lookup"><span data-stu-id="cb92d-299">Setting password strength</span></span>

<span data-ttu-id="cb92d-300">V části [Konfigurace](#pw) najdete ukázku, která nastavuje minimální požadavky na heslo.</span><span class="sxs-lookup"><span data-stu-id="cb92d-300">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="cb92d-301">Další kroky</span><span class="sxs-lookup"><span data-stu-id="cb92d-301">Next Steps</span></span>

* <span data-ttu-id="cb92d-302">Informace o konfiguraci pomocí SQLite najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity .</span><span class="sxs-lookup"><span data-stu-id="cb92d-302">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="cb92d-303">[KonfiguraceIdentity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="cb92d-303">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

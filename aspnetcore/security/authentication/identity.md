---
title: Úvod do identity na ASP.NET Core
author: rick-anderson
description: Použijte identitu s aplikací ASP.NET Core. Přečtěte si, jak nastavit požadavky na heslo (RequireDigit, RequiredLength, RequiredUniqueChars a další).
ms.author: riande
ms.date: 01/15/2020
uid: security/authentication/identity
ms.openlocfilehash: 164ba10c1d1e2a73ebeb8240293a58f158055699
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172534"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="d75a1-104">Úvod do identity na ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d75a1-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d75a1-105">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d75a1-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d75a1-106">ASP.NET Core identita:</span><span class="sxs-lookup"><span data-stu-id="d75a1-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="d75a1-107">Je rozhraní API, které podporuje funkce přihlášení uživatelského rozhraní (UI).</span><span class="sxs-lookup"><span data-stu-id="d75a1-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="d75a1-108">Spravuje uživatele, hesla, data profilu, role, deklarace identity, tokeny, potvrzení e-mailu a další.</span><span class="sxs-lookup"><span data-stu-id="d75a1-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="d75a1-109">Uživatelé můžou vytvořit účet s přihlašovacími informacemi uloženými v identitě nebo můžou použít externího poskytovatele přihlášení.</span><span class="sxs-lookup"><span data-stu-id="d75a1-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="d75a1-110">Mezi podporované externí zprostředkovatelé přihlášení patří [Facebook, Google, účet Microsoft a Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="d75a1-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="d75a1-111">[Zdrojový kód identity](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) je k dispozici na GitHubu.</span><span class="sxs-lookup"><span data-stu-id="d75a1-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="d75a1-112">[Identita uživatelského rozhraní](xref:security/authentication/scaffold-identity) a zobrazení vygenerovaných souborů pro kontrolu interakce šablony s identitou.</span><span class="sxs-lookup"><span data-stu-id="d75a1-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="d75a1-113">Identita je obvykle nakonfigurovaná pomocí SQL Server databáze pro ukládání uživatelských jmen, hesel a profilových dat.</span><span class="sxs-lookup"><span data-stu-id="d75a1-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="d75a1-114">Případně můžete použít jiné trvalé úložiště, například Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="d75a1-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="d75a1-115">V tomto tématu se dozvíte, jak používat identitu k registraci, přihlášení a odhlášení uživatele.</span><span class="sxs-lookup"><span data-stu-id="d75a1-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="d75a1-116">Podrobnější pokyny k vytváření aplikací, které používají identitu, najdete v části Další kroky na konci tohoto článku.</span><span class="sxs-lookup"><span data-stu-id="d75a1-116">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<span data-ttu-id="d75a1-117">[Platforma Microsoft Identity](/azure/active-directory/develop/) je:</span><span class="sxs-lookup"><span data-stu-id="d75a1-117">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="d75a1-118">Vývoj platformy pro vývojáře Azure Active Directory (Azure AD).</span><span class="sxs-lookup"><span data-stu-id="d75a1-118">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="d75a1-119">Nesouvisí se ASP.NET Core identitou.</span><span class="sxs-lookup"><span data-stu-id="d75a1-119">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="d75a1-120">[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([Jak stáhnout)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d75a1-120">[View or download the sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="d75a1-121">Vytvoření webové aplikace s ověřováním</span><span class="sxs-lookup"><span data-stu-id="d75a1-121">Create a Web app with authentication</span></span>

<span data-ttu-id="d75a1-122">Vytvořte ASP.NET Core projekt webové aplikace s jednotlivými uživatelskými účty.</span><span class="sxs-lookup"><span data-stu-id="d75a1-122">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d75a1-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d75a1-123">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d75a1-124">Vyberte **soubor** > **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="d75a1-124">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="d75a1-125">Vyberte **ASP.NET Core webové aplikace**.</span><span class="sxs-lookup"><span data-stu-id="d75a1-125">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="d75a1-126">Pojmenujte projekt **WebApp1** tak, aby měl stejný obor názvů jako projekt ke stažení.</span><span class="sxs-lookup"><span data-stu-id="d75a1-126">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="d75a1-127">Klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="d75a1-127">Click **OK**.</span></span>
* <span data-ttu-id="d75a1-128">Vyberte **webovou aplikaci**ASP.NET Core a pak vyberte **změnit ověřování**.</span><span class="sxs-lookup"><span data-stu-id="d75a1-128">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="d75a1-129">Vyberte **jednotlivé uživatelské účty** a klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="d75a1-129">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="d75a1-130">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="d75a1-130">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="d75a1-131">Předchozí příkaz vytvoří webovou aplikaci Razor pomocí SQLite.</span><span class="sxs-lookup"><span data-stu-id="d75a1-131">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="d75a1-132">Pokud chcete vytvořit webovou aplikaci pomocí LocalDB, spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="d75a1-132">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="d75a1-133">Vygenerovaný projekt poskytuje [ASP.NET Coreou identitu](xref:security/authentication/identity) jako [knihovnu tříd Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="d75a1-133">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="d75a1-134">Knihovna tříd identity Razor zpřístupňuje koncové body s oblastí `Identity`.</span><span class="sxs-lookup"><span data-stu-id="d75a1-134">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="d75a1-135">Například:</span><span class="sxs-lookup"><span data-stu-id="d75a1-135">For example:</span></span>

* <span data-ttu-id="d75a1-136">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="d75a1-136">/Identity/Account/Login</span></span>
* <span data-ttu-id="d75a1-137">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="d75a1-137">/Identity/Account/Logout</span></span>
* <span data-ttu-id="d75a1-138">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="d75a1-138">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="d75a1-139">Použít migrace</span><span class="sxs-lookup"><span data-stu-id="d75a1-139">Apply migrations</span></span>

<span data-ttu-id="d75a1-140">Použijte migrace k inicializaci databáze.</span><span class="sxs-lookup"><span data-stu-id="d75a1-140">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d75a1-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d75a1-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d75a1-142">Spusťte následující příkaz v konzole správce balíčků (PMC):</span><span class="sxs-lookup"><span data-stu-id="d75a1-142">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="d75a1-143">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="d75a1-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d75a1-144">Migrace nejsou v tomto kroku nutné při použití SQLite.</span><span class="sxs-lookup"><span data-stu-id="d75a1-144">Migrations are not necessary at this step when using SQLite.</span></span> <span data-ttu-id="d75a1-145">V případě LocalDB spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="d75a1-145">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="d75a1-146">Registrace a přihlášení testu</span><span class="sxs-lookup"><span data-stu-id="d75a1-146">Test Register and Login</span></span>

<span data-ttu-id="d75a1-147">Spusťte aplikaci a zaregistrujte uživatele.</span><span class="sxs-lookup"><span data-stu-id="d75a1-147">Run the app and register a user.</span></span> <span data-ttu-id="d75a1-148">V závislosti na velikosti obrazovky možná budete muset vybrat přepínač navigace a zobrazit tak odkazy na **registraci** a **přihlášení** .</span><span class="sxs-lookup"><span data-stu-id="d75a1-148">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="d75a1-149">Konfigurace služby identity</span><span class="sxs-lookup"><span data-stu-id="d75a1-149">Configure Identity services</span></span>

<span data-ttu-id="d75a1-150">Služby se přidávají v `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d75a1-150">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="d75a1-151">Typický vzor je zavolat všechny metody `Add{Service}` a potom zavolat všechny metody `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="d75a1-151">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="d75a1-152">Předchozí zvýrazněný kód nakonfiguruje identitu s výchozími hodnotami možností.</span><span class="sxs-lookup"><span data-stu-id="d75a1-152">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="d75a1-153">Služby jsou zpřístupněny aplikaci prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d75a1-153">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="d75a1-154">Identita je povolena voláním <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span><span class="sxs-lookup"><span data-stu-id="d75a1-154">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="d75a1-155">`UseAuthentication` do kanálu požadavků přidá [middleware](xref:fundamentals/middleware/index) ověřování.</span><span class="sxs-lookup"><span data-stu-id="d75a1-155">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="d75a1-156">Aplikace generovaná šablonou nepoužívá [autorizaci](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="d75a1-156">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="d75a1-157">`app.UseAuthorization` je součástí, aby se zajistilo, že je přidaný do správného pořadí, aby aplikace přidala autorizaci.</span><span class="sxs-lookup"><span data-stu-id="d75a1-157">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="d75a1-158">`UseRouting`, `UseAuthentication`, `UseAuthorization`a `UseEndpoints` musí být volány v pořadí uvedeném v předchozím kódu.</span><span class="sxs-lookup"><span data-stu-id="d75a1-158">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="d75a1-159">Další informace o `IdentityOptions` a `Startup`najdete v tématu <xref:Microsoft.AspNetCore.Identity.IdentityOptions> a [spuštění aplikace](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="d75a1-159">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="d75a1-160">Registrace, přihlášení a odhlášení uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="d75a1-160">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d75a1-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d75a1-161">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d75a1-162">Přidejte soubory registru, přihlášení a odhlášení.</span><span class="sxs-lookup"><span data-stu-id="d75a1-162">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="d75a1-163">Použijte [identitu uživatelského rozhraní do projektu Razor s pokyny k autorizaci](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) a vygenerujte kód uvedený v této části.</span><span class="sxs-lookup"><span data-stu-id="d75a1-163">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="d75a1-164">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="d75a1-164">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d75a1-165">Pokud jste vytvořili projekt s názvem **WebApp1**, spusťte následující příkazy.</span><span class="sxs-lookup"><span data-stu-id="d75a1-165">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="d75a1-166">Jinak použijte správný obor názvů pro `ApplicationDbContext`:</span><span class="sxs-lookup"><span data-stu-id="d75a1-166">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="d75a1-167">PowerShell používá jako oddělovač příkazů středník.</span><span class="sxs-lookup"><span data-stu-id="d75a1-167">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="d75a1-168">Při použití prostředí PowerShell, oddělte středníky v seznamu souborů nebo vložte seznam souborů do dvojitých uvozovek, jak ukazuje předchozí příklad.</span><span class="sxs-lookup"><span data-stu-id="d75a1-168">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="d75a1-169">Další informace o identitě uživatelského rozhraní naleznete v tématu [Identita uživatelského rozhraní do projektu Razor s autorizací](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="d75a1-169">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="d75a1-170">Ověřit registraci</span><span class="sxs-lookup"><span data-stu-id="d75a1-170">Examine Register</span></span>

<span data-ttu-id="d75a1-171">Když uživatel klikne na odkaz **zaregistrovat** , je vyvolána akce `RegisterModel.OnPostAsync`.</span><span class="sxs-lookup"><span data-stu-id="d75a1-171">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="d75a1-172">Uživatel je vytvořen pomocí [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) na objektu `_userManager`.</span><span class="sxs-lookup"><span data-stu-id="d75a1-172">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="d75a1-173">`_userManager` poskytuje vkládání závislostí):</span><span class="sxs-lookup"><span data-stu-id="d75a1-173">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="d75a1-174">Pokud byl uživatel vytvořen úspěšně, uživatel je přihlášen voláním `_signInManager.SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="d75a1-174">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="d75a1-175">Postup, jak zabránit okamžitému přihlášení při registraci, najdete v tématu [potvrzení účtu](xref:security/authentication/accconfirm#prevent-login-at-registration) .</span><span class="sxs-lookup"><span data-stu-id="d75a1-175">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="d75a1-176">Přihlásit se</span><span class="sxs-lookup"><span data-stu-id="d75a1-176">Log in</span></span>

<span data-ttu-id="d75a1-177">Přihlašovací formulář se zobrazí v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="d75a1-177">The Login form is displayed when:</span></span>

* <span data-ttu-id="d75a1-178">Je vybrán odkaz **Přihlásit** se.</span><span class="sxs-lookup"><span data-stu-id="d75a1-178">The **Log in** link is selected.</span></span>
* <span data-ttu-id="d75a1-179">Uživatel se pokusí získat přístup ke stránce s omezením, že nemá oprávnění k přístupu, **nebo** když ho systém neověřil.</span><span class="sxs-lookup"><span data-stu-id="d75a1-179">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="d75a1-180">Při odeslání formuláře na přihlašovací stránce se zavolá akce `OnPostAsync`.</span><span class="sxs-lookup"><span data-stu-id="d75a1-180">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="d75a1-181">`PasswordSignInAsync` se volá na objekt `_signInManager` (poskytnutý vkládáním závislostí).</span><span class="sxs-lookup"><span data-stu-id="d75a1-181">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="d75a1-182">Základní `Controller` třída zpřístupňuje vlastnost `User`, ke které lze dostat z metod kontroleru.</span><span class="sxs-lookup"><span data-stu-id="d75a1-182">The base `Controller` class exposes a `User` property that can be accessed from controller methods.</span></span> <span data-ttu-id="d75a1-183">Můžete například vytvořit výčet `User.Claims` a provést autorizační rozhodnutí.</span><span class="sxs-lookup"><span data-stu-id="d75a1-183">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="d75a1-184">Další informace naleznete v tématu <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="d75a1-184">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="d75a1-185">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="d75a1-185">Log out</span></span>

<span data-ttu-id="d75a1-186">Odkaz **Odhlásit** se vyvolá `LogoutModel.OnPost` akci.</span><span class="sxs-lookup"><span data-stu-id="d75a1-186">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="d75a1-187">V předchozím kódu musí být kód `return RedirectToPage();` přesměrování, aby prohlížeč prováděl novou žádost a identita pro uživatele byla aktualizována.</span><span class="sxs-lookup"><span data-stu-id="d75a1-187">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="d75a1-188">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) vymaže deklarace identity uživatele uložené v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="d75a1-188">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="d75a1-189">Příspěvek je zadán ve *stránkách/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d75a1-189">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="d75a1-190">Test identity</span><span class="sxs-lookup"><span data-stu-id="d75a1-190">Test Identity</span></span>

<span data-ttu-id="d75a1-191">Výchozí šablony webového projektu umožňují anonymní přístup k domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="d75a1-191">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="d75a1-192">K otestování identity přidejte [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span><span class="sxs-lookup"><span data-stu-id="d75a1-192">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="d75a1-193">Pokud jste přihlášeni, odhlaste se. Spusťte aplikaci a vyberte odkaz na **ochranu osobních údajů** .</span><span class="sxs-lookup"><span data-stu-id="d75a1-193">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="d75a1-194">Budete přesměrováni na přihlašovací stránku.</span><span class="sxs-lookup"><span data-stu-id="d75a1-194">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="d75a1-195">Prozkoumat identitu</span><span class="sxs-lookup"><span data-stu-id="d75a1-195">Explore Identity</span></span>

<span data-ttu-id="d75a1-196">Podrobněji prozkoumat identitu:</span><span class="sxs-lookup"><span data-stu-id="d75a1-196">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="d75a1-197">Vytvořit úplný zdroj uživatelského rozhraní identity</span><span class="sxs-lookup"><span data-stu-id="d75a1-197">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="d75a1-198">Projděte si zdroj každé stránky a proveďte krok pomocí ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="d75a1-198">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="d75a1-199">Komponenty identity</span><span class="sxs-lookup"><span data-stu-id="d75a1-199">Identity Components</span></span>

<span data-ttu-id="d75a1-200">Všechny balíčky NuGet závislé na identitách jsou součástí [ASP.NET Core sdílené rozhraní](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="d75a1-200">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="d75a1-201">Primárním balíčkem identity je [Microsoft. AspNetCore. identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="d75a1-201">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="d75a1-202">Tento balíček obsahuje základní sadu rozhraní pro ASP.NET Core identitu a je zahrnutý v `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="d75a1-202">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="d75a1-203">Migrace na ASP.NET Core identity</span><span class="sxs-lookup"><span data-stu-id="d75a1-203">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="d75a1-204">Další informace a pokyny k migraci stávajícího úložiště identit najdete v tématu [migrace ověřování a identity](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="d75a1-204">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="d75a1-205">Nastavení síly hesla</span><span class="sxs-lookup"><span data-stu-id="d75a1-205">Setting password strength</span></span>

<span data-ttu-id="d75a1-206">V části [Konfigurace](#pw) najdete ukázku, která nastavuje minimální požadavky na heslo.</span><span class="sxs-lookup"><span data-stu-id="d75a1-206">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="d75a1-207">AddDefaultIdentity a AddIdentity</span><span class="sxs-lookup"><span data-stu-id="d75a1-207">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="d75a1-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> byla představena v ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="d75a1-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="d75a1-209">Volání `AddDefaultIdentity` je podobné volání následujícího:</span><span class="sxs-lookup"><span data-stu-id="d75a1-209">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="d75a1-210">Další informace najdete v tématu [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="d75a1-210">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="d75a1-211">Zabránit publikování prostředků statické identity</span><span class="sxs-lookup"><span data-stu-id="d75a1-211">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="d75a1-212">Pokud chcete zabránit publikování statických prostředků identity (šablony stylů a souborů JavaScriptu pro uživatelské rozhraní identity) do kořenového adresáře webu, přidejte následující vlastnost `ResolveStaticWebAssetsInputsDependsOn` a `RemoveIdentityAssets` cílit do souboru projektu aplikace:</span><span class="sxs-lookup"><span data-stu-id="d75a1-212">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

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

## <a name="next-steps"></a><span data-ttu-id="d75a1-213">Další kroky</span><span class="sxs-lookup"><span data-stu-id="d75a1-213">Next Steps</span></span>

* <span data-ttu-id="d75a1-214">Informace o konfiguraci identity pomocí SQLite najdete v [tomto problému GitHubu](https://github.com/aspnet/AspNetCore.Docs/issues/5131) .</span><span class="sxs-lookup"><span data-stu-id="d75a1-214">See [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="d75a1-215">Konfigurace systému Identity</span><span class="sxs-lookup"><span data-stu-id="d75a1-215">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d75a1-216">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d75a1-216">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d75a1-217">ASP.NET Core identity je systém členství, který do ASP.NET Core aplikací přidává funkce přihlášení.</span><span class="sxs-lookup"><span data-stu-id="d75a1-217">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="d75a1-218">Uživatelé můžou vytvořit účet s přihlašovacími informacemi uloženými v identitě nebo můžou použít externího poskytovatele přihlášení.</span><span class="sxs-lookup"><span data-stu-id="d75a1-218">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="d75a1-219">Mezi podporované externí zprostředkovatelé přihlášení patří [Facebook, Google, účet Microsoft a Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="d75a1-219">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="d75a1-220">Identitu můžete nakonfigurovat pomocí SQL Server databáze pro ukládání uživatelských jmen, hesel a profilových dat.</span><span class="sxs-lookup"><span data-stu-id="d75a1-220">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="d75a1-221">Případně můžete použít jiné trvalé úložiště, například Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="d75a1-221">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="d75a1-222">[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([Jak stáhnout)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d75a1-222">[View or download the sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="d75a1-223">V tomto tématu se dozvíte, jak používat identitu k registraci, přihlášení a odhlášení uživatele.</span><span class="sxs-lookup"><span data-stu-id="d75a1-223">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="d75a1-224">Podrobnější pokyny k vytváření aplikací, které používají identitu, najdete v části Další kroky na konci tohoto článku.</span><span class="sxs-lookup"><span data-stu-id="d75a1-224">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="d75a1-225">AddDefaultIdentity a AddIdentity</span><span class="sxs-lookup"><span data-stu-id="d75a1-225">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="d75a1-226"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> byla představena v ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="d75a1-226"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="d75a1-227">Volání `AddDefaultIdentity` je podobné volání následujícího:</span><span class="sxs-lookup"><span data-stu-id="d75a1-227">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="d75a1-228">Další informace najdete v tématu [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="d75a1-228">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="d75a1-229">Vytvoření webové aplikace s ověřováním</span><span class="sxs-lookup"><span data-stu-id="d75a1-229">Create a Web app with authentication</span></span>

<span data-ttu-id="d75a1-230">Vytvořte ASP.NET Core projekt webové aplikace s jednotlivými uživatelskými účty.</span><span class="sxs-lookup"><span data-stu-id="d75a1-230">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d75a1-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d75a1-231">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d75a1-232">Vyberte **soubor** > **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="d75a1-232">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="d75a1-233">Vyberte **ASP.NET Core webové aplikace**.</span><span class="sxs-lookup"><span data-stu-id="d75a1-233">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="d75a1-234">Pojmenujte projekt **WebApp1** tak, aby měl stejný obor názvů jako projekt ke stažení.</span><span class="sxs-lookup"><span data-stu-id="d75a1-234">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="d75a1-235">Klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="d75a1-235">Click **OK**.</span></span>
* <span data-ttu-id="d75a1-236">Vyberte **webovou aplikaci**ASP.NET Core a pak vyberte **změnit ověřování**.</span><span class="sxs-lookup"><span data-stu-id="d75a1-236">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="d75a1-237">Vyberte **jednotlivé uživatelské účty** a klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="d75a1-237">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="d75a1-238">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="d75a1-238">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="d75a1-239">Vygenerovaný projekt poskytuje [ASP.NET Coreou identitu](xref:security/authentication/identity) jako [knihovnu tříd Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="d75a1-239">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="d75a1-240">Knihovna tříd identity Razor zpřístupňuje koncové body s oblastí `Identity`.</span><span class="sxs-lookup"><span data-stu-id="d75a1-240">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="d75a1-241">Například:</span><span class="sxs-lookup"><span data-stu-id="d75a1-241">For example:</span></span>

* <span data-ttu-id="d75a1-242">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="d75a1-242">/Identity/Account/Login</span></span>
* <span data-ttu-id="d75a1-243">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="d75a1-243">/Identity/Account/Logout</span></span>
* <span data-ttu-id="d75a1-244">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="d75a1-244">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="d75a1-245">Použít migrace</span><span class="sxs-lookup"><span data-stu-id="d75a1-245">Apply migrations</span></span>

<span data-ttu-id="d75a1-246">Použijte migrace k inicializaci databáze.</span><span class="sxs-lookup"><span data-stu-id="d75a1-246">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d75a1-247">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d75a1-247">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d75a1-248">Spusťte následující příkaz v konzole správce balíčků (PMC):</span><span class="sxs-lookup"><span data-stu-id="d75a1-248">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="d75a1-249">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="d75a1-249">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="d75a1-250">Registrace a přihlášení testu</span><span class="sxs-lookup"><span data-stu-id="d75a1-250">Test Register and Login</span></span>

<span data-ttu-id="d75a1-251">Spusťte aplikaci a zaregistrujte uživatele.</span><span class="sxs-lookup"><span data-stu-id="d75a1-251">Run the app and register a user.</span></span> <span data-ttu-id="d75a1-252">V závislosti na velikosti obrazovky možná budete muset vybrat přepínač navigace a zobrazit tak odkazy na **registraci** a **přihlášení** .</span><span class="sxs-lookup"><span data-stu-id="d75a1-252">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="d75a1-253">Konfigurace služby identity</span><span class="sxs-lookup"><span data-stu-id="d75a1-253">Configure Identity services</span></span>

<span data-ttu-id="d75a1-254">Služby se přidávají v `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d75a1-254">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="d75a1-255">Typický vzor je zavolat všechny metody `Add{Service}` a potom zavolat všechny metody `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="d75a1-255">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="d75a1-256">Předchozí kód nakonfiguruje identitu s výchozími hodnotami možností.</span><span class="sxs-lookup"><span data-stu-id="d75a1-256">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="d75a1-257">Služby jsou zpřístupněny aplikaci prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d75a1-257">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="d75a1-258">Identita je povolena voláním [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="d75a1-258">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="d75a1-259">`UseAuthentication` do kanálu požadavků přidá [middleware](xref:fundamentals/middleware/index) ověřování.</span><span class="sxs-lookup"><span data-stu-id="d75a1-259">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="d75a1-260">Další informace naleznete v tématu [Třída IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) a [spuštění aplikace](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="d75a1-260">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="d75a1-261">Registrace, přihlášení a odhlášení uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="d75a1-261">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="d75a1-262">Použijte [identitu uživatelského rozhraní do projektu Razor s pokyny k autorizaci](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) a vygenerujte kód uvedený v této části.</span><span class="sxs-lookup"><span data-stu-id="d75a1-262">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d75a1-263">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d75a1-263">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d75a1-264">Přidejte soubory registru, přihlášení a odhlášení.</span><span class="sxs-lookup"><span data-stu-id="d75a1-264">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="d75a1-265">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="d75a1-265">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d75a1-266">Pokud jste vytvořili projekt s názvem **WebApp1**, spusťte následující příkazy.</span><span class="sxs-lookup"><span data-stu-id="d75a1-266">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="d75a1-267">Jinak použijte správný obor názvů pro `ApplicationDbContext`:</span><span class="sxs-lookup"><span data-stu-id="d75a1-267">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="d75a1-268">PowerShell používá jako oddělovač příkazů středník.</span><span class="sxs-lookup"><span data-stu-id="d75a1-268">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="d75a1-269">Při použití prostředí PowerShell, oddělte středníky v seznamu souborů nebo vložte seznam souborů do dvojitých uvozovek, jak ukazuje předchozí příklad.</span><span class="sxs-lookup"><span data-stu-id="d75a1-269">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="d75a1-270">Ověřit registraci</span><span class="sxs-lookup"><span data-stu-id="d75a1-270">Examine Register</span></span>

<span data-ttu-id="d75a1-271">Když uživatel klikne na odkaz **zaregistrovat** , je vyvolána akce `RegisterModel.OnPostAsync`.</span><span class="sxs-lookup"><span data-stu-id="d75a1-271">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="d75a1-272">Uživatel je vytvořen pomocí [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) na objektu `_userManager`.</span><span class="sxs-lookup"><span data-stu-id="d75a1-272">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="d75a1-273">`_userManager` poskytuje vkládání závislostí):</span><span class="sxs-lookup"><span data-stu-id="d75a1-273">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="d75a1-274">Pokud byl uživatel vytvořen úspěšně, uživatel je přihlášen voláním `_signInManager.SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="d75a1-274">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="d75a1-275">**Poznámka:** Postup, jak zabránit okamžitému přihlášení při registraci, najdete v tématu [potvrzení účtu](xref:security/authentication/accconfirm#prevent-login-at-registration) .</span><span class="sxs-lookup"><span data-stu-id="d75a1-275">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="d75a1-276">Přihlásit se</span><span class="sxs-lookup"><span data-stu-id="d75a1-276">Log in</span></span>

<span data-ttu-id="d75a1-277">Přihlašovací formulář se zobrazí v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="d75a1-277">The Login form is displayed when:</span></span>

* <span data-ttu-id="d75a1-278">Je vybrán odkaz **Přihlásit** se.</span><span class="sxs-lookup"><span data-stu-id="d75a1-278">The **Log in** link is selected.</span></span>
* <span data-ttu-id="d75a1-279">Uživatel se pokusí získat přístup ke stránce s omezením, že nemá oprávnění k přístupu, **nebo** když ho systém neověřil.</span><span class="sxs-lookup"><span data-stu-id="d75a1-279">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="d75a1-280">Při odeslání formuláře na přihlašovací stránce se zavolá akce `OnPostAsync`.</span><span class="sxs-lookup"><span data-stu-id="d75a1-280">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="d75a1-281">`PasswordSignInAsync` se volá na objekt `_signInManager` (poskytnutý vkládáním závislostí).</span><span class="sxs-lookup"><span data-stu-id="d75a1-281">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="d75a1-282">Základní `Controller` třída zpřístupňuje vlastnost `User`, ke které máte přístup z metod kontroleru.</span><span class="sxs-lookup"><span data-stu-id="d75a1-282">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="d75a1-283">Můžete například vytvořit výčet `User.Claims` a provést autorizační rozhodnutí.</span><span class="sxs-lookup"><span data-stu-id="d75a1-283">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="d75a1-284">Další informace naleznete v tématu <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="d75a1-284">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="d75a1-285">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="d75a1-285">Log out</span></span>

<span data-ttu-id="d75a1-286">Odkaz **Odhlásit** se vyvolá `LogoutModel.OnPost` akci.</span><span class="sxs-lookup"><span data-stu-id="d75a1-286">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="d75a1-287">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) vymaže deklarace identity uživatele uložené v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="d75a1-287">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="d75a1-288">Příspěvek je zadán ve *stránkách/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d75a1-288">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="d75a1-289">Test identity</span><span class="sxs-lookup"><span data-stu-id="d75a1-289">Test Identity</span></span>

<span data-ttu-id="d75a1-290">Výchozí šablony webového projektu umožňují anonymní přístup k domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="d75a1-290">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="d75a1-291">Chcete-li otestovat identitu, přidejte [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) na stránku ochrany osobních údajů.</span><span class="sxs-lookup"><span data-stu-id="d75a1-291">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="d75a1-292">Pokud jste přihlášeni, odhlaste se. Spusťte aplikaci a vyberte odkaz na **ochranu osobních údajů** .</span><span class="sxs-lookup"><span data-stu-id="d75a1-292">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="d75a1-293">Budete přesměrováni na přihlašovací stránku.</span><span class="sxs-lookup"><span data-stu-id="d75a1-293">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="d75a1-294">Prozkoumat identitu</span><span class="sxs-lookup"><span data-stu-id="d75a1-294">Explore Identity</span></span>

<span data-ttu-id="d75a1-295">Podrobněji prozkoumat identitu:</span><span class="sxs-lookup"><span data-stu-id="d75a1-295">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="d75a1-296">Vytvořit úplný zdroj uživatelského rozhraní identity</span><span class="sxs-lookup"><span data-stu-id="d75a1-296">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="d75a1-297">Projděte si zdroj každé stránky a proveďte krok pomocí ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="d75a1-297">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="d75a1-298">Komponenty identity</span><span class="sxs-lookup"><span data-stu-id="d75a1-298">Identity Components</span></span>

<span data-ttu-id="d75a1-299">Všechny balíčky NuGet závislé na identitě jsou zahrnuté ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="d75a1-299">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="d75a1-300">Primárním balíčkem identity je [Microsoft. AspNetCore. identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="d75a1-300">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="d75a1-301">Tento balíček obsahuje základní sadu rozhraní pro ASP.NET Core identitu a je zahrnutý v `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="d75a1-301">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="d75a1-302">Migrace na ASP.NET Core identity</span><span class="sxs-lookup"><span data-stu-id="d75a1-302">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="d75a1-303">Další informace a pokyny k migraci stávajícího úložiště identit najdete v tématu [migrace ověřování a identity](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="d75a1-303">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="d75a1-304">Nastavení síly hesla</span><span class="sxs-lookup"><span data-stu-id="d75a1-304">Setting password strength</span></span>

<span data-ttu-id="d75a1-305">V části [Konfigurace](#pw) najdete ukázku, která nastavuje minimální požadavky na heslo.</span><span class="sxs-lookup"><span data-stu-id="d75a1-305">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d75a1-306">Další kroky</span><span class="sxs-lookup"><span data-stu-id="d75a1-306">Next Steps</span></span>

* <span data-ttu-id="d75a1-307">Informace o konfiguraci identity pomocí SQLite najdete v [tomto problému GitHubu](https://github.com/aspnet/AspNetCore.Docs/issues/5131) .</span><span class="sxs-lookup"><span data-stu-id="d75a1-307">See [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="d75a1-308">Konfigurace systému Identity</span><span class="sxs-lookup"><span data-stu-id="d75a1-308">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

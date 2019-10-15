---
title: Úvod do identity na ASP.NET Core
author: rick-anderson
description: Použijte identitu s aplikací ASP.NET Core. Přečtěte si, jak nastavit požadavky na heslo (RequireDigit, RequiredLength, RequiredUniqueChars a další).
ms.author: riande
ms.date: 10/15/2019
uid: security/authentication/identity
ms.openlocfilehash: 8da13ca5f74a9c829eb8137d33af0684ff88266d
ms.sourcegitcommit: 07d98ada57f2a5f6d809d44bdad7a15013109549
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333557"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="7fc97-104">Úvod do identity na ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7fc97-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7fc97-105">Od [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7fc97-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7fc97-106">ASP.NET Core identity je systém členství, který podporuje funkce přihlášení uživatelského rozhraní (UI).</span><span class="sxs-lookup"><span data-stu-id="7fc97-106">ASP.NET Core Identity is a membership system that supports user interface (UI) login functionality.</span></span> <span data-ttu-id="7fc97-107">Uživatelé můžou vytvořit účet s přihlašovacími informacemi uloženými v identitě nebo můžou použít externího poskytovatele přihlášení.</span><span class="sxs-lookup"><span data-stu-id="7fc97-107">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="7fc97-108">Mezi podporované externí zprostředkovatelé přihlášení patří [Facebook, Google, účet Microsoft a Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="7fc97-108">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="7fc97-109">Identitu můžete nakonfigurovat pomocí SQL Server databáze pro ukládání uživatelských jmen, hesel a profilových dat.</span><span class="sxs-lookup"><span data-stu-id="7fc97-109">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="7fc97-110">Případně můžete použít jiné trvalé úložiště, například Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="7fc97-110">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="7fc97-111">V tomto tématu se dozvíte, jak používat identitu k registraci, přihlášení a odhlášení uživatele.</span><span class="sxs-lookup"><span data-stu-id="7fc97-111">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="7fc97-112">Podrobnější pokyny k vytváření aplikací, které používají identitu, najdete v části Další kroky na konci tohoto článku.</span><span class="sxs-lookup"><span data-stu-id="7fc97-112">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="7fc97-113">[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([Jak stáhnout)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="7fc97-113">[View or download the sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="7fc97-114">Vytvoření webové aplikace s ověřováním</span><span class="sxs-lookup"><span data-stu-id="7fc97-114">Create a Web app with authentication</span></span>

<span data-ttu-id="7fc97-115">Vytvořte ASP.NET Core projekt webové aplikace s jednotlivými uživatelskými účty.</span><span class="sxs-lookup"><span data-stu-id="7fc97-115">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7fc97-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7fc97-116">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7fc97-117">Vyberte **soubor** > **Nový** **projekt**>.</span><span class="sxs-lookup"><span data-stu-id="7fc97-117">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="7fc97-118">Vyberte **ASP.NET Core webové aplikace**.</span><span class="sxs-lookup"><span data-stu-id="7fc97-118">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="7fc97-119">Pojmenujte projekt **WebApp1** tak, aby měl stejný obor názvů jako projekt ke stažení.</span><span class="sxs-lookup"><span data-stu-id="7fc97-119">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="7fc97-120">Klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="7fc97-120">Click **OK**.</span></span>
* <span data-ttu-id="7fc97-121">Vyberte **webovou aplikaci**ASP.NET Core a pak vyberte **změnit ověřování**.</span><span class="sxs-lookup"><span data-stu-id="7fc97-121">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="7fc97-122">Vyberte **jednotlivé uživatelské účty** a klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="7fc97-122">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="7fc97-123">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="7fc97-123">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="7fc97-124">Předchozí příkaz vytvoří webovou aplikaci Razor pomocí SQLite.</span><span class="sxs-lookup"><span data-stu-id="7fc97-124">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="7fc97-125">Pokud chcete vytvořit webovou aplikaci pomocí LocalDB, spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="7fc97-125">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="7fc97-126">Vygenerovaný projekt poskytuje [ASP.NET Coreou identitu](xref:security/authentication/identity) jako [knihovnu tříd Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="7fc97-126">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="7fc97-127">Knihovna tříd identity Razor zpřístupňuje koncové body s oblastí `Identity`.</span><span class="sxs-lookup"><span data-stu-id="7fc97-127">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="7fc97-128">Příklad:</span><span class="sxs-lookup"><span data-stu-id="7fc97-128">For example:</span></span>

* <span data-ttu-id="7fc97-129">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="7fc97-129">/Identity/Account/Login</span></span>
* <span data-ttu-id="7fc97-130">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="7fc97-130">/Identity/Account/Logout</span></span>
* <span data-ttu-id="7fc97-131">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="7fc97-131">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="7fc97-132">Použít migrace</span><span class="sxs-lookup"><span data-stu-id="7fc97-132">Apply migrations</span></span>

<span data-ttu-id="7fc97-133">Použijte migrace k inicializaci databáze.</span><span class="sxs-lookup"><span data-stu-id="7fc97-133">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7fc97-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7fc97-134">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7fc97-135">Spusťte následující příkaz v konzole správce balíčků (PMC):</span><span class="sxs-lookup"><span data-stu-id="7fc97-135">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="7fc97-136">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="7fc97-136">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="7fc97-137">Migrace nejsou v tomto kroku nutné při použití SQLite.</span><span class="sxs-lookup"><span data-stu-id="7fc97-137">Migrations are not necessary at this step when using SQLite.</span></span> <span data-ttu-id="7fc97-138">V případě LocalDB spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="7fc97-138">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="7fc97-139">Registrace a přihlášení testu</span><span class="sxs-lookup"><span data-stu-id="7fc97-139">Test Register and Login</span></span>

<span data-ttu-id="7fc97-140">Spusťte aplikaci a zaregistrujte uživatele.</span><span class="sxs-lookup"><span data-stu-id="7fc97-140">Run the app and register a user.</span></span> <span data-ttu-id="7fc97-141">V závislosti na velikosti obrazovky možná budete muset vybrat přepínač navigace a zobrazit tak odkazy na **registraci** a **přihlášení** .</span><span class="sxs-lookup"><span data-stu-id="7fc97-141">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="7fc97-142">Konfigurace služby identity</span><span class="sxs-lookup"><span data-stu-id="7fc97-142">Configure Identity services</span></span>

<span data-ttu-id="7fc97-143">Služby jsou přidány do `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7fc97-143">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="7fc97-144">Typickým vzorem je zavolat všechny metody `Add{Service}` a pak zavolat všechny metody `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="7fc97-144">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="7fc97-145">Předchozí zvýrazněný kód nakonfiguruje identitu s výchozími hodnotami možností.</span><span class="sxs-lookup"><span data-stu-id="7fc97-145">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="7fc97-146">Služby jsou zpřístupněny aplikaci prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="7fc97-146">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="7fc97-147">Identita je povolena voláním <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span><span class="sxs-lookup"><span data-stu-id="7fc97-147">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="7fc97-148">`UseAuthentication` přidá do kanálu žádosti [middleware](xref:fundamentals/middleware/index) ověřování.</span><span class="sxs-lookup"><span data-stu-id="7fc97-148">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="7fc97-149">Aplikace generovaná šablonou nepoužívá [autorizaci](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="7fc97-149">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="7fc97-150">pro zajištění, že je přidání do správného pořadí nutné, aby aplikace přidala autorizaci, je zahrnutá `app.UseAuthorization`.</span><span class="sxs-lookup"><span data-stu-id="7fc97-150">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="7fc97-151">`UseRouting`, `UseAuthentication`, `UseAuthorization` a `UseEndpoints`, je nutné volat v pořadí uvedeném v předchozím kódu.</span><span class="sxs-lookup"><span data-stu-id="7fc97-151">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="7fc97-152">Další informace o `IdentityOptions` a `Startup` najdete v tématu <xref:Microsoft.AspNetCore.Identity.IdentityOptions> a [spuštění aplikace](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="7fc97-152">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="7fc97-153">Registrace, přihlášení a odhlášení uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="7fc97-153">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7fc97-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7fc97-154">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7fc97-155">Přidejte soubory registru, přihlášení a odhlášení.</span><span class="sxs-lookup"><span data-stu-id="7fc97-155">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="7fc97-156">Použijte [identitu uživatelského rozhraní do projektu Razor s pokyny k autorizaci](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) a vygenerujte kód uvedený v této části.</span><span class="sxs-lookup"><span data-stu-id="7fc97-156">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="7fc97-157">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="7fc97-157">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="7fc97-158">Pokud jste vytvořili projekt s názvem **WebApp1**, spusťte následující příkazy.</span><span class="sxs-lookup"><span data-stu-id="7fc97-158">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="7fc97-159">V opačném případě použijte pro `ApplicationDbContext` správný obor názvů:</span><span class="sxs-lookup"><span data-stu-id="7fc97-159">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="7fc97-160">PowerShell používá jako oddělovač příkazů středník.</span><span class="sxs-lookup"><span data-stu-id="7fc97-160">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="7fc97-161">Při použití prostředí PowerShell, oddělte středníky v seznamu souborů nebo vložte seznam souborů do dvojitých uvozovek, jak ukazuje předchozí příklad.</span><span class="sxs-lookup"><span data-stu-id="7fc97-161">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="7fc97-162">Další informace o identitě uživatelského rozhraní naleznete v tématu [Identita uživatelského rozhraní do projektu Razor s autorizací](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="7fc97-162">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="7fc97-163">Ověřit registraci</span><span class="sxs-lookup"><span data-stu-id="7fc97-163">Examine Register</span></span>

<span data-ttu-id="7fc97-164">Když uživatel klikne na odkaz **zaregistrovat** , je vyvolána akce `RegisterModel.OnPostAsync`.</span><span class="sxs-lookup"><span data-stu-id="7fc97-164">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="7fc97-165">Uživatel je vytvořen pomocí [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) na objektu `_userManager`.</span><span class="sxs-lookup"><span data-stu-id="7fc97-165">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="7fc97-166">`_userManager` je poskytován pomocí injektáže závislosti):</span><span class="sxs-lookup"><span data-stu-id="7fc97-166">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="7fc97-167">Pokud byl uživatel vytvořen úspěšně, uživatel je přihlášen voláním `_signInManager.SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="7fc97-167">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="7fc97-168">Postup, jak zabránit okamžitému přihlášení při registraci, najdete v tématu [potvrzení účtu](xref:security/authentication/accconfirm#prevent-login-at-registration) .</span><span class="sxs-lookup"><span data-stu-id="7fc97-168">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="7fc97-169">Přihlásit se</span><span class="sxs-lookup"><span data-stu-id="7fc97-169">Log in</span></span>

<span data-ttu-id="7fc97-170">Přihlašovací formulář se zobrazí v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="7fc97-170">The Login form is displayed when:</span></span>

* <span data-ttu-id="7fc97-171">Je vybrán odkaz **Přihlásit** se.</span><span class="sxs-lookup"><span data-stu-id="7fc97-171">The **Log in** link is selected.</span></span>
* <span data-ttu-id="7fc97-172">Uživatel se pokusí získat přístup ke stránce s omezením, že nemá oprávnění k přístupu, **nebo** když ho systém neověřil.</span><span class="sxs-lookup"><span data-stu-id="7fc97-172">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="7fc97-173">Při odeslání formuláře na přihlašovací stránce se zavolá akce `OnPostAsync`.</span><span class="sxs-lookup"><span data-stu-id="7fc97-173">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="7fc97-174">`PasswordSignInAsync` se volá u objektu `_signInManager` (poskytovaného vkládáním závislostí).</span><span class="sxs-lookup"><span data-stu-id="7fc97-174">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="7fc97-175">Základní třída `Controller` zpřístupňuje vlastnost `User`, která je k dispozici z metod kontroleru.</span><span class="sxs-lookup"><span data-stu-id="7fc97-175">The base `Controller` class exposes a `User` property that can be accessed from controller methods.</span></span> <span data-ttu-id="7fc97-176">Můžete například vytvořit výčet `User.Claims` a provést autorizační rozhodnutí.</span><span class="sxs-lookup"><span data-stu-id="7fc97-176">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="7fc97-177">Další informace najdete v tématu <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="7fc97-177">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="7fc97-178">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="7fc97-178">Log out</span></span>

<span data-ttu-id="7fc97-179">Odkaz **odhlášení** vyvolá akci `LogoutModel.OnPost`.</span><span class="sxs-lookup"><span data-stu-id="7fc97-179">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="7fc97-180">V předchozím kódu musí být kód `return RedirectToPage();` přesměrování, aby prohlížeč prováděl novou žádost a identita pro uživatele byla aktualizována.</span><span class="sxs-lookup"><span data-stu-id="7fc97-180">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="7fc97-181">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) vymaže deklarace identity uživatele uložené v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="7fc97-181">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="7fc97-182">Příspěvek je zadán ve *stránkách/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="7fc97-182">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="7fc97-183">Test identity</span><span class="sxs-lookup"><span data-stu-id="7fc97-183">Test Identity</span></span>

<span data-ttu-id="7fc97-184">Výchozí šablony webového projektu umožňují anonymní přístup k domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="7fc97-184">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="7fc97-185">Chcete-li otestovat identitu, přidejte [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span><span class="sxs-lookup"><span data-stu-id="7fc97-185">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="7fc97-186">Pokud jste přihlášeni, odhlaste se. Spusťte aplikaci a vyberte odkaz na **ochranu osobních údajů** .</span><span class="sxs-lookup"><span data-stu-id="7fc97-186">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="7fc97-187">Budete přesměrováni na přihlašovací stránku.</span><span class="sxs-lookup"><span data-stu-id="7fc97-187">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="7fc97-188">Prozkoumat identitu</span><span class="sxs-lookup"><span data-stu-id="7fc97-188">Explore Identity</span></span>

<span data-ttu-id="7fc97-189">Podrobněji prozkoumat identitu:</span><span class="sxs-lookup"><span data-stu-id="7fc97-189">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="7fc97-190">Vytvořit úplný zdroj uživatelského rozhraní identity</span><span class="sxs-lookup"><span data-stu-id="7fc97-190">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="7fc97-191">Projděte si zdroj každé stránky a proveďte krok pomocí ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="7fc97-191">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="7fc97-192">Komponenty identity</span><span class="sxs-lookup"><span data-stu-id="7fc97-192">Identity Components</span></span>

<span data-ttu-id="7fc97-193">Všechny balíčky NuGet závislé na identitách jsou součástí [ASP.NET Core sdílené rozhraní](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="7fc97-193">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="7fc97-194">Primárním balíčkem identity je [Microsoft. AspNetCore. identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="7fc97-194">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="7fc97-195">Tento balíček obsahuje základní sadu rozhraní pro ASP.NET Core identitu a je zahrnutý v `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="7fc97-195">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="7fc97-196">Migrace na ASP.NET Core identity</span><span class="sxs-lookup"><span data-stu-id="7fc97-196">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="7fc97-197">Další informace a pokyny k migraci stávajícího úložiště identit najdete v tématu [migrace ověřování a identity](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="7fc97-197">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="7fc97-198">Nastavení síly hesla</span><span class="sxs-lookup"><span data-stu-id="7fc97-198">Setting password strength</span></span>

<span data-ttu-id="7fc97-199">V části [Konfigurace](#pw) najdete ukázku, která nastavuje minimální požadavky na heslo.</span><span class="sxs-lookup"><span data-stu-id="7fc97-199">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="7fc97-200">AddDefaultIdentity a AddIdentity</span><span class="sxs-lookup"><span data-stu-id="7fc97-200">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="7fc97-201"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> byla představena v ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="7fc97-201"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="7fc97-202">Volání `AddDefaultIdentity` je podobné volání následujícího:</span><span class="sxs-lookup"><span data-stu-id="7fc97-202">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="7fc97-203">Další informace najdete v tématu [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="7fc97-203">See [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="next-steps"></a><span data-ttu-id="7fc97-204">Další kroky</span><span class="sxs-lookup"><span data-stu-id="7fc97-204">Next Steps</span></span>

* [<span data-ttu-id="7fc97-205">Konfigurace systému Identity</span><span class="sxs-lookup"><span data-stu-id="7fc97-205">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7fc97-206">Od [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7fc97-206">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7fc97-207">ASP.NET Core identity je systém členství, který do ASP.NET Core aplikací přidává funkce přihlášení.</span><span class="sxs-lookup"><span data-stu-id="7fc97-207">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="7fc97-208">Uživatelé můžou vytvořit účet s přihlašovacími informacemi uloženými v identitě nebo můžou použít externího poskytovatele přihlášení.</span><span class="sxs-lookup"><span data-stu-id="7fc97-208">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="7fc97-209">Mezi podporované externí zprostředkovatelé přihlášení patří [Facebook, Google, účet Microsoft a Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="7fc97-209">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="7fc97-210">Identitu můžete nakonfigurovat pomocí SQL Server databáze pro ukládání uživatelských jmen, hesel a profilových dat.</span><span class="sxs-lookup"><span data-stu-id="7fc97-210">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="7fc97-211">Případně můžete použít jiné trvalé úložiště, například Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="7fc97-211">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="7fc97-212">[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([Jak stáhnout)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="7fc97-212">[View or download the sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="7fc97-213">V tomto tématu se dozvíte, jak používat identitu k registraci, přihlášení a odhlášení uživatele.</span><span class="sxs-lookup"><span data-stu-id="7fc97-213">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="7fc97-214">Podrobnější pokyny k vytváření aplikací, které používají identitu, najdete v části Další kroky na konci tohoto článku.</span><span class="sxs-lookup"><span data-stu-id="7fc97-214">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="7fc97-215">AddDefaultIdentity a AddIdentity</span><span class="sxs-lookup"><span data-stu-id="7fc97-215">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="7fc97-216"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> byla představena v ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="7fc97-216"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="7fc97-217">Volání `AddDefaultIdentity` je podobné volání následujícího:</span><span class="sxs-lookup"><span data-stu-id="7fc97-217">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="7fc97-218">Další informace najdete v tématu [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="7fc97-218">See [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="7fc97-219">Vytvoření webové aplikace s ověřováním</span><span class="sxs-lookup"><span data-stu-id="7fc97-219">Create a Web app with authentication</span></span>

<span data-ttu-id="7fc97-220">Vytvořte ASP.NET Core projekt webové aplikace s jednotlivými uživatelskými účty.</span><span class="sxs-lookup"><span data-stu-id="7fc97-220">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7fc97-221">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7fc97-221">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7fc97-222">Vyberte **soubor** > **Nový** **projekt**>.</span><span class="sxs-lookup"><span data-stu-id="7fc97-222">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="7fc97-223">Vyberte **ASP.NET Core webové aplikace**.</span><span class="sxs-lookup"><span data-stu-id="7fc97-223">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="7fc97-224">Pojmenujte projekt **WebApp1** tak, aby měl stejný obor názvů jako projekt ke stažení.</span><span class="sxs-lookup"><span data-stu-id="7fc97-224">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="7fc97-225">Klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="7fc97-225">Click **OK**.</span></span>
* <span data-ttu-id="7fc97-226">Vyberte **webovou aplikaci**ASP.NET Core a pak vyberte **změnit ověřování**.</span><span class="sxs-lookup"><span data-stu-id="7fc97-226">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="7fc97-227">Vyberte **jednotlivé uživatelské účty** a klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="7fc97-227">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="7fc97-228">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="7fc97-228">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="7fc97-229">Vygenerovaný projekt poskytuje [ASP.NET Coreou identitu](xref:security/authentication/identity) jako [knihovnu tříd Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="7fc97-229">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="7fc97-230">Knihovna tříd identity Razor zpřístupňuje koncové body s oblastí `Identity`.</span><span class="sxs-lookup"><span data-stu-id="7fc97-230">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="7fc97-231">Příklad:</span><span class="sxs-lookup"><span data-stu-id="7fc97-231">For example:</span></span>

* <span data-ttu-id="7fc97-232">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="7fc97-232">/Identity/Account/Login</span></span>
* <span data-ttu-id="7fc97-233">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="7fc97-233">/Identity/Account/Logout</span></span>
* <span data-ttu-id="7fc97-234">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="7fc97-234">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="7fc97-235">Použít migrace</span><span class="sxs-lookup"><span data-stu-id="7fc97-235">Apply migrations</span></span>

<span data-ttu-id="7fc97-236">Použijte migrace k inicializaci databáze.</span><span class="sxs-lookup"><span data-stu-id="7fc97-236">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7fc97-237">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7fc97-237">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7fc97-238">Spusťte následující příkaz v konzole správce balíčků (PMC):</span><span class="sxs-lookup"><span data-stu-id="7fc97-238">Run the following command in the Package Manager Console (PMC):</span></span>

```PM> Update-Database```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="7fc97-239">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="7fc97-239">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="7fc97-240">Registrace a přihlášení testu</span><span class="sxs-lookup"><span data-stu-id="7fc97-240">Test Register and Login</span></span>

<span data-ttu-id="7fc97-241">Spusťte aplikaci a zaregistrujte uživatele.</span><span class="sxs-lookup"><span data-stu-id="7fc97-241">Run the app and register a user.</span></span> <span data-ttu-id="7fc97-242">V závislosti na velikosti obrazovky možná budete muset vybrat přepínač navigace a zobrazit tak odkazy na **registraci** a **přihlášení** .</span><span class="sxs-lookup"><span data-stu-id="7fc97-242">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="7fc97-243">Konfigurace služby identity</span><span class="sxs-lookup"><span data-stu-id="7fc97-243">Configure Identity services</span></span>

<span data-ttu-id="7fc97-244">Služby jsou přidány do `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7fc97-244">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="7fc97-245">Typickým vzorem je zavolat všechny metody `Add{Service}` a pak zavolat všechny metody `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="7fc97-245">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="7fc97-246">Předchozí kód nakonfiguruje identitu s výchozími hodnotami možností.</span><span class="sxs-lookup"><span data-stu-id="7fc97-246">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="7fc97-247">Služby jsou zpřístupněny aplikaci prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="7fc97-247">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="7fc97-248">Identita je povolena voláním [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="7fc97-248">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="7fc97-249">`UseAuthentication` přidá do kanálu žádosti [middleware](xref:fundamentals/middleware/index) ověřování.</span><span class="sxs-lookup"><span data-stu-id="7fc97-249">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="7fc97-250">Další informace naleznete v tématu [Třída IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) a [spuštění aplikace](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="7fc97-250">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="7fc97-251">Registrace, přihlášení a odhlášení uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="7fc97-251">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="7fc97-252">Použijte [identitu uživatelského rozhraní do projektu Razor s pokyny k autorizaci](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) a vygenerujte kód uvedený v této části.</span><span class="sxs-lookup"><span data-stu-id="7fc97-252">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7fc97-253">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7fc97-253">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7fc97-254">Přidejte soubory registru, přihlášení a odhlášení.</span><span class="sxs-lookup"><span data-stu-id="7fc97-254">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="7fc97-255">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="7fc97-255">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="7fc97-256">Pokud jste vytvořili projekt s názvem **WebApp1**, spusťte následující příkazy.</span><span class="sxs-lookup"><span data-stu-id="7fc97-256">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="7fc97-257">V opačném případě použijte pro `ApplicationDbContext` správný obor názvů:</span><span class="sxs-lookup"><span data-stu-id="7fc97-257">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="7fc97-258">PowerShell používá jako oddělovač příkazů středník.</span><span class="sxs-lookup"><span data-stu-id="7fc97-258">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="7fc97-259">Při použití prostředí PowerShell, oddělte středníky v seznamu souborů nebo vložte seznam souborů do dvojitých uvozovek, jak ukazuje předchozí příklad.</span><span class="sxs-lookup"><span data-stu-id="7fc97-259">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="7fc97-260">Ověřit registraci</span><span class="sxs-lookup"><span data-stu-id="7fc97-260">Examine Register</span></span>

<span data-ttu-id="7fc97-261">Když uživatel klikne na odkaz **zaregistrovat** , je vyvolána akce `RegisterModel.OnPostAsync`.</span><span class="sxs-lookup"><span data-stu-id="7fc97-261">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="7fc97-262">Uživatel je vytvořen pomocí [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) na objektu `_userManager`.</span><span class="sxs-lookup"><span data-stu-id="7fc97-262">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="7fc97-263">`_userManager` je poskytován pomocí injektáže závislosti):</span><span class="sxs-lookup"><span data-stu-id="7fc97-263">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="7fc97-264">Pokud byl uživatel vytvořen úspěšně, uživatel je přihlášen voláním `_signInManager.SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="7fc97-264">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="7fc97-265">**Poznámka:** Postup, jak zabránit okamžitému přihlášení při registraci, najdete v tématu [potvrzení účtu](xref:security/authentication/accconfirm#prevent-login-at-registration) .</span><span class="sxs-lookup"><span data-stu-id="7fc97-265">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="7fc97-266">Přihlásit se</span><span class="sxs-lookup"><span data-stu-id="7fc97-266">Log in</span></span>

<span data-ttu-id="7fc97-267">Přihlašovací formulář se zobrazí v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="7fc97-267">The Login form is displayed when:</span></span>

* <span data-ttu-id="7fc97-268">Je vybrán odkaz **Přihlásit** se.</span><span class="sxs-lookup"><span data-stu-id="7fc97-268">The **Log in** link is selected.</span></span>
* <span data-ttu-id="7fc97-269">Uživatel se pokusí získat přístup ke stránce s omezením, že nemá oprávnění k přístupu, **nebo** když ho systém neověřil.</span><span class="sxs-lookup"><span data-stu-id="7fc97-269">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="7fc97-270">Při odeslání formuláře na přihlašovací stránce se zavolá akce `OnPostAsync`.</span><span class="sxs-lookup"><span data-stu-id="7fc97-270">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="7fc97-271">`PasswordSignInAsync` se volá u objektu `_signInManager` (poskytovaného vkládáním závislostí).</span><span class="sxs-lookup"><span data-stu-id="7fc97-271">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="7fc97-272">Základní třída `Controller` zpřístupňuje vlastnost `User`, ke které máte přístup z metod kontroleru.</span><span class="sxs-lookup"><span data-stu-id="7fc97-272">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="7fc97-273">Můžete například vytvořit výčet `User.Claims` a provést autorizační rozhodnutí.</span><span class="sxs-lookup"><span data-stu-id="7fc97-273">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="7fc97-274">Další informace najdete v tématu <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="7fc97-274">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="7fc97-275">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="7fc97-275">Log out</span></span>

<span data-ttu-id="7fc97-276">Odkaz **odhlášení** vyvolá akci `LogoutModel.OnPost`.</span><span class="sxs-lookup"><span data-stu-id="7fc97-276">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="7fc97-277">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) vymaže deklarace identity uživatele uložené v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="7fc97-277">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="7fc97-278">Příspěvek je zadán ve *stránkách/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="7fc97-278">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="7fc97-279">Test identity</span><span class="sxs-lookup"><span data-stu-id="7fc97-279">Test Identity</span></span>

<span data-ttu-id="7fc97-280">Výchozí šablony webového projektu umožňují anonymní přístup k domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="7fc97-280">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="7fc97-281">Chcete-li otestovat identitu, přidejte [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) na stránku ochrany osobních údajů.</span><span class="sxs-lookup"><span data-stu-id="7fc97-281">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="7fc97-282">Pokud jste přihlášeni, odhlaste se. Spusťte aplikaci a vyberte odkaz na **ochranu osobních údajů** .</span><span class="sxs-lookup"><span data-stu-id="7fc97-282">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="7fc97-283">Budete přesměrováni na přihlašovací stránku.</span><span class="sxs-lookup"><span data-stu-id="7fc97-283">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="7fc97-284">Prozkoumat identitu</span><span class="sxs-lookup"><span data-stu-id="7fc97-284">Explore Identity</span></span>

<span data-ttu-id="7fc97-285">Podrobněji prozkoumat identitu:</span><span class="sxs-lookup"><span data-stu-id="7fc97-285">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="7fc97-286">Vytvořit úplný zdroj uživatelského rozhraní identity</span><span class="sxs-lookup"><span data-stu-id="7fc97-286">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="7fc97-287">Projděte si zdroj každé stránky a proveďte krok pomocí ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="7fc97-287">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="7fc97-288">Komponenty identity</span><span class="sxs-lookup"><span data-stu-id="7fc97-288">Identity Components</span></span>

<span data-ttu-id="7fc97-289">Všechny balíčky NuGet závislé na identitě jsou zahrnuté ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="7fc97-289">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="7fc97-290">Primárním balíčkem identity je [Microsoft. AspNetCore. identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="7fc97-290">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="7fc97-291">Tento balíček obsahuje základní sadu rozhraní pro ASP.NET Core identitu a je zahrnutý v `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="7fc97-291">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="7fc97-292">Migrace na ASP.NET Core identity</span><span class="sxs-lookup"><span data-stu-id="7fc97-292">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="7fc97-293">Další informace a pokyny k migraci stávajícího úložiště identit najdete v tématu [migrace ověřování a identity](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="7fc97-293">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="7fc97-294">Nastavení síly hesla</span><span class="sxs-lookup"><span data-stu-id="7fc97-294">Setting password strength</span></span>

<span data-ttu-id="7fc97-295">V části [Konfigurace](#pw) najdete ukázku, která nastavuje minimální požadavky na heslo.</span><span class="sxs-lookup"><span data-stu-id="7fc97-295">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="7fc97-296">Další kroky</span><span class="sxs-lookup"><span data-stu-id="7fc97-296">Next Steps</span></span>

* [<span data-ttu-id="7fc97-297">Konfigurace systému Identity</span><span class="sxs-lookup"><span data-stu-id="7fc97-297">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

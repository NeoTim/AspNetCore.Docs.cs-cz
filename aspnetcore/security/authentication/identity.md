---
title: Úvod do identity na ASP.NET Core
author: rick-anderson
description: Použijte identitu s aplikací ASP.NET Core. Přečtěte si, jak nastavit požadavky na heslo (RequireDigit, RequiredLength, RequiredUniqueChars a další).
ms.author: riande
ms.date: 12/05/2019
uid: security/authentication/identity
ms.openlocfilehash: c867b73a96fd081f6e2ca17fef561ac539c0a129
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880733"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="da8c6-104">Úvod do identity na ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="da8c6-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="da8c6-105">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="da8c6-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="da8c6-106">ASP.NET Core identita:</span><span class="sxs-lookup"><span data-stu-id="da8c6-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="da8c6-107">Je rozhraní API, které podporuje funkce přihlášení uživatelského rozhraní (UI).</span><span class="sxs-lookup"><span data-stu-id="da8c6-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="da8c6-108">Spravuje uživatele, hesla, data profilu, role, deklarace identity, tokeny, potvrzení e-mailu a další.</span><span class="sxs-lookup"><span data-stu-id="da8c6-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="da8c6-109">Uživatelé můžou vytvořit účet s přihlašovacími informacemi uloženými v identitě nebo můžou použít externího poskytovatele přihlášení.</span><span class="sxs-lookup"><span data-stu-id="da8c6-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="da8c6-110">Mezi podporované externí zprostředkovatelé přihlášení patří [Facebook, Google, účet Microsoft a Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="da8c6-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="da8c6-111">[Zdrojový kód identity](https://github.com/aspnet/AspNetCore/tree/master/src/Identity) je k dispozici na GitHubu.</span><span class="sxs-lookup"><span data-stu-id="da8c6-111">The [Identity source code](https://github.com/aspnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="da8c6-112">[Identita uživatelského rozhraní](xref:security/authentication/scaffold-identity) a zobrazení vygenerovaných souborů pro kontrolu interakce šablony s identitou.</span><span class="sxs-lookup"><span data-stu-id="da8c6-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="da8c6-113">Identita je obvykle nakonfigurovaná pomocí SQL Server databáze pro ukládání uživatelských jmen, hesel a profilových dat.</span><span class="sxs-lookup"><span data-stu-id="da8c6-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="da8c6-114">Případně můžete použít jiné trvalé úložiště, například Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="da8c6-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="da8c6-115">V tomto tématu se dozvíte, jak používat identitu k registraci, přihlášení a odhlášení uživatele.</span><span class="sxs-lookup"><span data-stu-id="da8c6-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="da8c6-116">Podrobnější pokyny k vytváření aplikací, které používají identitu, najdete v části Další kroky na konci tohoto článku.</span><span class="sxs-lookup"><span data-stu-id="da8c6-116">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<span data-ttu-id="da8c6-117">[Platforma Microsoft Identity](/azure/active-directory/develop/) je:</span><span class="sxs-lookup"><span data-stu-id="da8c6-117">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="da8c6-118">Vývoj platformy pro vývojáře Azure Active Directory (Azure AD).</span><span class="sxs-lookup"><span data-stu-id="da8c6-118">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="da8c6-119">Nesouvisí se ASP.NET Core identitou.</span><span class="sxs-lookup"><span data-stu-id="da8c6-119">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="da8c6-120">[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([Jak stáhnout)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="da8c6-120">[View or download the sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="da8c6-121">Vytvoření webové aplikace s ověřováním</span><span class="sxs-lookup"><span data-stu-id="da8c6-121">Create a Web app with authentication</span></span>

<span data-ttu-id="da8c6-122">Vytvořte ASP.NET Core projekt webové aplikace s jednotlivými uživatelskými účty.</span><span class="sxs-lookup"><span data-stu-id="da8c6-122">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="da8c6-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="da8c6-123">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="da8c6-124">Vyberte **soubor** > **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="da8c6-124">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="da8c6-125">Vyberte **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="da8c6-125">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="da8c6-126">Pojmenujte projekt **WebApp1** tak, aby měl stejný obor názvů jako projekt ke stažení.</span><span class="sxs-lookup"><span data-stu-id="da8c6-126">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="da8c6-127">Klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="da8c6-127">Click **OK**.</span></span>
* <span data-ttu-id="da8c6-128">Vyberte **webovou aplikaci**ASP.NET Core a pak vyberte **změnit ověřování**.</span><span class="sxs-lookup"><span data-stu-id="da8c6-128">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="da8c6-129">Vyberte **jednotlivé uživatelské účty** a klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="da8c6-129">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="da8c6-130">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="da8c6-130">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="da8c6-131">Předchozí příkaz vytvoří webovou aplikaci Razor pomocí SQLite.</span><span class="sxs-lookup"><span data-stu-id="da8c6-131">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="da8c6-132">Pokud chcete vytvořit webovou aplikaci pomocí LocalDB, spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="da8c6-132">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="da8c6-133">Vygenerovaný projekt poskytuje [ASP.NET Coreou identitu](xref:security/authentication/identity) jako [knihovnu tříd Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="da8c6-133">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="da8c6-134">Knihovna tříd identity Razor zpřístupňuje koncové body s oblastí `Identity`.</span><span class="sxs-lookup"><span data-stu-id="da8c6-134">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="da8c6-135">Příklad:</span><span class="sxs-lookup"><span data-stu-id="da8c6-135">For example:</span></span>

* <span data-ttu-id="da8c6-136">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="da8c6-136">/Identity/Account/Login</span></span>
* <span data-ttu-id="da8c6-137">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="da8c6-137">/Identity/Account/Logout</span></span>
* <span data-ttu-id="da8c6-138">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="da8c6-138">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="da8c6-139">Použít migrace</span><span class="sxs-lookup"><span data-stu-id="da8c6-139">Apply migrations</span></span>

<span data-ttu-id="da8c6-140">Použijte migrace k inicializaci databáze.</span><span class="sxs-lookup"><span data-stu-id="da8c6-140">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="da8c6-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="da8c6-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="da8c6-142">Spusťte následující příkaz v konzole správce balíčků (PMC):</span><span class="sxs-lookup"><span data-stu-id="da8c6-142">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="da8c6-143">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="da8c6-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="da8c6-144">Migrace nejsou v tomto kroku nutné při použití SQLite.</span><span class="sxs-lookup"><span data-stu-id="da8c6-144">Migrations are not necessary at this step when using SQLite.</span></span> <span data-ttu-id="da8c6-145">V případě LocalDB spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="da8c6-145">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="da8c6-146">Registrace a přihlášení testu</span><span class="sxs-lookup"><span data-stu-id="da8c6-146">Test Register and Login</span></span>

<span data-ttu-id="da8c6-147">Spusťte aplikaci a zaregistrujte uživatele.</span><span class="sxs-lookup"><span data-stu-id="da8c6-147">Run the app and register a user.</span></span> <span data-ttu-id="da8c6-148">V závislosti na velikosti obrazovky možná budete muset vybrat přepínač navigace a zobrazit tak odkazy na **registraci** a **přihlášení** .</span><span class="sxs-lookup"><span data-stu-id="da8c6-148">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="da8c6-149">Konfigurace služby identity</span><span class="sxs-lookup"><span data-stu-id="da8c6-149">Configure Identity services</span></span>

<span data-ttu-id="da8c6-150">Služby se přidávají v `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="da8c6-150">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="da8c6-151">Typický vzor je zavolat všechny metody `Add{Service}` a potom zavolat všechny metody `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="da8c6-151">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="da8c6-152">Předchozí zvýrazněný kód nakonfiguruje identitu s výchozími hodnotami možností.</span><span class="sxs-lookup"><span data-stu-id="da8c6-152">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="da8c6-153">Služby jsou zpřístupněny aplikaci prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="da8c6-153">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="da8c6-154">Identita je povolena voláním <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span><span class="sxs-lookup"><span data-stu-id="da8c6-154">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="da8c6-155">`UseAuthentication` do kanálu požadavků přidá [middleware](xref:fundamentals/middleware/index) ověřování.</span><span class="sxs-lookup"><span data-stu-id="da8c6-155">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="da8c6-156">Aplikace generovaná šablonou nepoužívá [autorizaci](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="da8c6-156">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="da8c6-157">`app.UseAuthorization` je součástí, aby se zajistilo, že je přidaný do správného pořadí, aby aplikace přidala autorizaci.</span><span class="sxs-lookup"><span data-stu-id="da8c6-157">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="da8c6-158">`UseRouting`, `UseAuthentication`, `UseAuthorization`a `UseEndpoints` musí být volány v pořadí uvedeném v předchozím kódu.</span><span class="sxs-lookup"><span data-stu-id="da8c6-158">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="da8c6-159">Další informace o `IdentityOptions` a `Startup`najdete v tématu <xref:Microsoft.AspNetCore.Identity.IdentityOptions> a [spuštění aplikace](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="da8c6-159">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="da8c6-160">Registrace, přihlášení a odhlášení uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="da8c6-160">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="da8c6-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="da8c6-161">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="da8c6-162">Přidejte soubory registru, přihlášení a odhlášení.</span><span class="sxs-lookup"><span data-stu-id="da8c6-162">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="da8c6-163">Použijte [identitu uživatelského rozhraní do projektu Razor s pokyny k autorizaci](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) a vygenerujte kód uvedený v této části.</span><span class="sxs-lookup"><span data-stu-id="da8c6-163">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="da8c6-164">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="da8c6-164">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="da8c6-165">Pokud jste vytvořili projekt s názvem **WebApp1**, spusťte následující příkazy.</span><span class="sxs-lookup"><span data-stu-id="da8c6-165">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="da8c6-166">Jinak použijte správný obor názvů pro `ApplicationDbContext`:</span><span class="sxs-lookup"><span data-stu-id="da8c6-166">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="da8c6-167">PowerShell používá jako oddělovač příkazů středník.</span><span class="sxs-lookup"><span data-stu-id="da8c6-167">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="da8c6-168">Při použití prostředí PowerShell, oddělte středníky v seznamu souborů nebo vložte seznam souborů do dvojitých uvozovek, jak ukazuje předchozí příklad.</span><span class="sxs-lookup"><span data-stu-id="da8c6-168">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="da8c6-169">Další informace o identitě uživatelského rozhraní naleznete v tématu [Identita uživatelského rozhraní do projektu Razor s autorizací](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="da8c6-169">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="da8c6-170">Ověřit registraci</span><span class="sxs-lookup"><span data-stu-id="da8c6-170">Examine Register</span></span>

<span data-ttu-id="da8c6-171">Když uživatel klikne na odkaz **zaregistrovat** , je vyvolána akce `RegisterModel.OnPostAsync`.</span><span class="sxs-lookup"><span data-stu-id="da8c6-171">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="da8c6-172">Uživatel je vytvořen pomocí [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) na objektu `_userManager`.</span><span class="sxs-lookup"><span data-stu-id="da8c6-172">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="da8c6-173">`_userManager` poskytuje vkládání závislostí):</span><span class="sxs-lookup"><span data-stu-id="da8c6-173">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="da8c6-174">Pokud byl uživatel vytvořen úspěšně, uživatel je přihlášen voláním `_signInManager.SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="da8c6-174">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="da8c6-175">Postup, jak zabránit okamžitému přihlášení při registraci, najdete v tématu [potvrzení účtu](xref:security/authentication/accconfirm#prevent-login-at-registration) .</span><span class="sxs-lookup"><span data-stu-id="da8c6-175">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="da8c6-176">Log in</span><span class="sxs-lookup"><span data-stu-id="da8c6-176">Log in</span></span>

<span data-ttu-id="da8c6-177">Přihlašovací formulář se zobrazí v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="da8c6-177">The Login form is displayed when:</span></span>

* <span data-ttu-id="da8c6-178">Je vybrán odkaz **Přihlásit** se.</span><span class="sxs-lookup"><span data-stu-id="da8c6-178">The **Log in** link is selected.</span></span>
* <span data-ttu-id="da8c6-179">Uživatel se pokusí získat přístup ke stránce s omezením, že nemá oprávnění k přístupu, **nebo** když ho systém neověřil.</span><span class="sxs-lookup"><span data-stu-id="da8c6-179">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="da8c6-180">Při odeslání formuláře na přihlašovací stránce se zavolá akce `OnPostAsync`.</span><span class="sxs-lookup"><span data-stu-id="da8c6-180">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="da8c6-181">`PasswordSignInAsync` se volá na objekt `_signInManager` (poskytnutý vkládáním závislostí).</span><span class="sxs-lookup"><span data-stu-id="da8c6-181">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="da8c6-182">Základní `Controller` třída zpřístupňuje vlastnost `User`, ke které lze dostat z metod kontroleru.</span><span class="sxs-lookup"><span data-stu-id="da8c6-182">The base `Controller` class exposes a `User` property that can be accessed from controller methods.</span></span> <span data-ttu-id="da8c6-183">Můžete například vytvořit výčet `User.Claims` a provést autorizační rozhodnutí.</span><span class="sxs-lookup"><span data-stu-id="da8c6-183">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="da8c6-184">Další informace najdete v tématu <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="da8c6-184">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="da8c6-185">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="da8c6-185">Log out</span></span>

<span data-ttu-id="da8c6-186">Odkaz **Odhlásit** se vyvolá `LogoutModel.OnPost` akci.</span><span class="sxs-lookup"><span data-stu-id="da8c6-186">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="da8c6-187">V předchozím kódu musí být kód `return RedirectToPage();` přesměrování, aby prohlížeč prováděl novou žádost a identita pro uživatele byla aktualizována.</span><span class="sxs-lookup"><span data-stu-id="da8c6-187">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="da8c6-188">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) vymaže deklarace identity uživatele uložené v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="da8c6-188">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="da8c6-189">Příspěvek je zadán ve *stránkách/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="da8c6-189">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="da8c6-190">Test identity</span><span class="sxs-lookup"><span data-stu-id="da8c6-190">Test Identity</span></span>

<span data-ttu-id="da8c6-191">Výchozí šablony webového projektu umožňují anonymní přístup k domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="da8c6-191">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="da8c6-192">K otestování identity přidejte [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span><span class="sxs-lookup"><span data-stu-id="da8c6-192">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="da8c6-193">Pokud jste přihlášeni, odhlaste se. Spusťte aplikaci a vyberte odkaz na **ochranu osobních údajů** .</span><span class="sxs-lookup"><span data-stu-id="da8c6-193">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="da8c6-194">Budete přesměrováni na přihlašovací stránku.</span><span class="sxs-lookup"><span data-stu-id="da8c6-194">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="da8c6-195">Prozkoumat identitu</span><span class="sxs-lookup"><span data-stu-id="da8c6-195">Explore Identity</span></span>

<span data-ttu-id="da8c6-196">Podrobněji prozkoumat identitu:</span><span class="sxs-lookup"><span data-stu-id="da8c6-196">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="da8c6-197">Vytvořit úplný zdroj uživatelského rozhraní identity</span><span class="sxs-lookup"><span data-stu-id="da8c6-197">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="da8c6-198">Projděte si zdroj každé stránky a proveďte krok pomocí ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="da8c6-198">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="da8c6-199">Komponenty identity</span><span class="sxs-lookup"><span data-stu-id="da8c6-199">Identity Components</span></span>

<span data-ttu-id="da8c6-200">Všechny balíčky NuGet závislé na identitách jsou součástí [ASP.NET Core sdílené rozhraní](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="da8c6-200">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="da8c6-201">Primárním balíčkem identity je [Microsoft. AspNetCore. identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="da8c6-201">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="da8c6-202">Tento balíček obsahuje základní sadu rozhraní pro ASP.NET Core identitu a je zahrnutý v `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="da8c6-202">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="da8c6-203">Migrace na ASP.NET Core identity</span><span class="sxs-lookup"><span data-stu-id="da8c6-203">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="da8c6-204">Další informace a pokyny k migraci stávajícího úložiště identit najdete v tématu [migrace ověřování a identity](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="da8c6-204">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="da8c6-205">Nastavení síly hesla</span><span class="sxs-lookup"><span data-stu-id="da8c6-205">Setting password strength</span></span>

<span data-ttu-id="da8c6-206">V části [Konfigurace](#pw) najdete ukázku, která nastavuje minimální požadavky na heslo.</span><span class="sxs-lookup"><span data-stu-id="da8c6-206">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="da8c6-207">AddDefaultIdentity a AddIdentity</span><span class="sxs-lookup"><span data-stu-id="da8c6-207">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="da8c6-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> byla představena v ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="da8c6-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="da8c6-209">Volání `AddDefaultIdentity` je podobné volání následujícího:</span><span class="sxs-lookup"><span data-stu-id="da8c6-209">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="da8c6-210">Další informace najdete v tématu [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="da8c6-210">See [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="next-steps"></a><span data-ttu-id="da8c6-211">Další kroky</span><span class="sxs-lookup"><span data-stu-id="da8c6-211">Next Steps</span></span>

* [<span data-ttu-id="da8c6-212">Konfigurace systému Identity</span><span class="sxs-lookup"><span data-stu-id="da8c6-212">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="da8c6-213">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="da8c6-213">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="da8c6-214">ASP.NET Core identity je systém členství, který do ASP.NET Core aplikací přidává funkce přihlášení.</span><span class="sxs-lookup"><span data-stu-id="da8c6-214">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="da8c6-215">Uživatelé můžou vytvořit účet s přihlašovacími informacemi uloženými v identitě nebo můžou použít externího poskytovatele přihlášení.</span><span class="sxs-lookup"><span data-stu-id="da8c6-215">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="da8c6-216">Mezi podporované externí zprostředkovatelé přihlášení patří [Facebook, Google, účet Microsoft a Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="da8c6-216">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="da8c6-217">Identitu můžete nakonfigurovat pomocí SQL Server databáze pro ukládání uživatelských jmen, hesel a profilových dat.</span><span class="sxs-lookup"><span data-stu-id="da8c6-217">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="da8c6-218">Případně můžete použít jiné trvalé úložiště, například Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="da8c6-218">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="da8c6-219">[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([Jak stáhnout)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="da8c6-219">[View or download the sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="da8c6-220">V tomto tématu se dozvíte, jak používat identitu k registraci, přihlášení a odhlášení uživatele.</span><span class="sxs-lookup"><span data-stu-id="da8c6-220">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="da8c6-221">Podrobnější pokyny k vytváření aplikací, které používají identitu, najdete v části Další kroky na konci tohoto článku.</span><span class="sxs-lookup"><span data-stu-id="da8c6-221">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="da8c6-222">AddDefaultIdentity a AddIdentity</span><span class="sxs-lookup"><span data-stu-id="da8c6-222">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="da8c6-223"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> byla představena v ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="da8c6-223"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="da8c6-224">Volání `AddDefaultIdentity` je podobné volání následujícího:</span><span class="sxs-lookup"><span data-stu-id="da8c6-224">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="da8c6-225">Další informace najdete v tématu [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="da8c6-225">See [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="da8c6-226">Vytvoření webové aplikace s ověřováním</span><span class="sxs-lookup"><span data-stu-id="da8c6-226">Create a Web app with authentication</span></span>

<span data-ttu-id="da8c6-227">Vytvořte ASP.NET Core projekt webové aplikace s jednotlivými uživatelskými účty.</span><span class="sxs-lookup"><span data-stu-id="da8c6-227">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="da8c6-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="da8c6-228">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="da8c6-229">Vyberte **soubor** > **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="da8c6-229">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="da8c6-230">Vyberte **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="da8c6-230">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="da8c6-231">Pojmenujte projekt **WebApp1** tak, aby měl stejný obor názvů jako projekt ke stažení.</span><span class="sxs-lookup"><span data-stu-id="da8c6-231">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="da8c6-232">Klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="da8c6-232">Click **OK**.</span></span>
* <span data-ttu-id="da8c6-233">Vyberte **webovou aplikaci**ASP.NET Core a pak vyberte **změnit ověřování**.</span><span class="sxs-lookup"><span data-stu-id="da8c6-233">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="da8c6-234">Vyberte **jednotlivé uživatelské účty** a klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="da8c6-234">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="da8c6-235">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="da8c6-235">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="da8c6-236">Vygenerovaný projekt poskytuje [ASP.NET Coreou identitu](xref:security/authentication/identity) jako [knihovnu tříd Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="da8c6-236">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="da8c6-237">Knihovna tříd identity Razor zpřístupňuje koncové body s oblastí `Identity`.</span><span class="sxs-lookup"><span data-stu-id="da8c6-237">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="da8c6-238">Příklad:</span><span class="sxs-lookup"><span data-stu-id="da8c6-238">For example:</span></span>

* <span data-ttu-id="da8c6-239">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="da8c6-239">/Identity/Account/Login</span></span>
* <span data-ttu-id="da8c6-240">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="da8c6-240">/Identity/Account/Logout</span></span>
* <span data-ttu-id="da8c6-241">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="da8c6-241">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="da8c6-242">Použít migrace</span><span class="sxs-lookup"><span data-stu-id="da8c6-242">Apply migrations</span></span>

<span data-ttu-id="da8c6-243">Použijte migrace k inicializaci databáze.</span><span class="sxs-lookup"><span data-stu-id="da8c6-243">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="da8c6-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="da8c6-244">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="da8c6-245">Spusťte následující příkaz v konzole správce balíčků (PMC):</span><span class="sxs-lookup"><span data-stu-id="da8c6-245">Run the following command in the Package Manager Console (PMC):</span></span>

```PM> Update-Database```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="da8c6-246">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="da8c6-246">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="da8c6-247">Registrace a přihlášení testu</span><span class="sxs-lookup"><span data-stu-id="da8c6-247">Test Register and Login</span></span>

<span data-ttu-id="da8c6-248">Spusťte aplikaci a zaregistrujte uživatele.</span><span class="sxs-lookup"><span data-stu-id="da8c6-248">Run the app and register a user.</span></span> <span data-ttu-id="da8c6-249">V závislosti na velikosti obrazovky možná budete muset vybrat přepínač navigace a zobrazit tak odkazy na **registraci** a **přihlášení** .</span><span class="sxs-lookup"><span data-stu-id="da8c6-249">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="da8c6-250">Konfigurace služby identity</span><span class="sxs-lookup"><span data-stu-id="da8c6-250">Configure Identity services</span></span>

<span data-ttu-id="da8c6-251">Služby se přidávají v `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="da8c6-251">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="da8c6-252">Typický vzor je zavolat všechny metody `Add{Service}` a potom zavolat všechny metody `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="da8c6-252">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="da8c6-253">Předchozí kód nakonfiguruje identitu s výchozími hodnotami možností.</span><span class="sxs-lookup"><span data-stu-id="da8c6-253">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="da8c6-254">Služby jsou zpřístupněny aplikaci prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="da8c6-254">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="da8c6-255">Identita je povolena voláním [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="da8c6-255">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="da8c6-256">`UseAuthentication` do kanálu požadavků přidá [middleware](xref:fundamentals/middleware/index) ověřování.</span><span class="sxs-lookup"><span data-stu-id="da8c6-256">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="da8c6-257">Další informace naleznete v tématu [Třída IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) a [spuštění aplikace](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="da8c6-257">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="da8c6-258">Registrace, přihlášení a odhlášení uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="da8c6-258">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="da8c6-259">Použijte [identitu uživatelského rozhraní do projektu Razor s pokyny k autorizaci](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) a vygenerujte kód uvedený v této části.</span><span class="sxs-lookup"><span data-stu-id="da8c6-259">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="da8c6-260">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="da8c6-260">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="da8c6-261">Přidejte soubory registru, přihlášení a odhlášení.</span><span class="sxs-lookup"><span data-stu-id="da8c6-261">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="da8c6-262">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="da8c6-262">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="da8c6-263">Pokud jste vytvořili projekt s názvem **WebApp1**, spusťte následující příkazy.</span><span class="sxs-lookup"><span data-stu-id="da8c6-263">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="da8c6-264">Jinak použijte správný obor názvů pro `ApplicationDbContext`:</span><span class="sxs-lookup"><span data-stu-id="da8c6-264">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="da8c6-265">PowerShell používá jako oddělovač příkazů středník.</span><span class="sxs-lookup"><span data-stu-id="da8c6-265">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="da8c6-266">Při použití prostředí PowerShell, oddělte středníky v seznamu souborů nebo vložte seznam souborů do dvojitých uvozovek, jak ukazuje předchozí příklad.</span><span class="sxs-lookup"><span data-stu-id="da8c6-266">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="da8c6-267">Ověřit registraci</span><span class="sxs-lookup"><span data-stu-id="da8c6-267">Examine Register</span></span>

<span data-ttu-id="da8c6-268">Když uživatel klikne na odkaz **zaregistrovat** , je vyvolána akce `RegisterModel.OnPostAsync`.</span><span class="sxs-lookup"><span data-stu-id="da8c6-268">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="da8c6-269">Uživatel je vytvořen pomocí [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) na objektu `_userManager`.</span><span class="sxs-lookup"><span data-stu-id="da8c6-269">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="da8c6-270">`_userManager` poskytuje vkládání závislostí):</span><span class="sxs-lookup"><span data-stu-id="da8c6-270">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="da8c6-271">Pokud byl uživatel vytvořen úspěšně, uživatel je přihlášen voláním `_signInManager.SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="da8c6-271">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="da8c6-272">**Poznámka:** Postup, jak zabránit okamžitému přihlášení při registraci, najdete v tématu [potvrzení účtu](xref:security/authentication/accconfirm#prevent-login-at-registration) .</span><span class="sxs-lookup"><span data-stu-id="da8c6-272">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="da8c6-273">Log in</span><span class="sxs-lookup"><span data-stu-id="da8c6-273">Log in</span></span>

<span data-ttu-id="da8c6-274">Přihlašovací formulář se zobrazí v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="da8c6-274">The Login form is displayed when:</span></span>

* <span data-ttu-id="da8c6-275">Je vybrán odkaz **Přihlásit** se.</span><span class="sxs-lookup"><span data-stu-id="da8c6-275">The **Log in** link is selected.</span></span>
* <span data-ttu-id="da8c6-276">Uživatel se pokusí získat přístup ke stránce s omezením, že nemá oprávnění k přístupu, **nebo** když ho systém neověřil.</span><span class="sxs-lookup"><span data-stu-id="da8c6-276">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="da8c6-277">Při odeslání formuláře na přihlašovací stránce se zavolá akce `OnPostAsync`.</span><span class="sxs-lookup"><span data-stu-id="da8c6-277">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="da8c6-278">`PasswordSignInAsync` se volá na objekt `_signInManager` (poskytnutý vkládáním závislostí).</span><span class="sxs-lookup"><span data-stu-id="da8c6-278">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="da8c6-279">Základní `Controller` třída zpřístupňuje vlastnost `User`, ke které máte přístup z metod kontroleru.</span><span class="sxs-lookup"><span data-stu-id="da8c6-279">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="da8c6-280">Můžete například vytvořit výčet `User.Claims` a provést autorizační rozhodnutí.</span><span class="sxs-lookup"><span data-stu-id="da8c6-280">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="da8c6-281">Další informace najdete v tématu <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="da8c6-281">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="da8c6-282">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="da8c6-282">Log out</span></span>

<span data-ttu-id="da8c6-283">Odkaz **Odhlásit** se vyvolá `LogoutModel.OnPost` akci.</span><span class="sxs-lookup"><span data-stu-id="da8c6-283">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="da8c6-284">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) vymaže deklarace identity uživatele uložené v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="da8c6-284">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="da8c6-285">Příspěvek je zadán ve *stránkách/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="da8c6-285">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="da8c6-286">Test identity</span><span class="sxs-lookup"><span data-stu-id="da8c6-286">Test Identity</span></span>

<span data-ttu-id="da8c6-287">Výchozí šablony webového projektu umožňují anonymní přístup k domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="da8c6-287">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="da8c6-288">Chcete-li otestovat identitu, přidejte [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) na stránku ochrany osobních údajů.</span><span class="sxs-lookup"><span data-stu-id="da8c6-288">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="da8c6-289">Pokud jste přihlášeni, odhlaste se. Spusťte aplikaci a vyberte odkaz na **ochranu osobních údajů** .</span><span class="sxs-lookup"><span data-stu-id="da8c6-289">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="da8c6-290">Budete přesměrováni na přihlašovací stránku.</span><span class="sxs-lookup"><span data-stu-id="da8c6-290">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="da8c6-291">Prozkoumat identitu</span><span class="sxs-lookup"><span data-stu-id="da8c6-291">Explore Identity</span></span>

<span data-ttu-id="da8c6-292">Podrobněji prozkoumat identitu:</span><span class="sxs-lookup"><span data-stu-id="da8c6-292">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="da8c6-293">Vytvořit úplný zdroj uživatelského rozhraní identity</span><span class="sxs-lookup"><span data-stu-id="da8c6-293">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="da8c6-294">Projděte si zdroj každé stránky a proveďte krok pomocí ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="da8c6-294">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="da8c6-295">Komponenty identity</span><span class="sxs-lookup"><span data-stu-id="da8c6-295">Identity Components</span></span>

<span data-ttu-id="da8c6-296">Všechny balíčky NuGet závislé na identitě jsou zahrnuté ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="da8c6-296">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="da8c6-297">Primárním balíčkem identity je [Microsoft. AspNetCore. identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="da8c6-297">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="da8c6-298">Tento balíček obsahuje základní sadu rozhraní pro ASP.NET Core identitu a je zahrnutý v `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="da8c6-298">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="da8c6-299">Migrace na ASP.NET Core identity</span><span class="sxs-lookup"><span data-stu-id="da8c6-299">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="da8c6-300">Další informace a pokyny k migraci stávajícího úložiště identit najdete v tématu [migrace ověřování a identity](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="da8c6-300">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="da8c6-301">Nastavení síly hesla</span><span class="sxs-lookup"><span data-stu-id="da8c6-301">Setting password strength</span></span>

<span data-ttu-id="da8c6-302">V části [Konfigurace](#pw) najdete ukázku, která nastavuje minimální požadavky na heslo.</span><span class="sxs-lookup"><span data-stu-id="da8c6-302">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="da8c6-303">Další kroky</span><span class="sxs-lookup"><span data-stu-id="da8c6-303">Next Steps</span></span>

* [<span data-ttu-id="da8c6-304">Konfigurace systému Identity</span><span class="sxs-lookup"><span data-stu-id="da8c6-304">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

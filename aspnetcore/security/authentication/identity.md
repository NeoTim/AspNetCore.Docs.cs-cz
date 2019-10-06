---
title: Úvod do identity na ASP.NET Core
author: rick-anderson
description: Použijte identitu s aplikací ASP.NET Core. Přečtěte si, jak nastavit požadavky na heslo (RequireDigit, RequiredLength, RequiredUniqueChars a další).
ms.author: riande
ms.date: 03/26/2019
uid: security/authentication/identity
ms.openlocfilehash: 6701eb0ac1b1abb8699a5a529bcc29f295e5c7c9
ms.sourcegitcommit: 4115bf0e850c13d4e655beb5ab5e8ff431173cb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981913"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="918d0-104">Úvod do identity na ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="918d0-104">Introduction to Identity on ASP.NET Core</span></span>

<span data-ttu-id="918d0-105">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="918d0-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="918d0-106">ASP.NET Core identity je systém členství, který do ASP.NET Core aplikací přidává funkce přihlášení.</span><span class="sxs-lookup"><span data-stu-id="918d0-106">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="918d0-107">Uživatelé můžou vytvořit účet s přihlašovacími informacemi uloženými v identitě nebo můžou použít externího poskytovatele přihlášení.</span><span class="sxs-lookup"><span data-stu-id="918d0-107">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="918d0-108">Mezi podporované externí zprostředkovatelé přihlášení patří [Facebook, Google, účet Microsoft a Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="918d0-108">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="918d0-109">Identitu můžete nakonfigurovat pomocí SQL Server databáze pro ukládání uživatelských jmen, hesel a profilových dat.</span><span class="sxs-lookup"><span data-stu-id="918d0-109">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="918d0-110">Případně můžete použít jiné trvalé úložiště, například Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="918d0-110">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="918d0-111">[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([Jak stáhnout)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="918d0-111">[View or download the sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="918d0-112">V tomto tématu se dozvíte, jak používat identitu k registraci, přihlášení a odhlášení uživatele.</span><span class="sxs-lookup"><span data-stu-id="918d0-112">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="918d0-113">Podrobnější pokyny k vytváření aplikací, které používají identitu, najdete v části Další kroky na konci tohoto článku.</span><span class="sxs-lookup"><span data-stu-id="918d0-113">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

::: moniker range=">= aspnetcore-2.1"

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="918d0-114">AddDefaultIdentity a AddIdentity</span><span class="sxs-lookup"><span data-stu-id="918d0-114">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="918d0-115">[AddDefaultIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionuiextensions.adddefaultidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionUIExtensions_AddDefaultIdentity__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__) byl představen v ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="918d0-115">[AddDefaultIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionuiextensions.adddefaultidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionUIExtensions_AddDefaultIdentity__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__) was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="918d0-116">Volání `AddDefaultIdentity` je podobné volání následujícího:</span><span class="sxs-lookup"><span data-stu-id="918d0-116">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* [<span data-ttu-id="918d0-117">AddIdentity</span><span class="sxs-lookup"><span data-stu-id="918d0-117">AddIdentity</span></span>](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.addidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_AddIdentity__2_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__)
* [<span data-ttu-id="918d0-118">AddDefaultUI</span><span class="sxs-lookup"><span data-stu-id="918d0-118">AddDefaultUI</span></span>](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderuiextensions.adddefaultui?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderUIExtensions_AddDefaultUI_Microsoft_AspNetCore_Identity_IdentityBuilder_)
* [<span data-ttu-id="918d0-119">AddDefaultTokenProviders</span><span class="sxs-lookup"><span data-stu-id="918d0-119">AddDefaultTokenProviders</span></span>](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderextensions.adddefaulttokenproviders?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderExtensions_AddDefaultTokenProviders_Microsoft_AspNetCore_Identity_IdentityBuilder_)

<span data-ttu-id="918d0-120">Další informace najdete v tématu [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="918d0-120">See [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

::: moniker-end

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="918d0-121">Vytvoření webové aplikace s ověřováním</span><span class="sxs-lookup"><span data-stu-id="918d0-121">Create a Web app with authentication</span></span>

<span data-ttu-id="918d0-122">Vytvořte ASP.NET Core projekt webové aplikace s jednotlivými uživatelskými účty.</span><span class="sxs-lookup"><span data-stu-id="918d0-122">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="918d0-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="918d0-123">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="918d0-124">Vyberte **Soubor** > **Nový** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="918d0-124">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="918d0-125">Vyberte **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="918d0-125">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="918d0-126">Pojmenujte projekt **WebApp1** tak, aby měl stejný obor názvů jako projekt ke stažení.</span><span class="sxs-lookup"><span data-stu-id="918d0-126">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="918d0-127">Klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="918d0-127">Click **OK**.</span></span>
* <span data-ttu-id="918d0-128">Vyberte **webovou aplikaci**ASP.NET Core a pak vyberte **změnit ověřování**.</span><span class="sxs-lookup"><span data-stu-id="918d0-128">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="918d0-129">Vyberte **jednotlivé uživatelské účty** a klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="918d0-129">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="918d0-130">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="918d0-130">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="918d0-131">Vygenerovaný projekt poskytuje [ASP.NET Coreou identitu](xref:security/authentication/identity) jako [knihovnu tříd Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="918d0-131">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="918d0-132">Knihovna tříd identity Razor zpřístupňuje koncové body s oblastí `Identity`.</span><span class="sxs-lookup"><span data-stu-id="918d0-132">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="918d0-133">Příklad:</span><span class="sxs-lookup"><span data-stu-id="918d0-133">For example:</span></span>

* <span data-ttu-id="918d0-134">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="918d0-134">/Identity/Account/Login</span></span>
* <span data-ttu-id="918d0-135">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="918d0-135">/Identity/Account/Logout</span></span>
* <span data-ttu-id="918d0-136">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="918d0-136">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="918d0-137">Použít migrace</span><span class="sxs-lookup"><span data-stu-id="918d0-137">Apply migrations</span></span>

<span data-ttu-id="918d0-138">Použijte migrace pro inicializovat databáze.</span><span class="sxs-lookup"><span data-stu-id="918d0-138">Apply the migrations to initialise the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="918d0-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="918d0-139">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="918d0-140">Spusťte následující příkaz v konzole správce balíčků (PMC):</span><span class="sxs-lookup"><span data-stu-id="918d0-140">Run the following command in the Package Manager Console (PMC):</span></span>

```PM> Update-Database```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="918d0-141">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="918d0-141">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="918d0-142">Registrace a přihlášení testu</span><span class="sxs-lookup"><span data-stu-id="918d0-142">Test Register and Login</span></span>

<span data-ttu-id="918d0-143">Spusťte aplikaci a zaregistrujte uživatele.</span><span class="sxs-lookup"><span data-stu-id="918d0-143">Run the app and register a user.</span></span> <span data-ttu-id="918d0-144">V závislosti na velikosti obrazovky možná budete muset vybrat přepínač navigace a zobrazit tak odkazy na **registraci** a **přihlášení** .</span><span class="sxs-lookup"><span data-stu-id="918d0-144">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="918d0-145">Konfigurace služby identity</span><span class="sxs-lookup"><span data-stu-id="918d0-145">Configure Identity services</span></span>

<span data-ttu-id="918d0-146">Služby jsou přidány do `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="918d0-146">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="918d0-147">Typickým vzorem je zavolat všechny metody `Add{Service}` a pak zavolat všechny metody `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="918d0-147">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="918d0-148">Předchozí kód nakonfiguruje identitu s výchozími hodnotami možností.</span><span class="sxs-lookup"><span data-stu-id="918d0-148">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="918d0-149">Služby jsou zpřístupněny aplikaci prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="918d0-149">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

   <span data-ttu-id="918d0-150">Identita je povolena voláním [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="918d0-150">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="918d0-151">`UseAuthentication` přidá do kanálu žádosti [middleware](xref:fundamentals/middleware/index) ověřování.</span><span class="sxs-lookup"><span data-stu-id="918d0-151">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

   [!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,11-28,30-42)]

   <span data-ttu-id="918d0-152">Služby jsou zpřístupněny aplikaci prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="918d0-152">Services are made available to the application through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

   <span data-ttu-id="918d0-153">Pro aplikaci je povolena identita voláním `UseAuthentication` v metodě `Configure`.</span><span class="sxs-lookup"><span data-stu-id="918d0-153">Identity is enabled for the application by calling `UseAuthentication` in the `Configure` method.</span></span> <span data-ttu-id="918d0-154">`UseAuthentication` přidá do kanálu žádosti [middleware](xref:fundamentals/middleware/index) ověřování.</span><span class="sxs-lookup"><span data-stu-id="918d0-154">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configure&highlight=17)]

::: moniker-end

::: moniker range="= aspnetcore-1.1"

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,13-33)]

   <span data-ttu-id="918d0-155">Tyto služby jsou zpřístupněny aplikaci prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="918d0-155">These services are made available to the application through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

   <span data-ttu-id="918d0-156">Pro aplikaci je povolena identita voláním `UseIdentity` v metodě `Configure`.</span><span class="sxs-lookup"><span data-stu-id="918d0-156">Identity is enabled for the application by calling `UseIdentity` in the `Configure` method.</span></span> <span data-ttu-id="918d0-157">`UseIdentity` přidá do kanálu žádosti [middleware](xref:fundamentals/middleware/index) ověřování na základě souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="918d0-157">`UseIdentity` adds cookie-based authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configure&highlight=21)]

::: moniker-end

<span data-ttu-id="918d0-158">Další informace naleznete v tématu [Třída IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) a [spuštění aplikace](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="918d0-158">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="918d0-159">Registrace, přihlášení a odhlášení uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="918d0-159">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="918d0-160">Použijte [identitu uživatelského rozhraní do projektu Razor s pokyny k autorizaci](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) a vygenerujte kód uvedený v této části.</span><span class="sxs-lookup"><span data-stu-id="918d0-160">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="918d0-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="918d0-161">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="918d0-162">Přidejte soubory registru, přihlášení a odhlášení.</span><span class="sxs-lookup"><span data-stu-id="918d0-162">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="918d0-163">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="918d0-163">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="918d0-164">Pokud jste vytvořili projekt s názvem **WebApp1**, spusťte následující příkazy.</span><span class="sxs-lookup"><span data-stu-id="918d0-164">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="918d0-165">V opačném případě použijte pro `ApplicationDbContext` správný obor názvů:</span><span class="sxs-lookup"><span data-stu-id="918d0-165">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="918d0-166">PowerShell používá jako oddělovač příkazů středník.</span><span class="sxs-lookup"><span data-stu-id="918d0-166">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="918d0-167">Při použití prostředí PowerShell, oddělte středníky v seznamu souborů nebo vložte seznam souborů do dvojitých uvozovek, jak ukazuje předchozí příklad.</span><span class="sxs-lookup"><span data-stu-id="918d0-167">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="918d0-168">Ověřit registraci</span><span class="sxs-lookup"><span data-stu-id="918d0-168">Examine Register</span></span>

::: moniker range=">= aspnetcore-2.1"

   <span data-ttu-id="918d0-169">Když uživatel klikne na odkaz **zaregistrovat** , je vyvolána akce `RegisterModel.OnPostAsync`.</span><span class="sxs-lookup"><span data-stu-id="918d0-169">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="918d0-170">Uživatel je vytvořen pomocí [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) na objektu `_userManager`.</span><span class="sxs-lookup"><span data-stu-id="918d0-170">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="918d0-171">`_userManager` je poskytován pomocí injektáže závislosti):</span><span class="sxs-lookup"><span data-stu-id="918d0-171">`_userManager` is provided by dependency injection):</span></span>

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7,22)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   <span data-ttu-id="918d0-172">Když uživatel klikne na odkaz **zaregistrovat** , akce `Register` se vyvolá v `AccountController`.</span><span class="sxs-lookup"><span data-stu-id="918d0-172">When a user clicks the **Register** link, the `Register` action is invoked on `AccountController`.</span></span> <span data-ttu-id="918d0-173">Akce `Register` vytvoří uživatele voláním `CreateAsync` na objekt `_userManager` (poskytnutý pro vkládání závislostí do `AccountController`):</span><span class="sxs-lookup"><span data-stu-id="918d0-173">The `Register` action creates the user by calling `CreateAsync` on the `_userManager` object (provided to `AccountController` by dependency injection):</span></span>

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_register&highlight=11)]

::: moniker-end

   <span data-ttu-id="918d0-174">Pokud byl uživatel vytvořen úspěšně, uživatel je přihlášen voláním `_signInManager.SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="918d0-174">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

   <span data-ttu-id="918d0-175">**Poznámka:** Postup, jak zabránit okamžitému přihlášení při registraci, najdete v tématu [potvrzení účtu](xref:security/authentication/accconfirm#prevent-login-at-registration) .</span><span class="sxs-lookup"><span data-stu-id="918d0-175">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="918d0-176">Přihlásit se</span><span class="sxs-lookup"><span data-stu-id="918d0-176">Log in</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="918d0-177">Přihlašovací formulář se zobrazí v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="918d0-177">The Login form is displayed when:</span></span>

* <span data-ttu-id="918d0-178">Je vybrán odkaz **Přihlásit** se.</span><span class="sxs-lookup"><span data-stu-id="918d0-178">The **Log in** link is selected.</span></span>
* <span data-ttu-id="918d0-179">Uživatel se pokusí získat přístup ke stránce s omezením, že nemá oprávnění k přístupu, **nebo** když ho systém neověřil.</span><span class="sxs-lookup"><span data-stu-id="918d0-179">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="918d0-180">Při odeslání formuláře na přihlašovací stránce se zavolá akce `OnPostAsync`.</span><span class="sxs-lookup"><span data-stu-id="918d0-180">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="918d0-181">`PasswordSignInAsync` se volá u objektu `_signInManager` (poskytovaného vkládáním závislostí).</span><span class="sxs-lookup"><span data-stu-id="918d0-181">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

   <span data-ttu-id="918d0-182">Základní třída `Controller` zpřístupňuje vlastnost `User`, ke které máte přístup z metod kontroleru.</span><span class="sxs-lookup"><span data-stu-id="918d0-182">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="918d0-183">Můžete například vytvořit výčet `User.Claims` a provést autorizační rozhodnutí.</span><span class="sxs-lookup"><span data-stu-id="918d0-183">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="918d0-184">Další informace naleznete v tématu <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="918d0-184">For more information, see <xref:security/authorization/introduction>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="918d0-185">Přihlašovací formulář se zobrazí, když uživatel vybere odkaz **Přihlásit** nebo se přesměruje při přístupu ke stránce, která vyžaduje ověření.</span><span class="sxs-lookup"><span data-stu-id="918d0-185">The Login form is displayed when users select the **Log in** link or are redirected when accessing a page that requires authentication.</span></span> <span data-ttu-id="918d0-186">Když uživatel formulář odešle na přihlašovací stránce, zavolá se akce `AccountController` `Login`.</span><span class="sxs-lookup"><span data-stu-id="918d0-186">When the user submits the form on the Login page, the `AccountController` `Login` action is called.</span></span>

<span data-ttu-id="918d0-187">Akce `Login` volá `PasswordSignInAsync` na objekt `_signInManager` (poskytnutý pro vkládání závislostí do `AccountController`).</span><span class="sxs-lookup"><span data-stu-id="918d0-187">The `Login` action calls `PasswordSignInAsync` on the `_signInManager` object (provided to `AccountController` by dependency injection).</span></span>

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_login&highlight=13-14)]

<span data-ttu-id="918d0-188">Základní třída (`Controller` nebo `PageModel`) zpřístupňuje vlastnost `User`.</span><span class="sxs-lookup"><span data-stu-id="918d0-188">The base (`Controller` or `PageModel`) class exposes a `User` property.</span></span> <span data-ttu-id="918d0-189">Můžete například vytvořit výčet `User.Claims` pro rozhodování o autorizaci.</span><span class="sxs-lookup"><span data-stu-id="918d0-189">For example, `User.Claims` can be enumerated to make authorization decisions.</span></span>

::: moniker-end

### <a name="log-out"></a><span data-ttu-id="918d0-190">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="918d0-190">Log out</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="918d0-191">Odkaz **odhlášení** vyvolá akci `LogoutModel.OnPost`.</span><span class="sxs-lookup"><span data-stu-id="918d0-191">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="918d0-192">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) vymaže deklarace identity uživatele uložené v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="918d0-192">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="918d0-193">Příspěvek je zadán ve *stránkách/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="918d0-193">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   <span data-ttu-id="918d0-194">Kliknutím na odkaz **Odhlásit** se zavolá akce `LogOut`.</span><span class="sxs-lookup"><span data-stu-id="918d0-194">Clicking the **Log out** link calls the `LogOut` action.</span></span>

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_logout&highlight=7)]

   <span data-ttu-id="918d0-195">Předchozí kód volá metodu `_signInManager.SignOutAsync`.</span><span class="sxs-lookup"><span data-stu-id="918d0-195">The preceding code calls the `_signInManager.SignOutAsync` method.</span></span> <span data-ttu-id="918d0-196">Metoda `SignOutAsync` vymaže deklarace identity uživatele uložené v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="918d0-196">The `SignOutAsync` method clears the user's claims stored in a cookie.</span></span>

::: moniker-end

## <a name="test-identity"></a><span data-ttu-id="918d0-197">Test identity</span><span class="sxs-lookup"><span data-stu-id="918d0-197">Test Identity</span></span>

<span data-ttu-id="918d0-198">Výchozí šablony webového projektu umožňují anonymní přístup k domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="918d0-198">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="918d0-199">Chcete-li otestovat identitu, přidejte [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) na stránku ochrany osobních údajů.</span><span class="sxs-lookup"><span data-stu-id="918d0-199">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=6)]

<span data-ttu-id="918d0-200">Pokud jste přihlášeni, odhlaste se. Spusťte aplikaci a vyberte odkaz na **ochranu osobních údajů** .</span><span class="sxs-lookup"><span data-stu-id="918d0-200">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="918d0-201">Budete přesměrováni na přihlašovací stránku.</span><span class="sxs-lookup"><span data-stu-id="918d0-201">You are redirected to the login page.</span></span>

::: moniker range=">= aspnetcore-2.1"

### <a name="explore-identity"></a><span data-ttu-id="918d0-202">Prozkoumat identitu</span><span class="sxs-lookup"><span data-stu-id="918d0-202">Explore Identity</span></span>

<span data-ttu-id="918d0-203">Podrobněji prozkoumat identitu:</span><span class="sxs-lookup"><span data-stu-id="918d0-203">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="918d0-204">Vytvořit úplný zdroj uživatelského rozhraní identity</span><span class="sxs-lookup"><span data-stu-id="918d0-204">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="918d0-205">Projděte si zdroj každé stránky a proveďte krok pomocí ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="918d0-205">Examine the source of each page and step through the debugger.</span></span>

::: moniker-end

## <a name="identity-components"></a><span data-ttu-id="918d0-206">Komponenty identity</span><span class="sxs-lookup"><span data-stu-id="918d0-206">Identity Components</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="918d0-207">Všechny balíčky NuGet závislé na identitě jsou zahrnuté ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="918d0-207">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="918d0-208">Primárním balíčkem identity je [Microsoft. AspNetCore. identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="918d0-208">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="918d0-209">Tento balíček obsahuje základní sadu rozhraní pro ASP.NET Core identitu a je zahrnutý v `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="918d0-209">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="918d0-210">Migrace na ASP.NET Core identity</span><span class="sxs-lookup"><span data-stu-id="918d0-210">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="918d0-211">Další informace a pokyny k migraci stávajícího úložiště identit najdete v tématu [migrace ověřování a identity](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="918d0-211">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="918d0-212">Nastavení síly hesla</span><span class="sxs-lookup"><span data-stu-id="918d0-212">Setting password strength</span></span>

<span data-ttu-id="918d0-213">V části [Konfigurace](#pw) najdete ukázku, která nastavuje minimální požadavky na heslo.</span><span class="sxs-lookup"><span data-stu-id="918d0-213">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="918d0-214">Další kroky</span><span class="sxs-lookup"><span data-stu-id="918d0-214">Next Steps</span></span>

* [<span data-ttu-id="918d0-215">Konfigurace systému Identity</span><span class="sxs-lookup"><span data-stu-id="918d0-215">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

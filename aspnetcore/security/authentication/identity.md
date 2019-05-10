---
title: Úvod do Identity v ASP.NET Core
author: rick-anderson
description: Pomocí Identity aplikace v ASP.NET Core. Zjistěte, jak nastavit požadavky na heslo (RequireDigit RequiredLength, RequiredUniqueChars a další).
ms.author: riande
ms.date: 03/26/2019
uid: security/authentication/identity
ms.openlocfilehash: d813fa364bb733185baa7b2cd2d95f8b4ff570e2
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64900483"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="42e3d-104">Úvod do Identity v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="42e3d-104">Introduction to Identity on ASP.NET Core</span></span>

<span data-ttu-id="42e3d-105">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="42e3d-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="42e3d-106">ASP.NET Core Identity je systém členství, který přidá funkce přihlášení do aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="42e3d-106">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="42e3d-107">Uživatelé můžou vytvářet účet s přihlašovací údaje uložené v Identity nebo může použít externího zprostředkovatele přihlášení.</span><span class="sxs-lookup"><span data-stu-id="42e3d-107">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="42e3d-108">Podporované externí přihlášení zprostředkovatele patří [Facebook, Google, Account Microsoft a Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="42e3d-108">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="42e3d-109">Identity lze konfigurovat pomocí databáze SQL serveru ukládat uživatelská jména, hesla a data profilu.</span><span class="sxs-lookup"><span data-stu-id="42e3d-109">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="42e3d-110">Můžete také jiné trvalého úložiště je možné, například Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="42e3d-110">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="42e3d-111">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([ke stažení)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="42e3d-111">[View or download the sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="42e3d-112">V tomto tématu se naučíte, jak zaregistrovat, přihlaste se pomocí Identity a odhlášení uživatele.</span><span class="sxs-lookup"><span data-stu-id="42e3d-112">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="42e3d-113">Podrobnější pokyny k vytváření aplikací, které používají Identity najdete v části Další kroky na konci tohoto článku.</span><span class="sxs-lookup"><span data-stu-id="42e3d-113">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

::: moniker range=">= aspnetcore-2.1"

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="42e3d-114">AddDefaultIdentity a AddIdentity</span><span class="sxs-lookup"><span data-stu-id="42e3d-114">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="42e3d-115">[AddDefaultIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionuiextensions.adddefaultidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionUIExtensions_AddDefaultIdentity__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__) byla zavedena v ASP.NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="42e3d-115">[AddDefaultIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionuiextensions.adddefaultidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionUIExtensions_AddDefaultIdentity__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__) was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="42e3d-116">Volání `AddDefaultIdentity` je podobná následující volání:</span><span class="sxs-lookup"><span data-stu-id="42e3d-116">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* [<span data-ttu-id="42e3d-117">AddIdentity</span><span class="sxs-lookup"><span data-stu-id="42e3d-117">AddIdentity</span></span>](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.addidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_AddIdentity__2_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__)
* [<span data-ttu-id="42e3d-118">AddDefaultUI</span><span class="sxs-lookup"><span data-stu-id="42e3d-118">AddDefaultUI</span></span>](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderuiextensions.adddefaultui?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderUIExtensions_AddDefaultUI_Microsoft_AspNetCore_Identity_IdentityBuilder_)
* [<span data-ttu-id="42e3d-119">AddDefaultTokenProviders</span><span class="sxs-lookup"><span data-stu-id="42e3d-119">AddDefaultTokenProviders</span></span>](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderextensions.adddefaulttokenproviders?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderExtensions_AddDefaultTokenProviders_Microsoft_AspNetCore_Identity_IdentityBuilder_)

<span data-ttu-id="42e3d-120">Zobrazit [AddDefaultIdentity zdroj](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) Další informace.</span><span class="sxs-lookup"><span data-stu-id="42e3d-120">See [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

::: moniker-end

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="42e3d-121">Vytvoření webové aplikace s ověřováním</span><span class="sxs-lookup"><span data-stu-id="42e3d-121">Create a Web app with authentication</span></span>

<span data-ttu-id="42e3d-122">Vytvoření projektu webové aplikace ASP.NET Core s jednotlivými uživatelskými účty.</span><span class="sxs-lookup"><span data-stu-id="42e3d-122">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="42e3d-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="42e3d-123">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="42e3d-124">Vyberte **Soubor** > **Nový** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="42e3d-124">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="42e3d-125">Vyberte **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="42e3d-125">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="42e3d-126">Pojmenujte projekt **WebApp1** stejný obor názvů jako projekt soubor ke stažení.</span><span class="sxs-lookup"><span data-stu-id="42e3d-126">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="42e3d-127">Klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="42e3d-127">Click **OK**.</span></span>
* <span data-ttu-id="42e3d-128">Vyberte v ASP.NET Core **webovou aplikaci**a pak vyberte **změna ověřování**.</span><span class="sxs-lookup"><span data-stu-id="42e3d-128">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="42e3d-129">Vyberte **jednotlivé uživatelské účty** a klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="42e3d-129">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="42e3d-130">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="42e3d-130">.NET Core CLI</span></span>](#tab/netcore-cli)

```cli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="42e3d-131">Generovaný projekt poskytuje [ASP.NET Core Identity](xref:security/authentication/identity) jako [knihovny tříd Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="42e3d-131">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="42e3d-132">Knihovny tříd Razor Identity zveřejňuje koncové body se `Identity` oblasti.</span><span class="sxs-lookup"><span data-stu-id="42e3d-132">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="42e3d-133">Příklad:</span><span class="sxs-lookup"><span data-stu-id="42e3d-133">For example:</span></span>

* <span data-ttu-id="42e3d-134">/ Identity / / přihlášení k účtu</span><span class="sxs-lookup"><span data-stu-id="42e3d-134">/Identity/Account/Login</span></span>
* <span data-ttu-id="42e3d-135">/ Identity/účet a odhlášení</span><span class="sxs-lookup"><span data-stu-id="42e3d-135">/Identity/Account/Logout</span></span>
* <span data-ttu-id="42e3d-136">/ Identity/účet a spravovat</span><span class="sxs-lookup"><span data-stu-id="42e3d-136">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="42e3d-137">Použití migrace</span><span class="sxs-lookup"><span data-stu-id="42e3d-137">Apply migrations</span></span>

<span data-ttu-id="42e3d-138">Použití migrace inicializovat databázi.</span><span class="sxs-lookup"><span data-stu-id="42e3d-138">Apply the migrations to initialise the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="42e3d-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="42e3d-139">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="42e3d-140">Spuštěním následujícího příkazu v konzole Správce balíčků (PMC):</span><span class="sxs-lookup"><span data-stu-id="42e3d-140">Run the following command in the Package Manager Console (PMC):</span></span>

```PM> Update-Database```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="42e3d-141">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="42e3d-141">.NET Core CLI</span></span>](#tab/netcore-cli)

```cli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="42e3d-142">Test registrace a přihlášení</span><span class="sxs-lookup"><span data-stu-id="42e3d-142">Test Register and Login</span></span>

<span data-ttu-id="42e3d-143">Spusťte aplikaci a zaregistrovat uživatele.</span><span class="sxs-lookup"><span data-stu-id="42e3d-143">Run the app and register a user.</span></span> <span data-ttu-id="42e3d-144">V závislosti na velikost obrazovky může být nutné vybrat navigace přepínacího tlačítka zobrazíte **zaregistrovat** a **přihlášení** odkazy.</span><span class="sxs-lookup"><span data-stu-id="42e3d-144">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="42e3d-145">Konfigurace Identity služby</span><span class="sxs-lookup"><span data-stu-id="42e3d-145">Configure Identity services</span></span>

<span data-ttu-id="42e3d-146">Služby jsou přidány v `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="42e3d-146">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="42e3d-147">Typický vzor je volat všechny `Add{Service}` metody a poté zavolejte všechny `services.Configure{Service}` metody.</span><span class="sxs-lookup"><span data-stu-id="42e3d-147">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="42e3d-148">Předchozí kód konfiguruje Identity s výchozími hodnotami. možnost.</span><span class="sxs-lookup"><span data-stu-id="42e3d-148">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="42e3d-149">Služby jsou k dispozici pro aplikaci přes [injektáž závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="42e3d-149">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

   <span data-ttu-id="42e3d-150">Povolení identity voláním [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="42e3d-150">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="42e3d-151">`UseAuthentication` Přidá ověřování [middleware](xref:fundamentals/middleware/index) požadavku kanálu.</span><span class="sxs-lookup"><span data-stu-id="42e3d-151">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

   [!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,11-28,30-42)]

   <span data-ttu-id="42e3d-152">Služby jsou k dispozici pro aplikace prostřednictvím [injektáž závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="42e3d-152">Services are made available to the application through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

   <span data-ttu-id="42e3d-153">Povolení identity pro aplikace po zavolání `UseAuthentication` v `Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="42e3d-153">Identity is enabled for the application by calling `UseAuthentication` in the `Configure` method.</span></span> <span data-ttu-id="42e3d-154">`UseAuthentication` Přidá ověřování [middleware](xref:fundamentals/middleware/index) požadavku kanálu.</span><span class="sxs-lookup"><span data-stu-id="42e3d-154">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configure&highlight=17)]

::: moniker-end

::: moniker range="= aspnetcore-1.1"

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,13-33)]

   <span data-ttu-id="42e3d-155">Tyto služby jsou k dispozici pro aplikace prostřednictvím [injektáž závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="42e3d-155">These services are made available to the application through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

   <span data-ttu-id="42e3d-156">Povolení identity pro aplikace po zavolání `UseIdentity` v `Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="42e3d-156">Identity is enabled for the application by calling `UseIdentity` in the `Configure` method.</span></span> <span data-ttu-id="42e3d-157">`UseIdentity` Přidá na základě souboru cookie ověřování [middleware](xref:fundamentals/middleware/index) požadavku kanálu.</span><span class="sxs-lookup"><span data-stu-id="42e3d-157">`UseIdentity` adds cookie-based authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configure&highlight=21)]

::: moniker-end

<span data-ttu-id="42e3d-158">Další informace najdete v tématu [IdentityOptions třídy](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) a [spuštění aplikace](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="42e3d-158">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="42e3d-159">Vygenerované uživatelské rozhraní registrace, přihlášení a odhlášení</span><span class="sxs-lookup"><span data-stu-id="42e3d-159">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="42e3d-160">Postupujte podle [generování uživatelského rozhraní identity do projektu Razor s autorizací](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) pokyny ke generování kódu zobrazeného v této části.</span><span class="sxs-lookup"><span data-stu-id="42e3d-160">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="42e3d-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="42e3d-161">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="42e3d-162">Přidáte soubory registrace, přihlášení a odhlášení.</span><span class="sxs-lookup"><span data-stu-id="42e3d-162">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="42e3d-163">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="42e3d-163">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="42e3d-164">Pokud jste vytvořili projekt s názvem **WebApp1**, spusťte následující příkazy.</span><span class="sxs-lookup"><span data-stu-id="42e3d-164">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="42e3d-165">Jinak použijte správný obor názvů pro `ApplicationDbContext`:</span><span class="sxs-lookup"><span data-stu-id="42e3d-165">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```cli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="42e3d-166">PowerShell používá jako oddělovač příkazu středník.</span><span class="sxs-lookup"><span data-stu-id="42e3d-166">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="42e3d-167">Při použití prostředí PowerShell, řídicí středníky v seznamu souborů nebo vložit seznam souborů v dvojitých uvozovkách, jak ukazuje předchozí příklad.</span><span class="sxs-lookup"><span data-stu-id="42e3d-167">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="42e3d-168">Prozkoumejte registru</span><span class="sxs-lookup"><span data-stu-id="42e3d-168">Examine Register</span></span>

::: moniker range=">= aspnetcore-2.1"

   <span data-ttu-id="42e3d-169">Když uživatel klikne **zaregistrovat** odkaz, `RegisterModel.OnPostAsync` vyvolání akce.</span><span class="sxs-lookup"><span data-stu-id="42e3d-169">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="42e3d-170">Uživatel vytvoří [asynchronně](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) na `_userManager` objektu.</span><span class="sxs-lookup"><span data-stu-id="42e3d-170">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="42e3d-171">`_userManager` poskytuje injektáž závislostí):</span><span class="sxs-lookup"><span data-stu-id="42e3d-171">`_userManager` is provided by dependency injection):</span></span>

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7,22)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   <span data-ttu-id="42e3d-172">Když uživatel klikne **zaregistrovat** odkaz, `Register` akce je volána na `AccountController`.</span><span class="sxs-lookup"><span data-stu-id="42e3d-172">When a user clicks the **Register** link, the `Register` action is invoked on `AccountController`.</span></span> <span data-ttu-id="42e3d-173">`Register` Akce vytvoří uživatele voláním `CreateAsync` na `_userManager` objektu (poskytnuté `AccountController` pomocí vkládání závislostí):</span><span class="sxs-lookup"><span data-stu-id="42e3d-173">The `Register` action creates the user by calling `CreateAsync` on the `_userManager` object (provided to `AccountController` by dependency injection):</span></span>

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_register&highlight=11)]

::: moniker-end

   <span data-ttu-id="42e3d-174">Pokud byl uživatel vytvořen úspěšně, uživatel je přihlášen voláním `_signInManager.SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="42e3d-174">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

   <span data-ttu-id="42e3d-175">**Poznámka:** Zobrazit [účtu potvrzení](xref:security/authentication/accconfirm#prevent-login-at-registration) kroky, jak zabránit okamžité přihlášení při registraci.</span><span class="sxs-lookup"><span data-stu-id="42e3d-175">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="42e3d-176">Přihlásit se</span><span class="sxs-lookup"><span data-stu-id="42e3d-176">Log in</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="42e3d-177">Přihlašovací formulář se zobrazí při:</span><span class="sxs-lookup"><span data-stu-id="42e3d-177">The Login form is displayed when:</span></span>

* <span data-ttu-id="42e3d-178">**Přihlášení** vybraný odkaz.</span><span class="sxs-lookup"><span data-stu-id="42e3d-178">The **Log in** link is selected.</span></span>
* <span data-ttu-id="42e3d-179">Uživatel se pokusí otevřít stránku s omezeným přístupem, který uživatel nemá oprávnění k přístupu **nebo** při nebyly byl ověřen systémem.</span><span class="sxs-lookup"><span data-stu-id="42e3d-179">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="42e3d-180">Když se odešle formulář na přihlašovací stránku, `OnPostAsync` akce je volána.</span><span class="sxs-lookup"><span data-stu-id="42e3d-180">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="42e3d-181">`PasswordSignInAsync` je volán na `_signInManager` objektu (postkytovatel: injektáž závislostí).</span><span class="sxs-lookup"><span data-stu-id="42e3d-181">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

   <span data-ttu-id="42e3d-182">Základní `Controller` třídy zpřístupňuje `User` vlastnost, která se dá dostat z metody kontroleru.</span><span class="sxs-lookup"><span data-stu-id="42e3d-182">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="42e3d-183">Například můžete zobrazit výčet `User.Claims` a rozhodnutí o autorizaci.</span><span class="sxs-lookup"><span data-stu-id="42e3d-183">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="42e3d-184">Další informace naleznete v tématu <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="42e3d-184">For more information, see <xref:security/authorization/introduction>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="42e3d-185">Přihlašovací formulář se zobrazí, když uživatelé vyberou **přihlášení** propojení nebo přesměrováni při přístupu ke stránce, která vyžaduje ověření.</span><span class="sxs-lookup"><span data-stu-id="42e3d-185">The Login form is displayed when users select the **Log in** link or are redirected when accessing a page that requires authentication.</span></span> <span data-ttu-id="42e3d-186">Když uživatel odešle formulář na přihlašovací stránku, `AccountController` `Login` akce je volána.</span><span class="sxs-lookup"><span data-stu-id="42e3d-186">When the user submits the form on the Login page, the `AccountController` `Login` action is called.</span></span>

<span data-ttu-id="42e3d-187">`Login` Volání akce `PasswordSignInAsync` na `_signInManager` objektu (poskytnuté `AccountController` pomocí vkládání závislostí).</span><span class="sxs-lookup"><span data-stu-id="42e3d-187">The `Login` action calls `PasswordSignInAsync` on the `_signInManager` object (provided to `AccountController` by dependency injection).</span></span>

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_login&highlight=13-14)]

<span data-ttu-id="42e3d-188">Základní (`Controller` nebo `PageModel`) třídy zpřístupňuje `User` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="42e3d-188">The base (`Controller` or `PageModel`) class exposes a `User` property.</span></span> <span data-ttu-id="42e3d-189">Například `User.Claims` může být přezkoumána za účelem rozhodnutí o autorizaci.</span><span class="sxs-lookup"><span data-stu-id="42e3d-189">For example, `User.Claims` can be enumerated to make authorization decisions.</span></span>

::: moniker-end

### <a name="log-out"></a><span data-ttu-id="42e3d-190">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="42e3d-190">Log out</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="42e3d-191">**Odhlásit** vyvolá tento odkaz `LogoutModel.OnPost` akce.</span><span class="sxs-lookup"><span data-stu-id="42e3d-191">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="42e3d-192">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) vymaže deklarací identity uživatele uložené v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="42e3d-192">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span> <span data-ttu-id="42e3d-193">Není přesměrování po volání `SignOutAsync` nebo uživatel uvidí **není** odhlásit.</span><span class="sxs-lookup"><span data-stu-id="42e3d-193">Don't redirect after calling `SignOutAsync` or the user will **not** be signed out.</span></span>

<span data-ttu-id="42e3d-194">Příspěvek je zadán v *Pages/Shared/_LoginPartial.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="42e3d-194">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   <span data-ttu-id="42e3d-195">Kliknutím **Odhlásit** propojit volání `LogOut` akce.</span><span class="sxs-lookup"><span data-stu-id="42e3d-195">Clicking the **Log out** link calls the `LogOut` action.</span></span>

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_logout&highlight=7)]

   <span data-ttu-id="42e3d-196">Předchozí kód volá `_signInManager.SignOutAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="42e3d-196">The preceding code calls the `_signInManager.SignOutAsync` method.</span></span> <span data-ttu-id="42e3d-197">`SignOutAsync` Metoda vymaže deklarací identity uživatele uložené v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="42e3d-197">The `SignOutAsync` method clears the user's claims stored in a cookie.</span></span>

::: moniker-end

## <a name="test-identity"></a><span data-ttu-id="42e3d-198">Otestování Identity</span><span class="sxs-lookup"><span data-stu-id="42e3d-198">Test Identity</span></span>

<span data-ttu-id="42e3d-199">Šablony webových projektů výchozí povolit anonymní přístup k domovské stránky.</span><span class="sxs-lookup"><span data-stu-id="42e3d-199">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="42e3d-200">K otestování Identity, přidejte [ `[Authorize]` ](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) na stránce o ochraně osobních údajů.</span><span class="sxs-lookup"><span data-stu-id="42e3d-200">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=6)]

<span data-ttu-id="42e3d-201">Pokud jste přihlášeni, odhlaste se. Spusťte aplikaci a vyberte **ochrany osobních údajů** odkaz.</span><span class="sxs-lookup"><span data-stu-id="42e3d-201">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="42e3d-202">Budete přesměrováni na přihlašovací stránku.</span><span class="sxs-lookup"><span data-stu-id="42e3d-202">You are redirected to the login page.</span></span>

::: moniker range=">= aspnetcore-2.1"

### <a name="explore-identity"></a><span data-ttu-id="42e3d-203">Prozkoumejte službu Identity</span><span class="sxs-lookup"><span data-stu-id="42e3d-203">Explore Identity</span></span>

<span data-ttu-id="42e3d-204">Identita prozkoumat podrobněji:</span><span class="sxs-lookup"><span data-stu-id="42e3d-204">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="42e3d-205">Vytvoření zdroje plnou identitou uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="42e3d-205">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="42e3d-206">Zkontrolujte příčiny jednotlivé stránky a projít ladicí program.</span><span class="sxs-lookup"><span data-stu-id="42e3d-206">Examine the source of each page and step through the debugger.</span></span>

::: moniker-end

## <a name="identity-components"></a><span data-ttu-id="42e3d-207">Komponenty identit</span><span class="sxs-lookup"><span data-stu-id="42e3d-207">Identity Components</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="42e3d-208">Všechny Identity závislé balíčky NuGet jsou součástí [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="42e3d-208">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="42e3d-209">Primární balíček pro identitu [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="42e3d-209">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="42e3d-210">Tento balíček obsahuje základní sadu rozhraní pro ASP.NET Core Identity a je součástí `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="42e3d-210">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="42e3d-211">Migrace do ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="42e3d-211">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="42e3d-212">Další informace a pokyny k migraci vaší existující úložiště identit najdete v tématu [migrovat ověřování a identita](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="42e3d-212">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="42e3d-213">Nastavení síly hesla</span><span class="sxs-lookup"><span data-stu-id="42e3d-213">Setting password strength</span></span>

<span data-ttu-id="42e3d-214">Zobrazit [konfigurace](#pw) ukázku, která nastavuje minimální požadavky.</span><span class="sxs-lookup"><span data-stu-id="42e3d-214">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="42e3d-215">Další kroky</span><span class="sxs-lookup"><span data-stu-id="42e3d-215">Next Steps</span></span>

* [<span data-ttu-id="42e3d-216">Konfigurace systému Identity</span><span class="sxs-lookup"><span data-stu-id="42e3d-216">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

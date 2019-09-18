---
title: Úvod do identity na ASP.NET Core
author: rick-anderson
description: Použijte identitu s aplikací ASP.NET Core. Přečtěte si, jak nastavit požadavky na heslo (RequireDigit, RequiredLength, RequiredUniqueChars a další).
ms.author: riande
ms.date: 03/26/2019
uid: security/authentication/identity
ms.openlocfilehash: 979681cfc196aca9fb5097583d99a086e1c597ba
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082450"
---
# <a name="introduction-to-identity-on-aspnet-core"></a>Úvod do identity na ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core identity je systém členství, který do ASP.NET Core aplikací přidává funkce přihlášení. Uživatelé můžou vytvořit účet s přihlašovacími informacemi uloženými v identitě nebo můžou použít externího poskytovatele přihlášení. Mezi podporované externí zprostředkovatelé přihlášení patří [Facebook, Google, účet Microsoft a Twitter](xref:security/authentication/social/index).

Identitu můžete nakonfigurovat pomocí SQL Server databáze pro ukládání uživatelských jmen, hesel a profilových dat. Případně můžete použít jiné trvalé úložiště, například Azure Table Storage.

[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([stažení)](xref:index#how-to-download-a-sample)).

V tomto tématu se dozvíte, jak používat identitu k registraci, přihlášení a odhlášení uživatele. Podrobnější pokyny k vytváření aplikací, které používají identitu, najdete v části Další kroky na konci tohoto článku.

::: moniker range=">= aspnetcore-2.1"

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a>AddDefaultIdentity a AddIdentity

[AddDefaultIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionuiextensions.adddefaultidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionUIExtensions_AddDefaultIdentity__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__) byl představen v ASP.NET Core 2,1. Volání `AddDefaultIdentity` je podobné volání následujícího:

* [AddIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.addidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_AddIdentity__2_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__)
* [AddDefaultUI](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderuiextensions.adddefaultui?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderUIExtensions_AddDefaultUI_Microsoft_AspNetCore_Identity_IdentityBuilder_)
* [AddDefaultTokenProviders](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderextensions.adddefaulttokenproviders?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderExtensions_AddDefaultTokenProviders_Microsoft_AspNetCore_Identity_IdentityBuilder_)

Další informace najdete v tématu [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .

::: moniker-end

## <a name="create-a-web-app-with-authentication"></a>Vytvoření webové aplikace s ověřováním

Vytvořte ASP.NET Core projekt webové aplikace s jednotlivými uživatelskými účty.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Vyberte **Soubor** > **Nový** > **Projekt**.
* Vyberte **webová aplikace ASP.NET Core**. Pojmenujte projekt **WebApp1** tak, aby měl stejný obor názvů jako projekt ke stažení. Klikněte na **OK**.
* Vyberte **webovou aplikaci**ASP.NET Core a pak vyberte **změnit ověřování**.
* Vyberte **jednotlivé uživatelské účty** a klikněte na **OK**.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

Vygenerovaný projekt poskytuje [ASP.NET Coreou identitu](xref:security/authentication/identity) jako [knihovnu tříd Razor](xref:razor-pages/ui-class). Knihovna tříd identity Razor zpřístupňuje koncové body s `Identity` oblastí. Příklad:

* /Identity/Account/Login
* /Identity/Account/Logout
* /Identity/Account/Manage

### <a name="apply-migrations"></a>Použít migrace

Použijte migrace pro inicializovat databáze.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Spusťte následující příkaz v konzole správce balíčků (PMC):

```PM> Update-Database```

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>Registrace a přihlášení testu

Spusťte aplikaci a zaregistrujte uživatele. V závislosti na velikosti obrazovky možná budete muset vybrat přepínač navigace a zobrazit tak odkazy na **registraci** a **přihlášení** .

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a>Konfigurace služby identity

Služby jsou přidány do `ConfigureServices`. Typický vzor je zavolat všechny `Add{Service}` metody a pak zavolat `services.Configure{Service}` všechny metody.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

Předchozí kód nakonfiguruje identitu s výchozími hodnotami možností. Služby jsou zpřístupněny aplikaci prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).

   Identita je povolena voláním [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_). `UseAuthentication`přidá [middleware](xref:fundamentals/middleware/index) ověřování do kanálu požadavků.

   [!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,11-28,30-42)]

   Služby jsou zpřístupněny aplikaci prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).

   Pro aplikaci je povolena identita voláním `UseAuthentication` `Configure` metody. `UseAuthentication`přidá [middleware](xref:fundamentals/middleware/index) ověřování do kanálu požadavků.

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configure&highlight=17)]

::: moniker-end

::: moniker range="= aspnetcore-1.1"

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,13-33)]

   Tyto služby jsou zpřístupněny aplikaci prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).

   Pro aplikaci je povolena identita voláním `UseIdentity` `Configure` metody. `UseIdentity`Přidá do kanálu žádosti [middleware](xref:fundamentals/middleware/index) ověřování na základě souborů cookie.

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configure&highlight=21)]

::: moniker-end

Další informace naleznete v tématu [Třída IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) a [spuštění aplikace](xref:fundamentals/startup).

## <a name="scaffold-register-login-and-logout"></a>Registrace, přihlášení a odhlášení uživatelského rozhraní

Použijte [identitu uživatelského rozhraní do projektu Razor s pokyny k autorizaci](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) a vygenerujte kód uvedený v této části.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Přidejte soubory registru, přihlášení a odhlášení.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Pokud jste vytvořili projekt s názvem **WebApp1**, spusťte následující příkazy. Jinak použijte správný obor názvů pro `ApplicationDbContext`:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

PowerShell používá jako oddělovač příkazů středník. Při použití prostředí PowerShell, oddělte středníky v seznamu souborů nebo vložte seznam souborů do dvojitých uvozovek, jak ukazuje předchozí příklad.

---

### <a name="examine-register"></a>Ověřit registraci

::: moniker range=">= aspnetcore-2.1"

   Když uživatel klikne na odkaz **zaregistrovat** , `RegisterModel.OnPostAsync` je akce vyvolána. Uživatel je vytvořen pomocí [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) na `_userManager` objektu. `_userManager`je poskytována vkládáním závislostí):

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7,22)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   Když uživatel klikne na odkaz **zaregistrovat** , `Register` `AccountController`akce se vyvolá. Akce vytvoří uživatele `_userManager` voláním `AccountController` objektu (poskytnutého vkládáním závislostí): `CreateAsync` `Register`

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_register&highlight=11)]

::: moniker-end

   Pokud byl uživatel vytvořen úspěšně, uživatel je přihlášen voláním `_signInManager.SignInAsync`.

   **Poznámka:** Postup, jak zabránit okamžitému přihlášení při registraci, najdete v tématu [potvrzení účtu](xref:security/authentication/accconfirm#prevent-login-at-registration) .

### <a name="log-in"></a>Přihlásit se

::: moniker range=">= aspnetcore-2.1"

Přihlašovací formulář se zobrazí v těchto případech:

* Je vybrán odkaz **Přihlásit** se.
* Uživatel se pokusí získat přístup ke stránce s omezením, že nemá oprávnění k přístupu, **nebo** když ho systém neověřil.

Při odeslání `OnPostAsync` formuláře na přihlašovací stránce se zavolá akce. `PasswordSignInAsync`je volána u `_signInManager` objektu (poskytovaného vkládáním závislostí).

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

   Základní `Controller` Třída`User` zpřístupňuje vlastnost, ke které máte přístup z metod kontroleru. Můžete například vytvořit výčet `User.Claims` a provést rozhodnutí o autorizaci. Další informace naleznete v tématu <xref:security/authorization/introduction>.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Přihlašovací formulář se zobrazí, když uživatel vybere odkaz **Přihlásit** nebo se přesměruje při přístupu ke stránce, která vyžaduje ověření. Když uživatel formulář odešle na přihlašovací stránce, `AccountController` `Login` je volána akce.

Akce volá `PasswordSignInAsync` objekt(`AccountController` poskytnutý pomocí injektáže závislosti). `_signInManager` `Login`

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_login&highlight=13-14)]

Třída Base (`Controller` nebo `PageModel`) zpřístupňuje `User` vlastnost. `User.Claims` Můžete například vytvořit výčet pro rozhodování o autorizacích.

::: moniker-end

### <a name="log-out"></a>Odhlásit se

::: moniker range=">= aspnetcore-2.1"

Odkaz **odhlášení** vyvolá `LogoutModel.OnPost` akci. 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) vymaže deklarace identity uživatele uložené v souboru cookie.

Příspěvek je zadán ve *stránkách/Shared/_LoginPartial. cshtml*:

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   Kliknutím na odkaz **Odhlásit** `LogOut` akce zavoláte akci.

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_logout&highlight=7)]

   Předchozí kód volá `_signInManager.SignOutAsync` metodu. `SignOutAsync` Metoda vymaže deklarace identity uživatele uložené v souboru cookie.

::: moniker-end

## <a name="test-identity"></a>Test identity

Výchozí šablony webového projektu umožňují anonymní přístup k domovské stránce. Chcete-li otestovat identitu [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) , přidejte na stránku soukromí.

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=6)]

Pokud jste přihlášeni, odhlaste se. Spusťte aplikaci a vyberte odkaz na **ochranu osobních údajů** . Budete přesměrováni na přihlašovací stránku.

::: moniker range=">= aspnetcore-2.1"

### <a name="explore-identity"></a>Prozkoumat identitu

Podrobněji prozkoumat identitu:

* [Vytvořit úplný zdroj uživatelského rozhraní identity](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Projděte si zdroj každé stránky a proveďte krok pomocí ladicího programu.

::: moniker-end

## <a name="identity-components"></a>Komponenty identity

::: moniker range=">= aspnetcore-2.1"

Všechny balíčky NuGet závislé na identitě jsou zahrnuté ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).

::: moniker-end

Primárním balíčkem identity je [Microsoft. AspNetCore. identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/). Tento balíček obsahuje základní sadu rozhraní pro ASP.NET Core identitu a je zahrnutý v `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.

## <a name="migrating-to-aspnet-core-identity"></a>Migrace na ASP.NET Core identity

Další informace a pokyny k migraci stávajícího úložiště identit najdete v tématu [migrace ověřování a identity](xref:migration/identity).

## <a name="setting-password-strength"></a>Nastavení síly hesla

V části [Konfigurace](#pw) najdete ukázku, která nastavuje minimální požadavky na heslo.

## <a name="next-steps"></a>Další kroky

* [Konfigurace systému Identity](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

---
title: Úvod do identity na ASP.NET Core
author: rick-anderson
description: Použijte identitu s aplikací ASP.NET Core. Přečtěte si, jak nastavit požadavky na heslo (RequireDigit, RequiredLength, RequiredUniqueChars a další).
ms.author: riande
ms.date: 12/05/2019
uid: security/authentication/identity
ms.openlocfilehash: 787d39dd7824f912128e6af849fa268c3e8eb908
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75359196"
---
# <a name="introduction-to-identity-on-aspnet-core"></a>Úvod do identity na ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core identita:

* Je rozhraní API, které podporuje funkce přihlášení uživatelského rozhraní (UI).
* Spravuje uživatele, hesla, data profilu, role, deklarace identity, tokeny, potvrzení e-mailu a další.

Uživatelé můžou vytvořit účet s přihlašovacími informacemi uloženými v identitě nebo můžou použít externího poskytovatele přihlášení. Mezi podporované externí zprostředkovatelé přihlášení patří [Facebook, Google, účet Microsoft a Twitter](xref:security/authentication/social/index).

[Zdrojový kód identity](https://github.com/aspnet/AspNetCore/tree/master/src/Identity) je k dispozici na GitHubu. [Identita uživatelského rozhraní](xref:security/authentication/scaffold-identity) a zobrazení vygenerovaných souborů pro kontrolu interakce šablony s identitou.

Identita je obvykle nakonfigurovaná pomocí SQL Server databáze pro ukládání uživatelských jmen, hesel a profilových dat. Případně můžete použít jiné trvalé úložiště, například Azure Table Storage.

V tomto tématu se dozvíte, jak používat identitu k registraci, přihlášení a odhlášení uživatele. Podrobnější pokyny k vytváření aplikací, které používají identitu, najdete v části Další kroky na konci tohoto článku.

[Platforma Microsoft Identity](/azure/active-directory/develop/) je:

* Vývoj platformy pro vývojáře Azure Active Directory (Azure AD).
* Nesouvisí se ASP.NET Core identitou.

[!INCLUDE[](~/includes/IdentityServer4.md)]

[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([Jak stáhnout)](xref:index#how-to-download-a-sample)).

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a>Vytvoření webové aplikace s ověřováním

Vytvořte ASP.NET Core projekt webové aplikace s jednotlivými uživatelskými účty.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Vyberte **soubor** > **Nový** > **projekt**.
* Vyberte **webová aplikace ASP.NET Core**. Pojmenujte projekt **WebApp1** tak, aby měl stejný obor názvů jako projekt ke stažení. Klikněte na tlačítko **OK**.
* Vyberte **webovou aplikaci**ASP.NET Core a pak vyberte **změnit ověřování**.
* Vyberte **jednotlivé uživatelské účty** a klikněte na **OK**.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

Předchozí příkaz vytvoří webovou aplikaci Razor pomocí SQLite. Pokud chcete vytvořit webovou aplikaci pomocí LocalDB, spusťte následující příkaz:

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

Vygenerovaný projekt poskytuje [ASP.NET Coreou identitu](xref:security/authentication/identity) jako [knihovnu tříd Razor](xref:razor-pages/ui-class). Knihovna tříd identity Razor zpřístupňuje koncové body s oblastí `Identity`. Příklad:

* /Identity/Account/Login
* /Identity/Account/Logout
* /Identity/Account/Manage

### <a name="apply-migrations"></a>Použít migrace

Použijte migrace k inicializaci databáze.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Spusťte následující příkaz v konzole správce balíčků (PMC):

`PM> Update-Database`

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Migrace nejsou v tomto kroku nutné při použití SQLite. V případě LocalDB spusťte následující příkaz:

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>Registrace a přihlášení testu

Spusťte aplikaci a zaregistrujte uživatele. V závislosti na velikosti obrazovky možná budete muset vybrat přepínač navigace a zobrazit tak odkazy na **registraci** a **přihlášení** .

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a>Konfigurace služby identity

Služby se přidávají v `ConfigureServices`. Typický vzor je zavolat všechny metody `Add{Service}` a potom zavolat všechny metody `services.Configure{Service}`.

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

Předchozí zvýrazněný kód nakonfiguruje identitu s výchozími hodnotami možností. Služby jsou zpřístupněny aplikaci prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).

Identita je povolena voláním <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>. `UseAuthentication` do kanálu požadavků přidá [middleware](xref:fundamentals/middleware/index) ověřování.

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

Aplikace generovaná šablonou nepoužívá [autorizaci](xref:security/authorization/secure-data). `app.UseAuthorization` je součástí, aby se zajistilo, že je přidaný do správného pořadí, aby aplikace přidala autorizaci. `UseRouting`, `UseAuthentication`, `UseAuthorization`a `UseEndpoints` musí být volány v pořadí uvedeném v předchozím kódu.

Další informace o `IdentityOptions` a `Startup`najdete v tématu <xref:Microsoft.AspNetCore.Identity.IdentityOptions> a [spuštění aplikace](xref:fundamentals/startup).

## <a name="scaffold-register-login-and-logout"></a>Registrace, přihlášení a odhlášení uživatelského rozhraní

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Přidejte soubory registru, přihlášení a odhlášení. Použijte [identitu uživatelského rozhraní do projektu Razor s pokyny k autorizaci](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) a vygenerujte kód uvedený v této části.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Pokud jste vytvořili projekt s názvem **WebApp1**, spusťte následující příkazy. Jinak použijte správný obor názvů pro `ApplicationDbContext`:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

PowerShell používá jako oddělovač příkazů středník. Při použití prostředí PowerShell, oddělte středníky v seznamu souborů nebo vložte seznam souborů do dvojitých uvozovek, jak ukazuje předchozí příklad.

Další informace o identitě uživatelského rozhraní naleznete v tématu [Identita uživatelského rozhraní do projektu Razor s autorizací](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).

---

### <a name="examine-register"></a>Ověřit registraci

Když uživatel klikne na odkaz **zaregistrovat** , je vyvolána akce `RegisterModel.OnPostAsync`. Uživatel je vytvořen pomocí [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) na objektu `_userManager`. `_userManager` poskytuje vkládání závislostí):

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

Pokud byl uživatel vytvořen úspěšně, uživatel je přihlášen voláním `_signInManager.SignInAsync`.

Postup, jak zabránit okamžitému přihlášení při registraci, najdete v tématu [potvrzení účtu](xref:security/authentication/accconfirm#prevent-login-at-registration) .

### <a name="log-in"></a>Log in

Přihlašovací formulář se zobrazí v těchto případech:

* Je vybrán odkaz **Přihlásit** se.
* Uživatel se pokusí získat přístup ke stránce s omezením, že nemá oprávnění k přístupu, **nebo** když ho systém neověřil.

Při odeslání formuláře na přihlašovací stránce se zavolá akce `OnPostAsync`. `PasswordSignInAsync` se volá na objekt `_signInManager` (poskytnutý vkládáním závislostí).

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

Základní `Controller` třída zpřístupňuje vlastnost `User`, ke které lze dostat z metod kontroleru. Můžete například vytvořit výčet `User.Claims` a provést autorizační rozhodnutí. Další informace najdete v tématu <xref:security/authorization/introduction>.

### <a name="log-out"></a>Odhlásit se

Odkaz **Odhlásit** se vyvolá `LogoutModel.OnPost` akci. 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

V předchozím kódu musí být kód `return RedirectToPage();` přesměrování, aby prohlížeč prováděl novou žádost a identita pro uživatele byla aktualizována.

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) vymaže deklarace identity uživatele uložené v souboru cookie.

Příspěvek je zadán ve *stránkách/Shared/_LoginPartial. cshtml*:

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a>Test identity

Výchozí šablony webového projektu umožňují anonymní přístup k domovské stránce. K otestování identity přidejte [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

Pokud jste přihlášeni, odhlaste se. Spusťte aplikaci a vyberte odkaz na **ochranu osobních údajů** . Budete přesměrováni na přihlašovací stránku.

### <a name="explore-identity"></a>Prozkoumat identitu

Podrobněji prozkoumat identitu:

* [Vytvořit úplný zdroj uživatelského rozhraní identity](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Projděte si zdroj každé stránky a proveďte krok pomocí ladicího programu.

## <a name="identity-components"></a>Komponenty identity

Všechny balíčky NuGet závislé na identitách jsou součástí [ASP.NET Core sdílené rozhraní](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).

Primárním balíčkem identity je [Microsoft. AspNetCore. identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/). Tento balíček obsahuje základní sadu rozhraní pro ASP.NET Core identitu a je zahrnutý v `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.

## <a name="migrating-to-aspnet-core-identity"></a>Migrace na ASP.NET Core identity

Další informace a pokyny k migraci stávajícího úložiště identit najdete v tématu [migrace ověřování a identity](xref:migration/identity).

## <a name="setting-password-strength"></a>Nastavení síly hesla

V části [Konfigurace](#pw) najdete ukázku, která nastavuje minimální požadavky na heslo.

## <a name="adddefaultidentity-and-addidentity"></a>AddDefaultIdentity a AddIdentity

<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> byla představena v ASP.NET Core 2,1. Volání `AddDefaultIdentity` je podobné volání následujícího:

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

Další informace najdete v tématu [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .

## <a name="next-steps"></a>Další kroky

* Informace o konfiguraci identity pomocí SQLite najdete v [tomto problému GitHubu](https://github.com/aspnet/AspNetCore.Docs/issues/5131) .
* [Konfigurace systému Identity](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core identity je systém členství, který do ASP.NET Core aplikací přidává funkce přihlášení. Uživatelé můžou vytvořit účet s přihlašovacími informacemi uloženými v identitě nebo můžou použít externího poskytovatele přihlášení. Mezi podporované externí zprostředkovatelé přihlášení patří [Facebook, Google, účet Microsoft a Twitter](xref:security/authentication/social/index).

Identitu můžete nakonfigurovat pomocí SQL Server databáze pro ukládání uživatelských jmen, hesel a profilových dat. Případně můžete použít jiné trvalé úložiště, například Azure Table Storage.

[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([Jak stáhnout)](xref:index#how-to-download-a-sample)).

V tomto tématu se dozvíte, jak používat identitu k registraci, přihlášení a odhlášení uživatele. Podrobnější pokyny k vytváření aplikací, které používají identitu, najdete v části Další kroky na konci tohoto článku.

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a>AddDefaultIdentity a AddIdentity

<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> byla představena v ASP.NET Core 2,1. Volání `AddDefaultIdentity` je podobné volání následujícího:

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

Další informace najdete v tématu [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .

## <a name="create-a-web-app-with-authentication"></a>Vytvoření webové aplikace s ověřováním

Vytvořte ASP.NET Core projekt webové aplikace s jednotlivými uživatelskými účty.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Vyberte **soubor** > **Nový** > **projekt**.
* Vyberte **webová aplikace ASP.NET Core**. Pojmenujte projekt **WebApp1** tak, aby měl stejný obor názvů jako projekt ke stažení. Klikněte na tlačítko **OK**.
* Vyberte **webovou aplikaci**ASP.NET Core a pak vyberte **změnit ověřování**.
* Vyberte **jednotlivé uživatelské účty** a klikněte na **OK**.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

Vygenerovaný projekt poskytuje [ASP.NET Coreou identitu](xref:security/authentication/identity) jako [knihovnu tříd Razor](xref:razor-pages/ui-class). Knihovna tříd identity Razor zpřístupňuje koncové body s oblastí `Identity`. Příklad:

* /Identity/Account/Login
* /Identity/Account/Logout
* /Identity/Account/Manage

### <a name="apply-migrations"></a>Použít migrace

Použijte migrace k inicializaci databáze.

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

Služby se přidávají v `ConfigureServices`. Typický vzor je zavolat všechny metody `Add{Service}` a potom zavolat všechny metody `services.Configure{Service}`.

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

Předchozí kód nakonfiguruje identitu s výchozími hodnotami možností. Služby jsou zpřístupněny aplikaci prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).

Identita je povolena voláním [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_). `UseAuthentication` do kanálu požadavků přidá [middleware](xref:fundamentals/middleware/index) ověřování.

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

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

Když uživatel klikne na odkaz **zaregistrovat** , je vyvolána akce `RegisterModel.OnPostAsync`. Uživatel je vytvořen pomocí [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) na objektu `_userManager`. `_userManager` poskytuje vkládání závislostí):

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

Pokud byl uživatel vytvořen úspěšně, uživatel je přihlášen voláním `_signInManager.SignInAsync`.

**Poznámka:** Postup, jak zabránit okamžitému přihlášení při registraci, najdete v tématu [potvrzení účtu](xref:security/authentication/accconfirm#prevent-login-at-registration) .

### <a name="log-in"></a>Log in

Přihlašovací formulář se zobrazí v těchto případech:

* Je vybrán odkaz **Přihlásit** se.
* Uživatel se pokusí získat přístup ke stránce s omezením, že nemá oprávnění k přístupu, **nebo** když ho systém neověřil.

Při odeslání formuláře na přihlašovací stránce se zavolá akce `OnPostAsync`. `PasswordSignInAsync` se volá na objekt `_signInManager` (poskytnutý vkládáním závislostí).

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

Základní `Controller` třída zpřístupňuje vlastnost `User`, ke které máte přístup z metod kontroleru. Můžete například vytvořit výčet `User.Claims` a provést autorizační rozhodnutí. Další informace najdete v tématu <xref:security/authorization/introduction>.

### <a name="log-out"></a>Odhlásit se

Odkaz **Odhlásit** se vyvolá `LogoutModel.OnPost` akci. 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) vymaže deklarace identity uživatele uložené v souboru cookie.

Příspěvek je zadán ve *stránkách/Shared/_LoginPartial. cshtml*:

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a>Test identity

Výchozí šablony webového projektu umožňují anonymní přístup k domovské stránce. Chcete-li otestovat identitu, přidejte [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) na stránku ochrany osobních údajů.

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

Pokud jste přihlášeni, odhlaste se. Spusťte aplikaci a vyberte odkaz na **ochranu osobních údajů** . Budete přesměrováni na přihlašovací stránku.

### <a name="explore-identity"></a>Prozkoumat identitu

Podrobněji prozkoumat identitu:

* [Vytvořit úplný zdroj uživatelského rozhraní identity](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Projděte si zdroj každé stránky a proveďte krok pomocí ladicího programu.

## <a name="identity-components"></a>Komponenty identity

Všechny balíčky NuGet závislé na identitě jsou zahrnuté ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).

Primárním balíčkem identity je [Microsoft. AspNetCore. identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/). Tento balíček obsahuje základní sadu rozhraní pro ASP.NET Core identitu a je zahrnutý v `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.

## <a name="migrating-to-aspnet-core-identity"></a>Migrace na ASP.NET Core identity

Další informace a pokyny k migraci stávajícího úložiště identit najdete v tématu [migrace ověřování a identity](xref:migration/identity).

## <a name="setting-password-strength"></a>Nastavení síly hesla

V části [Konfigurace](#pw) najdete ukázku, která nastavuje minimální požadavky na heslo.

## <a name="next-steps"></a>Další kroky

* Informace o konfiguraci identity pomocí SQLite najdete v [tomto problému GitHubu](https://github.com/aspnet/AspNetCore.Docs/issues/5131) .
* [Konfigurace systému Identity](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

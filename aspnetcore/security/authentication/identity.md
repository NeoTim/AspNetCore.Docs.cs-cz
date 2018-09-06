---
title: Úvod do Identity v ASP.NET Core
author: rick-anderson
description: Pomocí Identity aplikace v ASP.NET Core. Zjistěte, jak nastavit požadavky na heslo (RequireDigit RequiredLength, RequiredUniqueChars a další).
ms.author: riande
ms.date: 08/08/2018
uid: security/authentication/identity
ms.openlocfilehash: bc69b1db56361dc185f582032148a4fb8078fdda
ms.sourcegitcommit: 4cd8dce371d63a66d780e4af1baab2bcf9d61b24
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "43893230"
---
# <a name="introduction-to-identity-on-aspnet-core"></a>Úvod do Identity v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core Identity je systém členství, který přidá funkce přihlášení do aplikace ASP.NET Core. Uživatelé můžou vytvářet účet s přihlašovací údaje uložené v Identity nebo může použít externího zprostředkovatele přihlášení. Podporované externí přihlášení zprostředkovatele patří [Facebook, Google, Account Microsoft a Twitter](xref:security/authentication/social/index).

Identity lze konfigurovat pomocí databáze SQL serveru ukládat uživatelská jména, hesla a data profilu. Můžete také jiné trvalého úložiště je možné, například Azure Table Storage.

[Zobrazení nebo stažení ukázkového kódu.](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) [(Jak stáhnout)](xref:tutorials/index#how-to-download-a-sample)

V tomto tématu se naučíte, jak zaregistrovat, přihlaste se pomocí Identity a odhlášení uživatele. Podrobnější pokyny k vytváření aplikací, které používají Identity najdete v části Další kroky na konci tohoto článku.

## <a name="create-a-web-app-with-authentication"></a>Vytvoření webové aplikace s ověřováním

Vytvoření projektu webové aplikace ASP.NET Core s jednotlivými uživatelskými účty.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Vyberte **souboru** > **nové** > **projektu**. 
* Vyberte **webová aplikace ASP.NET Core**. Pojmenujte projekt **WebApp1** stejný obor názvů jako projekt soubor ke stažení. Klikněte na tlačítko **OK**.
* Vyberte v ASP.NET Core **webovou aplikaci** ASP.NET Core 2.1 vyberte **změna ověřování**.
* Vyberte **jednotlivé uživatelské účty** a klikněte na tlačítko **OK**.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

```cli
dotnet new webapp --auth Individual -o WebApp1
```

---

Generovaný projekt poskytuje [ASP.NET Core Identity](xref:security/authentication/identity) jako [knihovny tříd Razor](xref:razor-pages/ui-class).

### <a name="test-register-and-login"></a>Test registrace a přihlášení

Spusťte aplikaci a zaregistrovat uživatele. V závislosti na velikost obrazovky může být nutné vybrat navigace přepínacího tlačítka zobrazíte **zaregistrovat** a **přihlášení** odkazy.

![přepínací tlačítko navigační panel](identity/_static/navToggle.png)

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>
### <a name="configure-identity-services"></a>Konfigurace Identity služby

Služby jsou přidány v `ConfigureServices`. Následující kód neobsahuje vygenerovanou šablonu `CookiePolicyOptions`:

::: moniker range=">= aspnetcore-2.1"

   [!code-csharp[](identity/sample/src/ASPNETv2.1-IdentityDemo/Startup.cs?name=snippet_configureservices)]

Předchozí kód konfiguruje Identity s výchozími hodnotami. možnost. Služby jsou k dispozici pro aplikaci přes [injektáž závislostí](xref:fundamentals/dependency-injection).

   Povolení identity voláním [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_). `UseAuthentication` Přidá ověřování [middleware](xref:fundamentals/middleware/index) požadavku kanálu.

   [!code-csharp[](identity/sample/src/ASPNETv2.1-IdentityDemo/Startup.cs?name=snippet_configure&highlight=18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,11-28,30-42)]

   Služby jsou k dispozici pro aplikace prostřednictvím [injektáž závislostí](xref:fundamentals/dependency-injection).

   Povolení identity pro aplikace po zavolání `UseAuthentication` v `Configure` metody. `UseAuthentication` Přidá ověřování [middleware](xref:fundamentals/middleware/index) požadavku kanálu.

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configure&highlight=17)]

::: moniker-end

::: moniker range="= aspnetcore-1.1"

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,13-33)]

   Tyto služby jsou k dispozici pro aplikace prostřednictvím [injektáž závislostí](xref:fundamentals/dependency-injection).

   Povolení identity pro aplikace po zavolání `UseIdentity` v `Configure` metody. `UseIdentity` Přidá na základě souboru cookie ověřování [middleware](xref:fundamentals/middleware/index) požadavku kanálu.

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configure&highlight=21)]

::: moniker-end

Další informace najdete v tématu [IdentityOptions třídy](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) a [spuštění aplikace](xref:fundamentals/startup).

## <a name="scaffold-register-login-and-logout"></a>Vygenerované uživatelské rozhraní registrace, přihlášení a odhlášení

Postupujte podle [generování uživatelského rozhraní identity do projektu Razor s autorizací](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) pokyny.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Přidáte soubory registrace, přihlášení a odhlášení.


# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Pokud jste vytvořili projekt s názvem **WebApp1**, spusťte následující příkazy. Jinak použijte správný obor názvů pro `ApplicationDbContext`:


```cli
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"

```

PowerShell používá jako oddělovač příkazu středník. Při použití prostředí PowerShell, řídicí středníky v seznamu souborů nebo vložit seznam souborů v dvojitých uvozovkách, jak ukazuje předchozí příklad.

---

### <a name="examine-register"></a>Prozkoumejte registru

::: moniker range=">= aspnetcore-2.1"

   Když uživatel klikne **zaregistrovat** odkaz, `RegisterModel.OnPostAsync` vyvolání akce. Uživatel vytvoří [asynchronně](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) na `_userManager` objektu. `_userManager` poskytuje injektáž závislostí):

   [!code-csharp[](identity/sample/src/ASPNETv2.1-IdentityDemo/Register.cshtml.cs?name=snippet&highlight=7,22)]

::: moniker-end
::: moniker range="= aspnetcore-2.0"

   Když uživatel klikne **zaregistrovat** odkaz, `Register` akce je volána na `AccountController`. `Register` Akce vytvoří uživatele voláním `CreateAsync` na `_userManager` objektu (poskytnuté `AccountController` pomocí vkládání závislostí):

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_register&highlight=11)]

::: moniker-end

   Pokud byl uživatel vytvořen úspěšně, uživatel je přihlášen voláním `_signInManager.SignInAsync`.

   **Poznámka:** naleznete v tématu [účtu potvrzení](xref:security/authentication/accconfirm#prevent-login-at-registration) kroky, jak zabránit okamžité přihlášení při registraci.

### <a name="log-in"></a>Přihlásit se

::: moniker range=">= aspnetcore-2.1"

Přihlašovací formulář se zobrazí při:

* **Přihlášení** vybraný odkaz.
* Když uživatel přistupuje k stránku, kde nejsou ověřené **nebo** ověřen, bude přesměrován na přihlašovací stránku. 

Když se odešle formulář na přihlašovací stránku, `OnPostAsync` akce je volána. `PasswordSignInAsync` je volán na `_signInManager` objektu (postkytovatel: injektáž závislostí).

   [!code-csharp[](identity/sample/src/ASPNETv2.1-IdentityDemo/Login.cshtml.cs?name=snippet&highlight=10-11)]

   Základní `Controller` třídy zpřístupňuje `User` vlastnost, která se dá dostat z metody kontroleru. Například můžete zobrazit výčet `User.Claims` a rozhodnutí o autorizaci. Další informace najdete v tématu [autorizace](xref:security/authorization/index).

::: moniker-end
::: moniker range="= aspnetcore-2.0"

Přihlašovací formulář se zobrazí, když uživatelé vyberou **přihlášení** propojení nebo přesměrováni při přístupu ke stránce, která vyžaduje ověření. Když uživatel odešle formulář na přihlašovací stránku, `AccountController` `Login` akce je volána.

`Login` Volání akce `PasswordSignInAsync` na `_signInManager` objektu (poskytnuté `AccountController` pomocí vkládání závislostí).

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_login&highlight=13-14)]

Základní (`Controller` nebo `PageModel`) třídy zpřístupňuje `User` vlastnost. Například `User.Claims` může být přezkoumána za účelem rozhodnutí o autorizaci.

::: moniker-end

### <a name="log-out"></a>Odhlásit se

::: moniker range=">= aspnetcore-2.1"

**Odhlásit** vyvolá tento odkaz `LogoutModel.OnPost` akce. 

[!code-csharp[](identity/sample/src/ASPNETv2.1-IdentityDemo/Logout.cshtml.cs)]

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) vymaže deklarací identity uživatele uložené v souboru cookie. Není přesměrování po volání `SignOutAsync` nebo uživatel uvidí **není** odhlásit.

Příspěvek je zadán v *Pages/Shared/_LoginPartial.cshtml*:

[!code-csharp[](identity/sample/src/ASPNETv2.1-IdentityDemo/_LoginPartial.cshtml?highlight=10)]

::: moniker-end
::: moniker range="= aspnetcore-2.0"
   Kliknutím **Odhlásit** propojit volání `LogOut` akce.

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_logout&highlight=7)]

   Předchozí kód volá `_signInManager.SignOutAsync` metody. `SignOutAsync` Metoda vymaže deklarací identity uživatele uložené v souboru cookie.
::: moniker-end

## <a name="test-identity"></a>Otestování Identity

Šablony webových projektů výchozí povolit anonymní přístup k domovské stránky. K otestování Identity, přidejte [ `[Authorize]` ](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) na stránku o.

[!code-csharp[](identity/sample/src/ASPNETv2.1-IdentityDemo/About.cshtml.cs)]

Pokud jste přihlášeni, odhlaste se. Spusťte aplikaci a vyberte **o** odkaz. Budete přesměrováni na přihlašovací stránku.

::: moniker range=">= aspnetcore-2.1"

### <a name="explore-identity"></a>Prozkoumejte službu Identity

Identita prozkoumat podrobněji:

* [Vytvoření zdroje plnou identitou uživatelského rozhraní](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Zkontrolujte příčiny jednotlivé stránky a projít ladicí program.

::: moniker-end

## <a name="identity-components"></a>Komponenty identit

::: moniker range=">= aspnetcore-2.1"

Všechny Identity závislé balíčky NuGet jsou součástí [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).
::: moniker-end

Primární balíček pro identitu [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/). Tento balíček obsahuje základní sadu rozhraní pro ASP.NET Core Identity a je součástí `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.

## <a name="migrating-to-aspnet-core-identity"></a>Migrace do ASP.NET Core Identity

Další informace a pokyny k migraci vaší existující úložiště identit najdete v tématu [migrovat ověřování a identita](xref:migration/identity).

## <a name="setting-password-strength"></a>Nastavení síly hesla

Zobrazit [konfigurace](#pw) ukázku, která nastavuje minimální požadavky.

## <a name="next-steps"></a>Další kroky

* [Konfigurace systému Identity](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* [Konfigurace Identity primárních klíčů datový typ](xref:security/authentication/identity-primary-key-configuration).
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

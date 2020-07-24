---
title: 'Úvod do :::no-loc(Identity)::: ASP.NET Core'
author: rick-anderson
description: 'Použijte :::no-loc(Identity)::: s aplikací ASP.NET Core. Přečtěte si, jak nastavit požadavky na heslo (RequireDigit, RequiredLength, RequiredUniqueChars a další).'
ms.author: riande
ms.date: 7/15/2020
no-loc:
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/identity
ms.openlocfilehash: 25070e90050db9dca8b003ae782662811096526a
ms.sourcegitcommit: 1b89fc58114a251926abadfd5c69c120f1ba12d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2020
ms.locfileid: "87160308"
---
# <a name="introduction-to-no-locidentity-on-aspnet-core"></a>Úvod do :::no-loc(Identity)::: ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core :::no-loc(Identity)::: :

* Je rozhraní API, které podporuje funkce přihlášení uživatelského rozhraní (UI).
* Spravuje uživatele, hesla, data profilu, role, deklarace identity, tokeny, potvrzení e-mailu a další.

Uživatelé můžou vytvořit účet s přihlašovacími informacemi uloženými v :::no-loc(Identity)::: nebo můžou použít externího poskytovatele přihlášení. Mezi podporované externí zprostředkovatelé přihlášení patří [Facebook, Google, účet Microsoft a Twitter](xref:security/authentication/social/index).

[!INCLUDE[](~/includes/requireAuth.md)]

[ :::no-loc(Identity)::: Zdrojový kód](https://github.com/dotnet/AspNetCore/tree/master/src/:::no-loc(Identity):::) je k dispozici na GitHubu. [Generování :::no-loc(Identity)::: uživatelského rozhraní](xref:security/authentication/scaffold-identity) a zobrazit generované soubory pro kontrolu interakce šablony s :::no-loc(Identity)::: .

:::no-loc(Identity):::je obvykle nakonfigurovaný pomocí SQL Server databáze pro ukládání uživatelských jmen, hesel a profilů. Případně můžete použít jiné trvalé úložiště, například Azure Table Storage.

V tomto tématu se dozvíte, jak používat :::no-loc(Identity)::: k registraci, přihlášení a odhlášení uživatele. Poznámka: šablony považují uživatelské jméno a e-mail za stejné pro uživatele. Podrobnější pokyny k vytváření aplikací, které používají :::no-loc(Identity)::: , najdete v části [Další kroky](#next).

[Platforma Microsoft Identity](/azure/active-directory/develop/) je:

* Vývoj platformy pro vývojáře Azure Active Directory (Azure AD).
* Nesouvisí s ASP.NET Core :::no-loc(Identity)::: .

[!INCLUDE[](~/includes/:::no-loc(Identity):::Server4.md)]

[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([Jak stáhnout)](xref:index#how-to-download-a-sample)).

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a>Vytvoření webové aplikace s ověřováním

Vytvořte ASP.NET Core projekt webové aplikace s jednotlivými uživatelskými účty.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Vyberte **soubor** > **Nový** > **projekt**.
* Vyberte **ASP.NET Core webové aplikace**. Pojmenujte projekt **WebApp1** tak, aby měl stejný obor názvů jako projekt ke stažení. Klikněte na **OK**.
* Vyberte **webovou aplikaci**ASP.NET Core a pak vyberte **změnit ověřování**.
* Vyberte **jednotlivé uživatelské účty** a klikněte na **OK**.

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

Předchozí příkaz vytvoří :::no-loc(Razor)::: webovou aplikaci pomocí sqlite. Pokud chcete vytvořit webovou aplikaci pomocí LocalDB, spusťte následující příkaz:

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

Vygenerovaný projekt poskytuje [ASP.NET Core :::no-loc(Identity)::: ](xref:security/authentication/identity) jako [ :::no-loc(Razor)::: knihovnu tříd](xref:razor-pages/ui-class). :::no-loc(Identity)::: :::no-loc(Razor)::: Knihovna tříd zpřístupňuje koncové body s `:::no-loc(Identity):::` oblastí. Příklad:

* /:::no-loc(Identity):::/Account/Login
* /:::no-loc(Identity):::/Account/Logout
* /:::no-loc(Identity):::/Account/Manage

### <a name="apply-migrations"></a>Použít migrace

Použijte migrace k inicializaci databáze.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Spusťte následující příkaz v konzole správce balíčků (PMC):

`PM> Update-Database`

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Migrace nejsou v tomto kroku nutné při použití SQLite.

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

V případě LocalDB spusťte následující příkaz:

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>Registrace a přihlášení testu

Spusťte aplikaci a zaregistrujte uživatele. V závislosti na velikosti obrazovky možná budete muset vybrat přepínač navigace a zobrazit tak odkazy na **registraci** a **přihlášení** .

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a>Konfigurace :::no-loc(Identity)::: služeb

Služby jsou přidány do `ConfigureServices` . Typický vzor je zavolat všechny `Add{Service}` metody a pak zavolat všechny `services.Configure{Service}` metody.

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

Předchozí zvýrazněný kód nakonfiguruje :::no-loc(Identity)::: s výchozími hodnotami možností. Služby jsou zpřístupněny aplikaci prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).

:::no-loc(Identity):::je povoleno voláním <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> . `UseAuthentication`přidá [middleware](xref:fundamentals/middleware/index) ověřování do kanálu požadavků.

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

Aplikace generovaná šablonou nepoužívá [autorizaci](xref:security/authorization/secure-data). `app.UseAuthorization`je součástí, abyste zajistili, že je přidaný do správného pořadí, aby aplikace přidala autorizaci. `UseRouting`, `UseAuthentication` , `UseAuthorization` , a `UseEndpoints` musí být volány v pořadí uvedeném v předchozím kódu.

Další informace o systémech `:::no-loc(Identity):::Options` a `Startup` najdete v tématu <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Options> a [spuštění aplikace](xref:fundamentals/startup).

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a>Registr, přihlášení, odhlášení a RegisterConfirmation uživatelského rozhraní

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Přidejte `Register` soubory, `Login` , `LogOut` a `RegisterConfirmation` . Použijte [identitu uživatelského rozhraní do :::no-loc(Razor)::: projektu s pokyny pro autorizaci](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) a vygenerujte kód uvedený v této části.

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Pokud jste vytvořili projekt s názvem **WebApp1**, spusťte následující příkazy. Jinak použijte správný obor názvů pro `ApplicationDbContext` :

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

PowerShell používá jako oddělovač příkazů středník. Při použití prostředí PowerShell, oddělte středníky v seznamu souborů nebo vložte seznam souborů do dvojitých uvozovek, jak ukazuje předchozí příklad.

Další informace o generování uživatelského rozhraní :::no-loc(Identity)::: najdete v tématu [Identita uživatelského rozhraní do :::no-loc(Razor)::: projektu s autorizací](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).

---

### <a name="examine-register"></a>Ověřit registraci

Když uživatel klikne na **Register** stránce na tlačítko registrace `Register` , vyvolá se `RegisterModel.OnPostAsync` akce. Uživatel je vytvořen pomocí [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) na `_userManager` objekt:

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/:::no-loc(Identity):::/UI/src/Areas/:::no-loc(Identity):::/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a>Přihlášení

Přihlašovací formulář se zobrazí v těchto případech:

* Je vybrán odkaz **Přihlásit** se.
* Uživatel se pokusí získat přístup ke stránce s omezením, že nemá oprávnění k přístupu, **nebo** když ho systém neověřil.

Při odeslání formuláře na přihlašovací stránce se `OnPostAsync` zavolá akce. `PasswordSignInAsync`je volána u `_signInManager` objektu.

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

Informace o tom, jak provádět autorizační rozhodnutí, najdete v tématu <xref:security/authorization/introduction> .

### <a name="log-out"></a>Odhlásit se

Odkaz **odhlášení** vyvolá `LogoutModel.OnPost` akci. 

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Logout.cshtml.cs?highlight=36)]

V předchozím kódu `return RedirectToPage();` musí být kód přesměrování, aby prohlížeč prováděl novou žádost a identita pro uživatele byla aktualizována.

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) vymaže deklarace identity uživatele uložené v souboru cookie.

Příspěvek je zadán ve *stránkách/Shared/_LoginPartial. cshtml*:

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-no-locidentity"></a>Napaden:::no-loc(Identity):::

Výchozí šablony webového projektu umožňují anonymní přístup k domovské stránce. Chcete-li otestovat :::no-loc(Identity)::: , přidejte [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

Pokud jste přihlášeni, odhlaste se. Spusťte aplikaci a vyberte odkaz na **ochranu osobních údajů** . Budete přesměrováni na přihlašovací stránku.

### <a name="explore-no-locidentity"></a>Okno:::no-loc(Identity):::

Podrobněji prozkoumat :::no-loc(Identity)::: :

* [Vytvořit úplný zdroj uživatelského rozhraní identity](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Projděte si zdroj každé stránky a proveďte krok pomocí ladicího programu.

## <a name="no-locidentity-components"></a>:::no-loc(Identity):::Konstrukční

Všechny :::no-loc(Identity)::: balíčky NuGet závislé na rozhraních jsou součástí [ASP.NET Core sdílené rozhraní](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).

Primární balíček pro :::no-loc(Identity)::: je [Microsoft. AspNetCore :::no-loc(Identity)::: .](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/). Tento balíček obsahuje základní sadu rozhraní pro ASP.NET Core :::no-loc(Identity)::: a je součástí nástroje `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` .

## <a name="migrating-to-aspnet-core-no-locidentity"></a>Migrace na ASP.NET Core:::no-loc(Identity):::

Další informace a pokyny k migraci stávajícího :::no-loc(Identity)::: úložiště najdete v tématu [migrace ověřování a :::no-loc(Identity)::: ](xref:migration/identity).

## <a name="setting-password-strength"></a>Nastavení síly hesla

V části [Konfigurace](#pw) najdete ukázku, která nastavuje minimální požadavky na heslo.

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a>AddDefault :::no-loc(Identity)::: a přidat:::no-loc(Identity):::

<xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*>Služba WAS byla představena v ASP.NET Core 2,1. Volání `AddDefault:::no-loc(Identity):::` je podobné volání následujícího:

* <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderExtensions.AddDefaultTokenProviders*>

Další informace najdete v tématu [AddDefault :::no-loc(Identity)::: source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) .

## <a name="prevent-publish-of-static-no-locidentity-assets"></a>Zabránit publikování statických :::no-loc(Identity)::: prostředků

Chcete-li zabránit publikování statických :::no-loc(Identity)::: prostředků (šablon stylů a souborů JavaScriptu pro :::no-loc(Identity)::: uživatelské rozhraní) do kořenového adresáře webu, přidejte následující `ResolveStaticWebAssetsInputsDependsOn` vlastnost a `Remove:::no-loc(Identity):::Assets` cíl do souboru projektu aplikace:

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>Remove:::no-loc(Identity):::Assets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="Remove:::no-loc(Identity):::Assets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.:::no-loc(Identity):::.UI'" />
  </ItemGroup>
</Target>
```

<a name="next"></a>

## <a name="next-steps"></a>Další kroky

* [:::no-loc(Identity):::Zdrojový kód ASP.NET Core](https://github.com/dotnet/aspnetcore/tree/master/src/:::no-loc(Identity):::)
* Informace o konfiguraci pomocí SQLite najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/5131) :::no-loc(Identity)::: .
* [Konfigurace:::no-loc(Identity):::](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core :::no-loc(Identity)::: je systém členství, který do ASP.NET Core aplikací přidává funkce přihlášení. Uživatelé můžou vytvořit účet s přihlašovacími informacemi uloženými v :::no-loc(Identity)::: nebo můžou použít externího poskytovatele přihlášení. Mezi podporované externí zprostředkovatelé přihlášení patří [Facebook, Google, účet Microsoft a Twitter](xref:security/authentication/social/index).

:::no-loc(Identity):::dá se nakonfigurovat pomocí SQL Server databáze, aby se ukládala uživatelská jména, hesla a data profilu. Případně můžete použít jiné trvalé úložiště, například Azure Table Storage.

[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-:::no-loc(Identity):::DemoComplete/) ([Jak stáhnout)](xref:index#how-to-download-a-sample)).

V tomto tématu se dozvíte, jak používat :::no-loc(Identity)::: k registraci, přihlášení a odhlášení uživatele. Podrobnější pokyny k vytváření aplikací, které používají :::no-loc(Identity)::: , najdete v části Další kroky na konci tohoto článku.

<a name="adi"></a>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a>AddDefault :::no-loc(Identity)::: a přidat:::no-loc(Identity):::

<xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*>Služba WAS byla představena v ASP.NET Core 2,1. Volání `AddDefault:::no-loc(Identity):::` je podobné volání následujícího:

* <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderExtensions.AddDefaultTokenProviders*>

Další informace najdete v tématu [AddDefault :::no-loc(Identity)::: source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) .

## <a name="create-a-web-app-with-authentication"></a>Vytvoření webové aplikace s ověřováním

Vytvořte ASP.NET Core projekt webové aplikace s jednotlivými uživatelskými účty.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Vyberte **soubor** > **Nový** > **projekt**.
* Vyberte **ASP.NET Core webové aplikace**. Pojmenujte projekt **WebApp1** tak, aby měl stejný obor názvů jako projekt ke stažení. Klikněte na **OK**.
* Vyberte **webovou aplikaci**ASP.NET Core a pak vyberte **změnit ověřování**.
* Vyberte **jednotlivé uživatelské účty** a klikněte na **OK**.

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

Vygenerovaný projekt poskytuje [ASP.NET Core :::no-loc(Identity)::: ](xref:security/authentication/identity) jako [ :::no-loc(Razor)::: knihovnu tříd](xref:razor-pages/ui-class). :::no-loc(Identity)::: :::no-loc(Razor)::: Knihovna tříd zpřístupňuje koncové body s `:::no-loc(Identity):::` oblastí. Příklad:

* /:::no-loc(Identity):::/Account/Login
* /:::no-loc(Identity):::/Account/Logout
* /:::no-loc(Identity):::/Account/Manage

### <a name="apply-migrations"></a>Použít migrace

Použijte migrace k inicializaci databáze.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Spusťte následující příkaz v konzole správce balíčků (PMC):

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>Registrace a přihlášení testu

Spusťte aplikaci a zaregistrujte uživatele. V závislosti na velikosti obrazovky možná budete muset vybrat přepínač navigace a zobrazit tak odkazy na **registraci** a **přihlášení** .

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a>Konfigurace :::no-loc(Identity)::: služeb

Služby jsou přidány do `ConfigureServices` . Typický vzor je zavolat všechny `Add{Service}` metody a pak zavolat všechny `services.Configure{Service}` metody.

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

Předchozí kód nakonfiguruje :::no-loc(Identity)::: s výchozími hodnotami možností. Služby jsou zpřístupněny aplikaci prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).

:::no-loc(Identity):::je povolen voláním [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_). `UseAuthentication`přidá [middleware](xref:fundamentals/middleware/index) ověřování do kanálu požadavků.

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

Další informace naleznete v tématu [ :::no-loc(Identity)::: třídy možností](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) a [spuštění aplikace](xref:fundamentals/startup).

## <a name="scaffold-register-login-and-logout"></a>Registrace, přihlášení a odhlášení uživatelského rozhraní

Použijte [identitu uživatelského rozhraní do :::no-loc(Razor)::: projektu s pokyny pro autorizaci](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) a vygenerujte kód uvedený v této části.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Přidejte soubory registru, přihlášení a odhlášení.

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Pokud jste vytvořili projekt s názvem **WebApp1**, spusťte následující příkazy. Jinak použijte správný obor názvů pro `ApplicationDbContext` :

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

PowerShell používá jako oddělovač příkazů středník. Při použití prostředí PowerShell, oddělte středníky v seznamu souborů nebo vložte seznam souborů do dvojitých uvozovek, jak ukazuje předchozí příklad.

---

### <a name="examine-register"></a>Ověřit registraci

Když uživatel klikne na odkaz **zaregistrovat** , `RegisterModel.OnPostAsync` je akce vyvolána. Uživatel je vytvořen pomocí [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) na `_userManager` objekt:

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

Pokud byl uživatel vytvořen úspěšně, uživatel je přihlášen voláním `_signInManager.SignInAsync` .

**Poznámka:** Postup, jak zabránit okamžitému přihlášení při registraci, najdete v tématu [potvrzení účtu](xref:security/authentication/accconfirm#prevent-login-at-registration) .

### <a name="log-in"></a>Přihlášení

Přihlašovací formulář se zobrazí v těchto případech:

* Je vybrán odkaz **Přihlásit** se.
* Uživatel se pokusí získat přístup ke stránce s omezením, že nemá oprávnění k přístupu, **nebo** když ho systém neověřil.

Při odeslání formuláře na přihlašovací stránce se `OnPostAsync` zavolá akce. `PasswordSignInAsync`je volána u `_signInManager` objektu.

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

Informace o tom, jak provádět autorizační rozhodnutí, najdete v tématu <xref:security/authorization/introduction> .

### <a name="log-out"></a>Odhlásit se

Odkaz **odhlášení** vyvolá `LogoutModel.OnPost` akci. 

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Logout.cshtml.cs)]

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) vymaže deklarace identity uživatele uložené v souboru cookie.

Příspěvek je zadán ve *stránkách/Shared/_LoginPartial. cshtml*:

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-no-locidentity"></a>Napaden:::no-loc(Identity):::

Výchozí šablony webového projektu umožňují anonymní přístup k domovské stránce. Chcete-li otestovat :::no-loc(Identity)::: , přidejte [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) na stránku soukromí.

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

Pokud jste přihlášeni, odhlaste se. Spusťte aplikaci a vyberte odkaz na **ochranu osobních údajů** . Budete přesměrováni na přihlašovací stránku.

### <a name="explore-no-locidentity"></a>Okno:::no-loc(Identity):::

Podrobněji prozkoumat :::no-loc(Identity)::: :

* [Vytvořit úplný zdroj uživatelského rozhraní identity](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Projděte si zdroj každé stránky a proveďte krok pomocí ladicího programu.

## <a name="no-locidentity-components"></a>:::no-loc(Identity):::Konstrukční

Všechny :::no-loc(Identity)::: závislé balíčky NuGet jsou součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).

Primární balíček pro :::no-loc(Identity)::: je [Microsoft. AspNetCore :::no-loc(Identity)::: .](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/). Tento balíček obsahuje základní sadu rozhraní pro ASP.NET Core :::no-loc(Identity)::: a je součástí nástroje `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` .

## <a name="migrating-to-aspnet-core-no-locidentity"></a>Migrace na ASP.NET Core:::no-loc(Identity):::

Další informace a pokyny k migraci stávajícího :::no-loc(Identity)::: úložiště najdete v tématu [migrace ověřování a :::no-loc(Identity)::: ](xref:migration/identity).

## <a name="setting-password-strength"></a>Nastavení síly hesla

V části [Konfigurace](#pw) najdete ukázku, která nastavuje minimální požadavky na heslo.

## <a name="next-steps"></a>Další kroky

* Informace o konfiguraci pomocí SQLite najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/5131) :::no-loc(Identity)::: .
* [Konfigurace:::no-loc(Identity):::](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

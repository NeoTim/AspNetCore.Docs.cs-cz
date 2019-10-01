---
title: Identita uživatelského rozhraní v ASP.NET Corech projektech
author: rick-anderson
description: Naučte se, jak generovat identitu uživatelského rozhraní v projektu ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/authentication/scaffold-identity
ms.openlocfilehash: f3ae089d344d95ed84c9720ab4ba2c697400901e
ms.sourcegitcommit: dc96d76f6b231de59586fcbb989a7fb5106d26a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703763"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a>Identita uživatelského rozhraní v ASP.NET Corech projektech

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core 2,1 a novější poskytuje [ASP.NET Core identitu](xref:security/authentication/identity) jako [knihovnu tříd Razor](xref:razor-pages/ui-class). Aplikace, které obsahují identitu, můžou použít generátory, aby selektivně přidaly zdrojový kód obsažený v knihovně tříd identity Razor (RCL). Můžete chtít generovat zdrojový kód, abyste mohli kód upravit a změnit chování. Můžete například dát generátoru pokyn, aby vygeneroval kód používaný při registraci. Generovaný kód má přednost před stejným kódem v RCL identity. Pokud chcete získat úplnou kontrolu nad uživatelským rozhraním a nepoužívat výchozí RCL, přečtěte si část [Vytvoření úplného zdroje uživatelského rozhraní identity](#full).

Aplikace, které **neobsahují** ověřování, můžou použít generátor pro přidání balíčku identity RCL. Máte možnost vybrat kód identity, který se má vygenerovat.

I když generátor generuje většinu potřebného kódu, budete muset aktualizovat projekt, aby se tento proces dokončil. Tento dokument popisuje kroky potřebné k dokončení aktualizace generování uživatelského rozhraní identity.

Když se spustí generování identit identity, vytvoří se v adresáři projektu soubor *ScaffoldingReadme. txt* . Soubor *ScaffoldingReadme. txt* obsahuje obecné pokyny k tomu, co je potřeba k dokončení aktualizace generování uživatelského rozhraní identity. Tento dokument obsahuje ucelené pokyny, než soubor *ScaffoldingReadme. txt* .

Doporučujeme používat systém správy zdrojového kódu, který zobrazuje rozdíly mezi soubory a umožňuje zálohování změn. Zkontrolujte změny po spuštění generování uživatelského rozhraní identity.

> [!NOTE]
> Služby jsou vyžadovány při použití [dvou ověření](xref:security/authentication/identity-enable-qrcodes), [potvrzení účtu a obnovení hesla](xref:security/authentication/accconfirm)a dalších funkcí zabezpečení s identitou. Služby nebo zástupné procedury služby nejsou generovány při generování uživatelského rozhraní. Služby, které umožňují tyto funkce povolit, je nutné přidat ručně. Podívejte se například na příkaz [vyžadovat potvrzení e-mailu](xref:security/authentication/accconfirm#require-email-confirmation).

## <a name="scaffold-identity-into-an-empty-project"></a>Identita uživatelského rozhraní do prázdného projektu

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Do třídy `Startup` přidejte následující zvýrazněná volání:

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>Identita uživatelského rozhraní do projektu Razor bez existující autorizace

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identita je nakonfigurovaná v *oblasti/identity/IdentityHostingStartup. cs*. Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Migrace, UseAuthentication a rozložení

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Povolit ověřování

V metodě `Configure` třídy `Startup` volejte [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles`:

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Změny rozložení

Volitelné: Přidejte k souboru rozložení částečnou přihlášení (`_LoginPartial`):

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>Identita uživatelského rozhraní do projektu Razor s autorizací

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files Account.Register
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
Některé možnosti identity jsou nakonfigurované v *oblasti/identity/IdentityHostingStartup. cs*. Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>Identita uživatelského rozhraní do projektu MVC bez existující autorizace

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Volitelné: Do souboru *views/Shared/_Layout. cshtml* přidejte částečná přihlášení (`_LoginPartial`):

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* Přesuňte soubor *Pages/Shared/_LoginPartial. cshtml* do *views/Shared/_LoginPartial. cshtml.*

Identita je nakonfigurovaná v *oblasti/identity/IdentityHostingStartup. cs*. Další informace najdete v tématu IHostingStartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Volat [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles`:

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>Identita uživatelského rozhraní do projektu MVC s autorizací

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext --files Account.Register
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Odstraňte *stránky nebo sdílenou* složku a soubory v této složce.

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>Vytvořit úplný zdroj uživatelského rozhraní identity

Chcete-li zachovat úplnou kontrolu nad uživatelským rozhraním identity, spusťte generátor identity a vyberte možnost **přepsat všechny soubory**.

Následující zvýrazněný kód ukazuje změny, které nahradí výchozí uživatelské rozhraní identity identitou ve webové aplikaci ASP.NET Core 2,1. To může být vhodné, pokud chcete mít úplnou kontrolu nad uživatelským rozhraním identity.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

Výchozí identita je nahrazena následujícím kódem:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

Následující kód nastaví [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)a [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

Zaregistrujte implementaci `IEmailSender`, například:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

## <a name="additional-resources"></a>Další zdroje

* [Změny ověřovacího kódu na ASP.NET Core 2,1 a novější](xref:migration/20_21#changes-to-authentication-code)

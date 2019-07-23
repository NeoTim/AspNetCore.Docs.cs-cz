---
title: Identity vygenerované uživatelské rozhraní v projektech ASP.NET Core
author: rick-anderson
description: Zjistěte, jak automaticky vygenerovat identitu v projektu aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 9e784864ee78bdfb76dab7748f3fb81728b9c289
ms.sourcegitcommit: 91cc1f07ef178ab709ea42f8b3a10399c970496e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67622768"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a>Identity vygenerované uživatelské rozhraní v projektech ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core 2.1 nebo novější obsahuje [ASP.NET Core Identity](xref:security/authentication/identity) jako [knihovny tříd Razor](xref:razor-pages/ui-class). Aplikace, které obsahují Identity můžete použít Generátor selektivně Přidání zdrojového kódu obsažen v knihovně Razor třídu (RCL) Identity. Můžete chtít vygenerování zdrojového kódu, abyste mohli upravit kód a změnit chování. Například může dát pokyn generátor pro generování kódu při registraci. Generovaného kódu mají přednost před stejný kód v Identity RCL. Získejte úplnou kontrolu nad uživatelského rozhraní a nepoužívat výchozí RCL, najdete v části [vytvořit úplnou identity uživatelského rozhraní zdroj](#full).

Aplikace, které provádějí **není** zahrnout ověřování můžete použít generátor a přidejte příslušný balíček RCL Identity. Máte možnost výběru Identity kód chcete vygenerovat.

I když generátor generuje většinu kódu nezbytné, budete muset aktualizovat projekt proces dokončete. Tento dokument popisuje kroky potřebné k dokončení generování uživatelského rozhraní aktualizace Identity.

Při spuštění generátor Identity *ScaffoldingReadme.txt* vytvoří soubor v adresáři projektu. *ScaffoldingReadme.txt* soubor obsahuje obecné pokyny, co je potřeba k dokončení generování uživatelského rozhraní aktualizace Identity. Tento dokument obsahuje podrobnější pokyny než *ScaffoldingReadme.txt* souboru.

Doporučujeme používat systém správy zdrojového kódu, které jsou uvedeny rozdíly souborů a umožňuje zpět mimo změny. Zkontrolujte změny po spuštění generátor Identity.

> [!NOTE]
> Při použití se vyžadují služby [dvoufaktorové ověřování](xref:security/authentication/identity-enable-qrcodes), [účtu potvrzení a heslo pro obnovení](xref:security/authentication/accconfirm)a další funkce zabezpečení s identitou. Služby nebo zástupné procedury služby nejsou generovány při generování uživatelského rozhraní Identity. Služby a povolení těchto funkcí je nutné přidat ručně. Viz například [vyžadují e-mailové potvrzení](xref:security/authentication/accconfirm#require-email-confirmation).

## <a name="scaffold-identity-into-an-empty-project"></a>Vygenerované uživatelské rozhraní identity do prázdného projektu

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Přidejte následující zvýrazněný volání `Startup` třídy:

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>Vygenerované uživatelské rozhraní identity do projektu Razor bez existující autorizace

<!--
set projNam=RPnoAuth
set projType=webapp
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

Identita je nakonfigurovaný v *Areas/Identity/IdentityHostingStartup.cs*. Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Migrace, UseAuthentication a rozložení

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Povolení ověřování

V `Configure` metodu `Startup` třídy, zavolejte [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles`:

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Změny rozložení

Volitelné: Přidat částečné přihlášení (`_LoginPartial`) pro soubor rozložení:

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>Vygenerované uživatelské rozhraní identity do projektu Razor s autorizací

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
Některé možnosti Identity jsou nakonfigurované v *Areas/Identity/IdentityHostingStartup.cs*. Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>Vygenerované uživatelské rozhraní identity do projektu aplikace MVC bez existující autorizace

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

Volitelné: Přidat částečné přihlášení (`_LoginPartial`) k *Views/Shared/_Layout.cshtml* souboru:

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* Přesunout *Pages/Shared/_LoginPartial.cshtml* do souboru *Views/Shared/_LoginPartial.cshtml*

Identita je nakonfigurovaný v *Areas/Identity/IdentityHostingStartup.cs*. Další informace najdete v tématu IHostingStartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Volání [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles`:

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>Vygenerované uživatelské rozhraní identity do projektu aplikace MVC s autorizací

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext --files Account.Register
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Odstranit *stránek/Shared* složky a soubory v této složce.

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>Vytvoření zdroje plnou identitou uživatelského rozhraní

Pokud chcete zachovat plnou kontrolu nad Identity uživatelského rozhraní, spusťte generátor Identity a vyberte **přepsat všechny soubory**.

Následující zvýrazněný kód ukazuje změny nahraďte výchozí uživatelské rozhraní Identity Identity ve webové aplikaci ASP.NET Core 2.1. Můžete k tomu mají plnou kontrolu nad Identity uživatelského rozhraní.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

V následujícím kódu se nahradí výchozí Identity:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

Následující kód nastaví [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), a [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

Registrace `IEmailSender` implementace, například:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

## <a name="additional-resources"></a>Další zdroje

* [Změny kódu ověřování ASP.NET Core 2.1 a vyšší](xref:migration/20_21#changes-to-authentication-code)

---
title: Přidat, stažení a odstranění dat uživatele na identitu v projektu aplikace ASP.NET Core
author: rick-anderson
description: Zjistěte, jak přidat vlastní uživatelská data na identitu v projektu aplikace ASP.NET Core. Odstraníte data podle nařízení GDPR.
ms.author: riande
ms.date: 6/16/2018
ms.custom: seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: 8c0413a16d92b717619387748ee78f0d14d6c852
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59516206"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a>Přidání, stáhněte si a odstranit vlastní uživatelská data na identitu v projektu aplikace ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

Tento článek popisuje, jak:

* Přidáte vlastní uživatelská data do webové aplikace ASP.NET Core.
* Uspořádání vlastního uživatele datového modelu o [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute?view=aspnetcore-2.1) atribut tak, aby byl automaticky k dispozici ke stažení a odstranění. Nastavení může ke stažení a odstranění dat pomáhá plnit [GDPR](xref:security/gdpr) požadavky.

Ukázkový projekt je vytvořený z webová aplikace Razor Pages, ale pokyny jsou podobné pro webovou aplikaci ASP.NET Core MVC.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/live/aspnetcore/security/authentication/add-user-data) ([stažení](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [](~/includes/2.2-SDK.md)]

## <a name="create-a-razor-web-app"></a>Vytvoření webové aplikace Razor

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**. Pojmenujte projekt **WebApp1** Pokud budete chtít odpovídat oboru názvů [stáhněte si ukázky](https://github.com/aspnet/Docs/tree/live/aspnetcore/security/authentication/add-user-data/sample) kódu.
* Vyberte **webová aplikace ASP.NET Core** > **OK**
* Vyberte **2.2 technologie ASP.NET Core** v rozevírací nabídce
* Vyberte **webovou aplikaci**  > **OK**
* Sestavte a spusťte projekt.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

```cli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a>Spustit generátor Identity

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Z **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt > **přidat** > **novou vygenerovanou položku**.
* V levém podokně **přidat vygenerované uživatelské rozhraní** dialogového okna, vyberte **Identity** > **přidat**.
* V **ADD Identity** dialogové okno, následující možnosti:
  * Vyberte existující soubor rozložení *~/Pages/Shared/_Layout.cshtml*
  * Vyberte následující soubory, které chcete přepsat:
    * **Účtu/registrace**
    * **Účet a správa/indexu**
  * Vyberte **+** tlačítko pro vytvoření nového **třída kontextu dat**. Přijmout typ (**WebApp1.Models.WebApp1Context** Pokud je název projektu **WebApp1**).
  * Vyberte **+** tlačítko pro vytvoření nového **třídu uživatelů**. Přijmout typ (**WebApp1User** Pokud je název projektu **WebApp1**) > **přidat**.
* Vyberte **přidat**.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Pokud jste nenainstalovali dříve generátor ASP.NET Core, nainstalujte ho:

```cli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Přidat odkaz na balíček pro [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) do souboru projektu (.csproj). Spusťte následující příkaz v adresáři projektu:

```cli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Spusťte následující příkaz k výpisu možností generátor Identity:

```cli
dotnet aspnet-codegenerator identity -h
```

Ve složce projektu spustit generátor Identity:

```cli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

Postupujte podle pokynů v [migrace, UseAuthentication a rozložení](xref:security/authentication/scaffold-identity#efm) provést následující kroky:

* Vytvoření migrace a aktualizaci databáze.
* Add `UseAuthentication` to `Startup.Configure`.
* Přidat `<partial name="_LoginPartial" />` do rozložení souboru.
* Testování aplikace:
  * Registrace uživatele
  * Vyberte nové uživatelské jméno (vedle **odhlášení** odkaz). Můžete potřebovat rozbalte okno nebo vyberte ikonu navigačního panelu a zobrazí se uživatelské jméno a dalších odkazů.
  * Vyberte **osobní údaje** kartu.
  * Vyberte **Stáhnout** tlačítko a prověřit *PersonalData.json* souboru.
  * Test **odstranit** tlačítko, které odstraní přihlášeného uživatele.

## <a name="add-custom-user-data-to-the-identity-db"></a>Přidat vlastní uživatelská data do databáze Identity

Aktualizace `IdentityUser` odvozené třídy s vlastními vlastnostmi. Pokud jste projekt WebApp1, má název souboru *Areas/Identity/Data/WebApp1User.cs*. Aktualizace souboru následujícím kódem:

[!code-csharp[Main](add-user-data/sample-2.2/Areas/Identity/Data/WebApp1User.cs)]

Upravené vlastnosti pomocí [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute?view=aspnetcore-2.1) atributu:

* Odstranit, když *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* volá stránky Razor `UserManager.Delete`.
* Součástí staženého data podle *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* stránky Razor.

### <a name="update-the-accountmanageindexcshtml-page"></a>Aktualizovat stránku Account/Manage/Index.cshtml

Aktualizace `InputModel` v *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* s následující zvýrazněný kód:

[!code-csharp[Main](add-user-data/sample-2.2/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

Aktualizace *Areas/Identity/Pages/Account/Manage/Index.cshtml* s následující zvýrazněný kód:

[!code-html[Main](add-user-data/sample-2.2/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

### <a name="update-the-accountregistercshtml-page"></a>Aktualizovat stránku Account/Register.cshtml

Aktualizace `InputModel` v *Areas/Identity/Pages/Account/Register.cshtml.cs* s následující zvýrazněný kód:

[!code-csharp[Main](add-user-data/sample-2.2/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

Aktualizace *Areas/Identity/Pages/Account/Register.cshtml* s následující zvýrazněný kód:

[!code-html[Main](add-user-data/sample-2.2/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

Sestavte projekt.

### <a name="add-a-migration-for-the-custom-user-data"></a>Přidejte migraci pro vlastní uživatelská data

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

V sadě Visual Studio **Konzola správce balíčků**:

```PMC
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

```cli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a>Test vytvořit, zobrazit, stáhnout, odstranit vlastní uživatelská data

Testování aplikace:

* Registrace nového uživatele.
* Zobrazit vlastní uživatelské údaje o `/Identity/Account/Manage` stránky.
* Stáhnout a zobrazit osobní data uživatelů z `/Identity/Account/Manage/PersonalData` stránky.

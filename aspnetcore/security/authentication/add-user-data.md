---
title: Přidat, stažení a odstranění dat uživatele na identitu v projektu aplikace ASP.NET Core
author: rick-anderson
description: Zjistěte, jak přidat vlastní uživatelská data na identitu v projektu aplikace ASP.NET Core. Odstraníte data podle nařízení GDPR.
ms.author: riande
ms.date: 01/28/2020
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: 7a67f55da0e685ed3fd5badb30e8be683411a5ae
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78662685"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a>Přidání, stáhněte si a odstranit vlastní uživatelská data na identitu v projektu aplikace ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Tento článek popisuje, jak:

* Přidáte vlastní uživatelská data do webové aplikace ASP.NET Core.
* Označte vlastní uživatelský datový model pomocí atributu <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute>, aby byl automaticky dostupný ke stažení a odstranění. Zpřístupnění dat, které je možné stáhnout a odstranit, pomůže splnit [GDPR](xref:security/gdpr) požadavky.

Ukázkový projekt je vytvořený z webová aplikace Razor Pages, ale pokyny jsou podobné pro webovou aplikaci ASP.NET Core MVC.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Předpoklady

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a>Vytvoření webové aplikace Razor

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* V nabídce **soubor** v aplikaci Visual Studio vyberte **Nový** > **projekt**. Pojmenujte projekt **WebApp1** , pokud chcete, aby odpovídal oboru názvů [ukázkového kódu ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .
* Vyberte **ASP.NET Core webové aplikace** > **OK** .
* V rozevíracím seznamu vyberte **ASP.NET Core 3,0** .
* Vyberte **webovou aplikaci** > **OK** .
* Sestavte a spusťte projekt.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* V nabídce **soubor** v aplikaci Visual Studio vyberte **Nový** > **projekt**. Pojmenujte projekt **WebApp1** , pokud chcete, aby odpovídal oboru názvů [ukázkového kódu ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .
* Vyberte **ASP.NET Core webové aplikace** > **OK** .
* V rozevíracím seznamu vyberte **ASP.NET Core 2,2** .
* Vyberte **webovou aplikaci** > **OK** .
* Sestavte a spusťte projekt.

::: moniker-end


# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a>Spustit generátor Identity

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Z **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt > **Přidat** > **novou vygenerované položky**.
* V levém podokně dialogového okna **Přidat generování uživatelského rozhraní** vyberte **Identita** > **Přidat**.
* V dialogovém okně **Přidat identitu** tyto možnosti:
  * Vyberte existující soubor rozložení *~/Pages/Shared/_Layout. cshtml.*
  * Vyberte následující soubory, které chcete přepsat:
    * **Účet/registr**
    * **Účet/Správa/index**
  * Výběrem tlačítka **+** vytvořte novou **třídu datového kontextu**. Přijměte typ (**WebApp1. Models. WebApp1Context** , pokud se projekt jmenuje **WebApp1**).
  * Výběrem tlačítka **+** vytvořte novou **třídu uživatelů**. Přijměte typ (**WebApp1User** , pokud se projekt jmenuje **WebApp1**) > **Přidat**.
* Vyberte **Přidat**.

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Pokud jste nenainstalovali dříve generátor ASP.NET Core, nainstalujte ho:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Přidejte odkaz na balíček do [Microsoft. VisualStudio. Web. strategii. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) do souboru projektu (. csproj). Spusťte následující příkaz v adresáři projektu:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Spusťte následující příkaz k výpisu možností generátor Identity:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

Ve složce projektu spustit generátor Identity:

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

Postupujte podle pokynů v části [migrace, UseAuthentication a rozložení a](xref:security/authentication/scaffold-identity#efm) proveďte následující kroky:

* Vytvoření migrace a aktualizaci databáze.
* Add `UseAuthentication` to `Startup.Configure`.
* Přidejte `<partial name="_LoginPartial" />` do souboru rozložení.
* Testování aplikace:
  * Registrace uživatele
  * Vyberte nové uživatelské jméno (vedle odkazu pro **odhlášení** ). Můžete potřebovat rozbalte okno nebo vyberte ikonu navigačního panelu a zobrazí se uživatelské jméno a dalších odkazů.
  * Vyberte kartu **osobní data** .
  * Vyberte tlačítko **Download (stáhnout** ) a prověřit soubor *personaldata. JSON* .
  * Otestujte tlačítko **Odstranit** , které odstraní přihlášeného uživatele.

## <a name="add-custom-user-data-to-the-identity-db"></a>Přidat vlastní uživatelská data do databáze Identity

Aktualizujte odvozenou třídu `IdentityUser` vlastními vlastnostmi. Pokud jste jmenovali projekt WebApp1, soubor má název *areas/identity/data/WebApp1User. cs*. Aktualizace souboru následujícím kódem:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

Vlastnosti s atributem [personaldata](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) jsou:

* Odstraní se, když stránka *oblasti/identita/stránky/účet/Správa/DeletePersonalData. cshtml* Razor volá `UserManager.Delete`.
* Obsahuje stažená data na stránce *oblasti/identita/stránky/účet/Správa/DownloadPersonalData. cshtml* Razor.

### <a name="update-the-accountmanageindexcshtml-page"></a>Aktualizovat stránku Account/Manage/Index.cshtml

Aktualizujte `InputModel` v *oblasti/identita/stránky/účet/Správa/index. cshtml. cs* s následujícím zvýrazněným kódem:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

Aktualizujte *oblasti/identita/stránky/účet/Správa/index. cshtml* pomocí následujícího zvýrazněného označení:

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

Aktualizujte *oblasti/identita/stránky/účet/Správa/index. cshtml* pomocí následujícího zvýrazněného označení:

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a>Aktualizovat stránku Account/Register.cshtml

Aktualizujte `InputModel` v *oblasti/identita/stránky/účet/Register. cshtml. cs* s následujícím zvýrazněným kódem:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

Aktualizujte *oblasti/identita/stránky/účet/Register. cshtml* pomocí následujícího zvýrazněného označení:

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

Aktualizujte *oblasti/identita/stránky/účet/Register. cshtml* pomocí následujícího zvýrazněného označení:

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


Sestavte projekt.

### <a name="add-a-migration-for-the-custom-user-data"></a>Přidejte migraci pro vlastní uživatelská data

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

V **konzole správce balíčků**sady Visual Studio:

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a>Test vytvořit, zobrazit, stáhnout, odstranit vlastní uživatelská data

Testování aplikace:

* Registrace nového uživatele.
* Zobrazte si vlastní uživatelská data na stránce `/Identity/Account/Manage`.
* Stáhněte si a zobrazte osobní údaje uživatelů ze stránky `/Identity/Account/Manage/PersonalData`.

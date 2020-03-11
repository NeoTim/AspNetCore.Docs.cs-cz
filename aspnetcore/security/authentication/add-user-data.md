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
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a><span data-ttu-id="9f757-104">Přidání, stáhněte si a odstranit vlastní uživatelská data na identitu v projektu aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9f757-104">Add, download, and delete custom user data to Identity in an ASP.NET Core project</span></span>

<span data-ttu-id="9f757-105">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9f757-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9f757-106">Tento článek popisuje, jak:</span><span class="sxs-lookup"><span data-stu-id="9f757-106">This article shows how to:</span></span>

* <span data-ttu-id="9f757-107">Přidáte vlastní uživatelská data do webové aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9f757-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="9f757-108">Označte vlastní uživatelský datový model pomocí atributu <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute>, aby byl automaticky dostupný ke stažení a odstranění.</span><span class="sxs-lookup"><span data-stu-id="9f757-108">Mark the custom user data model with the <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="9f757-109">Zpřístupnění dat, které je možné stáhnout a odstranit, pomůže splnit [GDPR](xref:security/gdpr) požadavky.</span><span class="sxs-lookup"><span data-stu-id="9f757-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="9f757-110">Ukázkový projekt je vytvořený z webová aplikace Razor Pages, ale pokyny jsou podobné pro webovou aplikaci ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="9f757-110">The project sample is created from a Razor Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="9f757-111">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9f757-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9f757-112">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="9f757-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a><span data-ttu-id="9f757-113">Vytvoření webové aplikace Razor</span><span class="sxs-lookup"><span data-stu-id="9f757-113">Create a Razor web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9f757-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9f757-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="9f757-115">V nabídce **soubor** v aplikaci Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="9f757-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="9f757-116">Pojmenujte projekt **WebApp1** , pokud chcete, aby odpovídal oboru názvů [ukázkového kódu ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .</span><span class="sxs-lookup"><span data-stu-id="9f757-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="9f757-117">Vyberte **ASP.NET Core webové aplikace** > **OK** .</span><span class="sxs-lookup"><span data-stu-id="9f757-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="9f757-118">V rozevíracím seznamu vyberte **ASP.NET Core 3,0** .</span><span class="sxs-lookup"><span data-stu-id="9f757-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="9f757-119">Vyberte **webovou aplikaci** > **OK** .</span><span class="sxs-lookup"><span data-stu-id="9f757-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="9f757-120">Sestavte a spusťte projekt.</span><span class="sxs-lookup"><span data-stu-id="9f757-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="9f757-121">V nabídce **soubor** v aplikaci Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="9f757-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="9f757-122">Pojmenujte projekt **WebApp1** , pokud chcete, aby odpovídal oboru názvů [ukázkového kódu ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .</span><span class="sxs-lookup"><span data-stu-id="9f757-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="9f757-123">Vyberte **ASP.NET Core webové aplikace** > **OK** .</span><span class="sxs-lookup"><span data-stu-id="9f757-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="9f757-124">V rozevíracím seznamu vyberte **ASP.NET Core 2,2** .</span><span class="sxs-lookup"><span data-stu-id="9f757-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="9f757-125">Vyberte **webovou aplikaci** > **OK** .</span><span class="sxs-lookup"><span data-stu-id="9f757-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="9f757-126">Sestavte a spusťte projekt.</span><span class="sxs-lookup"><span data-stu-id="9f757-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="9f757-127">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="9f757-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a><span data-ttu-id="9f757-128">Spustit generátor Identity</span><span class="sxs-lookup"><span data-stu-id="9f757-128">Run the Identity scaffolder</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9f757-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9f757-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9f757-130">Z **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt > **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="9f757-130">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="9f757-131">V levém podokně dialogového okna **Přidat generování uživatelského rozhraní** vyberte **Identita** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="9f757-131">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="9f757-132">V dialogovém okně **Přidat identitu** tyto možnosti:</span><span class="sxs-lookup"><span data-stu-id="9f757-132">In the **Add Identity** dialog, the following options:</span></span>
  * <span data-ttu-id="9f757-133">Vyberte existující soubor rozložení *~/Pages/Shared/_Layout. cshtml.*</span><span class="sxs-lookup"><span data-stu-id="9f757-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="9f757-134">Vyberte následující soubory, které chcete přepsat:</span><span class="sxs-lookup"><span data-stu-id="9f757-134">Select the following files to override:</span></span>
    * <span data-ttu-id="9f757-135">**Účet/registr**</span><span class="sxs-lookup"><span data-stu-id="9f757-135">**Account/Register**</span></span>
    * <span data-ttu-id="9f757-136">**Účet/Správa/index**</span><span class="sxs-lookup"><span data-stu-id="9f757-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="9f757-137">Výběrem tlačítka **+** vytvořte novou **třídu datového kontextu**.</span><span class="sxs-lookup"><span data-stu-id="9f757-137">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="9f757-138">Přijměte typ (**WebApp1. Models. WebApp1Context** , pokud se projekt jmenuje **WebApp1**).</span><span class="sxs-lookup"><span data-stu-id="9f757-138">Accept the type (**WebApp1.Models.WebApp1Context** if the project is named **WebApp1**).</span></span>
  * <span data-ttu-id="9f757-139">Výběrem tlačítka **+** vytvořte novou **třídu uživatelů**.</span><span class="sxs-lookup"><span data-stu-id="9f757-139">Select the **+** button to create a new **User class**.</span></span> <span data-ttu-id="9f757-140">Přijměte typ (**WebApp1User** , pokud se projekt jmenuje **WebApp1**) > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="9f757-140">Accept the type (**WebApp1User** if the project is named **WebApp1**) > **Add**.</span></span>
* <span data-ttu-id="9f757-141">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="9f757-141">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="9f757-142">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="9f757-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="9f757-143">Pokud jste nenainstalovali dříve generátor ASP.NET Core, nainstalujte ho:</span><span class="sxs-lookup"><span data-stu-id="9f757-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="9f757-144">Přidejte odkaz na balíček do [Microsoft. VisualStudio. Web. strategii. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) do souboru projektu (. csproj).</span><span class="sxs-lookup"><span data-stu-id="9f757-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="9f757-145">Spusťte následující příkaz v adresáři projektu:</span><span class="sxs-lookup"><span data-stu-id="9f757-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="9f757-146">Spusťte následující příkaz k výpisu možností generátor Identity:</span><span class="sxs-lookup"><span data-stu-id="9f757-146">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="9f757-147">Ve složce projektu spustit generátor Identity:</span><span class="sxs-lookup"><span data-stu-id="9f757-147">In the project folder, run the Identity scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="9f757-148">Postupujte podle pokynů v části [migrace, UseAuthentication a rozložení a](xref:security/authentication/scaffold-identity#efm) proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="9f757-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="9f757-149">Vytvoření migrace a aktualizaci databáze.</span><span class="sxs-lookup"><span data-stu-id="9f757-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="9f757-150">Add `UseAuthentication` to `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="9f757-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="9f757-151">Přidejte `<partial name="_LoginPartial" />` do souboru rozložení.</span><span class="sxs-lookup"><span data-stu-id="9f757-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="9f757-152">Testování aplikace:</span><span class="sxs-lookup"><span data-stu-id="9f757-152">Test the app:</span></span>
  * <span data-ttu-id="9f757-153">Registrace uživatele</span><span class="sxs-lookup"><span data-stu-id="9f757-153">Register a user</span></span>
  * <span data-ttu-id="9f757-154">Vyberte nové uživatelské jméno (vedle odkazu pro **odhlášení** ).</span><span class="sxs-lookup"><span data-stu-id="9f757-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="9f757-155">Můžete potřebovat rozbalte okno nebo vyberte ikonu navigačního panelu a zobrazí se uživatelské jméno a dalších odkazů.</span><span class="sxs-lookup"><span data-stu-id="9f757-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="9f757-156">Vyberte kartu **osobní data** .</span><span class="sxs-lookup"><span data-stu-id="9f757-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="9f757-157">Vyberte tlačítko **Download (stáhnout** ) a prověřit soubor *personaldata. JSON* .</span><span class="sxs-lookup"><span data-stu-id="9f757-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="9f757-158">Otestujte tlačítko **Odstranit** , které odstraní přihlášeného uživatele.</span><span class="sxs-lookup"><span data-stu-id="9f757-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-identity-db"></a><span data-ttu-id="9f757-159">Přidat vlastní uživatelská data do databáze Identity</span><span class="sxs-lookup"><span data-stu-id="9f757-159">Add custom user data to the Identity DB</span></span>

<span data-ttu-id="9f757-160">Aktualizujte odvozenou třídu `IdentityUser` vlastními vlastnostmi.</span><span class="sxs-lookup"><span data-stu-id="9f757-160">Update the `IdentityUser` derived class with custom properties.</span></span> <span data-ttu-id="9f757-161">Pokud jste jmenovali projekt WebApp1, soubor má název *areas/identity/data/WebApp1User. cs*.</span><span class="sxs-lookup"><span data-stu-id="9f757-161">If you named the project WebApp1, the file is named *Areas/Identity/Data/WebApp1User.cs*.</span></span> <span data-ttu-id="9f757-162">Aktualizace souboru následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="9f757-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="9f757-163">Vlastnosti s atributem [personaldata](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) jsou:</span><span class="sxs-lookup"><span data-stu-id="9f757-163">Properties with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="9f757-164">Odstraní se, když stránka *oblasti/identita/stránky/účet/Správa/DeletePersonalData. cshtml* Razor volá `UserManager.Delete`.</span><span class="sxs-lookup"><span data-stu-id="9f757-164">Deleted when the *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="9f757-165">Obsahuje stažená data na stránce *oblasti/identita/stránky/účet/Správa/DownloadPersonalData. cshtml* Razor.</span><span class="sxs-lookup"><span data-stu-id="9f757-165">Included in the downloaded data by the *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="9f757-166">Aktualizovat stránku Account/Manage/Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="9f757-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="9f757-167">Aktualizujte `InputModel` v *oblasti/identita/stránky/účet/Správa/index. cshtml. cs* s následujícím zvýrazněným kódem:</span><span class="sxs-lookup"><span data-stu-id="9f757-167">Update the `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="9f757-168">Aktualizujte *oblasti/identita/stránky/účet/Správa/index. cshtml* pomocí následujícího zvýrazněného označení:</span><span class="sxs-lookup"><span data-stu-id="9f757-168">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="9f757-169">Aktualizujte *oblasti/identita/stránky/účet/Správa/index. cshtml* pomocí následujícího zvýrazněného označení:</span><span class="sxs-lookup"><span data-stu-id="9f757-169">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="9f757-170">Aktualizovat stránku Account/Register.cshtml</span><span class="sxs-lookup"><span data-stu-id="9f757-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="9f757-171">Aktualizujte `InputModel` v *oblasti/identita/stránky/účet/Register. cshtml. cs* s následujícím zvýrazněným kódem:</span><span class="sxs-lookup"><span data-stu-id="9f757-171">Update the `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="9f757-172">Aktualizujte *oblasti/identita/stránky/účet/Register. cshtml* pomocí následujícího zvýrazněného označení:</span><span class="sxs-lookup"><span data-stu-id="9f757-172">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="9f757-173">Aktualizujte *oblasti/identita/stránky/účet/Register. cshtml* pomocí následujícího zvýrazněného označení:</span><span class="sxs-lookup"><span data-stu-id="9f757-173">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="9f757-174">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="9f757-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="9f757-175">Přidejte migraci pro vlastní uživatelská data</span><span class="sxs-lookup"><span data-stu-id="9f757-175">Add a migration for the custom user data</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9f757-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9f757-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9f757-177">V **konzole správce balíčků**sady Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="9f757-177">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="9f757-178">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="9f757-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="9f757-179">Test vytvořit, zobrazit, stáhnout, odstranit vlastní uživatelská data</span><span class="sxs-lookup"><span data-stu-id="9f757-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="9f757-180">Testování aplikace:</span><span class="sxs-lookup"><span data-stu-id="9f757-180">Test the app:</span></span>

* <span data-ttu-id="9f757-181">Registrace nového uživatele.</span><span class="sxs-lookup"><span data-stu-id="9f757-181">Register a new user.</span></span>
* <span data-ttu-id="9f757-182">Zobrazte si vlastní uživatelská data na stránce `/Identity/Account/Manage`.</span><span class="sxs-lookup"><span data-stu-id="9f757-182">View the custom user data on the `/Identity/Account/Manage` page.</span></span>
* <span data-ttu-id="9f757-183">Stáhněte si a zobrazte osobní údaje uživatelů ze stránky `/Identity/Account/Manage/PersonalData`.</span><span class="sxs-lookup"><span data-stu-id="9f757-183">Download and view the users personal data from the `/Identity/Account/Manage/PersonalData` page.</span></span>

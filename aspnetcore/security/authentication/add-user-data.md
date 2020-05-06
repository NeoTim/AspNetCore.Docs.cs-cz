---
title: Přidání, stažení a odstranění uživatelských dat Identity v ASP.NET Core projektu
author: rick-anderson
description: Naučte se, jak do projektu ASP.NET Core Identity přidat vlastní uživatelská data. Odstranit data na GDPR
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/add-user-data
ms.openlocfilehash: 29c23e10d11eb1042b64fc071c221a9ead857fcc
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777329"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a><span data-ttu-id="44b0f-104">Přidání, stažení a odstranění vlastních uživatelských dat do identity v ASP.NET Core projektu</span><span class="sxs-lookup"><span data-stu-id="44b0f-104">Add, download, and delete custom user data to Identity in an ASP.NET Core project</span></span>

<span data-ttu-id="44b0f-105">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="44b0f-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="44b0f-106">V tomto článku se dozvíte, jak:</span><span class="sxs-lookup"><span data-stu-id="44b0f-106">This article shows how to:</span></span>

* <span data-ttu-id="44b0f-107">Přidejte vlastní uživatelská data do ASP.NET Core webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="44b0f-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="44b0f-108">Označte vlastní uživatelský datový model s <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> atributem tak, aby byl automaticky dostupný ke stažení a odstranění.</span><span class="sxs-lookup"><span data-stu-id="44b0f-108">Mark the custom user data model with the <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="44b0f-109">Zpřístupnění dat, které je možné stáhnout a odstranit, pomůže splnit [GDPR](xref:security/gdpr) požadavky.</span><span class="sxs-lookup"><span data-stu-id="44b0f-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="44b0f-110">Ukázka projektu je vytvořena z Razor Pages webové aplikace, ale pokyny jsou podobné pro webovou aplikaci ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="44b0f-110">The project sample is created from a Razor Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="44b0f-111">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="44b0f-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="44b0f-112">Požadavky</span><span class="sxs-lookup"><span data-stu-id="44b0f-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a><span data-ttu-id="44b0f-113">Vytvoření webové aplikace Razor</span><span class="sxs-lookup"><span data-stu-id="44b0f-113">Create a Razor web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44b0f-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44b0f-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="44b0f-115">V nabídce **soubor** sady Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="44b0f-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="44b0f-116">Pojmenujte projekt **WebApp1** , pokud chcete, aby odpovídal oboru názvů [ukázkového kódu ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .</span><span class="sxs-lookup"><span data-stu-id="44b0f-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="44b0f-117">Vyberte **ASP.NET Core webové aplikace** > **OK** .</span><span class="sxs-lookup"><span data-stu-id="44b0f-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="44b0f-118">V rozevíracím seznamu vyberte **ASP.NET Core 3,0** .</span><span class="sxs-lookup"><span data-stu-id="44b0f-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="44b0f-119">Vybrat **webovou aplikaci** > v **pořádku**</span><span class="sxs-lookup"><span data-stu-id="44b0f-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="44b0f-120">Sestavte a spusťte projekt.</span><span class="sxs-lookup"><span data-stu-id="44b0f-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="44b0f-121">V nabídce **soubor** sady Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="44b0f-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="44b0f-122">Pojmenujte projekt **WebApp1** , pokud chcete, aby odpovídal oboru názvů [ukázkového kódu ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .</span><span class="sxs-lookup"><span data-stu-id="44b0f-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="44b0f-123">Vyberte **ASP.NET Core webové aplikace** > **OK** .</span><span class="sxs-lookup"><span data-stu-id="44b0f-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="44b0f-124">V rozevíracím seznamu vyberte **ASP.NET Core 2,2** .</span><span class="sxs-lookup"><span data-stu-id="44b0f-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="44b0f-125">Vybrat **webovou aplikaci** > v **pořádku**</span><span class="sxs-lookup"><span data-stu-id="44b0f-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="44b0f-126">Sestavte a spusťte projekt.</span><span class="sxs-lookup"><span data-stu-id="44b0f-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="44b0f-127">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="44b0f-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a><span data-ttu-id="44b0f-128">Spustit generování uživatelského rozhraní identity</span><span class="sxs-lookup"><span data-stu-id="44b0f-128">Run the Identity scaffolder</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44b0f-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44b0f-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="44b0f-130">Z **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt > **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="44b0f-130">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="44b0f-131">V levém podokně dialogového okna **Přidat generování uživatelského rozhraní** vyberte **Identita** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="44b0f-131">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="44b0f-132">V dialogovém okně **Přidat identitu** tyto možnosti:</span><span class="sxs-lookup"><span data-stu-id="44b0f-132">In the **Add Identity** dialog, the following options:</span></span>
  * <span data-ttu-id="44b0f-133">Vyberte existující soubor rozložení *~/Pages/Shared/_Layout. cshtml.*</span><span class="sxs-lookup"><span data-stu-id="44b0f-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="44b0f-134">Vyberte následující soubory, které chcete přepsat:</span><span class="sxs-lookup"><span data-stu-id="44b0f-134">Select the following files to override:</span></span>
    * <span data-ttu-id="44b0f-135">**Účet/registr**</span><span class="sxs-lookup"><span data-stu-id="44b0f-135">**Account/Register**</span></span>
    * <span data-ttu-id="44b0f-136">**Účet/Správa/index**</span><span class="sxs-lookup"><span data-stu-id="44b0f-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="44b0f-137">Vyberte **+** tlačítko pro vytvoření nové **třídy datového kontextu**.</span><span class="sxs-lookup"><span data-stu-id="44b0f-137">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="44b0f-138">Přijměte typ (**WebApp1. Models. WebApp1Context** , pokud se projekt jmenuje **WebApp1**).</span><span class="sxs-lookup"><span data-stu-id="44b0f-138">Accept the type (**WebApp1.Models.WebApp1Context** if the project is named **WebApp1**).</span></span>
  * <span data-ttu-id="44b0f-139">Výběrem **+** tlačítka vytvoříte novou **třídu uživatelů**.</span><span class="sxs-lookup"><span data-stu-id="44b0f-139">Select the **+** button to create a new **User class**.</span></span> <span data-ttu-id="44b0f-140">Přijměte typ (**WebApp1User** , pokud se projekt jmenuje **WebApp1**) > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="44b0f-140">Accept the type (**WebApp1User** if the project is named **WebApp1**) > **Add**.</span></span>
* <span data-ttu-id="44b0f-141">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="44b0f-141">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="44b0f-142">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="44b0f-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="44b0f-143">Pokud jste předtím ASP.NET Core lešení nenainstalovali, nainstalujte ho hned takto:</span><span class="sxs-lookup"><span data-stu-id="44b0f-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="44b0f-144">Přidejte odkaz na balíček do [Microsoft. VisualStudio. Web. strategii. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) do souboru projektu (. csproj).</span><span class="sxs-lookup"><span data-stu-id="44b0f-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="44b0f-145">V adresáři projektu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="44b0f-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="44b0f-146">Spuštěním následujícího příkazu zobrazíte seznam možností generování identit:</span><span class="sxs-lookup"><span data-stu-id="44b0f-146">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="44b0f-147">Ve složce projektu spusťte generování uživatelského rozhraní identity:</span><span class="sxs-lookup"><span data-stu-id="44b0f-147">In the project folder, run the Identity scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="44b0f-148">Postupujte podle pokynů v části [migrace, UseAuthentication a rozložení a](xref:security/authentication/scaffold-identity#efm) proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="44b0f-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="44b0f-149">Vytvořte migraci a aktualizujte databázi.</span><span class="sxs-lookup"><span data-stu-id="44b0f-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="44b0f-150">Add `UseAuthentication` to `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="44b0f-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="44b0f-151">Přidejte `<partial name="_LoginPartial" />` do souboru rozložení.</span><span class="sxs-lookup"><span data-stu-id="44b0f-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="44b0f-152">Otestujte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="44b0f-152">Test the app:</span></span>
  * <span data-ttu-id="44b0f-153">Registrace uživatele</span><span class="sxs-lookup"><span data-stu-id="44b0f-153">Register a user</span></span>
  * <span data-ttu-id="44b0f-154">Vyberte nové uživatelské jméno (vedle odkazu pro **odhlášení** ).</span><span class="sxs-lookup"><span data-stu-id="44b0f-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="44b0f-155">Možná budete muset rozbalit okno nebo vybrat ikonu navigačního panelu a zobrazit uživatelské jméno a další odkazy.</span><span class="sxs-lookup"><span data-stu-id="44b0f-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="44b0f-156">Vyberte kartu **osobní data** .</span><span class="sxs-lookup"><span data-stu-id="44b0f-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="44b0f-157">Vyberte tlačítko **Download (stáhnout** ) a prověřit soubor *personaldata. JSON* .</span><span class="sxs-lookup"><span data-stu-id="44b0f-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="44b0f-158">Otestujte tlačítko **Odstranit** , které odstraní přihlášeného uživatele.</span><span class="sxs-lookup"><span data-stu-id="44b0f-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-identity-db"></a><span data-ttu-id="44b0f-159">Přidat vlastní uživatelská data do databáze identity</span><span class="sxs-lookup"><span data-stu-id="44b0f-159">Add custom user data to the Identity DB</span></span>

<span data-ttu-id="44b0f-160">Aktualizujte `IdentityUser` odvozenou třídu vlastními vlastnostmi.</span><span class="sxs-lookup"><span data-stu-id="44b0f-160">Update the `IdentityUser` derived class with custom properties.</span></span> <span data-ttu-id="44b0f-161">Pokud jste jmenovali projekt WebApp1, soubor má název *areas/identity/data/WebApp1User. cs*.</span><span class="sxs-lookup"><span data-stu-id="44b0f-161">If you named the project WebApp1, the file is named *Areas/Identity/Data/WebApp1User.cs*.</span></span> <span data-ttu-id="44b0f-162">Aktualizujte soubor pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="44b0f-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="44b0f-163">Vlastnosti s atributem [personaldata](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) jsou:</span><span class="sxs-lookup"><span data-stu-id="44b0f-163">Properties with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="44b0f-164">Odstraní se při volání `UserManager.Delete`stránky *oblasti/identita, stránky/účet/Správa/DeletePersonalData. cshtml* Razor.</span><span class="sxs-lookup"><span data-stu-id="44b0f-164">Deleted when the *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="44b0f-165">Obsahuje stažená data na stránce *oblasti/identita/stránky/účet/Správa/DownloadPersonalData. cshtml* Razor.</span><span class="sxs-lookup"><span data-stu-id="44b0f-165">Included in the downloaded data by the *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="44b0f-166">Aktualizuje stránku účet/Správa/index. cshtml.</span><span class="sxs-lookup"><span data-stu-id="44b0f-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="44b0f-167">Aktualizujte `InputModel` v části *oblasti/identita/stránky/účet/Správa/index. cshtml. cs* následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="44b0f-167">Update the `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="44b0f-168">Aktualizujte *oblasti/identita/stránky/účet/Správa/index. cshtml* pomocí následujícího zvýrazněného označení:</span><span class="sxs-lookup"><span data-stu-id="44b0f-168">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="44b0f-169">Aktualizujte *oblasti/identita/stránky/účet/Správa/index. cshtml* pomocí následujícího zvýrazněného označení:</span><span class="sxs-lookup"><span data-stu-id="44b0f-169">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="44b0f-170">Aktualizace stránky account/Register. cshtml</span><span class="sxs-lookup"><span data-stu-id="44b0f-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="44b0f-171">Aktualizujte `InputModel` v části *oblasti/identita/stránky/účet/Register. cshtml. cs* následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="44b0f-171">Update the `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="44b0f-172">Aktualizujte *oblasti/identita/stránky/účet/Register. cshtml* pomocí následujícího zvýrazněného označení:</span><span class="sxs-lookup"><span data-stu-id="44b0f-172">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="44b0f-173">Aktualizujte *oblasti/identita/stránky/účet/Register. cshtml* pomocí následujícího zvýrazněného označení:</span><span class="sxs-lookup"><span data-stu-id="44b0f-173">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="44b0f-174">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="44b0f-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="44b0f-175">Přidání migrace pro vlastní uživatelská data</span><span class="sxs-lookup"><span data-stu-id="44b0f-175">Add a migration for the custom user data</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44b0f-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44b0f-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="44b0f-177">V **konzole správce balíčků**sady Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="44b0f-177">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="44b0f-178">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="44b0f-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="44b0f-179">Test vytvoření, zobrazení, stažení, odstranění vlastních uživatelských dat</span><span class="sxs-lookup"><span data-stu-id="44b0f-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="44b0f-180">Otestujte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="44b0f-180">Test the app:</span></span>

* <span data-ttu-id="44b0f-181">Zaregistrujte nového uživatele.</span><span class="sxs-lookup"><span data-stu-id="44b0f-181">Register a new user.</span></span>
* <span data-ttu-id="44b0f-182">Zobrazte si vlastní uživatelská data na `/Identity/Account/Manage` stránce.</span><span class="sxs-lookup"><span data-stu-id="44b0f-182">View the custom user data on the `/Identity/Account/Manage` page.</span></span>
* <span data-ttu-id="44b0f-183">Stáhněte a zobrazte si osobní údaje uživatelů ze `/Identity/Account/Manage/PersonalData` stránky.</span><span class="sxs-lookup"><span data-stu-id="44b0f-183">Download and view the users personal data from the `/Identity/Account/Manage/PersonalData` page.</span></span>

## <a name="add-claims-to-identity-using-iuserclaimsprincipalfactoryapplicationuser"></a><span data-ttu-id="44b0f-184">Přidání deklarací identity Identity pro použití IUserClaimsPrincipalFactory<ApplicationUser></span><span class="sxs-lookup"><span data-stu-id="44b0f-184">Add claims to Identity using IUserClaimsPrincipalFactory<ApplicationUser></span></span>

<span data-ttu-id="44b0f-185">Další deklarace identity je možné přidat do Identity ASP.NET Core pomocí `IUserClaimsPrincipalFactory<T>` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="44b0f-185">Additional claims can be added to ASP.NET Core Identity by using the `IUserClaimsPrincipalFactory<T>` interface.</span></span> <span data-ttu-id="44b0f-186">Tato třída se dá do aplikace přidat v `Startup.ConfigureServices` metodě.</span><span class="sxs-lookup"><span data-stu-id="44b0f-186">This class can be added to the app in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="44b0f-187">Přidejte vlastní implementaci třídy následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="44b0f-187">Add the custom implementation of the class as follows:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

<span data-ttu-id="44b0f-188">Ukázkový kód používá `ApplicationUser` třídu.</span><span class="sxs-lookup"><span data-stu-id="44b0f-188">The demo code uses the `ApplicationUser` class.</span></span> <span data-ttu-id="44b0f-189">Tato třída přidá `IsAdmin` vlastnost, která se používá k přidání dodatečné deklarace identity.</span><span class="sxs-lookup"><span data-stu-id="44b0f-189">This class adds an `IsAdmin` property which is used to add the additional claim.</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

<span data-ttu-id="44b0f-190">`AdditionalUserClaimsPrincipalFactory` Implementuje `UserClaimsPrincipalFactory` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="44b0f-190">The `AdditionalUserClaimsPrincipalFactory` implements the `UserClaimsPrincipalFactory` interface.</span></span> <span data-ttu-id="44b0f-191">Do se `ClaimsPrincipal`přidá nová deklarace identity role.</span><span class="sxs-lookup"><span data-stu-id="44b0f-191">A new role claim is added to the `ClaimsPrincipal`.</span></span>

```csharp
public class AdditionalUserClaimsPrincipalFactory 
        : UserClaimsPrincipalFactory<ApplicationUser, IdentityRole>
{
    public AdditionalUserClaimsPrincipalFactory( 
        UserManager<ApplicationUser> userManager,
        RoleManager<IdentityRole> roleManager, 
        IOptions<IdentityOptions> optionsAccessor) 
        : base(userManager, roleManager, optionsAccessor)
    {}

    public async override Task<ClaimsPrincipal> CreateAsync(ApplicationUser user)
    {
        var principal = await base.CreateAsync(user);
        var identity = (ClaimsIdentity)principal.Identity;

        var claims = new List<Claim>();
        if (user.IsAdmin)
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "admin"));
        }
        else
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "user"));
        }

        identity.AddClaims(claims);
        return principal;
    }
}
```

<span data-ttu-id="44b0f-192">Další deklaraci identity pak můžete v aplikaci použít.</span><span class="sxs-lookup"><span data-stu-id="44b0f-192">The additional claim can then be used in the app.</span></span> <span data-ttu-id="44b0f-193">Na Razor stránce lze `IAuthorizationService` instanci použít pro přístup k hodnotě deklarace identity.</span><span class="sxs-lookup"><span data-stu-id="44b0f-193">In a Razor Page, the `IAuthorizationService` instance can be used to access the claim value.</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

@if ((await AuthorizationService.AuthorizeAsync(User, "IsAdmin")).Succeeded)
{
    <ul class="mr-auto navbar-nav">
        <li class="nav-item">
            <a class="nav-link" asp-controller="Admin" asp-action="Index">ADMIN</a>
        </li>
    </ul>
}
```

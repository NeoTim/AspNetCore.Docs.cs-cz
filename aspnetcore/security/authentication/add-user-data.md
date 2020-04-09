---
title: Přidání, stažení a odstranění uživatelských dat do identity v projektu ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak přidat vlastní uživatelská data do identity v projektu ASP.NET Core. Smazat data podle GDPR.
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: 76b83df22381429feab80056c36dbdac1e5f20c7
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501233"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a><span data-ttu-id="84c10-104">Přidání, stažení a odstranění vlastních uživatelských dat do identity v projektu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="84c10-104">Add, download, and delete custom user data to Identity in an ASP.NET Core project</span></span>

<span data-ttu-id="84c10-105">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="84c10-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="84c10-106">Tento článek ukazuje, jak:</span><span class="sxs-lookup"><span data-stu-id="84c10-106">This article shows how to:</span></span>

* <span data-ttu-id="84c10-107">Přidejte vlastní uživatelská data do webové aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="84c10-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="84c10-108">Označte vlastní uživatelský <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> datový model atributem tak, aby byl automaticky k dispozici ke stažení a odstranění.</span><span class="sxs-lookup"><span data-stu-id="84c10-108">Mark the custom user data model with the <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="84c10-109">Zpřístupnění dat ke stažení a smazání pomáhá splnit požadavky [GDPR.](xref:security/gdpr)</span><span class="sxs-lookup"><span data-stu-id="84c10-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="84c10-110">Ukázka projektu se vytvoří z webové aplikace Razor Pages, ale pokyny jsou podobné pro ASP.NET webové aplikace Core MVC.</span><span class="sxs-lookup"><span data-stu-id="84c10-110">The project sample is created from a Razor Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="84c10-111">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="84c10-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="84c10-112">Požadavky</span><span class="sxs-lookup"><span data-stu-id="84c10-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a><span data-ttu-id="84c10-113">Vytvoření webové aplikace Razor</span><span class="sxs-lookup"><span data-stu-id="84c10-113">Create a Razor web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="84c10-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="84c10-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="84c10-115">V nabídce **Soubor** sady Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="84c10-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="84c10-116">Název projektu **WebApp1,** pokud chcete, aby odpovídaly oboru názvů [ke stažení ukázkový](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) kód.</span><span class="sxs-lookup"><span data-stu-id="84c10-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="84c10-117">Vybrat **ASP.NET základní webovou aplikaci** > **OK**</span><span class="sxs-lookup"><span data-stu-id="84c10-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="84c10-118">V rozevíracím souboru vyberte **ASP.NET jádrem 3.0.**</span><span class="sxs-lookup"><span data-stu-id="84c10-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="84c10-119">Vybrat **webovou aplikaci** > **ok**</span><span class="sxs-lookup"><span data-stu-id="84c10-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="84c10-120">Sestavení a spuštění projektu.</span><span class="sxs-lookup"><span data-stu-id="84c10-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="84c10-121">V nabídce **Soubor** sady Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="84c10-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="84c10-122">Název projektu **WebApp1,** pokud chcete, aby odpovídaly oboru názvů [ke stažení ukázkový](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) kód.</span><span class="sxs-lookup"><span data-stu-id="84c10-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="84c10-123">Vybrat **ASP.NET základní webovou aplikaci** > **OK**</span><span class="sxs-lookup"><span data-stu-id="84c10-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="84c10-124">V rozevíracím souboru vyberte **ASP.NET Core 2.2.**</span><span class="sxs-lookup"><span data-stu-id="84c10-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="84c10-125">Vybrat **webovou aplikaci** > **ok**</span><span class="sxs-lookup"><span data-stu-id="84c10-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="84c10-126">Sestavení a spuštění projektu.</span><span class="sxs-lookup"><span data-stu-id="84c10-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="84c10-127">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="84c10-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a><span data-ttu-id="84c10-128">Spuštění složky scaffolder identity</span><span class="sxs-lookup"><span data-stu-id="84c10-128">Run the Identity scaffolder</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="84c10-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="84c10-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="84c10-130">V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt > **Přidat** > **novou položku scaffolded item**.</span><span class="sxs-lookup"><span data-stu-id="84c10-130">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="84c10-131">V levém podokně dialogového okna **Přidat poslíšovací systém** vyberte **Přidat identitu** > **.**</span><span class="sxs-lookup"><span data-stu-id="84c10-131">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="84c10-132">V dialogovém okně Přidat identitu následující **volby:**</span><span class="sxs-lookup"><span data-stu-id="84c10-132">In the **Add Identity** dialog, the following options:</span></span>
  * <span data-ttu-id="84c10-133">Vyberte existující soubor rozložení *~/Pages/Shared/_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="84c10-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="84c10-134">Vyberte následující soubory, které chcete přepsat:</span><span class="sxs-lookup"><span data-stu-id="84c10-134">Select the following files to override:</span></span>
    * <span data-ttu-id="84c10-135">**Účet/registr**</span><span class="sxs-lookup"><span data-stu-id="84c10-135">**Account/Register**</span></span>
    * <span data-ttu-id="84c10-136">**Účet/Správa/Index**</span><span class="sxs-lookup"><span data-stu-id="84c10-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="84c10-137">Vyberte **+** tlačítko pro vytvoření nové **třídy kontextu Dat**.</span><span class="sxs-lookup"><span data-stu-id="84c10-137">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="84c10-138">Přijměte typ (**WebApp1.Models.WebApp1Context,** pokud projekt s názvem **WebApp1**).</span><span class="sxs-lookup"><span data-stu-id="84c10-138">Accept the type (**WebApp1.Models.WebApp1Context** if the project is named **WebApp1**).</span></span>
  * <span data-ttu-id="84c10-139">Vyberte **+** tlačítko pro vytvoření nové **třídy User**.</span><span class="sxs-lookup"><span data-stu-id="84c10-139">Select the **+** button to create a new **User class**.</span></span> <span data-ttu-id="84c10-140">Přijměte typ (**WebApp1User,** pokud projekt s názvem **WebApp1**) > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="84c10-140">Accept the type (**WebApp1User** if the project is named **WebApp1**) > **Add**.</span></span>
* <span data-ttu-id="84c10-141">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="84c10-141">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="84c10-142">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="84c10-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="84c10-143">Pokud jste dříve nenainstalovali ASP.NET složky core, nainstalujte jej nyní:</span><span class="sxs-lookup"><span data-stu-id="84c10-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="84c10-144">Přidejte odkaz na balíček [microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) do souboru projektu (.csproj).</span><span class="sxs-lookup"><span data-stu-id="84c10-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="84c10-145">V adresáři projektu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="84c10-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="84c10-146">Spuštěním následujícího příkazu zobrazíte seznam možností složky identity:</span><span class="sxs-lookup"><span data-stu-id="84c10-146">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="84c10-147">Ve složce projektu spusťte složku scaffolder identity:</span><span class="sxs-lookup"><span data-stu-id="84c10-147">In the project folder, run the Identity scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="84c10-148">Postupujte podle pokynů v [migraci, UseAuthentication a rozložení](xref:security/authentication/scaffold-identity#efm) provést následující kroky:</span><span class="sxs-lookup"><span data-stu-id="84c10-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="84c10-149">Vytvořte migraci a aktualizujte databázi.</span><span class="sxs-lookup"><span data-stu-id="84c10-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="84c10-150">Add `UseAuthentication` to `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="84c10-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="84c10-151">Přidat `<partial name="_LoginPartial" />` do souboru rozložení.</span><span class="sxs-lookup"><span data-stu-id="84c10-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="84c10-152">Otestujte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="84c10-152">Test the app:</span></span>
  * <span data-ttu-id="84c10-153">Registrace uživatele</span><span class="sxs-lookup"><span data-stu-id="84c10-153">Register a user</span></span>
  * <span data-ttu-id="84c10-154">Vyberte nové uživatelské jméno (vedle odkazu **Odhlásit** se).</span><span class="sxs-lookup"><span data-stu-id="84c10-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="84c10-155">Možná budete muset rozbalit okno nebo vybrat ikonu navigačního panelu, aby se zobrazilo uživatelské jméno a další odkazy.</span><span class="sxs-lookup"><span data-stu-id="84c10-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="84c10-156">Vyberte kartu **Osobní údaje.**</span><span class="sxs-lookup"><span data-stu-id="84c10-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="84c10-157">Vyberte tlačítko **Stáhnout** a prohlédněte si soubor *PersonalData.json.*</span><span class="sxs-lookup"><span data-stu-id="84c10-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="84c10-158">Otestujte tlačítko **Odstranit,** které odstraní přihlášeného uživatele.</span><span class="sxs-lookup"><span data-stu-id="84c10-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-identity-db"></a><span data-ttu-id="84c10-159">Přidání vlastních uživatelských dat do databáze identity</span><span class="sxs-lookup"><span data-stu-id="84c10-159">Add custom user data to the Identity DB</span></span>

<span data-ttu-id="84c10-160">Aktualizujte `IdentityUser` odvozenou třídu vlastními vlastnostmi.</span><span class="sxs-lookup"><span data-stu-id="84c10-160">Update the `IdentityUser` derived class with custom properties.</span></span> <span data-ttu-id="84c10-161">Pokud jste projekt pojmenovali WebApp1, soubor se jmenuje *Oblasti/Identita/Data/WebApp1User.cs*.</span><span class="sxs-lookup"><span data-stu-id="84c10-161">If you named the project WebApp1, the file is named *Areas/Identity/Data/WebApp1User.cs*.</span></span> <span data-ttu-id="84c10-162">Aktualizujte soubor pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="84c10-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="84c10-163">Vlastnosti s atributem [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) jsou:</span><span class="sxs-lookup"><span data-stu-id="84c10-163">Properties with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="84c10-164">Odstraněno při volání *stránek/identity/stránek/účtu/správy/odstranění osobního data.cshtml* `UserManager.Delete`.</span><span class="sxs-lookup"><span data-stu-id="84c10-164">Deleted when the *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="84c10-165">Zahrnuto do stažených dat *na stránce Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span><span class="sxs-lookup"><span data-stu-id="84c10-165">Included in the downloaded data by the *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="84c10-166">Aktualizace stránky Účet/Správa/Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="84c10-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="84c10-167">Aktualizujte `InputModel` v *oblastech/Identita/Stránky/Účet/Spravovat/Index.cshtml.cs* s následujícím zvýrazněným kódem:</span><span class="sxs-lookup"><span data-stu-id="84c10-167">Update the `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="84c10-168">Aktualizujte *oblasti/Identity/Stránky/Účet/Spravovat/Index.cshtml* pomocí následujících zvýrazněných značek:</span><span class="sxs-lookup"><span data-stu-id="84c10-168">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="84c10-169">Aktualizujte *oblasti/Identity/Stránky/Účet/Spravovat/Index.cshtml* pomocí následujících zvýrazněných značek:</span><span class="sxs-lookup"><span data-stu-id="84c10-169">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="84c10-170">Aktualizace stránky Účet/Register.cshtml</span><span class="sxs-lookup"><span data-stu-id="84c10-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="84c10-171">Aktualizujte `InputModel` v *oblastech/Identity/Pages/Account/Register.cshtml.cs* následujícím zvýrazněným kódem:</span><span class="sxs-lookup"><span data-stu-id="84c10-171">Update the `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="84c10-172">Aktualizujte *oblasti/Identity/Stránky/Účet/Register.cshtml* pomocí následujících zvýrazněných značek:</span><span class="sxs-lookup"><span data-stu-id="84c10-172">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="84c10-173">Aktualizujte *oblasti/Identity/Stránky/Účet/Register.cshtml* pomocí následujících zvýrazněných značek:</span><span class="sxs-lookup"><span data-stu-id="84c10-173">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="84c10-174">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="84c10-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="84c10-175">Přidání migrace pro vlastní uživatelská data</span><span class="sxs-lookup"><span data-stu-id="84c10-175">Add a migration for the custom user data</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="84c10-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="84c10-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="84c10-177">V konzole **Správce balíčků**sady Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="84c10-177">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="84c10-178">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="84c10-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="84c10-179">Testování vytváření, zobrazení, stahování, odstraňování vlastních uživatelských dat</span><span class="sxs-lookup"><span data-stu-id="84c10-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="84c10-180">Otestujte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="84c10-180">Test the app:</span></span>

* <span data-ttu-id="84c10-181">Zaregistrujte nového uživatele.</span><span class="sxs-lookup"><span data-stu-id="84c10-181">Register a new user.</span></span>
* <span data-ttu-id="84c10-182">Zobrazení vlastních uživatelských `/Identity/Account/Manage` dat na stránce.</span><span class="sxs-lookup"><span data-stu-id="84c10-182">View the custom user data on the `/Identity/Account/Manage` page.</span></span>
* <span data-ttu-id="84c10-183">Stáhněte si a prohlédněte `/Identity/Account/Manage/PersonalData` si osobní údaje uživatelů ze stránky.</span><span class="sxs-lookup"><span data-stu-id="84c10-183">Download and view the users personal data from the `/Identity/Account/Manage/PersonalData` page.</span></span>

## <a name="add-claims-to-identity-using-iuserclaimsprincipalfactoryapplicationuser"></a><span data-ttu-id="84c10-184">Přidání deklarací identity pomocí aplikace IUserClaimsPrincipalFactory<ApplicationUser></span><span class="sxs-lookup"><span data-stu-id="84c10-184">Add claims to Identity using IUserClaimsPrincipalFactory<ApplicationUser></span></span>

<span data-ttu-id="84c10-185">Další deklarace identity lze přidat do `IUserClaimsPrincipalFactory<T>` ASP.NET základní identity pomocí rozhraní.</span><span class="sxs-lookup"><span data-stu-id="84c10-185">Additional claims can be added to ASP.NET Core Identity by using the `IUserClaimsPrincipalFactory<T>` interface.</span></span> <span data-ttu-id="84c10-186">Tuto třídu lze přidat do `Startup.ConfigureServices` aplikace v metodě.</span><span class="sxs-lookup"><span data-stu-id="84c10-186">This class can be added to the app in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="84c10-187">Přidejte vlastní implementaci třídy takto:</span><span class="sxs-lookup"><span data-stu-id="84c10-187">Add the custom implementation of the class as follows:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

<span data-ttu-id="84c10-188">Demo kód používá `ApplicationUser` třídu.</span><span class="sxs-lookup"><span data-stu-id="84c10-188">The demo code uses the `ApplicationUser` class.</span></span> <span data-ttu-id="84c10-189">Tato třída `IsAdmin` přidá vlastnost, která se používá k přidání další deklarace.</span><span class="sxs-lookup"><span data-stu-id="84c10-189">This class adds an `IsAdmin` property which is used to add the additional claim.</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

<span data-ttu-id="84c10-190">Implementuje `AdditionalUserClaimsPrincipalFactory` `UserClaimsPrincipalFactory` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="84c10-190">The `AdditionalUserClaimsPrincipalFactory` implements the `UserClaimsPrincipalFactory` interface.</span></span> <span data-ttu-id="84c10-191">Do souboru . je `ClaimsPrincipal`přidána nová deklarace role.</span><span class="sxs-lookup"><span data-stu-id="84c10-191">A new role claim is added to the `ClaimsPrincipal`.</span></span>

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

<span data-ttu-id="84c10-192">Další deklarace pak může být použita v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="84c10-192">The additional claim can then be used in the app.</span></span> <span data-ttu-id="84c10-193">Na stránce Razor `IAuthorizationService` lze instanci použít pro přístup k hodnotě deklarace.</span><span class="sxs-lookup"><span data-stu-id="84c10-193">In a Razor Page, the `IAuthorizationService` instance can be used to access the claim value.</span></span>

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

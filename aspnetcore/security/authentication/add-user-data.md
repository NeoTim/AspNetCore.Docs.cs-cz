---
title: Přidání, stažení a odstranění uživatelských dat Identity v ASP.NET Core projektu
author: rick-anderson
description: Naučte se, jak do projektu ASP.NET Core přidat vlastní uživatelská data Identity . Odstranit data na GDPR
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/add-user-data
ms.openlocfilehash: a71395e82ed15dae753888a438471495208a14da
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631846"
---
# <a name="add-download-and-delete-custom-user-data-to-no-locidentity-in-an-aspnet-core-project"></a><span data-ttu-id="35afe-104">Přidání, stažení a odstranění vlastních uživatelských dat do Identity projektu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="35afe-104">Add, download, and delete custom user data to Identity in an ASP.NET Core project</span></span>

<span data-ttu-id="35afe-105">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="35afe-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="35afe-106">V tomto článku se dozvíte, jak:</span><span class="sxs-lookup"><span data-stu-id="35afe-106">This article shows how to:</span></span>

* <span data-ttu-id="35afe-107">Přidejte vlastní uživatelská data do ASP.NET Core webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="35afe-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="35afe-108">Označte vlastní uživatelský datový model s <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> atributem tak, aby byl automaticky dostupný ke stažení a odstranění.</span><span class="sxs-lookup"><span data-stu-id="35afe-108">Mark the custom user data model with the <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="35afe-109">Zpřístupnění dat, které je možné stáhnout a odstranit, pomůže splnit [GDPR](xref:security/gdpr) požadavky.</span><span class="sxs-lookup"><span data-stu-id="35afe-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="35afe-110">Ukázka projektu se vytvoří z Razor webové aplikace stránky, ale pokyny jsou podobné jako u webové aplikace ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="35afe-110">The project sample is created from a Razor Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="35afe-111">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="35afe-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="35afe-112">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="35afe-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-no-locrazor-web-app"></a><span data-ttu-id="35afe-113">Vytvoření Razor webové aplikace</span><span class="sxs-lookup"><span data-stu-id="35afe-113">Create a Razor web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="35afe-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="35afe-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="35afe-115">V nabídce **soubor** sady Visual Studio vyberte **Nový**  >  **projekt**.</span><span class="sxs-lookup"><span data-stu-id="35afe-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="35afe-116">Pojmenujte projekt **WebApp1** , pokud chcete, aby odpovídal oboru názvů [ukázkového kódu ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .</span><span class="sxs-lookup"><span data-stu-id="35afe-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="35afe-117">Vyberte **ASP.NET Core webové aplikace** > **OK** .</span><span class="sxs-lookup"><span data-stu-id="35afe-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="35afe-118">V rozevíracím seznamu vyberte **ASP.NET Core 3,0** .</span><span class="sxs-lookup"><span data-stu-id="35afe-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="35afe-119">Vybrat **webovou aplikaci** v > **pořádku**</span><span class="sxs-lookup"><span data-stu-id="35afe-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="35afe-120">Sestavte a spusťte projekt.</span><span class="sxs-lookup"><span data-stu-id="35afe-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="35afe-121">V nabídce **soubor** sady Visual Studio vyberte **Nový**  >  **projekt**.</span><span class="sxs-lookup"><span data-stu-id="35afe-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="35afe-122">Pojmenujte projekt **WebApp1** , pokud chcete, aby odpovídal oboru názvů [ukázkového kódu ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .</span><span class="sxs-lookup"><span data-stu-id="35afe-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="35afe-123">Vyberte **ASP.NET Core webové aplikace** > **OK** .</span><span class="sxs-lookup"><span data-stu-id="35afe-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="35afe-124">V rozevíracím seznamu vyberte **ASP.NET Core 2,2** .</span><span class="sxs-lookup"><span data-stu-id="35afe-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="35afe-125">Vybrat **webovou aplikaci** v > **pořádku**</span><span class="sxs-lookup"><span data-stu-id="35afe-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="35afe-126">Sestavte a spusťte projekt.</span><span class="sxs-lookup"><span data-stu-id="35afe-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="35afe-127">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="35afe-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-no-locidentity-scaffolder"></a><span data-ttu-id="35afe-128">Spuštění generování generátoru Identity</span><span class="sxs-lookup"><span data-stu-id="35afe-128">Run the Identity scaffolder</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="35afe-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="35afe-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="35afe-130">Z **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt > **Přidat**  >  **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="35afe-130">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="35afe-131">V levém podokně dialogového okna **Přidat generování uživatelského rozhraní** vyberte **Identity**  >  **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="35afe-131">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="35afe-132">V dialogovém **okně Identity Přidat** následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="35afe-132">In the **Add Identity** dialog, the following options:</span></span>
  * <span data-ttu-id="35afe-133">Vyberte existující soubor rozložení  *~/Pages/Shared/_Layout. cshtml.*</span><span class="sxs-lookup"><span data-stu-id="35afe-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="35afe-134">Vyberte následující soubory, které chcete přepsat:</span><span class="sxs-lookup"><span data-stu-id="35afe-134">Select the following files to override:</span></span>
    * <span data-ttu-id="35afe-135">**Účet/registr**</span><span class="sxs-lookup"><span data-stu-id="35afe-135">**Account/Register**</span></span>
    * <span data-ttu-id="35afe-136">**Účet/Správa/index**</span><span class="sxs-lookup"><span data-stu-id="35afe-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="35afe-137">Vyberte **+** tlačítko pro vytvoření nové **třídy datového kontextu**.</span><span class="sxs-lookup"><span data-stu-id="35afe-137">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="35afe-138">Přijměte typ (**WebApp1. Models. WebApp1Context** , pokud se projekt jmenuje **WebApp1**).</span><span class="sxs-lookup"><span data-stu-id="35afe-138">Accept the type (**WebApp1.Models.WebApp1Context** if the project is named **WebApp1**).</span></span>
  * <span data-ttu-id="35afe-139">Výběrem **+** tlačítka vytvoříte novou **třídu uživatelů**.</span><span class="sxs-lookup"><span data-stu-id="35afe-139">Select the **+** button to create a new **User class**.</span></span> <span data-ttu-id="35afe-140">Přijměte typ (**WebApp1User** , pokud se projekt jmenuje **WebApp1**) > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="35afe-140">Accept the type (**WebApp1User** if the project is named **WebApp1**) > **Add**.</span></span>
* <span data-ttu-id="35afe-141">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="35afe-141">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="35afe-142">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="35afe-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="35afe-143">Pokud jste předtím ASP.NET Core lešení nenainstalovali, nainstalujte ho hned takto:</span><span class="sxs-lookup"><span data-stu-id="35afe-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="35afe-144">Přidejte odkaz na balíček do [Microsoft. VisualStudio. Web. strategii. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) do souboru projektu (. csproj).</span><span class="sxs-lookup"><span data-stu-id="35afe-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="35afe-145">V adresáři projektu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="35afe-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="35afe-146">Spusťte následující příkaz, který zobrazí seznam Identity možností generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="35afe-146">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="35afe-147">Ve složce projektu spusťte modul pro Identity generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="35afe-147">In the project folder, run the Identity scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="35afe-148">Postupujte podle pokynů v části [migrace, UseAuthentication a rozložení a](xref:security/authentication/scaffold-identity#efm) proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="35afe-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="35afe-149">Vytvořte migraci a aktualizujte databázi.</span><span class="sxs-lookup"><span data-stu-id="35afe-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="35afe-150">Add `UseAuthentication` to `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="35afe-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="35afe-151">Přidejte `<partial name="_LoginPartial" />` do souboru rozložení.</span><span class="sxs-lookup"><span data-stu-id="35afe-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="35afe-152">Otestujte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="35afe-152">Test the app:</span></span>
  * <span data-ttu-id="35afe-153">Registrace uživatele</span><span class="sxs-lookup"><span data-stu-id="35afe-153">Register a user</span></span>
  * <span data-ttu-id="35afe-154">Vyberte nové uživatelské jméno (vedle odkazu pro **odhlášení** ).</span><span class="sxs-lookup"><span data-stu-id="35afe-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="35afe-155">Možná budete muset rozbalit okno nebo vybrat ikonu navigačního panelu a zobrazit uživatelské jméno a další odkazy.</span><span class="sxs-lookup"><span data-stu-id="35afe-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="35afe-156">Vyberte kartu **osobní data** .</span><span class="sxs-lookup"><span data-stu-id="35afe-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="35afe-157">Vyberte tlačítko **Download (stáhnout** ) a prověřit *PersonalData.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="35afe-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="35afe-158">Otestujte tlačítko **Odstranit** , které odstraní přihlášeného uživatele.</span><span class="sxs-lookup"><span data-stu-id="35afe-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-no-locidentity-db"></a><span data-ttu-id="35afe-159">Přidat do databáze vlastní uživatelská data Identity</span><span class="sxs-lookup"><span data-stu-id="35afe-159">Add custom user data to the Identity DB</span></span>

<span data-ttu-id="35afe-160">Aktualizujte `IdentityUser` odvozenou třídu vlastními vlastnostmi.</span><span class="sxs-lookup"><span data-stu-id="35afe-160">Update the `IdentityUser` derived class with custom properties.</span></span> <span data-ttu-id="35afe-161">Pokud jste pojmenovali projekt WebApp1, soubor má název *oblasti/ Identity /data/WebApp1User.cs*.</span><span class="sxs-lookup"><span data-stu-id="35afe-161">If you named the project WebApp1, the file is named *Areas/Identity/Data/WebApp1User.cs*.</span></span> <span data-ttu-id="35afe-162">Aktualizujte soubor pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="35afe-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="35afe-163">Vlastnosti s atributem [personaldata](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) jsou:</span><span class="sxs-lookup"><span data-stu-id="35afe-163">Properties with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="35afe-164">Odstraní se při volání *oblastí nebo Identity /Pages/Account/Manage/DeletePersonalData.cshtml* Razor stránky `UserManager.Delete` .</span><span class="sxs-lookup"><span data-stu-id="35afe-164">Deleted when the *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="35afe-165">Je součástí stažených dat na stránce *oblasti/ Identity /Pages/Account/Manage/DownloadPersonalData.cshtml* Razor .</span><span class="sxs-lookup"><span data-stu-id="35afe-165">Included in the downloaded data by the *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="35afe-166">Aktualizuje stránku účet/Správa/index. cshtml.</span><span class="sxs-lookup"><span data-stu-id="35afe-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="35afe-167">Aktualizujte `InputModel` v *oblasti/ Identity /Pages/Account/Manage/index.cshtml.cs* pomocí následujícího zvýrazněného kódu:</span><span class="sxs-lookup"><span data-stu-id="35afe-167">Update the `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="35afe-168">Aktualizujte *oblasti/ Identity /Pages/Account/Manage/index.cshtml* pomocí následujícího zvýrazněného kódu:</span><span class="sxs-lookup"><span data-stu-id="35afe-168">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="35afe-169">Aktualizujte *oblasti/ Identity /Pages/Account/Manage/index.cshtml* pomocí následujícího zvýrazněného kódu:</span><span class="sxs-lookup"><span data-stu-id="35afe-169">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="35afe-170">Aktualizace stránky account/Register. cshtml</span><span class="sxs-lookup"><span data-stu-id="35afe-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="35afe-171">Aktualizujte `InputModel` v *oblasti/ Identity /Pages/Account/Register.cshtml.cs* pomocí následujícího zvýrazněného kódu:</span><span class="sxs-lookup"><span data-stu-id="35afe-171">Update the `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="35afe-172">Aktualizujte *oblasti/ Identity /Pages/Account/Register.cshtml* pomocí následujícího zvýrazněného kódu:</span><span class="sxs-lookup"><span data-stu-id="35afe-172">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="35afe-173">Aktualizujte *oblasti/ Identity /Pages/Account/Register.cshtml* pomocí následujícího zvýrazněného kódu:</span><span class="sxs-lookup"><span data-stu-id="35afe-173">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="35afe-174">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="35afe-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="35afe-175">Přidání migrace pro vlastní uživatelská data</span><span class="sxs-lookup"><span data-stu-id="35afe-175">Add a migration for the custom user data</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="35afe-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="35afe-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="35afe-177">V **konzole správce balíčků**sady Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="35afe-177">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="35afe-178">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="35afe-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="35afe-179">Test vytvoření, zobrazení, stažení, odstranění vlastních uživatelských dat</span><span class="sxs-lookup"><span data-stu-id="35afe-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="35afe-180">Otestujte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="35afe-180">Test the app:</span></span>

* <span data-ttu-id="35afe-181">Zaregistrujte nového uživatele.</span><span class="sxs-lookup"><span data-stu-id="35afe-181">Register a new user.</span></span>
* <span data-ttu-id="35afe-182">Zobrazte si vlastní uživatelská data na `/Identity/Account/Manage` stránce.</span><span class="sxs-lookup"><span data-stu-id="35afe-182">View the custom user data on the `/Identity/Account/Manage` page.</span></span>
* <span data-ttu-id="35afe-183">Stáhněte a zobrazte si osobní údaje uživatelů ze `/Identity/Account/Manage/PersonalData` stránky.</span><span class="sxs-lookup"><span data-stu-id="35afe-183">Download and view the users personal data from the `/Identity/Account/Manage/PersonalData` page.</span></span>

## <a name="add-claims-to-no-locidentity-using-iuserclaimsprincipalfactoryapplicationuser"></a><span data-ttu-id="35afe-184">Přidání deklarací identity pro Identity použití IUserClaimsPrincipalFactory<ApplicationUser></span><span class="sxs-lookup"><span data-stu-id="35afe-184">Add claims to Identity using IUserClaimsPrincipalFactory<ApplicationUser></span></span>

> [!NOTE]
> <span data-ttu-id="35afe-185">Tato část není příponou předchozího kurzu.</span><span class="sxs-lookup"><span data-stu-id="35afe-185">This section isn't an extension of the previous tutorial.</span></span> <span data-ttu-id="35afe-186">Pokud chcete u aplikace sestavené pomocí tohoto kurzu použít následující kroky, podívejte se na [Tento problém GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/18797).</span><span class="sxs-lookup"><span data-stu-id="35afe-186">To apply the following steps to the app built using the tutorial, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/18797).</span></span>

<span data-ttu-id="35afe-187">Další deklarace identity je možné přidat ASP.NET Core Identity pomocí `IUserClaimsPrincipalFactory<T>` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="35afe-187">Additional claims can be added to ASP.NET Core Identity by using the `IUserClaimsPrincipalFactory<T>` interface.</span></span> <span data-ttu-id="35afe-188">Tato třída se dá do aplikace přidat v `Startup.ConfigureServices` metodě.</span><span class="sxs-lookup"><span data-stu-id="35afe-188">This class can be added to the app in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="35afe-189">Přidejte vlastní implementaci třídy následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="35afe-189">Add the custom implementation of the class as follows:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

<span data-ttu-id="35afe-190">Ukázkový kód používá `ApplicationUser` třídu.</span><span class="sxs-lookup"><span data-stu-id="35afe-190">The demo code uses the `ApplicationUser` class.</span></span> <span data-ttu-id="35afe-191">Tato třída přidá `IsAdmin` vlastnost, která se používá k přidání dodatečné deklarace identity.</span><span class="sxs-lookup"><span data-stu-id="35afe-191">This class adds an `IsAdmin` property which is used to add the additional claim.</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

<span data-ttu-id="35afe-192">`AdditionalUserClaimsPrincipalFactory`Implementuje `UserClaimsPrincipalFactory` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="35afe-192">The `AdditionalUserClaimsPrincipalFactory` implements the `UserClaimsPrincipalFactory` interface.</span></span> <span data-ttu-id="35afe-193">Do se přidá nová deklarace identity role `ClaimsPrincipal` .</span><span class="sxs-lookup"><span data-stu-id="35afe-193">A new role claim is added to the `ClaimsPrincipal`.</span></span>

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

<span data-ttu-id="35afe-194">Další deklaraci identity pak můžete v aplikaci použít.</span><span class="sxs-lookup"><span data-stu-id="35afe-194">The additional claim can then be used in the app.</span></span> <span data-ttu-id="35afe-195">Na Razor stránce `IAuthorizationService` lze instanci použít pro přístup k hodnotě deklarace identity.</span><span class="sxs-lookup"><span data-stu-id="35afe-195">In a Razor Page, the `IAuthorizationService` instance can be used to access the claim value.</span></span>

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

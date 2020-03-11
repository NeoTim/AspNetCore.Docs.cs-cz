---
title: Vytvoření aplikace ASP.NET Core s uživatelskými daty chráněnými autorizací
author: rick-anderson
description: Zjistěte, jak vytvořit aplikace Razor Pages s uživatelskými daty chráněnými autorizací. Zahrnuje HTTPS, ověřování, zabezpečení ASP.NET Core Identity.
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
uid: security/authorization/secure-data
ms.openlocfilehash: 7710a8965771db02e601dafb7da752906bcd43e5
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78659577"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="c106a-104">Vytvoření aplikace ASP.NET Core s uživatelskými daty chráněnými autorizací</span><span class="sxs-lookup"><span data-stu-id="c106a-104">Create an ASP.NET Core app with user data protected by authorization</span></span>

<span data-ttu-id="c106a-105">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Jan Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="c106a-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="c106a-106">Podívejte se na [Tento PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) pro ASP.NET Core verzi MVC.</span><span class="sxs-lookup"><span data-stu-id="c106a-106">See [this PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core MVC version.</span></span> <span data-ttu-id="c106a-107">Verze tohoto kurzu ASP.NET Core 1,1 je v [této](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) složce.</span><span class="sxs-lookup"><span data-stu-id="c106a-107">The ASP.NET Core 1.1 version of this tutorial is in [this](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) folder.</span></span> <span data-ttu-id="c106a-108">Ukázka 1,1 ASP.NET Core je v [ukázkách](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span><span class="sxs-lookup"><span data-stu-id="c106a-108">The 1.1 ASP.NET Core sample is in the [samples](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="c106a-109">Zobrazit [Tento PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="c106a-109">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c106a-110">Tento kurz ukazuje, jak vytvořit webovou aplikaci ASP.NET Core s uživatelskými daty chráněnými autorizací.</span><span class="sxs-lookup"><span data-stu-id="c106a-110">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="c106a-111">Zobrazí seznam kontakty, které ověřeným uživatelům (registrovaných) jste vytvořili.</span><span class="sxs-lookup"><span data-stu-id="c106a-111">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="c106a-112">Existují tři skupiny zabezpečení:</span><span class="sxs-lookup"><span data-stu-id="c106a-112">There are three security groups:</span></span>

* <span data-ttu-id="c106a-113">**Registrovaní uživatelé** můžou zobrazit všechna schválená data a můžou upravovat nebo odstraňovat vlastní data.</span><span class="sxs-lookup"><span data-stu-id="c106a-113">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="c106a-114">**Manažeři** mohou schvalovat nebo odmítat kontaktní data.</span><span class="sxs-lookup"><span data-stu-id="c106a-114">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="c106a-115">Uživatelé vidí pouze schválené kontakty.</span><span class="sxs-lookup"><span data-stu-id="c106a-115">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="c106a-116">**Správci** mohou schvalovat nebo odmítat a upravovat nebo odstraňovat jakákoli data.</span><span class="sxs-lookup"><span data-stu-id="c106a-116">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="c106a-117">Obrázky v tomto dokumentu se přesně neshodují s nejnovějšími šablonami.</span><span class="sxs-lookup"><span data-stu-id="c106a-117">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="c106a-118">Na následujícím obrázku je uživatel Rick (`rick@example.com`) přihlášen.</span><span class="sxs-lookup"><span data-stu-id="c106a-118">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="c106a-119">Rick může zobrazit pouze schválené kontakty a **upravovat**/**Odstranit**/**vytvořit nové** odkazy na své kontakty.</span><span class="sxs-lookup"><span data-stu-id="c106a-119">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="c106a-120">Jenom poslední záznam vytvořený pomocí Rick zobrazí odkazy pro **Úpravy** a **odstranění** .</span><span class="sxs-lookup"><span data-stu-id="c106a-120">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="c106a-121">Ostatní uživatelé neuvidí poslední záznam, dokud správce nebo správce změní stav na "Schváleno".</span><span class="sxs-lookup"><span data-stu-id="c106a-121">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Snímek obrazovky zobrazující Rick přihlášení](secure-data/_static/rick.png)

<span data-ttu-id="c106a-123">Na následujícím obrázku je `manager@contoso.com` přihlásit se a v roli manažera:</span><span class="sxs-lookup"><span data-stu-id="c106a-123">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Snímek obrazovky zobrazující přihlášený manager@contoso.com](secure-data/_static/manager1.png)

<span data-ttu-id="c106a-125">Následující obrázek ukazuje vedoucí zobrazení podrobností o kontaktu:</span><span class="sxs-lookup"><span data-stu-id="c106a-125">The following image shows the managers details view of a contact:</span></span>

![Zobrazení manažera kontaktu](secure-data/_static/manager.png)

<span data-ttu-id="c106a-127">Tlačítka **schválení** a **odmítnutí** se zobrazují pouze pro manažery a správce.</span><span class="sxs-lookup"><span data-stu-id="c106a-127">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="c106a-128">Na následujícím obrázku je `admin@contoso.com` přihlásit se a v roli správce:</span><span class="sxs-lookup"><span data-stu-id="c106a-128">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Snímek obrazovky zobrazující přihlášený admin@contoso.com](secure-data/_static/admin.png)

<span data-ttu-id="c106a-130">Správce má všechna oprávnění.</span><span class="sxs-lookup"><span data-stu-id="c106a-130">The administrator has all privileges.</span></span> <span data-ttu-id="c106a-131">Může číst/upravovat/odstraňovat všechny kontakty a změnit stav kontakty.</span><span class="sxs-lookup"><span data-stu-id="c106a-131">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="c106a-132">Aplikace byla vytvořena pomocí [generování uživatelského rozhraní](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) následujícího modelu `Contact`:</span><span class="sxs-lookup"><span data-stu-id="c106a-132">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="c106a-133">Ukázka obsahuje následující rutiny autorizace:</span><span class="sxs-lookup"><span data-stu-id="c106a-133">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="c106a-134">`ContactIsOwnerAuthorizationHandler`: zajišťuje, aby uživatel mohl upravovat pouze svá data.</span><span class="sxs-lookup"><span data-stu-id="c106a-134">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="c106a-135">`ContactManagerAuthorizationHandler`: umožňuje správcům schvalovat nebo odmítat kontakty.</span><span class="sxs-lookup"><span data-stu-id="c106a-135">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="c106a-136">`ContactAdministratorsAuthorizationHandler`: umožňuje správcům schvalovat nebo odmítat kontakty a upravovat nebo odstraňovat kontakty.</span><span class="sxs-lookup"><span data-stu-id="c106a-136">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c106a-137">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="c106a-137">Prerequisites</span></span>

<span data-ttu-id="c106a-138">V tomto kurzu je advanced.</span><span class="sxs-lookup"><span data-stu-id="c106a-138">This tutorial is advanced.</span></span> <span data-ttu-id="c106a-139">Měli byste se seznámit s:</span><span class="sxs-lookup"><span data-stu-id="c106a-139">You should be familiar with:</span></span>

* [<span data-ttu-id="c106a-140">Jádro ASP.NET</span><span class="sxs-lookup"><span data-stu-id="c106a-140">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="c106a-141">Ověřování</span><span class="sxs-lookup"><span data-stu-id="c106a-141">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="c106a-142">Potvrzení účtu a obnovení hesla</span><span class="sxs-lookup"><span data-stu-id="c106a-142">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="c106a-143">Autorizace</span><span class="sxs-lookup"><span data-stu-id="c106a-143">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="c106a-144">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="c106a-144">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="c106a-145">Starter a dokončené aplikace</span><span class="sxs-lookup"><span data-stu-id="c106a-145">The starter and completed app</span></span>

<span data-ttu-id="c106a-146">[Stáhněte](xref:index#how-to-download-a-sample) [dokončenou](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c106a-146">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="c106a-147">[Otestujte](#test-the-completed-app) dokončenou aplikaci, abyste se seznámili se svými funkcemi zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="c106a-147">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="c106a-148">Úvodní aplikaci</span><span class="sxs-lookup"><span data-stu-id="c106a-148">The starter app</span></span>

<span data-ttu-id="c106a-149">[Stáhněte](xref:index#how-to-download-a-sample) si [úvodní](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c106a-149">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="c106a-150">Spusťte aplikaci, klepněte na odkaz **ContactManager** a ověřte, že můžete vytvořit, upravit a odstranit kontakt.</span><span class="sxs-lookup"><span data-stu-id="c106a-150">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="c106a-151">Zabezpečení dat uživatele</span><span class="sxs-lookup"><span data-stu-id="c106a-151">Secure user data</span></span>

<span data-ttu-id="c106a-152">Následující oddíly mají všechny hlavní kroky k vytvoření aplikace pro data zabezpečení uživatele.</span><span class="sxs-lookup"><span data-stu-id="c106a-152">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="c106a-153">Vám může být užitečné k odkazování na dokončení projektu.</span><span class="sxs-lookup"><span data-stu-id="c106a-153">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="c106a-154">Tie kontaktní údaje pro uživatele</span><span class="sxs-lookup"><span data-stu-id="c106a-154">Tie the contact data to the user</span></span>

<span data-ttu-id="c106a-155">Pomocí ID uživatele [Identity](xref:security/authentication/identity) ASP.NET zajistěte, aby mohli uživatelé upravovat data, ale ne jiná data uživatelů.</span><span class="sxs-lookup"><span data-stu-id="c106a-155">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="c106a-156">Do modelu `Contact` přidejte `OwnerID` a `ContactStatus`:</span><span class="sxs-lookup"><span data-stu-id="c106a-156">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="c106a-157">`OwnerID` je ID uživatele z tabulky `AspNetUser` v databázi [identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="c106a-157">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="c106a-158">Pole `Status` určuje, zda je kontakt viditelný pro obecné uživatele.</span><span class="sxs-lookup"><span data-stu-id="c106a-158">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="c106a-159">Vytvořte novou migraci a aktualizaci databáze:</span><span class="sxs-lookup"><span data-stu-id="c106a-159">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="c106a-160">Přidání služby Role na identitu</span><span class="sxs-lookup"><span data-stu-id="c106a-160">Add Role services to Identity</span></span>

<span data-ttu-id="c106a-161">Připojit [Přidat role](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) pro přidání služeb role:</span><span class="sxs-lookup"><span data-stu-id="c106a-161">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a><span data-ttu-id="c106a-162">Vyžadovat ověření uživatelé</span><span class="sxs-lookup"><span data-stu-id="c106a-162">Require authenticated users</span></span>

<span data-ttu-id="c106a-163">Nastavte výchozí zásady ověřování tak, aby vyžadovala ověření uživatelů:</span><span class="sxs-lookup"><span data-stu-id="c106a-163">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 <span data-ttu-id="c106a-164">Ověřování na úrovni této stránky, řadiče nebo metody akce lze odhlásit pomocí atributu `[AllowAnonymous]`.</span><span class="sxs-lookup"><span data-stu-id="c106a-164">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="c106a-165">Nastavení výchozí zásady ověřování tak, aby vyžadovala ověření uživatelů chrání nově přidané Razor Pages a kontrolery.</span><span class="sxs-lookup"><span data-stu-id="c106a-165">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="c106a-166">Pokud je ve výchozím nastavení vyžadováno ověřování, je bezpečnější než spoléhání na nové řadiče a Razor Pages pro zahrnutí atributu `[Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="c106a-166">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="c106a-167">Přidejte [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) na stránky rejstřík a soukromí, aby anonymní uživatelé mohli získat informace o lokalitě před jejich registrací.</span><span class="sxs-lookup"><span data-stu-id="c106a-167">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index and Privacy pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="c106a-168">Konfigurace testovací účet</span><span class="sxs-lookup"><span data-stu-id="c106a-168">Configure the test account</span></span>

<span data-ttu-id="c106a-169">Třída `SeedData` vytvoří dva účty: správce a manažer.</span><span class="sxs-lookup"><span data-stu-id="c106a-169">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="c106a-170">Pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets) nastavte heslo pro tyto účty.</span><span class="sxs-lookup"><span data-stu-id="c106a-170">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="c106a-171">Nastavte heslo z adresáře projektu (adresář obsahující *program.cs*):</span><span class="sxs-lookup"><span data-stu-id="c106a-171">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="c106a-172">Pokud není zadáno silné heslo, je vyvolána výjimka při volání `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="c106a-172">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="c106a-173">Aktualizujte `Main` pro použití testovacího hesla:</span><span class="sxs-lookup"><span data-stu-id="c106a-173">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="c106a-174">Vytvoření testovacích účtů a aktualizovat kontakty</span><span class="sxs-lookup"><span data-stu-id="c106a-174">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="c106a-175">Aktualizujte metodu `Initialize` ve třídě `SeedData` k vytvoření testovacích účtů:</span><span class="sxs-lookup"><span data-stu-id="c106a-175">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="c106a-176">Přidejte do kontaktů ID uživatele a `ContactStatus` správce.</span><span class="sxs-lookup"><span data-stu-id="c106a-176">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="c106a-177">Proveďte jednu z kontakty "Submitted" a jedné "byl odmítnut".</span><span class="sxs-lookup"><span data-stu-id="c106a-177">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="c106a-178">Přidáte ID uživatele a stav pro všechny kontakty.</span><span class="sxs-lookup"><span data-stu-id="c106a-178">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="c106a-179">Je zobrazena pouze jeden kontakt:</span><span class="sxs-lookup"><span data-stu-id="c106a-179">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="c106a-180">Vytvořit vlastníka, správce a Správce autorizace obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="c106a-180">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="c106a-181">Ve složce pro *autorizaci* vytvořte třídu `ContactIsOwnerAuthorizationHandler`.</span><span class="sxs-lookup"><span data-stu-id="c106a-181">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="c106a-182">`ContactIsOwnerAuthorizationHandler` ověří, že uživatel pracuje na prostředku, který je vlastníkem prostředku.</span><span class="sxs-lookup"><span data-stu-id="c106a-182">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="c106a-183">`ContactIsOwnerAuthorizationHandler` volá [kontext. Úspěšné](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , pokud je aktuální ověřený uživatel vlastníkem kontaktu.</span><span class="sxs-lookup"><span data-stu-id="c106a-183">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="c106a-184">Obslužné rutiny autorizace obecně:</span><span class="sxs-lookup"><span data-stu-id="c106a-184">Authorization handlers generally:</span></span>

* <span data-ttu-id="c106a-185">Vrátí `context.Succeed`, pokud jsou splněny požadavky.</span><span class="sxs-lookup"><span data-stu-id="c106a-185">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="c106a-186">Vrátí `Task.CompletedTask`, pokud nejsou splněny požadavky.</span><span class="sxs-lookup"><span data-stu-id="c106a-186">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="c106a-187">`Task.CompletedTask` není úspěch nebo neúspěch&mdash;umožňuje spuštění dalších obslužných rutin autorizace.</span><span class="sxs-lookup"><span data-stu-id="c106a-187">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="c106a-188">Pokud potřebujete explicitně selhat, vraťte [kontext. Selhání](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="c106a-188">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="c106a-189">Aplikaci umožňuje kontaktní vlastníkům upravit, odstranit a vytvořit svoje vlastní data.</span><span class="sxs-lookup"><span data-stu-id="c106a-189">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="c106a-190">`ContactIsOwnerAuthorizationHandler` nemusí kontrolovat operaci předanou parametrem požadavku.</span><span class="sxs-lookup"><span data-stu-id="c106a-190">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="c106a-191">Vytvořte obslužnou rutinu Správce autorizací</span><span class="sxs-lookup"><span data-stu-id="c106a-191">Create a manager authorization handler</span></span>

<span data-ttu-id="c106a-192">Ve složce pro *autorizaci* vytvořte třídu `ContactManagerAuthorizationHandler`.</span><span class="sxs-lookup"><span data-stu-id="c106a-192">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="c106a-193">`ContactManagerAuthorizationHandler` ověří uživatele, který pracuje na prostředku, jako správce.</span><span class="sxs-lookup"><span data-stu-id="c106a-193">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="c106a-194">Pouze vedoucí mohli schválit nebo odmítnout změny obsahu (nové nebo změněné).</span><span class="sxs-lookup"><span data-stu-id="c106a-194">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="c106a-195">Vytvořte obslužnou rutinu Správce autorizací</span><span class="sxs-lookup"><span data-stu-id="c106a-195">Create an administrator authorization handler</span></span>

<span data-ttu-id="c106a-196">Ve složce pro *autorizaci* vytvořte třídu `ContactAdministratorsAuthorizationHandler`.</span><span class="sxs-lookup"><span data-stu-id="c106a-196">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="c106a-197">`ContactAdministratorsAuthorizationHandler` ověří uživatele, který pracuje na prostředku, jako správce.</span><span class="sxs-lookup"><span data-stu-id="c106a-197">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="c106a-198">Správce může provádět všechny operace.</span><span class="sxs-lookup"><span data-stu-id="c106a-198">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="c106a-199">Zaregistrujte obslužné rutiny autorizace</span><span class="sxs-lookup"><span data-stu-id="c106a-199">Register the authorization handlers</span></span>

<span data-ttu-id="c106a-200">Služby používající Entity Framework Core musí být registrovány pro [vkládání závislostí](xref:fundamentals/dependency-injection) pomocí [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="c106a-200">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="c106a-201">`ContactIsOwnerAuthorizationHandler` používá ASP.NET Core [identity](xref:security/authentication/identity), která je postavená na Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="c106a-201">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="c106a-202">Zaregistrujte obslužné rutiny s kolekcí služeb, aby byly k dispozici pro `ContactsController` prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c106a-202">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="c106a-203">Do konce `ConfigureServices`přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="c106a-203">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="c106a-204">`ContactAdministratorsAuthorizationHandler` a `ContactManagerAuthorizationHandler` jsou přidány jako singleton.</span><span class="sxs-lookup"><span data-stu-id="c106a-204">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="c106a-205">Jsou to jednoznačné, protože nepoužívají EF a všechny potřebné informace jsou v parametru `Context` `HandleRequirementAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="c106a-205">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="c106a-206">Povolení podpory</span><span class="sxs-lookup"><span data-stu-id="c106a-206">Support authorization</span></span>

<span data-ttu-id="c106a-207">V této části se aktualizace stránky Razor a přidejte třídu požadavků operace.</span><span class="sxs-lookup"><span data-stu-id="c106a-207">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="c106a-208">Zkontrolujte požadavky na třídy kontaktní operace</span><span class="sxs-lookup"><span data-stu-id="c106a-208">Review the contact operations requirements class</span></span>

<span data-ttu-id="c106a-209">Zkontrolujte třídu `ContactOperations`.</span><span class="sxs-lookup"><span data-stu-id="c106a-209">Review the `ContactOperations` class.</span></span> <span data-ttu-id="c106a-210">Tato třída obsahuje požadavky na aplikace podporuje:</span><span class="sxs-lookup"><span data-stu-id="c106a-210">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="c106a-211">Vytvoření základní třídu pro stránky Razor kontakty</span><span class="sxs-lookup"><span data-stu-id="c106a-211">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="c106a-212">Vytvořte základní třídu, která obsahuje služby využité v kontaktech stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="c106a-212">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="c106a-213">Základní třída vloží kód inicializace na jednom místě:</span><span class="sxs-lookup"><span data-stu-id="c106a-213">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="c106a-214">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="c106a-214">The preceding code:</span></span>

* <span data-ttu-id="c106a-215">Přidá službu `IAuthorizationService` pro přístup k obslužným rutinám autorizace.</span><span class="sxs-lookup"><span data-stu-id="c106a-215">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="c106a-216">Přidá službu identity `UserManager`.</span><span class="sxs-lookup"><span data-stu-id="c106a-216">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="c106a-217">Přidejte `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="c106a-217">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="c106a-218">Aktualizace CreateModel</span><span class="sxs-lookup"><span data-stu-id="c106a-218">Update the CreateModel</span></span>

<span data-ttu-id="c106a-219">Aktualizujte konstruktor Create Page model tak, aby používal základní třídu `DI_BasePageModel`:</span><span class="sxs-lookup"><span data-stu-id="c106a-219">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="c106a-220">Aktualizujte metodu `CreateModel.OnPostAsync` na:</span><span class="sxs-lookup"><span data-stu-id="c106a-220">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="c106a-221">Přidejte ID uživatele do modelu `Contact`.</span><span class="sxs-lookup"><span data-stu-id="c106a-221">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="c106a-222">Ověřte, zda že má uživatel oprávnění k vytvoření kontakty obslužná rutina ověřování volejte.</span><span class="sxs-lookup"><span data-stu-id="c106a-222">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="c106a-223">Aktualizace IndexModel</span><span class="sxs-lookup"><span data-stu-id="c106a-223">Update the IndexModel</span></span>

<span data-ttu-id="c106a-224">Aktualizujte metodu `OnGetAsync`, aby se pro obecné uživatele zobrazovaly jenom schválené kontakty:</span><span class="sxs-lookup"><span data-stu-id="c106a-224">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="c106a-225">Aktualizace EditModel</span><span class="sxs-lookup"><span data-stu-id="c106a-225">Update the EditModel</span></span>

<span data-ttu-id="c106a-226">Přidejte obslužnou rutinu ověřování k ověření, že uživatel je vlastníkem kontaktu.</span><span class="sxs-lookup"><span data-stu-id="c106a-226">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="c106a-227">Vzhledem k tomu, že se ověřuje autorizace prostředků, atribut `[Authorize]` není dostatečný.</span><span class="sxs-lookup"><span data-stu-id="c106a-227">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="c106a-228">Aplikace nemá přístup k prostředku, když jsou vyhodnocovány atributy.</span><span class="sxs-lookup"><span data-stu-id="c106a-228">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="c106a-229">Autorizace na základě prostředků musí být nezbytné.</span><span class="sxs-lookup"><span data-stu-id="c106a-229">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="c106a-230">Kontroly musí provést, když má aplikace přístup k prostředku načtením v modelu stránky nebo načtením v rámci samotná obslužná rutina.</span><span class="sxs-lookup"><span data-stu-id="c106a-230">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="c106a-231">Často přistupovat k prostředku předáním klíč prostředku.</span><span class="sxs-lookup"><span data-stu-id="c106a-231">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="c106a-232">Aktualizace DeleteModel</span><span class="sxs-lookup"><span data-stu-id="c106a-232">Update the DeleteModel</span></span>

<span data-ttu-id="c106a-233">Aktualizace modelu stránku odstranit sloužící k ověření, že uživatel má oprávnění odstranit u kontaktu obslužnou rutinu ověřování.</span><span class="sxs-lookup"><span data-stu-id="c106a-233">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="c106a-234">Vložit do zobrazení autorizační službu</span><span class="sxs-lookup"><span data-stu-id="c106a-234">Inject the authorization service into the views</span></span>

<span data-ttu-id="c106a-235">V současné době ukazuje uživatelského rozhraní upravovat a odstraňovat kontakty, které uživatel nemůže upravovat odkazy.</span><span class="sxs-lookup"><span data-stu-id="c106a-235">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="c106a-236">Vložení autorizační služby do souboru *Pages/_ViewImports. cshtml* , aby bylo dostupné pro všechna zobrazení:</span><span class="sxs-lookup"><span data-stu-id="c106a-236">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="c106a-237">Předchozí kód přidá několik příkazů `using`.</span><span class="sxs-lookup"><span data-stu-id="c106a-237">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="c106a-238">Aktualizujte odkazy pro **Úpravy** a **odstranění** na *stránkách/kontakty/index. cshtml* , aby byly vygenerovány pouze pro uživatele s příslušnými oprávněními:</span><span class="sxs-lookup"><span data-stu-id="c106a-238">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="c106a-239">Skrytí propojení od uživatele, kteří nemají oprávnění ke změně dat nebude zabezpečení aplikace.</span><span class="sxs-lookup"><span data-stu-id="c106a-239">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="c106a-240">Skrytí propojení nastaví aplikaci jako přívětivější tím, že zobrazuje pouze platné odkazy.</span><span class="sxs-lookup"><span data-stu-id="c106a-240">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="c106a-241">Uživatelé můžou hack generované adres URL pro vyvolání funkce upravit a odstranit operací s daty, které není vlastní.</span><span class="sxs-lookup"><span data-stu-id="c106a-241">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="c106a-242">Stránka Razor nebo kontroleru musí vynucovat kontroly přístupu pro data zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="c106a-242">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="c106a-243">Podrobné informace o aktualizaci</span><span class="sxs-lookup"><span data-stu-id="c106a-243">Update Details</span></span>

<span data-ttu-id="c106a-244">Aktualizujte zobrazení podrobností, aby vedoucí mohli schválit nebo odmítnout kontaktů:</span><span class="sxs-lookup"><span data-stu-id="c106a-244">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="c106a-245">Model stránky podrobnosti aktualizace:</span><span class="sxs-lookup"><span data-stu-id="c106a-245">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="c106a-246">Přidat nebo odebrat uživatele k roli</span><span class="sxs-lookup"><span data-stu-id="c106a-246">Add or remove a user to a role</span></span>

<span data-ttu-id="c106a-247">Informace o [tomto problému najdete zde](https://github.com/dotnet/AspNetCore.Docs/issues/8502) :</span><span class="sxs-lookup"><span data-stu-id="c106a-247">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="c106a-248">Odebrání oprávnění uživatele.</span><span class="sxs-lookup"><span data-stu-id="c106a-248">Removing privileges from a user.</span></span> <span data-ttu-id="c106a-249">Například ztlumení uživatele v aplikaci chatu.</span><span class="sxs-lookup"><span data-stu-id="c106a-249">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="c106a-250">Přidání oprávnění pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="c106a-250">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="c106a-251">Rozdíly mezi výzvou a zakazující</span><span class="sxs-lookup"><span data-stu-id="c106a-251">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="c106a-252">Tato aplikace nastaví výchozí zásadu, která bude [vyžadovat ověřené uživatele](#require-authenticated-users).</span><span class="sxs-lookup"><span data-stu-id="c106a-252">This app sets the default policy to [require authenticated users](#require-authenticated-users).</span></span> <span data-ttu-id="c106a-253">Následující kód povoluje anonymní uživatele.</span><span class="sxs-lookup"><span data-stu-id="c106a-253">The following code allows anonymous users.</span></span> <span data-ttu-id="c106a-254">Anonymní uživatelé mají povoleno zobrazovat rozdíly mezi výzvou a zakázáním.</span><span class="sxs-lookup"><span data-stu-id="c106a-254">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="c106a-255">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="c106a-255">In the preceding code:</span></span>

* <span data-ttu-id="c106a-256">Pokud uživatel **není ověřen,** je vrácena `ChallengeResult`.</span><span class="sxs-lookup"><span data-stu-id="c106a-256">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="c106a-257">Po vrácení `ChallengeResult` se uživatel přesměruje na přihlašovací stránku.</span><span class="sxs-lookup"><span data-stu-id="c106a-257">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="c106a-258">Když je uživatel ověřený, ale není autorizovaný, vrátí se `ForbidResult`.</span><span class="sxs-lookup"><span data-stu-id="c106a-258">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="c106a-259">Po vrácení `ForbidResult` se uživatel přesměruje na stránku přístup byl odepřen.</span><span class="sxs-lookup"><span data-stu-id="c106a-259">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="c106a-260">Testování dokončené aplikace</span><span class="sxs-lookup"><span data-stu-id="c106a-260">Test the completed app</span></span>

<span data-ttu-id="c106a-261">Pokud jste ještě nenastavili heslo pro osazené uživatelské účty, nastavte heslo pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets#secret-manager) :</span><span class="sxs-lookup"><span data-stu-id="c106a-261">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="c106a-262">Zvolte silné heslo: použití osm nebo více znaků, aspoň jedno velké písmeno, číslo a symbol.</span><span class="sxs-lookup"><span data-stu-id="c106a-262">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="c106a-263">`Passw0rd!` například splňuje požadavky na silné heslo.</span><span class="sxs-lookup"><span data-stu-id="c106a-263">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="c106a-264">Spusťte následující příkaz ze složky projektu, kde `<PW>` je heslo:</span><span class="sxs-lookup"><span data-stu-id="c106a-264">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="c106a-265">Pokud má kontaktů:</span><span class="sxs-lookup"><span data-stu-id="c106a-265">If the app has contacts:</span></span>

* <span data-ttu-id="c106a-266">Odstraní všechny záznamy v `Contact` tabulce.</span><span class="sxs-lookup"><span data-stu-id="c106a-266">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="c106a-267">Restartujte aplikaci k přidání dat do databáze.</span><span class="sxs-lookup"><span data-stu-id="c106a-267">Restart the app to seed the database.</span></span>

<span data-ttu-id="c106a-268">Snadný způsob, jak otestovat dokončená aplikace je spustíte tři různé prohlížeče (nebo incognito/InPrivate relace).</span><span class="sxs-lookup"><span data-stu-id="c106a-268">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="c106a-269">V jednom prohlížeči Zaregistrujte nového uživatele (například `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="c106a-269">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="c106a-270">Přihlaste se k každým prohlížečem s jiným uživatelem.</span><span class="sxs-lookup"><span data-stu-id="c106a-270">Sign in to each browser with a different user.</span></span> <span data-ttu-id="c106a-271">Ověřte následující operace:</span><span class="sxs-lookup"><span data-stu-id="c106a-271">Verify the following operations:</span></span>

* <span data-ttu-id="c106a-272">Registrovaných uživatelů můžete zobrazit všechny schválené kontaktní údaje.</span><span class="sxs-lookup"><span data-stu-id="c106a-272">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="c106a-273">Registrovaných uživatelů můžete upravit nebo odstranit svá vlastní data.</span><span class="sxs-lookup"><span data-stu-id="c106a-273">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="c106a-274">Správci mohou schvalovat a odmítat kontaktní údaje.</span><span class="sxs-lookup"><span data-stu-id="c106a-274">Managers can approve/reject contact data.</span></span> <span data-ttu-id="c106a-275">Zobrazení `Details` zobrazuje tlačítka **schválení** a **odmítnutí** .</span><span class="sxs-lookup"><span data-stu-id="c106a-275">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="c106a-276">Správci můžou schvalovat a odmítat a upravit nebo odstranit všechna data.</span><span class="sxs-lookup"><span data-stu-id="c106a-276">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="c106a-277">Uživatel</span><span class="sxs-lookup"><span data-stu-id="c106a-277">User</span></span>                | <span data-ttu-id="c106a-278">Nasazených aplikací</span><span class="sxs-lookup"><span data-stu-id="c106a-278">Seeded by the app</span></span> | <span data-ttu-id="c106a-279">Možnosti</span><span class="sxs-lookup"><span data-stu-id="c106a-279">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="c106a-280">Ne</span><span class="sxs-lookup"><span data-stu-id="c106a-280">No</span></span>                | <span data-ttu-id="c106a-281">Upravit nebo odstranit vlastní data.</span><span class="sxs-lookup"><span data-stu-id="c106a-281">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="c106a-282">Ano</span><span class="sxs-lookup"><span data-stu-id="c106a-282">Yes</span></span>               | <span data-ttu-id="c106a-283">Vlastní data, schvalovat a odmítat a upravit nebo odstranit.</span><span class="sxs-lookup"><span data-stu-id="c106a-283">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="c106a-284">Ano</span><span class="sxs-lookup"><span data-stu-id="c106a-284">Yes</span></span>               | <span data-ttu-id="c106a-285">Schválit nebo odmítnout a upravit nebo odstranit všechna data.</span><span class="sxs-lookup"><span data-stu-id="c106a-285">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="c106a-286">Vytvoření kontaktu v prohlížeči na správce.</span><span class="sxs-lookup"><span data-stu-id="c106a-286">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="c106a-287">Zkopírujte adresu URL pro odstranění a upravit z kontaktujte správce.</span><span class="sxs-lookup"><span data-stu-id="c106a-287">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="c106a-288">Vložte tyto odkazy do testů webového prohlížeče k ověření, že testovací uživatel nemůže provádět tyto operace.</span><span class="sxs-lookup"><span data-stu-id="c106a-288">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="c106a-289">Vytvořit úvodní aplikaci</span><span class="sxs-lookup"><span data-stu-id="c106a-289">Create the starter app</span></span>

* <span data-ttu-id="c106a-290">Vytvoření aplikace stránky Razor s názvem "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="c106a-290">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="c106a-291">Vytvořte aplikaci pomocí **individuálních uživatelských účtů**.</span><span class="sxs-lookup"><span data-stu-id="c106a-291">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="c106a-292">Pojmenujte ji "ContactManager" tak obor názvů odpovídá oboru názvů použitého v ukázce.</span><span class="sxs-lookup"><span data-stu-id="c106a-292">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="c106a-293">`-uld` určuje LocalDB místo SQLite.</span><span class="sxs-lookup"><span data-stu-id="c106a-293">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="c106a-294">Přidat *modely/kontakt. cs*:</span><span class="sxs-lookup"><span data-stu-id="c106a-294">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="c106a-295">Generování `Contact` modelu.</span><span class="sxs-lookup"><span data-stu-id="c106a-295">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="c106a-296">Vytvořte počáteční migraci a aktualizaci databáze:</span><span class="sxs-lookup"><span data-stu-id="c106a-296">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="c106a-297">Pokud dojde k chybě s příkazem `dotnet aspnet-codegenerator razorpage`, přečtěte si [Tento problém GitHub](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="c106a-297">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="c106a-298">Aktualizujte kotvu **ContactManager** v souboru *Pages/shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="c106a-298">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="c106a-299">Testování aplikace pomocí vytváření, úpravy a odstranění kontaktu</span><span class="sxs-lookup"><span data-stu-id="c106a-299">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="c106a-300">Přidání dat do databáze</span><span class="sxs-lookup"><span data-stu-id="c106a-300">Seed the database</span></span>

<span data-ttu-id="c106a-301">Do složky *data* přidejte třídu [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) :</span><span class="sxs-lookup"><span data-stu-id="c106a-301">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="c106a-302">Volat `SeedData.Initialize` z `Main`:</span><span class="sxs-lookup"><span data-stu-id="c106a-302">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="c106a-303">Otestujte, že aplikace naplnila databázi.</span><span class="sxs-lookup"><span data-stu-id="c106a-303">Test that the app seeded the database.</span></span> <span data-ttu-id="c106a-304">Pokud existují nějaké řádky v kontaktu DB, metoda počáteční hodnoty není spuštěna.</span><span class="sxs-lookup"><span data-stu-id="c106a-304">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="c106a-305">Tento kurz ukazuje, jak vytvořit webovou aplikaci ASP.NET Core s uživatelskými daty chráněnými autorizací.</span><span class="sxs-lookup"><span data-stu-id="c106a-305">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="c106a-306">Zobrazí seznam kontakty, které ověřeným uživatelům (registrovaných) jste vytvořili.</span><span class="sxs-lookup"><span data-stu-id="c106a-306">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="c106a-307">Existují tři skupiny zabezpečení:</span><span class="sxs-lookup"><span data-stu-id="c106a-307">There are three security groups:</span></span>

* <span data-ttu-id="c106a-308">**Registrovaní uživatelé** můžou zobrazit všechna schválená data a můžou upravovat nebo odstraňovat vlastní data.</span><span class="sxs-lookup"><span data-stu-id="c106a-308">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="c106a-309">**Manažeři** mohou schvalovat nebo odmítat kontaktní data.</span><span class="sxs-lookup"><span data-stu-id="c106a-309">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="c106a-310">Uživatelé vidí pouze schválené kontakty.</span><span class="sxs-lookup"><span data-stu-id="c106a-310">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="c106a-311">**Správci** mohou schvalovat nebo odmítat a upravovat nebo odstraňovat jakákoli data.</span><span class="sxs-lookup"><span data-stu-id="c106a-311">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="c106a-312">Na následujícím obrázku je uživatel Rick (`rick@example.com`) přihlášen.</span><span class="sxs-lookup"><span data-stu-id="c106a-312">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="c106a-313">Rick může zobrazit pouze schválené kontakty a **upravovat**/**Odstranit**/**vytvořit nové** odkazy na své kontakty.</span><span class="sxs-lookup"><span data-stu-id="c106a-313">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="c106a-314">Jenom poslední záznam vytvořený pomocí Rick zobrazí odkazy pro **Úpravy** a **odstranění** .</span><span class="sxs-lookup"><span data-stu-id="c106a-314">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="c106a-315">Ostatní uživatelé neuvidí poslední záznam, dokud správce nebo správce změní stav na "Schváleno".</span><span class="sxs-lookup"><span data-stu-id="c106a-315">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Snímek obrazovky zobrazující Rick přihlášení](secure-data/_static/rick.png)

<span data-ttu-id="c106a-317">Na následujícím obrázku je `manager@contoso.com` přihlásit se a v roli manažera:</span><span class="sxs-lookup"><span data-stu-id="c106a-317">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Snímek obrazovky zobrazující přihlášený manager@contoso.com](secure-data/_static/manager1.png)

<span data-ttu-id="c106a-319">Následující obrázek ukazuje vedoucí zobrazení podrobností o kontaktu:</span><span class="sxs-lookup"><span data-stu-id="c106a-319">The following image shows the managers details view of a contact:</span></span>

![Zobrazení manažera kontaktu](secure-data/_static/manager.png)

<span data-ttu-id="c106a-321">Tlačítka **schválení** a **odmítnutí** se zobrazují pouze pro manažery a správce.</span><span class="sxs-lookup"><span data-stu-id="c106a-321">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="c106a-322">Na následujícím obrázku je `admin@contoso.com` přihlásit se a v roli správce:</span><span class="sxs-lookup"><span data-stu-id="c106a-322">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Snímek obrazovky zobrazující přihlášený admin@contoso.com](secure-data/_static/admin.png)

<span data-ttu-id="c106a-324">Správce má všechna oprávnění.</span><span class="sxs-lookup"><span data-stu-id="c106a-324">The administrator has all privileges.</span></span> <span data-ttu-id="c106a-325">Může číst/upravovat/odstraňovat všechny kontakty a změnit stav kontakty.</span><span class="sxs-lookup"><span data-stu-id="c106a-325">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="c106a-326">Aplikace byla vytvořena pomocí [generování uživatelského rozhraní](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) následujícího modelu `Contact`:</span><span class="sxs-lookup"><span data-stu-id="c106a-326">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="c106a-327">Ukázka obsahuje následující rutiny autorizace:</span><span class="sxs-lookup"><span data-stu-id="c106a-327">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="c106a-328">`ContactIsOwnerAuthorizationHandler`: zajišťuje, aby uživatel mohl upravovat pouze svá data.</span><span class="sxs-lookup"><span data-stu-id="c106a-328">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="c106a-329">`ContactManagerAuthorizationHandler`: umožňuje správcům schvalovat nebo odmítat kontakty.</span><span class="sxs-lookup"><span data-stu-id="c106a-329">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="c106a-330">`ContactAdministratorsAuthorizationHandler`: umožňuje správcům schvalovat nebo odmítat kontakty a upravovat nebo odstraňovat kontakty.</span><span class="sxs-lookup"><span data-stu-id="c106a-330">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c106a-331">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="c106a-331">Prerequisites</span></span>

<span data-ttu-id="c106a-332">V tomto kurzu je advanced.</span><span class="sxs-lookup"><span data-stu-id="c106a-332">This tutorial is advanced.</span></span> <span data-ttu-id="c106a-333">Měli byste se seznámit s:</span><span class="sxs-lookup"><span data-stu-id="c106a-333">You should be familiar with:</span></span>

* [<span data-ttu-id="c106a-334">Jádro ASP.NET</span><span class="sxs-lookup"><span data-stu-id="c106a-334">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="c106a-335">Ověřování</span><span class="sxs-lookup"><span data-stu-id="c106a-335">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="c106a-336">Potvrzení účtu a obnovení hesla</span><span class="sxs-lookup"><span data-stu-id="c106a-336">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="c106a-337">Autorizace</span><span class="sxs-lookup"><span data-stu-id="c106a-337">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="c106a-338">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="c106a-338">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="c106a-339">Starter a dokončené aplikace</span><span class="sxs-lookup"><span data-stu-id="c106a-339">The starter and completed app</span></span>

<span data-ttu-id="c106a-340">[Stáhněte](xref:index#how-to-download-a-sample) [dokončenou](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c106a-340">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="c106a-341">[Otestujte](#test-the-completed-app) dokončenou aplikaci, abyste se seznámili se svými funkcemi zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="c106a-341">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="c106a-342">Úvodní aplikaci</span><span class="sxs-lookup"><span data-stu-id="c106a-342">The starter app</span></span>

<span data-ttu-id="c106a-343">[Stáhněte](xref:index#how-to-download-a-sample) si [úvodní](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c106a-343">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="c106a-344">Spusťte aplikaci, klepněte na odkaz **ContactManager** a ověřte, že můžete vytvořit, upravit a odstranit kontakt.</span><span class="sxs-lookup"><span data-stu-id="c106a-344">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="c106a-345">Zabezpečení dat uživatele</span><span class="sxs-lookup"><span data-stu-id="c106a-345">Secure user data</span></span>

<span data-ttu-id="c106a-346">Následující oddíly mají všechny hlavní kroky k vytvoření aplikace pro data zabezpečení uživatele.</span><span class="sxs-lookup"><span data-stu-id="c106a-346">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="c106a-347">Vám může být užitečné k odkazování na dokončení projektu.</span><span class="sxs-lookup"><span data-stu-id="c106a-347">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="c106a-348">Tie kontaktní údaje pro uživatele</span><span class="sxs-lookup"><span data-stu-id="c106a-348">Tie the contact data to the user</span></span>

<span data-ttu-id="c106a-349">Pomocí ID uživatele [Identity](xref:security/authentication/identity) ASP.NET zajistěte, aby mohli uživatelé upravovat data, ale ne jiná data uživatelů.</span><span class="sxs-lookup"><span data-stu-id="c106a-349">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="c106a-350">Do modelu `Contact` přidejte `OwnerID` a `ContactStatus`:</span><span class="sxs-lookup"><span data-stu-id="c106a-350">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="c106a-351">`OwnerID` je ID uživatele z tabulky `AspNetUser` v databázi [identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="c106a-351">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="c106a-352">Pole `Status` určuje, zda je kontakt viditelný pro obecné uživatele.</span><span class="sxs-lookup"><span data-stu-id="c106a-352">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="c106a-353">Vytvořte novou migraci a aktualizaci databáze:</span><span class="sxs-lookup"><span data-stu-id="c106a-353">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="c106a-354">Přidání služby Role na identitu</span><span class="sxs-lookup"><span data-stu-id="c106a-354">Add Role services to Identity</span></span>

<span data-ttu-id="c106a-355">Připojit [Přidat role](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) pro přidání služeb role:</span><span class="sxs-lookup"><span data-stu-id="c106a-355">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a><span data-ttu-id="c106a-356">Vyžadovat ověření uživatelé</span><span class="sxs-lookup"><span data-stu-id="c106a-356">Require authenticated users</span></span>

<span data-ttu-id="c106a-357">Nastavte výchozí zásady ověřování tak, aby vyžadovala ověření uživatelů:</span><span class="sxs-lookup"><span data-stu-id="c106a-357">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="c106a-358">Ověřování na úrovni této stránky, řadiče nebo metody akce lze odhlásit pomocí atributu `[AllowAnonymous]`.</span><span class="sxs-lookup"><span data-stu-id="c106a-358">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="c106a-359">Nastavení výchozí zásady ověřování tak, aby vyžadovala ověření uživatelů chrání nově přidané Razor Pages a kontrolery.</span><span class="sxs-lookup"><span data-stu-id="c106a-359">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="c106a-360">Pokud je ve výchozím nastavení vyžadováno ověřování, je bezpečnější než spoléhání na nové řadiče a Razor Pages pro zahrnutí atributu `[Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="c106a-360">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="c106a-361">Přidejte [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) na stránky rejstřík, o a kontakt, aby anonymní uživatelé mohli získat informace o lokalitě před jejich registrací.</span><span class="sxs-lookup"><span data-stu-id="c106a-361">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="c106a-362">Konfigurace testovací účet</span><span class="sxs-lookup"><span data-stu-id="c106a-362">Configure the test account</span></span>

<span data-ttu-id="c106a-363">Třída `SeedData` vytvoří dva účty: správce a manažer.</span><span class="sxs-lookup"><span data-stu-id="c106a-363">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="c106a-364">Pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets) nastavte heslo pro tyto účty.</span><span class="sxs-lookup"><span data-stu-id="c106a-364">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="c106a-365">Nastavte heslo z adresáře projektu (adresář obsahující *program.cs*):</span><span class="sxs-lookup"><span data-stu-id="c106a-365">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="c106a-366">Pokud není zadáno silné heslo, je vyvolána výjimka při volání `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="c106a-366">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="c106a-367">Aktualizujte `Main` pro použití testovacího hesla:</span><span class="sxs-lookup"><span data-stu-id="c106a-367">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="c106a-368">Vytvoření testovacích účtů a aktualizovat kontakty</span><span class="sxs-lookup"><span data-stu-id="c106a-368">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="c106a-369">Aktualizujte metodu `Initialize` ve třídě `SeedData` k vytvoření testovacích účtů:</span><span class="sxs-lookup"><span data-stu-id="c106a-369">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="c106a-370">Přidejte do kontaktů ID uživatele a `ContactStatus` správce.</span><span class="sxs-lookup"><span data-stu-id="c106a-370">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="c106a-371">Proveďte jednu z kontakty "Submitted" a jedné "byl odmítnut".</span><span class="sxs-lookup"><span data-stu-id="c106a-371">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="c106a-372">Přidáte ID uživatele a stav pro všechny kontakty.</span><span class="sxs-lookup"><span data-stu-id="c106a-372">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="c106a-373">Je zobrazena pouze jeden kontakt:</span><span class="sxs-lookup"><span data-stu-id="c106a-373">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="c106a-374">Vytvořit vlastníka, správce a Správce autorizace obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="c106a-374">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="c106a-375">Vytvořte složku *autorizace* a v ní vytvořte třídu `ContactIsOwnerAuthorizationHandler`.</span><span class="sxs-lookup"><span data-stu-id="c106a-375">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="c106a-376">`ContactIsOwnerAuthorizationHandler` ověří, že uživatel pracuje na prostředku, který je vlastníkem prostředku.</span><span class="sxs-lookup"><span data-stu-id="c106a-376">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="c106a-377">`ContactIsOwnerAuthorizationHandler` volá [kontext. Úspěšné](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , pokud je aktuální ověřený uživatel vlastníkem kontaktu.</span><span class="sxs-lookup"><span data-stu-id="c106a-377">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="c106a-378">Obslužné rutiny autorizace obecně:</span><span class="sxs-lookup"><span data-stu-id="c106a-378">Authorization handlers generally:</span></span>

* <span data-ttu-id="c106a-379">Vrátí `context.Succeed`, pokud jsou splněny požadavky.</span><span class="sxs-lookup"><span data-stu-id="c106a-379">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="c106a-380">Vrátí `Task.CompletedTask`, pokud nejsou splněny požadavky.</span><span class="sxs-lookup"><span data-stu-id="c106a-380">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="c106a-381">`Task.CompletedTask` není úspěch nebo neúspěch&mdash;umožňuje spuštění dalších obslužných rutin autorizace.</span><span class="sxs-lookup"><span data-stu-id="c106a-381">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="c106a-382">Pokud potřebujete explicitně selhat, vraťte [kontext. Selhání](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="c106a-382">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="c106a-383">Aplikaci umožňuje kontaktní vlastníkům upravit, odstranit a vytvořit svoje vlastní data.</span><span class="sxs-lookup"><span data-stu-id="c106a-383">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="c106a-384">`ContactIsOwnerAuthorizationHandler` nemusí kontrolovat operaci předanou parametrem požadavku.</span><span class="sxs-lookup"><span data-stu-id="c106a-384">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="c106a-385">Vytvořte obslužnou rutinu Správce autorizací</span><span class="sxs-lookup"><span data-stu-id="c106a-385">Create a manager authorization handler</span></span>

<span data-ttu-id="c106a-386">Ve složce pro *autorizaci* vytvořte třídu `ContactManagerAuthorizationHandler`.</span><span class="sxs-lookup"><span data-stu-id="c106a-386">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="c106a-387">`ContactManagerAuthorizationHandler` ověří uživatele, který pracuje na prostředku, jako správce.</span><span class="sxs-lookup"><span data-stu-id="c106a-387">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="c106a-388">Pouze vedoucí mohli schválit nebo odmítnout změny obsahu (nové nebo změněné).</span><span class="sxs-lookup"><span data-stu-id="c106a-388">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="c106a-389">Vytvořte obslužnou rutinu Správce autorizací</span><span class="sxs-lookup"><span data-stu-id="c106a-389">Create an administrator authorization handler</span></span>

<span data-ttu-id="c106a-390">Ve složce pro *autorizaci* vytvořte třídu `ContactAdministratorsAuthorizationHandler`.</span><span class="sxs-lookup"><span data-stu-id="c106a-390">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="c106a-391">`ContactAdministratorsAuthorizationHandler` ověří uživatele, který pracuje na prostředku, jako správce.</span><span class="sxs-lookup"><span data-stu-id="c106a-391">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="c106a-392">Správce může provádět všechny operace.</span><span class="sxs-lookup"><span data-stu-id="c106a-392">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="c106a-393">Zaregistrujte obslužné rutiny autorizace</span><span class="sxs-lookup"><span data-stu-id="c106a-393">Register the authorization handlers</span></span>

<span data-ttu-id="c106a-394">Služby používající Entity Framework Core musí být registrovány pro [vkládání závislostí](xref:fundamentals/dependency-injection) pomocí [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="c106a-394">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="c106a-395">`ContactIsOwnerAuthorizationHandler` používá ASP.NET Core [identity](xref:security/authentication/identity), která je postavená na Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="c106a-395">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="c106a-396">Zaregistrujte obslužné rutiny s kolekcí služeb, aby byly k dispozici pro `ContactsController` prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c106a-396">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="c106a-397">Do konce `ConfigureServices`přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="c106a-397">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="c106a-398">`ContactAdministratorsAuthorizationHandler` a `ContactManagerAuthorizationHandler` jsou přidány jako singleton.</span><span class="sxs-lookup"><span data-stu-id="c106a-398">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="c106a-399">Jsou to jednoznačné, protože nepoužívají EF a všechny potřebné informace jsou v parametru `Context` `HandleRequirementAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="c106a-399">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="c106a-400">Povolení podpory</span><span class="sxs-lookup"><span data-stu-id="c106a-400">Support authorization</span></span>

<span data-ttu-id="c106a-401">V této části se aktualizace stránky Razor a přidejte třídu požadavků operace.</span><span class="sxs-lookup"><span data-stu-id="c106a-401">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="c106a-402">Zkontrolujte požadavky na třídy kontaktní operace</span><span class="sxs-lookup"><span data-stu-id="c106a-402">Review the contact operations requirements class</span></span>

<span data-ttu-id="c106a-403">Zkontrolujte třídu `ContactOperations`.</span><span class="sxs-lookup"><span data-stu-id="c106a-403">Review the `ContactOperations` class.</span></span> <span data-ttu-id="c106a-404">Tato třída obsahuje požadavky na aplikace podporuje:</span><span class="sxs-lookup"><span data-stu-id="c106a-404">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="c106a-405">Vytvoření základní třídu pro stránky Razor kontakty</span><span class="sxs-lookup"><span data-stu-id="c106a-405">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="c106a-406">Vytvořte základní třídu, která obsahuje služby využité v kontaktech stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="c106a-406">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="c106a-407">Základní třída vloží kód inicializace na jednom místě:</span><span class="sxs-lookup"><span data-stu-id="c106a-407">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="c106a-408">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="c106a-408">The preceding code:</span></span>

* <span data-ttu-id="c106a-409">Přidá službu `IAuthorizationService` pro přístup k obslužným rutinám autorizace.</span><span class="sxs-lookup"><span data-stu-id="c106a-409">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="c106a-410">Přidá službu identity `UserManager`.</span><span class="sxs-lookup"><span data-stu-id="c106a-410">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="c106a-411">Přidejte `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="c106a-411">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="c106a-412">Aktualizace CreateModel</span><span class="sxs-lookup"><span data-stu-id="c106a-412">Update the CreateModel</span></span>

<span data-ttu-id="c106a-413">Aktualizujte konstruktor Create Page model tak, aby používal základní třídu `DI_BasePageModel`:</span><span class="sxs-lookup"><span data-stu-id="c106a-413">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="c106a-414">Aktualizujte metodu `CreateModel.OnPostAsync` na:</span><span class="sxs-lookup"><span data-stu-id="c106a-414">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="c106a-415">Přidejte ID uživatele do modelu `Contact`.</span><span class="sxs-lookup"><span data-stu-id="c106a-415">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="c106a-416">Ověřte, zda že má uživatel oprávnění k vytvoření kontakty obslužná rutina ověřování volejte.</span><span class="sxs-lookup"><span data-stu-id="c106a-416">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="c106a-417">Aktualizace IndexModel</span><span class="sxs-lookup"><span data-stu-id="c106a-417">Update the IndexModel</span></span>

<span data-ttu-id="c106a-418">Aktualizujte metodu `OnGetAsync`, aby se pro obecné uživatele zobrazovaly jenom schválené kontakty:</span><span class="sxs-lookup"><span data-stu-id="c106a-418">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="c106a-419">Aktualizace EditModel</span><span class="sxs-lookup"><span data-stu-id="c106a-419">Update the EditModel</span></span>

<span data-ttu-id="c106a-420">Přidejte obslužnou rutinu ověřování k ověření, že uživatel je vlastníkem kontaktu.</span><span class="sxs-lookup"><span data-stu-id="c106a-420">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="c106a-421">Vzhledem k tomu, že se ověřuje autorizace prostředků, atribut `[Authorize]` není dostatečný.</span><span class="sxs-lookup"><span data-stu-id="c106a-421">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="c106a-422">Aplikace nemá přístup k prostředku, když jsou vyhodnocovány atributy.</span><span class="sxs-lookup"><span data-stu-id="c106a-422">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="c106a-423">Autorizace na základě prostředků musí být nezbytné.</span><span class="sxs-lookup"><span data-stu-id="c106a-423">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="c106a-424">Kontroly musí provést, když má aplikace přístup k prostředku načtením v modelu stránky nebo načtením v rámci samotná obslužná rutina.</span><span class="sxs-lookup"><span data-stu-id="c106a-424">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="c106a-425">Často přistupovat k prostředku předáním klíč prostředku.</span><span class="sxs-lookup"><span data-stu-id="c106a-425">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="c106a-426">Aktualizace DeleteModel</span><span class="sxs-lookup"><span data-stu-id="c106a-426">Update the DeleteModel</span></span>

<span data-ttu-id="c106a-427">Aktualizace modelu stránku odstranit sloužící k ověření, že uživatel má oprávnění odstranit u kontaktu obslužnou rutinu ověřování.</span><span class="sxs-lookup"><span data-stu-id="c106a-427">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="c106a-428">Vložit do zobrazení autorizační službu</span><span class="sxs-lookup"><span data-stu-id="c106a-428">Inject the authorization service into the views</span></span>

<span data-ttu-id="c106a-429">V současné době ukazuje uživatelského rozhraní upravovat a odstraňovat kontakty, které uživatel nemůže upravovat odkazy.</span><span class="sxs-lookup"><span data-stu-id="c106a-429">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="c106a-430">Vložení autorizační služby do souboru *views/_ViewImports. cshtml* , aby byla dostupná pro všechna zobrazení:</span><span class="sxs-lookup"><span data-stu-id="c106a-430">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="c106a-431">Předchozí kód přidá několik příkazů `using`.</span><span class="sxs-lookup"><span data-stu-id="c106a-431">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="c106a-432">Aktualizujte odkazy pro **Úpravy** a **odstranění** na *stránkách/kontakty/index. cshtml* , aby byly vygenerovány pouze pro uživatele s příslušnými oprávněními:</span><span class="sxs-lookup"><span data-stu-id="c106a-432">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="c106a-433">Skrytí propojení od uživatele, kteří nemají oprávnění ke změně dat nebude zabezpečení aplikace.</span><span class="sxs-lookup"><span data-stu-id="c106a-433">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="c106a-434">Skrytí propojení nastaví aplikaci jako přívětivější tím, že zobrazuje pouze platné odkazy.</span><span class="sxs-lookup"><span data-stu-id="c106a-434">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="c106a-435">Uživatelé můžou hack generované adres URL pro vyvolání funkce upravit a odstranit operací s daty, které není vlastní.</span><span class="sxs-lookup"><span data-stu-id="c106a-435">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="c106a-436">Stránka Razor nebo kontroleru musí vynucovat kontroly přístupu pro data zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="c106a-436">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="c106a-437">Podrobné informace o aktualizaci</span><span class="sxs-lookup"><span data-stu-id="c106a-437">Update Details</span></span>

<span data-ttu-id="c106a-438">Aktualizujte zobrazení podrobností, aby vedoucí mohli schválit nebo odmítnout kontaktů:</span><span class="sxs-lookup"><span data-stu-id="c106a-438">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="c106a-439">Model stránky podrobnosti aktualizace:</span><span class="sxs-lookup"><span data-stu-id="c106a-439">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="c106a-440">Přidat nebo odebrat uživatele k roli</span><span class="sxs-lookup"><span data-stu-id="c106a-440">Add or remove a user to a role</span></span>

<span data-ttu-id="c106a-441">Informace o [tomto problému najdete zde](https://github.com/dotnet/AspNetCore.Docs/issues/8502) :</span><span class="sxs-lookup"><span data-stu-id="c106a-441">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="c106a-442">Odebrání oprávnění uživatele.</span><span class="sxs-lookup"><span data-stu-id="c106a-442">Removing privileges from a user.</span></span> <span data-ttu-id="c106a-443">Například ztlumení uživatele v aplikaci chatu.</span><span class="sxs-lookup"><span data-stu-id="c106a-443">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="c106a-444">Přidání oprávnění pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="c106a-444">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="c106a-445">Testování dokončené aplikace</span><span class="sxs-lookup"><span data-stu-id="c106a-445">Test the completed app</span></span>

<span data-ttu-id="c106a-446">Pokud jste ještě nenastavili heslo pro osazené uživatelské účty, nastavte heslo pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets#secret-manager) :</span><span class="sxs-lookup"><span data-stu-id="c106a-446">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="c106a-447">Zvolte silné heslo: použití osm nebo více znaků, aspoň jedno velké písmeno, číslo a symbol.</span><span class="sxs-lookup"><span data-stu-id="c106a-447">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="c106a-448">`Passw0rd!` například splňuje požadavky na silné heslo.</span><span class="sxs-lookup"><span data-stu-id="c106a-448">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="c106a-449">Spusťte následující příkaz ze složky projektu, kde `<PW>` je heslo:</span><span class="sxs-lookup"><span data-stu-id="c106a-449">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="c106a-450">Zrušení a aktualizace databáze</span><span class="sxs-lookup"><span data-stu-id="c106a-450">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="c106a-451">Restartujte aplikaci k přidání dat do databáze.</span><span class="sxs-lookup"><span data-stu-id="c106a-451">Restart the app to seed the database.</span></span>

<span data-ttu-id="c106a-452">Snadný způsob, jak otestovat dokončená aplikace je spustíte tři různé prohlížeče (nebo incognito/InPrivate relace).</span><span class="sxs-lookup"><span data-stu-id="c106a-452">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="c106a-453">V jednom prohlížeči Zaregistrujte nového uživatele (například `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="c106a-453">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="c106a-454">Přihlaste se k každým prohlížečem s jiným uživatelem.</span><span class="sxs-lookup"><span data-stu-id="c106a-454">Sign in to each browser with a different user.</span></span> <span data-ttu-id="c106a-455">Ověřte následující operace:</span><span class="sxs-lookup"><span data-stu-id="c106a-455">Verify the following operations:</span></span>

* <span data-ttu-id="c106a-456">Registrovaných uživatelů můžete zobrazit všechny schválené kontaktní údaje.</span><span class="sxs-lookup"><span data-stu-id="c106a-456">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="c106a-457">Registrovaných uživatelů můžete upravit nebo odstranit svá vlastní data.</span><span class="sxs-lookup"><span data-stu-id="c106a-457">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="c106a-458">Správci mohou schvalovat a odmítat kontaktní údaje.</span><span class="sxs-lookup"><span data-stu-id="c106a-458">Managers can approve/reject contact data.</span></span> <span data-ttu-id="c106a-459">Zobrazení `Details` zobrazuje tlačítka **schválení** a **odmítnutí** .</span><span class="sxs-lookup"><span data-stu-id="c106a-459">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="c106a-460">Správci můžou schvalovat a odmítat a upravit nebo odstranit všechna data.</span><span class="sxs-lookup"><span data-stu-id="c106a-460">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="c106a-461">Uživatel</span><span class="sxs-lookup"><span data-stu-id="c106a-461">User</span></span>                | <span data-ttu-id="c106a-462">Nasazených aplikací</span><span class="sxs-lookup"><span data-stu-id="c106a-462">Seeded by the app</span></span> | <span data-ttu-id="c106a-463">Možnosti</span><span class="sxs-lookup"><span data-stu-id="c106a-463">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="c106a-464">Ne</span><span class="sxs-lookup"><span data-stu-id="c106a-464">No</span></span>                | <span data-ttu-id="c106a-465">Upravit nebo odstranit vlastní data.</span><span class="sxs-lookup"><span data-stu-id="c106a-465">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="c106a-466">Ano</span><span class="sxs-lookup"><span data-stu-id="c106a-466">Yes</span></span>               | <span data-ttu-id="c106a-467">Vlastní data, schvalovat a odmítat a upravit nebo odstranit.</span><span class="sxs-lookup"><span data-stu-id="c106a-467">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="c106a-468">Ano</span><span class="sxs-lookup"><span data-stu-id="c106a-468">Yes</span></span>               | <span data-ttu-id="c106a-469">Schválit nebo odmítnout a upravit nebo odstranit všechna data.</span><span class="sxs-lookup"><span data-stu-id="c106a-469">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="c106a-470">Vytvoření kontaktu v prohlížeči na správce.</span><span class="sxs-lookup"><span data-stu-id="c106a-470">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="c106a-471">Zkopírujte adresu URL pro odstranění a upravit z kontaktujte správce.</span><span class="sxs-lookup"><span data-stu-id="c106a-471">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="c106a-472">Vložte tyto odkazy do testů webového prohlížeče k ověření, že testovací uživatel nemůže provádět tyto operace.</span><span class="sxs-lookup"><span data-stu-id="c106a-472">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="c106a-473">Vytvořit úvodní aplikaci</span><span class="sxs-lookup"><span data-stu-id="c106a-473">Create the starter app</span></span>

* <span data-ttu-id="c106a-474">Vytvoření aplikace stránky Razor s názvem "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="c106a-474">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="c106a-475">Vytvořte aplikaci pomocí **individuálních uživatelských účtů**.</span><span class="sxs-lookup"><span data-stu-id="c106a-475">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="c106a-476">Pojmenujte ji "ContactManager" tak obor názvů odpovídá oboru názvů použitého v ukázce.</span><span class="sxs-lookup"><span data-stu-id="c106a-476">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="c106a-477">`-uld` určuje LocalDB místo SQLite.</span><span class="sxs-lookup"><span data-stu-id="c106a-477">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="c106a-478">Přidat *modely/kontakt. cs*:</span><span class="sxs-lookup"><span data-stu-id="c106a-478">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="c106a-479">Generování `Contact` modelu.</span><span class="sxs-lookup"><span data-stu-id="c106a-479">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="c106a-480">Vytvořte počáteční migraci a aktualizaci databáze:</span><span class="sxs-lookup"><span data-stu-id="c106a-480">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="c106a-481">Aktualizujte kotvu **ContactManager** v souboru *pages/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="c106a-481">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="c106a-482">Testování aplikace pomocí vytváření, úpravy a odstranění kontaktu</span><span class="sxs-lookup"><span data-stu-id="c106a-482">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="c106a-483">Přidání dat do databáze</span><span class="sxs-lookup"><span data-stu-id="c106a-483">Seed the database</span></span>

<span data-ttu-id="c106a-484">Do složky *data* přidejte třídu [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) .</span><span class="sxs-lookup"><span data-stu-id="c106a-484">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="c106a-485">Volat `SeedData.Initialize` z `Main`:</span><span class="sxs-lookup"><span data-stu-id="c106a-485">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="c106a-486">Otestujte, že aplikace naplnila databázi.</span><span class="sxs-lookup"><span data-stu-id="c106a-486">Test that the app seeded the database.</span></span> <span data-ttu-id="c106a-487">Pokud existují nějaké řádky v kontaktu DB, metoda počáteční hodnoty není spuštěna.</span><span class="sxs-lookup"><span data-stu-id="c106a-487">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="c106a-488">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c106a-488">Additional resources</span></span>

* [<span data-ttu-id="c106a-489">Vytvoření webové aplikace .NET Core a SQL Database v Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c106a-489">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="c106a-490">[ASP.NET Core autorizačního prostředí](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="c106a-490">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="c106a-491">Toto testovací prostředí obsahuje větší podrobnosti o funkcích zabezpečení v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="c106a-491">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="c106a-492">Vlastní autorizace na základě zásad</span><span class="sxs-lookup"><span data-stu-id="c106a-492">Custom policy-based authorization</span></span>](xref:security/authorization/policies)

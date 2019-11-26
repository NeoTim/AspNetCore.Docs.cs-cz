---
title: Vytvoření aplikace ASP.NET Core s uživatelskými daty chráněnými autorizací
author: rick-anderson
description: Zjistěte, jak vytvořit aplikace Razor Pages s uživatelskými daty chráněnými autorizací. Zahrnuje HTTPS, ověřování, zabezpečení ASP.NET Core Identity.
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
uid: security/authorization/secure-data
ms.openlocfilehash: 65c72d4dd457f85451796c5713bedebafec7a7de
ms.sourcegitcommit: 8157e5a351f49aeef3769f7d38b787b4386aad5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74239837"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="a4740-104">Vytvoření aplikace ASP.NET Core s uživatelskými daty chráněnými autorizací</span><span class="sxs-lookup"><span data-stu-id="a4740-104">Create an ASP.NET Core app with user data protected by authorization</span></span>

<span data-ttu-id="a4740-105">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Jan Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="a4740-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="a4740-106">Podívejte se na [Tento PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) pro ASP.NET Core verzi MVC.</span><span class="sxs-lookup"><span data-stu-id="a4740-106">See [this PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core MVC version.</span></span> <span data-ttu-id="a4740-107">Verze tohoto kurzu ASP.NET Core 1,1 je v [této](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) složce.</span><span class="sxs-lookup"><span data-stu-id="a4740-107">The ASP.NET Core 1.1 version of this tutorial is in [this](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) folder.</span></span> <span data-ttu-id="a4740-108">Ukázka 1,1 ASP.NET Core je v [ukázkách](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span><span class="sxs-lookup"><span data-stu-id="a4740-108">The 1.1 ASP.NET Core sample is in the [samples](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="a4740-109">Zobrazit [Tento PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="a4740-109">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a4740-110">Tento kurz ukazuje, jak vytvořit webovou aplikaci ASP.NET Core s uživatelskými daty chráněnými autorizací.</span><span class="sxs-lookup"><span data-stu-id="a4740-110">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="a4740-111">Zobrazí seznam kontakty, které ověřeným uživatelům (registrovaných) jste vytvořili.</span><span class="sxs-lookup"><span data-stu-id="a4740-111">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="a4740-112">Existují tři skupiny zabezpečení:</span><span class="sxs-lookup"><span data-stu-id="a4740-112">There are three security groups:</span></span>

* <span data-ttu-id="a4740-113">**Registrovaní uživatelé** můžou zobrazit všechna schválená data a můžou upravovat nebo odstraňovat vlastní data.</span><span class="sxs-lookup"><span data-stu-id="a4740-113">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="a4740-114">**Manažeři** mohou schvalovat nebo odmítat kontaktní data.</span><span class="sxs-lookup"><span data-stu-id="a4740-114">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="a4740-115">Uživatelé vidí pouze schválené kontakty.</span><span class="sxs-lookup"><span data-stu-id="a4740-115">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="a4740-116">**Správci** mohou schvalovat nebo odmítat a upravovat nebo odstraňovat jakákoli data.</span><span class="sxs-lookup"><span data-stu-id="a4740-116">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="a4740-117">Obrázky v tomto dokumentu se přesně neshodují s nejnovějšími šablonami.</span><span class="sxs-lookup"><span data-stu-id="a4740-117">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="a4740-118">Na následujícím obrázku je uživatel Rick (`rick@example.com`) přihlášen.</span><span class="sxs-lookup"><span data-stu-id="a4740-118">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="a4740-119">Rick může zobrazit pouze schválené kontakty a **upravovat**/**Odstranit**/**vytvořit nové** odkazy na své kontakty.</span><span class="sxs-lookup"><span data-stu-id="a4740-119">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="a4740-120">Jenom poslední záznam vytvořený pomocí Rick zobrazí odkazy pro **Úpravy** a **odstranění** .</span><span class="sxs-lookup"><span data-stu-id="a4740-120">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="a4740-121">Ostatní uživatelé neuvidí poslední záznam, dokud správce nebo správce změní stav na "Schváleno".</span><span class="sxs-lookup"><span data-stu-id="a4740-121">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Snímek obrazovky zobrazující Rick přihlášení](secure-data/_static/rick.png)

<span data-ttu-id="a4740-123">Na následujícím obrázku je `manager@contoso.com` přihlásit se a v roli manažera:</span><span class="sxs-lookup"><span data-stu-id="a4740-123">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Snímek obrazovky zobrazující přihlášený manager@contoso.com](secure-data/_static/manager1.png)

<span data-ttu-id="a4740-125">Následující obrázek ukazuje vedoucí zobrazení podrobností o kontaktu:</span><span class="sxs-lookup"><span data-stu-id="a4740-125">The following image shows the managers details view of a contact:</span></span>

![Zobrazení manažera kontaktu](secure-data/_static/manager.png)

<span data-ttu-id="a4740-127">Tlačítka **schválení** a **odmítnutí** se zobrazují pouze pro manažery a správce.</span><span class="sxs-lookup"><span data-stu-id="a4740-127">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="a4740-128">Na následujícím obrázku je `admin@contoso.com` přihlásit se a v roli správce:</span><span class="sxs-lookup"><span data-stu-id="a4740-128">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Snímek obrazovky zobrazující přihlášený admin@contoso.com](secure-data/_static/admin.png)

<span data-ttu-id="a4740-130">Správce má všechna oprávnění.</span><span class="sxs-lookup"><span data-stu-id="a4740-130">The administrator has all privileges.</span></span> <span data-ttu-id="a4740-131">Může číst/upravovat/odstraňovat všechny kontakty a změnit stav kontakty.</span><span class="sxs-lookup"><span data-stu-id="a4740-131">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="a4740-132">Aplikace byla vytvořena pomocí [generování uživatelského rozhraní](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) následujícího modelu `Contact`:</span><span class="sxs-lookup"><span data-stu-id="a4740-132">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="a4740-133">Ukázka obsahuje následující rutiny autorizace:</span><span class="sxs-lookup"><span data-stu-id="a4740-133">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="a4740-134">`ContactIsOwnerAuthorizationHandler`: zajišťuje, aby uživatel mohl upravovat pouze svá data.</span><span class="sxs-lookup"><span data-stu-id="a4740-134">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="a4740-135">`ContactManagerAuthorizationHandler`: umožňuje správcům schvalovat nebo odmítat kontakty.</span><span class="sxs-lookup"><span data-stu-id="a4740-135">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="a4740-136">`ContactAdministratorsAuthorizationHandler`: umožňuje správcům schvalovat nebo odmítat kontakty a upravovat nebo odstraňovat kontakty.</span><span class="sxs-lookup"><span data-stu-id="a4740-136">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a4740-137">Požadavky</span><span class="sxs-lookup"><span data-stu-id="a4740-137">Prerequisites</span></span>

<span data-ttu-id="a4740-138">V tomto kurzu je advanced.</span><span class="sxs-lookup"><span data-stu-id="a4740-138">This tutorial is advanced.</span></span> <span data-ttu-id="a4740-139">Měli byste se seznámit s:</span><span class="sxs-lookup"><span data-stu-id="a4740-139">You should be familiar with:</span></span>

* [<span data-ttu-id="a4740-140">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a4740-140">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="a4740-141">Ověřování</span><span class="sxs-lookup"><span data-stu-id="a4740-141">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="a4740-142">Potvrzení účtu a obnovení hesla</span><span class="sxs-lookup"><span data-stu-id="a4740-142">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="a4740-143">Autorizace</span><span class="sxs-lookup"><span data-stu-id="a4740-143">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="a4740-144">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="a4740-144">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="a4740-145">Starter a dokončené aplikace</span><span class="sxs-lookup"><span data-stu-id="a4740-145">The starter and completed app</span></span>

<span data-ttu-id="a4740-146">[Stáhněte](xref:index#how-to-download-a-sample) [dokončenou](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a4740-146">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="a4740-147">[Otestujte](#test-the-completed-app) dokončenou aplikaci, abyste se seznámili se svými funkcemi zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="a4740-147">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="a4740-148">Úvodní aplikaci</span><span class="sxs-lookup"><span data-stu-id="a4740-148">The starter app</span></span>

<span data-ttu-id="a4740-149">[Stáhněte](xref:index#how-to-download-a-sample) si [úvodní](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a4740-149">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="a4740-150">Spusťte aplikaci, klepněte na odkaz **ContactManager** a ověřte, že můžete vytvořit, upravit a odstranit kontakt.</span><span class="sxs-lookup"><span data-stu-id="a4740-150">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="a4740-151">Zabezpečení dat uživatele</span><span class="sxs-lookup"><span data-stu-id="a4740-151">Secure user data</span></span>

<span data-ttu-id="a4740-152">Následující oddíly mají všechny hlavní kroky k vytvoření aplikace pro data zabezpečení uživatele.</span><span class="sxs-lookup"><span data-stu-id="a4740-152">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="a4740-153">Vám může být užitečné k odkazování na dokončení projektu.</span><span class="sxs-lookup"><span data-stu-id="a4740-153">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="a4740-154">Tie kontaktní údaje pro uživatele</span><span class="sxs-lookup"><span data-stu-id="a4740-154">Tie the contact data to the user</span></span>

<span data-ttu-id="a4740-155">Pomocí ID uživatele [Identity](xref:security/authentication/identity) ASP.NET zajistěte, aby mohli uživatelé upravovat data, ale ne jiná data uživatelů.</span><span class="sxs-lookup"><span data-stu-id="a4740-155">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="a4740-156">Do modelu `Contact` přidejte `OwnerID` a `ContactStatus`:</span><span class="sxs-lookup"><span data-stu-id="a4740-156">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="a4740-157">`OwnerID` je ID uživatele z tabulky `AspNetUser` v databázi [identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="a4740-157">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="a4740-158">Pole `Status` určuje, zda je kontakt viditelný pro obecné uživatele.</span><span class="sxs-lookup"><span data-stu-id="a4740-158">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="a4740-159">Vytvořte novou migraci a aktualizaci databáze:</span><span class="sxs-lookup"><span data-stu-id="a4740-159">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="a4740-160">Přidání služby Role na identitu</span><span class="sxs-lookup"><span data-stu-id="a4740-160">Add Role services to Identity</span></span>

<span data-ttu-id="a4740-161">Připojit [Přidat role](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) pro přidání služeb role:</span><span class="sxs-lookup"><span data-stu-id="a4740-161">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a><span data-ttu-id="a4740-162">Vyžadovat ověření uživatelé</span><span class="sxs-lookup"><span data-stu-id="a4740-162">Require authenticated users</span></span>

<span data-ttu-id="a4740-163">Nastavte výchozí zásady ověřování tak, aby vyžadovala ověření uživatelů:</span><span class="sxs-lookup"><span data-stu-id="a4740-163">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 <span data-ttu-id="a4740-164">Ověřování na úrovni této stránky, řadiče nebo metody akce lze odhlásit pomocí atributu `[AllowAnonymous]`.</span><span class="sxs-lookup"><span data-stu-id="a4740-164">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="a4740-165">Nastavení výchozí zásady ověřování tak, aby vyžadovala ověření uživatelů chrání nově přidané Razor Pages a kontrolery.</span><span class="sxs-lookup"><span data-stu-id="a4740-165">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="a4740-166">Pokud je ve výchozím nastavení vyžadováno ověřování, je bezpečnější než spoléhání na nové řadiče a Razor Pages pro zahrnutí atributu `[Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="a4740-166">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="a4740-167">Přidejte [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) na stránky rejstřík a soukromí, aby anonymní uživatelé mohli získat informace o lokalitě před jejich registrací.</span><span class="sxs-lookup"><span data-stu-id="a4740-167">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index and Privacy pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="a4740-168">Konfigurace testovací účet</span><span class="sxs-lookup"><span data-stu-id="a4740-168">Configure the test account</span></span>

<span data-ttu-id="a4740-169">Třída `SeedData` vytvoří dva účty: správce a manažer.</span><span class="sxs-lookup"><span data-stu-id="a4740-169">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="a4740-170">Pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets) nastavte heslo pro tyto účty.</span><span class="sxs-lookup"><span data-stu-id="a4740-170">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="a4740-171">Nastavte heslo z adresáře projektu (adresář obsahující *program.cs*):</span><span class="sxs-lookup"><span data-stu-id="a4740-171">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="a4740-172">Pokud není zadáno silné heslo, je vyvolána výjimka při volání `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="a4740-172">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="a4740-173">Aktualizujte `Main` pro použití testovacího hesla:</span><span class="sxs-lookup"><span data-stu-id="a4740-173">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="a4740-174">Vytvoření testovacích účtů a aktualizovat kontakty</span><span class="sxs-lookup"><span data-stu-id="a4740-174">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="a4740-175">Aktualizujte metodu `Initialize` ve třídě `SeedData` k vytvoření testovacích účtů:</span><span class="sxs-lookup"><span data-stu-id="a4740-175">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="a4740-176">Přidejte do kontaktů ID uživatele a `ContactStatus` správce.</span><span class="sxs-lookup"><span data-stu-id="a4740-176">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="a4740-177">Proveďte jednu z kontakty "Submitted" a jedné "byl odmítnut".</span><span class="sxs-lookup"><span data-stu-id="a4740-177">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="a4740-178">Přidáte ID uživatele a stav pro všechny kontakty.</span><span class="sxs-lookup"><span data-stu-id="a4740-178">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="a4740-179">Je zobrazena pouze jeden kontakt:</span><span class="sxs-lookup"><span data-stu-id="a4740-179">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="a4740-180">Vytvořit vlastníka, správce a Správce autorizace obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="a4740-180">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="a4740-181">Ve složce pro *autorizaci* vytvořte třídu `ContactIsOwnerAuthorizationHandler`.</span><span class="sxs-lookup"><span data-stu-id="a4740-181">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="a4740-182">`ContactIsOwnerAuthorizationHandler` ověří, že uživatel pracuje na prostředku, který je vlastníkem prostředku.</span><span class="sxs-lookup"><span data-stu-id="a4740-182">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="a4740-183">`ContactIsOwnerAuthorizationHandler` volá [kontext. Úspěšné](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , pokud je aktuální ověřený uživatel vlastníkem kontaktu.</span><span class="sxs-lookup"><span data-stu-id="a4740-183">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="a4740-184">Obslužné rutiny autorizace obecně:</span><span class="sxs-lookup"><span data-stu-id="a4740-184">Authorization handlers generally:</span></span>

* <span data-ttu-id="a4740-185">Vrátí `context.Succeed`, pokud jsou splněny požadavky.</span><span class="sxs-lookup"><span data-stu-id="a4740-185">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="a4740-186">Vrátí `Task.CompletedTask`, pokud nejsou splněny požadavky.</span><span class="sxs-lookup"><span data-stu-id="a4740-186">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="a4740-187">`Task.CompletedTask` není úspěch nebo neúspěch&mdash;umožňuje spuštění dalších obslužných rutin autorizace.</span><span class="sxs-lookup"><span data-stu-id="a4740-187">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="a4740-188">Pokud potřebujete explicitně selhat, vraťte [kontext. Selhání](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="a4740-188">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="a4740-189">Aplikaci umožňuje kontaktní vlastníkům upravit, odstranit a vytvořit svoje vlastní data.</span><span class="sxs-lookup"><span data-stu-id="a4740-189">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="a4740-190">`ContactIsOwnerAuthorizationHandler` nemusí kontrolovat operaci předanou parametrem požadavku.</span><span class="sxs-lookup"><span data-stu-id="a4740-190">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="a4740-191">Vytvořte obslužnou rutinu Správce autorizací</span><span class="sxs-lookup"><span data-stu-id="a4740-191">Create a manager authorization handler</span></span>

<span data-ttu-id="a4740-192">Ve složce pro *autorizaci* vytvořte třídu `ContactManagerAuthorizationHandler`.</span><span class="sxs-lookup"><span data-stu-id="a4740-192">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="a4740-193">`ContactManagerAuthorizationHandler` ověří uživatele, který pracuje na prostředku, jako správce.</span><span class="sxs-lookup"><span data-stu-id="a4740-193">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="a4740-194">Pouze vedoucí mohli schválit nebo odmítnout změny obsahu (nové nebo změněné).</span><span class="sxs-lookup"><span data-stu-id="a4740-194">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="a4740-195">Vytvořte obslužnou rutinu Správce autorizací</span><span class="sxs-lookup"><span data-stu-id="a4740-195">Create an administrator authorization handler</span></span>

<span data-ttu-id="a4740-196">Ve složce pro *autorizaci* vytvořte třídu `ContactAdministratorsAuthorizationHandler`.</span><span class="sxs-lookup"><span data-stu-id="a4740-196">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="a4740-197">`ContactAdministratorsAuthorizationHandler` ověří uživatele, který pracuje na prostředku, jako správce.</span><span class="sxs-lookup"><span data-stu-id="a4740-197">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="a4740-198">Správce může provádět všechny operace.</span><span class="sxs-lookup"><span data-stu-id="a4740-198">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="a4740-199">Zaregistrujte obslužné rutiny autorizace</span><span class="sxs-lookup"><span data-stu-id="a4740-199">Register the authorization handlers</span></span>

<span data-ttu-id="a4740-200">Služby používající Entity Framework Core musí být registrovány pro [vkládání závislostí](xref:fundamentals/dependency-injection) pomocí [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="a4740-200">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="a4740-201">`ContactIsOwnerAuthorizationHandler` používá ASP.NET Core [identity](xref:security/authentication/identity), která je postavená na Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="a4740-201">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="a4740-202">Zaregistrujte obslužné rutiny s kolekcí služeb, aby byly k dispozici pro `ContactsController` prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a4740-202">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="a4740-203">Do konce `ConfigureServices`přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="a4740-203">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="a4740-204">`ContactAdministratorsAuthorizationHandler` a `ContactManagerAuthorizationHandler` jsou přidány jako singleton.</span><span class="sxs-lookup"><span data-stu-id="a4740-204">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="a4740-205">Jsou to jednoznačné, protože nepoužívají EF a všechny potřebné informace jsou v parametru `Context` `HandleRequirementAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="a4740-205">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="a4740-206">Povolení podpory</span><span class="sxs-lookup"><span data-stu-id="a4740-206">Support authorization</span></span>

<span data-ttu-id="a4740-207">V této části se aktualizace stránky Razor a přidejte třídu požadavků operace.</span><span class="sxs-lookup"><span data-stu-id="a4740-207">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="a4740-208">Zkontrolujte požadavky na třídy kontaktní operace</span><span class="sxs-lookup"><span data-stu-id="a4740-208">Review the contact operations requirements class</span></span>

<span data-ttu-id="a4740-209">Zkontrolujte třídu `ContactOperations`.</span><span class="sxs-lookup"><span data-stu-id="a4740-209">Review the `ContactOperations` class.</span></span> <span data-ttu-id="a4740-210">Tato třída obsahuje požadavky na aplikace podporuje:</span><span class="sxs-lookup"><span data-stu-id="a4740-210">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="a4740-211">Vytvoření základní třídu pro stránky Razor kontakty</span><span class="sxs-lookup"><span data-stu-id="a4740-211">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="a4740-212">Vytvořte základní třídu, která obsahuje služby využité v kontaktech stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="a4740-212">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="a4740-213">Základní třída vloží kód inicializace na jednom místě:</span><span class="sxs-lookup"><span data-stu-id="a4740-213">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="a4740-214">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="a4740-214">The preceding code:</span></span>

* <span data-ttu-id="a4740-215">Přidá službu `IAuthorizationService` pro přístup k obslužným rutinám autorizace.</span><span class="sxs-lookup"><span data-stu-id="a4740-215">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="a4740-216">Přidá službu identity `UserManager`.</span><span class="sxs-lookup"><span data-stu-id="a4740-216">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="a4740-217">Přidejte `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="a4740-217">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="a4740-218">Aktualizace CreateModel</span><span class="sxs-lookup"><span data-stu-id="a4740-218">Update the CreateModel</span></span>

<span data-ttu-id="a4740-219">Aktualizujte konstruktor Create Page model tak, aby používal základní třídu `DI_BasePageModel`:</span><span class="sxs-lookup"><span data-stu-id="a4740-219">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="a4740-220">Aktualizujte metodu `CreateModel.OnPostAsync` na:</span><span class="sxs-lookup"><span data-stu-id="a4740-220">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="a4740-221">Přidejte ID uživatele do modelu `Contact`.</span><span class="sxs-lookup"><span data-stu-id="a4740-221">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="a4740-222">Ověřte, zda že má uživatel oprávnění k vytvoření kontakty obslužná rutina ověřování volejte.</span><span class="sxs-lookup"><span data-stu-id="a4740-222">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="a4740-223">Aktualizace IndexModel</span><span class="sxs-lookup"><span data-stu-id="a4740-223">Update the IndexModel</span></span>

<span data-ttu-id="a4740-224">Aktualizujte metodu `OnGetAsync`, aby se pro obecné uživatele zobrazovaly jenom schválené kontakty:</span><span class="sxs-lookup"><span data-stu-id="a4740-224">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="a4740-225">Aktualizace EditModel</span><span class="sxs-lookup"><span data-stu-id="a4740-225">Update the EditModel</span></span>

<span data-ttu-id="a4740-226">Přidejte obslužnou rutinu ověřování k ověření, že uživatel je vlastníkem kontaktu.</span><span class="sxs-lookup"><span data-stu-id="a4740-226">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="a4740-227">Vzhledem k tomu, že se ověřuje autorizace prostředků, atribut `[Authorize]` není dostatečný.</span><span class="sxs-lookup"><span data-stu-id="a4740-227">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="a4740-228">Aplikace nemá přístup k prostředku, když jsou vyhodnocovány atributy.</span><span class="sxs-lookup"><span data-stu-id="a4740-228">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="a4740-229">Autorizace na základě prostředků musí být nezbytné.</span><span class="sxs-lookup"><span data-stu-id="a4740-229">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="a4740-230">Kontroly musí provést, když má aplikace přístup k prostředku načtením v modelu stránky nebo načtením v rámci samotná obslužná rutina.</span><span class="sxs-lookup"><span data-stu-id="a4740-230">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="a4740-231">Často přistupovat k prostředku předáním klíč prostředku.</span><span class="sxs-lookup"><span data-stu-id="a4740-231">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="a4740-232">Aktualizace DeleteModel</span><span class="sxs-lookup"><span data-stu-id="a4740-232">Update the DeleteModel</span></span>

<span data-ttu-id="a4740-233">Aktualizace modelu stránku odstranit sloužící k ověření, že uživatel má oprávnění odstranit u kontaktu obslužnou rutinu ověřování.</span><span class="sxs-lookup"><span data-stu-id="a4740-233">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="a4740-234">Vložit do zobrazení autorizační službu</span><span class="sxs-lookup"><span data-stu-id="a4740-234">Inject the authorization service into the views</span></span>

<span data-ttu-id="a4740-235">V současné době ukazuje uživatelského rozhraní upravovat a odstraňovat kontakty, které uživatel nemůže upravovat odkazy.</span><span class="sxs-lookup"><span data-stu-id="a4740-235">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="a4740-236">Vložení autorizační služby do souboru *Pages/_ViewImports. cshtml* , aby bylo dostupné pro všechna zobrazení:</span><span class="sxs-lookup"><span data-stu-id="a4740-236">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="a4740-237">Předchozí kód přidá několik příkazů `using`.</span><span class="sxs-lookup"><span data-stu-id="a4740-237">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="a4740-238">Aktualizujte odkazy pro **Úpravy** a **odstranění** na *stránkách/kontakty/index. cshtml* , aby byly vygenerovány pouze pro uživatele s příslušnými oprávněními:</span><span class="sxs-lookup"><span data-stu-id="a4740-238">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="a4740-239">Skrytí propojení od uživatele, kteří nemají oprávnění ke změně dat nebude zabezpečení aplikace.</span><span class="sxs-lookup"><span data-stu-id="a4740-239">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="a4740-240">Skrytí propojení nastaví aplikaci jako přívětivější tím, že zobrazuje pouze platné odkazy.</span><span class="sxs-lookup"><span data-stu-id="a4740-240">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="a4740-241">Uživatelé můžou hack generované adres URL pro vyvolání funkce upravit a odstranit operací s daty, které není vlastní.</span><span class="sxs-lookup"><span data-stu-id="a4740-241">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="a4740-242">Stránka Razor nebo kontroleru musí vynucovat kontroly přístupu pro data zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="a4740-242">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="a4740-243">Podrobné informace o aktualizaci</span><span class="sxs-lookup"><span data-stu-id="a4740-243">Update Details</span></span>

<span data-ttu-id="a4740-244">Aktualizujte zobrazení podrobností, aby vedoucí mohli schválit nebo odmítnout kontaktů:</span><span class="sxs-lookup"><span data-stu-id="a4740-244">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="a4740-245">Model stránky podrobnosti aktualizace:</span><span class="sxs-lookup"><span data-stu-id="a4740-245">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="a4740-246">Přidat nebo odebrat uživatele k roli</span><span class="sxs-lookup"><span data-stu-id="a4740-246">Add or remove a user to a role</span></span>

<span data-ttu-id="a4740-247">Informace o [tomto problému najdete zde](https://github.com/aspnet/AspNetCore.Docs/issues/8502) :</span><span class="sxs-lookup"><span data-stu-id="a4740-247">See [this issue](https://github.com/aspnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="a4740-248">Odebrání oprávnění uživatele.</span><span class="sxs-lookup"><span data-stu-id="a4740-248">Removing privileges from a user.</span></span> <span data-ttu-id="a4740-249">Například ztlumení uživatele v aplikaci chatu.</span><span class="sxs-lookup"><span data-stu-id="a4740-249">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="a4740-250">Přidání oprávnění pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="a4740-250">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="a4740-251">Rozdíly mezi výzvou a zakazující</span><span class="sxs-lookup"><span data-stu-id="a4740-251">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="a4740-252">Tato aplikace nastaví výchozí zásadu, která bude [vyžadovat ověřené uživatele](#require-authenticated-users).</span><span class="sxs-lookup"><span data-stu-id="a4740-252">This app sets the default policy to [require authenticated users](#require-authenticated-users).</span></span> <span data-ttu-id="a4740-253">Následující kód povoluje anonymní uživatele.</span><span class="sxs-lookup"><span data-stu-id="a4740-253">The following code allows anonymous users.</span></span> <span data-ttu-id="a4740-254">Anonymní uživatelé mají povoleno zobrazovat rozdíly mezi výzvou a zakázáním.</span><span class="sxs-lookup"><span data-stu-id="a4740-254">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="a4740-255">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="a4740-255">In the preceding code:</span></span>

* <span data-ttu-id="a4740-256">Pokud uživatel **není ověřen,** je vrácena `ChallengeResult`.</span><span class="sxs-lookup"><span data-stu-id="a4740-256">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="a4740-257">Po vrácení `ChallengeResult` se uživatel přesměruje na přihlašovací stránku.</span><span class="sxs-lookup"><span data-stu-id="a4740-257">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="a4740-258">Když je uživatel ověřený, ale není autorizovaný, vrátí se `ForbidResult`.</span><span class="sxs-lookup"><span data-stu-id="a4740-258">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="a4740-259">Po vrácení `ForbidResult` se uživatel přesměruje na stránku přístup byl odepřen.</span><span class="sxs-lookup"><span data-stu-id="a4740-259">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="a4740-260">Testování dokončené aplikace</span><span class="sxs-lookup"><span data-stu-id="a4740-260">Test the completed app</span></span>

<span data-ttu-id="a4740-261">Pokud jste ještě nenastavili heslo pro osazené uživatelské účty, nastavte heslo pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets#secret-manager) :</span><span class="sxs-lookup"><span data-stu-id="a4740-261">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="a4740-262">Zvolte silné heslo: použití osm nebo více znaků, aspoň jedno velké písmeno, číslo a symbol.</span><span class="sxs-lookup"><span data-stu-id="a4740-262">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="a4740-263">`Passw0rd!` například splňuje požadavky na silné heslo.</span><span class="sxs-lookup"><span data-stu-id="a4740-263">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="a4740-264">Spusťte následující příkaz ze složky projektu, kde `<PW>` je heslo:</span><span class="sxs-lookup"><span data-stu-id="a4740-264">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="a4740-265">Pokud má kontaktů:</span><span class="sxs-lookup"><span data-stu-id="a4740-265">If the app has contacts:</span></span>

* <span data-ttu-id="a4740-266">Odstraní všechny záznamy v `Contact` tabulce.</span><span class="sxs-lookup"><span data-stu-id="a4740-266">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="a4740-267">Restartujte aplikaci k přidání dat do databáze.</span><span class="sxs-lookup"><span data-stu-id="a4740-267">Restart the app to seed the database.</span></span>

<span data-ttu-id="a4740-268">Snadný způsob, jak otestovat dokončená aplikace je spustíte tři různé prohlížeče (nebo incognito/InPrivate relace).</span><span class="sxs-lookup"><span data-stu-id="a4740-268">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="a4740-269">V jednom prohlížeči Zaregistrujte nového uživatele (například `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="a4740-269">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="a4740-270">Přihlaste se k každým prohlížečem s jiným uživatelem.</span><span class="sxs-lookup"><span data-stu-id="a4740-270">Sign in to each browser with a different user.</span></span> <span data-ttu-id="a4740-271">Ověřte následující operace:</span><span class="sxs-lookup"><span data-stu-id="a4740-271">Verify the following operations:</span></span>

* <span data-ttu-id="a4740-272">Registrovaných uživatelů můžete zobrazit všechny schválené kontaktní údaje.</span><span class="sxs-lookup"><span data-stu-id="a4740-272">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="a4740-273">Registrovaných uživatelů můžete upravit nebo odstranit svá vlastní data.</span><span class="sxs-lookup"><span data-stu-id="a4740-273">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="a4740-274">Správci mohou schvalovat a odmítat kontaktní údaje.</span><span class="sxs-lookup"><span data-stu-id="a4740-274">Managers can approve/reject contact data.</span></span> <span data-ttu-id="a4740-275">Zobrazení `Details` zobrazuje tlačítka **schválení** a **odmítnutí** .</span><span class="sxs-lookup"><span data-stu-id="a4740-275">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="a4740-276">Správci můžou schvalovat a odmítat a upravit nebo odstranit všechna data.</span><span class="sxs-lookup"><span data-stu-id="a4740-276">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="a4740-277">Uživatel</span><span class="sxs-lookup"><span data-stu-id="a4740-277">User</span></span>                | <span data-ttu-id="a4740-278">Nasazených aplikací</span><span class="sxs-lookup"><span data-stu-id="a4740-278">Seeded by the app</span></span> | <span data-ttu-id="a4740-279">Možnosti</span><span class="sxs-lookup"><span data-stu-id="a4740-279">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="a4740-280">Ne</span><span class="sxs-lookup"><span data-stu-id="a4740-280">No</span></span>                | <span data-ttu-id="a4740-281">Upravit nebo odstranit vlastní data.</span><span class="sxs-lookup"><span data-stu-id="a4740-281">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="a4740-282">Ano</span><span class="sxs-lookup"><span data-stu-id="a4740-282">Yes</span></span>               | <span data-ttu-id="a4740-283">Vlastní data, schvalovat a odmítat a upravit nebo odstranit.</span><span class="sxs-lookup"><span data-stu-id="a4740-283">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="a4740-284">Ano</span><span class="sxs-lookup"><span data-stu-id="a4740-284">Yes</span></span>               | <span data-ttu-id="a4740-285">Schválit nebo odmítnout a upravit nebo odstranit všechna data.</span><span class="sxs-lookup"><span data-stu-id="a4740-285">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="a4740-286">Vytvoření kontaktu v prohlížeči na správce.</span><span class="sxs-lookup"><span data-stu-id="a4740-286">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="a4740-287">Zkopírujte adresu URL pro odstranění a upravit z kontaktujte správce.</span><span class="sxs-lookup"><span data-stu-id="a4740-287">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="a4740-288">Vložte tyto odkazy do testů webového prohlížeče k ověření, že testovací uživatel nemůže provádět tyto operace.</span><span class="sxs-lookup"><span data-stu-id="a4740-288">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="a4740-289">Vytvořit úvodní aplikaci</span><span class="sxs-lookup"><span data-stu-id="a4740-289">Create the starter app</span></span>

* <span data-ttu-id="a4740-290">Vytvoření aplikace stránky Razor s názvem "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="a4740-290">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="a4740-291">Vytvořte aplikaci pomocí **individuálních uživatelských účtů**.</span><span class="sxs-lookup"><span data-stu-id="a4740-291">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="a4740-292">Pojmenujte ji "ContactManager" tak obor názvů odpovídá oboru názvů použitého v ukázce.</span><span class="sxs-lookup"><span data-stu-id="a4740-292">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="a4740-293">`-uld` určuje LocalDB místo SQLite.</span><span class="sxs-lookup"><span data-stu-id="a4740-293">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="a4740-294">Přidat *modely/kontakt. cs*:</span><span class="sxs-lookup"><span data-stu-id="a4740-294">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="a4740-295">Generování `Contact` modelu.</span><span class="sxs-lookup"><span data-stu-id="a4740-295">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="a4740-296">Vytvořte počáteční migraci a aktualizaci databáze:</span><span class="sxs-lookup"><span data-stu-id="a4740-296">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="a4740-297">Pokud dojde k chybě s příkazem `dotnet aspnet-codegenerator razorpage`, přečtěte si [Tento problém GitHub](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="a4740-297">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="a4740-298">Aktualizujte kotvu **ContactManager** v souboru *Pages/shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="a4740-298">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="a4740-299">Testování aplikace pomocí vytváření, úpravy a odstranění kontaktu</span><span class="sxs-lookup"><span data-stu-id="a4740-299">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="a4740-300">Přidání dat do databáze</span><span class="sxs-lookup"><span data-stu-id="a4740-300">Seed the database</span></span>

<span data-ttu-id="a4740-301">Do složky *data* přidejte třídu [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) :</span><span class="sxs-lookup"><span data-stu-id="a4740-301">Add the [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="a4740-302">Volat `SeedData.Initialize` z `Main`:</span><span class="sxs-lookup"><span data-stu-id="a4740-302">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="a4740-303">Otestujte, že aplikace naplnila databázi.</span><span class="sxs-lookup"><span data-stu-id="a4740-303">Test that the app seeded the database.</span></span> <span data-ttu-id="a4740-304">Pokud existují nějaké řádky v kontaktu DB, metoda počáteční hodnoty není spuštěna.</span><span class="sxs-lookup"><span data-stu-id="a4740-304">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="a4740-305">Tento kurz ukazuje, jak vytvořit webovou aplikaci ASP.NET Core s uživatelskými daty chráněnými autorizací.</span><span class="sxs-lookup"><span data-stu-id="a4740-305">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="a4740-306">Zobrazí seznam kontakty, které ověřeným uživatelům (registrovaných) jste vytvořili.</span><span class="sxs-lookup"><span data-stu-id="a4740-306">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="a4740-307">Existují tři skupiny zabezpečení:</span><span class="sxs-lookup"><span data-stu-id="a4740-307">There are three security groups:</span></span>

* <span data-ttu-id="a4740-308">**Registrovaní uživatelé** můžou zobrazit všechna schválená data a můžou upravovat nebo odstraňovat vlastní data.</span><span class="sxs-lookup"><span data-stu-id="a4740-308">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="a4740-309">**Manažeři** mohou schvalovat nebo odmítat kontaktní data.</span><span class="sxs-lookup"><span data-stu-id="a4740-309">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="a4740-310">Uživatelé vidí pouze schválené kontakty.</span><span class="sxs-lookup"><span data-stu-id="a4740-310">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="a4740-311">**Správci** mohou schvalovat nebo odmítat a upravovat nebo odstraňovat jakákoli data.</span><span class="sxs-lookup"><span data-stu-id="a4740-311">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="a4740-312">Na následujícím obrázku je uživatel Rick (`rick@example.com`) přihlášen.</span><span class="sxs-lookup"><span data-stu-id="a4740-312">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="a4740-313">Rick může zobrazit pouze schválené kontakty a **upravovat**/**Odstranit**/**vytvořit nové** odkazy na své kontakty.</span><span class="sxs-lookup"><span data-stu-id="a4740-313">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="a4740-314">Jenom poslední záznam vytvořený pomocí Rick zobrazí odkazy pro **Úpravy** a **odstranění** .</span><span class="sxs-lookup"><span data-stu-id="a4740-314">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="a4740-315">Ostatní uživatelé neuvidí poslední záznam, dokud správce nebo správce změní stav na "Schváleno".</span><span class="sxs-lookup"><span data-stu-id="a4740-315">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Snímek obrazovky zobrazující Rick přihlášení](secure-data/_static/rick.png)

<span data-ttu-id="a4740-317">Na následujícím obrázku je `manager@contoso.com` přihlásit se a v roli manažera:</span><span class="sxs-lookup"><span data-stu-id="a4740-317">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Snímek obrazovky zobrazující přihlášený manager@contoso.com](secure-data/_static/manager1.png)

<span data-ttu-id="a4740-319">Následující obrázek ukazuje vedoucí zobrazení podrobností o kontaktu:</span><span class="sxs-lookup"><span data-stu-id="a4740-319">The following image shows the managers details view of a contact:</span></span>

![Zobrazení manažera kontaktu](secure-data/_static/manager.png)

<span data-ttu-id="a4740-321">Tlačítka **schválení** a **odmítnutí** se zobrazují pouze pro manažery a správce.</span><span class="sxs-lookup"><span data-stu-id="a4740-321">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="a4740-322">Na následujícím obrázku je `admin@contoso.com` přihlásit se a v roli správce:</span><span class="sxs-lookup"><span data-stu-id="a4740-322">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Snímek obrazovky zobrazující přihlášený admin@contoso.com](secure-data/_static/admin.png)

<span data-ttu-id="a4740-324">Správce má všechna oprávnění.</span><span class="sxs-lookup"><span data-stu-id="a4740-324">The administrator has all privileges.</span></span> <span data-ttu-id="a4740-325">Může číst/upravovat/odstraňovat všechny kontakty a změnit stav kontakty.</span><span class="sxs-lookup"><span data-stu-id="a4740-325">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="a4740-326">Aplikace byla vytvořena pomocí [generování uživatelského rozhraní](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) následujícího modelu `Contact`:</span><span class="sxs-lookup"><span data-stu-id="a4740-326">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="a4740-327">Ukázka obsahuje následující rutiny autorizace:</span><span class="sxs-lookup"><span data-stu-id="a4740-327">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="a4740-328">`ContactIsOwnerAuthorizationHandler`: zajišťuje, aby uživatel mohl upravovat pouze svá data.</span><span class="sxs-lookup"><span data-stu-id="a4740-328">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="a4740-329">`ContactManagerAuthorizationHandler`: umožňuje správcům schvalovat nebo odmítat kontakty.</span><span class="sxs-lookup"><span data-stu-id="a4740-329">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="a4740-330">`ContactAdministratorsAuthorizationHandler`: umožňuje správcům schvalovat nebo odmítat kontakty a upravovat nebo odstraňovat kontakty.</span><span class="sxs-lookup"><span data-stu-id="a4740-330">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a4740-331">Požadavky</span><span class="sxs-lookup"><span data-stu-id="a4740-331">Prerequisites</span></span>

<span data-ttu-id="a4740-332">V tomto kurzu je advanced.</span><span class="sxs-lookup"><span data-stu-id="a4740-332">This tutorial is advanced.</span></span> <span data-ttu-id="a4740-333">Měli byste se seznámit s:</span><span class="sxs-lookup"><span data-stu-id="a4740-333">You should be familiar with:</span></span>

* [<span data-ttu-id="a4740-334">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a4740-334">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="a4740-335">Ověřování</span><span class="sxs-lookup"><span data-stu-id="a4740-335">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="a4740-336">Potvrzení účtu a obnovení hesla</span><span class="sxs-lookup"><span data-stu-id="a4740-336">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="a4740-337">Autorizace</span><span class="sxs-lookup"><span data-stu-id="a4740-337">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="a4740-338">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="a4740-338">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="a4740-339">Starter a dokončené aplikace</span><span class="sxs-lookup"><span data-stu-id="a4740-339">The starter and completed app</span></span>

<span data-ttu-id="a4740-340">[Stáhněte](xref:index#how-to-download-a-sample) [dokončenou](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a4740-340">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="a4740-341">[Otestujte](#test-the-completed-app) dokončenou aplikaci, abyste se seznámili se svými funkcemi zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="a4740-341">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="a4740-342">Úvodní aplikaci</span><span class="sxs-lookup"><span data-stu-id="a4740-342">The starter app</span></span>

<span data-ttu-id="a4740-343">[Stáhněte](xref:index#how-to-download-a-sample) si [úvodní](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a4740-343">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="a4740-344">Spusťte aplikaci, klepněte na odkaz **ContactManager** a ověřte, že můžete vytvořit, upravit a odstranit kontakt.</span><span class="sxs-lookup"><span data-stu-id="a4740-344">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="a4740-345">Zabezpečení dat uživatele</span><span class="sxs-lookup"><span data-stu-id="a4740-345">Secure user data</span></span>

<span data-ttu-id="a4740-346">Následující oddíly mají všechny hlavní kroky k vytvoření aplikace pro data zabezpečení uživatele.</span><span class="sxs-lookup"><span data-stu-id="a4740-346">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="a4740-347">Vám může být užitečné k odkazování na dokončení projektu.</span><span class="sxs-lookup"><span data-stu-id="a4740-347">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="a4740-348">Tie kontaktní údaje pro uživatele</span><span class="sxs-lookup"><span data-stu-id="a4740-348">Tie the contact data to the user</span></span>

<span data-ttu-id="a4740-349">Pomocí ID uživatele [Identity](xref:security/authentication/identity) ASP.NET zajistěte, aby mohli uživatelé upravovat data, ale ne jiná data uživatelů.</span><span class="sxs-lookup"><span data-stu-id="a4740-349">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="a4740-350">Do modelu `Contact` přidejte `OwnerID` a `ContactStatus`:</span><span class="sxs-lookup"><span data-stu-id="a4740-350">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="a4740-351">`OwnerID` je ID uživatele z tabulky `AspNetUser` v databázi [identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="a4740-351">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="a4740-352">Pole `Status` určuje, zda je kontakt viditelný pro obecné uživatele.</span><span class="sxs-lookup"><span data-stu-id="a4740-352">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="a4740-353">Vytvořte novou migraci a aktualizaci databáze:</span><span class="sxs-lookup"><span data-stu-id="a4740-353">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="a4740-354">Přidání služby Role na identitu</span><span class="sxs-lookup"><span data-stu-id="a4740-354">Add Role services to Identity</span></span>

<span data-ttu-id="a4740-355">Připojit [Přidat role](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) pro přidání služeb role:</span><span class="sxs-lookup"><span data-stu-id="a4740-355">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a><span data-ttu-id="a4740-356">Vyžadovat ověření uživatelé</span><span class="sxs-lookup"><span data-stu-id="a4740-356">Require authenticated users</span></span>

<span data-ttu-id="a4740-357">Nastavte výchozí zásady ověřování tak, aby vyžadovala ověření uživatelů:</span><span class="sxs-lookup"><span data-stu-id="a4740-357">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="a4740-358">Ověřování na úrovni této stránky, řadiče nebo metody akce lze odhlásit pomocí atributu `[AllowAnonymous]`.</span><span class="sxs-lookup"><span data-stu-id="a4740-358">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="a4740-359">Nastavení výchozí zásady ověřování tak, aby vyžadovala ověření uživatelů chrání nově přidané Razor Pages a kontrolery.</span><span class="sxs-lookup"><span data-stu-id="a4740-359">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="a4740-360">Pokud je ve výchozím nastavení vyžadováno ověřování, je bezpečnější než spoléhání na nové řadiče a Razor Pages pro zahrnutí atributu `[Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="a4740-360">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="a4740-361">Přidejte [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) na stránky rejstřík, o a kontakt, aby anonymní uživatelé mohli získat informace o lokalitě před jejich registrací.</span><span class="sxs-lookup"><span data-stu-id="a4740-361">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="a4740-362">Konfigurace testovací účet</span><span class="sxs-lookup"><span data-stu-id="a4740-362">Configure the test account</span></span>

<span data-ttu-id="a4740-363">Třída `SeedData` vytvoří dva účty: správce a manažer.</span><span class="sxs-lookup"><span data-stu-id="a4740-363">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="a4740-364">Pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets) nastavte heslo pro tyto účty.</span><span class="sxs-lookup"><span data-stu-id="a4740-364">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="a4740-365">Nastavte heslo z adresáře projektu (adresář obsahující *program.cs*):</span><span class="sxs-lookup"><span data-stu-id="a4740-365">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="a4740-366">Pokud není zadáno silné heslo, je vyvolána výjimka při volání `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="a4740-366">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="a4740-367">Aktualizujte `Main` pro použití testovacího hesla:</span><span class="sxs-lookup"><span data-stu-id="a4740-367">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="a4740-368">Vytvoření testovacích účtů a aktualizovat kontakty</span><span class="sxs-lookup"><span data-stu-id="a4740-368">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="a4740-369">Aktualizujte metodu `Initialize` ve třídě `SeedData` k vytvoření testovacích účtů:</span><span class="sxs-lookup"><span data-stu-id="a4740-369">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="a4740-370">Přidejte do kontaktů ID uživatele a `ContactStatus` správce.</span><span class="sxs-lookup"><span data-stu-id="a4740-370">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="a4740-371">Proveďte jednu z kontakty "Submitted" a jedné "byl odmítnut".</span><span class="sxs-lookup"><span data-stu-id="a4740-371">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="a4740-372">Přidáte ID uživatele a stav pro všechny kontakty.</span><span class="sxs-lookup"><span data-stu-id="a4740-372">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="a4740-373">Je zobrazena pouze jeden kontakt:</span><span class="sxs-lookup"><span data-stu-id="a4740-373">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="a4740-374">Vytvořit vlastníka, správce a Správce autorizace obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="a4740-374">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="a4740-375">Vytvořte složku *autorizace* a v ní vytvořte třídu `ContactIsOwnerAuthorizationHandler`.</span><span class="sxs-lookup"><span data-stu-id="a4740-375">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="a4740-376">`ContactIsOwnerAuthorizationHandler` ověří, že uživatel pracuje na prostředku, který je vlastníkem prostředku.</span><span class="sxs-lookup"><span data-stu-id="a4740-376">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="a4740-377">`ContactIsOwnerAuthorizationHandler` volá [kontext. Úspěšné](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , pokud je aktuální ověřený uživatel vlastníkem kontaktu.</span><span class="sxs-lookup"><span data-stu-id="a4740-377">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="a4740-378">Obslužné rutiny autorizace obecně:</span><span class="sxs-lookup"><span data-stu-id="a4740-378">Authorization handlers generally:</span></span>

* <span data-ttu-id="a4740-379">Vrátí `context.Succeed`, pokud jsou splněny požadavky.</span><span class="sxs-lookup"><span data-stu-id="a4740-379">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="a4740-380">Vrátí `Task.CompletedTask`, pokud nejsou splněny požadavky.</span><span class="sxs-lookup"><span data-stu-id="a4740-380">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="a4740-381">`Task.CompletedTask` není úspěch nebo neúspěch&mdash;umožňuje spuštění dalších obslužných rutin autorizace.</span><span class="sxs-lookup"><span data-stu-id="a4740-381">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="a4740-382">Pokud potřebujete explicitně selhat, vraťte [kontext. Selhání](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="a4740-382">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="a4740-383">Aplikaci umožňuje kontaktní vlastníkům upravit, odstranit a vytvořit svoje vlastní data.</span><span class="sxs-lookup"><span data-stu-id="a4740-383">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="a4740-384">`ContactIsOwnerAuthorizationHandler` nemusí kontrolovat operaci předanou parametrem požadavku.</span><span class="sxs-lookup"><span data-stu-id="a4740-384">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="a4740-385">Vytvořte obslužnou rutinu Správce autorizací</span><span class="sxs-lookup"><span data-stu-id="a4740-385">Create a manager authorization handler</span></span>

<span data-ttu-id="a4740-386">Ve složce pro *autorizaci* vytvořte třídu `ContactManagerAuthorizationHandler`.</span><span class="sxs-lookup"><span data-stu-id="a4740-386">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="a4740-387">`ContactManagerAuthorizationHandler` ověří uživatele, který pracuje na prostředku, jako správce.</span><span class="sxs-lookup"><span data-stu-id="a4740-387">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="a4740-388">Pouze vedoucí mohli schválit nebo odmítnout změny obsahu (nové nebo změněné).</span><span class="sxs-lookup"><span data-stu-id="a4740-388">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="a4740-389">Vytvořte obslužnou rutinu Správce autorizací</span><span class="sxs-lookup"><span data-stu-id="a4740-389">Create an administrator authorization handler</span></span>

<span data-ttu-id="a4740-390">Ve složce pro *autorizaci* vytvořte třídu `ContactAdministratorsAuthorizationHandler`.</span><span class="sxs-lookup"><span data-stu-id="a4740-390">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="a4740-391">`ContactAdministratorsAuthorizationHandler` ověří uživatele, který pracuje na prostředku, jako správce.</span><span class="sxs-lookup"><span data-stu-id="a4740-391">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="a4740-392">Správce může provádět všechny operace.</span><span class="sxs-lookup"><span data-stu-id="a4740-392">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="a4740-393">Zaregistrujte obslužné rutiny autorizace</span><span class="sxs-lookup"><span data-stu-id="a4740-393">Register the authorization handlers</span></span>

<span data-ttu-id="a4740-394">Služby používající Entity Framework Core musí být registrovány pro [vkládání závislostí](xref:fundamentals/dependency-injection) pomocí [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="a4740-394">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="a4740-395">`ContactIsOwnerAuthorizationHandler` používá ASP.NET Core [identity](xref:security/authentication/identity), která je postavená na Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="a4740-395">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="a4740-396">Zaregistrujte obslužné rutiny s kolekcí služeb, aby byly k dispozici pro `ContactsController` prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a4740-396">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="a4740-397">Do konce `ConfigureServices`přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="a4740-397">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="a4740-398">`ContactAdministratorsAuthorizationHandler` a `ContactManagerAuthorizationHandler` jsou přidány jako singleton.</span><span class="sxs-lookup"><span data-stu-id="a4740-398">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="a4740-399">Jsou to jednoznačné, protože nepoužívají EF a všechny potřebné informace jsou v parametru `Context` `HandleRequirementAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="a4740-399">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="a4740-400">Povolení podpory</span><span class="sxs-lookup"><span data-stu-id="a4740-400">Support authorization</span></span>

<span data-ttu-id="a4740-401">V této části se aktualizace stránky Razor a přidejte třídu požadavků operace.</span><span class="sxs-lookup"><span data-stu-id="a4740-401">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="a4740-402">Zkontrolujte požadavky na třídy kontaktní operace</span><span class="sxs-lookup"><span data-stu-id="a4740-402">Review the contact operations requirements class</span></span>

<span data-ttu-id="a4740-403">Zkontrolujte třídu `ContactOperations`.</span><span class="sxs-lookup"><span data-stu-id="a4740-403">Review the `ContactOperations` class.</span></span> <span data-ttu-id="a4740-404">Tato třída obsahuje požadavky na aplikace podporuje:</span><span class="sxs-lookup"><span data-stu-id="a4740-404">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="a4740-405">Vytvoření základní třídu pro stránky Razor kontakty</span><span class="sxs-lookup"><span data-stu-id="a4740-405">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="a4740-406">Vytvořte základní třídu, která obsahuje služby využité v kontaktech stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="a4740-406">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="a4740-407">Základní třída vloží kód inicializace na jednom místě:</span><span class="sxs-lookup"><span data-stu-id="a4740-407">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="a4740-408">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="a4740-408">The preceding code:</span></span>

* <span data-ttu-id="a4740-409">Přidá službu `IAuthorizationService` pro přístup k obslužným rutinám autorizace.</span><span class="sxs-lookup"><span data-stu-id="a4740-409">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="a4740-410">Přidá službu identity `UserManager`.</span><span class="sxs-lookup"><span data-stu-id="a4740-410">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="a4740-411">Přidejte `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="a4740-411">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="a4740-412">Aktualizace CreateModel</span><span class="sxs-lookup"><span data-stu-id="a4740-412">Update the CreateModel</span></span>

<span data-ttu-id="a4740-413">Aktualizujte konstruktor Create Page model tak, aby používal základní třídu `DI_BasePageModel`:</span><span class="sxs-lookup"><span data-stu-id="a4740-413">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="a4740-414">Aktualizujte metodu `CreateModel.OnPostAsync` na:</span><span class="sxs-lookup"><span data-stu-id="a4740-414">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="a4740-415">Přidejte ID uživatele do modelu `Contact`.</span><span class="sxs-lookup"><span data-stu-id="a4740-415">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="a4740-416">Ověřte, zda že má uživatel oprávnění k vytvoření kontakty obslužná rutina ověřování volejte.</span><span class="sxs-lookup"><span data-stu-id="a4740-416">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="a4740-417">Aktualizace IndexModel</span><span class="sxs-lookup"><span data-stu-id="a4740-417">Update the IndexModel</span></span>

<span data-ttu-id="a4740-418">Aktualizujte metodu `OnGetAsync`, aby se pro obecné uživatele zobrazovaly jenom schválené kontakty:</span><span class="sxs-lookup"><span data-stu-id="a4740-418">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="a4740-419">Aktualizace EditModel</span><span class="sxs-lookup"><span data-stu-id="a4740-419">Update the EditModel</span></span>

<span data-ttu-id="a4740-420">Přidejte obslužnou rutinu ověřování k ověření, že uživatel je vlastníkem kontaktu.</span><span class="sxs-lookup"><span data-stu-id="a4740-420">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="a4740-421">Vzhledem k tomu, že se ověřuje autorizace prostředků, atribut `[Authorize]` není dostatečný.</span><span class="sxs-lookup"><span data-stu-id="a4740-421">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="a4740-422">Aplikace nemá přístup k prostředku, když jsou vyhodnocovány atributy.</span><span class="sxs-lookup"><span data-stu-id="a4740-422">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="a4740-423">Autorizace na základě prostředků musí být nezbytné.</span><span class="sxs-lookup"><span data-stu-id="a4740-423">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="a4740-424">Kontroly musí provést, když má aplikace přístup k prostředku načtením v modelu stránky nebo načtením v rámci samotná obslužná rutina.</span><span class="sxs-lookup"><span data-stu-id="a4740-424">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="a4740-425">Často přistupovat k prostředku předáním klíč prostředku.</span><span class="sxs-lookup"><span data-stu-id="a4740-425">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="a4740-426">Aktualizace DeleteModel</span><span class="sxs-lookup"><span data-stu-id="a4740-426">Update the DeleteModel</span></span>

<span data-ttu-id="a4740-427">Aktualizace modelu stránku odstranit sloužící k ověření, že uživatel má oprávnění odstranit u kontaktu obslužnou rutinu ověřování.</span><span class="sxs-lookup"><span data-stu-id="a4740-427">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="a4740-428">Vložit do zobrazení autorizační službu</span><span class="sxs-lookup"><span data-stu-id="a4740-428">Inject the authorization service into the views</span></span>

<span data-ttu-id="a4740-429">V současné době ukazuje uživatelského rozhraní upravovat a odstraňovat kontakty, které uživatel nemůže upravovat odkazy.</span><span class="sxs-lookup"><span data-stu-id="a4740-429">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="a4740-430">Vložení autorizační služby do souboru *views/_ViewImports. cshtml* , aby byla dostupná pro všechna zobrazení:</span><span class="sxs-lookup"><span data-stu-id="a4740-430">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="a4740-431">Předchozí kód přidá několik příkazů `using`.</span><span class="sxs-lookup"><span data-stu-id="a4740-431">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="a4740-432">Aktualizujte odkazy pro **Úpravy** a **odstranění** na *stránkách/kontakty/index. cshtml* , aby byly vygenerovány pouze pro uživatele s příslušnými oprávněními:</span><span class="sxs-lookup"><span data-stu-id="a4740-432">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="a4740-433">Skrytí propojení od uživatele, kteří nemají oprávnění ke změně dat nebude zabezpečení aplikace.</span><span class="sxs-lookup"><span data-stu-id="a4740-433">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="a4740-434">Skrytí propojení nastaví aplikaci jako přívětivější tím, že zobrazuje pouze platné odkazy.</span><span class="sxs-lookup"><span data-stu-id="a4740-434">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="a4740-435">Uživatelé můžou hack generované adres URL pro vyvolání funkce upravit a odstranit operací s daty, které není vlastní.</span><span class="sxs-lookup"><span data-stu-id="a4740-435">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="a4740-436">Stránka Razor nebo kontroleru musí vynucovat kontroly přístupu pro data zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="a4740-436">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="a4740-437">Podrobné informace o aktualizaci</span><span class="sxs-lookup"><span data-stu-id="a4740-437">Update Details</span></span>

<span data-ttu-id="a4740-438">Aktualizujte zobrazení podrobností, aby vedoucí mohli schválit nebo odmítnout kontaktů:</span><span class="sxs-lookup"><span data-stu-id="a4740-438">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="a4740-439">Model stránky podrobnosti aktualizace:</span><span class="sxs-lookup"><span data-stu-id="a4740-439">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="a4740-440">Přidat nebo odebrat uživatele k roli</span><span class="sxs-lookup"><span data-stu-id="a4740-440">Add or remove a user to a role</span></span>

<span data-ttu-id="a4740-441">Informace o [tomto problému najdete zde](https://github.com/aspnet/AspNetCore.Docs/issues/8502) :</span><span class="sxs-lookup"><span data-stu-id="a4740-441">See [this issue](https://github.com/aspnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="a4740-442">Odebrání oprávnění uživatele.</span><span class="sxs-lookup"><span data-stu-id="a4740-442">Removing privileges from a user.</span></span> <span data-ttu-id="a4740-443">Například ztlumení uživatele v aplikaci chatu.</span><span class="sxs-lookup"><span data-stu-id="a4740-443">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="a4740-444">Přidání oprávnění pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="a4740-444">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="a4740-445">Testování dokončené aplikace</span><span class="sxs-lookup"><span data-stu-id="a4740-445">Test the completed app</span></span>

<span data-ttu-id="a4740-446">Pokud jste ještě nenastavili heslo pro osazené uživatelské účty, nastavte heslo pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets#secret-manager) :</span><span class="sxs-lookup"><span data-stu-id="a4740-446">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="a4740-447">Zvolte silné heslo: použití osm nebo více znaků, aspoň jedno velké písmeno, číslo a symbol.</span><span class="sxs-lookup"><span data-stu-id="a4740-447">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="a4740-448">`Passw0rd!` například splňuje požadavky na silné heslo.</span><span class="sxs-lookup"><span data-stu-id="a4740-448">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="a4740-449">Spusťte následující příkaz ze složky projektu, kde `<PW>` je heslo:</span><span class="sxs-lookup"><span data-stu-id="a4740-449">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="a4740-450">Zrušení a aktualizace databáze</span><span class="sxs-lookup"><span data-stu-id="a4740-450">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="a4740-451">Restartujte aplikaci k přidání dat do databáze.</span><span class="sxs-lookup"><span data-stu-id="a4740-451">Restart the app to seed the database.</span></span>

<span data-ttu-id="a4740-452">Snadný způsob, jak otestovat dokončená aplikace je spustíte tři různé prohlížeče (nebo incognito/InPrivate relace).</span><span class="sxs-lookup"><span data-stu-id="a4740-452">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="a4740-453">V jednom prohlížeči Zaregistrujte nového uživatele (například `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="a4740-453">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="a4740-454">Přihlaste se k každým prohlížečem s jiným uživatelem.</span><span class="sxs-lookup"><span data-stu-id="a4740-454">Sign in to each browser with a different user.</span></span> <span data-ttu-id="a4740-455">Ověřte následující operace:</span><span class="sxs-lookup"><span data-stu-id="a4740-455">Verify the following operations:</span></span>

* <span data-ttu-id="a4740-456">Registrovaných uživatelů můžete zobrazit všechny schválené kontaktní údaje.</span><span class="sxs-lookup"><span data-stu-id="a4740-456">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="a4740-457">Registrovaných uživatelů můžete upravit nebo odstranit svá vlastní data.</span><span class="sxs-lookup"><span data-stu-id="a4740-457">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="a4740-458">Správci mohou schvalovat a odmítat kontaktní údaje.</span><span class="sxs-lookup"><span data-stu-id="a4740-458">Managers can approve/reject contact data.</span></span> <span data-ttu-id="a4740-459">Zobrazení `Details` zobrazuje tlačítka **schválení** a **odmítnutí** .</span><span class="sxs-lookup"><span data-stu-id="a4740-459">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="a4740-460">Správci můžou schvalovat a odmítat a upravit nebo odstranit všechna data.</span><span class="sxs-lookup"><span data-stu-id="a4740-460">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="a4740-461">Uživatel</span><span class="sxs-lookup"><span data-stu-id="a4740-461">User</span></span>                | <span data-ttu-id="a4740-462">Nasazených aplikací</span><span class="sxs-lookup"><span data-stu-id="a4740-462">Seeded by the app</span></span> | <span data-ttu-id="a4740-463">Možnosti</span><span class="sxs-lookup"><span data-stu-id="a4740-463">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="a4740-464">Ne</span><span class="sxs-lookup"><span data-stu-id="a4740-464">No</span></span>                | <span data-ttu-id="a4740-465">Upravit nebo odstranit vlastní data.</span><span class="sxs-lookup"><span data-stu-id="a4740-465">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="a4740-466">Ano</span><span class="sxs-lookup"><span data-stu-id="a4740-466">Yes</span></span>               | <span data-ttu-id="a4740-467">Vlastní data, schvalovat a odmítat a upravit nebo odstranit.</span><span class="sxs-lookup"><span data-stu-id="a4740-467">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="a4740-468">Ano</span><span class="sxs-lookup"><span data-stu-id="a4740-468">Yes</span></span>               | <span data-ttu-id="a4740-469">Schválit nebo odmítnout a upravit nebo odstranit všechna data.</span><span class="sxs-lookup"><span data-stu-id="a4740-469">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="a4740-470">Vytvoření kontaktu v prohlížeči na správce.</span><span class="sxs-lookup"><span data-stu-id="a4740-470">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="a4740-471">Zkopírujte adresu URL pro odstranění a upravit z kontaktujte správce.</span><span class="sxs-lookup"><span data-stu-id="a4740-471">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="a4740-472">Vložte tyto odkazy do testů webového prohlížeče k ověření, že testovací uživatel nemůže provádět tyto operace.</span><span class="sxs-lookup"><span data-stu-id="a4740-472">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="a4740-473">Vytvořit úvodní aplikaci</span><span class="sxs-lookup"><span data-stu-id="a4740-473">Create the starter app</span></span>

* <span data-ttu-id="a4740-474">Vytvoření aplikace stránky Razor s názvem "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="a4740-474">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="a4740-475">Vytvořte aplikaci pomocí **individuálních uživatelských účtů**.</span><span class="sxs-lookup"><span data-stu-id="a4740-475">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="a4740-476">Pojmenujte ji "ContactManager" tak obor názvů odpovídá oboru názvů použitého v ukázce.</span><span class="sxs-lookup"><span data-stu-id="a4740-476">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="a4740-477">`-uld` určuje LocalDB místo SQLite.</span><span class="sxs-lookup"><span data-stu-id="a4740-477">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="a4740-478">Přidat *modely/kontakt. cs*:</span><span class="sxs-lookup"><span data-stu-id="a4740-478">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="a4740-479">Generování `Contact` modelu.</span><span class="sxs-lookup"><span data-stu-id="a4740-479">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="a4740-480">Vytvořte počáteční migraci a aktualizaci databáze:</span><span class="sxs-lookup"><span data-stu-id="a4740-480">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="a4740-481">Aktualizujte kotvu **ContactManager** v souboru *pages/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="a4740-481">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="a4740-482">Testování aplikace pomocí vytváření, úpravy a odstranění kontaktu</span><span class="sxs-lookup"><span data-stu-id="a4740-482">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="a4740-483">Přidání dat do databáze</span><span class="sxs-lookup"><span data-stu-id="a4740-483">Seed the database</span></span>

<span data-ttu-id="a4740-484">Do složky *data* přidejte třídu [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) .</span><span class="sxs-lookup"><span data-stu-id="a4740-484">Add the [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="a4740-485">Volat `SeedData.Initialize` z `Main`:</span><span class="sxs-lookup"><span data-stu-id="a4740-485">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="a4740-486">Otestujte, že aplikace naplnila databázi.</span><span class="sxs-lookup"><span data-stu-id="a4740-486">Test that the app seeded the database.</span></span> <span data-ttu-id="a4740-487">Pokud existují nějaké řádky v kontaktu DB, metoda počáteční hodnoty není spuštěna.</span><span class="sxs-lookup"><span data-stu-id="a4740-487">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="a4740-488">Další zdroje informací:</span><span class="sxs-lookup"><span data-stu-id="a4740-488">Additional resources</span></span>

* [<span data-ttu-id="a4740-489">Vytvoření webové aplikace .NET Core a SQL Database v Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a4740-489">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="a4740-490">[ASP.NET Core autorizačního prostředí](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="a4740-490">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="a4740-491">Toto testovací prostředí obsahuje větší podrobnosti o funkcích zabezpečení v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="a4740-491">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="a4740-492">Vlastní autorizace na základě zásad</span><span class="sxs-lookup"><span data-stu-id="a4740-492">Custom policy-based authorization</span></span>](xref:security/authorization/policies)

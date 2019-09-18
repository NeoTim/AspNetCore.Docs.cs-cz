---
title: Vytvoření aplikace ASP.NET Core s uživatelskými daty chráněnými autorizací
author: rick-anderson
description: Zjistěte, jak vytvořit aplikace Razor Pages s uživatelskými daty chráněnými autorizací. Zahrnuje HTTPS, ověřování, zabezpečení ASP.NET Core Identity.
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
uid: security/authorization/secure-data
ms.openlocfilehash: d827f6f839c9e42e6d3d7b04fe8b24a1c9732aee
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082444"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="eb56e-104">Vytvoření aplikace ASP.NET Core s uživatelskými daty chráněnými autorizací</span><span class="sxs-lookup"><span data-stu-id="eb56e-104">Create an ASP.NET Core app with user data protected by authorization</span></span>

<span data-ttu-id="eb56e-105">Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="eb56e-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="eb56e-106">Zobrazit [tento PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) pro verzi technologie ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="eb56e-106">See [this PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core MVC version.</span></span> <span data-ttu-id="eb56e-107">ASP.NET Core 1.1 verzi tohoto kurzu je v [to](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) složky.</span><span class="sxs-lookup"><span data-stu-id="eb56e-107">The ASP.NET Core 1.1 version of this tutorial is in [this](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) folder.</span></span> <span data-ttu-id="eb56e-108">1\.1 ukázka ASP.NET Core je v [ukázky](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span><span class="sxs-lookup"><span data-stu-id="eb56e-108">The 1.1 ASP.NET Core sample is in the [samples](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="eb56e-109">Zobrazit [tento pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="eb56e-109">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="eb56e-110">Tento kurz ukazuje, jak vytvořit webovou aplikaci ASP.NET Core s uživatelskými daty chráněnými autorizací.</span><span class="sxs-lookup"><span data-stu-id="eb56e-110">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="eb56e-111">Zobrazí seznam kontakty, které ověřeným uživatelům (registrovaných) jste vytvořili.</span><span class="sxs-lookup"><span data-stu-id="eb56e-111">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="eb56e-112">Existují tři skupiny zabezpečení:</span><span class="sxs-lookup"><span data-stu-id="eb56e-112">There are three security groups:</span></span>

* <span data-ttu-id="eb56e-113">**Zaregistrované uživatele** můžete zobrazit všechny schválené data a můžete upravit nebo odstranit svá vlastní data.</span><span class="sxs-lookup"><span data-stu-id="eb56e-113">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="eb56e-114">**Správci** můžete schválit nebo odmítnout kontaktní údaje.</span><span class="sxs-lookup"><span data-stu-id="eb56e-114">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="eb56e-115">Uživatelé vidí pouze schválené kontakty.</span><span class="sxs-lookup"><span data-stu-id="eb56e-115">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="eb56e-116">**Správci** můžete schvalovat a odmítat a upravit nebo odstranit všechna data.</span><span class="sxs-lookup"><span data-stu-id="eb56e-116">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="eb56e-117">Obrázky v tomto dokumentu se přesně neshodují s nejnovějšími šablonami.</span><span class="sxs-lookup"><span data-stu-id="eb56e-117">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="eb56e-118">Na následujícím obrázku, uživatel Rick (`rick@example.com`) je přihlášený.</span><span class="sxs-lookup"><span data-stu-id="eb56e-118">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="eb56e-119">Rick může zobrazit jenom schválené kontakty a **upravit**/**odstranit**/**vytvořit nový** odkazy pro jeho kontakty.</span><span class="sxs-lookup"><span data-stu-id="eb56e-119">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="eb56e-120">Pouze poslední záznam vytvořil Rick, zobrazí **upravit** a **odstranit** odkazy.</span><span class="sxs-lookup"><span data-stu-id="eb56e-120">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="eb56e-121">Ostatní uživatelé neuvidí poslední záznam, dokud správce nebo správce změní stav na "Schváleno".</span><span class="sxs-lookup"><span data-stu-id="eb56e-121">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Snímek obrazovky zobrazující Rick přihlášení](secure-data/_static/rick.png)

<span data-ttu-id="eb56e-123">Na následujícím obrázku `manager@contoso.com` je přihlášen a v roli manažera:</span><span class="sxs-lookup"><span data-stu-id="eb56e-123">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Snímek obrazovky zobrazující manager@contoso.com přihlášení](secure-data/_static/manager1.png)

<span data-ttu-id="eb56e-125">Následující obrázek ukazuje vedoucí zobrazení podrobností o kontaktu:</span><span class="sxs-lookup"><span data-stu-id="eb56e-125">The following image shows the managers details view of a contact:</span></span>

![Zobrazení manažera kontaktu](secure-data/_static/manager.png)

<span data-ttu-id="eb56e-127">**Schválit** a **odmítnout** tlačítek se zobrazí pouze správci a správci.</span><span class="sxs-lookup"><span data-stu-id="eb56e-127">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="eb56e-128">Na následujícím obrázku `admin@contoso.com` je přihlášen a v roli správce:</span><span class="sxs-lookup"><span data-stu-id="eb56e-128">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Snímek obrazovky zobrazující admin@contoso.com přihlášení](secure-data/_static/admin.png)

<span data-ttu-id="eb56e-130">Správce má všechna oprávnění.</span><span class="sxs-lookup"><span data-stu-id="eb56e-130">The administrator has all privileges.</span></span> <span data-ttu-id="eb56e-131">Může číst/upravovat/odstraňovat všechny kontakty a změnit stav kontakty.</span><span class="sxs-lookup"><span data-stu-id="eb56e-131">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="eb56e-132">Aplikace byla vytvořena pomocí [generování uživatelského rozhraní](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) následující `Contact` modelu:</span><span class="sxs-lookup"><span data-stu-id="eb56e-132">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="eb56e-133">Ukázka obsahuje následující rutiny autorizace:</span><span class="sxs-lookup"><span data-stu-id="eb56e-133">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="eb56e-134">`ContactIsOwnerAuthorizationHandler`: Zajišťuje, aby uživatel mohl upravovat pouze svá data.</span><span class="sxs-lookup"><span data-stu-id="eb56e-134">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="eb56e-135">`ContactManagerAuthorizationHandler`: Umožňuje správcům schvalovat nebo odmítat kontakty.</span><span class="sxs-lookup"><span data-stu-id="eb56e-135">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="eb56e-136">`ContactAdministratorsAuthorizationHandler`: Umožňuje správcům schvalovat nebo odmítat kontakty a upravovat nebo odstraňovat kontakty.</span><span class="sxs-lookup"><span data-stu-id="eb56e-136">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="eb56e-137">Požadavky</span><span class="sxs-lookup"><span data-stu-id="eb56e-137">Prerequisites</span></span>

<span data-ttu-id="eb56e-138">V tomto kurzu je advanced.</span><span class="sxs-lookup"><span data-stu-id="eb56e-138">This tutorial is advanced.</span></span> <span data-ttu-id="eb56e-139">Měli byste se seznámit s:</span><span class="sxs-lookup"><span data-stu-id="eb56e-139">You should be familiar with:</span></span>

* [<span data-ttu-id="eb56e-140">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eb56e-140">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="eb56e-141">Ověřování</span><span class="sxs-lookup"><span data-stu-id="eb56e-141">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="eb56e-142">Potvrzení účtu a obnovení hesla</span><span class="sxs-lookup"><span data-stu-id="eb56e-142">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="eb56e-143">Autorizace</span><span class="sxs-lookup"><span data-stu-id="eb56e-143">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="eb56e-144">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="eb56e-144">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="eb56e-145">Starter a dokončené aplikace</span><span class="sxs-lookup"><span data-stu-id="eb56e-145">The starter and completed app</span></span>

<span data-ttu-id="eb56e-146">[Stáhněte si](xref:index#how-to-download-a-sample) [Dokončit](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) aplikace.</span><span class="sxs-lookup"><span data-stu-id="eb56e-146">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="eb56e-147">[Test](#test-the-completed-app) dokončené aplikace tak, že jste se seznámili s jeho funkcí zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="eb56e-147">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="eb56e-148">Úvodní aplikaci</span><span class="sxs-lookup"><span data-stu-id="eb56e-148">The starter app</span></span>

<span data-ttu-id="eb56e-149">[Stáhněte si](xref:index#how-to-download-a-sample) [starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) aplikace.</span><span class="sxs-lookup"><span data-stu-id="eb56e-149">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="eb56e-150">Spusťte aplikaci, klepněte **ContactManager** propojit a ověření můžete vytvářet, upravovat a odstraňovat kontakt.</span><span class="sxs-lookup"><span data-stu-id="eb56e-150">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="eb56e-151">Zabezpečení dat uživatele</span><span class="sxs-lookup"><span data-stu-id="eb56e-151">Secure user data</span></span>

<span data-ttu-id="eb56e-152">Následující oddíly mají všechny hlavní kroky k vytvoření aplikace pro data zabezpečení uživatele.</span><span class="sxs-lookup"><span data-stu-id="eb56e-152">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="eb56e-153">Vám může být užitečné k odkazování na dokončení projektu.</span><span class="sxs-lookup"><span data-stu-id="eb56e-153">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="eb56e-154">Tie kontaktní údaje pro uživatele</span><span class="sxs-lookup"><span data-stu-id="eb56e-154">Tie the contact data to the user</span></span>

<span data-ttu-id="eb56e-155">Použití technologie ASP.NET [Identity](xref:security/authentication/identity) ID uživatele, aby tak uživatelé můžete upravit jejich data, ale ne data jiných uživatelů.</span><span class="sxs-lookup"><span data-stu-id="eb56e-155">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="eb56e-156">Přidat `OwnerID` a `ContactStatus` k `Contact` modelu:</span><span class="sxs-lookup"><span data-stu-id="eb56e-156">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="eb56e-157">`OwnerID` je ID uživatele z `AspNetUser` v tabulku [Identity](xref:security/authentication/identity) databáze.</span><span class="sxs-lookup"><span data-stu-id="eb56e-157">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="eb56e-158">`Status` Pole určuje, zda je kontakt zobrazitelné obecný uživatel.</span><span class="sxs-lookup"><span data-stu-id="eb56e-158">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="eb56e-159">Vytvořte novou migraci a aktualizaci databáze:</span><span class="sxs-lookup"><span data-stu-id="eb56e-159">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="eb56e-160">Přidání služby Role na identitu</span><span class="sxs-lookup"><span data-stu-id="eb56e-160">Add Role services to Identity</span></span>

<span data-ttu-id="eb56e-161">Připojit [kliknutím na Přidat role](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) přidat služby rolí:</span><span class="sxs-lookup"><span data-stu-id="eb56e-161">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a><span data-ttu-id="eb56e-162">Vyžadovat ověření uživatelé</span><span class="sxs-lookup"><span data-stu-id="eb56e-162">Require authenticated users</span></span>

<span data-ttu-id="eb56e-163">Nastavte výchozí zásady ověřování tak, aby vyžadovala ověření uživatelů:</span><span class="sxs-lookup"><span data-stu-id="eb56e-163">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 <span data-ttu-id="eb56e-164">Můžete zrušit ověřování na úrovni metody stránky Razor, kontroler nebo akce s `[AllowAnonymous]` atribut.</span><span class="sxs-lookup"><span data-stu-id="eb56e-164">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="eb56e-165">Nastavení výchozí zásady ověřování tak, aby vyžadovala ověření uživatelů chrání nově přidané Razor Pages a kontrolery.</span><span class="sxs-lookup"><span data-stu-id="eb56e-165">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="eb56e-166">Ve výchozím nastavení je vyžadováno ověření je bezpečnější než spoléhání se na nové řadiče a Razor Pages zahrnout s `[Authorize]` atribut.</span><span class="sxs-lookup"><span data-stu-id="eb56e-166">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="eb56e-167">Přidejte [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) na stránky rejstřík a soukromí, aby anonymní uživatelé mohli získat informace o lokalitě před jejich registrací.</span><span class="sxs-lookup"><span data-stu-id="eb56e-167">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index and Privacy pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="eb56e-168">Konfigurace testovací účet</span><span class="sxs-lookup"><span data-stu-id="eb56e-168">Configure the test account</span></span>

<span data-ttu-id="eb56e-169">`SeedData` Třída vytvoří dva účty: správce a správce.</span><span class="sxs-lookup"><span data-stu-id="eb56e-169">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="eb56e-170">Použití [nástroj tajný klíč správce](xref:security/app-secrets) nastavení hesla pro tyto účty.</span><span class="sxs-lookup"><span data-stu-id="eb56e-170">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="eb56e-171">Nastavte heslo z adresáře projektu (adresáře, který obsahuje *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="eb56e-171">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="eb56e-172">Pokud není zadán silné heslo, je vyvolána výjimka, když `SeedData.Initialize` je volána.</span><span class="sxs-lookup"><span data-stu-id="eb56e-172">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="eb56e-173">Aktualizace `Main` test heslo budete používat:</span><span class="sxs-lookup"><span data-stu-id="eb56e-173">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="eb56e-174">Vytvoření testovacích účtů a aktualizovat kontakty</span><span class="sxs-lookup"><span data-stu-id="eb56e-174">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="eb56e-175">Aktualizace `Initialize` metodu `SeedData` třídy za účelem vytvoření testovacích účtů:</span><span class="sxs-lookup"><span data-stu-id="eb56e-175">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="eb56e-176">Přidat správce ID uživatele a `ContactStatus` kontaktům.</span><span class="sxs-lookup"><span data-stu-id="eb56e-176">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="eb56e-177">Proveďte jednu z kontakty "Submitted" a jedné "byl odmítnut".</span><span class="sxs-lookup"><span data-stu-id="eb56e-177">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="eb56e-178">Přidáte ID uživatele a stav pro všechny kontakty.</span><span class="sxs-lookup"><span data-stu-id="eb56e-178">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="eb56e-179">Je zobrazena pouze jeden kontakt:</span><span class="sxs-lookup"><span data-stu-id="eb56e-179">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="eb56e-180">Vytvořit vlastníka, správce a Správce autorizace obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="eb56e-180">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="eb56e-181">Vytvoření `ContactIsOwnerAuthorizationHandler` třídy v *autorizace* složky.</span><span class="sxs-lookup"><span data-stu-id="eb56e-181">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="eb56e-182">`ContactIsOwnerAuthorizationHandler` Ověřuje, že uživatel na prostředek vlastníkem prostředku.</span><span class="sxs-lookup"><span data-stu-id="eb56e-182">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="eb56e-183">`ContactIsOwnerAuthorizationHandler` Volání [kontextu. Úspěšné](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) aktuálně ověřeného uživatele při jeho vlastníkem.</span><span class="sxs-lookup"><span data-stu-id="eb56e-183">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="eb56e-184">Obslužné rutiny autorizace obecně:</span><span class="sxs-lookup"><span data-stu-id="eb56e-184">Authorization handlers generally:</span></span>

* <span data-ttu-id="eb56e-185">Vrátí `context.Succeed` Pokud jsou splněny.</span><span class="sxs-lookup"><span data-stu-id="eb56e-185">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="eb56e-186">Vrátí `Task.CompletedTask` Pokud požadavky nejsou splněny.</span><span class="sxs-lookup"><span data-stu-id="eb56e-186">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="eb56e-187">`Task.CompletedTask`není úspěch nebo neúspěch&mdash;, umožňuje spuštění dalších obslužných rutin autorizace.</span><span class="sxs-lookup"><span data-stu-id="eb56e-187">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="eb56e-188">Pokud je potřeba explicitně nezdaří, vrátí [kontextu. Selhání](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="eb56e-188">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="eb56e-189">Aplikaci umožňuje kontaktní vlastníkům upravit, odstranit a vytvořit svoje vlastní data.</span><span class="sxs-lookup"><span data-stu-id="eb56e-189">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="eb56e-190">`ContactIsOwnerAuthorizationHandler` není nutné ke kontrole operace předané v parametru požadavku.</span><span class="sxs-lookup"><span data-stu-id="eb56e-190">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="eb56e-191">Vytvořte obslužnou rutinu Správce autorizací</span><span class="sxs-lookup"><span data-stu-id="eb56e-191">Create a manager authorization handler</span></span>

<span data-ttu-id="eb56e-192">Vytvoření `ContactManagerAuthorizationHandler` třídy v *autorizace* složky.</span><span class="sxs-lookup"><span data-stu-id="eb56e-192">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="eb56e-193">`ContactManagerAuthorizationHandler` Ověřuje uživatele na prostředek je správce.</span><span class="sxs-lookup"><span data-stu-id="eb56e-193">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="eb56e-194">Pouze vedoucí mohli schválit nebo odmítnout změny obsahu (nové nebo změněné).</span><span class="sxs-lookup"><span data-stu-id="eb56e-194">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="eb56e-195">Vytvořte obslužnou rutinu Správce autorizací</span><span class="sxs-lookup"><span data-stu-id="eb56e-195">Create an administrator authorization handler</span></span>

<span data-ttu-id="eb56e-196">Vytvoření `ContactAdministratorsAuthorizationHandler` třídy v *autorizace* složky.</span><span class="sxs-lookup"><span data-stu-id="eb56e-196">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="eb56e-197">`ContactAdministratorsAuthorizationHandler` Ověřuje uživatele na prostředek je správce.</span><span class="sxs-lookup"><span data-stu-id="eb56e-197">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="eb56e-198">Správce může provádět všechny operace.</span><span class="sxs-lookup"><span data-stu-id="eb56e-198">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="eb56e-199">Zaregistrujte obslužné rutiny autorizace</span><span class="sxs-lookup"><span data-stu-id="eb56e-199">Register the authorization handlers</span></span>

<span data-ttu-id="eb56e-200">Pomocí Entity Framework Core Services musí být zaregistrovaný pro [injektáž závislostí](xref:fundamentals/dependency-injection) pomocí [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="eb56e-200">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="eb56e-201">`ContactIsOwnerAuthorizationHandler` Používá ASP.NET Core [Identity](xref:security/authentication/identity), která je založená na Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="eb56e-201">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="eb56e-202">Obslužné rutiny zaregistrovat u kolekce služby jsou k dispozici na `ContactsController` prostřednictvím [injektáž závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="eb56e-202">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="eb56e-203">Přidejte následující kód do konce `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="eb56e-203">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="eb56e-204">`ContactAdministratorsAuthorizationHandler` a `ContactManagerAuthorizationHandler` jsou přidány jako jednotlivé prvky.</span><span class="sxs-lookup"><span data-stu-id="eb56e-204">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="eb56e-205">Jednotlivé prvky jsou vzhledem k tomu, že se nepoužívají EF a všechny informace potřebné musí být `Context` parametr `HandleRequirementAsync` metoda.</span><span class="sxs-lookup"><span data-stu-id="eb56e-205">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="eb56e-206">Povolení podpory</span><span class="sxs-lookup"><span data-stu-id="eb56e-206">Support authorization</span></span>

<span data-ttu-id="eb56e-207">V této části se aktualizace stránky Razor a přidejte třídu požadavků operace.</span><span class="sxs-lookup"><span data-stu-id="eb56e-207">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="eb56e-208">Zkontrolujte požadavky na třídy kontaktní operace</span><span class="sxs-lookup"><span data-stu-id="eb56e-208">Review the contact operations requirements class</span></span>

<span data-ttu-id="eb56e-209">Zkontrolujte `ContactOperations` třídy.</span><span class="sxs-lookup"><span data-stu-id="eb56e-209">Review the `ContactOperations` class.</span></span> <span data-ttu-id="eb56e-210">Tato třída obsahuje požadavky na aplikace podporuje:</span><span class="sxs-lookup"><span data-stu-id="eb56e-210">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="eb56e-211">Vytvoření základní třídu pro stránky Razor kontakty</span><span class="sxs-lookup"><span data-stu-id="eb56e-211">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="eb56e-212">Vytvořte základní třídu, která obsahuje služby využité v kontaktech stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="eb56e-212">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="eb56e-213">Základní třída vloží kód inicializace na jednom místě:</span><span class="sxs-lookup"><span data-stu-id="eb56e-213">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="eb56e-214">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="eb56e-214">The preceding code:</span></span>

* <span data-ttu-id="eb56e-215">Přidá `IAuthorizationService` služby pro přístup k rutinám autorizace.</span><span class="sxs-lookup"><span data-stu-id="eb56e-215">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="eb56e-216">Přidá identitu `UserManager` služby.</span><span class="sxs-lookup"><span data-stu-id="eb56e-216">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="eb56e-217">Přidat `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="eb56e-217">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="eb56e-218">Aktualizace CreateModel</span><span class="sxs-lookup"><span data-stu-id="eb56e-218">Update the CreateModel</span></span>

<span data-ttu-id="eb56e-219">Aktualizovat konstruktoru vytvořit stránku model použití `DI_BasePageModel` základní třídy:</span><span class="sxs-lookup"><span data-stu-id="eb56e-219">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="eb56e-220">Aktualizace `CreateModel.OnPostAsync` metodu:</span><span class="sxs-lookup"><span data-stu-id="eb56e-220">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="eb56e-221">Přidat ID uživatele, `Contact` modelu.</span><span class="sxs-lookup"><span data-stu-id="eb56e-221">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="eb56e-222">Ověřte, zda že má uživatel oprávnění k vytvoření kontakty obslužná rutina ověřování volejte.</span><span class="sxs-lookup"><span data-stu-id="eb56e-222">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="eb56e-223">Aktualizace IndexModel</span><span class="sxs-lookup"><span data-stu-id="eb56e-223">Update the IndexModel</span></span>

<span data-ttu-id="eb56e-224">Aktualizace `OnGetAsync` metody, jsou uvedeny pouze schválené kontakty obecné uživatelům:</span><span class="sxs-lookup"><span data-stu-id="eb56e-224">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="eb56e-225">Aktualizace EditModel</span><span class="sxs-lookup"><span data-stu-id="eb56e-225">Update the EditModel</span></span>

<span data-ttu-id="eb56e-226">Přidejte obslužnou rutinu ověřování k ověření, že uživatel je vlastníkem kontaktu.</span><span class="sxs-lookup"><span data-stu-id="eb56e-226">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="eb56e-227">Protože ra se ověřuje, `[Authorize]` atribut není dostatek.</span><span class="sxs-lookup"><span data-stu-id="eb56e-227">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="eb56e-228">Aplikace nemá přístup k prostředku, když jsou vyhodnocovány atributy.</span><span class="sxs-lookup"><span data-stu-id="eb56e-228">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="eb56e-229">Autorizace na základě prostředků musí být nezbytné.</span><span class="sxs-lookup"><span data-stu-id="eb56e-229">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="eb56e-230">Kontroly musí provést, když má aplikace přístup k prostředku načtením v modelu stránky nebo načtením v rámci samotná obslužná rutina.</span><span class="sxs-lookup"><span data-stu-id="eb56e-230">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="eb56e-231">Často přistupovat k prostředku předáním klíč prostředku.</span><span class="sxs-lookup"><span data-stu-id="eb56e-231">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="eb56e-232">Aktualizace DeleteModel</span><span class="sxs-lookup"><span data-stu-id="eb56e-232">Update the DeleteModel</span></span>

<span data-ttu-id="eb56e-233">Aktualizace modelu stránku odstranit sloužící k ověření, že uživatel má oprávnění odstranit u kontaktu obslužnou rutinu ověřování.</span><span class="sxs-lookup"><span data-stu-id="eb56e-233">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="eb56e-234">Vložit do zobrazení autorizační službu</span><span class="sxs-lookup"><span data-stu-id="eb56e-234">Inject the authorization service into the views</span></span>

<span data-ttu-id="eb56e-235">V současné době ukazuje uživatelského rozhraní upravovat a odstraňovat kontakty, které uživatel nemůže upravovat odkazy.</span><span class="sxs-lookup"><span data-stu-id="eb56e-235">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="eb56e-236">Vložení autorizační služby do souboru *Pages/_ViewImports. cshtml* , aby bylo dostupné pro všechna zobrazení:</span><span class="sxs-lookup"><span data-stu-id="eb56e-236">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="eb56e-237">Předchozí kód přidá několik `using` příkazy.</span><span class="sxs-lookup"><span data-stu-id="eb56e-237">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="eb56e-238">Aktualizace **upravit** a **odstranit** odkazů v *Pages/Contacts/Index.cshtml* tak, že pouze vykreslen pro uživatele s příslušnými oprávněními:</span><span class="sxs-lookup"><span data-stu-id="eb56e-238">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="eb56e-239">Skrytí propojení od uživatele, kteří nemají oprávnění ke změně dat nebude zabezpečení aplikace.</span><span class="sxs-lookup"><span data-stu-id="eb56e-239">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="eb56e-240">Skrytí propojení nastaví aplikaci jako přívětivější tím, že zobrazuje pouze platné odkazy.</span><span class="sxs-lookup"><span data-stu-id="eb56e-240">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="eb56e-241">Uživatelé můžou hack generované adres URL pro vyvolání funkce upravit a odstranit operací s daty, které není vlastní.</span><span class="sxs-lookup"><span data-stu-id="eb56e-241">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="eb56e-242">Stránka Razor nebo kontroleru musí vynucovat kontroly přístupu pro data zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="eb56e-242">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="eb56e-243">Podrobné informace o aktualizaci</span><span class="sxs-lookup"><span data-stu-id="eb56e-243">Update Details</span></span>

<span data-ttu-id="eb56e-244">Aktualizujte zobrazení podrobností, aby vedoucí mohli schválit nebo odmítnout kontaktů:</span><span class="sxs-lookup"><span data-stu-id="eb56e-244">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="eb56e-245">Model stránky podrobnosti aktualizace:</span><span class="sxs-lookup"><span data-stu-id="eb56e-245">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="eb56e-246">Přidat nebo odebrat uživatele k roli</span><span class="sxs-lookup"><span data-stu-id="eb56e-246">Add or remove a user to a role</span></span>

<span data-ttu-id="eb56e-247">Zobrazit [tento problém](https://github.com/aspnet/AspNetCore.Docs/issues/8502) informace na:</span><span class="sxs-lookup"><span data-stu-id="eb56e-247">See [this issue](https://github.com/aspnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="eb56e-248">Odebrání oprávnění uživatele.</span><span class="sxs-lookup"><span data-stu-id="eb56e-248">Removing privileges from a user.</span></span> <span data-ttu-id="eb56e-249">Například ztlumení uživatele v aplikaci chatu.</span><span class="sxs-lookup"><span data-stu-id="eb56e-249">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="eb56e-250">Přidání oprávnění pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="eb56e-250">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="eb56e-251">Testování dokončené aplikace</span><span class="sxs-lookup"><span data-stu-id="eb56e-251">Test the completed app</span></span>

<span data-ttu-id="eb56e-252">Pokud jste dosud nenastavili hesla pro dosazené uživatelské účty, použijte [nástroj tajný klíč správce](xref:security/app-secrets#secret-manager) nastavit heslo:</span><span class="sxs-lookup"><span data-stu-id="eb56e-252">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="eb56e-253">Vyberte silné heslo: Použijte osm nebo více znaků a alespoň jedno velké písmeno, číslici a symbol.</span><span class="sxs-lookup"><span data-stu-id="eb56e-253">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="eb56e-254">Například `Passw0rd!` splňuje požadavky na silné heslo.</span><span class="sxs-lookup"><span data-stu-id="eb56e-254">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="eb56e-255">Spusťte následující příkaz ze složky projektu, kde `<PW>` je heslo:</span><span class="sxs-lookup"><span data-stu-id="eb56e-255">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="eb56e-256">Pokud má kontaktů:</span><span class="sxs-lookup"><span data-stu-id="eb56e-256">If the app has contacts:</span></span>

* <span data-ttu-id="eb56e-257">Odstranění všech záznamů v `Contact` tabulky.</span><span class="sxs-lookup"><span data-stu-id="eb56e-257">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="eb56e-258">Restartujte aplikaci k přidání dat do databáze.</span><span class="sxs-lookup"><span data-stu-id="eb56e-258">Restart the app to seed the database.</span></span>

<span data-ttu-id="eb56e-259">Snadný způsob, jak otestovat dokončená aplikace je spustíte tři různé prohlížeče (nebo incognito/InPrivate relace).</span><span class="sxs-lookup"><span data-stu-id="eb56e-259">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="eb56e-260">V jeden prohlížeč, registraci nového uživatele (například `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="eb56e-260">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="eb56e-261">Přihlaste se k každým prohlížečem s jiným uživatelem.</span><span class="sxs-lookup"><span data-stu-id="eb56e-261">Sign in to each browser with a different user.</span></span> <span data-ttu-id="eb56e-262">Ověřte následující operace:</span><span class="sxs-lookup"><span data-stu-id="eb56e-262">Verify the following operations:</span></span>

* <span data-ttu-id="eb56e-263">Registrovaných uživatelů můžete zobrazit všechny schválené kontaktní údaje.</span><span class="sxs-lookup"><span data-stu-id="eb56e-263">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="eb56e-264">Registrovaných uživatelů můžete upravit nebo odstranit svá vlastní data.</span><span class="sxs-lookup"><span data-stu-id="eb56e-264">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="eb56e-265">Správci mohou schvalovat a odmítat kontaktní údaje.</span><span class="sxs-lookup"><span data-stu-id="eb56e-265">Managers can approve/reject contact data.</span></span> <span data-ttu-id="eb56e-266">`Details` Zobrazení ukazuje **schválit** a **odmítnout** tlačítka.</span><span class="sxs-lookup"><span data-stu-id="eb56e-266">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="eb56e-267">Správci můžou schvalovat a odmítat a upravit nebo odstranit všechna data.</span><span class="sxs-lookup"><span data-stu-id="eb56e-267">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="eb56e-268">Uživatel</span><span class="sxs-lookup"><span data-stu-id="eb56e-268">User</span></span>                | <span data-ttu-id="eb56e-269">Nasazených aplikací</span><span class="sxs-lookup"><span data-stu-id="eb56e-269">Seeded by the app</span></span> | <span data-ttu-id="eb56e-270">Možnosti</span><span class="sxs-lookup"><span data-stu-id="eb56e-270">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="eb56e-271">Ne</span><span class="sxs-lookup"><span data-stu-id="eb56e-271">No</span></span>                | <span data-ttu-id="eb56e-272">Upravit nebo odstranit vlastní data.</span><span class="sxs-lookup"><span data-stu-id="eb56e-272">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="eb56e-273">Ano</span><span class="sxs-lookup"><span data-stu-id="eb56e-273">Yes</span></span>               | <span data-ttu-id="eb56e-274">Vlastní data, schvalovat a odmítat a upravit nebo odstranit.</span><span class="sxs-lookup"><span data-stu-id="eb56e-274">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="eb56e-275">Ano</span><span class="sxs-lookup"><span data-stu-id="eb56e-275">Yes</span></span>               | <span data-ttu-id="eb56e-276">Schválit nebo odmítnout a upravit nebo odstranit všechna data.</span><span class="sxs-lookup"><span data-stu-id="eb56e-276">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="eb56e-277">Vytvoření kontaktu v prohlížeči na správce.</span><span class="sxs-lookup"><span data-stu-id="eb56e-277">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="eb56e-278">Zkopírujte adresu URL pro odstranění a upravit z kontaktujte správce.</span><span class="sxs-lookup"><span data-stu-id="eb56e-278">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="eb56e-279">Vložte tyto odkazy do testů webového prohlížeče k ověření, že testovací uživatel nemůže provádět tyto operace.</span><span class="sxs-lookup"><span data-stu-id="eb56e-279">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="eb56e-280">Vytvořit úvodní aplikaci</span><span class="sxs-lookup"><span data-stu-id="eb56e-280">Create the starter app</span></span>

* <span data-ttu-id="eb56e-281">Vytvoření aplikace stránky Razor s názvem "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="eb56e-281">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="eb56e-282">Vytvoření aplikace s **jednotlivých uživatelských účtů**.</span><span class="sxs-lookup"><span data-stu-id="eb56e-282">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="eb56e-283">Pojmenujte ji "ContactManager" tak obor názvů odpovídá oboru názvů použitého v ukázce.</span><span class="sxs-lookup"><span data-stu-id="eb56e-283">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="eb56e-284">`-uld` Určuje LocalDB místo SQLite</span><span class="sxs-lookup"><span data-stu-id="eb56e-284">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="eb56e-285">Přidat *modely/kontakt. cs*:</span><span class="sxs-lookup"><span data-stu-id="eb56e-285">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="eb56e-286">Vygenerované uživatelské rozhraní `Contact` modelu.</span><span class="sxs-lookup"><span data-stu-id="eb56e-286">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="eb56e-287">Vytvořte počáteční migraci a aktualizaci databáze:</span><span class="sxs-lookup"><span data-stu-id="eb56e-287">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="eb56e-288">Pokud se zobrazí chyba s `dotnet aspnet-codegenerator razorpage` příkazem, přečtěte si [Tento problém GitHub](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="eb56e-288">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="eb56e-289">Aktualizujte kotvu **ContactManager** v souboru *Pages/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="eb56e-289">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="eb56e-290">Testování aplikace pomocí vytváření, úpravy a odstranění kontaktu</span><span class="sxs-lookup"><span data-stu-id="eb56e-290">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="eb56e-291">Přidání dat do databáze</span><span class="sxs-lookup"><span data-stu-id="eb56e-291">Seed the database</span></span>

<span data-ttu-id="eb56e-292">Do složky *data* přidejte třídu [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) :</span><span class="sxs-lookup"><span data-stu-id="eb56e-292">Add the [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="eb56e-293">Volání `SeedData.Initialize` z `Main`:</span><span class="sxs-lookup"><span data-stu-id="eb56e-293">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="eb56e-294">Otestujte, že aplikace naplnila databázi.</span><span class="sxs-lookup"><span data-stu-id="eb56e-294">Test that the app seeded the database.</span></span> <span data-ttu-id="eb56e-295">Pokud existují nějaké řádky v kontaktu DB, metoda počáteční hodnoty není spuštěna.</span><span class="sxs-lookup"><span data-stu-id="eb56e-295">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="eb56e-296">Tento kurz ukazuje, jak vytvořit webovou aplikaci ASP.NET Core s uživatelskými daty chráněnými autorizací.</span><span class="sxs-lookup"><span data-stu-id="eb56e-296">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="eb56e-297">Zobrazí seznam kontakty, které ověřeným uživatelům (registrovaných) jste vytvořili.</span><span class="sxs-lookup"><span data-stu-id="eb56e-297">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="eb56e-298">Existují tři skupiny zabezpečení:</span><span class="sxs-lookup"><span data-stu-id="eb56e-298">There are three security groups:</span></span>

* <span data-ttu-id="eb56e-299">**Zaregistrované uživatele** můžete zobrazit všechny schválené data a můžete upravit nebo odstranit svá vlastní data.</span><span class="sxs-lookup"><span data-stu-id="eb56e-299">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="eb56e-300">**Správci** můžete schválit nebo odmítnout kontaktní údaje.</span><span class="sxs-lookup"><span data-stu-id="eb56e-300">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="eb56e-301">Uživatelé vidí pouze schválené kontakty.</span><span class="sxs-lookup"><span data-stu-id="eb56e-301">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="eb56e-302">**Správci** můžete schvalovat a odmítat a upravit nebo odstranit všechna data.</span><span class="sxs-lookup"><span data-stu-id="eb56e-302">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="eb56e-303">Na následujícím obrázku, uživatel Rick (`rick@example.com`) je přihlášený.</span><span class="sxs-lookup"><span data-stu-id="eb56e-303">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="eb56e-304">Rick může zobrazit jenom schválené kontakty a **upravit**/**odstranit**/**vytvořit nový** odkazy pro jeho kontakty.</span><span class="sxs-lookup"><span data-stu-id="eb56e-304">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="eb56e-305">Pouze poslední záznam vytvořil Rick, zobrazí **upravit** a **odstranit** odkazy.</span><span class="sxs-lookup"><span data-stu-id="eb56e-305">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="eb56e-306">Ostatní uživatelé neuvidí poslední záznam, dokud správce nebo správce změní stav na "Schváleno".</span><span class="sxs-lookup"><span data-stu-id="eb56e-306">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Snímek obrazovky zobrazující Rick přihlášení](secure-data/_static/rick.png)

<span data-ttu-id="eb56e-308">Na následujícím obrázku `manager@contoso.com` je přihlášen a v roli manažera:</span><span class="sxs-lookup"><span data-stu-id="eb56e-308">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Snímek obrazovky zobrazující manager@contoso.com přihlášení](secure-data/_static/manager1.png)

<span data-ttu-id="eb56e-310">Následující obrázek ukazuje vedoucí zobrazení podrobností o kontaktu:</span><span class="sxs-lookup"><span data-stu-id="eb56e-310">The following image shows the managers details view of a contact:</span></span>

![Zobrazení manažera kontaktu](secure-data/_static/manager.png)

<span data-ttu-id="eb56e-312">**Schválit** a **odmítnout** tlačítek se zobrazí pouze správci a správci.</span><span class="sxs-lookup"><span data-stu-id="eb56e-312">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="eb56e-313">Na následujícím obrázku `admin@contoso.com` je přihlášen a v roli správce:</span><span class="sxs-lookup"><span data-stu-id="eb56e-313">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Snímek obrazovky zobrazující admin@contoso.com přihlášení](secure-data/_static/admin.png)

<span data-ttu-id="eb56e-315">Správce má všechna oprávnění.</span><span class="sxs-lookup"><span data-stu-id="eb56e-315">The administrator has all privileges.</span></span> <span data-ttu-id="eb56e-316">Může číst/upravovat/odstraňovat všechny kontakty a změnit stav kontakty.</span><span class="sxs-lookup"><span data-stu-id="eb56e-316">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="eb56e-317">Aplikace byla vytvořena pomocí [generování uživatelského rozhraní](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) následující `Contact` modelu:</span><span class="sxs-lookup"><span data-stu-id="eb56e-317">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="eb56e-318">Ukázka obsahuje následující rutiny autorizace:</span><span class="sxs-lookup"><span data-stu-id="eb56e-318">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="eb56e-319">`ContactIsOwnerAuthorizationHandler`: Zajišťuje, aby uživatel mohl upravovat pouze svá data.</span><span class="sxs-lookup"><span data-stu-id="eb56e-319">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="eb56e-320">`ContactManagerAuthorizationHandler`: Umožňuje správcům schvalovat nebo odmítat kontakty.</span><span class="sxs-lookup"><span data-stu-id="eb56e-320">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="eb56e-321">`ContactAdministratorsAuthorizationHandler`: Umožňuje správcům schvalovat nebo odmítat kontakty a upravovat nebo odstraňovat kontakty.</span><span class="sxs-lookup"><span data-stu-id="eb56e-321">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="eb56e-322">Požadavky</span><span class="sxs-lookup"><span data-stu-id="eb56e-322">Prerequisites</span></span>

<span data-ttu-id="eb56e-323">V tomto kurzu je advanced.</span><span class="sxs-lookup"><span data-stu-id="eb56e-323">This tutorial is advanced.</span></span> <span data-ttu-id="eb56e-324">Měli byste se seznámit s:</span><span class="sxs-lookup"><span data-stu-id="eb56e-324">You should be familiar with:</span></span>

* [<span data-ttu-id="eb56e-325">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eb56e-325">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="eb56e-326">Ověřování</span><span class="sxs-lookup"><span data-stu-id="eb56e-326">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="eb56e-327">Potvrzení účtu a obnovení hesla</span><span class="sxs-lookup"><span data-stu-id="eb56e-327">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="eb56e-328">Autorizace</span><span class="sxs-lookup"><span data-stu-id="eb56e-328">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="eb56e-329">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="eb56e-329">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="eb56e-330">Starter a dokončené aplikace</span><span class="sxs-lookup"><span data-stu-id="eb56e-330">The starter and completed app</span></span>

<span data-ttu-id="eb56e-331">[Stáhněte si](xref:index#how-to-download-a-sample) [Dokončit](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) aplikace.</span><span class="sxs-lookup"><span data-stu-id="eb56e-331">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="eb56e-332">[Test](#test-the-completed-app) dokončené aplikace tak, že jste se seznámili s jeho funkcí zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="eb56e-332">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="eb56e-333">Úvodní aplikaci</span><span class="sxs-lookup"><span data-stu-id="eb56e-333">The starter app</span></span>

<span data-ttu-id="eb56e-334">[Stáhněte si](xref:index#how-to-download-a-sample) [starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) aplikace.</span><span class="sxs-lookup"><span data-stu-id="eb56e-334">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="eb56e-335">Spusťte aplikaci, klepněte **ContactManager** propojit a ověření můžete vytvářet, upravovat a odstraňovat kontakt.</span><span class="sxs-lookup"><span data-stu-id="eb56e-335">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="eb56e-336">Zabezpečení dat uživatele</span><span class="sxs-lookup"><span data-stu-id="eb56e-336">Secure user data</span></span>

<span data-ttu-id="eb56e-337">Následující oddíly mají všechny hlavní kroky k vytvoření aplikace pro data zabezpečení uživatele.</span><span class="sxs-lookup"><span data-stu-id="eb56e-337">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="eb56e-338">Vám může být užitečné k odkazování na dokončení projektu.</span><span class="sxs-lookup"><span data-stu-id="eb56e-338">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="eb56e-339">Tie kontaktní údaje pro uživatele</span><span class="sxs-lookup"><span data-stu-id="eb56e-339">Tie the contact data to the user</span></span>

<span data-ttu-id="eb56e-340">Použití technologie ASP.NET [Identity](xref:security/authentication/identity) ID uživatele, aby tak uživatelé můžete upravit jejich data, ale ne data jiných uživatelů.</span><span class="sxs-lookup"><span data-stu-id="eb56e-340">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="eb56e-341">Přidat `OwnerID` a `ContactStatus` k `Contact` modelu:</span><span class="sxs-lookup"><span data-stu-id="eb56e-341">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="eb56e-342">`OwnerID` je ID uživatele z `AspNetUser` v tabulku [Identity](xref:security/authentication/identity) databáze.</span><span class="sxs-lookup"><span data-stu-id="eb56e-342">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="eb56e-343">`Status` Pole určuje, zda je kontakt zobrazitelné obecný uživatel.</span><span class="sxs-lookup"><span data-stu-id="eb56e-343">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="eb56e-344">Vytvořte novou migraci a aktualizaci databáze:</span><span class="sxs-lookup"><span data-stu-id="eb56e-344">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="eb56e-345">Přidání služby Role na identitu</span><span class="sxs-lookup"><span data-stu-id="eb56e-345">Add Role services to Identity</span></span>

<span data-ttu-id="eb56e-346">Připojit [kliknutím na Přidat role](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) přidat služby rolí:</span><span class="sxs-lookup"><span data-stu-id="eb56e-346">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a><span data-ttu-id="eb56e-347">Vyžadovat ověření uživatelé</span><span class="sxs-lookup"><span data-stu-id="eb56e-347">Require authenticated users</span></span>

<span data-ttu-id="eb56e-348">Nastavte výchozí zásady ověřování tak, aby vyžadovala ověření uživatelů:</span><span class="sxs-lookup"><span data-stu-id="eb56e-348">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="eb56e-349">Můžete zrušit ověřování na úrovni metody stránky Razor, kontroler nebo akce s `[AllowAnonymous]` atribut.</span><span class="sxs-lookup"><span data-stu-id="eb56e-349">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="eb56e-350">Nastavení výchozí zásady ověřování tak, aby vyžadovala ověření uživatelů chrání nově přidané Razor Pages a kontrolery.</span><span class="sxs-lookup"><span data-stu-id="eb56e-350">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="eb56e-351">Ve výchozím nastavení je vyžadováno ověření je bezpečnější než spoléhání se na nové řadiče a Razor Pages zahrnout s `[Authorize]` atribut.</span><span class="sxs-lookup"><span data-stu-id="eb56e-351">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="eb56e-352">Přidat [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) index stránky o a kontaktní anonymní uživatelé získali informace o webu předtím, než aby se zaregistrovali.</span><span class="sxs-lookup"><span data-stu-id="eb56e-352">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="eb56e-353">Konfigurace testovací účet</span><span class="sxs-lookup"><span data-stu-id="eb56e-353">Configure the test account</span></span>

<span data-ttu-id="eb56e-354">`SeedData` Třída vytvoří dva účty: správce a správce.</span><span class="sxs-lookup"><span data-stu-id="eb56e-354">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="eb56e-355">Použití [nástroj tajný klíč správce](xref:security/app-secrets) nastavení hesla pro tyto účty.</span><span class="sxs-lookup"><span data-stu-id="eb56e-355">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="eb56e-356">Nastavte heslo z adresáře projektu (adresáře, který obsahuje *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="eb56e-356">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="eb56e-357">Pokud není zadán silné heslo, je vyvolána výjimka, když `SeedData.Initialize` je volána.</span><span class="sxs-lookup"><span data-stu-id="eb56e-357">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="eb56e-358">Aktualizace `Main` test heslo budete používat:</span><span class="sxs-lookup"><span data-stu-id="eb56e-358">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="eb56e-359">Vytvoření testovacích účtů a aktualizovat kontakty</span><span class="sxs-lookup"><span data-stu-id="eb56e-359">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="eb56e-360">Aktualizace `Initialize` metodu `SeedData` třídy za účelem vytvoření testovacích účtů:</span><span class="sxs-lookup"><span data-stu-id="eb56e-360">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="eb56e-361">Přidat správce ID uživatele a `ContactStatus` kontaktům.</span><span class="sxs-lookup"><span data-stu-id="eb56e-361">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="eb56e-362">Proveďte jednu z kontakty "Submitted" a jedné "byl odmítnut".</span><span class="sxs-lookup"><span data-stu-id="eb56e-362">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="eb56e-363">Přidáte ID uživatele a stav pro všechny kontakty.</span><span class="sxs-lookup"><span data-stu-id="eb56e-363">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="eb56e-364">Je zobrazena pouze jeden kontakt:</span><span class="sxs-lookup"><span data-stu-id="eb56e-364">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="eb56e-365">Vytvořit vlastníka, správce a Správce autorizace obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="eb56e-365">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="eb56e-366">Vytvořte složku *autorizace* a v ní vytvořte `ContactIsOwnerAuthorizationHandler` třídu.</span><span class="sxs-lookup"><span data-stu-id="eb56e-366">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="eb56e-367">`ContactIsOwnerAuthorizationHandler` Ověřuje, že uživatel na prostředek vlastníkem prostředku.</span><span class="sxs-lookup"><span data-stu-id="eb56e-367">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="eb56e-368">`ContactIsOwnerAuthorizationHandler` Volání [kontextu. Úspěšné](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) aktuálně ověřeného uživatele při jeho vlastníkem.</span><span class="sxs-lookup"><span data-stu-id="eb56e-368">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="eb56e-369">Obslužné rutiny autorizace obecně:</span><span class="sxs-lookup"><span data-stu-id="eb56e-369">Authorization handlers generally:</span></span>

* <span data-ttu-id="eb56e-370">Vrátí `context.Succeed` Pokud jsou splněny.</span><span class="sxs-lookup"><span data-stu-id="eb56e-370">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="eb56e-371">Vrátí `Task.CompletedTask` Pokud požadavky nejsou splněny.</span><span class="sxs-lookup"><span data-stu-id="eb56e-371">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="eb56e-372">`Task.CompletedTask`není úspěch nebo neúspěch&mdash;, umožňuje spuštění dalších obslužných rutin autorizace.</span><span class="sxs-lookup"><span data-stu-id="eb56e-372">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="eb56e-373">Pokud je potřeba explicitně nezdaří, vrátí [kontextu. Selhání](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="eb56e-373">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="eb56e-374">Aplikaci umožňuje kontaktní vlastníkům upravit, odstranit a vytvořit svoje vlastní data.</span><span class="sxs-lookup"><span data-stu-id="eb56e-374">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="eb56e-375">`ContactIsOwnerAuthorizationHandler` není nutné ke kontrole operace předané v parametru požadavku.</span><span class="sxs-lookup"><span data-stu-id="eb56e-375">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="eb56e-376">Vytvořte obslužnou rutinu Správce autorizací</span><span class="sxs-lookup"><span data-stu-id="eb56e-376">Create a manager authorization handler</span></span>

<span data-ttu-id="eb56e-377">Vytvoření `ContactManagerAuthorizationHandler` třídy v *autorizace* složky.</span><span class="sxs-lookup"><span data-stu-id="eb56e-377">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="eb56e-378">`ContactManagerAuthorizationHandler` Ověřuje uživatele na prostředek je správce.</span><span class="sxs-lookup"><span data-stu-id="eb56e-378">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="eb56e-379">Pouze vedoucí mohli schválit nebo odmítnout změny obsahu (nové nebo změněné).</span><span class="sxs-lookup"><span data-stu-id="eb56e-379">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="eb56e-380">Vytvořte obslužnou rutinu Správce autorizací</span><span class="sxs-lookup"><span data-stu-id="eb56e-380">Create an administrator authorization handler</span></span>

<span data-ttu-id="eb56e-381">Vytvoření `ContactAdministratorsAuthorizationHandler` třídy v *autorizace* složky.</span><span class="sxs-lookup"><span data-stu-id="eb56e-381">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="eb56e-382">`ContactAdministratorsAuthorizationHandler` Ověřuje uživatele na prostředek je správce.</span><span class="sxs-lookup"><span data-stu-id="eb56e-382">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="eb56e-383">Správce může provádět všechny operace.</span><span class="sxs-lookup"><span data-stu-id="eb56e-383">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="eb56e-384">Zaregistrujte obslužné rutiny autorizace</span><span class="sxs-lookup"><span data-stu-id="eb56e-384">Register the authorization handlers</span></span>

<span data-ttu-id="eb56e-385">Pomocí Entity Framework Core Services musí být zaregistrovaný pro [injektáž závislostí](xref:fundamentals/dependency-injection) pomocí [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="eb56e-385">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="eb56e-386">`ContactIsOwnerAuthorizationHandler` Používá ASP.NET Core [Identity](xref:security/authentication/identity), která je založená na Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="eb56e-386">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="eb56e-387">Obslužné rutiny zaregistrovat u kolekce služby jsou k dispozici na `ContactsController` prostřednictvím [injektáž závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="eb56e-387">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="eb56e-388">Přidejte následující kód do konce `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="eb56e-388">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="eb56e-389">`ContactAdministratorsAuthorizationHandler` a `ContactManagerAuthorizationHandler` jsou přidány jako jednotlivé prvky.</span><span class="sxs-lookup"><span data-stu-id="eb56e-389">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="eb56e-390">Jednotlivé prvky jsou vzhledem k tomu, že se nepoužívají EF a všechny informace potřebné musí být `Context` parametr `HandleRequirementAsync` metoda.</span><span class="sxs-lookup"><span data-stu-id="eb56e-390">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="eb56e-391">Povolení podpory</span><span class="sxs-lookup"><span data-stu-id="eb56e-391">Support authorization</span></span>

<span data-ttu-id="eb56e-392">V této části se aktualizace stránky Razor a přidejte třídu požadavků operace.</span><span class="sxs-lookup"><span data-stu-id="eb56e-392">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="eb56e-393">Zkontrolujte požadavky na třídy kontaktní operace</span><span class="sxs-lookup"><span data-stu-id="eb56e-393">Review the contact operations requirements class</span></span>

<span data-ttu-id="eb56e-394">Zkontrolujte `ContactOperations` třídy.</span><span class="sxs-lookup"><span data-stu-id="eb56e-394">Review the `ContactOperations` class.</span></span> <span data-ttu-id="eb56e-395">Tato třída obsahuje požadavky na aplikace podporuje:</span><span class="sxs-lookup"><span data-stu-id="eb56e-395">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="eb56e-396">Vytvoření základní třídu pro stránky Razor kontakty</span><span class="sxs-lookup"><span data-stu-id="eb56e-396">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="eb56e-397">Vytvořte základní třídu, která obsahuje služby využité v kontaktech stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="eb56e-397">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="eb56e-398">Základní třída vloží kód inicializace na jednom místě:</span><span class="sxs-lookup"><span data-stu-id="eb56e-398">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="eb56e-399">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="eb56e-399">The preceding code:</span></span>

* <span data-ttu-id="eb56e-400">Přidá `IAuthorizationService` služby pro přístup k rutinám autorizace.</span><span class="sxs-lookup"><span data-stu-id="eb56e-400">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="eb56e-401">Přidá identitu `UserManager` služby.</span><span class="sxs-lookup"><span data-stu-id="eb56e-401">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="eb56e-402">Přidat `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="eb56e-402">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="eb56e-403">Aktualizace CreateModel</span><span class="sxs-lookup"><span data-stu-id="eb56e-403">Update the CreateModel</span></span>

<span data-ttu-id="eb56e-404">Aktualizovat konstruktoru vytvořit stránku model použití `DI_BasePageModel` základní třídy:</span><span class="sxs-lookup"><span data-stu-id="eb56e-404">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="eb56e-405">Aktualizace `CreateModel.OnPostAsync` metodu:</span><span class="sxs-lookup"><span data-stu-id="eb56e-405">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="eb56e-406">Přidat ID uživatele, `Contact` modelu.</span><span class="sxs-lookup"><span data-stu-id="eb56e-406">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="eb56e-407">Ověřte, zda že má uživatel oprávnění k vytvoření kontakty obslužná rutina ověřování volejte.</span><span class="sxs-lookup"><span data-stu-id="eb56e-407">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="eb56e-408">Aktualizace IndexModel</span><span class="sxs-lookup"><span data-stu-id="eb56e-408">Update the IndexModel</span></span>

<span data-ttu-id="eb56e-409">Aktualizace `OnGetAsync` metody, jsou uvedeny pouze schválené kontakty obecné uživatelům:</span><span class="sxs-lookup"><span data-stu-id="eb56e-409">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="eb56e-410">Aktualizace EditModel</span><span class="sxs-lookup"><span data-stu-id="eb56e-410">Update the EditModel</span></span>

<span data-ttu-id="eb56e-411">Přidejte obslužnou rutinu ověřování k ověření, že uživatel je vlastníkem kontaktu.</span><span class="sxs-lookup"><span data-stu-id="eb56e-411">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="eb56e-412">Protože ra se ověřuje, `[Authorize]` atribut není dostatek.</span><span class="sxs-lookup"><span data-stu-id="eb56e-412">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="eb56e-413">Aplikace nemá přístup k prostředku, když jsou vyhodnocovány atributy.</span><span class="sxs-lookup"><span data-stu-id="eb56e-413">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="eb56e-414">Autorizace na základě prostředků musí být nezbytné.</span><span class="sxs-lookup"><span data-stu-id="eb56e-414">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="eb56e-415">Kontroly musí provést, když má aplikace přístup k prostředku načtením v modelu stránky nebo načtením v rámci samotná obslužná rutina.</span><span class="sxs-lookup"><span data-stu-id="eb56e-415">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="eb56e-416">Často přistupovat k prostředku předáním klíč prostředku.</span><span class="sxs-lookup"><span data-stu-id="eb56e-416">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="eb56e-417">Aktualizace DeleteModel</span><span class="sxs-lookup"><span data-stu-id="eb56e-417">Update the DeleteModel</span></span>

<span data-ttu-id="eb56e-418">Aktualizace modelu stránku odstranit sloužící k ověření, že uživatel má oprávnění odstranit u kontaktu obslužnou rutinu ověřování.</span><span class="sxs-lookup"><span data-stu-id="eb56e-418">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="eb56e-419">Vložit do zobrazení autorizační službu</span><span class="sxs-lookup"><span data-stu-id="eb56e-419">Inject the authorization service into the views</span></span>

<span data-ttu-id="eb56e-420">V současné době ukazuje uživatelského rozhraní upravovat a odstraňovat kontakty, které uživatel nemůže upravovat odkazy.</span><span class="sxs-lookup"><span data-stu-id="eb56e-420">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="eb56e-421">Vloží autorizační službu v *Views/_ViewImports.cshtml* souboru tak, aby byl k dispozici pro všechna zobrazení:</span><span class="sxs-lookup"><span data-stu-id="eb56e-421">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="eb56e-422">Předchozí kód přidá několik `using` příkazy.</span><span class="sxs-lookup"><span data-stu-id="eb56e-422">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="eb56e-423">Aktualizace **upravit** a **odstranit** odkazů v *Pages/Contacts/Index.cshtml* tak, že pouze vykreslen pro uživatele s příslušnými oprávněními:</span><span class="sxs-lookup"><span data-stu-id="eb56e-423">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="eb56e-424">Skrytí propojení od uživatele, kteří nemají oprávnění ke změně dat nebude zabezpečení aplikace.</span><span class="sxs-lookup"><span data-stu-id="eb56e-424">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="eb56e-425">Skrytí propojení nastaví aplikaci jako přívětivější tím, že zobrazuje pouze platné odkazy.</span><span class="sxs-lookup"><span data-stu-id="eb56e-425">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="eb56e-426">Uživatelé můžou hack generované adres URL pro vyvolání funkce upravit a odstranit operací s daty, které není vlastní.</span><span class="sxs-lookup"><span data-stu-id="eb56e-426">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="eb56e-427">Stránka Razor nebo kontroleru musí vynucovat kontroly přístupu pro data zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="eb56e-427">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="eb56e-428">Podrobné informace o aktualizaci</span><span class="sxs-lookup"><span data-stu-id="eb56e-428">Update Details</span></span>

<span data-ttu-id="eb56e-429">Aktualizujte zobrazení podrobností, aby vedoucí mohli schválit nebo odmítnout kontaktů:</span><span class="sxs-lookup"><span data-stu-id="eb56e-429">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="eb56e-430">Model stránky podrobnosti aktualizace:</span><span class="sxs-lookup"><span data-stu-id="eb56e-430">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="eb56e-431">Přidat nebo odebrat uživatele k roli</span><span class="sxs-lookup"><span data-stu-id="eb56e-431">Add or remove a user to a role</span></span>

<span data-ttu-id="eb56e-432">Zobrazit [tento problém](https://github.com/aspnet/AspNetCore.Docs/issues/8502) informace na:</span><span class="sxs-lookup"><span data-stu-id="eb56e-432">See [this issue](https://github.com/aspnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="eb56e-433">Odebrání oprávnění uživatele.</span><span class="sxs-lookup"><span data-stu-id="eb56e-433">Removing privileges from a user.</span></span> <span data-ttu-id="eb56e-434">Například ztlumení uživatele v aplikaci chatu.</span><span class="sxs-lookup"><span data-stu-id="eb56e-434">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="eb56e-435">Přidání oprávnění pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="eb56e-435">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="eb56e-436">Testování dokončené aplikace</span><span class="sxs-lookup"><span data-stu-id="eb56e-436">Test the completed app</span></span>

<span data-ttu-id="eb56e-437">Pokud jste dosud nenastavili hesla pro dosazené uživatelské účty, použijte [nástroj tajný klíč správce](xref:security/app-secrets#secret-manager) nastavit heslo:</span><span class="sxs-lookup"><span data-stu-id="eb56e-437">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="eb56e-438">Vyberte silné heslo: Použijte osm nebo více znaků a alespoň jedno velké písmeno, číslici a symbol.</span><span class="sxs-lookup"><span data-stu-id="eb56e-438">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="eb56e-439">Například `Passw0rd!` splňuje požadavky na silné heslo.</span><span class="sxs-lookup"><span data-stu-id="eb56e-439">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="eb56e-440">Spusťte následující příkaz ze složky projektu, kde `<PW>` je heslo:</span><span class="sxs-lookup"><span data-stu-id="eb56e-440">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="eb56e-441">Zrušení a aktualizace databáze</span><span class="sxs-lookup"><span data-stu-id="eb56e-441">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="eb56e-442">Restartujte aplikaci k přidání dat do databáze.</span><span class="sxs-lookup"><span data-stu-id="eb56e-442">Restart the app to seed the database.</span></span>

<span data-ttu-id="eb56e-443">Snadný způsob, jak otestovat dokončená aplikace je spustíte tři různé prohlížeče (nebo incognito/InPrivate relace).</span><span class="sxs-lookup"><span data-stu-id="eb56e-443">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="eb56e-444">V jeden prohlížeč, registraci nového uživatele (například `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="eb56e-444">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="eb56e-445">Přihlaste se k každým prohlížečem s jiným uživatelem.</span><span class="sxs-lookup"><span data-stu-id="eb56e-445">Sign in to each browser with a different user.</span></span> <span data-ttu-id="eb56e-446">Ověřte následující operace:</span><span class="sxs-lookup"><span data-stu-id="eb56e-446">Verify the following operations:</span></span>

* <span data-ttu-id="eb56e-447">Registrovaných uživatelů můžete zobrazit všechny schválené kontaktní údaje.</span><span class="sxs-lookup"><span data-stu-id="eb56e-447">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="eb56e-448">Registrovaných uživatelů můžete upravit nebo odstranit svá vlastní data.</span><span class="sxs-lookup"><span data-stu-id="eb56e-448">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="eb56e-449">Správci mohou schvalovat a odmítat kontaktní údaje.</span><span class="sxs-lookup"><span data-stu-id="eb56e-449">Managers can approve/reject contact data.</span></span> <span data-ttu-id="eb56e-450">`Details` Zobrazení ukazuje **schválit** a **odmítnout** tlačítka.</span><span class="sxs-lookup"><span data-stu-id="eb56e-450">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="eb56e-451">Správci můžou schvalovat a odmítat a upravit nebo odstranit všechna data.</span><span class="sxs-lookup"><span data-stu-id="eb56e-451">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="eb56e-452">Uživatel</span><span class="sxs-lookup"><span data-stu-id="eb56e-452">User</span></span>                | <span data-ttu-id="eb56e-453">Nasazených aplikací</span><span class="sxs-lookup"><span data-stu-id="eb56e-453">Seeded by the app</span></span> | <span data-ttu-id="eb56e-454">Možnosti</span><span class="sxs-lookup"><span data-stu-id="eb56e-454">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="eb56e-455">Ne</span><span class="sxs-lookup"><span data-stu-id="eb56e-455">No</span></span>                | <span data-ttu-id="eb56e-456">Upravit nebo odstranit vlastní data.</span><span class="sxs-lookup"><span data-stu-id="eb56e-456">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="eb56e-457">Ano</span><span class="sxs-lookup"><span data-stu-id="eb56e-457">Yes</span></span>               | <span data-ttu-id="eb56e-458">Vlastní data, schvalovat a odmítat a upravit nebo odstranit.</span><span class="sxs-lookup"><span data-stu-id="eb56e-458">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="eb56e-459">Ano</span><span class="sxs-lookup"><span data-stu-id="eb56e-459">Yes</span></span>               | <span data-ttu-id="eb56e-460">Schválit nebo odmítnout a upravit nebo odstranit všechna data.</span><span class="sxs-lookup"><span data-stu-id="eb56e-460">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="eb56e-461">Vytvoření kontaktu v prohlížeči na správce.</span><span class="sxs-lookup"><span data-stu-id="eb56e-461">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="eb56e-462">Zkopírujte adresu URL pro odstranění a upravit z kontaktujte správce.</span><span class="sxs-lookup"><span data-stu-id="eb56e-462">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="eb56e-463">Vložte tyto odkazy do testů webového prohlížeče k ověření, že testovací uživatel nemůže provádět tyto operace.</span><span class="sxs-lookup"><span data-stu-id="eb56e-463">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="eb56e-464">Vytvořit úvodní aplikaci</span><span class="sxs-lookup"><span data-stu-id="eb56e-464">Create the starter app</span></span>

* <span data-ttu-id="eb56e-465">Vytvoření aplikace stránky Razor s názvem "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="eb56e-465">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="eb56e-466">Vytvoření aplikace s **jednotlivých uživatelských účtů**.</span><span class="sxs-lookup"><span data-stu-id="eb56e-466">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="eb56e-467">Pojmenujte ji "ContactManager" tak obor názvů odpovídá oboru názvů použitého v ukázce.</span><span class="sxs-lookup"><span data-stu-id="eb56e-467">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="eb56e-468">`-uld` Určuje LocalDB místo SQLite</span><span class="sxs-lookup"><span data-stu-id="eb56e-468">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="eb56e-469">Přidat *modely/kontakt. cs*:</span><span class="sxs-lookup"><span data-stu-id="eb56e-469">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="eb56e-470">Vygenerované uživatelské rozhraní `Contact` modelu.</span><span class="sxs-lookup"><span data-stu-id="eb56e-470">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="eb56e-471">Vytvořte počáteční migraci a aktualizaci databáze:</span><span class="sxs-lookup"><span data-stu-id="eb56e-471">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="eb56e-472">Aktualizace **ContactManager** ukotvit v *Pages/_Layout.cshtml* souboru:</span><span class="sxs-lookup"><span data-stu-id="eb56e-472">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="eb56e-473">Testování aplikace pomocí vytváření, úpravy a odstranění kontaktu</span><span class="sxs-lookup"><span data-stu-id="eb56e-473">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="eb56e-474">Přidání dat do databáze</span><span class="sxs-lookup"><span data-stu-id="eb56e-474">Seed the database</span></span>

<span data-ttu-id="eb56e-475">Přidat [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) třídu *Data* složky.</span><span class="sxs-lookup"><span data-stu-id="eb56e-475">Add the [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="eb56e-476">Volání `SeedData.Initialize` z `Main`:</span><span class="sxs-lookup"><span data-stu-id="eb56e-476">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="eb56e-477">Otestujte, že aplikace naplnila databázi.</span><span class="sxs-lookup"><span data-stu-id="eb56e-477">Test that the app seeded the database.</span></span> <span data-ttu-id="eb56e-478">Pokud existují nějaké řádky v kontaktu DB, metoda počáteční hodnoty není spuštěna.</span><span class="sxs-lookup"><span data-stu-id="eb56e-478">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="eb56e-479">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="eb56e-479">Additional resources</span></span>

* [<span data-ttu-id="eb56e-480">Vytvoření webové aplikace .NET Core využívající SQL Database ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="eb56e-480">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="eb56e-481">[ASP.NET Core povolení Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="eb56e-481">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="eb56e-482">Toto testovací prostředí obsahuje větší podrobnosti o funkcích zabezpečení v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="eb56e-482">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="eb56e-483">Autorizace na základě zásad</span><span class="sxs-lookup"><span data-stu-id="eb56e-483">Custom policy-based authorization</span></span>](xref:security/authorization/policies)

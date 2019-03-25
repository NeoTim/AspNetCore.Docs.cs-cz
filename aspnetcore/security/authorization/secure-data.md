---
title: Vytvoření aplikace ASP.NET Core s uživatelskými daty chráněnými autorizací
author: rick-anderson
description: Zjistěte, jak vytvořit aplikace Razor Pages s uživatelskými daty chráněnými autorizací. Zahrnuje HTTPS, ověřování, zabezpečení ASP.NET Core Identity.
ms.author: riande
ms.date: 12/18/2018
ms.custom: seodec18
uid: security/authorization/secure-data
ms.openlocfilehash: 748de9264a1b22b50e0832fdd350e06eeefd5cb3
ms.sourcegitcommit: 7d6019f762fc5b8cbedcd69801e8310f51a17c18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2019
ms.locfileid: "58419456"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="129fb-104">Vytvoření aplikace ASP.NET Core s uživatelskými daty chráněnými autorizací</span><span class="sxs-lookup"><span data-stu-id="129fb-104">Create an ASP.NET Core app with user data protected by authorization</span></span>

<span data-ttu-id="129fb-105">Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="129fb-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="129fb-106">Zobrazit [tento PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) pro verzi technologie ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="129fb-106">See [this PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core MVC version.</span></span> <span data-ttu-id="129fb-107">ASP.NET Core 1.1 verzi tohoto kurzu je v [to](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data) složky.</span><span class="sxs-lookup"><span data-stu-id="129fb-107">The ASP.NET Core 1.1 version of this tutorial is in [this](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data) folder.</span></span> <span data-ttu-id="129fb-108">1.1 ukázka ASP.NET Core je v [ukázky](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span><span class="sxs-lookup"><span data-stu-id="129fb-108">The 1.1 ASP.NET Core sample is in the [samples](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="129fb-109">Zobrazit [tento pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="129fb-109">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="129fb-110">Tento kurz ukazuje, jak vytvořit webovou aplikaci ASP.NET Core s uživatelskými daty chráněnými autorizací.</span><span class="sxs-lookup"><span data-stu-id="129fb-110">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="129fb-111">Zobrazí seznam kontakty, které ověřeným uživatelům (registrovaných) jste vytvořili.</span><span class="sxs-lookup"><span data-stu-id="129fb-111">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="129fb-112">Existují tři skupiny zabezpečení:</span><span class="sxs-lookup"><span data-stu-id="129fb-112">There are three security groups:</span></span>

* <span data-ttu-id="129fb-113">**Zaregistrované uživatele** můžete zobrazit všechny schválené data a můžete upravit nebo odstranit svá vlastní data.</span><span class="sxs-lookup"><span data-stu-id="129fb-113">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="129fb-114">**Správci** můžete schválit nebo odmítnout kontaktní údaje.</span><span class="sxs-lookup"><span data-stu-id="129fb-114">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="129fb-115">Uživatelé vidí pouze schválené kontakty.</span><span class="sxs-lookup"><span data-stu-id="129fb-115">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="129fb-116">**Správci** můžete schvalovat a odmítat a upravit nebo odstranit všechna data.</span><span class="sxs-lookup"><span data-stu-id="129fb-116">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="129fb-117">Na následujícím obrázku, uživatel Rick (`rick@example.com`) je přihlášený.</span><span class="sxs-lookup"><span data-stu-id="129fb-117">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="129fb-118">Rick může zobrazit jenom schválené kontakty a **upravit**/**odstranit**/**vytvořit nový** odkazy pro jeho kontakty.</span><span class="sxs-lookup"><span data-stu-id="129fb-118">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="129fb-119">Pouze poslední záznam vytvořil Rick, zobrazí **upravit** a **odstranit** odkazy.</span><span class="sxs-lookup"><span data-stu-id="129fb-119">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="129fb-120">Ostatní uživatelé neuvidí poslední záznam, dokud správce nebo správce změní stav na "Schváleno".</span><span class="sxs-lookup"><span data-stu-id="129fb-120">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Snímek obrazovky zobrazující Rick přihlášení](secure-data/_static/rick.png)

<span data-ttu-id="129fb-122">Na následujícím obrázku `manager@contoso.com` je podepsán v a v roli správce:</span><span class="sxs-lookup"><span data-stu-id="129fb-122">In the following image, `manager@contoso.com` is signed in and in the managers role:</span></span>

![Snímek obrazovky zobrazující manager@contoso.com přihlášení](secure-data/_static/manager1.png)

<span data-ttu-id="129fb-124">Následující obrázek ukazuje vedoucí zobrazení podrobností o kontaktu:</span><span class="sxs-lookup"><span data-stu-id="129fb-124">The following image shows the managers details view of a contact:</span></span>

![Zobrazení manažera kontaktu](secure-data/_static/manager.png)

<span data-ttu-id="129fb-126">**Schválit** a **odmítnout** tlačítek se zobrazí pouze správci a správci.</span><span class="sxs-lookup"><span data-stu-id="129fb-126">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="129fb-127">Na následujícím obrázku `admin@contoso.com` je podepsán v a v roli správce:</span><span class="sxs-lookup"><span data-stu-id="129fb-127">In the following image, `admin@contoso.com` is signed in and in the administrators role:</span></span>

![Snímek obrazovky zobrazující admin@contoso.com přihlášení](secure-data/_static/admin.png)

<span data-ttu-id="129fb-129">Správce má všechna oprávnění.</span><span class="sxs-lookup"><span data-stu-id="129fb-129">The administrator has all privileges.</span></span> <span data-ttu-id="129fb-130">Může číst/upravovat/odstraňovat všechny kontakty a změnit stav kontakty.</span><span class="sxs-lookup"><span data-stu-id="129fb-130">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="129fb-131">Aplikace byla vytvořena pomocí [generování uživatelského rozhraní](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) následující `Contact` modelu:</span><span class="sxs-lookup"><span data-stu-id="129fb-131">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="129fb-132">Ukázka obsahuje následující rutiny autorizace:</span><span class="sxs-lookup"><span data-stu-id="129fb-132">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="129fb-133">`ContactIsOwnerAuthorizationHandler`: Zajišťuje, že uživatel může upravovat jenom svá data.</span><span class="sxs-lookup"><span data-stu-id="129fb-133">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="129fb-134">`ContactManagerAuthorizationHandler`: Umožňuje správcům schválit nebo odmítnout kontakty.</span><span class="sxs-lookup"><span data-stu-id="129fb-134">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="129fb-135">`ContactAdministratorsAuthorizationHandler`: Umožňuje správcům schválit nebo odmítnout kontakty a úpravy nebo odstranění kontaktů.</span><span class="sxs-lookup"><span data-stu-id="129fb-135">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="129fb-136">Požadavky</span><span class="sxs-lookup"><span data-stu-id="129fb-136">Prerequisites</span></span>

<span data-ttu-id="129fb-137">V tomto kurzu je advanced.</span><span class="sxs-lookup"><span data-stu-id="129fb-137">This tutorial is advanced.</span></span> <span data-ttu-id="129fb-138">Měli byste se seznámit s:</span><span class="sxs-lookup"><span data-stu-id="129fb-138">You should be familiar with:</span></span>

* [<span data-ttu-id="129fb-139">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="129fb-139">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="129fb-140">Ověřování</span><span class="sxs-lookup"><span data-stu-id="129fb-140">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="129fb-141">Potvrzení účtu a obnovení hesla</span><span class="sxs-lookup"><span data-stu-id="129fb-141">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="129fb-142">Autorizace</span><span class="sxs-lookup"><span data-stu-id="129fb-142">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="129fb-143">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="129fb-143">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="129fb-144">V ASP.NET Core 2.1 `User.IsInRole` selže při použití `AddDefaultIdentity`.</span><span class="sxs-lookup"><span data-stu-id="129fb-144">In ASP.NET Core 2.1, `User.IsInRole` fails when using `AddDefaultIdentity`.</span></span> <span data-ttu-id="129fb-145">Tento kurz používá `AddDefaultIdentity` a vyžaduje tudíž ASP.NET Core 2.2 nebo vyšší.</span><span class="sxs-lookup"><span data-stu-id="129fb-145">This tutorial uses `AddDefaultIdentity` and therefore requires ASP.NET Core 2.2 or later.</span></span> <span data-ttu-id="129fb-146">Zobrazit [tento problém Githubu](https://github.com/aspnet/Identity/issues/1813#issuecomment-394543909) pro vyřešit.</span><span class="sxs-lookup"><span data-stu-id="129fb-146">See [this GitHub issue](https://github.com/aspnet/Identity/issues/1813#issuecomment-394543909) for a work-around.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="129fb-147">Starter a dokončené aplikace</span><span class="sxs-lookup"><span data-stu-id="129fb-147">The starter and completed app</span></span>

<span data-ttu-id="129fb-148">[Stáhněte si](xref:index#how-to-download-a-sample) [Dokončit](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) aplikace.</span><span class="sxs-lookup"><span data-stu-id="129fb-148">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="129fb-149">[Test](#test-the-completed-app) dokončené aplikace tak, že jste se seznámili s jeho funkcí zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="129fb-149">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="129fb-150">Úvodní aplikaci</span><span class="sxs-lookup"><span data-stu-id="129fb-150">The starter app</span></span>

<span data-ttu-id="129fb-151">[Stáhněte si](xref:index#how-to-download-a-sample) [starter](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2) aplikace.</span><span class="sxs-lookup"><span data-stu-id="129fb-151">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2) app.</span></span>

<span data-ttu-id="129fb-152">Spusťte aplikaci, klepněte **ContactManager** propojit a ověření můžete vytvářet, upravovat a odstraňovat kontakt.</span><span class="sxs-lookup"><span data-stu-id="129fb-152">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="129fb-153">Zabezpečení dat uživatele</span><span class="sxs-lookup"><span data-stu-id="129fb-153">Secure user data</span></span>

<span data-ttu-id="129fb-154">Následující oddíly mají všechny hlavní kroky k vytvoření aplikace pro data zabezpečení uživatele.</span><span class="sxs-lookup"><span data-stu-id="129fb-154">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="129fb-155">Vám může být užitečné k odkazování na dokončení projektu.</span><span class="sxs-lookup"><span data-stu-id="129fb-155">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="129fb-156">Tie kontaktní údaje pro uživatele</span><span class="sxs-lookup"><span data-stu-id="129fb-156">Tie the contact data to the user</span></span>

<span data-ttu-id="129fb-157">Použití technologie ASP.NET [Identity](xref:security/authentication/identity) ID uživatele, aby tak uživatelé můžete upravit jejich data, ale ne data jiných uživatelů.</span><span class="sxs-lookup"><span data-stu-id="129fb-157">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="129fb-158">Přidat `OwnerID` a `ContactStatus` k `Contact` modelu:</span><span class="sxs-lookup"><span data-stu-id="129fb-158">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="129fb-159">`OwnerID` je ID uživatele z `AspNetUser` v tabulku [Identity](xref:security/authentication/identity) databáze.</span><span class="sxs-lookup"><span data-stu-id="129fb-159">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="129fb-160">`Status` Pole určuje, zda je kontakt zobrazitelné obecný uživatel.</span><span class="sxs-lookup"><span data-stu-id="129fb-160">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="129fb-161">Vytvořte novou migraci a aktualizaci databáze:</span><span class="sxs-lookup"><span data-stu-id="129fb-161">Create a new migration and update the database:</span></span>

```console
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="129fb-162">Přidání služby Role na identitu</span><span class="sxs-lookup"><span data-stu-id="129fb-162">Add Role services to Identity</span></span>

<span data-ttu-id="129fb-163">Připojit [kliknutím na Přidat role](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) přidat služby rolí:</span><span class="sxs-lookup"><span data-stu-id="129fb-163">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a><span data-ttu-id="129fb-164">Vyžadovat ověření uživatelé</span><span class="sxs-lookup"><span data-stu-id="129fb-164">Require authenticated users</span></span>

<span data-ttu-id="129fb-165">Nastavte výchozí zásady ověřování tak, aby vyžadovala ověření uživatelů:</span><span class="sxs-lookup"><span data-stu-id="129fb-165">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="129fb-166">Můžete zrušit ověřování na úrovni metody stránky Razor, kontroler nebo akce s `[AllowAnonymous]` atribut.</span><span class="sxs-lookup"><span data-stu-id="129fb-166">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="129fb-167">Nastavení výchozí zásady ověřování tak, aby vyžadovala ověření uživatelů chrání nově přidané Razor Pages a kontrolery.</span><span class="sxs-lookup"><span data-stu-id="129fb-167">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="129fb-168">Ve výchozím nastavení je vyžadováno ověření je bezpečnější než spoléhání se na nové řadiče a Razor Pages zahrnout s `[Authorize]` atribut.</span><span class="sxs-lookup"><span data-stu-id="129fb-168">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="129fb-169">Přidat [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) index stránky o a kontaktní anonymní uživatelé získali informace o webu předtím, než aby se zaregistrovali.</span><span class="sxs-lookup"><span data-stu-id="129fb-169">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="129fb-170">Konfigurace testovací účet</span><span class="sxs-lookup"><span data-stu-id="129fb-170">Configure the test account</span></span>

<span data-ttu-id="129fb-171">`SeedData` Třída vytvoří dva účty: správce a správce.</span><span class="sxs-lookup"><span data-stu-id="129fb-171">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="129fb-172">Použití [nástroj tajný klíč správce](xref:security/app-secrets) nastavení hesla pro tyto účty.</span><span class="sxs-lookup"><span data-stu-id="129fb-172">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="129fb-173">Nastavte heslo z adresáře projektu (adresáře, který obsahuje *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="129fb-173">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```console
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="129fb-174">Pokud není zadán silné heslo, je vyvolána výjimka, když `SeedData.Initialize` je volána.</span><span class="sxs-lookup"><span data-stu-id="129fb-174">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="129fb-175">Aktualizace `Main` test heslo budete používat:</span><span class="sxs-lookup"><span data-stu-id="129fb-175">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="129fb-176">Vytvoření testovacích účtů a aktualizovat kontakty</span><span class="sxs-lookup"><span data-stu-id="129fb-176">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="129fb-177">Aktualizace `Initialize` metodu `SeedData` třídy za účelem vytvoření testovacích účtů:</span><span class="sxs-lookup"><span data-stu-id="129fb-177">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="129fb-178">Přidat správce ID uživatele a `ContactStatus` kontaktům.</span><span class="sxs-lookup"><span data-stu-id="129fb-178">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="129fb-179">Proveďte jednu z kontakty "Submitted" a jedné "byl odmítnut".</span><span class="sxs-lookup"><span data-stu-id="129fb-179">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="129fb-180">Přidáte ID uživatele a stav pro všechny kontakty.</span><span class="sxs-lookup"><span data-stu-id="129fb-180">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="129fb-181">Je zobrazena pouze jeden kontakt:</span><span class="sxs-lookup"><span data-stu-id="129fb-181">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="129fb-182">Vytvořit vlastníka, správce a Správce autorizace obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="129fb-182">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="129fb-183">Vytvoření `ContactIsOwnerAuthorizationHandler` třídy v *autorizace* složky.</span><span class="sxs-lookup"><span data-stu-id="129fb-183">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="129fb-184">`ContactIsOwnerAuthorizationHandler` Ověřuje, že uživatel na prostředek vlastníkem prostředku.</span><span class="sxs-lookup"><span data-stu-id="129fb-184">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="129fb-185">`ContactIsOwnerAuthorizationHandler` Volání [kontextu. Úspěšné](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) aktuálně ověřeného uživatele při jeho vlastníkem.</span><span class="sxs-lookup"><span data-stu-id="129fb-185">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="129fb-186">Obslužné rutiny autorizace obecně:</span><span class="sxs-lookup"><span data-stu-id="129fb-186">Authorization handlers generally:</span></span>

* <span data-ttu-id="129fb-187">Vrátí `context.Succeed` Pokud jsou splněny.</span><span class="sxs-lookup"><span data-stu-id="129fb-187">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="129fb-188">Vrátí `Task.CompletedTask` Pokud požadavky nejsou splněny.</span><span class="sxs-lookup"><span data-stu-id="129fb-188">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="129fb-189">`Task.CompletedTask` je ani úspěch nebo neúspěch&mdash;umožňuje ostatních obslužných rutin autorizaci ke spuštění.</span><span class="sxs-lookup"><span data-stu-id="129fb-189">`Task.CompletedTask` is neither success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="129fb-190">Pokud je potřeba explicitně nezdaří, vrátí [kontextu. Selhání](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="129fb-190">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="129fb-191">Aplikaci umožňuje kontaktní vlastníkům upravit, odstranit a vytvořit svoje vlastní data.</span><span class="sxs-lookup"><span data-stu-id="129fb-191">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="129fb-192">`ContactIsOwnerAuthorizationHandler` není nutné ke kontrole operace předané v parametru požadavku.</span><span class="sxs-lookup"><span data-stu-id="129fb-192">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="129fb-193">Vytvořte obslužnou rutinu Správce autorizací</span><span class="sxs-lookup"><span data-stu-id="129fb-193">Create a manager authorization handler</span></span>

<span data-ttu-id="129fb-194">Vytvoření `ContactManagerAuthorizationHandler` třídy v *autorizace* složky.</span><span class="sxs-lookup"><span data-stu-id="129fb-194">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="129fb-195">`ContactManagerAuthorizationHandler` Ověřuje uživatele na prostředek je správce.</span><span class="sxs-lookup"><span data-stu-id="129fb-195">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="129fb-196">Pouze vedoucí mohli schválit nebo odmítnout změny obsahu (nové nebo změněné).</span><span class="sxs-lookup"><span data-stu-id="129fb-196">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="129fb-197">Vytvořte obslužnou rutinu Správce autorizací</span><span class="sxs-lookup"><span data-stu-id="129fb-197">Create an administrator authorization handler</span></span>

<span data-ttu-id="129fb-198">Vytvoření `ContactAdministratorsAuthorizationHandler` třídy v *autorizace* složky.</span><span class="sxs-lookup"><span data-stu-id="129fb-198">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="129fb-199">`ContactAdministratorsAuthorizationHandler` Ověřuje uživatele na prostředek je správce.</span><span class="sxs-lookup"><span data-stu-id="129fb-199">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="129fb-200">Správce může provádět všechny operace.</span><span class="sxs-lookup"><span data-stu-id="129fb-200">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="129fb-201">Zaregistrujte obslužné rutiny autorizace</span><span class="sxs-lookup"><span data-stu-id="129fb-201">Register the authorization handlers</span></span>

<span data-ttu-id="129fb-202">Pomocí Entity Framework Core Services musí být zaregistrovaný pro [injektáž závislostí](xref:fundamentals/dependency-injection) pomocí [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="129fb-202">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="129fb-203">`ContactIsOwnerAuthorizationHandler` Používá ASP.NET Core [Identity](xref:security/authentication/identity), která je založená na Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="129fb-203">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="129fb-204">Obslužné rutiny zaregistrovat u kolekce služby jsou k dispozici na `ContactsController` prostřednictvím [injektáž závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="129fb-204">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="129fb-205">Přidejte následující kód do konce `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="129fb-205">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="129fb-206">`ContactAdministratorsAuthorizationHandler` a `ContactManagerAuthorizationHandler` jsou přidány jako jednotlivé prvky.</span><span class="sxs-lookup"><span data-stu-id="129fb-206">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="129fb-207">Jednotlivé prvky jsou vzhledem k tomu, že se nepoužívají EF a všechny informace potřebné musí být `Context` parametr `HandleRequirementAsync` metoda.</span><span class="sxs-lookup"><span data-stu-id="129fb-207">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="129fb-208">Povolení podpory</span><span class="sxs-lookup"><span data-stu-id="129fb-208">Support authorization</span></span>

<span data-ttu-id="129fb-209">V této části se aktualizace stránky Razor a přidejte třídu požadavků operace.</span><span class="sxs-lookup"><span data-stu-id="129fb-209">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="129fb-210">Zkontrolujte požadavky na třídy kontaktní operace</span><span class="sxs-lookup"><span data-stu-id="129fb-210">Review the contact operations requirements class</span></span>

<span data-ttu-id="129fb-211">Zkontrolujte `ContactOperations` třídy.</span><span class="sxs-lookup"><span data-stu-id="129fb-211">Review the `ContactOperations` class.</span></span> <span data-ttu-id="129fb-212">Tato třída obsahuje požadavky na aplikace podporuje:</span><span class="sxs-lookup"><span data-stu-id="129fb-212">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="129fb-213">Vytvoření základní třídu pro stránky Razor kontakty</span><span class="sxs-lookup"><span data-stu-id="129fb-213">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="129fb-214">Vytvořte základní třídu, která obsahuje služby využité v kontaktech stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="129fb-214">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="129fb-215">Základní třída vloží kód inicializace na jednom místě:</span><span class="sxs-lookup"><span data-stu-id="129fb-215">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="129fb-216">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="129fb-216">The preceding code:</span></span>

* <span data-ttu-id="129fb-217">Přidá `IAuthorizationService` služby pro přístup k rutinám autorizace.</span><span class="sxs-lookup"><span data-stu-id="129fb-217">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="129fb-218">Přidá identitu `UserManager` služby.</span><span class="sxs-lookup"><span data-stu-id="129fb-218">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="129fb-219">Přidat `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="129fb-219">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="129fb-220">Aktualizace CreateModel</span><span class="sxs-lookup"><span data-stu-id="129fb-220">Update the CreateModel</span></span>

<span data-ttu-id="129fb-221">Aktualizovat konstruktoru vytvořit stránku model použití `DI_BasePageModel` základní třídy:</span><span class="sxs-lookup"><span data-stu-id="129fb-221">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="129fb-222">Aktualizace `CreateModel.OnPostAsync` metodu:</span><span class="sxs-lookup"><span data-stu-id="129fb-222">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="129fb-223">Přidat ID uživatele, `Contact` modelu.</span><span class="sxs-lookup"><span data-stu-id="129fb-223">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="129fb-224">Ověřte, zda že má uživatel oprávnění k vytvoření kontakty obslužná rutina ověřování volejte.</span><span class="sxs-lookup"><span data-stu-id="129fb-224">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="129fb-225">Aktualizace IndexModel</span><span class="sxs-lookup"><span data-stu-id="129fb-225">Update the IndexModel</span></span>

<span data-ttu-id="129fb-226">Aktualizace `OnGetAsync` metody, jsou uvedeny pouze schválené kontakty obecné uživatelům:</span><span class="sxs-lookup"><span data-stu-id="129fb-226">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="129fb-227">Aktualizace EditModel</span><span class="sxs-lookup"><span data-stu-id="129fb-227">Update the EditModel</span></span>

<span data-ttu-id="129fb-228">Přidejte obslužnou rutinu ověřování k ověření, že uživatel je vlastníkem kontaktu.</span><span class="sxs-lookup"><span data-stu-id="129fb-228">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="129fb-229">Protože ra se ověřuje, `[Authorize]` atribut není dostatek.</span><span class="sxs-lookup"><span data-stu-id="129fb-229">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="129fb-230">Aplikace nemá přístup k prostředku, když jsou vyhodnocovány atributy.</span><span class="sxs-lookup"><span data-stu-id="129fb-230">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="129fb-231">Autorizace na základě prostředků musí být nezbytné.</span><span class="sxs-lookup"><span data-stu-id="129fb-231">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="129fb-232">Kontroly musí provést, když má aplikace přístup k prostředku načtením v modelu stránky nebo načtením v rámci samotná obslužná rutina.</span><span class="sxs-lookup"><span data-stu-id="129fb-232">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="129fb-233">Často přistupovat k prostředku předáním klíč prostředku.</span><span class="sxs-lookup"><span data-stu-id="129fb-233">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="129fb-234">Aktualizace DeleteModel</span><span class="sxs-lookup"><span data-stu-id="129fb-234">Update the DeleteModel</span></span>

<span data-ttu-id="129fb-235">Aktualizace modelu stránku odstranit sloužící k ověření, že uživatel má oprávnění odstranit u kontaktu obslužnou rutinu ověřování.</span><span class="sxs-lookup"><span data-stu-id="129fb-235">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="129fb-236">Vložit do zobrazení autorizační službu</span><span class="sxs-lookup"><span data-stu-id="129fb-236">Inject the authorization service into the views</span></span>

<span data-ttu-id="129fb-237">V současné době ukazuje uživatelského rozhraní upravovat a odstraňovat kontakty, které uživatel nemůže upravovat odkazy.</span><span class="sxs-lookup"><span data-stu-id="129fb-237">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="129fb-238">Vloží autorizační službu v *Views/_ViewImports.cshtml* souboru tak, aby byl k dispozici pro všechna zobrazení:</span><span class="sxs-lookup"><span data-stu-id="129fb-238">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="129fb-239">Předchozí kód přidá několik `using` příkazy.</span><span class="sxs-lookup"><span data-stu-id="129fb-239">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="129fb-240">Aktualizace **upravit** a **odstranit** odkazů v *Pages/Contacts/Index.cshtml* tak, že pouze vykreslen pro uživatele s příslušnými oprávněními:</span><span class="sxs-lookup"><span data-stu-id="129fb-240">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="129fb-241">Skrytí propojení od uživatele, kteří nemají oprávnění ke změně dat nebude zabezpečení aplikace.</span><span class="sxs-lookup"><span data-stu-id="129fb-241">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="129fb-242">Skrytí propojení nastaví aplikaci jako přívětivější tím, že zobrazuje pouze platné odkazy.</span><span class="sxs-lookup"><span data-stu-id="129fb-242">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="129fb-243">Uživatelé můžou hack generované adres URL pro vyvolání funkce upravit a odstranit operací s daty, které není vlastní.</span><span class="sxs-lookup"><span data-stu-id="129fb-243">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="129fb-244">Stránka Razor nebo kontroleru musí vynucovat kontroly přístupu pro data zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="129fb-244">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="129fb-245">Podrobné informace o aktualizaci</span><span class="sxs-lookup"><span data-stu-id="129fb-245">Update Details</span></span>

<span data-ttu-id="129fb-246">Aktualizujte zobrazení podrobností, aby vedoucí mohli schválit nebo odmítnout kontaktů:</span><span class="sxs-lookup"><span data-stu-id="129fb-246">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="129fb-247">Model stránky podrobnosti aktualizace:</span><span class="sxs-lookup"><span data-stu-id="129fb-247">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="129fb-248">Přidat nebo odebrat uživatele k roli</span><span class="sxs-lookup"><span data-stu-id="129fb-248">Add or remove a user to a role</span></span>

<span data-ttu-id="129fb-249">Zobrazit [tento problém](https://github.com/aspnet/Docs/issues/8502) informace na:</span><span class="sxs-lookup"><span data-stu-id="129fb-249">See [this issue](https://github.com/aspnet/Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="129fb-250">Odebrání oprávnění uživatele.</span><span class="sxs-lookup"><span data-stu-id="129fb-250">Removing privileges from a user.</span></span> <span data-ttu-id="129fb-251">Třeba se ztlumení uživatele v chatovací aplikaci.</span><span class="sxs-lookup"><span data-stu-id="129fb-251">For example muting a user in a chat app.</span></span>
* <span data-ttu-id="129fb-252">Přidání oprávnění pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="129fb-252">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="129fb-253">Testování dokončené aplikace</span><span class="sxs-lookup"><span data-stu-id="129fb-253">Test the completed app</span></span>

<span data-ttu-id="129fb-254">Pokud jste dosud nenastavili hesla pro dosazené uživatelské účty, použijte [nástroj tajný klíč správce](xref:security/app-secrets#secret-manager) nastavit heslo:</span><span class="sxs-lookup"><span data-stu-id="129fb-254">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="129fb-255">Zvolte silné heslo: Použít osm nebo více znaků a alespoň jeden znak velká písmena, čísla a symbolů.</span><span class="sxs-lookup"><span data-stu-id="129fb-255">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="129fb-256">Například `Passw0rd!` splňuje požadavky na silné heslo.</span><span class="sxs-lookup"><span data-stu-id="129fb-256">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="129fb-257">Spusťte následující příkaz ze složky projektu, kde `<PW>` je heslo:</span><span class="sxs-lookup"><span data-stu-id="129fb-257">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```console
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="129fb-258">Pokud má kontaktů:</span><span class="sxs-lookup"><span data-stu-id="129fb-258">If the app has contacts:</span></span>

* <span data-ttu-id="129fb-259">Odstranění všech záznamů v `Contact` tabulky.</span><span class="sxs-lookup"><span data-stu-id="129fb-259">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="129fb-260">Restartujte aplikaci k přidání dat do databáze.</span><span class="sxs-lookup"><span data-stu-id="129fb-260">Restart the app to seed the database.</span></span>

<span data-ttu-id="129fb-261">Snadný způsob, jak otestovat dokončená aplikace je spustíte tři různé prohlížeče (nebo incognito/InPrivate relace).</span><span class="sxs-lookup"><span data-stu-id="129fb-261">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="129fb-262">V jeden prohlížeč, registraci nového uživatele (například `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="129fb-262">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="129fb-263">Přihlaste se k každým prohlížečem s jiným uživatelem.</span><span class="sxs-lookup"><span data-stu-id="129fb-263">Sign in to each browser with a different user.</span></span> <span data-ttu-id="129fb-264">Ověřte následující operace:</span><span class="sxs-lookup"><span data-stu-id="129fb-264">Verify the following operations:</span></span>

* <span data-ttu-id="129fb-265">Registrovaných uživatelů můžete zobrazit všechny schválené kontaktní údaje.</span><span class="sxs-lookup"><span data-stu-id="129fb-265">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="129fb-266">Registrovaných uživatelů můžete upravit nebo odstranit svá vlastní data.</span><span class="sxs-lookup"><span data-stu-id="129fb-266">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="129fb-267">Správci mohou schvalovat a odmítat kontaktní údaje.</span><span class="sxs-lookup"><span data-stu-id="129fb-267">Managers can approve/reject contact data.</span></span> <span data-ttu-id="129fb-268">`Details` Zobrazení ukazuje **schválit** a **odmítnout** tlačítka.</span><span class="sxs-lookup"><span data-stu-id="129fb-268">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="129fb-269">Správci můžou schvalovat a odmítat a upravit nebo odstranit všechna data.</span><span class="sxs-lookup"><span data-stu-id="129fb-269">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="129fb-270">Uživatel</span><span class="sxs-lookup"><span data-stu-id="129fb-270">User</span></span>                | <span data-ttu-id="129fb-271">Nasazených aplikací</span><span class="sxs-lookup"><span data-stu-id="129fb-271">Seeded by the app</span></span> | <span data-ttu-id="129fb-272">Možnosti</span><span class="sxs-lookup"><span data-stu-id="129fb-272">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="129fb-273">Ne</span><span class="sxs-lookup"><span data-stu-id="129fb-273">No</span></span>                | <span data-ttu-id="129fb-274">Upravit nebo odstranit vlastní data.</span><span class="sxs-lookup"><span data-stu-id="129fb-274">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="129fb-275">Ano</span><span class="sxs-lookup"><span data-stu-id="129fb-275">Yes</span></span>               | <span data-ttu-id="129fb-276">Vlastní data, schvalovat a odmítat a upravit nebo odstranit.</span><span class="sxs-lookup"><span data-stu-id="129fb-276">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="129fb-277">Ano</span><span class="sxs-lookup"><span data-stu-id="129fb-277">Yes</span></span>               | <span data-ttu-id="129fb-278">Schválit nebo odmítnout a upravit nebo odstranit všechna data.</span><span class="sxs-lookup"><span data-stu-id="129fb-278">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="129fb-279">Vytvoření kontaktu v prohlížeči na správce.</span><span class="sxs-lookup"><span data-stu-id="129fb-279">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="129fb-280">Zkopírujte adresu URL pro odstranění a upravit z kontaktujte správce.</span><span class="sxs-lookup"><span data-stu-id="129fb-280">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="129fb-281">Vložte tyto odkazy do testů webového prohlížeče k ověření, že testovací uživatel nemůže provádět tyto operace.</span><span class="sxs-lookup"><span data-stu-id="129fb-281">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="129fb-282">Vytvořit úvodní aplikaci</span><span class="sxs-lookup"><span data-stu-id="129fb-282">Create the starter app</span></span>

* <span data-ttu-id="129fb-283">Vytvoření aplikace stránky Razor s názvem "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="129fb-283">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="129fb-284">Vytvoření aplikace s **jednotlivých uživatelských účtů**.</span><span class="sxs-lookup"><span data-stu-id="129fb-284">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="129fb-285">Pojmenujte ji "ContactManager" tak obor názvů odpovídá oboru názvů použitého v ukázce.</span><span class="sxs-lookup"><span data-stu-id="129fb-285">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="129fb-286">`-uld` Určuje LocalDB místo SQLite</span><span class="sxs-lookup"><span data-stu-id="129fb-286">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```console
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="129fb-287">Přidat *Models/Contact.cs*:</span><span class="sxs-lookup"><span data-stu-id="129fb-287">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="129fb-288">Vygenerované uživatelské rozhraní `Contact` modelu.</span><span class="sxs-lookup"><span data-stu-id="129fb-288">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="129fb-289">Vytvořte počáteční migraci a aktualizaci databáze:</span><span class="sxs-lookup"><span data-stu-id="129fb-289">Create initial migration and update the database:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="129fb-290">Aktualizace **ContactManager** ukotvit v *Pages/_Layout.cshtml* souboru:</span><span class="sxs-lookup"><span data-stu-id="129fb-290">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="129fb-291">Testování aplikace pomocí vytváření, úpravy a odstranění kontaktu</span><span class="sxs-lookup"><span data-stu-id="129fb-291">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="129fb-292">Přidání dat do databáze</span><span class="sxs-lookup"><span data-stu-id="129fb-292">Seed the database</span></span>

<span data-ttu-id="129fb-293">Přidat [SeedData](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) třídu *Data* složky.</span><span class="sxs-lookup"><span data-stu-id="129fb-293">Add the [SeedData](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="129fb-294">Volání `SeedData.Initialize` z `Main`:</span><span class="sxs-lookup"><span data-stu-id="129fb-294">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="129fb-295">Otestujte, že aplikace naplnila databázi.</span><span class="sxs-lookup"><span data-stu-id="129fb-295">Test that the app seeded the database.</span></span> <span data-ttu-id="129fb-296">Pokud existují nějaké řádky v kontaktu DB, metoda počáteční hodnoty není spuštěna.</span><span class="sxs-lookup"><span data-stu-id="129fb-296">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="129fb-297">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="129fb-297">Additional resources</span></span>

* [<span data-ttu-id="129fb-298">Vytvoření webové aplikace .NET Core využívající SQL Database ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="129fb-298">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="129fb-299">[ASP.NET Core povolení Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="129fb-299">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="129fb-300">Toto testovací prostředí obsahuje větší podrobnosti o funkcích zabezpečení v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="129fb-300">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="129fb-301">Autorizace na základě zásad</span><span class="sxs-lookup"><span data-stu-id="129fb-301">Custom policy-based authorization</span></span>](xref:security/authorization/policies)

::: moniker-end

---
title: Vytvoření aplikace ASP.NET Core s uživatelskými daty chráněnými autorizací
author: rick-anderson
description: Naučte se vytvářet ASP.NET Core webové aplikace s uživatelskými daty chráněnými autorizací. Zahrnuje protokol HTTPS, ověřování, zabezpečení ASP.NET Core Identity .
ms.author: riande
ms.date: 7/18/2020
ms.custom: mvc, seodec18
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/secure-data
ms.openlocfilehash: 44777369693f9eb29d78c3ba638db2e692f430ae
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021182"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="b56a3-104">Vytvoření webové aplikace v ASP.NET Core s uživatelskými daty chráněnými autorizací</span><span class="sxs-lookup"><span data-stu-id="b56a3-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="b56a3-105">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Jan Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="b56a3-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="b56a3-106">Zobrazit [Tento PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="b56a3-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b56a3-107">V tomto kurzu se dozvíte, jak vytvořit webovou aplikaci ASP.NET Core s uživatelskými daty chráněnými autorizací.</span><span class="sxs-lookup"><span data-stu-id="b56a3-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="b56a3-108">Zobrazuje seznam kontaktů ověřovaných (registrovaných) uživatelů.</span><span class="sxs-lookup"><span data-stu-id="b56a3-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="b56a3-109">Existují tři skupiny zabezpečení:</span><span class="sxs-lookup"><span data-stu-id="b56a3-109">There are three security groups:</span></span>

* <span data-ttu-id="b56a3-110">**Registrovaní uživatelé** můžou zobrazit všechna schválená data a můžou upravovat nebo odstraňovat vlastní data.</span><span class="sxs-lookup"><span data-stu-id="b56a3-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="b56a3-111">**Manažeři** mohou schvalovat nebo odmítat kontaktní data.</span><span class="sxs-lookup"><span data-stu-id="b56a3-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="b56a3-112">Uživatelé vidí jenom schválené kontakty.</span><span class="sxs-lookup"><span data-stu-id="b56a3-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="b56a3-113">**Správci** mohou schvalovat nebo odmítat a upravovat nebo odstraňovat jakákoli data.</span><span class="sxs-lookup"><span data-stu-id="b56a3-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="b56a3-114">Obrázky v tomto dokumentu se přesně neshodují s nejnovějšími šablonami.</span><span class="sxs-lookup"><span data-stu-id="b56a3-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="b56a3-115">Na následujícím obrázku je uživatel Rick ( `rick@example.com` ) přihlášen.</span><span class="sxs-lookup"><span data-stu-id="b56a3-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="b56a3-116">Rick může zobrazit pouze schválené kontakty a **Upravit** / **Odstranit** / **nové** odkazy pro své kontakty.</span><span class="sxs-lookup"><span data-stu-id="b56a3-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="b56a3-117">Jenom poslední záznam vytvořený pomocí Rick zobrazí odkazy pro **Úpravy** a **odstranění** .</span><span class="sxs-lookup"><span data-stu-id="b56a3-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="b56a3-118">Ostatní uživatelé uvidí poslední záznam, dokud správce nebo správce nezmění stav na schváleno.</span><span class="sxs-lookup"><span data-stu-id="b56a3-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Snímek obrazovky zobrazující přihlášený Rick](secure-data/_static/rick.png)

<span data-ttu-id="b56a3-120">Na následujícím obrázku `manager@contoso.com` je přihlášen a v roli manažera:</span><span class="sxs-lookup"><span data-stu-id="b56a3-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Snímek obrazovky zobrazující manager@contoso.com přihlášený](secure-data/_static/manager1.png)

<span data-ttu-id="b56a3-122">Následující obrázek ukazuje zobrazení podrobností o kontaktu pro správce:</span><span class="sxs-lookup"><span data-stu-id="b56a3-122">The following image shows the managers details view of a contact:</span></span>

![Zobrazení kontaktu manažera](secure-data/_static/manager.png)

<span data-ttu-id="b56a3-124">Tlačítka **schválení** a **odmítnutí** se zobrazují pouze pro manažery a správce.</span><span class="sxs-lookup"><span data-stu-id="b56a3-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="b56a3-125">Na následujícím obrázku `admin@contoso.com` je přihlášen a v roli správce:</span><span class="sxs-lookup"><span data-stu-id="b56a3-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Snímek obrazovky zobrazující admin@contoso.com přihlášený](secure-data/_static/admin.png)

<span data-ttu-id="b56a3-127">Správce má všechna oprávnění.</span><span class="sxs-lookup"><span data-stu-id="b56a3-127">The administrator has all privileges.</span></span> <span data-ttu-id="b56a3-128">Může číst, upravovat a odstraňovat jakékoli kontakty a měnit stav kontaktů.</span><span class="sxs-lookup"><span data-stu-id="b56a3-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="b56a3-129">Aplikace byla vytvořena pomocí [generování uživatelského rozhraní](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) tohoto `Contact` modelu:</span><span class="sxs-lookup"><span data-stu-id="b56a3-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="b56a3-130">Ukázka obsahuje následující obslužné rutiny autorizace:</span><span class="sxs-lookup"><span data-stu-id="b56a3-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="b56a3-131">`ContactIsOwnerAuthorizationHandler`: Zajistí, že uživatel může upravovat pouze svá data.</span><span class="sxs-lookup"><span data-stu-id="b56a3-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="b56a3-132">`ContactManagerAuthorizationHandler`: Umožňuje správcům schvalovat nebo odmítat kontakty.</span><span class="sxs-lookup"><span data-stu-id="b56a3-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="b56a3-133">`ContactAdministratorsAuthorizationHandler`: Umožňuje správcům schvalovat nebo odmítat kontakty a upravovat nebo odstraňovat kontakty.</span><span class="sxs-lookup"><span data-stu-id="b56a3-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b56a3-134">Požadavky</span><span class="sxs-lookup"><span data-stu-id="b56a3-134">Prerequisites</span></span>

<span data-ttu-id="b56a3-135">Tento kurz je pokročilý.</span><span class="sxs-lookup"><span data-stu-id="b56a3-135">This tutorial is advanced.</span></span> <span data-ttu-id="b56a3-136">Měli byste být obeznámeni s:</span><span class="sxs-lookup"><span data-stu-id="b56a3-136">You should be familiar with:</span></span>

* [<span data-ttu-id="b56a3-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b56a3-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="b56a3-138">Ověřování</span><span class="sxs-lookup"><span data-stu-id="b56a3-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="b56a3-139">Potvrzení účtu a obnovení hesla</span><span class="sxs-lookup"><span data-stu-id="b56a3-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="b56a3-140">Autorizace</span><span class="sxs-lookup"><span data-stu-id="b56a3-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="b56a3-141">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="b56a3-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="b56a3-142">Úvodní a dokončená aplikace</span><span class="sxs-lookup"><span data-stu-id="b56a3-142">The starter and completed app</span></span>

<span data-ttu-id="b56a3-143">[Stáhněte](xref:index#how-to-download-a-sample) [dokončenou](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b56a3-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="b56a3-144">[Otestujte](#test-the-completed-app) dokončenou aplikaci, abyste se seznámili se svými funkcemi zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="b56a3-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="b56a3-145">Úvodní aplikace</span><span class="sxs-lookup"><span data-stu-id="b56a3-145">The starter app</span></span>

<span data-ttu-id="b56a3-146">[Stáhněte](xref:index#how-to-download-a-sample) si [úvodní](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b56a3-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="b56a3-147">Spusťte aplikaci, klepněte na odkaz **ContactManager** a ověřte, že můžete vytvořit, upravit a odstranit kontakt.</span><span class="sxs-lookup"><span data-stu-id="b56a3-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="b56a3-148">Zabezpečená uživatelská data</span><span class="sxs-lookup"><span data-stu-id="b56a3-148">Secure user data</span></span>

<span data-ttu-id="b56a3-149">V následujících částech najdete všechny hlavní kroky k vytvoření aplikace zabezpečených uživatelských dat.</span><span class="sxs-lookup"><span data-stu-id="b56a3-149">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="b56a3-150">Může to být užitečné pro odkazování na dokončený projekt.</span><span class="sxs-lookup"><span data-stu-id="b56a3-150">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="b56a3-151">Spojit kontaktní údaje s uživatelem</span><span class="sxs-lookup"><span data-stu-id="b56a3-151">Tie the contact data to the user</span></span>

<span data-ttu-id="b56a3-152">Pomocí [Identity](xref:security/authentication/identity) ID uživatele ASP.NET zajistěte, aby mohli uživatelé upravovat data, ale ne jiná data uživatelů.</span><span class="sxs-lookup"><span data-stu-id="b56a3-152">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="b56a3-153">Přidejte `OwnerID` a `ContactStatus` do `Contact` modelu:</span><span class="sxs-lookup"><span data-stu-id="b56a3-153">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="b56a3-154">`OwnerID`je ID uživatele z `AspNetUser` tabulky v [Identity](xref:security/authentication/identity) databázi.</span><span class="sxs-lookup"><span data-stu-id="b56a3-154">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="b56a3-155">`Status`Pole určuje, jestli je kontakt viditelný pro obecné uživatele.</span><span class="sxs-lookup"><span data-stu-id="b56a3-155">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="b56a3-156">Vytvořte novou migraci a aktualizujte databázi:</span><span class="sxs-lookup"><span data-stu-id="b56a3-156">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="b56a3-157">Přidat služby rolí doIdentity</span><span class="sxs-lookup"><span data-stu-id="b56a3-157">Add Role services to Identity</span></span>

<span data-ttu-id="b56a3-158">Připojit [Přidat role](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) pro přidání služeb role:</span><span class="sxs-lookup"><span data-stu-id="b56a3-158">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="b56a3-159">Vyžadovat ověřené uživatele</span><span class="sxs-lookup"><span data-stu-id="b56a3-159">Require authenticated users</span></span>

<span data-ttu-id="b56a3-160">Nastavte zásady nouzového ověřování tak, aby vyžadovaly ověření uživatelů:</span><span class="sxs-lookup"><span data-stu-id="b56a3-160">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="b56a3-161">Předchozí zvýrazněný kód nastaví [zásady nouzového ověřování](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span><span class="sxs-lookup"><span data-stu-id="b56a3-161">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="b56a3-162">Zásada záložního ověřování vyžaduje, aby ***Všichni*** uživatelé byli ověřeni, s výjimkou Razor stránek, řadičů nebo metod akcí s atributem ověřování.</span><span class="sxs-lookup"><span data-stu-id="b56a3-162">The fallback authentication policy requires ***all*** users to be authenticated, except for Razor Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="b56a3-163">Například Razor stránky, řadiče nebo metody akcí pomocí `[AllowAnonymous]` nebo `[Authorize(PolicyName="MyPolicy")]` použijte použitý atribut ověřování, nikoli zásady nouzového ověřování.</span><span class="sxs-lookup"><span data-stu-id="b56a3-163">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="b56a3-164">Zásady nouzového ověřování:</span><span class="sxs-lookup"><span data-stu-id="b56a3-164">The fallback authentication policy:</span></span>

* <span data-ttu-id="b56a3-165">Se použije u všech požadavků, které explicitně neurčují zásady ověřování.</span><span class="sxs-lookup"><span data-stu-id="b56a3-165">Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="b56a3-166">V případě požadavků poskytovaných směrováním koncových bodů by to zahrnovalo koncový bod, který nespecifikuje autorizační atribut.</span><span class="sxs-lookup"><span data-stu-id="b56a3-166">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="b56a3-167">Pro požadavky poskytované jiným middlewarem middleware po autorizačním middlewaru, jako jsou třeba [statické soubory](xref:fundamentals/static-files), by se tato zásada použila u všech požadavků.</span><span class="sxs-lookup"><span data-stu-id="b56a3-167">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="b56a3-168">Nastavení zásady nouzového ověřování, aby vyžadovala ověření uživatelů, chrání nově přidané Razor stránky a řadiče.</span><span class="sxs-lookup"><span data-stu-id="b56a3-168">Setting the fallback authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="b56a3-169">Pokud je ve výchozím nastavení vyžadováno ověřování, je bezpečnější než spoléhání na nové řadiče a Razor stránky pro zahrnutí `[Authorize]` atributu.</span><span class="sxs-lookup"><span data-stu-id="b56a3-169">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="b56a3-170"><xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions>Třída také obsahuje <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="b56a3-170">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="b56a3-171">`DefaultPolicy`Je zásada použitá u `[Authorize]` atributu, pokud není zadána žádná zásada.</span><span class="sxs-lookup"><span data-stu-id="b56a3-171">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="b56a3-172">`[Authorize]`neobsahuje pojmenovanou zásadu (na rozdíl od) `[Authorize(PolicyName="MyPolicy")]` .</span><span class="sxs-lookup"><span data-stu-id="b56a3-172">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="b56a3-173">Další informace o zásadách najdete v tématu <xref:security/authorization/policies> .</span><span class="sxs-lookup"><span data-stu-id="b56a3-173">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="b56a3-174">Alternativní způsob, jakým se pro řadiče a Razor stránky MVC vyžaduje ověření všech uživatelů, je přidání autorizačního filtru:</span><span class="sxs-lookup"><span data-stu-id="b56a3-174">An alternative way for MVC controllers and Razor Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="b56a3-175">Předchozí kód používá filtr autorizace a nastavení záložní Zásady používá směrování koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="b56a3-175">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="b56a3-176">Nastavení záložní zásady je upřednostňovaným způsobem, jak vyžadovat ověření všech uživatelů.</span><span class="sxs-lookup"><span data-stu-id="b56a3-176">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="b56a3-177">Přidat [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) na `Index` stránky a `Privacy` , aby anonymní uživatelé mohli získat informace o lokalitě před jejich registrací:</span><span class="sxs-lookup"><span data-stu-id="b56a3-177">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="b56a3-178">Konfigurace testovacího účtu</span><span class="sxs-lookup"><span data-stu-id="b56a3-178">Configure the test account</span></span>

<span data-ttu-id="b56a3-179">`SeedData`Třída vytvoří dva účty: správce a manažer.</span><span class="sxs-lookup"><span data-stu-id="b56a3-179">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="b56a3-180">Pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets) nastavte heslo pro tyto účty.</span><span class="sxs-lookup"><span data-stu-id="b56a3-180">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="b56a3-181">Nastavte heslo z adresáře projektu (adresář obsahující *program.cs*):</span><span class="sxs-lookup"><span data-stu-id="b56a3-181">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="b56a3-182">Pokud není zadáno silné heslo, je vyvolána výjimka, když `SeedData.Initialize` je volána metoda.</span><span class="sxs-lookup"><span data-stu-id="b56a3-182">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="b56a3-183">Aktualizace `Main` pro použití testovacího hesla:</span><span class="sxs-lookup"><span data-stu-id="b56a3-183">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="b56a3-184">Vytvoření testovacích účtů a aktualizace kontaktů</span><span class="sxs-lookup"><span data-stu-id="b56a3-184">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="b56a3-185">Aktualizujte `Initialize` metodu ve `SeedData` třídě pro vytvoření testovacích účtů:</span><span class="sxs-lookup"><span data-stu-id="b56a3-185">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="b56a3-186">Přidejte ID uživatele správce a `ContactStatus` ke kontaktům.</span><span class="sxs-lookup"><span data-stu-id="b56a3-186">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="b56a3-187">Poznamenejte si jednu z kontaktů "odeslané" a jednu "zamítnutou".</span><span class="sxs-lookup"><span data-stu-id="b56a3-187">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="b56a3-188">Přidejte ke všem kontaktům ID a stav uživatele.</span><span class="sxs-lookup"><span data-stu-id="b56a3-188">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="b56a3-189">Zobrazí se pouze jeden kontakt:</span><span class="sxs-lookup"><span data-stu-id="b56a3-189">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="b56a3-190">Vytváření obslužných rutin autorizace vlastníka, správce a správce</span><span class="sxs-lookup"><span data-stu-id="b56a3-190">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="b56a3-191">`ContactIsOwnerAuthorizationHandler`Ve složce pro *autorizaci* vytvořte třídu.</span><span class="sxs-lookup"><span data-stu-id="b56a3-191">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="b56a3-192">`ContactIsOwnerAuthorizationHandler`Ověřuje, že uživatel, který pracuje na prostředku, je vlastníkem prostředku.</span><span class="sxs-lookup"><span data-stu-id="b56a3-192">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="b56a3-193">`ContactIsOwnerAuthorizationHandler`Kontext volání [. Úspěšné](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , pokud je aktuální ověřený uživatel vlastníkem kontaktu.</span><span class="sxs-lookup"><span data-stu-id="b56a3-193">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="b56a3-194">Obslužné rutiny autorizace obecně:</span><span class="sxs-lookup"><span data-stu-id="b56a3-194">Authorization handlers generally:</span></span>

* <span data-ttu-id="b56a3-195">Vrátí `context.Succeed` se, pokud jsou splněny požadavky.</span><span class="sxs-lookup"><span data-stu-id="b56a3-195">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="b56a3-196">Vrátí se `Task.CompletedTask` , pokud nejsou splněné požadavky.</span><span class="sxs-lookup"><span data-stu-id="b56a3-196">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="b56a3-197">`Task.CompletedTask`není úspěch nebo neúspěch &mdash; , umožňuje spuštění dalších obslužných rutin autorizace.</span><span class="sxs-lookup"><span data-stu-id="b56a3-197">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="b56a3-198">Pokud potřebujete explicitně selhat, vraťte [kontext. Selhání](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="b56a3-198">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="b56a3-199">Aplikace umožňuje vlastníkům kontaktů upravit/odstranit/vytvořit vlastní data.</span><span class="sxs-lookup"><span data-stu-id="b56a3-199">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="b56a3-200">`ContactIsOwnerAuthorizationHandler`není nutné kontrolovat operaci předanou parametrem požadavku.</span><span class="sxs-lookup"><span data-stu-id="b56a3-200">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="b56a3-201">Vytvoření obslužné rutiny autorizace Správce</span><span class="sxs-lookup"><span data-stu-id="b56a3-201">Create a manager authorization handler</span></span>

<span data-ttu-id="b56a3-202">`ContactManagerAuthorizationHandler`Ve složce pro *autorizaci* vytvořte třídu.</span><span class="sxs-lookup"><span data-stu-id="b56a3-202">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="b56a3-203">Ověří uživatele, který pracuje `ContactManagerAuthorizationHandler` na prostředku, jako správce.</span><span class="sxs-lookup"><span data-stu-id="b56a3-203">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="b56a3-204">Pouze správci mohou schvalovat nebo odmítat změny v obsahu (nové nebo změněné).</span><span class="sxs-lookup"><span data-stu-id="b56a3-204">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="b56a3-205">Vytvoření obslužné rutiny autorizace Správce</span><span class="sxs-lookup"><span data-stu-id="b56a3-205">Create an administrator authorization handler</span></span>

<span data-ttu-id="b56a3-206">`ContactAdministratorsAuthorizationHandler`Ve složce pro *autorizaci* vytvořte třídu.</span><span class="sxs-lookup"><span data-stu-id="b56a3-206">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="b56a3-207">`ContactAdministratorsAuthorizationHandler`Ověří uživatele, který působí na prostředku, jako správce.</span><span class="sxs-lookup"><span data-stu-id="b56a3-207">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="b56a3-208">Správce může provádět všechny operace.</span><span class="sxs-lookup"><span data-stu-id="b56a3-208">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="b56a3-209">Registrace obslužných rutin autorizace</span><span class="sxs-lookup"><span data-stu-id="b56a3-209">Register the authorization handlers</span></span>

<span data-ttu-id="b56a3-210">Služby používající Entity Framework Core musí být registrovány pro [vkládání závislostí](xref:fundamentals/dependency-injection) pomocí [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="b56a3-210">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="b56a3-211">Rozhraní `ContactIsOwnerAuthorizationHandler` používá ASP.NET Core [Identity](xref:security/authentication/identity) , které je postavené na Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="b56a3-211">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="b56a3-212">Zaregistrujte obslužné rutiny u kolekce služeb, aby byly k dispozici pro `ContactsController` [vkládání závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="b56a3-212">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b56a3-213">Přidejte následující kód na konec `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b56a3-213">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="b56a3-214">`ContactAdministratorsAuthorizationHandler`a `ContactManagerAuthorizationHandler` jsou přidány jako singleton.</span><span class="sxs-lookup"><span data-stu-id="b56a3-214">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="b56a3-215">Jsou typu Singleton, protože nepoužívají EF a všechny potřebné informace jsou v `Context` parametru `HandleRequirementAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="b56a3-215">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="b56a3-216">Autorizace podpory</span><span class="sxs-lookup"><span data-stu-id="b56a3-216">Support authorization</span></span>

<span data-ttu-id="b56a3-217">V této části aktualizujete Razor stránky a přidáte třídu požadavků na operace.</span><span class="sxs-lookup"><span data-stu-id="b56a3-217">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="b56a3-218">Kontrola třídy požadavků na operace kontaktů</span><span class="sxs-lookup"><span data-stu-id="b56a3-218">Review the contact operations requirements class</span></span>

<span data-ttu-id="b56a3-219">Zkontrolujte `ContactOperations` třídu.</span><span class="sxs-lookup"><span data-stu-id="b56a3-219">Review the `ContactOperations` class.</span></span> <span data-ttu-id="b56a3-220">Tato třída obsahuje požadavky, které aplikace podporuje:</span><span class="sxs-lookup"><span data-stu-id="b56a3-220">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="b56a3-221">Vytvoření základní třídy pro Razor stránky kontaktů</span><span class="sxs-lookup"><span data-stu-id="b56a3-221">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="b56a3-222">Vytvořte základní třídu, která obsahuje služby používané na Razor stránkách kontaktů.</span><span class="sxs-lookup"><span data-stu-id="b56a3-222">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="b56a3-223">Základní třída umístí inicializační kód do jednoho umístění:</span><span class="sxs-lookup"><span data-stu-id="b56a3-223">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="b56a3-224">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="b56a3-224">The preceding code:</span></span>

* <span data-ttu-id="b56a3-225">Přidá `IAuthorizationService` službu pro přístup k obslužným rutinám autorizace.</span><span class="sxs-lookup"><span data-stu-id="b56a3-225">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="b56a3-226">Přidá Identity `UserManager` službu.</span><span class="sxs-lookup"><span data-stu-id="b56a3-226">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="b56a3-227">Přidejte `ApplicationDbContext` .</span><span class="sxs-lookup"><span data-stu-id="b56a3-227">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="b56a3-228">Aktualizace CreateModel</span><span class="sxs-lookup"><span data-stu-id="b56a3-228">Update the CreateModel</span></span>

<span data-ttu-id="b56a3-229">Aktualizujte konstruktor Create Page model tak, aby používal `DI_BasePageModel` základní třídu:</span><span class="sxs-lookup"><span data-stu-id="b56a3-229">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="b56a3-230">Aktualizujte `CreateModel.OnPostAsync` metodu na:</span><span class="sxs-lookup"><span data-stu-id="b56a3-230">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="b56a3-231">Přidejte ID uživatele do `Contact` modelu.</span><span class="sxs-lookup"><span data-stu-id="b56a3-231">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="b56a3-232">Voláním obslužné rutiny autorizace ověřte, zda má uživatel oprávnění k vytváření kontaktů.</span><span class="sxs-lookup"><span data-stu-id="b56a3-232">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="b56a3-233">Aktualizace IndexModel</span><span class="sxs-lookup"><span data-stu-id="b56a3-233">Update the IndexModel</span></span>

<span data-ttu-id="b56a3-234">Aktualizujte `OnGetAsync` metodu tak, aby se pro obecné uživatele zobrazovaly jenom schválené kontakty:</span><span class="sxs-lookup"><span data-stu-id="b56a3-234">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="b56a3-235">Aktualizace EditModel</span><span class="sxs-lookup"><span data-stu-id="b56a3-235">Update the EditModel</span></span>

<span data-ttu-id="b56a3-236">Přidejte obslužnou rutinu autorizace pro ověření, že uživatel vlastní kontakt.</span><span class="sxs-lookup"><span data-stu-id="b56a3-236">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="b56a3-237">Vzhledem k tomu, že je ověřována autorizace prostředků, není `[Authorize]` atribut dostatečně.</span><span class="sxs-lookup"><span data-stu-id="b56a3-237">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="b56a3-238">Aplikace nemá při vyhodnocování atributů přístup k prostředku.</span><span class="sxs-lookup"><span data-stu-id="b56a3-238">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="b56a3-239">Ověřování na základě prostředků musí být nezbytné.</span><span class="sxs-lookup"><span data-stu-id="b56a3-239">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="b56a3-240">Kontroly musí být provedeny, jakmile aplikace má přístup k prostředku, a to buď načtením v modelu stránky, nebo jejich načtením v rámci samotné obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="b56a3-240">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="b56a3-241">K prostředku často přistupujete předáním klíče prostředku.</span><span class="sxs-lookup"><span data-stu-id="b56a3-241">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="b56a3-242">Aktualizace DeleteModel</span><span class="sxs-lookup"><span data-stu-id="b56a3-242">Update the DeleteModel</span></span>

<span data-ttu-id="b56a3-243">Aktualizujte odstranit model stránky tak, aby používal obslužnou rutinu autorizace k ověření, jestli má uživatel oprávnění k odstranění kontaktu.</span><span class="sxs-lookup"><span data-stu-id="b56a3-243">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="b56a3-244">Vložení autorizační služby do zobrazení</span><span class="sxs-lookup"><span data-stu-id="b56a3-244">Inject the authorization service into the views</span></span>

<span data-ttu-id="b56a3-245">V současné době uživatelské rozhraní zobrazuje odkazy upravit a odstranit pro kontakty, které uživatel nemůže upravovat.</span><span class="sxs-lookup"><span data-stu-id="b56a3-245">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="b56a3-246">Vložení autorizační služby do souboru *Pages/_ViewImports. cshtml* , aby bylo dostupné pro všechna zobrazení:</span><span class="sxs-lookup"><span data-stu-id="b56a3-246">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="b56a3-247">Předchozí kód přidá několik `using` příkazů.</span><span class="sxs-lookup"><span data-stu-id="b56a3-247">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="b56a3-248">Aktualizujte odkazy pro **Úpravy** a **odstranění** na *stránkách/kontakty/index. cshtml* , aby byly vygenerovány pouze pro uživatele s příslušnými oprávněními:</span><span class="sxs-lookup"><span data-stu-id="b56a3-248">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="b56a3-249">Skrytím odkazů uživatelů, kteří nemají oprávnění ke změně dat, nebude aplikace zabezpečená.</span><span class="sxs-lookup"><span data-stu-id="b56a3-249">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="b56a3-250">Skrytím odkazů je aplikace uživatelsky přívětivější zobrazením pouze platných odkazů.</span><span class="sxs-lookup"><span data-stu-id="b56a3-250">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="b56a3-251">Uživatelé mohou napadení vygenerovaných adres URL vyvolávat a vyvolat operace úpravy a odstranění na data, která nevlastní.</span><span class="sxs-lookup"><span data-stu-id="b56a3-251">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="b56a3-252">RazorStránka nebo kontroler musí vymáhat kontroly přístupu pro zabezpečení dat.</span><span class="sxs-lookup"><span data-stu-id="b56a3-252">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="b56a3-253">Aktualizovat podrobnosti</span><span class="sxs-lookup"><span data-stu-id="b56a3-253">Update Details</span></span>

<span data-ttu-id="b56a3-254">Aktualizujte zobrazení podrobností tak, aby manažeři mohli schvalovat nebo odmítat kontakty:</span><span class="sxs-lookup"><span data-stu-id="b56a3-254">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="b56a3-255">Aktualizujte model stránky podrobností:</span><span class="sxs-lookup"><span data-stu-id="b56a3-255">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="b56a3-256">Přidání nebo odebrání uživatele k roli</span><span class="sxs-lookup"><span data-stu-id="b56a3-256">Add or remove a user to a role</span></span>

<span data-ttu-id="b56a3-257">Informace o [tomto problému najdete zde](https://github.com/dotnet/AspNetCore.Docs/issues/8502) :</span><span class="sxs-lookup"><span data-stu-id="b56a3-257">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="b56a3-258">Odebírají se oprávnění od uživatele.</span><span class="sxs-lookup"><span data-stu-id="b56a3-258">Removing privileges from a user.</span></span> <span data-ttu-id="b56a3-259">Například ztlumení uživatele v aplikaci chatu.</span><span class="sxs-lookup"><span data-stu-id="b56a3-259">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="b56a3-260">Přidávání oprávnění uživateli.</span><span class="sxs-lookup"><span data-stu-id="b56a3-260">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="b56a3-261">Rozdíly mezi výzvou a zakazující</span><span class="sxs-lookup"><span data-stu-id="b56a3-261">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="b56a3-262">Tato aplikace nastaví výchozí zásadu, která bude [vyžadovat ověřené uživatele](#rau).</span><span class="sxs-lookup"><span data-stu-id="b56a3-262">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="b56a3-263">Následující kód povoluje anonymní uživatele.</span><span class="sxs-lookup"><span data-stu-id="b56a3-263">The following code allows anonymous users.</span></span> <span data-ttu-id="b56a3-264">Anonymní uživatelé mají povoleno zobrazovat rozdíly mezi výzvou a zakázáním.</span><span class="sxs-lookup"><span data-stu-id="b56a3-264">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="b56a3-265">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="b56a3-265">In the preceding code:</span></span>

* <span data-ttu-id="b56a3-266">Pokud uživatel **není ověřen,** `ChallengeResult` je vrácena.</span><span class="sxs-lookup"><span data-stu-id="b56a3-266">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="b56a3-267">Když `ChallengeResult` se vrátí, uživatel se přesměruje na přihlašovací stránku.</span><span class="sxs-lookup"><span data-stu-id="b56a3-267">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="b56a3-268">Pokud je uživatel ověřený, ale není autorizovaný, `ForbidResult` vrátí se.</span><span class="sxs-lookup"><span data-stu-id="b56a3-268">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="b56a3-269">Když `ForbidResult` se vrátí, uživatel se přesměruje na stránku přístup byl odepřen.</span><span class="sxs-lookup"><span data-stu-id="b56a3-269">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="b56a3-270">Testování dokončené aplikace</span><span class="sxs-lookup"><span data-stu-id="b56a3-270">Test the completed app</span></span>

<span data-ttu-id="b56a3-271">Pokud jste ještě nenastavili heslo pro osazené uživatelské účty, nastavte heslo pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets#secret-manager) :</span><span class="sxs-lookup"><span data-stu-id="b56a3-271">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="b56a3-272">Vyberte silné heslo: použijte osm nebo více znaků a alespoň jedno velké písmeno, číslici a symbol.</span><span class="sxs-lookup"><span data-stu-id="b56a3-272">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="b56a3-273">Například `Passw0rd!` splňuje požadavky na silné heslo.</span><span class="sxs-lookup"><span data-stu-id="b56a3-273">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="b56a3-274">Spusťte následující příkaz ze složky projektu, kde `<PW>` je heslo:</span><span class="sxs-lookup"><span data-stu-id="b56a3-274">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="b56a3-275">Pokud má aplikace kontakty:</span><span class="sxs-lookup"><span data-stu-id="b56a3-275">If the app has contacts:</span></span>

* <span data-ttu-id="b56a3-276">Odstraní všechny záznamy v `Contact` tabulce.</span><span class="sxs-lookup"><span data-stu-id="b56a3-276">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="b56a3-277">Restartujte aplikaci a dosadíte databázi.</span><span class="sxs-lookup"><span data-stu-id="b56a3-277">Restart the app to seed the database.</span></span>

<span data-ttu-id="b56a3-278">Snadný způsob, jak otestovat dokončenou aplikaci, je spustit tři různé prohlížeče (nebo relace anonymním/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="b56a3-278">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="b56a3-279">V jednom prohlížeči Zaregistrujte nového uživatele (například `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="b56a3-279">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="b56a3-280">Přihlaste se ke každému prohlížeči pomocí jiného uživatele.</span><span class="sxs-lookup"><span data-stu-id="b56a3-280">Sign in to each browser with a different user.</span></span> <span data-ttu-id="b56a3-281">Ověřte následující operace:</span><span class="sxs-lookup"><span data-stu-id="b56a3-281">Verify the following operations:</span></span>

* <span data-ttu-id="b56a3-282">Registrovaní uživatelé mohou zobrazit všechna schválená kontaktní data.</span><span class="sxs-lookup"><span data-stu-id="b56a3-282">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="b56a3-283">Registrovaní uživatelé můžou upravovat nebo odstraňovat svá vlastní data.</span><span class="sxs-lookup"><span data-stu-id="b56a3-283">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="b56a3-284">Manažeři mohou schvalovat nebo odmítat kontaktní data.</span><span class="sxs-lookup"><span data-stu-id="b56a3-284">Managers can approve/reject contact data.</span></span> <span data-ttu-id="b56a3-285">`Details`Zobrazení zobrazuje tlačítka **schválení** a **odmítnutí** .</span><span class="sxs-lookup"><span data-stu-id="b56a3-285">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="b56a3-286">Správci mohou schvalovat nebo odmítat a upravovat nebo odstraňovat všechna data.</span><span class="sxs-lookup"><span data-stu-id="b56a3-286">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="b56a3-287">Uživatel</span><span class="sxs-lookup"><span data-stu-id="b56a3-287">User</span></span>                | <span data-ttu-id="b56a3-288">Podsazený aplikací</span><span class="sxs-lookup"><span data-stu-id="b56a3-288">Seeded by the app</span></span> | <span data-ttu-id="b56a3-289">Možnosti</span><span class="sxs-lookup"><span data-stu-id="b56a3-289">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="b56a3-290">Ne</span><span class="sxs-lookup"><span data-stu-id="b56a3-290">No</span></span>                | <span data-ttu-id="b56a3-291">Upravit nebo odstranit vlastní data.</span><span class="sxs-lookup"><span data-stu-id="b56a3-291">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="b56a3-292">Ano</span><span class="sxs-lookup"><span data-stu-id="b56a3-292">Yes</span></span>               | <span data-ttu-id="b56a3-293">Schvalovat nebo odmítat a upravovat/odstraňovat vlastní data.</span><span class="sxs-lookup"><span data-stu-id="b56a3-293">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="b56a3-294">Ano</span><span class="sxs-lookup"><span data-stu-id="b56a3-294">Yes</span></span>               | <span data-ttu-id="b56a3-295">Schválí nebo odmítne a upraví nebo odstraní všechna data.</span><span class="sxs-lookup"><span data-stu-id="b56a3-295">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="b56a3-296">V prohlížeči správce vytvořte kontakt.</span><span class="sxs-lookup"><span data-stu-id="b56a3-296">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="b56a3-297">Zkopírujte adresu URL pro odstranění a úpravy v kontaktní osobě správce.</span><span class="sxs-lookup"><span data-stu-id="b56a3-297">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="b56a3-298">Vložením těchto odkazů do prohlížeče testovacího uživatele ověříte, že testovací uživatel nemůže tyto operace provést.</span><span class="sxs-lookup"><span data-stu-id="b56a3-298">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="b56a3-299">Vytvoření úvodní aplikace</span><span class="sxs-lookup"><span data-stu-id="b56a3-299">Create the starter app</span></span>

* <span data-ttu-id="b56a3-300">Vytvoření Razor aplikace Pages s názvem "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="b56a3-300">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="b56a3-301">Vytvořte aplikaci pomocí **individuálních uživatelských účtů**.</span><span class="sxs-lookup"><span data-stu-id="b56a3-301">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="b56a3-302">Pojmenujte ho "ContactManager", aby se obor názvů shodoval s oborem názvů použitým v ukázce.</span><span class="sxs-lookup"><span data-stu-id="b56a3-302">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="b56a3-303">`-uld`Určuje LocalDB místo SQLite.</span><span class="sxs-lookup"><span data-stu-id="b56a3-303">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="b56a3-304">Přidat *modely/kontakt. cs*:</span><span class="sxs-lookup"><span data-stu-id="b56a3-304">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="b56a3-305">Generování uživatelského rozhraní `Contact` modelu</span><span class="sxs-lookup"><span data-stu-id="b56a3-305">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="b56a3-306">Vytvořit počáteční migraci a aktualizovat databázi:</span><span class="sxs-lookup"><span data-stu-id="b56a3-306">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="b56a3-307">Pokud se zobrazí chyba s `dotnet aspnet-codegenerator razorpage` příkazem, přečtěte si [Tento problém GitHub](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="b56a3-307">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="b56a3-308">Aktualizujte kotvu **ContactManager** v souboru *Pages/shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="b56a3-308">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="b56a3-309">Testování aplikace vytvořením, úpravou a odstraněním kontaktu</span><span class="sxs-lookup"><span data-stu-id="b56a3-309">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="b56a3-310">Dosazení databáze</span><span class="sxs-lookup"><span data-stu-id="b56a3-310">Seed the database</span></span>

<span data-ttu-id="b56a3-311">Do složky *data* přidejte třídu [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) :</span><span class="sxs-lookup"><span data-stu-id="b56a3-311">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="b56a3-312">Zavolat `SeedData.Initialize` z `Main` :</span><span class="sxs-lookup"><span data-stu-id="b56a3-312">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="b56a3-313">Otestujte, že aplikace dosazený databázi.</span><span class="sxs-lookup"><span data-stu-id="b56a3-313">Test that the app seeded the database.</span></span> <span data-ttu-id="b56a3-314">Pokud je ve službě Contact DB nějaký řádek, metoda počáteční hodnoty se nespustí.</span><span class="sxs-lookup"><span data-stu-id="b56a3-314">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="b56a3-315">V tomto kurzu se dozvíte, jak vytvořit webovou aplikaci ASP.NET Core s uživatelskými daty chráněnými autorizací.</span><span class="sxs-lookup"><span data-stu-id="b56a3-315">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="b56a3-316">Zobrazuje seznam kontaktů ověřovaných (registrovaných) uživatelů.</span><span class="sxs-lookup"><span data-stu-id="b56a3-316">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="b56a3-317">Existují tři skupiny zabezpečení:</span><span class="sxs-lookup"><span data-stu-id="b56a3-317">There are three security groups:</span></span>

* <span data-ttu-id="b56a3-318">**Registrovaní uživatelé** můžou zobrazit všechna schválená data a můžou upravovat nebo odstraňovat vlastní data.</span><span class="sxs-lookup"><span data-stu-id="b56a3-318">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="b56a3-319">**Manažeři** mohou schvalovat nebo odmítat kontaktní data.</span><span class="sxs-lookup"><span data-stu-id="b56a3-319">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="b56a3-320">Uživatelé vidí jenom schválené kontakty.</span><span class="sxs-lookup"><span data-stu-id="b56a3-320">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="b56a3-321">**Správci** mohou schvalovat nebo odmítat a upravovat nebo odstraňovat jakákoli data.</span><span class="sxs-lookup"><span data-stu-id="b56a3-321">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="b56a3-322">Na následujícím obrázku je uživatel Rick ( `rick@example.com` ) přihlášen.</span><span class="sxs-lookup"><span data-stu-id="b56a3-322">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="b56a3-323">Rick může zobrazit pouze schválené kontakty a **Upravit** / **Odstranit** / **nové** odkazy pro své kontakty.</span><span class="sxs-lookup"><span data-stu-id="b56a3-323">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="b56a3-324">Jenom poslední záznam vytvořený pomocí Rick zobrazí odkazy pro **Úpravy** a **odstranění** .</span><span class="sxs-lookup"><span data-stu-id="b56a3-324">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="b56a3-325">Ostatní uživatelé uvidí poslední záznam, dokud správce nebo správce nezmění stav na schváleno.</span><span class="sxs-lookup"><span data-stu-id="b56a3-325">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Snímek obrazovky zobrazující přihlášený Rick](secure-data/_static/rick.png)

<span data-ttu-id="b56a3-327">Na následujícím obrázku `manager@contoso.com` je přihlášen a v roli manažera:</span><span class="sxs-lookup"><span data-stu-id="b56a3-327">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Snímek obrazovky zobrazující manager@contoso.com přihlášený](secure-data/_static/manager1.png)

<span data-ttu-id="b56a3-329">Následující obrázek ukazuje zobrazení podrobností o kontaktu pro správce:</span><span class="sxs-lookup"><span data-stu-id="b56a3-329">The following image shows the managers details view of a contact:</span></span>

![Zobrazení kontaktu manažera](secure-data/_static/manager.png)

<span data-ttu-id="b56a3-331">Tlačítka **schválení** a **odmítnutí** se zobrazují pouze pro manažery a správce.</span><span class="sxs-lookup"><span data-stu-id="b56a3-331">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="b56a3-332">Na následujícím obrázku `admin@contoso.com` je přihlášen a v roli správce:</span><span class="sxs-lookup"><span data-stu-id="b56a3-332">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Snímek obrazovky zobrazující admin@contoso.com přihlášený](secure-data/_static/admin.png)

<span data-ttu-id="b56a3-334">Správce má všechna oprávnění.</span><span class="sxs-lookup"><span data-stu-id="b56a3-334">The administrator has all privileges.</span></span> <span data-ttu-id="b56a3-335">Může číst, upravovat a odstraňovat jakékoli kontakty a měnit stav kontaktů.</span><span class="sxs-lookup"><span data-stu-id="b56a3-335">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="b56a3-336">Aplikace byla vytvořena pomocí [generování uživatelského rozhraní](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) tohoto `Contact` modelu:</span><span class="sxs-lookup"><span data-stu-id="b56a3-336">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="b56a3-337">Ukázka obsahuje následující obslužné rutiny autorizace:</span><span class="sxs-lookup"><span data-stu-id="b56a3-337">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="b56a3-338">`ContactIsOwnerAuthorizationHandler`: Zajistí, že uživatel může upravovat pouze svá data.</span><span class="sxs-lookup"><span data-stu-id="b56a3-338">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="b56a3-339">`ContactManagerAuthorizationHandler`: Umožňuje správcům schvalovat nebo odmítat kontakty.</span><span class="sxs-lookup"><span data-stu-id="b56a3-339">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="b56a3-340">`ContactAdministratorsAuthorizationHandler`: Umožňuje správcům schvalovat nebo odmítat kontakty a upravovat nebo odstraňovat kontakty.</span><span class="sxs-lookup"><span data-stu-id="b56a3-340">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b56a3-341">Požadavky</span><span class="sxs-lookup"><span data-stu-id="b56a3-341">Prerequisites</span></span>

<span data-ttu-id="b56a3-342">Tento kurz je pokročilý.</span><span class="sxs-lookup"><span data-stu-id="b56a3-342">This tutorial is advanced.</span></span> <span data-ttu-id="b56a3-343">Měli byste být obeznámeni s:</span><span class="sxs-lookup"><span data-stu-id="b56a3-343">You should be familiar with:</span></span>

* [<span data-ttu-id="b56a3-344">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b56a3-344">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="b56a3-345">Ověřování</span><span class="sxs-lookup"><span data-stu-id="b56a3-345">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="b56a3-346">Potvrzení účtu a obnovení hesla</span><span class="sxs-lookup"><span data-stu-id="b56a3-346">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="b56a3-347">Autorizace</span><span class="sxs-lookup"><span data-stu-id="b56a3-347">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="b56a3-348">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="b56a3-348">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="b56a3-349">Úvodní a dokončená aplikace</span><span class="sxs-lookup"><span data-stu-id="b56a3-349">The starter and completed app</span></span>

<span data-ttu-id="b56a3-350">[Stáhněte](xref:index#how-to-download-a-sample) [dokončenou](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b56a3-350">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="b56a3-351">[Otestujte](#test-the-completed-app) dokončenou aplikaci, abyste se seznámili se svými funkcemi zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="b56a3-351">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="b56a3-352">Úvodní aplikace</span><span class="sxs-lookup"><span data-stu-id="b56a3-352">The starter app</span></span>

<span data-ttu-id="b56a3-353">[Stáhněte](xref:index#how-to-download-a-sample) si [úvodní](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b56a3-353">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="b56a3-354">Spusťte aplikaci, klepněte na odkaz **ContactManager** a ověřte, že můžete vytvořit, upravit a odstranit kontakt.</span><span class="sxs-lookup"><span data-stu-id="b56a3-354">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="b56a3-355">Zabezpečená uživatelská data</span><span class="sxs-lookup"><span data-stu-id="b56a3-355">Secure user data</span></span>

<span data-ttu-id="b56a3-356">V následujících částech najdete všechny hlavní kroky k vytvoření aplikace zabezpečených uživatelských dat.</span><span class="sxs-lookup"><span data-stu-id="b56a3-356">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="b56a3-357">Může to být užitečné pro odkazování na dokončený projekt.</span><span class="sxs-lookup"><span data-stu-id="b56a3-357">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="b56a3-358">Spojit kontaktní údaje s uživatelem</span><span class="sxs-lookup"><span data-stu-id="b56a3-358">Tie the contact data to the user</span></span>

<span data-ttu-id="b56a3-359">Pomocí [Identity](xref:security/authentication/identity) ID uživatele ASP.NET zajistěte, aby mohli uživatelé upravovat data, ale ne jiná data uživatelů.</span><span class="sxs-lookup"><span data-stu-id="b56a3-359">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="b56a3-360">Přidejte `OwnerID` a `ContactStatus` do `Contact` modelu:</span><span class="sxs-lookup"><span data-stu-id="b56a3-360">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="b56a3-361">`OwnerID`je ID uživatele z `AspNetUser` tabulky v [Identity](xref:security/authentication/identity) databázi.</span><span class="sxs-lookup"><span data-stu-id="b56a3-361">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="b56a3-362">`Status`Pole určuje, jestli je kontakt viditelný pro obecné uživatele.</span><span class="sxs-lookup"><span data-stu-id="b56a3-362">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="b56a3-363">Vytvořte novou migraci a aktualizujte databázi:</span><span class="sxs-lookup"><span data-stu-id="b56a3-363">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="b56a3-364">Přidat služby rolí doIdentity</span><span class="sxs-lookup"><span data-stu-id="b56a3-364">Add Role services to Identity</span></span>

<span data-ttu-id="b56a3-365">Připojit [Přidat role](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) pro přidání služeb role:</span><span class="sxs-lookup"><span data-stu-id="b56a3-365">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="b56a3-366">Vyžadovat ověřené uživatele</span><span class="sxs-lookup"><span data-stu-id="b56a3-366">Require authenticated users</span></span>

<span data-ttu-id="b56a3-367">Nastavte výchozí zásadu ověřování tak, aby vyžadovala ověření uživatelů:</span><span class="sxs-lookup"><span data-stu-id="b56a3-367">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="b56a3-368">Ověřování můžete na Razor úrovni stránky, řadiče nebo akce pomocí atributu odsouhlasit `[AllowAnonymous]` .</span><span class="sxs-lookup"><span data-stu-id="b56a3-368">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="b56a3-369">Nastavení výchozích zásad ověřování, aby vyžadovaly ověření uživatelů, chrání nově přidané Razor stránky a řadiče.</span><span class="sxs-lookup"><span data-stu-id="b56a3-369">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="b56a3-370">Pokud je ve výchozím nastavení vyžadováno ověřování, je bezpečnější než spoléhání na nové řadiče a Razor stránky pro zahrnutí `[Authorize]` atributu.</span><span class="sxs-lookup"><span data-stu-id="b56a3-370">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="b56a3-371">Přidejte [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) na stránky rejstřík, o a kontakt, aby anonymní uživatelé mohli získat informace o lokalitě před jejich registrací.</span><span class="sxs-lookup"><span data-stu-id="b56a3-371">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="b56a3-372">Konfigurace testovacího účtu</span><span class="sxs-lookup"><span data-stu-id="b56a3-372">Configure the test account</span></span>

<span data-ttu-id="b56a3-373">`SeedData`Třída vytvoří dva účty: správce a manažer.</span><span class="sxs-lookup"><span data-stu-id="b56a3-373">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="b56a3-374">Pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets) nastavte heslo pro tyto účty.</span><span class="sxs-lookup"><span data-stu-id="b56a3-374">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="b56a3-375">Nastavte heslo z adresáře projektu (adresář obsahující *program.cs*):</span><span class="sxs-lookup"><span data-stu-id="b56a3-375">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="b56a3-376">Pokud není zadáno silné heslo, je vyvolána výjimka, když `SeedData.Initialize` je volána metoda.</span><span class="sxs-lookup"><span data-stu-id="b56a3-376">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="b56a3-377">Aktualizace `Main` pro použití testovacího hesla:</span><span class="sxs-lookup"><span data-stu-id="b56a3-377">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="b56a3-378">Vytvoření testovacích účtů a aktualizace kontaktů</span><span class="sxs-lookup"><span data-stu-id="b56a3-378">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="b56a3-379">Aktualizujte `Initialize` metodu ve `SeedData` třídě pro vytvoření testovacích účtů:</span><span class="sxs-lookup"><span data-stu-id="b56a3-379">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="b56a3-380">Přidejte ID uživatele správce a `ContactStatus` ke kontaktům.</span><span class="sxs-lookup"><span data-stu-id="b56a3-380">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="b56a3-381">Poznamenejte si jednu z kontaktů "odeslané" a jednu "zamítnutou".</span><span class="sxs-lookup"><span data-stu-id="b56a3-381">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="b56a3-382">Přidejte ke všem kontaktům ID a stav uživatele.</span><span class="sxs-lookup"><span data-stu-id="b56a3-382">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="b56a3-383">Zobrazí se pouze jeden kontakt:</span><span class="sxs-lookup"><span data-stu-id="b56a3-383">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="b56a3-384">Vytváření obslužných rutin autorizace vlastníka, správce a správce</span><span class="sxs-lookup"><span data-stu-id="b56a3-384">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="b56a3-385">Vytvořte složku *autorizace* a `ContactIsOwnerAuthorizationHandler` v ní vytvořte třídu.</span><span class="sxs-lookup"><span data-stu-id="b56a3-385">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="b56a3-386">`ContactIsOwnerAuthorizationHandler`Ověřuje, že uživatel, který pracuje na prostředku, je vlastníkem prostředku.</span><span class="sxs-lookup"><span data-stu-id="b56a3-386">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="b56a3-387">`ContactIsOwnerAuthorizationHandler`Kontext volání [. Úspěšné](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , pokud je aktuální ověřený uživatel vlastníkem kontaktu.</span><span class="sxs-lookup"><span data-stu-id="b56a3-387">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="b56a3-388">Obslužné rutiny autorizace obecně:</span><span class="sxs-lookup"><span data-stu-id="b56a3-388">Authorization handlers generally:</span></span>

* <span data-ttu-id="b56a3-389">Vrátí `context.Succeed` se, pokud jsou splněny požadavky.</span><span class="sxs-lookup"><span data-stu-id="b56a3-389">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="b56a3-390">Vrátí se `Task.CompletedTask` , pokud nejsou splněné požadavky.</span><span class="sxs-lookup"><span data-stu-id="b56a3-390">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="b56a3-391">`Task.CompletedTask`není úspěch nebo neúspěch &mdash; , umožňuje spuštění dalších obslužných rutin autorizace.</span><span class="sxs-lookup"><span data-stu-id="b56a3-391">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="b56a3-392">Pokud potřebujete explicitně selhat, vraťte [kontext. Selhání](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="b56a3-392">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="b56a3-393">Aplikace umožňuje vlastníkům kontaktů upravit/odstranit/vytvořit vlastní data.</span><span class="sxs-lookup"><span data-stu-id="b56a3-393">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="b56a3-394">`ContactIsOwnerAuthorizationHandler`není nutné kontrolovat operaci předanou parametrem požadavku.</span><span class="sxs-lookup"><span data-stu-id="b56a3-394">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="b56a3-395">Vytvoření obslužné rutiny autorizace Správce</span><span class="sxs-lookup"><span data-stu-id="b56a3-395">Create a manager authorization handler</span></span>

<span data-ttu-id="b56a3-396">`ContactManagerAuthorizationHandler`Ve složce pro *autorizaci* vytvořte třídu.</span><span class="sxs-lookup"><span data-stu-id="b56a3-396">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="b56a3-397">Ověří uživatele, který pracuje `ContactManagerAuthorizationHandler` na prostředku, jako správce.</span><span class="sxs-lookup"><span data-stu-id="b56a3-397">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="b56a3-398">Pouze správci mohou schvalovat nebo odmítat změny v obsahu (nové nebo změněné).</span><span class="sxs-lookup"><span data-stu-id="b56a3-398">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="b56a3-399">Vytvoření obslužné rutiny autorizace Správce</span><span class="sxs-lookup"><span data-stu-id="b56a3-399">Create an administrator authorization handler</span></span>

<span data-ttu-id="b56a3-400">`ContactAdministratorsAuthorizationHandler`Ve složce pro *autorizaci* vytvořte třídu.</span><span class="sxs-lookup"><span data-stu-id="b56a3-400">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="b56a3-401">`ContactAdministratorsAuthorizationHandler`Ověří uživatele, který působí na prostředku, jako správce.</span><span class="sxs-lookup"><span data-stu-id="b56a3-401">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="b56a3-402">Správce může provádět všechny operace.</span><span class="sxs-lookup"><span data-stu-id="b56a3-402">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="b56a3-403">Registrace obslužných rutin autorizace</span><span class="sxs-lookup"><span data-stu-id="b56a3-403">Register the authorization handlers</span></span>

<span data-ttu-id="b56a3-404">Služby používající Entity Framework Core musí být registrovány pro [vkládání závislostí](xref:fundamentals/dependency-injection) pomocí [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="b56a3-404">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="b56a3-405">Rozhraní `ContactIsOwnerAuthorizationHandler` používá ASP.NET Core [Identity](xref:security/authentication/identity) , které je postavené na Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="b56a3-405">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="b56a3-406">Zaregistrujte obslužné rutiny u kolekce služeb, aby byly k dispozici pro `ContactsController` [vkládání závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="b56a3-406">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b56a3-407">Přidejte následující kód na konec `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b56a3-407">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="b56a3-408">`ContactAdministratorsAuthorizationHandler`a `ContactManagerAuthorizationHandler` jsou přidány jako singleton.</span><span class="sxs-lookup"><span data-stu-id="b56a3-408">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="b56a3-409">Jsou typu Singleton, protože nepoužívají EF a všechny potřebné informace jsou v `Context` parametru `HandleRequirementAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="b56a3-409">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="b56a3-410">Autorizace podpory</span><span class="sxs-lookup"><span data-stu-id="b56a3-410">Support authorization</span></span>

<span data-ttu-id="b56a3-411">V této části aktualizujete Razor stránky a přidáte třídu požadavků na operace.</span><span class="sxs-lookup"><span data-stu-id="b56a3-411">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="b56a3-412">Kontrola třídy požadavků na operace kontaktů</span><span class="sxs-lookup"><span data-stu-id="b56a3-412">Review the contact operations requirements class</span></span>

<span data-ttu-id="b56a3-413">Zkontrolujte `ContactOperations` třídu.</span><span class="sxs-lookup"><span data-stu-id="b56a3-413">Review the `ContactOperations` class.</span></span> <span data-ttu-id="b56a3-414">Tato třída obsahuje požadavky, které aplikace podporuje:</span><span class="sxs-lookup"><span data-stu-id="b56a3-414">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="b56a3-415">Vytvoření základní třídy pro Razor stránky kontaktů</span><span class="sxs-lookup"><span data-stu-id="b56a3-415">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="b56a3-416">Vytvořte základní třídu, která obsahuje služby používané na Razor stránkách kontaktů.</span><span class="sxs-lookup"><span data-stu-id="b56a3-416">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="b56a3-417">Základní třída umístí inicializační kód do jednoho umístění:</span><span class="sxs-lookup"><span data-stu-id="b56a3-417">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="b56a3-418">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="b56a3-418">The preceding code:</span></span>

* <span data-ttu-id="b56a3-419">Přidá `IAuthorizationService` službu pro přístup k obslužným rutinám autorizace.</span><span class="sxs-lookup"><span data-stu-id="b56a3-419">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="b56a3-420">Přidá Identity `UserManager` službu.</span><span class="sxs-lookup"><span data-stu-id="b56a3-420">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="b56a3-421">Přidejte `ApplicationDbContext` .</span><span class="sxs-lookup"><span data-stu-id="b56a3-421">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="b56a3-422">Aktualizace CreateModel</span><span class="sxs-lookup"><span data-stu-id="b56a3-422">Update the CreateModel</span></span>

<span data-ttu-id="b56a3-423">Aktualizujte konstruktor Create Page model tak, aby používal `DI_BasePageModel` základní třídu:</span><span class="sxs-lookup"><span data-stu-id="b56a3-423">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="b56a3-424">Aktualizujte `CreateModel.OnPostAsync` metodu na:</span><span class="sxs-lookup"><span data-stu-id="b56a3-424">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="b56a3-425">Přidejte ID uživatele do `Contact` modelu.</span><span class="sxs-lookup"><span data-stu-id="b56a3-425">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="b56a3-426">Voláním obslužné rutiny autorizace ověřte, zda má uživatel oprávnění k vytváření kontaktů.</span><span class="sxs-lookup"><span data-stu-id="b56a3-426">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="b56a3-427">Aktualizace IndexModel</span><span class="sxs-lookup"><span data-stu-id="b56a3-427">Update the IndexModel</span></span>

<span data-ttu-id="b56a3-428">Aktualizujte `OnGetAsync` metodu tak, aby se pro obecné uživatele zobrazovaly jenom schválené kontakty:</span><span class="sxs-lookup"><span data-stu-id="b56a3-428">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="b56a3-429">Aktualizace EditModel</span><span class="sxs-lookup"><span data-stu-id="b56a3-429">Update the EditModel</span></span>

<span data-ttu-id="b56a3-430">Přidejte obslužnou rutinu autorizace pro ověření, že uživatel vlastní kontakt.</span><span class="sxs-lookup"><span data-stu-id="b56a3-430">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="b56a3-431">Vzhledem k tomu, že je ověřována autorizace prostředků, není `[Authorize]` atribut dostatečně.</span><span class="sxs-lookup"><span data-stu-id="b56a3-431">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="b56a3-432">Aplikace nemá při vyhodnocování atributů přístup k prostředku.</span><span class="sxs-lookup"><span data-stu-id="b56a3-432">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="b56a3-433">Ověřování na základě prostředků musí být nezbytné.</span><span class="sxs-lookup"><span data-stu-id="b56a3-433">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="b56a3-434">Kontroly musí být provedeny, jakmile aplikace má přístup k prostředku, a to buď načtením v modelu stránky, nebo jejich načtením v rámci samotné obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="b56a3-434">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="b56a3-435">K prostředku často přistupujete předáním klíče prostředku.</span><span class="sxs-lookup"><span data-stu-id="b56a3-435">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="b56a3-436">Aktualizace DeleteModel</span><span class="sxs-lookup"><span data-stu-id="b56a3-436">Update the DeleteModel</span></span>

<span data-ttu-id="b56a3-437">Aktualizujte odstranit model stránky tak, aby používal obslužnou rutinu autorizace k ověření, jestli má uživatel oprávnění k odstranění kontaktu.</span><span class="sxs-lookup"><span data-stu-id="b56a3-437">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="b56a3-438">Vložení autorizační služby do zobrazení</span><span class="sxs-lookup"><span data-stu-id="b56a3-438">Inject the authorization service into the views</span></span>

<span data-ttu-id="b56a3-439">V současné době uživatelské rozhraní zobrazuje odkazy upravit a odstranit pro kontakty, které uživatel nemůže upravovat.</span><span class="sxs-lookup"><span data-stu-id="b56a3-439">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="b56a3-440">Vložení autorizační služby do souboru *views/_ViewImports. cshtml* , aby byla dostupná pro všechna zobrazení:</span><span class="sxs-lookup"><span data-stu-id="b56a3-440">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="b56a3-441">Předchozí kód přidá několik `using` příkazů.</span><span class="sxs-lookup"><span data-stu-id="b56a3-441">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="b56a3-442">Aktualizujte odkazy pro **Úpravy** a **odstranění** na *stránkách/kontakty/index. cshtml* , aby byly vygenerovány pouze pro uživatele s příslušnými oprávněními:</span><span class="sxs-lookup"><span data-stu-id="b56a3-442">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="b56a3-443">Skrytím odkazů uživatelů, kteří nemají oprávnění ke změně dat, nebude aplikace zabezpečená.</span><span class="sxs-lookup"><span data-stu-id="b56a3-443">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="b56a3-444">Skrytím odkazů je aplikace uživatelsky přívětivější zobrazením pouze platných odkazů.</span><span class="sxs-lookup"><span data-stu-id="b56a3-444">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="b56a3-445">Uživatelé mohou napadení vygenerovaných adres URL vyvolávat a vyvolat operace úpravy a odstranění na data, která nevlastní.</span><span class="sxs-lookup"><span data-stu-id="b56a3-445">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="b56a3-446">RazorStránka nebo kontroler musí vymáhat kontroly přístupu pro zabezpečení dat.</span><span class="sxs-lookup"><span data-stu-id="b56a3-446">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="b56a3-447">Aktualizovat podrobnosti</span><span class="sxs-lookup"><span data-stu-id="b56a3-447">Update Details</span></span>

<span data-ttu-id="b56a3-448">Aktualizujte zobrazení podrobností tak, aby manažeři mohli schvalovat nebo odmítat kontakty:</span><span class="sxs-lookup"><span data-stu-id="b56a3-448">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="b56a3-449">Aktualizujte model stránky podrobností:</span><span class="sxs-lookup"><span data-stu-id="b56a3-449">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="b56a3-450">Přidání nebo odebrání uživatele k roli</span><span class="sxs-lookup"><span data-stu-id="b56a3-450">Add or remove a user to a role</span></span>

<span data-ttu-id="b56a3-451">Informace o [tomto problému najdete zde](https://github.com/dotnet/AspNetCore.Docs/issues/8502) :</span><span class="sxs-lookup"><span data-stu-id="b56a3-451">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="b56a3-452">Odebírají se oprávnění od uživatele.</span><span class="sxs-lookup"><span data-stu-id="b56a3-452">Removing privileges from a user.</span></span> <span data-ttu-id="b56a3-453">Například ztlumení uživatele v aplikaci chatu.</span><span class="sxs-lookup"><span data-stu-id="b56a3-453">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="b56a3-454">Přidávání oprávnění uživateli.</span><span class="sxs-lookup"><span data-stu-id="b56a3-454">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="b56a3-455">Testování dokončené aplikace</span><span class="sxs-lookup"><span data-stu-id="b56a3-455">Test the completed app</span></span>

<span data-ttu-id="b56a3-456">Pokud jste ještě nenastavili heslo pro osazené uživatelské účty, nastavte heslo pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets#secret-manager) :</span><span class="sxs-lookup"><span data-stu-id="b56a3-456">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="b56a3-457">Vyberte silné heslo: použijte osm nebo více znaků a alespoň jedno velké písmeno, číslici a symbol.</span><span class="sxs-lookup"><span data-stu-id="b56a3-457">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="b56a3-458">Například `Passw0rd!` splňuje požadavky na silné heslo.</span><span class="sxs-lookup"><span data-stu-id="b56a3-458">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="b56a3-459">Spusťte následující příkaz ze složky projektu, kde `<PW>` je heslo:</span><span class="sxs-lookup"><span data-stu-id="b56a3-459">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="b56a3-460">Zrušení a aktualizace databáze</span><span class="sxs-lookup"><span data-stu-id="b56a3-460">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="b56a3-461">Restartujte aplikaci a dosadíte databázi.</span><span class="sxs-lookup"><span data-stu-id="b56a3-461">Restart the app to seed the database.</span></span>

<span data-ttu-id="b56a3-462">Snadný způsob, jak otestovat dokončenou aplikaci, je spustit tři různé prohlížeče (nebo relace anonymním/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="b56a3-462">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="b56a3-463">V jednom prohlížeči Zaregistrujte nového uživatele (například `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="b56a3-463">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="b56a3-464">Přihlaste se ke každému prohlížeči pomocí jiného uživatele.</span><span class="sxs-lookup"><span data-stu-id="b56a3-464">Sign in to each browser with a different user.</span></span> <span data-ttu-id="b56a3-465">Ověřte následující operace:</span><span class="sxs-lookup"><span data-stu-id="b56a3-465">Verify the following operations:</span></span>

* <span data-ttu-id="b56a3-466">Registrovaní uživatelé mohou zobrazit všechna schválená kontaktní data.</span><span class="sxs-lookup"><span data-stu-id="b56a3-466">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="b56a3-467">Registrovaní uživatelé můžou upravovat nebo odstraňovat svá vlastní data.</span><span class="sxs-lookup"><span data-stu-id="b56a3-467">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="b56a3-468">Manažeři mohou schvalovat nebo odmítat kontaktní data.</span><span class="sxs-lookup"><span data-stu-id="b56a3-468">Managers can approve/reject contact data.</span></span> <span data-ttu-id="b56a3-469">`Details`Zobrazení zobrazuje tlačítka **schválení** a **odmítnutí** .</span><span class="sxs-lookup"><span data-stu-id="b56a3-469">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="b56a3-470">Správci mohou schvalovat nebo odmítat a upravovat nebo odstraňovat všechna data.</span><span class="sxs-lookup"><span data-stu-id="b56a3-470">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="b56a3-471">Uživatel</span><span class="sxs-lookup"><span data-stu-id="b56a3-471">User</span></span>                | <span data-ttu-id="b56a3-472">Podsazený aplikací</span><span class="sxs-lookup"><span data-stu-id="b56a3-472">Seeded by the app</span></span> | <span data-ttu-id="b56a3-473">Možnosti</span><span class="sxs-lookup"><span data-stu-id="b56a3-473">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="b56a3-474">Ne</span><span class="sxs-lookup"><span data-stu-id="b56a3-474">No</span></span>                | <span data-ttu-id="b56a3-475">Upravit nebo odstranit vlastní data.</span><span class="sxs-lookup"><span data-stu-id="b56a3-475">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="b56a3-476">Ano</span><span class="sxs-lookup"><span data-stu-id="b56a3-476">Yes</span></span>               | <span data-ttu-id="b56a3-477">Schvalovat nebo odmítat a upravovat/odstraňovat vlastní data.</span><span class="sxs-lookup"><span data-stu-id="b56a3-477">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="b56a3-478">Ano</span><span class="sxs-lookup"><span data-stu-id="b56a3-478">Yes</span></span>               | <span data-ttu-id="b56a3-479">Schválí nebo odmítne a upraví nebo odstraní všechna data.</span><span class="sxs-lookup"><span data-stu-id="b56a3-479">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="b56a3-480">V prohlížeči správce vytvořte kontakt.</span><span class="sxs-lookup"><span data-stu-id="b56a3-480">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="b56a3-481">Zkopírujte adresu URL pro odstranění a úpravy v kontaktní osobě správce.</span><span class="sxs-lookup"><span data-stu-id="b56a3-481">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="b56a3-482">Vložením těchto odkazů do prohlížeče testovacího uživatele ověříte, že testovací uživatel nemůže tyto operace provést.</span><span class="sxs-lookup"><span data-stu-id="b56a3-482">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="b56a3-483">Vytvoření úvodní aplikace</span><span class="sxs-lookup"><span data-stu-id="b56a3-483">Create the starter app</span></span>

* <span data-ttu-id="b56a3-484">Vytvoření Razor aplikace Pages s názvem "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="b56a3-484">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="b56a3-485">Vytvořte aplikaci pomocí **individuálních uživatelských účtů**.</span><span class="sxs-lookup"><span data-stu-id="b56a3-485">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="b56a3-486">Pojmenujte ho "ContactManager", aby se obor názvů shodoval s oborem názvů použitým v ukázce.</span><span class="sxs-lookup"><span data-stu-id="b56a3-486">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="b56a3-487">`-uld`Určuje LocalDB místo SQLite.</span><span class="sxs-lookup"><span data-stu-id="b56a3-487">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="b56a3-488">Přidat *modely/kontakt. cs*:</span><span class="sxs-lookup"><span data-stu-id="b56a3-488">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="b56a3-489">Generování uživatelského rozhraní `Contact` modelu</span><span class="sxs-lookup"><span data-stu-id="b56a3-489">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="b56a3-490">Vytvořit počáteční migraci a aktualizovat databázi:</span><span class="sxs-lookup"><span data-stu-id="b56a3-490">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="b56a3-491">Aktualizujte kotvu **ContactManager** v souboru *pages/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="b56a3-491">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="b56a3-492">Testování aplikace vytvořením, úpravou a odstraněním kontaktu</span><span class="sxs-lookup"><span data-stu-id="b56a3-492">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="b56a3-493">Dosazení databáze</span><span class="sxs-lookup"><span data-stu-id="b56a3-493">Seed the database</span></span>

<span data-ttu-id="b56a3-494">Do složky *data* přidejte třídu [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) .</span><span class="sxs-lookup"><span data-stu-id="b56a3-494">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="b56a3-495">Zavolat `SeedData.Initialize` z `Main` :</span><span class="sxs-lookup"><span data-stu-id="b56a3-495">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="b56a3-496">Otestujte, že aplikace dosazený databázi.</span><span class="sxs-lookup"><span data-stu-id="b56a3-496">Test that the app seeded the database.</span></span> <span data-ttu-id="b56a3-497">Pokud je ve službě Contact DB nějaký řádek, metoda počáteční hodnoty se nespustí.</span><span class="sxs-lookup"><span data-stu-id="b56a3-497">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="b56a3-498">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b56a3-498">Additional resources</span></span>

* [<span data-ttu-id="b56a3-499">Vytvoření webové aplikace .NET Core využívající SQL Database ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="b56a3-499">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="b56a3-500">[ASP.NET Core autorizačního prostředí](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="b56a3-500">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="b56a3-501">Tato laboratoř obsahuje další podrobnosti o funkcích zabezpečení, které jsou představené v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="b56a3-501">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="b56a3-502">Vlastní autorizace na základě zásad</span><span class="sxs-lookup"><span data-stu-id="b56a3-502">Custom policy-based authorization</span></span>](xref:security/authorization/policies)

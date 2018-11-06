---
uid: identity/overview/extensibility/change-primary-key-for-users-in-aspnet-identity
title: Změna primárního klíče pro uživatele v identitě ASP.NET | Dokumentace Microsoftu
author: Rick-Anderson
description: Výchozí webová aplikace v sadě Visual Studio 2013, používá řetězcovou hodnotu pro klíč pro uživatelské účty. ASP.NET Identity umožňuje změnit typ...
ms.author: riande
ms.date: 09/30/2014
ms.assetid: 44925849-5762-4504-a8cd-8f0cd06f6dc3
msc.legacyurl: /identity/overview/extensibility/change-primary-key-for-users-in-aspnet-identity
msc.type: authoredcontent
ms.openlocfilehash: d2856ce1ca61a29e091bfbd16647b673e6fc659b
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021102"
---
<a name="change-primary-key-for-users-in-aspnet-identity"></a><span data-ttu-id="1cba5-104">Změna primárního klíče uživatelů v ASP.NET Identity</span><span class="sxs-lookup"><span data-stu-id="1cba5-104">Change Primary Key for Users in ASP.NET Identity</span></span>
====================
<span data-ttu-id="1cba5-105">podle [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="1cba5-105">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="1cba5-106">Výchozí webová aplikace v sadě Visual Studio 2013, používá řetězcovou hodnotu pro klíč pro uživatelské účty.</span><span class="sxs-lookup"><span data-stu-id="1cba5-106">In Visual Studio 2013, the default web application uses a string value for the key for user accounts.</span></span> <span data-ttu-id="1cba5-107">ASP.NET Identity umožňuje změnit typ klíče pro splnění požadavků na data.</span><span class="sxs-lookup"><span data-stu-id="1cba5-107">ASP.NET Identity enables you to change the type of the key to meet your data requirements.</span></span> <span data-ttu-id="1cba5-108">Můžete například změnit typ klíče z řetězce na celé číslo.</span><span class="sxs-lookup"><span data-stu-id="1cba5-108">For example, you can change the type of the key from a string to an integer.</span></span>
> 
> <span data-ttu-id="1cba5-109">Toto téma ukazuje, jak začít s výchozí webové aplikace změňte klíč účtu uživatele na celé číslo.</span><span class="sxs-lookup"><span data-stu-id="1cba5-109">This topic shows how to start with the default web application and change the user account key to an integer.</span></span> <span data-ttu-id="1cba5-110">Stejné změny můžete použít k implementaci libovolného typu klíče ve vašem projektu.</span><span class="sxs-lookup"><span data-stu-id="1cba5-110">You can use the same modifications to implement any type of key in your project.</span></span> <span data-ttu-id="1cba5-111">Ukazuje, jak tyto změny ve výchozí webové aplikace, ale můžete použít podobné změny do vlastní aplikace.</span><span class="sxs-lookup"><span data-stu-id="1cba5-111">It shows how to make these changes in the default web application, but you could apply similar modifications to a customized application.</span></span> <span data-ttu-id="1cba5-112">Zobrazuje změny potřebné při práci s MVC nebo webového formuláře.</span><span class="sxs-lookup"><span data-stu-id="1cba5-112">It shows the changes needed when working with MVC or Web Forms.</span></span>
> 
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="1cba5-113">V tomto kurzu použili verze softwaru</span><span class="sxs-lookup"><span data-stu-id="1cba5-113">Software versions used in the tutorial</span></span>
> 
> 
> - <span data-ttu-id="1cba5-114">Visual Studio 2013 s aktualizací Update 2 (nebo novější)</span><span class="sxs-lookup"><span data-stu-id="1cba5-114">Visual Studio 2013 with Update 2 (or later)</span></span>
> - <span data-ttu-id="1cba5-115">ASP.NET Identity 2.1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="1cba5-115">ASP.NET Identity 2.1 or later</span></span>


<span data-ttu-id="1cba5-116">K provedení kroků v tomto kurzu, musíte mít Visual Studio 2013 Update 2 (nebo novější) a webové aplikace ze šablony webové aplikace ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="1cba5-116">To perform the steps in this tutorial, you must have Visual Studio 2013 Update 2 (or later), and a web application created from the ASP.NET Web Application template.</span></span> <span data-ttu-id="1cba5-117">Šablona změnit ve verzi Update 3.</span><span class="sxs-lookup"><span data-stu-id="1cba5-117">The template changed in Update 3.</span></span> <span data-ttu-id="1cba5-118">Toto téma ukazuje, jak změnit šablonu v aktualizaci Update 2 a Update 3.</span><span class="sxs-lookup"><span data-stu-id="1cba5-118">This topic shows how to change the template in Update 2 and Update 3.</span></span>

<span data-ttu-id="1cba5-119">Toto téma obsahuje následující oddíly:</span><span class="sxs-lookup"><span data-stu-id="1cba5-119">This topic contains the following sections:</span></span>

- [<span data-ttu-id="1cba5-120">Změnit typ klíče ve třídě Identity uživatele</span><span class="sxs-lookup"><span data-stu-id="1cba5-120">Change the type of the key in the Identity user class</span></span>](#userclass)
- [<span data-ttu-id="1cba5-121">Přidat vlastní Identity třídy, které používají typ klíče</span><span class="sxs-lookup"><span data-stu-id="1cba5-121">Add customized Identity classes that use the key type</span></span>](#customclass)
- [<span data-ttu-id="1cba5-122">Změna správce kontextu třídy a uživatel použít typ klíče</span><span class="sxs-lookup"><span data-stu-id="1cba5-122">Change the context class and user manager to use the key type</span></span>](#context)
- [<span data-ttu-id="1cba5-123">Změna konfigurace spuštění použít typ klíče</span><span class="sxs-lookup"><span data-stu-id="1cba5-123">Change start-up configuration to use the key type</span></span>](#startup)
- [<span data-ttu-id="1cba5-124">MVC s aktualizací Update 2 změňte AccountController předat typ klíče</span><span class="sxs-lookup"><span data-stu-id="1cba5-124">For MVC with Update 2, change the AccountController to pass the key type</span></span>](#mvcupdate2)
- [<span data-ttu-id="1cba5-125">Pro rozhraní MVC s aktualizací Update 3 změňte AccountController a ManageController předávat typ klíče</span><span class="sxs-lookup"><span data-stu-id="1cba5-125">For MVC with Update 3, change the AccountController and ManageController to pass the key type</span></span>](#mvcupdate3)
- [<span data-ttu-id="1cba5-126">Webové formuláře s aktualizací Update 2 změňte účet stránky předat typ klíče</span><span class="sxs-lookup"><span data-stu-id="1cba5-126">For Web Forms with Update 2, change Account pages to pass the key type</span></span>](#webformsupdate2)
- [<span data-ttu-id="1cba5-127">Webové formuláře s aktualizací Update 3 změňte účet stránky předat typ klíče</span><span class="sxs-lookup"><span data-stu-id="1cba5-127">For Web Forms with Update 3, change Account pages to pass the key type</span></span>](#webformsupdate3)
- [<span data-ttu-id="1cba5-128">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="1cba5-128">Run application</span></span>](#run)
- [<span data-ttu-id="1cba5-129">Další prostředky</span><span class="sxs-lookup"><span data-stu-id="1cba5-129">Other resources</span></span>](#other)

<a id="userclass"></a>
## <a name="change-the-type-of-the-key-in-the-identity-user-class"></a><span data-ttu-id="1cba5-130">Změnit typ klíče ve třídě Identity uživatele</span><span class="sxs-lookup"><span data-stu-id="1cba5-130">Change the type of the key in the Identity user class</span></span>

<span data-ttu-id="1cba5-131">V projektu ze šablony webové aplikace ASP.NET určíte, že třídy uživatelů používat celého čísla pro klíč pro uživatelské účty.</span><span class="sxs-lookup"><span data-stu-id="1cba5-131">In your project created from the ASP.NET Web Application template, specify that the ApplicationUser class uses an integer for the key for user accounts.</span></span> <span data-ttu-id="1cba5-132">V IdentityModels.cs, změňte třídy uživatelů dědit z IdentityUser, která má typ **int** pro obecný parametr TKey.</span><span class="sxs-lookup"><span data-stu-id="1cba5-132">In IdentityModels.cs, change the ApplicationUser class to inherit from IdentityUser that has a type of **int** for the TKey generic parameter.</span></span> <span data-ttu-id="1cba5-133">Můžete také předat názvy tři vlastní třídu, která dosud nebyla implementována.</span><span class="sxs-lookup"><span data-stu-id="1cba5-133">You also pass the names of three customized class which you have not implemented yet.</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample1.cs?highlight=1-2)]

<span data-ttu-id="1cba5-134">Změnili jste typ klíče, ale ve výchozím nastavení, zbytek aplikace stále se předpokládá, že klíč je řetězec.</span><span class="sxs-lookup"><span data-stu-id="1cba5-134">You have changed the type of the key, but, by default, the rest of the application still assumes the key is a string.</span></span> <span data-ttu-id="1cba5-135">Musíte explicitně uvést typ klíče v kódu, která přijímá řetězec.</span><span class="sxs-lookup"><span data-stu-id="1cba5-135">You must explicitly indicate the type of the key in code that assumes a string.</span></span>

<span data-ttu-id="1cba5-136">V **ApplicationUser** tříd, změnit **GenerateUserIdentityAsync** tak, aby zahrnoval int, jak je znázorněno v následující zvýrazněný kód.</span><span class="sxs-lookup"><span data-stu-id="1cba5-136">In the **ApplicationUser** class, change the **GenerateUserIdentityAsync** method to include int, as shown in the highlighted code below.</span></span> <span data-ttu-id="1cba5-137">Tato změna není nezbytné pro projekty webových formulářů pomocí šablony s aktualizací Update 3.</span><span class="sxs-lookup"><span data-stu-id="1cba5-137">This change is not necessary for Web Forms projects with the Update 3 template.</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample2.cs?highlight=2)]

<a id="customclass"></a>
## <a name="add-customized-identity-classes-that-use-the-key-type"></a><span data-ttu-id="1cba5-138">Přidat vlastní Identity třídy, které používají typ klíče</span><span class="sxs-lookup"><span data-stu-id="1cba5-138">Add customized Identity classes that use the key type</span></span>

<span data-ttu-id="1cba5-139">Ostatní Identity třídy, jako je například IdentityUserRole IdentityUserClaim, IdentityUserLogin, IdentityRole, úložiště UserStore, úložiště RoleStore, jsou stále nastavení používat s klíčem řetězce.</span><span class="sxs-lookup"><span data-stu-id="1cba5-139">The other Identity classes, such as IdentityUserRole, IdentityUserClaim, IdentityUserLogin, IdentityRole, UserStore, RoleStore, are still set up to use a string key.</span></span> <span data-ttu-id="1cba5-140">Vytvořte nové verze těchto tříd, které určuje celé číslo pro klíč.</span><span class="sxs-lookup"><span data-stu-id="1cba5-140">Create new versions of these classes that specify an integer for the key.</span></span> <span data-ttu-id="1cba5-141">Není potřeba poskytovat spoustu implementační kód v těchto tříd, především právě nastavujete int jako klíč.</span><span class="sxs-lookup"><span data-stu-id="1cba5-141">You do not need to provide much implementation code in these classes, you are primarily just setting int as the key.</span></span>

<span data-ttu-id="1cba5-142">Přidejte následující třídy do souboru IdentityModels.cs.</span><span class="sxs-lookup"><span data-stu-id="1cba5-142">Add the following classes to your IdentityModels.cs file.</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample3.cs)]

<a id="context"></a>
## <a name="change-the-context-class-and-user-manager-to-use-the-key-type"></a><span data-ttu-id="1cba5-143">Změna správce kontextu třídy a uživatel použít typ klíče</span><span class="sxs-lookup"><span data-stu-id="1cba5-143">Change the context class and user manager to use the key type</span></span>

<span data-ttu-id="1cba5-144">V IdentityModels.cs, změňte definici **ApplicationDbContext** třídě do nové vlastní třídy a **int** klíče, jak je znázorněno v zvýrazněný kód.</span><span class="sxs-lookup"><span data-stu-id="1cba5-144">In IdentityModels.cs, change the definition of the **ApplicationDbContext** class to use your new customized classes and an **int** for the key, as shown in the highlighted code.</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample4.cs?highlight=1-2)]

<span data-ttu-id="1cba5-145">Parametr ThrowIfV1Schema již není platný v konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="1cba5-145">The ThrowIfV1Schema parameter is no longer valid in the constructor.</span></span> <span data-ttu-id="1cba5-146">Konstruktor měnit, takže nepředává ThrowIfV1Schema hodnotu.</span><span class="sxs-lookup"><span data-stu-id="1cba5-146">Change the constructor so it does not pass a ThrowIfV1Schema value.</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample5.cs)]

<span data-ttu-id="1cba5-147">Otevřete IdentityConfig.cs a změňte **ApplicationUserManger** třídu použít nový uživatel ukládání třídy pro zachování dat a **int** klíče.</span><span class="sxs-lookup"><span data-stu-id="1cba5-147">Open IdentityConfig.cs, and change the **ApplicationUserManger** class to use your new user store class for persisting data and an **int** for the key.</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample6.cs?highlight=1,3,12,14,32,37,48)]

<span data-ttu-id="1cba5-148">V šabloně s aktualizací Update 3 je nutné změnit ApplicationSignInManager třídy.</span><span class="sxs-lookup"><span data-stu-id="1cba5-148">In the Update 3 template, you must change the ApplicationSignInManager class.</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample7.cs?highlight=1)]

<a id="startup"></a>
## <a name="change-start-up-configuration-to-use-the-key-type"></a><span data-ttu-id="1cba5-149">Změna konfigurace spuštění použít typ klíče</span><span class="sxs-lookup"><span data-stu-id="1cba5-149">Change start-up configuration to use the key type</span></span>

<span data-ttu-id="1cba5-150">V Startup.Auth.cs nahraďte kód funkce OnValidateIdentity znázorněno dole.</span><span class="sxs-lookup"><span data-stu-id="1cba5-150">In Startup.Auth.cs, replace the OnValidateIdentity code, as highlighted below.</span></span> <span data-ttu-id="1cba5-151">Všimněte si, že definice getUserIdCallback analyzuje řetězcovou hodnotu na celé číslo.</span><span class="sxs-lookup"><span data-stu-id="1cba5-151">Notice that the getUserIdCallback definition, parses the string value into an integer.</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample8.cs?highlight=7-12)]

<span data-ttu-id="1cba5-152">Pokud projekt nebyl rozpoznán obecnou implementaci **GetUserId** metody, budete muset aktualizovat balíček NuGet identit technologie ASP.NET na verzi 2.1</span><span class="sxs-lookup"><span data-stu-id="1cba5-152">If your project does not recognize the generic implementation of the **GetUserId** method, you may need to update the ASP.NET Identity NuGet package to version 2.1</span></span>

<span data-ttu-id="1cba5-153">Provedli jste velké množství změn infrastruktury třídy používané ASP.NET Identity.</span><span class="sxs-lookup"><span data-stu-id="1cba5-153">You have made a lot of changes to the infrastructure classes used by ASP.NET Identity.</span></span> <span data-ttu-id="1cba5-154">Pokud se pokusíte kompilaci projektu, můžete si všimnout velké množství chyb.</span><span class="sxs-lookup"><span data-stu-id="1cba5-154">If you try compiling the project, you will notice a lot of errors.</span></span> <span data-ttu-id="1cba5-155">Naštěstí zbývající chyby jsou podobné.</span><span class="sxs-lookup"><span data-stu-id="1cba5-155">Fortunately, the remaining errors are all similar.</span></span> <span data-ttu-id="1cba5-156">Třída Identity očekává celé číslo pro klíč, ale kontroler (nebo webového formuláře) předává hodnotu řetězce.</span><span class="sxs-lookup"><span data-stu-id="1cba5-156">The Identity class expects an integer for the key, but the controller (or Web Form) is passing a string value.</span></span> <span data-ttu-id="1cba5-157">V obou případech je potřeba převést z celé číslo a řetězec voláním **GetUserId&lt;int&gt;**.</span><span class="sxs-lookup"><span data-stu-id="1cba5-157">In each case, you need to convert from a string to and integer by calling **GetUserId&lt;int&gt;**.</span></span> <span data-ttu-id="1cba5-158">Můžete projít seznam chyb v kompilaci nebo postupujte podle níže uvedených změny.</span><span class="sxs-lookup"><span data-stu-id="1cba5-158">You can either work through the error list from compilation or follow the changes below.</span></span>

<span data-ttu-id="1cba5-159">Zbývající změny závisí na typu projektu při vytváření a aktualizace, které jste nainstalovali v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1cba5-159">The remaining changes depend on the type of project you are creating and which update you have installed in Visual Studio.</span></span> <span data-ttu-id="1cba5-160">Můžete přejít přímo do příslušné části prostřednictvím následujících odkazů</span><span class="sxs-lookup"><span data-stu-id="1cba5-160">You can go directly to the relevant section through the following links</span></span>

- [<span data-ttu-id="1cba5-161">MVC s aktualizací Update 2 změňte AccountController předat typ klíče</span><span class="sxs-lookup"><span data-stu-id="1cba5-161">For MVC with Update 2, change the AccountController to pass the key type</span></span>](#mvcupdate2)
- [<span data-ttu-id="1cba5-162">Pro rozhraní MVC s aktualizací Update 3 změňte AccountController a ManageController předávat typ klíče</span><span class="sxs-lookup"><span data-stu-id="1cba5-162">For MVC with Update 3, change the AccountController and ManageController to pass the key type</span></span>](#mvcupdate3)
- [<span data-ttu-id="1cba5-163">Webové formuláře s aktualizací Update 2 změňte účet stránky předat typ klíče</span><span class="sxs-lookup"><span data-stu-id="1cba5-163">For Web Forms with Update 2, change Account pages to pass the key type</span></span>](#webformsupdate2)
- [<span data-ttu-id="1cba5-164">Webové formuláře s aktualizací Update 3 změňte účet stránky předat typ klíče</span><span class="sxs-lookup"><span data-stu-id="1cba5-164">For Web Forms with Update 3, change Account pages to pass the key type</span></span>](#webformsupdate3)

<a id="mvcupdate2"></a>
## <a name="for-mvc-with-update-2-change-the-accountcontroller-to-pass-the-key-type"></a><span data-ttu-id="1cba5-165">MVC s aktualizací Update 2 změňte AccountController předat typ klíče</span><span class="sxs-lookup"><span data-stu-id="1cba5-165">For MVC with Update 2, change the AccountController to pass the key type</span></span>

<span data-ttu-id="1cba5-166">Otevřete soubor AccountController.cs.</span><span class="sxs-lookup"><span data-stu-id="1cba5-166">Open the AccountController.cs file.</span></span> <span data-ttu-id="1cba5-167">Je třeba změnit následující metody.</span><span class="sxs-lookup"><span data-stu-id="1cba5-167">You need to change the following methods.</span></span>

<span data-ttu-id="1cba5-168">**ConfirmEmail** – metoda</span><span class="sxs-lookup"><span data-stu-id="1cba5-168">**ConfirmEmail** method</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample9.cs?highlight=1,3)]

<span data-ttu-id="1cba5-169">**Zrušit přidružení** – metoda</span><span class="sxs-lookup"><span data-stu-id="1cba5-169">**Disassociate** method</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample10.cs?highlight=5,9)]

<span data-ttu-id="1cba5-170">**Manage(ManageUserViewModel)** – metoda</span><span class="sxs-lookup"><span data-stu-id="1cba5-170">**Manage(ManageUserViewModel)** method</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample11.cs?highlight=11,17,41)]

<span data-ttu-id="1cba5-171">**LinkLoginCallback** – metoda</span><span class="sxs-lookup"><span data-stu-id="1cba5-171">**LinkLoginCallback** method</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample12.cs?highlight=10)]

<span data-ttu-id="1cba5-172">**RemoveAccountList** – metoda</span><span class="sxs-lookup"><span data-stu-id="1cba5-172">**RemoveAccountList** method</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample13.cs?highlight=3)]

<span data-ttu-id="1cba5-173">**HasPassword** – metoda</span><span class="sxs-lookup"><span data-stu-id="1cba5-173">**HasPassword** method</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample14.cs?highlight=3)]

<span data-ttu-id="1cba5-174">Teď můžete [spusťte aplikaci](#run) a registraci nového uživatele.</span><span class="sxs-lookup"><span data-stu-id="1cba5-174">You can now [run the application](#run) and register a new user.</span></span>

<a id="mvcupdate3"></a>
## <a name="for-mvc-with-update-3-change-the-accountcontroller-and-managecontroller-to-pass-the-key-type"></a><span data-ttu-id="1cba5-175">Pro rozhraní MVC s aktualizací Update 3 změňte AccountController a ManageController předávat typ klíče</span><span class="sxs-lookup"><span data-stu-id="1cba5-175">For MVC with Update 3, change the AccountController and ManageController to pass the key type</span></span>

<span data-ttu-id="1cba5-176">Otevřete soubor AccountController.cs.</span><span class="sxs-lookup"><span data-stu-id="1cba5-176">Open the AccountController.cs file.</span></span> <span data-ttu-id="1cba5-177">Je třeba změnit následující metodu.</span><span class="sxs-lookup"><span data-stu-id="1cba5-177">You need to change the following method.</span></span>

<span data-ttu-id="1cba5-178">**ConfirmEmail** – metoda</span><span class="sxs-lookup"><span data-stu-id="1cba5-178">**ConfirmEmail** method</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample15.cs?highlight=1,3)]

<span data-ttu-id="1cba5-179">**SendCode** – metoda</span><span class="sxs-lookup"><span data-stu-id="1cba5-179">**SendCode** method</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample16.cs?highlight=4)]

<span data-ttu-id="1cba5-180">Otevřete soubor ManageController.cs.</span><span class="sxs-lookup"><span data-stu-id="1cba5-180">Open the ManageController.cs file.</span></span> <span data-ttu-id="1cba5-181">Je třeba změnit následující metody.</span><span class="sxs-lookup"><span data-stu-id="1cba5-181">You need to change the following methods.</span></span>

<span data-ttu-id="1cba5-182">**Index** – metoda</span><span class="sxs-lookup"><span data-stu-id="1cba5-182">**Index** method</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample17.cs?highlight=15-17)]

<span data-ttu-id="1cba5-183">**RemoveLogin** metody</span><span class="sxs-lookup"><span data-stu-id="1cba5-183">**RemoveLogin** methods</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample18.cs?highlight=3,13,17)]

<span data-ttu-id="1cba5-184">**AddPhoneNumber** – metoda</span><span class="sxs-lookup"><span data-stu-id="1cba5-184">**AddPhoneNumber** method</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample19.cs?highlight=9)]

<span data-ttu-id="1cba5-185">**EnableTwoFactorAuthentication** – metoda</span><span class="sxs-lookup"><span data-stu-id="1cba5-185">**EnableTwoFactorAuthentication** method</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample20.cs?highlight=3-4)]

<span data-ttu-id="1cba5-186">**DisableTwoFactorAuthentication** – metoda</span><span class="sxs-lookup"><span data-stu-id="1cba5-186">**DisableTwoFactorAuthentication** method</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample21.cs?highlight=3-4)]

<span data-ttu-id="1cba5-187">**VerifyPhoneNumber** metody</span><span class="sxs-lookup"><span data-stu-id="1cba5-187">**VerifyPhoneNumber** methods</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample22.cs?highlight=4,18,21)]

<span data-ttu-id="1cba5-188">**RemovePhoneNumber** – metoda</span><span class="sxs-lookup"><span data-stu-id="1cba5-188">**RemovePhoneNumber** method</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample23.cs?highlight=3,8)]

<span data-ttu-id="1cba5-189">**Metodu ChangePassword** – metoda</span><span class="sxs-lookup"><span data-stu-id="1cba5-189">**ChangePassword** method</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample24.cs?highlight=10,13)]

<span data-ttu-id="1cba5-190">**SetPassword** – metoda</span><span class="sxs-lookup"><span data-stu-id="1cba5-190">**SetPassword** method</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample25.cs?highlight=5,8)]

<span data-ttu-id="1cba5-191">**ManageLogins** – metoda</span><span class="sxs-lookup"><span data-stu-id="1cba5-191">**ManageLogins** method</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample26.cs?highlight=7,12)]

<span data-ttu-id="1cba5-192">**LinkLoginCallback** – metoda</span><span class="sxs-lookup"><span data-stu-id="1cba5-192">**LinkLoginCallback** method</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample27.cs?highlight=8)]

<span data-ttu-id="1cba5-193">**HasPassword** – metoda</span><span class="sxs-lookup"><span data-stu-id="1cba5-193">**HasPassword** method</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample28.cs?highlight=3)]

<span data-ttu-id="1cba5-194">**HasPhoneNumber** – metoda</span><span class="sxs-lookup"><span data-stu-id="1cba5-194">**HasPhoneNumber** method</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample29.cs?highlight=3)]

<span data-ttu-id="1cba5-195">Teď můžete [spusťte aplikaci](#run) a registraci nového uživatele.</span><span class="sxs-lookup"><span data-stu-id="1cba5-195">You can now [run the application](#run) and register a new user.</span></span>

<a id="webformsupdate2"></a>
## <a name="for-web-forms-with-update-2-change-account-pages-to-pass-the-key-type"></a><span data-ttu-id="1cba5-196">Webové formuláře s aktualizací Update 2 změňte účet stránky předat typ klíče</span><span class="sxs-lookup"><span data-stu-id="1cba5-196">For Web Forms with Update 2, change Account pages to pass the key type</span></span>

<span data-ttu-id="1cba5-197">Webové formuláře s aktualizací Update 2 budete muset změnit na následujících stránkách.</span><span class="sxs-lookup"><span data-stu-id="1cba5-197">For Web Forms with Update 2, you need to change the following pages.</span></span>

<span data-ttu-id="1cba5-198">**Confirm.aspx.CX**</span><span class="sxs-lookup"><span data-stu-id="1cba5-198">**Confirm.aspx.cx**</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample30.cs?highlight=8)]

<span data-ttu-id="1cba5-199">**RegisterExternalLogin.aspx.cs**</span><span class="sxs-lookup"><span data-stu-id="1cba5-199">**RegisterExternalLogin.aspx.cs**</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample31.cs?highlight=36)]

<span data-ttu-id="1cba5-200">**Manage.aspx.cs**</span><span class="sxs-lookup"><span data-stu-id="1cba5-200">**Manage.aspx.cs**</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample32.cs?highlight=3,22,47,52,69,85,93,98)]

<span data-ttu-id="1cba5-201">Teď můžete [spusťte aplikaci](#run) a registraci nového uživatele.</span><span class="sxs-lookup"><span data-stu-id="1cba5-201">You can now [run the application](#run) and register a new user.</span></span>

<a id="webformsupdate3"></a>
## <a name="for-web-forms-with-update-3-change-account-pages-to-pass-the-key-type"></a><span data-ttu-id="1cba5-202">Webové formuláře s aktualizací Update 3 změňte účet stránky předat typ klíče</span><span class="sxs-lookup"><span data-stu-id="1cba5-202">For Web Forms with Update 3, change Account pages to pass the key type</span></span>

<span data-ttu-id="1cba5-203">Webové formuláře s aktualizací Update 3 budete muset změnit na následujících stránkách.</span><span class="sxs-lookup"><span data-stu-id="1cba5-203">For Web Forms with Update 3, you need to change the following pages.</span></span>

<span data-ttu-id="1cba5-204">**Confirm.aspx.CX**</span><span class="sxs-lookup"><span data-stu-id="1cba5-204">**Confirm.aspx.cx**</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample33.cs?highlight=8)]

<span data-ttu-id="1cba5-205">**RegisterExternalLogin.aspx.cs**</span><span class="sxs-lookup"><span data-stu-id="1cba5-205">**RegisterExternalLogin.aspx.cs**</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample34.cs?highlight=36)]

<span data-ttu-id="1cba5-206">**Manage.aspx.cs**</span><span class="sxs-lookup"><span data-stu-id="1cba5-206">**Manage.aspx.cs**</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample35.cs?highlight=11,27,32,34,82,87,99,108)]

<span data-ttu-id="1cba5-207">**VerifyPhoneNumber.aspx.cs**</span><span class="sxs-lookup"><span data-stu-id="1cba5-207">**VerifyPhoneNumber.aspx.cs**</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample36.cs?highlight=8,23,27)]

<span data-ttu-id="1cba5-208">**AddPhoneNumber.aspx.cs**</span><span class="sxs-lookup"><span data-stu-id="1cba5-208">**AddPhoneNumber.aspx.cs**</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample37.cs?highlight=7)]

<span data-ttu-id="1cba5-209">**ManagePassword.aspx.cs**</span><span class="sxs-lookup"><span data-stu-id="1cba5-209">**ManagePassword.aspx.cs**</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample38.cs?highlight=11,47,50,68)]

<span data-ttu-id="1cba5-210">**ManageLogins.aspx.cs**</span><span class="sxs-lookup"><span data-stu-id="1cba5-210">**ManageLogins.aspx.cs**</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample39.cs?highlight=16,23,32,41,45)]

<span data-ttu-id="1cba5-211">**TwoFactorAuthenticationSignIn.aspx.cs**</span><span class="sxs-lookup"><span data-stu-id="1cba5-211">**TwoFactorAuthenticationSignIn.aspx.cs**</span></span>

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample40.cs?highlight=14-15,29,53)]

<a id="run"></a>
## <a name="run-application"></a><span data-ttu-id="1cba5-212">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="1cba5-212">Run application</span></span>

<span data-ttu-id="1cba5-213">Dokončili jste všechny požadované změny pro výchozí šablony webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="1cba5-213">You have finished all of the required changes to the default Web Application template.</span></span> <span data-ttu-id="1cba5-214">Spusťte aplikaci a zaregistrovat nového uživatele.</span><span class="sxs-lookup"><span data-stu-id="1cba5-214">Run the application and register a new user.</span></span> <span data-ttu-id="1cba5-215">Po registraci uživatele, můžete si všimnout, že AspNetUsers tabulka obsahuje sloupec Id, který je celé číslo.</span><span class="sxs-lookup"><span data-stu-id="1cba5-215">After registering the user, you will notice that the AspNetUsers table has an Id column that is an integer.</span></span>

![nový primární klíč](change-primary-key-for-users-in-aspnet-identity/_static/image1.png)

<span data-ttu-id="1cba5-217">Pokud jste předtím vytvořili ASP.NET Identity tabulky s primárním klíčem jiný, budete muset provést některé další změny.</span><span class="sxs-lookup"><span data-stu-id="1cba5-217">If you have previously created the ASP.NET Identity tables with a different primary key, you need to make some additional changes.</span></span> <span data-ttu-id="1cba5-218">Pokud je to možné odstraňte existující databázi.</span><span class="sxs-lookup"><span data-stu-id="1cba5-218">If possible, just delete the existing database.</span></span> <span data-ttu-id="1cba5-219">Databáze bude potřeba znovu vytvořit pomocí správného návrhu při spuštění webové aplikace a přidání nového uživatele.</span><span class="sxs-lookup"><span data-stu-id="1cba5-219">The database will be re-created with the correct design when you run the web application and add a new user.</span></span> <span data-ttu-id="1cba5-220">Pokud odstranění není možné, spustit migrace code first, chcete-li změnit tabulky.</span><span class="sxs-lookup"><span data-stu-id="1cba5-220">If deletion is not possible, run code first migrations to change the tables.</span></span> <span data-ttu-id="1cba5-221">Ale nový primární klíč celého čísla nebude nastavit jako vlastnost SQL IDENTITY v databázi.</span><span class="sxs-lookup"><span data-stu-id="1cba5-221">However, the new integer primary key will not be set up as a SQL IDENTITY property in the database.</span></span> <span data-ttu-id="1cba5-222">Sloupec Id je nutné ručně nastavit jako identita.</span><span class="sxs-lookup"><span data-stu-id="1cba5-222">You must manually set the Id column as an IDENTITY.</span></span>

<a id="other"></a>
## <a name="other-resources"></a><span data-ttu-id="1cba5-223">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="1cba5-223">Other resources</span></span>

- [<span data-ttu-id="1cba5-224">Přehled poskytovatelů vlastního úložiště pro ASP.NET Identity</span><span class="sxs-lookup"><span data-stu-id="1cba5-224">Overview of Custom Storage Providers for ASP.NET Identity</span></span>](overview-of-custom-storage-providers-for-aspnet-identity.md)
- [<span data-ttu-id="1cba5-225">Migrace stávajícího webu z členství SQL na ASP.NET Identity</span><span class="sxs-lookup"><span data-stu-id="1cba5-225">Migrating an Existing Website from SQL Membership to ASP.NET Identity</span></span>](../migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity.md)
- [<span data-ttu-id="1cba5-226">Migrace dat od univerzálního zprostředkovatele pro členství a uživatelských profilů na ASP.NET Identity</span><span class="sxs-lookup"><span data-stu-id="1cba5-226">Migrating Universal Provider Data for Membership and User Profiles to ASP.NET Identity</span></span>](../migrations/migrating-universal-provider-data-for-membership-and-user-profiles-to-aspnet-identity.md)
- <span data-ttu-id="1cba5-227">[Ukázková aplikace](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/ChangePK/readme.txt) změněné primárního klíče</span><span class="sxs-lookup"><span data-stu-id="1cba5-227">[Sample application](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/ChangePK/readme.txt) with changed primary key</span></span>

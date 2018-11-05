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
<a name="change-primary-key-for-users-in-aspnet-identity"></a>Změna primárního klíče uživatelů v ASP.NET Identity
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Výchozí webová aplikace v sadě Visual Studio 2013, používá řetězcovou hodnotu pro klíč pro uživatelské účty. ASP.NET Identity umožňuje změnit typ klíče pro splnění požadavků na data. Můžete například změnit typ klíče z řetězce na celé číslo.
> 
> Toto téma ukazuje, jak začít s výchozí webové aplikace změňte klíč účtu uživatele na celé číslo. Stejné změny můžete použít k implementaci libovolného typu klíče ve vašem projektu. Ukazuje, jak tyto změny ve výchozí webové aplikace, ale můžete použít podobné změny do vlastní aplikace. Zobrazuje změny potřebné při práci s MVC nebo webového formuláře.
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
> 
> 
> - Visual Studio 2013 s aktualizací Update 2 (nebo novější)
> - ASP.NET Identity 2.1 nebo novější


K provedení kroků v tomto kurzu, musíte mít Visual Studio 2013 Update 2 (nebo novější) a webové aplikace ze šablony webové aplikace ASP.NET. Šablona změnit ve verzi Update 3. Toto téma ukazuje, jak změnit šablonu v aktualizaci Update 2 a Update 3.

Toto téma obsahuje následující oddíly:

- [Změnit typ klíče ve třídě Identity uživatele](#userclass)
- [Přidat vlastní Identity třídy, které používají typ klíče](#customclass)
- [Změna správce kontextu třídy a uživatel použít typ klíče](#context)
- [Změna konfigurace spuštění použít typ klíče](#startup)
- [MVC s aktualizací Update 2 změňte AccountController předat typ klíče](#mvcupdate2)
- [Pro rozhraní MVC s aktualizací Update 3 změňte AccountController a ManageController předávat typ klíče](#mvcupdate3)
- [Webové formuláře s aktualizací Update 2 změňte účet stránky předat typ klíče](#webformsupdate2)
- [Webové formuláře s aktualizací Update 3 změňte účet stránky předat typ klíče](#webformsupdate3)
- [Spuštění aplikace](#run)
- [Další prostředky](#other)

<a id="userclass"></a>
## <a name="change-the-type-of-the-key-in-the-identity-user-class"></a>Změnit typ klíče ve třídě Identity uživatele

V projektu ze šablony webové aplikace ASP.NET určíte, že třídy uživatelů používat celého čísla pro klíč pro uživatelské účty. V IdentityModels.cs, změňte třídy uživatelů dědit z IdentityUser, která má typ **int** pro obecný parametr TKey. Můžete také předat názvy tři vlastní třídu, která dosud nebyla implementována.

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample1.cs?highlight=1-2)]

Změnili jste typ klíče, ale ve výchozím nastavení, zbytek aplikace stále se předpokládá, že klíč je řetězec. Musíte explicitně uvést typ klíče v kódu, která přijímá řetězec.

V **ApplicationUser** tříd, změnit **GenerateUserIdentityAsync** tak, aby zahrnoval int, jak je znázorněno v následující zvýrazněný kód. Tato změna není nezbytné pro projekty webových formulářů pomocí šablony s aktualizací Update 3.

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample2.cs?highlight=2)]

<a id="customclass"></a>
## <a name="add-customized-identity-classes-that-use-the-key-type"></a>Přidat vlastní Identity třídy, které používají typ klíče

Ostatní Identity třídy, jako je například IdentityUserRole IdentityUserClaim, IdentityUserLogin, IdentityRole, úložiště UserStore, úložiště RoleStore, jsou stále nastavení používat s klíčem řetězce. Vytvořte nové verze těchto tříd, které určuje celé číslo pro klíč. Není potřeba poskytovat spoustu implementační kód v těchto tříd, především právě nastavujete int jako klíč.

Přidejte následující třídy do souboru IdentityModels.cs.

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample3.cs)]

<a id="context"></a>
## <a name="change-the-context-class-and-user-manager-to-use-the-key-type"></a>Změna správce kontextu třídy a uživatel použít typ klíče

V IdentityModels.cs, změňte definici **ApplicationDbContext** třídě do nové vlastní třídy a **int** klíče, jak je znázorněno v zvýrazněný kód.

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample4.cs?highlight=1-2)]

Parametr ThrowIfV1Schema již není platný v konstruktoru. Konstruktor měnit, takže nepředává ThrowIfV1Schema hodnotu.

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample5.cs)]

Otevřete IdentityConfig.cs a změňte **ApplicationUserManger** třídu použít nový uživatel ukládání třídy pro zachování dat a **int** klíče.

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample6.cs?highlight=1,3,12,14,32,37,48)]

V šabloně s aktualizací Update 3 je nutné změnit ApplicationSignInManager třídy.

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample7.cs?highlight=1)]

<a id="startup"></a>
## <a name="change-start-up-configuration-to-use-the-key-type"></a>Změna konfigurace spuštění použít typ klíče

V Startup.Auth.cs nahraďte kód funkce OnValidateIdentity znázorněno dole. Všimněte si, že definice getUserIdCallback analyzuje řetězcovou hodnotu na celé číslo.

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample8.cs?highlight=7-12)]

Pokud projekt nebyl rozpoznán obecnou implementaci **GetUserId** metody, budete muset aktualizovat balíček NuGet identit technologie ASP.NET na verzi 2.1

Provedli jste velké množství změn infrastruktury třídy používané ASP.NET Identity. Pokud se pokusíte kompilaci projektu, můžete si všimnout velké množství chyb. Naštěstí zbývající chyby jsou podobné. Třída Identity očekává celé číslo pro klíč, ale kontroler (nebo webového formuláře) předává hodnotu řetězce. V obou případech je potřeba převést z celé číslo a řetězec voláním **GetUserId&lt;int&gt;**. Můžete projít seznam chyb v kompilaci nebo postupujte podle níže uvedených změny.

Zbývající změny závisí na typu projektu při vytváření a aktualizace, které jste nainstalovali v sadě Visual Studio. Můžete přejít přímo do příslušné části prostřednictvím následujících odkazů

- [MVC s aktualizací Update 2 změňte AccountController předat typ klíče](#mvcupdate2)
- [Pro rozhraní MVC s aktualizací Update 3 změňte AccountController a ManageController předávat typ klíče](#mvcupdate3)
- [Webové formuláře s aktualizací Update 2 změňte účet stránky předat typ klíče](#webformsupdate2)
- [Webové formuláře s aktualizací Update 3 změňte účet stránky předat typ klíče](#webformsupdate3)

<a id="mvcupdate2"></a>
## <a name="for-mvc-with-update-2-change-the-accountcontroller-to-pass-the-key-type"></a>MVC s aktualizací Update 2 změňte AccountController předat typ klíče

Otevřete soubor AccountController.cs. Je třeba změnit následující metody.

**ConfirmEmail** – metoda

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample9.cs?highlight=1,3)]

**Zrušit přidružení** – metoda

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample10.cs?highlight=5,9)]

**Manage(ManageUserViewModel)** – metoda

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample11.cs?highlight=11,17,41)]

**LinkLoginCallback** – metoda

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample12.cs?highlight=10)]

**RemoveAccountList** – metoda

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample13.cs?highlight=3)]

**HasPassword** – metoda

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample14.cs?highlight=3)]

Teď můžete [spusťte aplikaci](#run) a registraci nového uživatele.

<a id="mvcupdate3"></a>
## <a name="for-mvc-with-update-3-change-the-accountcontroller-and-managecontroller-to-pass-the-key-type"></a>Pro rozhraní MVC s aktualizací Update 3 změňte AccountController a ManageController předávat typ klíče

Otevřete soubor AccountController.cs. Je třeba změnit následující metodu.

**ConfirmEmail** – metoda

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample15.cs?highlight=1,3)]

**SendCode** – metoda

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample16.cs?highlight=4)]

Otevřete soubor ManageController.cs. Je třeba změnit následující metody.

**Index** – metoda

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample17.cs?highlight=15-17)]

**RemoveLogin** metody

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample18.cs?highlight=3,13,17)]

**AddPhoneNumber** – metoda

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample19.cs?highlight=9)]

**EnableTwoFactorAuthentication** – metoda

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample20.cs?highlight=3-4)]

**DisableTwoFactorAuthentication** – metoda

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample21.cs?highlight=3-4)]

**VerifyPhoneNumber** metody

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample22.cs?highlight=4,18,21)]

**RemovePhoneNumber** – metoda

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample23.cs?highlight=3,8)]

**Metodu ChangePassword** – metoda

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample24.cs?highlight=10,13)]

**SetPassword** – metoda

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample25.cs?highlight=5,8)]

**ManageLogins** – metoda

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample26.cs?highlight=7,12)]

**LinkLoginCallback** – metoda

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample27.cs?highlight=8)]

**HasPassword** – metoda

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample28.cs?highlight=3)]

**HasPhoneNumber** – metoda

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample29.cs?highlight=3)]

Teď můžete [spusťte aplikaci](#run) a registraci nového uživatele.

<a id="webformsupdate2"></a>
## <a name="for-web-forms-with-update-2-change-account-pages-to-pass-the-key-type"></a>Webové formuláře s aktualizací Update 2 změňte účet stránky předat typ klíče

Webové formuláře s aktualizací Update 2 budete muset změnit na následujících stránkách.

**Confirm.aspx.CX**

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample30.cs?highlight=8)]

**RegisterExternalLogin.aspx.cs**

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample31.cs?highlight=36)]

**Manage.aspx.cs**

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample32.cs?highlight=3,22,47,52,69,85,93,98)]

Teď můžete [spusťte aplikaci](#run) a registraci nového uživatele.

<a id="webformsupdate3"></a>
## <a name="for-web-forms-with-update-3-change-account-pages-to-pass-the-key-type"></a>Webové formuláře s aktualizací Update 3 změňte účet stránky předat typ klíče

Webové formuláře s aktualizací Update 3 budete muset změnit na následujících stránkách.

**Confirm.aspx.CX**

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample33.cs?highlight=8)]

**RegisterExternalLogin.aspx.cs**

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample34.cs?highlight=36)]

**Manage.aspx.cs**

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample35.cs?highlight=11,27,32,34,82,87,99,108)]

**VerifyPhoneNumber.aspx.cs**

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample36.cs?highlight=8,23,27)]

**AddPhoneNumber.aspx.cs**

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample37.cs?highlight=7)]

**ManagePassword.aspx.cs**

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample38.cs?highlight=11,47,50,68)]

**ManageLogins.aspx.cs**

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample39.cs?highlight=16,23,32,41,45)]

**TwoFactorAuthenticationSignIn.aspx.cs**

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample40.cs?highlight=14-15,29,53)]

<a id="run"></a>
## <a name="run-application"></a>Spuštění aplikace

Dokončili jste všechny požadované změny pro výchozí šablony webové aplikace. Spusťte aplikaci a zaregistrovat nového uživatele. Po registraci uživatele, můžete si všimnout, že AspNetUsers tabulka obsahuje sloupec Id, který je celé číslo.

![nový primární klíč](change-primary-key-for-users-in-aspnet-identity/_static/image1.png)

Pokud jste předtím vytvořili ASP.NET Identity tabulky s primárním klíčem jiný, budete muset provést některé další změny. Pokud je to možné odstraňte existující databázi. Databáze bude potřeba znovu vytvořit pomocí správného návrhu při spuštění webové aplikace a přidání nového uživatele. Pokud odstranění není možné, spustit migrace code first, chcete-li změnit tabulky. Ale nový primární klíč celého čísla nebude nastavit jako vlastnost SQL IDENTITY v databázi. Sloupec Id je nutné ručně nastavit jako identita.

<a id="other"></a>
## <a name="other-resources"></a>Další zdroje

- [Přehled poskytovatelů vlastního úložiště pro ASP.NET Identity](overview-of-custom-storage-providers-for-aspnet-identity.md)
- [Migrace stávajícího webu z členství SQL na ASP.NET Identity](../migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity.md)
- [Migrace dat od univerzálního zprostředkovatele pro členství a uživatelských profilů na ASP.NET Identity](../migrations/migrating-universal-provider-data-for-membership-and-user-profiles-to-aspnet-identity.md)
- [Ukázková aplikace](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/ChangePK/readme.txt) změněné primárního klíče

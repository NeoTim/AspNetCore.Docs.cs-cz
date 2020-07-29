---
title: Vytvoření aplikace ASP.NET Core s uživatelskými daty chráněnými autorizací
author: rick-anderson
description: Naučte se vytvářet ASP.NET Core webové aplikace s uživatelskými daty chráněnými autorizací. Zahrnuje protokol HTTPS, ověřování, zabezpečení ASP.NET Core Identity .
ms.author: riande
ms.date: 7/18/2020
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/secure-data
ms.openlocfilehash: 7d4c10fa0b1c569179fc3e0a518917ec0185c51f
ms.sourcegitcommit: 1b89fc58114a251926abadfd5c69c120f1ba12d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2020
ms.locfileid: "87160273"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a>Vytvoření webové aplikace v ASP.NET Core s uživatelskými daty chráněnými autorizací

Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Jan Audette](https://twitter.com/joeaudette)

::: moniker range="= aspnetcore-2.0"

Zobrazit [Tento PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

V tomto kurzu se dozvíte, jak vytvořit webovou aplikaci ASP.NET Core s uživatelskými daty chráněnými autorizací. Zobrazuje seznam kontaktů ověřovaných (registrovaných) uživatelů. Existují tři skupiny zabezpečení:

* **Registrovaní uživatelé** můžou zobrazit všechna schválená data a můžou upravovat nebo odstraňovat vlastní data.
* **Manažeři** mohou schvalovat nebo odmítat kontaktní data. Uživatelé vidí jenom schválené kontakty.
* **Správci** mohou schvalovat nebo odmítat a upravovat nebo odstraňovat jakákoli data.

Obrázky v tomto dokumentu se přesně neshodují s nejnovějšími šablonami.

Na následujícím obrázku je uživatel Rick ( `rick@example.com` ) přihlášen. Rick může zobrazit pouze schválené kontakty a **Upravit** / **Odstranit** / **nové** odkazy pro své kontakty. Jenom poslední záznam vytvořený pomocí Rick zobrazí odkazy pro **Úpravy** a **odstranění** . Ostatní uživatelé uvidí poslední záznam, dokud správce nebo správce nezmění stav na schváleno.

![Snímek obrazovky zobrazující přihlášený Rick](secure-data/_static/rick.png)

Na následujícím obrázku `manager@contoso.com` je přihlášen a v roli manažera:

![Snímek obrazovky zobrazující manager@contoso.com přihlášený](secure-data/_static/manager1.png)

Následující obrázek ukazuje zobrazení podrobností o kontaktu pro správce:

![Zobrazení kontaktu manažera](secure-data/_static/manager.png)

Tlačítka **schválení** a **odmítnutí** se zobrazují pouze pro manažery a správce.

Na následujícím obrázku `admin@contoso.com` je přihlášen a v roli správce:

![Snímek obrazovky zobrazující admin@contoso.com přihlášený](secure-data/_static/admin.png)

Správce má všechna oprávnění. Může číst, upravovat a odstraňovat jakékoli kontakty a měnit stav kontaktů.

Aplikace byla vytvořena pomocí [generování uživatelského rozhraní](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) tohoto `Contact` modelu:

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

Ukázka obsahuje následující obslužné rutiny autorizace:

* `ContactIsOwnerAuthorizationHandler`: Zajistí, že uživatel může upravovat pouze svá data.
* `ContactManagerAuthorizationHandler`: Umožňuje správcům schvalovat nebo odmítat kontakty.
* `ContactAdministratorsAuthorizationHandler`: Umožňuje správcům schvalovat nebo odmítat kontakty a upravovat nebo odstraňovat kontakty.

## <a name="prerequisites"></a>Předpoklady

Tento kurz je pokročilý. Měli byste být obeznámeni s:

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [Authentication](xref:security/authentication/identity)
* [Potvrzení účtu a obnovení hesla](xref:security/authentication/accconfirm)
* [Autorizace](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>Úvodní a dokončená aplikace

[Stáhněte](xref:index#how-to-download-a-sample) [dokončenou](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) aplikaci. [Otestujte](#test-the-completed-app) dokončenou aplikaci, abyste se seznámili se svými funkcemi zabezpečení.

### <a name="the-starter-app"></a>Úvodní aplikace

[Stáhněte](xref:index#how-to-download-a-sample) si [úvodní](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) aplikaci.

Spusťte aplikaci, klepněte na odkaz **ContactManager** a ověřte, že můžete vytvořit, upravit a odstranit kontakt.

## <a name="secure-user-data"></a>Zabezpečená uživatelská data

V následujících částech najdete všechny hlavní kroky k vytvoření aplikace zabezpečených uživatelských dat. Může to být užitečné pro odkazování na dokončený projekt.

### <a name="tie-the-contact-data-to-the-user"></a>Spojit kontaktní údaje s uživatelem

Pomocí [Identity](xref:security/authentication/identity) ID uživatele ASP.NET zajistěte, aby mohli uživatelé upravovat data, ale ne jiná data uživatelů. Přidejte `OwnerID` a `ContactStatus` do `Contact` modelu:

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID`je ID uživatele z `AspNetUser` tabulky v [Identity](xref:security/authentication/identity) databázi. `Status`Pole určuje, jestli je kontakt viditelný pro obecné uživatele.

Vytvořte novou migraci a aktualizujte databázi:

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a>Přidat služby rolí doIdentity

Připojit [Přidat role](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) pro přidání služeb role:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a>Vyžadovat ověřené uživatele

Nastavte zásady nouzového ověřování tak, aby vyžadovaly ověření uživatelů:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

Předchozí zvýrazněný kód nastaví [zásady nouzového ověřování](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy). Zásada záložního ověřování vyžaduje, aby ***Všichni*** uživatelé byli ověřeni, s výjimkou Razor stránek, řadičů nebo metod akcí s atributem ověřování. Například Razor stránky, řadiče nebo metody akcí pomocí `[AllowAnonymous]` nebo `[Authorize(PolicyName="MyPolicy")]` použijte použitý atribut ověřování, nikoli zásady nouzového ověřování.

Zásady nouzového ověřování:

* Se použije u všech požadavků, které explicitně neurčují zásady ověřování. V případě požadavků poskytovaných směrováním koncových bodů by to zahrnovalo koncový bod, který nespecifikuje autorizační atribut. Pro požadavky poskytované jiným middlewarem middleware po autorizačním middlewaru, jako jsou třeba [statické soubory](xref:fundamentals/static-files), by se tato zásada použila u všech požadavků.

Nastavení zásady nouzového ověřování, aby vyžadovala ověření uživatelů, chrání nově přidané Razor stránky a řadiče. Pokud je ve výchozím nastavení vyžadováno ověřování, je bezpečnější než spoléhání na nové řadiče a Razor stránky pro zahrnutí `[Authorize]` atributu.

<xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions>Třída také obsahuje <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> . `DefaultPolicy`Je zásada použitá u `[Authorize]` atributu, pokud není zadána žádná zásada. `[Authorize]`neobsahuje pojmenovanou zásadu (na rozdíl od) `[Authorize(PolicyName="MyPolicy")]` .

Další informace o zásadách najdete v tématu <xref:security/authorization/policies> .

Alternativní způsob, jakým se pro řadiče a Razor stránky MVC vyžaduje ověření všech uživatelů, je přidání autorizačního filtru:

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

Předchozí kód používá filtr autorizace a nastavení záložní Zásady používá směrování koncového bodu. Nastavení záložní zásady je upřednostňovaným způsobem, jak vyžadovat ověření všech uživatelů.

Přidat [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) na `Index` stránky a `Privacy` , aby anonymní uživatelé mohli získat informace o lokalitě před jejich registrací:

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a>Konfigurace testovacího účtu

`SeedData`Třída vytvoří dva účty: správce a manažer. Pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets) nastavte heslo pro tyto účty. Nastavte heslo z adresáře projektu (adresář obsahující *program.cs*):

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

Pokud není zadáno silné heslo, je vyvolána výjimka, když `SeedData.Initialize` je volána metoda.

Aktualizace `Main` pro použití testovacího hesla:

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>Vytvoření testovacích účtů a aktualizace kontaktů

Aktualizujte `Initialize` metodu ve `SeedData` třídě pro vytvoření testovacích účtů:

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

Přidejte ID uživatele správce a `ContactStatus` ke kontaktům. Poznamenejte si jednu z kontaktů "odeslané" a jednu "zamítnutou". Přidejte ke všem kontaktům ID a stav uživatele. Zobrazí se pouze jeden kontakt:

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>Vytváření obslužných rutin autorizace vlastníka, správce a správce

`ContactIsOwnerAuthorizationHandler`Ve složce pro *autorizaci* vytvořte třídu. `ContactIsOwnerAuthorizationHandler`Ověřuje, že uživatel, který pracuje na prostředku, je vlastníkem prostředku.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

`ContactIsOwnerAuthorizationHandler`Kontext volání [. Úspěšné](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , pokud je aktuální ověřený uživatel vlastníkem kontaktu. Obslužné rutiny autorizace obecně:

* Vrátí `context.Succeed` se, pokud jsou splněny požadavky.
* Vrátí se `Task.CompletedTask` , pokud nejsou splněné požadavky. `Task.CompletedTask`není úspěch nebo neúspěch &mdash; , umožňuje spuštění dalších obslužných rutin autorizace.

Pokud potřebujete explicitně selhat, vraťte [kontext. Selhání](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).

Aplikace umožňuje vlastníkům kontaktů upravit/odstranit/vytvořit vlastní data. `ContactIsOwnerAuthorizationHandler`není nutné kontrolovat operaci předanou parametrem požadavku.

### <a name="create-a-manager-authorization-handler"></a>Vytvoření obslužné rutiny autorizace Správce

`ContactManagerAuthorizationHandler`Ve složce pro *autorizaci* vytvořte třídu. Ověří uživatele, který pracuje `ContactManagerAuthorizationHandler` na prostředku, jako správce. Pouze správci mohou schvalovat nebo odmítat změny v obsahu (nové nebo změněné).

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>Vytvoření obslužné rutiny autorizace Správce

`ContactAdministratorsAuthorizationHandler`Ve složce pro *autorizaci* vytvořte třídu. `ContactAdministratorsAuthorizationHandler`Ověří uživatele, který působí na prostředku, jako správce. Správce může provádět všechny operace.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>Registrace obslužných rutin autorizace

Služby používající Entity Framework Core musí být registrovány pro [vkládání závislostí](xref:fundamentals/dependency-injection) pomocí [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions). Rozhraní `ContactIsOwnerAuthorizationHandler` používá ASP.NET Core [Identity](xref:security/authentication/identity) , které je postavené na Entity Framework Core. Zaregistrujte obslužné rutiny u kolekce služeb, aby byly k dispozici pro `ContactsController` [vkládání závislostí](xref:fundamentals/dependency-injection). Přidejte následující kód na konec `ConfigureServices` :

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

`ContactAdministratorsAuthorizationHandler`a `ContactManagerAuthorizationHandler` jsou přidány jako singleton. Jsou typu Singleton, protože nepoužívají EF a všechny potřebné informace jsou v `Context` parametru `HandleRequirementAsync` metody.

## <a name="support-authorization"></a>Autorizace podpory

V této části aktualizujete Razor stránky a přidáte třídu požadavků na operace.

### <a name="review-the-contact-operations-requirements-class"></a>Kontrola třídy požadavků na operace kontaktů

Zkontrolujte `ContactOperations` třídu. Tato třída obsahuje požadavky, které aplikace podporuje:

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a>Vytvoření základní třídy pro Razor stránky kontaktů

Vytvořte základní třídu, která obsahuje služby používané na Razor stránkách kontaktů. Základní třída umístí inicializační kód do jednoho umístění:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

Předcházející kód:

* Přidá `IAuthorizationService` službu pro přístup k obslužným rutinám autorizace.
* Přidá Identity `UserManager` službu.
* Přidejte `ApplicationDbContext` .

### <a name="update-the-createmodel"></a>Aktualizace CreateModel

Aktualizujte konstruktor Create Page model tak, aby používal `DI_BasePageModel` základní třídu:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

Aktualizujte `CreateModel.OnPostAsync` metodu na:

* Přidejte ID uživatele do `Contact` modelu.
* Voláním obslužné rutiny autorizace ověřte, zda má uživatel oprávnění k vytváření kontaktů.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>Aktualizace IndexModel

Aktualizujte `OnGetAsync` metodu tak, aby se pro obecné uživatele zobrazovaly jenom schválené kontakty:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>Aktualizace EditModel

Přidejte obslužnou rutinu autorizace pro ověření, že uživatel vlastní kontakt. Vzhledem k tomu, že je ověřována autorizace prostředků, není `[Authorize]` atribut dostatečně. Aplikace nemá při vyhodnocování atributů přístup k prostředku. Ověřování na základě prostředků musí být nezbytné. Kontroly musí být provedeny, jakmile aplikace má přístup k prostředku, a to buď načtením v modelu stránky, nebo jejich načtením v rámci samotné obslužné rutiny. K prostředku často přistupujete předáním klíče prostředku.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>Aktualizace DeleteModel

Aktualizujte odstranit model stránky tak, aby používal obslužnou rutinu autorizace k ověření, jestli má uživatel oprávnění k odstranění kontaktu.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>Vložení autorizační služby do zobrazení

V současné době uživatelské rozhraní zobrazuje odkazy upravit a odstranit pro kontakty, které uživatel nemůže upravovat.

Vložení autorizační služby do souboru *Pages/_ViewImports. cshtml* , aby bylo dostupné pro všechna zobrazení:

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

Předchozí kód přidá několik `using` příkazů.

Aktualizujte odkazy pro **Úpravy** a **odstranění** na *stránkách/kontakty/index. cshtml* , aby byly vygenerovány pouze pro uživatele s příslušnými oprávněními:

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> Skrytím odkazů uživatelů, kteří nemají oprávnění ke změně dat, nebude aplikace zabezpečená. Skrytím odkazů je aplikace uživatelsky přívětivější zobrazením pouze platných odkazů. Uživatelé mohou napadení vygenerovaných adres URL vyvolávat a vyvolat operace úpravy a odstranění na data, která nevlastní. RazorStránka nebo kontroler musí vymáhat kontroly přístupu pro zabezpečení dat.

### <a name="update-details"></a>Aktualizovat podrobnosti

Aktualizujte zobrazení podrobností tak, aby manažeři mohli schvalovat nebo odmítat kontakty:

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

Aktualizujte model stránky podrobností:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>Přidání nebo odebrání uživatele k roli

Informace o [tomto problému najdete zde](https://github.com/dotnet/AspNetCore.Docs/issues/8502) :

* Odebírají se oprávnění od uživatele. Například ztlumení uživatele v aplikaci chatu.
* Přidávání oprávnění uživateli.

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a>Rozdíly mezi výzvou a zakazující

Tato aplikace nastaví výchozí zásadu, která bude [vyžadovat ověřené uživatele](#rau). Následující kód povoluje anonymní uživatele. Anonymní uživatelé mají povoleno zobrazovat rozdíly mezi výzvou a zakázáním.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

V předchozím kódu:

* Pokud uživatel **není ověřen,** `ChallengeResult` je vrácena. Když `ChallengeResult` se vrátí, uživatel se přesměruje na přihlašovací stránku.
* Pokud je uživatel ověřený, ale není autorizovaný, `ForbidResult` vrátí se. Když `ForbidResult` se vrátí, uživatel se přesměruje na stránku přístup byl odepřen.

## <a name="test-the-completed-app"></a>Testování dokončené aplikace

Pokud jste ještě nenastavili heslo pro osazené uživatelské účty, nastavte heslo pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets#secret-manager) :

* Vyberte silné heslo: použijte osm nebo více znaků a alespoň jedno velké písmeno, číslici a symbol. Například `Passw0rd!` splňuje požadavky na silné heslo.
* Spusťte následující příkaz ze složky projektu, kde `<PW>` je heslo:

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

Pokud má aplikace kontakty:

* Odstraní všechny záznamy v `Contact` tabulce.
* Restartujte aplikaci a dosadíte databázi.

Snadný způsob, jak otestovat dokončenou aplikaci, je spustit tři různé prohlížeče (nebo relace anonymním/InPrivate). V jednom prohlížeči Zaregistrujte nového uživatele (například `test@example.com` ). Přihlaste se ke každému prohlížeči pomocí jiného uživatele. Ověřte následující operace:

* Registrovaní uživatelé mohou zobrazit všechna schválená kontaktní data.
* Registrovaní uživatelé můžou upravovat nebo odstraňovat svá vlastní data.
* Manažeři mohou schvalovat nebo odmítat kontaktní data. `Details`Zobrazení zobrazuje tlačítka **schválení** a **odmítnutí** .
* Správci mohou schvalovat nebo odmítat a upravovat nebo odstraňovat všechna data.

| Uživatel                | Podsazený aplikací | Možnosti                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | No                | Upravit nebo odstranit vlastní data.                |
| manager@contoso.com | Yes               | Schvalovat nebo odmítat a upravovat/odstraňovat vlastní data. |
| admin@contoso.com   | Yes               | Schválí nebo odmítne a upraví nebo odstraní všechna data. |

V prohlížeči správce vytvořte kontakt. Zkopírujte adresu URL pro odstranění a úpravy v kontaktní osobě správce. Vložením těchto odkazů do prohlížeče testovacího uživatele ověříte, že testovací uživatel nemůže tyto operace provést.

## <a name="create-the-starter-app"></a>Vytvoření úvodní aplikace

* Vytvoření Razor aplikace Pages s názvem "ContactManager"
  * Vytvořte aplikaci pomocí **individuálních uživatelských účtů**.
  * Pojmenujte ho "ContactManager", aby se obor názvů shodoval s oborem názvů použitým v ukázce.
  * `-uld`Určuje LocalDB místo SQLite.

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* Přidat *modely/kontakt. cs*:

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* Generování uživatelského rozhraní `Contact` modelu
* Vytvořit počáteční migraci a aktualizovat databázi:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

Pokud se zobrazí chyba s `dotnet aspnet-codegenerator razorpage` příkazem, přečtěte si [Tento problém GitHub](https://github.com/aspnet/Scaffolding/issues/984).

* Aktualizujte kotvu **ContactManager** v souboru *Pages/shared/_Layout. cshtml* :

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* Testování aplikace vytvořením, úpravou a odstraněním kontaktu

### <a name="seed-the-database"></a>Dosazení databáze

Do složky *data* přidejte třídu [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) :

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

Zavolat `SeedData.Initialize` z `Main` :

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

Otestujte, že aplikace dosazený databázi. Pokud je ve službě Contact DB nějaký řádek, metoda počáteční hodnoty se nespustí.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

V tomto kurzu se dozvíte, jak vytvořit webovou aplikaci ASP.NET Core s uživatelskými daty chráněnými autorizací. Zobrazuje seznam kontaktů ověřovaných (registrovaných) uživatelů. Existují tři skupiny zabezpečení:

* **Registrovaní uživatelé** můžou zobrazit všechna schválená data a můžou upravovat nebo odstraňovat vlastní data.
* **Manažeři** mohou schvalovat nebo odmítat kontaktní data. Uživatelé vidí jenom schválené kontakty.
* **Správci** mohou schvalovat nebo odmítat a upravovat nebo odstraňovat jakákoli data.

Na následujícím obrázku je uživatel Rick ( `rick@example.com` ) přihlášen. Rick může zobrazit pouze schválené kontakty a **Upravit** / **Odstranit** / **nové** odkazy pro své kontakty. Jenom poslední záznam vytvořený pomocí Rick zobrazí odkazy pro **Úpravy** a **odstranění** . Ostatní uživatelé uvidí poslední záznam, dokud správce nebo správce nezmění stav na schváleno.

![Snímek obrazovky zobrazující přihlášený Rick](secure-data/_static/rick.png)

Na následujícím obrázku `manager@contoso.com` je přihlášen a v roli manažera:

![Snímek obrazovky zobrazující manager@contoso.com přihlášený](secure-data/_static/manager1.png)

Následující obrázek ukazuje zobrazení podrobností o kontaktu pro správce:

![Zobrazení kontaktu manažera](secure-data/_static/manager.png)

Tlačítka **schválení** a **odmítnutí** se zobrazují pouze pro manažery a správce.

Na následujícím obrázku `admin@contoso.com` je přihlášen a v roli správce:

![Snímek obrazovky zobrazující admin@contoso.com přihlášený](secure-data/_static/admin.png)

Správce má všechna oprávnění. Může číst, upravovat a odstraňovat jakékoli kontakty a měnit stav kontaktů.

Aplikace byla vytvořena pomocí [generování uživatelského rozhraní](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) tohoto `Contact` modelu:

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

Ukázka obsahuje následující obslužné rutiny autorizace:

* `ContactIsOwnerAuthorizationHandler`: Zajistí, že uživatel může upravovat pouze svá data.
* `ContactManagerAuthorizationHandler`: Umožňuje správcům schvalovat nebo odmítat kontakty.
* `ContactAdministratorsAuthorizationHandler`: Umožňuje správcům schvalovat nebo odmítat kontakty a upravovat nebo odstraňovat kontakty.

## <a name="prerequisites"></a>Předpoklady

Tento kurz je pokročilý. Měli byste být obeznámeni s:

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [Authentication](xref:security/authentication/identity)
* [Potvrzení účtu a obnovení hesla](xref:security/authentication/accconfirm)
* [Autorizace](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>Úvodní a dokončená aplikace

[Stáhněte](xref:index#how-to-download-a-sample) [dokončenou](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) aplikaci. [Otestujte](#test-the-completed-app) dokončenou aplikaci, abyste se seznámili se svými funkcemi zabezpečení.

### <a name="the-starter-app"></a>Úvodní aplikace

[Stáhněte](xref:index#how-to-download-a-sample) si [úvodní](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) aplikaci.

Spusťte aplikaci, klepněte na odkaz **ContactManager** a ověřte, že můžete vytvořit, upravit a odstranit kontakt.

## <a name="secure-user-data"></a>Zabezpečená uživatelská data

V následujících částech najdete všechny hlavní kroky k vytvoření aplikace zabezpečených uživatelských dat. Může to být užitečné pro odkazování na dokončený projekt.

### <a name="tie-the-contact-data-to-the-user"></a>Spojit kontaktní údaje s uživatelem

Pomocí [Identity](xref:security/authentication/identity) ID uživatele ASP.NET zajistěte, aby mohli uživatelé upravovat data, ale ne jiná data uživatelů. Přidejte `OwnerID` a `ContactStatus` do `Contact` modelu:

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID`je ID uživatele z `AspNetUser` tabulky v [Identity](xref:security/authentication/identity) databázi. `Status`Pole určuje, jestli je kontakt viditelný pro obecné uživatele.

Vytvořte novou migraci a aktualizujte databázi:

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a>Přidat služby rolí doIdentity

Připojit [Přidat role](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) pro přidání služeb role:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a>Vyžadovat ověřené uživatele

Nastavte výchozí zásadu ověřování tak, aby vyžadovala ověření uživatelů:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 Ověřování můžete na Razor úrovni stránky, řadiče nebo akce pomocí atributu odsouhlasit `[AllowAnonymous]` . Nastavení výchozích zásad ověřování, aby vyžadovaly ověření uživatelů, chrání nově přidané Razor stránky a řadiče. Pokud je ve výchozím nastavení vyžadováno ověřování, je bezpečnější než spoléhání na nové řadiče a Razor stránky pro zahrnutí `[Authorize]` atributu.

Přidejte [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) na stránky rejstřík, o a kontakt, aby anonymní uživatelé mohli získat informace o lokalitě před jejich registrací.

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a>Konfigurace testovacího účtu

`SeedData`Třída vytvoří dva účty: správce a manažer. Pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets) nastavte heslo pro tyto účty. Nastavte heslo z adresáře projektu (adresář obsahující *program.cs*):

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

Pokud není zadáno silné heslo, je vyvolána výjimka, když `SeedData.Initialize` je volána metoda.

Aktualizace `Main` pro použití testovacího hesla:

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>Vytvoření testovacích účtů a aktualizace kontaktů

Aktualizujte `Initialize` metodu ve `SeedData` třídě pro vytvoření testovacích účtů:

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

Přidejte ID uživatele správce a `ContactStatus` ke kontaktům. Poznamenejte si jednu z kontaktů "odeslané" a jednu "zamítnutou". Přidejte ke všem kontaktům ID a stav uživatele. Zobrazí se pouze jeden kontakt:

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>Vytváření obslužných rutin autorizace vlastníka, správce a správce

Vytvořte složku *autorizace* a `ContactIsOwnerAuthorizationHandler` v ní vytvořte třídu. `ContactIsOwnerAuthorizationHandler`Ověřuje, že uživatel, který pracuje na prostředku, je vlastníkem prostředku.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

`ContactIsOwnerAuthorizationHandler`Kontext volání [. Úspěšné](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , pokud je aktuální ověřený uživatel vlastníkem kontaktu. Obslužné rutiny autorizace obecně:

* Vrátí `context.Succeed` se, pokud jsou splněny požadavky.
* Vrátí se `Task.CompletedTask` , pokud nejsou splněné požadavky. `Task.CompletedTask`není úspěch nebo neúspěch &mdash; , umožňuje spuštění dalších obslužných rutin autorizace.

Pokud potřebujete explicitně selhat, vraťte [kontext. Selhání](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).

Aplikace umožňuje vlastníkům kontaktů upravit/odstranit/vytvořit vlastní data. `ContactIsOwnerAuthorizationHandler`není nutné kontrolovat operaci předanou parametrem požadavku.

### <a name="create-a-manager-authorization-handler"></a>Vytvoření obslužné rutiny autorizace Správce

`ContactManagerAuthorizationHandler`Ve složce pro *autorizaci* vytvořte třídu. Ověří uživatele, který pracuje `ContactManagerAuthorizationHandler` na prostředku, jako správce. Pouze správci mohou schvalovat nebo odmítat změny v obsahu (nové nebo změněné).

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>Vytvoření obslužné rutiny autorizace Správce

`ContactAdministratorsAuthorizationHandler`Ve složce pro *autorizaci* vytvořte třídu. `ContactAdministratorsAuthorizationHandler`Ověří uživatele, který působí na prostředku, jako správce. Správce může provádět všechny operace.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>Registrace obslužných rutin autorizace

Služby používající Entity Framework Core musí být registrovány pro [vkládání závislostí](xref:fundamentals/dependency-injection) pomocí [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions). Rozhraní `ContactIsOwnerAuthorizationHandler` používá ASP.NET Core [Identity](xref:security/authentication/identity) , které je postavené na Entity Framework Core. Zaregistrujte obslužné rutiny u kolekce služeb, aby byly k dispozici pro `ContactsController` [vkládání závislostí](xref:fundamentals/dependency-injection). Přidejte následující kód na konec `ConfigureServices` :

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

`ContactAdministratorsAuthorizationHandler`a `ContactManagerAuthorizationHandler` jsou přidány jako singleton. Jsou typu Singleton, protože nepoužívají EF a všechny potřebné informace jsou v `Context` parametru `HandleRequirementAsync` metody.

## <a name="support-authorization"></a>Autorizace podpory

V této části aktualizujete Razor stránky a přidáte třídu požadavků na operace.

### <a name="review-the-contact-operations-requirements-class"></a>Kontrola třídy požadavků na operace kontaktů

Zkontrolujte `ContactOperations` třídu. Tato třída obsahuje požadavky, které aplikace podporuje:

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a>Vytvoření základní třídy pro Razor stránky kontaktů

Vytvořte základní třídu, která obsahuje služby používané na Razor stránkách kontaktů. Základní třída umístí inicializační kód do jednoho umístění:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

Předcházející kód:

* Přidá `IAuthorizationService` službu pro přístup k obslužným rutinám autorizace.
* Přidá Identity `UserManager` službu.
* Přidejte `ApplicationDbContext` .

### <a name="update-the-createmodel"></a>Aktualizace CreateModel

Aktualizujte konstruktor Create Page model tak, aby používal `DI_BasePageModel` základní třídu:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

Aktualizujte `CreateModel.OnPostAsync` metodu na:

* Přidejte ID uživatele do `Contact` modelu.
* Voláním obslužné rutiny autorizace ověřte, zda má uživatel oprávnění k vytváření kontaktů.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>Aktualizace IndexModel

Aktualizujte `OnGetAsync` metodu tak, aby se pro obecné uživatele zobrazovaly jenom schválené kontakty:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>Aktualizace EditModel

Přidejte obslužnou rutinu autorizace pro ověření, že uživatel vlastní kontakt. Vzhledem k tomu, že je ověřována autorizace prostředků, není `[Authorize]` atribut dostatečně. Aplikace nemá při vyhodnocování atributů přístup k prostředku. Ověřování na základě prostředků musí být nezbytné. Kontroly musí být provedeny, jakmile aplikace má přístup k prostředku, a to buď načtením v modelu stránky, nebo jejich načtením v rámci samotné obslužné rutiny. K prostředku často přistupujete předáním klíče prostředku.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>Aktualizace DeleteModel

Aktualizujte odstranit model stránky tak, aby používal obslužnou rutinu autorizace k ověření, jestli má uživatel oprávnění k odstranění kontaktu.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>Vložení autorizační služby do zobrazení

V současné době uživatelské rozhraní zobrazuje odkazy upravit a odstranit pro kontakty, které uživatel nemůže upravovat.

Vložení autorizační služby do souboru *views/_ViewImports. cshtml* , aby byla dostupná pro všechna zobrazení:

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

Předchozí kód přidá několik `using` příkazů.

Aktualizujte odkazy pro **Úpravy** a **odstranění** na *stránkách/kontakty/index. cshtml* , aby byly vygenerovány pouze pro uživatele s příslušnými oprávněními:

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> Skrytím odkazů uživatelů, kteří nemají oprávnění ke změně dat, nebude aplikace zabezpečená. Skrytím odkazů je aplikace uživatelsky přívětivější zobrazením pouze platných odkazů. Uživatelé mohou napadení vygenerovaných adres URL vyvolávat a vyvolat operace úpravy a odstranění na data, která nevlastní. RazorStránka nebo kontroler musí vymáhat kontroly přístupu pro zabezpečení dat.

### <a name="update-details"></a>Aktualizovat podrobnosti

Aktualizujte zobrazení podrobností tak, aby manažeři mohli schvalovat nebo odmítat kontakty:

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

Aktualizujte model stránky podrobností:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>Přidání nebo odebrání uživatele k roli

Informace o [tomto problému najdete zde](https://github.com/dotnet/AspNetCore.Docs/issues/8502) :

* Odebírají se oprávnění od uživatele. Například ztlumení uživatele v aplikaci chatu.
* Přidávání oprávnění uživateli.

## <a name="test-the-completed-app"></a>Testování dokončené aplikace

Pokud jste ještě nenastavili heslo pro osazené uživatelské účty, nastavte heslo pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets#secret-manager) :

* Vyberte silné heslo: použijte osm nebo více znaků a alespoň jedno velké písmeno, číslici a symbol. Například `Passw0rd!` splňuje požadavky na silné heslo.
* Spusťte následující příkaz ze složky projektu, kde `<PW>` je heslo:

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* Zrušení a aktualizace databáze

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* Restartujte aplikaci a dosadíte databázi.

Snadný způsob, jak otestovat dokončenou aplikaci, je spustit tři různé prohlížeče (nebo relace anonymním/InPrivate). V jednom prohlížeči Zaregistrujte nového uživatele (například `test@example.com` ). Přihlaste se ke každému prohlížeči pomocí jiného uživatele. Ověřte následující operace:

* Registrovaní uživatelé mohou zobrazit všechna schválená kontaktní data.
* Registrovaní uživatelé můžou upravovat nebo odstraňovat svá vlastní data.
* Manažeři mohou schvalovat nebo odmítat kontaktní data. `Details`Zobrazení zobrazuje tlačítka **schválení** a **odmítnutí** .
* Správci mohou schvalovat nebo odmítat a upravovat nebo odstraňovat všechna data.

| Uživatel                | Podsazený aplikací | Možnosti                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | No                | Upravit nebo odstranit vlastní data.                |
| manager@contoso.com | Yes               | Schvalovat nebo odmítat a upravovat/odstraňovat vlastní data. |
| admin@contoso.com   | Yes               | Schválí nebo odmítne a upraví nebo odstraní všechna data. |

V prohlížeči správce vytvořte kontakt. Zkopírujte adresu URL pro odstranění a úpravy v kontaktní osobě správce. Vložením těchto odkazů do prohlížeče testovacího uživatele ověříte, že testovací uživatel nemůže tyto operace provést.

## <a name="create-the-starter-app"></a>Vytvoření úvodní aplikace

* Vytvoření Razor aplikace Pages s názvem "ContactManager"
  * Vytvořte aplikaci pomocí **individuálních uživatelských účtů**.
  * Pojmenujte ho "ContactManager", aby se obor názvů shodoval s oborem názvů použitým v ukázce.
  * `-uld`Určuje LocalDB místo SQLite.

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* Přidat *modely/kontakt. cs*:

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* Generování uživatelského rozhraní `Contact` modelu
* Vytvořit počáteční migraci a aktualizovat databázi:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* Aktualizujte kotvu **ContactManager** v souboru *pages/_Layout. cshtml* :

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* Testování aplikace vytvořením, úpravou a odstraněním kontaktu

### <a name="seed-the-database"></a>Dosazení databáze

Do složky *data* přidejte třídu [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) .

Zavolat `SeedData.Initialize` z `Main` :

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

Otestujte, že aplikace dosazený databázi. Pokud je ve službě Contact DB nějaký řádek, metoda počáteční hodnoty se nespustí.

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a>Další zdroje informací

* [Vytvoření webové aplikace .NET Core využívající SQL Database ve službě Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [ASP.NET Core autorizačního prostředí](https://github.com/blowdart/AspNetAuthorizationWorkshop). Tato laboratoř obsahuje další podrobnosti o funkcích zabezpečení, které jsou představené v tomto kurzu.
* <xref:security/authorization/introduction>
* [Vlastní autorizace na základě zásad](xref:security/authorization/policies)

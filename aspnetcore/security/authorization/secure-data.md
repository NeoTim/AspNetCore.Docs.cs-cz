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
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a>Vytvoření aplikace ASP.NET Core s uživatelskými daty chráněnými autorizací

Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Jan Audette](https://twitter.com/joeaudette)

::: moniker range="<= aspnetcore-1.1"

Podívejte se na [Tento PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) pro ASP.NET Core verzi MVC. Verze tohoto kurzu ASP.NET Core 1,1 je v [této](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) složce. Ukázka 1,1 ASP.NET Core je v [ukázkách](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Zobrazit [Tento PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Tento kurz ukazuje, jak vytvořit webovou aplikaci ASP.NET Core s uživatelskými daty chráněnými autorizací. Zobrazí seznam kontakty, které ověřeným uživatelům (registrovaných) jste vytvořili. Existují tři skupiny zabezpečení:

* **Registrovaní uživatelé** můžou zobrazit všechna schválená data a můžou upravovat nebo odstraňovat vlastní data.
* **Manažeři** mohou schvalovat nebo odmítat kontaktní data. Uživatelé vidí pouze schválené kontakty.
* **Správci** mohou schvalovat nebo odmítat a upravovat nebo odstraňovat jakákoli data.

Obrázky v tomto dokumentu se přesně neshodují s nejnovějšími šablonami.

Na následujícím obrázku je uživatel Rick (`rick@example.com`) přihlášen. Rick může zobrazit pouze schválené kontakty a **upravovat**/**Odstranit**/**vytvořit nové** odkazy na své kontakty. Jenom poslední záznam vytvořený pomocí Rick zobrazí odkazy pro **Úpravy** a **odstranění** . Ostatní uživatelé neuvidí poslední záznam, dokud správce nebo správce změní stav na "Schváleno".

![Snímek obrazovky zobrazující Rick přihlášení](secure-data/_static/rick.png)

Na následujícím obrázku je `manager@contoso.com` přihlásit se a v roli manažera:

![Snímek obrazovky zobrazující přihlášený manager@contoso.com](secure-data/_static/manager1.png)

Následující obrázek ukazuje vedoucí zobrazení podrobností o kontaktu:

![Zobrazení manažera kontaktu](secure-data/_static/manager.png)

Tlačítka **schválení** a **odmítnutí** se zobrazují pouze pro manažery a správce.

Na následujícím obrázku je `admin@contoso.com` přihlásit se a v roli správce:

![Snímek obrazovky zobrazující přihlášený admin@contoso.com](secure-data/_static/admin.png)

Správce má všechna oprávnění. Může číst/upravovat/odstraňovat všechny kontakty a změnit stav kontakty.

Aplikace byla vytvořena pomocí [generování uživatelského rozhraní](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) následujícího modelu `Contact`:

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

Ukázka obsahuje následující rutiny autorizace:

* `ContactIsOwnerAuthorizationHandler`: zajišťuje, aby uživatel mohl upravovat pouze svá data.
* `ContactManagerAuthorizationHandler`: umožňuje správcům schvalovat nebo odmítat kontakty.
* `ContactAdministratorsAuthorizationHandler`: umožňuje správcům schvalovat nebo odmítat kontakty a upravovat nebo odstraňovat kontakty.

## <a name="prerequisites"></a>Předpoklady

V tomto kurzu je advanced. Měli byste se seznámit s:

* [Jádro ASP.NET](xref:tutorials/first-mvc-app/start-mvc)
* [Ověřování](xref:security/authentication/identity)
* [Potvrzení účtu a obnovení hesla](xref:security/authentication/accconfirm)
* [Autorizace](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>Starter a dokončené aplikace

[Stáhněte](xref:index#how-to-download-a-sample) [dokončenou](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) aplikaci. [Otestujte](#test-the-completed-app) dokončenou aplikaci, abyste se seznámili se svými funkcemi zabezpečení.

### <a name="the-starter-app"></a>Úvodní aplikaci

[Stáhněte](xref:index#how-to-download-a-sample) si [úvodní](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) aplikaci.

Spusťte aplikaci, klepněte na odkaz **ContactManager** a ověřte, že můžete vytvořit, upravit a odstranit kontakt.

## <a name="secure-user-data"></a>Zabezpečení dat uživatele

Následující oddíly mají všechny hlavní kroky k vytvoření aplikace pro data zabezpečení uživatele. Vám může být užitečné k odkazování na dokončení projektu.

### <a name="tie-the-contact-data-to-the-user"></a>Tie kontaktní údaje pro uživatele

Pomocí ID uživatele [Identity](xref:security/authentication/identity) ASP.NET zajistěte, aby mohli uživatelé upravovat data, ale ne jiná data uživatelů. Do modelu `Contact` přidejte `OwnerID` a `ContactStatus`:

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID` je ID uživatele z tabulky `AspNetUser` v databázi [identity](xref:security/authentication/identity) . Pole `Status` určuje, zda je kontakt viditelný pro obecné uživatele.

Vytvořte novou migraci a aktualizaci databáze:

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a>Přidání služby Role na identitu

Připojit [Přidat role](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) pro přidání služeb role:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a>Vyžadovat ověření uživatelé

Nastavte výchozí zásady ověřování tak, aby vyžadovala ověření uživatelů:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 Ověřování na úrovni této stránky, řadiče nebo metody akce lze odhlásit pomocí atributu `[AllowAnonymous]`. Nastavení výchozí zásady ověřování tak, aby vyžadovala ověření uživatelů chrání nově přidané Razor Pages a kontrolery. Pokud je ve výchozím nastavení vyžadováno ověřování, je bezpečnější než spoléhání na nové řadiče a Razor Pages pro zahrnutí atributu `[Authorize]`.

Přidejte [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) na stránky rejstřík a soukromí, aby anonymní uživatelé mohli získat informace o lokalitě před jejich registrací.

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a>Konfigurace testovací účet

Třída `SeedData` vytvoří dva účty: správce a manažer. Pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets) nastavte heslo pro tyto účty. Nastavte heslo z adresáře projektu (adresář obsahující *program.cs*):

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

Pokud není zadáno silné heslo, je vyvolána výjimka při volání `SeedData.Initialize`.

Aktualizujte `Main` pro použití testovacího hesla:

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>Vytvoření testovacích účtů a aktualizovat kontakty

Aktualizujte metodu `Initialize` ve třídě `SeedData` k vytvoření testovacích účtů:

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

Přidejte do kontaktů ID uživatele a `ContactStatus` správce. Proveďte jednu z kontakty "Submitted" a jedné "byl odmítnut". Přidáte ID uživatele a stav pro všechny kontakty. Je zobrazena pouze jeden kontakt:

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>Vytvořit vlastníka, správce a Správce autorizace obslužné rutiny

Ve složce pro *autorizaci* vytvořte třídu `ContactIsOwnerAuthorizationHandler`. `ContactIsOwnerAuthorizationHandler` ověří, že uživatel pracuje na prostředku, který je vlastníkem prostředku.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

`ContactIsOwnerAuthorizationHandler` volá [kontext. Úspěšné](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , pokud je aktuální ověřený uživatel vlastníkem kontaktu. Obslužné rutiny autorizace obecně:

* Vrátí `context.Succeed`, pokud jsou splněny požadavky.
* Vrátí `Task.CompletedTask`, pokud nejsou splněny požadavky. `Task.CompletedTask` není úspěch nebo neúspěch&mdash;umožňuje spuštění dalších obslužných rutin autorizace.

Pokud potřebujete explicitně selhat, vraťte [kontext. Selhání](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).

Aplikaci umožňuje kontaktní vlastníkům upravit, odstranit a vytvořit svoje vlastní data. `ContactIsOwnerAuthorizationHandler` nemusí kontrolovat operaci předanou parametrem požadavku.

### <a name="create-a-manager-authorization-handler"></a>Vytvořte obslužnou rutinu Správce autorizací

Ve složce pro *autorizaci* vytvořte třídu `ContactManagerAuthorizationHandler`. `ContactManagerAuthorizationHandler` ověří uživatele, který pracuje na prostředku, jako správce. Pouze vedoucí mohli schválit nebo odmítnout změny obsahu (nové nebo změněné).

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>Vytvořte obslužnou rutinu Správce autorizací

Ve složce pro *autorizaci* vytvořte třídu `ContactAdministratorsAuthorizationHandler`. `ContactAdministratorsAuthorizationHandler` ověří uživatele, který pracuje na prostředku, jako správce. Správce může provádět všechny operace.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>Zaregistrujte obslužné rutiny autorizace

Služby používající Entity Framework Core musí být registrovány pro [vkládání závislostí](xref:fundamentals/dependency-injection) pomocí [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions). `ContactIsOwnerAuthorizationHandler` používá ASP.NET Core [identity](xref:security/authentication/identity), která je postavená na Entity Framework Core. Zaregistrujte obslužné rutiny s kolekcí služeb, aby byly k dispozici pro `ContactsController` prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection). Do konce `ConfigureServices`přidejte následující kód:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

`ContactAdministratorsAuthorizationHandler` a `ContactManagerAuthorizationHandler` jsou přidány jako singleton. Jsou to jednoznačné, protože nepoužívají EF a všechny potřebné informace jsou v parametru `Context` `HandleRequirementAsync` metody.

## <a name="support-authorization"></a>Povolení podpory

V této části se aktualizace stránky Razor a přidejte třídu požadavků operace.

### <a name="review-the-contact-operations-requirements-class"></a>Zkontrolujte požadavky na třídy kontaktní operace

Zkontrolujte třídu `ContactOperations`. Tato třída obsahuje požadavky na aplikace podporuje:

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a>Vytvoření základní třídu pro stránky Razor kontakty

Vytvořte základní třídu, která obsahuje služby využité v kontaktech stránky Razor. Základní třída vloží kód inicializace na jednom místě:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

Předchozí kód:

* Přidá službu `IAuthorizationService` pro přístup k obslužným rutinám autorizace.
* Přidá službu identity `UserManager`.
* Přidejte `ApplicationDbContext`.

### <a name="update-the-createmodel"></a>Aktualizace CreateModel

Aktualizujte konstruktor Create Page model tak, aby používal základní třídu `DI_BasePageModel`:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

Aktualizujte metodu `CreateModel.OnPostAsync` na:

* Přidejte ID uživatele do modelu `Contact`.
* Ověřte, zda že má uživatel oprávnění k vytvoření kontakty obslužná rutina ověřování volejte.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>Aktualizace IndexModel

Aktualizujte metodu `OnGetAsync`, aby se pro obecné uživatele zobrazovaly jenom schválené kontakty:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>Aktualizace EditModel

Přidejte obslužnou rutinu ověřování k ověření, že uživatel je vlastníkem kontaktu. Vzhledem k tomu, že se ověřuje autorizace prostředků, atribut `[Authorize]` není dostatečný. Aplikace nemá přístup k prostředku, když jsou vyhodnocovány atributy. Autorizace na základě prostředků musí být nezbytné. Kontroly musí provést, když má aplikace přístup k prostředku načtením v modelu stránky nebo načtením v rámci samotná obslužná rutina. Často přistupovat k prostředku předáním klíč prostředku.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>Aktualizace DeleteModel

Aktualizace modelu stránku odstranit sloužící k ověření, že uživatel má oprávnění odstranit u kontaktu obslužnou rutinu ověřování.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>Vložit do zobrazení autorizační službu

V současné době ukazuje uživatelského rozhraní upravovat a odstraňovat kontakty, které uživatel nemůže upravovat odkazy.

Vložení autorizační služby do souboru *Pages/_ViewImports. cshtml* , aby bylo dostupné pro všechna zobrazení:

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

Předchozí kód přidá několik příkazů `using`.

Aktualizujte odkazy pro **Úpravy** a **odstranění** na *stránkách/kontakty/index. cshtml* , aby byly vygenerovány pouze pro uživatele s příslušnými oprávněními:

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> Skrytí propojení od uživatele, kteří nemají oprávnění ke změně dat nebude zabezpečení aplikace. Skrytí propojení nastaví aplikaci jako přívětivější tím, že zobrazuje pouze platné odkazy. Uživatelé můžou hack generované adres URL pro vyvolání funkce upravit a odstranit operací s daty, které není vlastní. Stránka Razor nebo kontroleru musí vynucovat kontroly přístupu pro data zabezpečení.

### <a name="update-details"></a>Podrobné informace o aktualizaci

Aktualizujte zobrazení podrobností, aby vedoucí mohli schválit nebo odmítnout kontaktů:

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

Model stránky podrobnosti aktualizace:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>Přidat nebo odebrat uživatele k roli

Informace o [tomto problému najdete zde](https://github.com/dotnet/AspNetCore.Docs/issues/8502) :

* Odebrání oprávnění uživatele. Například ztlumení uživatele v aplikaci chatu.
* Přidání oprávnění pro uživatele.

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a>Rozdíly mezi výzvou a zakazující

Tato aplikace nastaví výchozí zásadu, která bude [vyžadovat ověřené uživatele](#require-authenticated-users). Následující kód povoluje anonymní uživatele. Anonymní uživatelé mají povoleno zobrazovat rozdíly mezi výzvou a zakázáním.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

V předchozím kódu:

* Pokud uživatel **není ověřen,** je vrácena `ChallengeResult`. Po vrácení `ChallengeResult` se uživatel přesměruje na přihlašovací stránku.
* Když je uživatel ověřený, ale není autorizovaný, vrátí se `ForbidResult`. Po vrácení `ForbidResult` se uživatel přesměruje na stránku přístup byl odepřen.

## <a name="test-the-completed-app"></a>Testování dokončené aplikace

Pokud jste ještě nenastavili heslo pro osazené uživatelské účty, nastavte heslo pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets#secret-manager) :

* Zvolte silné heslo: použití osm nebo více znaků, aspoň jedno velké písmeno, číslo a symbol. `Passw0rd!` například splňuje požadavky na silné heslo.
* Spusťte následující příkaz ze složky projektu, kde `<PW>` je heslo:

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

Pokud má kontaktů:

* Odstraní všechny záznamy v `Contact` tabulce.
* Restartujte aplikaci k přidání dat do databáze.

Snadný způsob, jak otestovat dokončená aplikace je spustíte tři různé prohlížeče (nebo incognito/InPrivate relace). V jednom prohlížeči Zaregistrujte nového uživatele (například `test@example.com`). Přihlaste se k každým prohlížečem s jiným uživatelem. Ověřte následující operace:

* Registrovaných uživatelů můžete zobrazit všechny schválené kontaktní údaje.
* Registrovaných uživatelů můžete upravit nebo odstranit svá vlastní data.
* Správci mohou schvalovat a odmítat kontaktní údaje. Zobrazení `Details` zobrazuje tlačítka **schválení** a **odmítnutí** .
* Správci můžou schvalovat a odmítat a upravit nebo odstranit všechna data.

| Uživatel                | Nasazených aplikací | Možnosti                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | Ne                | Upravit nebo odstranit vlastní data.                |
| manager@contoso.com | Ano               | Vlastní data, schvalovat a odmítat a upravit nebo odstranit. |
| admin@contoso.com   | Ano               | Schválit nebo odmítnout a upravit nebo odstranit všechna data. |

Vytvoření kontaktu v prohlížeči na správce. Zkopírujte adresu URL pro odstranění a upravit z kontaktujte správce. Vložte tyto odkazy do testů webového prohlížeče k ověření, že testovací uživatel nemůže provádět tyto operace.

## <a name="create-the-starter-app"></a>Vytvořit úvodní aplikaci

* Vytvoření aplikace stránky Razor s názvem "ContactManager"
  * Vytvořte aplikaci pomocí **individuálních uživatelských účtů**.
  * Pojmenujte ji "ContactManager" tak obor názvů odpovídá oboru názvů použitého v ukázce.
  * `-uld` určuje LocalDB místo SQLite.

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* Přidat *modely/kontakt. cs*:

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* Generování `Contact` modelu.
* Vytvořte počáteční migraci a aktualizaci databáze:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

Pokud dojde k chybě s příkazem `dotnet aspnet-codegenerator razorpage`, přečtěte si [Tento problém GitHub](https://github.com/aspnet/Scaffolding/issues/984).

* Aktualizujte kotvu **ContactManager** v souboru *Pages/shared/_Layout. cshtml* :

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* Testování aplikace pomocí vytváření, úpravy a odstranění kontaktu

### <a name="seed-the-database"></a>Přidání dat do databáze

Do složky *data* přidejte třídu [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) :

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

Volat `SeedData.Initialize` z `Main`:

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

Otestujte, že aplikace naplnila databázi. Pokud existují nějaké řádky v kontaktu DB, metoda počáteční hodnoty není spuštěna.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

Tento kurz ukazuje, jak vytvořit webovou aplikaci ASP.NET Core s uživatelskými daty chráněnými autorizací. Zobrazí seznam kontakty, které ověřeným uživatelům (registrovaných) jste vytvořili. Existují tři skupiny zabezpečení:

* **Registrovaní uživatelé** můžou zobrazit všechna schválená data a můžou upravovat nebo odstraňovat vlastní data.
* **Manažeři** mohou schvalovat nebo odmítat kontaktní data. Uživatelé vidí pouze schválené kontakty.
* **Správci** mohou schvalovat nebo odmítat a upravovat nebo odstraňovat jakákoli data.

Na následujícím obrázku je uživatel Rick (`rick@example.com`) přihlášen. Rick může zobrazit pouze schválené kontakty a **upravovat**/**Odstranit**/**vytvořit nové** odkazy na své kontakty. Jenom poslední záznam vytvořený pomocí Rick zobrazí odkazy pro **Úpravy** a **odstranění** . Ostatní uživatelé neuvidí poslední záznam, dokud správce nebo správce změní stav na "Schváleno".

![Snímek obrazovky zobrazující Rick přihlášení](secure-data/_static/rick.png)

Na následujícím obrázku je `manager@contoso.com` přihlásit se a v roli manažera:

![Snímek obrazovky zobrazující přihlášený manager@contoso.com](secure-data/_static/manager1.png)

Následující obrázek ukazuje vedoucí zobrazení podrobností o kontaktu:

![Zobrazení manažera kontaktu](secure-data/_static/manager.png)

Tlačítka **schválení** a **odmítnutí** se zobrazují pouze pro manažery a správce.

Na následujícím obrázku je `admin@contoso.com` přihlásit se a v roli správce:

![Snímek obrazovky zobrazující přihlášený admin@contoso.com](secure-data/_static/admin.png)

Správce má všechna oprávnění. Může číst/upravovat/odstraňovat všechny kontakty a změnit stav kontakty.

Aplikace byla vytvořena pomocí [generování uživatelského rozhraní](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) následujícího modelu `Contact`:

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

Ukázka obsahuje následující rutiny autorizace:

* `ContactIsOwnerAuthorizationHandler`: zajišťuje, aby uživatel mohl upravovat pouze svá data.
* `ContactManagerAuthorizationHandler`: umožňuje správcům schvalovat nebo odmítat kontakty.
* `ContactAdministratorsAuthorizationHandler`: umožňuje správcům schvalovat nebo odmítat kontakty a upravovat nebo odstraňovat kontakty.

## <a name="prerequisites"></a>Předpoklady

V tomto kurzu je advanced. Měli byste se seznámit s:

* [Jádro ASP.NET](xref:tutorials/first-mvc-app/start-mvc)
* [Ověřování](xref:security/authentication/identity)
* [Potvrzení účtu a obnovení hesla](xref:security/authentication/accconfirm)
* [Autorizace](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>Starter a dokončené aplikace

[Stáhněte](xref:index#how-to-download-a-sample) [dokončenou](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) aplikaci. [Otestujte](#test-the-completed-app) dokončenou aplikaci, abyste se seznámili se svými funkcemi zabezpečení.

### <a name="the-starter-app"></a>Úvodní aplikaci

[Stáhněte](xref:index#how-to-download-a-sample) si [úvodní](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) aplikaci.

Spusťte aplikaci, klepněte na odkaz **ContactManager** a ověřte, že můžete vytvořit, upravit a odstranit kontakt.

## <a name="secure-user-data"></a>Zabezpečení dat uživatele

Následující oddíly mají všechny hlavní kroky k vytvoření aplikace pro data zabezpečení uživatele. Vám může být užitečné k odkazování na dokončení projektu.

### <a name="tie-the-contact-data-to-the-user"></a>Tie kontaktní údaje pro uživatele

Pomocí ID uživatele [Identity](xref:security/authentication/identity) ASP.NET zajistěte, aby mohli uživatelé upravovat data, ale ne jiná data uživatelů. Do modelu `Contact` přidejte `OwnerID` a `ContactStatus`:

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID` je ID uživatele z tabulky `AspNetUser` v databázi [identity](xref:security/authentication/identity) . Pole `Status` určuje, zda je kontakt viditelný pro obecné uživatele.

Vytvořte novou migraci a aktualizaci databáze:

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a>Přidání služby Role na identitu

Připojit [Přidat role](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) pro přidání služeb role:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a>Vyžadovat ověření uživatelé

Nastavte výchozí zásady ověřování tak, aby vyžadovala ověření uživatelů:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 Ověřování na úrovni této stránky, řadiče nebo metody akce lze odhlásit pomocí atributu `[AllowAnonymous]`. Nastavení výchozí zásady ověřování tak, aby vyžadovala ověření uživatelů chrání nově přidané Razor Pages a kontrolery. Pokud je ve výchozím nastavení vyžadováno ověřování, je bezpečnější než spoléhání na nové řadiče a Razor Pages pro zahrnutí atributu `[Authorize]`.

Přidejte [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) na stránky rejstřík, o a kontakt, aby anonymní uživatelé mohli získat informace o lokalitě před jejich registrací.

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a>Konfigurace testovací účet

Třída `SeedData` vytvoří dva účty: správce a manažer. Pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets) nastavte heslo pro tyto účty. Nastavte heslo z adresáře projektu (adresář obsahující *program.cs*):

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

Pokud není zadáno silné heslo, je vyvolána výjimka při volání `SeedData.Initialize`.

Aktualizujte `Main` pro použití testovacího hesla:

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>Vytvoření testovacích účtů a aktualizovat kontakty

Aktualizujte metodu `Initialize` ve třídě `SeedData` k vytvoření testovacích účtů:

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

Přidejte do kontaktů ID uživatele a `ContactStatus` správce. Proveďte jednu z kontakty "Submitted" a jedné "byl odmítnut". Přidáte ID uživatele a stav pro všechny kontakty. Je zobrazena pouze jeden kontakt:

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>Vytvořit vlastníka, správce a Správce autorizace obslužné rutiny

Vytvořte složku *autorizace* a v ní vytvořte třídu `ContactIsOwnerAuthorizationHandler`. `ContactIsOwnerAuthorizationHandler` ověří, že uživatel pracuje na prostředku, který je vlastníkem prostředku.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

`ContactIsOwnerAuthorizationHandler` volá [kontext. Úspěšné](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , pokud je aktuální ověřený uživatel vlastníkem kontaktu. Obslužné rutiny autorizace obecně:

* Vrátí `context.Succeed`, pokud jsou splněny požadavky.
* Vrátí `Task.CompletedTask`, pokud nejsou splněny požadavky. `Task.CompletedTask` není úspěch nebo neúspěch&mdash;umožňuje spuštění dalších obslužných rutin autorizace.

Pokud potřebujete explicitně selhat, vraťte [kontext. Selhání](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).

Aplikaci umožňuje kontaktní vlastníkům upravit, odstranit a vytvořit svoje vlastní data. `ContactIsOwnerAuthorizationHandler` nemusí kontrolovat operaci předanou parametrem požadavku.

### <a name="create-a-manager-authorization-handler"></a>Vytvořte obslužnou rutinu Správce autorizací

Ve složce pro *autorizaci* vytvořte třídu `ContactManagerAuthorizationHandler`. `ContactManagerAuthorizationHandler` ověří uživatele, který pracuje na prostředku, jako správce. Pouze vedoucí mohli schválit nebo odmítnout změny obsahu (nové nebo změněné).

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>Vytvořte obslužnou rutinu Správce autorizací

Ve složce pro *autorizaci* vytvořte třídu `ContactAdministratorsAuthorizationHandler`. `ContactAdministratorsAuthorizationHandler` ověří uživatele, který pracuje na prostředku, jako správce. Správce může provádět všechny operace.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>Zaregistrujte obslužné rutiny autorizace

Služby používající Entity Framework Core musí být registrovány pro [vkládání závislostí](xref:fundamentals/dependency-injection) pomocí [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions). `ContactIsOwnerAuthorizationHandler` používá ASP.NET Core [identity](xref:security/authentication/identity), která je postavená na Entity Framework Core. Zaregistrujte obslužné rutiny s kolekcí služeb, aby byly k dispozici pro `ContactsController` prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection). Do konce `ConfigureServices`přidejte následující kód:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

`ContactAdministratorsAuthorizationHandler` a `ContactManagerAuthorizationHandler` jsou přidány jako singleton. Jsou to jednoznačné, protože nepoužívají EF a všechny potřebné informace jsou v parametru `Context` `HandleRequirementAsync` metody.

## <a name="support-authorization"></a>Povolení podpory

V této části se aktualizace stránky Razor a přidejte třídu požadavků operace.

### <a name="review-the-contact-operations-requirements-class"></a>Zkontrolujte požadavky na třídy kontaktní operace

Zkontrolujte třídu `ContactOperations`. Tato třída obsahuje požadavky na aplikace podporuje:

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a>Vytvoření základní třídu pro stránky Razor kontakty

Vytvořte základní třídu, která obsahuje služby využité v kontaktech stránky Razor. Základní třída vloží kód inicializace na jednom místě:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

Předchozí kód:

* Přidá službu `IAuthorizationService` pro přístup k obslužným rutinám autorizace.
* Přidá službu identity `UserManager`.
* Přidejte `ApplicationDbContext`.

### <a name="update-the-createmodel"></a>Aktualizace CreateModel

Aktualizujte konstruktor Create Page model tak, aby používal základní třídu `DI_BasePageModel`:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

Aktualizujte metodu `CreateModel.OnPostAsync` na:

* Přidejte ID uživatele do modelu `Contact`.
* Ověřte, zda že má uživatel oprávnění k vytvoření kontakty obslužná rutina ověřování volejte.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>Aktualizace IndexModel

Aktualizujte metodu `OnGetAsync`, aby se pro obecné uživatele zobrazovaly jenom schválené kontakty:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>Aktualizace EditModel

Přidejte obslužnou rutinu ověřování k ověření, že uživatel je vlastníkem kontaktu. Vzhledem k tomu, že se ověřuje autorizace prostředků, atribut `[Authorize]` není dostatečný. Aplikace nemá přístup k prostředku, když jsou vyhodnocovány atributy. Autorizace na základě prostředků musí být nezbytné. Kontroly musí provést, když má aplikace přístup k prostředku načtením v modelu stránky nebo načtením v rámci samotná obslužná rutina. Často přistupovat k prostředku předáním klíč prostředku.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>Aktualizace DeleteModel

Aktualizace modelu stránku odstranit sloužící k ověření, že uživatel má oprávnění odstranit u kontaktu obslužnou rutinu ověřování.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>Vložit do zobrazení autorizační službu

V současné době ukazuje uživatelského rozhraní upravovat a odstraňovat kontakty, které uživatel nemůže upravovat odkazy.

Vložení autorizační služby do souboru *views/_ViewImports. cshtml* , aby byla dostupná pro všechna zobrazení:

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

Předchozí kód přidá několik příkazů `using`.

Aktualizujte odkazy pro **Úpravy** a **odstranění** na *stránkách/kontakty/index. cshtml* , aby byly vygenerovány pouze pro uživatele s příslušnými oprávněními:

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> Skrytí propojení od uživatele, kteří nemají oprávnění ke změně dat nebude zabezpečení aplikace. Skrytí propojení nastaví aplikaci jako přívětivější tím, že zobrazuje pouze platné odkazy. Uživatelé můžou hack generované adres URL pro vyvolání funkce upravit a odstranit operací s daty, které není vlastní. Stránka Razor nebo kontroleru musí vynucovat kontroly přístupu pro data zabezpečení.

### <a name="update-details"></a>Podrobné informace o aktualizaci

Aktualizujte zobrazení podrobností, aby vedoucí mohli schválit nebo odmítnout kontaktů:

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

Model stránky podrobnosti aktualizace:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>Přidat nebo odebrat uživatele k roli

Informace o [tomto problému najdete zde](https://github.com/dotnet/AspNetCore.Docs/issues/8502) :

* Odebrání oprávnění uživatele. Například ztlumení uživatele v aplikaci chatu.
* Přidání oprávnění pro uživatele.

## <a name="test-the-completed-app"></a>Testování dokončené aplikace

Pokud jste ještě nenastavili heslo pro osazené uživatelské účty, nastavte heslo pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets#secret-manager) :

* Zvolte silné heslo: použití osm nebo více znaků, aspoň jedno velké písmeno, číslo a symbol. `Passw0rd!` například splňuje požadavky na silné heslo.
* Spusťte následující příkaz ze složky projektu, kde `<PW>` je heslo:

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* Zrušení a aktualizace databáze

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* Restartujte aplikaci k přidání dat do databáze.

Snadný způsob, jak otestovat dokončená aplikace je spustíte tři různé prohlížeče (nebo incognito/InPrivate relace). V jednom prohlížeči Zaregistrujte nového uživatele (například `test@example.com`). Přihlaste se k každým prohlížečem s jiným uživatelem. Ověřte následující operace:

* Registrovaných uživatelů můžete zobrazit všechny schválené kontaktní údaje.
* Registrovaných uživatelů můžete upravit nebo odstranit svá vlastní data.
* Správci mohou schvalovat a odmítat kontaktní údaje. Zobrazení `Details` zobrazuje tlačítka **schválení** a **odmítnutí** .
* Správci můžou schvalovat a odmítat a upravit nebo odstranit všechna data.

| Uživatel                | Nasazených aplikací | Možnosti                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | Ne                | Upravit nebo odstranit vlastní data.                |
| manager@contoso.com | Ano               | Vlastní data, schvalovat a odmítat a upravit nebo odstranit. |
| admin@contoso.com   | Ano               | Schválit nebo odmítnout a upravit nebo odstranit všechna data. |

Vytvoření kontaktu v prohlížeči na správce. Zkopírujte adresu URL pro odstranění a upravit z kontaktujte správce. Vložte tyto odkazy do testů webového prohlížeče k ověření, že testovací uživatel nemůže provádět tyto operace.

## <a name="create-the-starter-app"></a>Vytvořit úvodní aplikaci

* Vytvoření aplikace stránky Razor s názvem "ContactManager"
  * Vytvořte aplikaci pomocí **individuálních uživatelských účtů**.
  * Pojmenujte ji "ContactManager" tak obor názvů odpovídá oboru názvů použitého v ukázce.
  * `-uld` určuje LocalDB místo SQLite.

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* Přidat *modely/kontakt. cs*:

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* Generování `Contact` modelu.
* Vytvořte počáteční migraci a aktualizaci databáze:

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

* Testování aplikace pomocí vytváření, úpravy a odstranění kontaktu

### <a name="seed-the-database"></a>Přidání dat do databáze

Do složky *data* přidejte třídu [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) .

Volat `SeedData.Initialize` z `Main`:

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

Otestujte, že aplikace naplnila databázi. Pokud existují nějaké řádky v kontaktu DB, metoda počáteční hodnoty není spuštěna.

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a>Další zdroje

* [Vytvoření webové aplikace .NET Core a SQL Database v Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [ASP.NET Core autorizačního prostředí](https://github.com/blowdart/AspNetAuthorizationWorkshop). Toto testovací prostředí obsahuje větší podrobnosti o funkcích zabezpečení v tomto kurzu.
* <xref:security/authorization/introduction>
* [Vlastní autorizace na základě zásad](xref:security/authorization/policies)

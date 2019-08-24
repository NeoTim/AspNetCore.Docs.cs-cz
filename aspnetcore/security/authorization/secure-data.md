---
title: Vytvoření aplikace ASP.NET Core s uživatelskými daty chráněnými autorizací
author: rick-anderson
description: Zjistěte, jak vytvořit aplikace Razor Pages s uživatelskými daty chráněnými autorizací. Zahrnuje HTTPS, ověřování, zabezpečení ASP.NET Core Identity.
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
uid: security/authorization/secure-data
ms.openlocfilehash: 225d0e3aa51745253d03e614b1c8568b3a6ba731
ms.sourcegitcommit: 983b31449fe398e6e922eb13e9eb6f4287ec91e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2019
ms.locfileid: "70017486"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a>Vytvoření aplikace ASP.NET Core s uživatelskými daty chráněnými autorizací

Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Joe Audette](https://twitter.com/joeaudette)

::: moniker range="<= aspnetcore-1.1"

Zobrazit [tento PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) pro verzi technologie ASP.NET Core MVC. ASP.NET Core 1.1 verzi tohoto kurzu je v [to](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) složky. 1\.1 ukázka ASP.NET Core je v [ukázky](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Zobrazit [tento pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Tento kurz ukazuje, jak vytvořit webovou aplikaci ASP.NET Core s uživatelskými daty chráněnými autorizací. Zobrazí seznam kontakty, které ověřeným uživatelům (registrovaných) jste vytvořili. Existují tři skupiny zabezpečení:

* **Zaregistrované uživatele** můžete zobrazit všechny schválené data a můžete upravit nebo odstranit svá vlastní data.
* **Správci** můžete schválit nebo odmítnout kontaktní údaje. Uživatelé vidí pouze schválené kontakty.
* **Správci** můžete schvalovat a odmítat a upravit nebo odstranit všechna data.

Obrázky v tomto dokumentu se přesně neshodují s nejnovějšími šablonami.

Na následujícím obrázku, uživatel Rick (`rick@example.com`) je přihlášený. Rick může zobrazit jenom schválené kontakty a **upravit**/**odstranit**/**vytvořit nový** odkazy pro jeho kontakty. Pouze poslední záznam vytvořil Rick, zobrazí **upravit** a **odstranit** odkazy. Ostatní uživatelé neuvidí poslední záznam, dokud správce nebo správce změní stav na "Schváleno".

![Snímek obrazovky zobrazující Rick přihlášení](secure-data/_static/rick.png)

Na následujícím obrázku `manager@contoso.com` je přihlášen a v roli manažera:

![Snímek obrazovky zobrazující manager@contoso.com přihlášení](secure-data/_static/manager1.png)

Následující obrázek ukazuje vedoucí zobrazení podrobností o kontaktu:

![Zobrazení manažera kontaktu](secure-data/_static/manager.png)

**Schválit** a **odmítnout** tlačítek se zobrazí pouze správci a správci.

Na následujícím obrázku `admin@contoso.com` je přihlášen a v roli správce:

![Snímek obrazovky zobrazující admin@contoso.com přihlášení](secure-data/_static/admin.png)

Správce má všechna oprávnění. Může číst/upravovat/odstraňovat všechny kontakty a změnit stav kontakty.

Aplikace byla vytvořena pomocí [generování uživatelského rozhraní](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) následující `Contact` modelu:

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

Ukázka obsahuje následující rutiny autorizace:

* `ContactIsOwnerAuthorizationHandler`: Zajišťuje, aby uživatel mohl upravovat pouze svá data.
* `ContactManagerAuthorizationHandler`: Umožňuje správcům schvalovat nebo odmítat kontakty.
* `ContactAdministratorsAuthorizationHandler`: Umožňuje správcům schvalovat nebo odmítat kontakty a upravovat nebo odstraňovat kontakty.

## <a name="prerequisites"></a>Požadavky

V tomto kurzu je advanced. Měli byste se seznámit s:

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [Ověřování](xref:security/authentication/identity)
* [Potvrzení účtu a obnovení hesla](xref:security/authentication/accconfirm)
* [Autorizace](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>Starter a dokončené aplikace

[Stáhněte si](xref:index#how-to-download-a-sample) [Dokončit](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) aplikace. [Test](#test-the-completed-app) dokončené aplikace tak, že jste se seznámili s jeho funkcí zabezpečení.

### <a name="the-starter-app"></a>Úvodní aplikaci

[Stáhněte si](xref:index#how-to-download-a-sample) [starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) aplikace.

Spusťte aplikaci, klepněte **ContactManager** propojit a ověření můžete vytvářet, upravovat a odstraňovat kontakt.

## <a name="secure-user-data"></a>Zabezpečení dat uživatele

Následující oddíly mají všechny hlavní kroky k vytvoření aplikace pro data zabezpečení uživatele. Vám může být užitečné k odkazování na dokončení projektu.

### <a name="tie-the-contact-data-to-the-user"></a>Tie kontaktní údaje pro uživatele

Použití technologie ASP.NET [Identity](xref:security/authentication/identity) ID uživatele, aby tak uživatelé můžete upravit jejich data, ale ne data jiných uživatelů. Přidat `OwnerID` a `ContactStatus` k `Contact` modelu:

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID` je ID uživatele z `AspNetUser` v tabulku [Identity](xref:security/authentication/identity) databáze. `Status` Pole určuje, zda je kontakt zobrazitelné obecný uživatel.

Vytvořte novou migraci a aktualizaci databáze:

```console
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a>Přidání služby Role na identitu

Připojit [kliknutím na Přidat role](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) přidat služby rolí:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a>Vyžadovat ověření uživatelé

Nastavte výchozí zásady ověřování tak, aby vyžadovala ověření uživatelů:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 Můžete zrušit ověřování na úrovni metody stránky Razor, kontroler nebo akce s `[AllowAnonymous]` atribut. Nastavení výchozí zásady ověřování tak, aby vyžadovala ověření uživatelů chrání nově přidané Razor Pages a kontrolery. Ve výchozím nastavení je vyžadováno ověření je bezpečnější než spoléhání se na nové řadiče a Razor Pages zahrnout s `[Authorize]` atribut.

Přidejte [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) na stránky rejstřík a soukromí, aby anonymní uživatelé mohli získat informace o lokalitě před jejich registrací.

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a>Konfigurace testovací účet

`SeedData` Třída vytvoří dva účty: správce a správce. Použití [nástroj tajný klíč správce](xref:security/app-secrets) nastavení hesla pro tyto účty. Nastavte heslo z adresáře projektu (adresáře, který obsahuje *Program.cs*):

```console
dotnet user-secrets set SeedUserPW <PW>
```

Pokud není zadán silné heslo, je vyvolána výjimka, když `SeedData.Initialize` je volána.

Aktualizace `Main` test heslo budete používat:

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>Vytvoření testovacích účtů a aktualizovat kontakty

Aktualizace `Initialize` metodu `SeedData` třídy za účelem vytvoření testovacích účtů:

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

Přidat správce ID uživatele a `ContactStatus` kontaktům. Proveďte jednu z kontakty "Submitted" a jedné "byl odmítnut". Přidáte ID uživatele a stav pro všechny kontakty. Je zobrazena pouze jeden kontakt:

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>Vytvořit vlastníka, správce a Správce autorizace obslužné rutiny

Vytvoření `ContactIsOwnerAuthorizationHandler` třídy v *autorizace* složky. `ContactIsOwnerAuthorizationHandler` Ověřuje, že uživatel na prostředek vlastníkem prostředku.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

`ContactIsOwnerAuthorizationHandler` Volání [kontextu. Úspěšné](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) aktuálně ověřeného uživatele při jeho vlastníkem. Obslužné rutiny autorizace obecně:

* Vrátí `context.Succeed` Pokud jsou splněny.
* Vrátí `Task.CompletedTask` Pokud požadavky nejsou splněny. `Task.CompletedTask`není úspěch nebo neúspěch&mdash;, umožňuje spuštění dalších obslužných rutin autorizace.

Pokud je potřeba explicitně nezdaří, vrátí [kontextu. Selhání](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).

Aplikaci umožňuje kontaktní vlastníkům upravit, odstranit a vytvořit svoje vlastní data. `ContactIsOwnerAuthorizationHandler` není nutné ke kontrole operace předané v parametru požadavku.

### <a name="create-a-manager-authorization-handler"></a>Vytvořte obslužnou rutinu Správce autorizací

Vytvoření `ContactManagerAuthorizationHandler` třídy v *autorizace* složky. `ContactManagerAuthorizationHandler` Ověřuje uživatele na prostředek je správce. Pouze vedoucí mohli schválit nebo odmítnout změny obsahu (nové nebo změněné).

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>Vytvořte obslužnou rutinu Správce autorizací

Vytvoření `ContactAdministratorsAuthorizationHandler` třídy v *autorizace* složky. `ContactAdministratorsAuthorizationHandler` Ověřuje uživatele na prostředek je správce. Správce může provádět všechny operace.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>Zaregistrujte obslužné rutiny autorizace

Pomocí Entity Framework Core Services musí být zaregistrovaný pro [injektáž závislostí](xref:fundamentals/dependency-injection) pomocí [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions). `ContactIsOwnerAuthorizationHandler` Používá ASP.NET Core [Identity](xref:security/authentication/identity), která je založená na Entity Framework Core. Obslužné rutiny zaregistrovat u kolekce služby jsou k dispozici na `ContactsController` prostřednictvím [injektáž závislostí](xref:fundamentals/dependency-injection). Přidejte následující kód do konce `ConfigureServices`:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

`ContactAdministratorsAuthorizationHandler` a `ContactManagerAuthorizationHandler` jsou přidány jako jednotlivé prvky. Jednotlivé prvky jsou vzhledem k tomu, že se nepoužívají EF a všechny informace potřebné musí být `Context` parametr `HandleRequirementAsync` metoda.

## <a name="support-authorization"></a>Povolení podpory

V této části se aktualizace stránky Razor a přidejte třídu požadavků operace.

### <a name="review-the-contact-operations-requirements-class"></a>Zkontrolujte požadavky na třídy kontaktní operace

Zkontrolujte `ContactOperations` třídy. Tato třída obsahuje požadavky na aplikace podporuje:

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a>Vytvoření základní třídu pro stránky Razor kontakty

Vytvořte základní třídu, která obsahuje služby využité v kontaktech stránky Razor. Základní třída vloží kód inicializace na jednom místě:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

Předchozí kód:

* Přidá `IAuthorizationService` služby pro přístup k rutinám autorizace.
* Přidá identitu `UserManager` služby.
* Přidat `ApplicationDbContext`.

### <a name="update-the-createmodel"></a>Aktualizace CreateModel

Aktualizovat konstruktoru vytvořit stránku model použití `DI_BasePageModel` základní třídy:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

Aktualizace `CreateModel.OnPostAsync` metodu:

* Přidat ID uživatele, `Contact` modelu.
* Ověřte, zda že má uživatel oprávnění k vytvoření kontakty obslužná rutina ověřování volejte.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>Aktualizace IndexModel

Aktualizace `OnGetAsync` metody, jsou uvedeny pouze schválené kontakty obecné uživatelům:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>Aktualizace EditModel

Přidejte obslužnou rutinu ověřování k ověření, že uživatel je vlastníkem kontaktu. Protože ra se ověřuje, `[Authorize]` atribut není dostatek. Aplikace nemá přístup k prostředku, když jsou vyhodnocovány atributy. Autorizace na základě prostředků musí být nezbytné. Kontroly musí provést, když má aplikace přístup k prostředku načtením v modelu stránky nebo načtením v rámci samotná obslužná rutina. Často přistupovat k prostředku předáním klíč prostředku.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>Aktualizace DeleteModel

Aktualizace modelu stránku odstranit sloužící k ověření, že uživatel má oprávnění odstranit u kontaktu obslužnou rutinu ověřování.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>Vložit do zobrazení autorizační službu

V současné době ukazuje uživatelského rozhraní upravovat a odstraňovat kontakty, které uživatel nemůže upravovat odkazy.

Vložení autorizační služby do souboru *Pages/_ViewImports. cshtml* , aby bylo dostupné pro všechna zobrazení:

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

Předchozí kód přidá několik `using` příkazy.

Aktualizace **upravit** a **odstranit** odkazů v *Pages/Contacts/Index.cshtml* tak, že pouze vykreslen pro uživatele s příslušnými oprávněními:

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> Skrytí propojení od uživatele, kteří nemají oprávnění ke změně dat nebude zabezpečení aplikace. Skrytí propojení nastaví aplikaci jako přívětivější tím, že zobrazuje pouze platné odkazy. Uživatelé můžou hack generované adres URL pro vyvolání funkce upravit a odstranit operací s daty, které není vlastní. Stránka Razor nebo kontroleru musí vynucovat kontroly přístupu pro data zabezpečení.

### <a name="update-details"></a>Podrobné informace o aktualizaci

Aktualizujte zobrazení podrobností, aby vedoucí mohli schválit nebo odmítnout kontaktů:

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

Model stránky podrobnosti aktualizace:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>Přidat nebo odebrat uživatele k roli

Zobrazit [tento problém](https://github.com/aspnet/AspNetCore.Docs/issues/8502) informace na:

* Odebrání oprávnění uživatele. Například ztlumení uživatele v aplikaci chatu.
* Přidání oprávnění pro uživatele.

## <a name="test-the-completed-app"></a>Testování dokončené aplikace

Pokud jste dosud nenastavili hesla pro dosazené uživatelské účty, použijte [nástroj tajný klíč správce](xref:security/app-secrets#secret-manager) nastavit heslo:

* Vyberte silné heslo: Použijte osm nebo více znaků a alespoň jedno velké písmeno, číslici a symbol. Například `Passw0rd!` splňuje požadavky na silné heslo.
* Spusťte následující příkaz ze složky projektu, kde `<PW>` je heslo:

  ```console
  dotnet user-secrets set SeedUserPW <PW>
  ```

Pokud má kontaktů:

* Odstranění všech záznamů v `Contact` tabulky.
* Restartujte aplikaci k přidání dat do databáze.

Snadný způsob, jak otestovat dokončená aplikace je spustíte tři různé prohlížeče (nebo incognito/InPrivate relace). V jeden prohlížeč, registraci nového uživatele (například `test@example.com`). Přihlaste se k každým prohlížečem s jiným uživatelem. Ověřte následující operace:

* Registrovaných uživatelů můžete zobrazit všechny schválené kontaktní údaje.
* Registrovaných uživatelů můžete upravit nebo odstranit svá vlastní data.
* Správci mohou schvalovat a odmítat kontaktní údaje. `Details` Zobrazení ukazuje **schválit** a **odmítnout** tlačítka.
* Správci můžou schvalovat a odmítat a upravit nebo odstranit všechna data.

| Uživatel                | Nasazených aplikací | Možnosti                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | Ne                | Upravit nebo odstranit vlastní data.                |
| manager@contoso.com | Ano               | Vlastní data, schvalovat a odmítat a upravit nebo odstranit. |
| admin@contoso.com   | Ano               | Schválit nebo odmítnout a upravit nebo odstranit všechna data. |

Vytvoření kontaktu v prohlížeči na správce. Zkopírujte adresu URL pro odstranění a upravit z kontaktujte správce. Vložte tyto odkazy do testů webového prohlížeče k ověření, že testovací uživatel nemůže provádět tyto operace.

## <a name="create-the-starter-app"></a>Vytvořit úvodní aplikaci

* Vytvoření aplikace stránky Razor s názvem "ContactManager"
  * Vytvoření aplikace s **jednotlivých uživatelských účtů**.
  * Pojmenujte ji "ContactManager" tak obor názvů odpovídá oboru názvů použitého v ukázce.
  * `-uld` Určuje LocalDB místo SQLite

  ```console
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* Přidat *modely/kontakt. cs*:

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* Vygenerované uživatelské rozhraní `Contact` modelu.
* Vytvořte počáteční migraci a aktualizaci databáze:

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
  ```

Pokud se zobrazí chyba s `dotnet aspnet-codegenerator razorpage` příkazem, přečtěte si [Tento problém GitHub](https://github.com/aspnet/Scaffolding/issues/984).

* Aktualizujte kotvu **ContactManager** v souboru *Pages/Shared/_Layout. cshtml* :

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* Testování aplikace pomocí vytváření, úpravy a odstranění kontaktu

### <a name="seed-the-database"></a>Přidání dat do databáze

Do složky *data* přidejte třídu [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) :

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

Volání `SeedData.Initialize` z `Main`:

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

Otestujte, že aplikace naplnila databázi. Pokud existují nějaké řádky v kontaktu DB, metoda počáteční hodnoty není spuštěna.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

Tento kurz ukazuje, jak vytvořit webovou aplikaci ASP.NET Core s uživatelskými daty chráněnými autorizací. Zobrazí seznam kontakty, které ověřeným uživatelům (registrovaných) jste vytvořili. Existují tři skupiny zabezpečení:

* **Zaregistrované uživatele** můžete zobrazit všechny schválené data a můžete upravit nebo odstranit svá vlastní data.
* **Správci** můžete schválit nebo odmítnout kontaktní údaje. Uživatelé vidí pouze schválené kontakty.
* **Správci** můžete schvalovat a odmítat a upravit nebo odstranit všechna data.

Na následujícím obrázku, uživatel Rick (`rick@example.com`) je přihlášený. Rick může zobrazit jenom schválené kontakty a **upravit**/**odstranit**/**vytvořit nový** odkazy pro jeho kontakty. Pouze poslední záznam vytvořil Rick, zobrazí **upravit** a **odstranit** odkazy. Ostatní uživatelé neuvidí poslední záznam, dokud správce nebo správce změní stav na "Schváleno".

![Snímek obrazovky zobrazující Rick přihlášení](secure-data/_static/rick.png)

Na následujícím obrázku `manager@contoso.com` je přihlášen a v roli manažera:

![Snímek obrazovky zobrazující manager@contoso.com přihlášení](secure-data/_static/manager1.png)

Následující obrázek ukazuje vedoucí zobrazení podrobností o kontaktu:

![Zobrazení manažera kontaktu](secure-data/_static/manager.png)

**Schválit** a **odmítnout** tlačítek se zobrazí pouze správci a správci.

Na následujícím obrázku `admin@contoso.com` je přihlášen a v roli správce:

![Snímek obrazovky zobrazující admin@contoso.com přihlášení](secure-data/_static/admin.png)

Správce má všechna oprávnění. Může číst/upravovat/odstraňovat všechny kontakty a změnit stav kontakty.

Aplikace byla vytvořena pomocí [generování uživatelského rozhraní](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) následující `Contact` modelu:

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

Ukázka obsahuje následující rutiny autorizace:

* `ContactIsOwnerAuthorizationHandler`: Zajišťuje, aby uživatel mohl upravovat pouze svá data.
* `ContactManagerAuthorizationHandler`: Umožňuje správcům schvalovat nebo odmítat kontakty.
* `ContactAdministratorsAuthorizationHandler`: Umožňuje správcům schvalovat nebo odmítat kontakty a upravovat nebo odstraňovat kontakty.

## <a name="prerequisites"></a>Požadavky

V tomto kurzu je advanced. Měli byste se seznámit s:

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [Ověřování](xref:security/authentication/identity)
* [Potvrzení účtu a obnovení hesla](xref:security/authentication/accconfirm)
* [Autorizace](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>Starter a dokončené aplikace

[Stáhněte si](xref:index#how-to-download-a-sample) [Dokončit](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) aplikace. [Test](#test-the-completed-app) dokončené aplikace tak, že jste se seznámili s jeho funkcí zabezpečení.

### <a name="the-starter-app"></a>Úvodní aplikaci

[Stáhněte si](xref:index#how-to-download-a-sample) [starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) aplikace.

Spusťte aplikaci, klepněte **ContactManager** propojit a ověření můžete vytvářet, upravovat a odstraňovat kontakt.

## <a name="secure-user-data"></a>Zabezpečení dat uživatele

Následující oddíly mají všechny hlavní kroky k vytvoření aplikace pro data zabezpečení uživatele. Vám může být užitečné k odkazování na dokončení projektu.

### <a name="tie-the-contact-data-to-the-user"></a>Tie kontaktní údaje pro uživatele

Použití technologie ASP.NET [Identity](xref:security/authentication/identity) ID uživatele, aby tak uživatelé můžete upravit jejich data, ale ne data jiných uživatelů. Přidat `OwnerID` a `ContactStatus` k `Contact` modelu:

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID` je ID uživatele z `AspNetUser` v tabulku [Identity](xref:security/authentication/identity) databáze. `Status` Pole určuje, zda je kontakt zobrazitelné obecný uživatel.

Vytvořte novou migraci a aktualizaci databáze:

```console
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a>Přidání služby Role na identitu

Připojit [kliknutím na Přidat role](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) přidat služby rolí:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a>Vyžadovat ověření uživatelé

Nastavte výchozí zásady ověřování tak, aby vyžadovala ověření uživatelů:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 Můžete zrušit ověřování na úrovni metody stránky Razor, kontroler nebo akce s `[AllowAnonymous]` atribut. Nastavení výchozí zásady ověřování tak, aby vyžadovala ověření uživatelů chrání nově přidané Razor Pages a kontrolery. Ve výchozím nastavení je vyžadováno ověření je bezpečnější než spoléhání se na nové řadiče a Razor Pages zahrnout s `[Authorize]` atribut.

Přidat [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) index stránky o a kontaktní anonymní uživatelé získali informace o webu předtím, než aby se zaregistrovali.

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a>Konfigurace testovací účet

`SeedData` Třída vytvoří dva účty: správce a správce. Použití [nástroj tajný klíč správce](xref:security/app-secrets) nastavení hesla pro tyto účty. Nastavte heslo z adresáře projektu (adresáře, který obsahuje *Program.cs*):

```console
dotnet user-secrets set SeedUserPW <PW>
```

Pokud není zadán silné heslo, je vyvolána výjimka, když `SeedData.Initialize` je volána.

Aktualizace `Main` test heslo budete používat:

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>Vytvoření testovacích účtů a aktualizovat kontakty

Aktualizace `Initialize` metodu `SeedData` třídy za účelem vytvoření testovacích účtů:

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

Přidat správce ID uživatele a `ContactStatus` kontaktům. Proveďte jednu z kontakty "Submitted" a jedné "byl odmítnut". Přidáte ID uživatele a stav pro všechny kontakty. Je zobrazena pouze jeden kontakt:

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>Vytvořit vlastníka, správce a Správce autorizace obslužné rutiny

Vytvoření `ContactIsOwnerAuthorizationHandler` třídy v *autorizace* složky. `ContactIsOwnerAuthorizationHandler` Ověřuje, že uživatel na prostředek vlastníkem prostředku.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

`ContactIsOwnerAuthorizationHandler` Volání [kontextu. Úspěšné](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) aktuálně ověřeného uživatele při jeho vlastníkem. Obslužné rutiny autorizace obecně:

* Vrátí `context.Succeed` Pokud jsou splněny.
* Vrátí `Task.CompletedTask` Pokud požadavky nejsou splněny. `Task.CompletedTask`není úspěch nebo neúspěch&mdash;, umožňuje spuštění dalších obslužných rutin autorizace.

Pokud je potřeba explicitně nezdaří, vrátí [kontextu. Selhání](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).

Aplikaci umožňuje kontaktní vlastníkům upravit, odstranit a vytvořit svoje vlastní data. `ContactIsOwnerAuthorizationHandler` není nutné ke kontrole operace předané v parametru požadavku.

### <a name="create-a-manager-authorization-handler"></a>Vytvořte obslužnou rutinu Správce autorizací

Vytvoření `ContactManagerAuthorizationHandler` třídy v *autorizace* složky. `ContactManagerAuthorizationHandler` Ověřuje uživatele na prostředek je správce. Pouze vedoucí mohli schválit nebo odmítnout změny obsahu (nové nebo změněné).

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>Vytvořte obslužnou rutinu Správce autorizací

Vytvoření `ContactAdministratorsAuthorizationHandler` třídy v *autorizace* složky. `ContactAdministratorsAuthorizationHandler` Ověřuje uživatele na prostředek je správce. Správce může provádět všechny operace.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>Zaregistrujte obslužné rutiny autorizace

Pomocí Entity Framework Core Services musí být zaregistrovaný pro [injektáž závislostí](xref:fundamentals/dependency-injection) pomocí [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions). `ContactIsOwnerAuthorizationHandler` Používá ASP.NET Core [Identity](xref:security/authentication/identity), která je založená na Entity Framework Core. Obslužné rutiny zaregistrovat u kolekce služby jsou k dispozici na `ContactsController` prostřednictvím [injektáž závislostí](xref:fundamentals/dependency-injection). Přidejte následující kód do konce `ConfigureServices`:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

`ContactAdministratorsAuthorizationHandler` a `ContactManagerAuthorizationHandler` jsou přidány jako jednotlivé prvky. Jednotlivé prvky jsou vzhledem k tomu, že se nepoužívají EF a všechny informace potřebné musí být `Context` parametr `HandleRequirementAsync` metoda.

## <a name="support-authorization"></a>Povolení podpory

V této části se aktualizace stránky Razor a přidejte třídu požadavků operace.

### <a name="review-the-contact-operations-requirements-class"></a>Zkontrolujte požadavky na třídy kontaktní operace

Zkontrolujte `ContactOperations` třídy. Tato třída obsahuje požadavky na aplikace podporuje:

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a>Vytvoření základní třídu pro stránky Razor kontakty

Vytvořte základní třídu, která obsahuje služby využité v kontaktech stránky Razor. Základní třída vloží kód inicializace na jednom místě:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

Předchozí kód:

* Přidá `IAuthorizationService` služby pro přístup k rutinám autorizace.
* Přidá identitu `UserManager` služby.
* Přidat `ApplicationDbContext`.

### <a name="update-the-createmodel"></a>Aktualizace CreateModel

Aktualizovat konstruktoru vytvořit stránku model použití `DI_BasePageModel` základní třídy:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

Aktualizace `CreateModel.OnPostAsync` metodu:

* Přidat ID uživatele, `Contact` modelu.
* Ověřte, zda že má uživatel oprávnění k vytvoření kontakty obslužná rutina ověřování volejte.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>Aktualizace IndexModel

Aktualizace `OnGetAsync` metody, jsou uvedeny pouze schválené kontakty obecné uživatelům:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>Aktualizace EditModel

Přidejte obslužnou rutinu ověřování k ověření, že uživatel je vlastníkem kontaktu. Protože ra se ověřuje, `[Authorize]` atribut není dostatek. Aplikace nemá přístup k prostředku, když jsou vyhodnocovány atributy. Autorizace na základě prostředků musí být nezbytné. Kontroly musí provést, když má aplikace přístup k prostředku načtením v modelu stránky nebo načtením v rámci samotná obslužná rutina. Často přistupovat k prostředku předáním klíč prostředku.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>Aktualizace DeleteModel

Aktualizace modelu stránku odstranit sloužící k ověření, že uživatel má oprávnění odstranit u kontaktu obslužnou rutinu ověřování.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>Vložit do zobrazení autorizační službu

V současné době ukazuje uživatelského rozhraní upravovat a odstraňovat kontakty, které uživatel nemůže upravovat odkazy.

Vloží autorizační službu v *Views/_ViewImports.cshtml* souboru tak, aby byl k dispozici pro všechna zobrazení:

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

Předchozí kód přidá několik `using` příkazy.

Aktualizace **upravit** a **odstranit** odkazů v *Pages/Contacts/Index.cshtml* tak, že pouze vykreslen pro uživatele s příslušnými oprávněními:

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> Skrytí propojení od uživatele, kteří nemají oprávnění ke změně dat nebude zabezpečení aplikace. Skrytí propojení nastaví aplikaci jako přívětivější tím, že zobrazuje pouze platné odkazy. Uživatelé můžou hack generované adres URL pro vyvolání funkce upravit a odstranit operací s daty, které není vlastní. Stránka Razor nebo kontroleru musí vynucovat kontroly přístupu pro data zabezpečení.

### <a name="update-details"></a>Podrobné informace o aktualizaci

Aktualizujte zobrazení podrobností, aby vedoucí mohli schválit nebo odmítnout kontaktů:

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

Model stránky podrobnosti aktualizace:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>Přidat nebo odebrat uživatele k roli

Zobrazit [tento problém](https://github.com/aspnet/AspNetCore.Docs/issues/8502) informace na:

* Odebrání oprávnění uživatele. Například ztlumení uživatele v aplikaci chatu.
* Přidání oprávnění pro uživatele.

## <a name="test-the-completed-app"></a>Testování dokončené aplikace

Pokud jste dosud nenastavili hesla pro dosazené uživatelské účty, použijte [nástroj tajný klíč správce](xref:security/app-secrets#secret-manager) nastavit heslo:

* Vyberte silné heslo: Použijte osm nebo více znaků a alespoň jedno velké písmeno, číslici a symbol. Například `Passw0rd!` splňuje požadavky na silné heslo.
* Spusťte následující příkaz ze složky projektu, kde `<PW>` je heslo:

  ```console
  dotnet user-secrets set SeedUserPW <PW>
  ```

* Zrušení a aktualizace databáze

    ```console
     dotnet ef database drop -f
     dotnet ef database update  
     ```

* Restartujte aplikaci k přidání dat do databáze.

Snadný způsob, jak otestovat dokončená aplikace je spustíte tři různé prohlížeče (nebo incognito/InPrivate relace). V jeden prohlížeč, registraci nového uživatele (například `test@example.com`). Přihlaste se k každým prohlížečem s jiným uživatelem. Ověřte následující operace:

* Registrovaných uživatelů můžete zobrazit všechny schválené kontaktní údaje.
* Registrovaných uživatelů můžete upravit nebo odstranit svá vlastní data.
* Správci mohou schvalovat a odmítat kontaktní údaje. `Details` Zobrazení ukazuje **schválit** a **odmítnout** tlačítka.
* Správci můžou schvalovat a odmítat a upravit nebo odstranit všechna data.

| Uživatel                | Nasazených aplikací | Možnosti                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | Ne                | Upravit nebo odstranit vlastní data.                |
| manager@contoso.com | Ano               | Vlastní data, schvalovat a odmítat a upravit nebo odstranit. |
| admin@contoso.com   | Ano               | Schválit nebo odmítnout a upravit nebo odstranit všechna data. |

Vytvoření kontaktu v prohlížeči na správce. Zkopírujte adresu URL pro odstranění a upravit z kontaktujte správce. Vložte tyto odkazy do testů webového prohlížeče k ověření, že testovací uživatel nemůže provádět tyto operace.

## <a name="create-the-starter-app"></a>Vytvořit úvodní aplikaci

* Vytvoření aplikace stránky Razor s názvem "ContactManager"
  * Vytvoření aplikace s **jednotlivých uživatelských účtů**.
  * Pojmenujte ji "ContactManager" tak obor názvů odpovídá oboru názvů použitého v ukázce.
  * `-uld` Určuje LocalDB místo SQLite

  ```console
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* Přidat *modely/kontakt. cs*:

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* Vygenerované uživatelské rozhraní `Contact` modelu.
* Vytvořte počáteční migraci a aktualizaci databáze:

  ```console
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* Aktualizace **ContactManager** ukotvit v *Pages/_Layout.cshtml* souboru:

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* Testování aplikace pomocí vytváření, úpravy a odstranění kontaktu

### <a name="seed-the-database"></a>Přidání dat do databáze

Přidat [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) třídu *Data* složky.

Volání `SeedData.Initialize` z `Main`:

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

Otestujte, že aplikace naplnila databázi. Pokud existují nějaké řádky v kontaktu DB, metoda počáteční hodnoty není spuštěna.

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a>Další zdroje

* [Vytvoření webové aplikace .NET Core využívající SQL Database ve službě Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [ASP.NET Core povolení Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop). Toto testovací prostředí obsahuje větší podrobnosti o funkcích zabezpečení v tomto kurzu.
* <xref:security/authorization/introduction>
* [Autorizace na základě zásad](xref:security/authorization/policies)

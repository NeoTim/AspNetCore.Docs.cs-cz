---
title: 'Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core MVC'
author: rick-anderson
description: Vytvoření webového rozhraní API pomocí ASP.NET Core MVC
ms.author: riande
ms.custom: mvc
ms.date: 12/10/2018
uid: tutorials/first-web-api
ms.openlocfilehash: a27031550dd6fe810a250b369f83b7fe322aad1f
ms.sourcegitcommit: 1ea1b4fc58055c62728143388562689f1ef96cb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53329169"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core-mvc"></a>Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core MVC

Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Mike Wasson](https://github.com/mikewasson)

V tomto kurzu se naučíte se základy vytváření webových rozhraní API pomocí ASP.NET Core.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte projekt webového rozhraní api.
> * Přidejte třídu modelu.
> * Vytvořte kontext databáze.
> * Zaregistrujte kontext databáze.
> * Přidání kontroleru.
> * Přidejte metody CRUD.
> * Konfigurace směrování a cesty adresy URL.
> * Zadejte návratové hodnoty.
> * Volání webového rozhraní API pomocí nástroje Postman.
> * Volání webového rozhraní api pomocí jQuery.

Na konci máte webové rozhraní API, která může spravovat "úkolů" položky uložené v relační databázi.

## <a name="overview"></a>Přehled

Tento kurz vytvoří následující rozhraní API:

|rozhraní API | Popis | Text požadavku | Text odpovědi |
|--- | ---- | ---- | ---- |
|ZÍSKAT /api/todo | Získat všechny položky seznamu úkolů | Žádné | Pole položek úkolů|
|ZÍSKAT/webové rozhraní API/todo / {id} | Získat položky podle ID | Žádná | Položky seznamu úkolů|
|Publikovat/api/todo | Přidat novou položku | Položky seznamu úkolů | Položky seznamu úkolů |
|Vložení/webové rozhraní API/todo / {id} | Aktualizovat existující položku &nbsp; | Položky seznamu úkolů | Žádná |
|ODSTRANIT/webové rozhraní API/todo / {id} &nbsp; &nbsp; | Odstranění položky &nbsp; &nbsp; | Žádné | Žádná|

Následující diagram znázorňuje návrh aplikace.

![Klient je reprezentována pole na levé straně a odešle žádost a obdrží odpověď od aplikace vykreslen na pravé straně pole. V dialogovém okně aplikace tři pole představují kontroleru, model a vrstva přístupu k datům. Žádost vstupu do kontroleru aplikace a operace čtení a zápisu, ke kterým dochází mezi kontrolerem a vrstva přístupu k datům. Model je serializován a vrátí klientovi v odpovědi.](first-web-api/_static/architecture.png)

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-web-project"></a>Vytvoření webového projektu

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Z **souboru** nabídce vyberte možnost **nový** > **projektu**.
* Vyberte **webové aplikace ASP.NET Core** šablony. Pojmenujte projekt *TodoApi* a klikněte na tlačítko **OK**.
* V **nové webové aplikace ASP.NET Core – TodoApi** dialogového okna, vyberte verzi technologie ASP.NET Core. Vyberte **API** šablonu a klikněte na tlačítko **OK**. Proveďte **není** vyberte **povolit podporu Dockeru**.

![VS – dialogové okno nového projektu](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Změňte adresář (`cd`) do složky, která bude obsahovat složky projektu.
* Spusťte následující příkazy:

   ```console
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  Tyto příkazy vytvořte nový projekt webového rozhraní API a otevřete novou instanci sady Visual Studio Code v nové složce projektu.

* Když dialogové okno požádá, pokud chcete do projektu přidejte požadované prostředky, vyberte **Ano**.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* Vyberte **souboru** > **nové řešení**.

  ![macOS nové řešení](first-web-api-mac/_static/sln.png)

* Vyberte **aplikace .NET Core** > **webového rozhraní API ASP.NET Core** > **Další**.

  ![macOS dialogové okno nového projektu](first-web-api-mac/_static/1.png)
  
* V **nakonfigurovat nové technologie ASP.NET Core webové rozhraní API** dialogového okna, přijměte výchozí nastavení **Cílová architektura** z **.NET Core 2.2*.

* Zadejte *TodoApi* pro **název projektu** a pak vyberte **vytvořit**.

  ![Dialogové okno Konfigurace](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a>Testovat rozhraní API

Šablona projektu vytvoří `values` rozhraní API. Volání `Get` metoda v prohlížeči a testování aplikace.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Stisknutím kláves Ctrl + F5 spusťte aplikaci. Visual Studio spustí prohlížeč a přejde na `https://localhost:<port>/api/values`, kde `<port>` je číslo portu náhodně vybrané.

Pokud se zobrazí dialogové okno s dotazem, pokud by měla důvěřovat certifikátu služby IIS Express, vyberte **Ano**. V **upozornění zabezpečení** dialogové okno, které se zobrazí další, vyberte **Ano**.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Stisknutím kláves Ctrl + F5 spusťte aplikaci. V prohlížeči přejděte na následující adrese URL: [ https://localhost:5001/api/values ](https://localhost:5001/api/values).

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

Vyberte **spustit** > **spustit s ladění** aplikaci spustit. Spuštění prohlížeče Visual Studio pro Mac a přejde na `https://localhost:<port>`, kde `<port>` je číslo portu náhodně vybrané. Vrátí chybu HTTP 404 (Nenalezeno). Připojit `/api/values` na adresu URL (změňte adresu URL na `https://localhost:<port>/api/values`).

---

Vrátí následující JSON:

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a>Přidejte třídu modelu

A *modelu* je sada tříd, které představují data, která aplikace spravuje. Model pro tuto aplikaci je jedinou `TodoItem` třídy.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt. Vyberte **přidat** > **novou složku**. Název složky *modely*.

* Klikněte pravým tlačítkem myši *modely* a pak zvolte položku **přidat** > **třídy**. Název třídy *TodoItem* a vyberte **přidat**.

* Nahraďte kód šablony následujícím kódem:

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Přidat složku s názvem *modely*.

* Přidat `TodoItem` třídu *modely* složka s následujícím kódem:

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* Klikněte pravým tlačítkem na projekt. Vyberte **přidat** > **novou složku**. Název složky *modely*.

  ![Nová složka](first-web-api-mac/_static/folder.png)

* Klikněte pravým tlačítkem myši *modely* a pak zvolte položku **přidat** > **nový soubor** > **Obecné**  >  **Prázdná třída**.

* Název třídy *TodoItem*a potom klikněte na tlačítko **nový**.

* Nahraďte kód šablony následujícím kódem:

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

`Id` Vlastnost funguje jako jedinečný klíč v relační databázi.

Třídy modelu můžete kamkoliv v projektu, ale *modely* složky používají konvence.

## <a name="add-a-database-context"></a>Přidat kontext databáze

*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro daný datový model. Tato třída se vytvoří odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klikněte pravým tlačítkem myši *modely* a pak zvolte položku **přidat** > **třídy**. Název třídy *TodoContext* a klikněte na tlačítko **přidat**.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Přidat `TodoContext` třídu *modely* složky.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* Přidat `TodoContext` třídy v *modely* složky:

---

* Nahraďte kód šablony následujícím kódem:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>Zaregistrujte kontext databáze

V ASP.NET Core, služeb, jako je kontext databáze, musí zaregistrovat [injektáž závislostí (DI)](xref:fundamentals/dependency-injection) kontejneru. Kontejner poskytuje služby řadiče.

Aktualizace *Startup.cs* s následující zvýrazněný kód:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

Předchozí kód:

* Odebere nevyužité `using` deklarace.
* Přidá do kontejneru DI kontext databáze.
* Určuje, zda kontext databáze bude používat databázi v paměti.

## <a name="add-a-controller"></a>Přidání kontroleru

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klikněte pravým tlačítkem myši *řadiče* složky.
* Vyberte **přidat** > **nová položka**.
* V **přidat novou položku** dialogového okna, vyberte **třída Kontroleru rozhraní API** šablony.
* Název třídy *TodoController*a vyberte **přidat**.

  ![Přidání nové položky dialogové okno s kontrolerem v vyhledávací pole a webové rozhraní api kontroleru vybrané](first-web-api/_static/new_controller.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* V *řadiče* složku, vytvořte třídu s názvem `TodoController`.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* V *řadiče* složky, přidejte třídu `TodoController`.

---

* Nahraďte kód šablony následujícím kódem:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

Předchozí kód:

* Definuje třídu kontroleru rozhraní API bez metody.
* Třída se upraví [ `[ApiController]` ](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) atribut. Tento atribut označuje, že kontroler reaguje na požadavky webové rozhraní API. Informace o konkrétní chování, které umožňuje atribut najdete v tématu [Poznámka atributem objektu ApiController](xref:web-api/index#annotation-with-apicontroller-attribute).
* Vložit kontext databáze používá DI (`TodoContext`) do kontroleru. Kontext databáze se používá ve všech [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) metody v kontroleru.
* Přidá položku s názvem `Item1` k databázi, pokud databáze je prázdný. Tento kód je v konstruktoru, proto se spustí pokaždé, když se nový požadavek HTTP. Pokud odstraníte všechny položky, vytvoří konstruktor `Item1` znovu při příštím volání metody rozhraní API. Proto může účet vypadat třeba odstranění akce nebyla úspěšná, když se ve skutečnosti fungovalo.

## <a name="add-get-methods"></a>Přidejte metody Get

Chcete-li poskytují rozhraní API, který načte položky, přidejte následující metody, které `TodoController` třídy:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

Tyto metody implementovat dva koncové body GET:

* `GET /api/todo`
* `GET /api/todo/{id}`

Testování aplikace pomocí volání dva koncové body v prohlížeči. Příklad:

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

Následující odpověď HTTP je vytvořen voláním `GetTodoItems`:

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a>Směrování a adresa URL cesty

[ `[HttpGet]` ](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) Atribut označuje metodu, která reaguje na požadavek HTTP GET. Cesta adresy URL pro každou metodu se vypočte takto:

* Začněte s řetězec šablony v kontroleru `Route` atribut:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* Nahraďte `[controller]` s názvem kontroleru, který je název třídy kontroleru minus příponu "Kontroleru". V tomto příkladu je název třídy kontroleru **Todo**Kontroleru, názvu kontroleru je "todo". ASP.NET Core [směrování](xref:mvc/controllers/routing) velká a malá písmena.
* Pokud `[HttpGet]` atribut má šablona trasy (například `[HttpGet("/products")]`, připojení, která k cestě. Tato ukázka nepoužívá šablony. Další informace najdete v tématu [atribut směrování pomocí protokolu Http [příkaz] atributy](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

V následujícím `GetTodoItem` metody `"{id}"` je proměnná zástupný symbol pro jedinečný identifikátor položky úkolů. Když `GetTodoItem` je vyvolána, hodnota `"{id}"` v adrese URL je k dispozici v metodě jeho`id` parametru.

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

`Name = "GetTodo"` Parametr vytvoří pojmenovanou trasu. Zobrazí se vám později jak může aplikace používat název vytvořit odkaz na protokol HTTP pomocí názvu trasy.

## <a name="return-values"></a>Vrácené hodnoty

Návratový typ `GetTodoItems` a `GetTodoItem` metody je [ActionResult\<T > typ](xref:web-api/action-return-types#actionresultt-type). ASP.NET Core automaticky serializuje objekt, který má [JSON](https://www.json.org/) a zapíše do datové části zprávy s odpovědí JSON. Kód odpovědi pro tento návratový typ je 200, za předpokladu, že nejsou žádné neošetřené výjimky. Nezpracované výjimky jsou přeloženy do chyby 5xx.

`ActionResult` typy vrácených hodnot může představovat stavové kódy HTTP široký rozsah. Například `GetTodoItem` může vrátit hodnoty dvou různých stavu:

* Pokud žádná položka odpovídá požadovaným ID, vrátí metoda 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) kód chyby.
* V opačném případě vrátí metoda 200 s těla odpovědi JSON. Vrací `item` výsledkem odpověď HTTP 200.

## <a name="test-the-gettodoitems-method"></a>Test GetTodoItems – metoda

Tento kurz používá Postman k otestování webové rozhraní API.

* Nainstalujte [Postman](https://www.getpostman.com/apps)
* Spusťte webovou aplikaci.
* Spusťte Postman.
* Zakázat **ověření certifikátu SSL**
  
  * Z **soubor > Nastavení** (**Obecné* kartu), zakažte **ověření certifikátu SSL**.
    > [!WARNING]
    > Znovu povolte ověření certifikátu SSL po otestování kontroleru.

* Vytvořte novou žádost.
  * Nastavte jako metodu HTTP **získat**.
  * Nastavení adresy URL požadavku `https://localhost:<port>/api/todo`. Například, `https://localhost:5001/api/todo`.
* Nastavte **dvě podokna zobrazení** v nástroji Postman.
* Vyberte **Poslat**.

![Postman se požadavek Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a>Přidání metody vytvoření

Přidejte následující `PostTodoItem` metody:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

Předchozí kód je metoda HTTP POST, je určeno [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) atribut. Metoda získá hodnotu položky úkolů z textu požadavku HTTP.

`CreatedAtRoute` Metody:

* Vrátí odezvě 201. HTTP 201 je standardní odpověď pro metodu POST protokolu HTTP, která vytvoří nový prostředek na serveru.
* Přidá do odpovědi hlavičku umístění. Hlavička umístění Určuje identifikátor URI nově vytvořeného úkolu položky. Další informace najdete v tématu [10.2.2 201 – vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* K vytvoření adresy URL používá "GetTodo" s názvem trasy. "GetTodo" s názvem trasy je definována v `GetTodoItem`:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a>Test PostTodoItem – metoda

* Sestavte projekt.
* V nástroji Postman, nastavte jako metodu HTTP `POST`.
* Vyberte **tělo** kartu.
* Vyberte **nezpracovaná** přepínač.
* Nastavte typ **JSON (application/json)**.
* V textu požadavku zadejte JSON pro úkol:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* Vyberte **Poslat**.

  ![Postman se vytvořit žádost](first-web-api/_static/create.png)

  Pokud obdržíte chybu 405 Metoda není povolena, je pravděpodobně výsledkem není kompilaci projektu po přidání po přidání `PostTodoItem` metody.

### <a name="test-the-location-header-uri"></a>Testování hlavičku location identifikátoru URI

* Vyberte **záhlaví** kartu **odpovědi** podokně.
* Kopírovat **umístění** hodnota hlavičky:

  ![Karta hlavičky z konzoly nástroje Postman](first-web-api/_static/pmc2.png)

* Nastavte jako metodu GET.
* Vložte identifikátor URI (například `https://localhost:5001/api/Todo/2`)
* Vyberte **Poslat**.

## <a name="add-a-puttodoitem-method"></a>Přidejte metodu PutTodoItem

Přidejte následující `PutTodoItem` metody:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

`PutTodoItem` je podobný `PostTodoItem`, s výjimkou používá HTTP PUT. Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). Podle specifikace HTTP vyžaduje požadavek PUT klientovi umožní odeslat celý aktualizovanou entitu, nikoliv pouze změny. Chcete-li podporovat částečné aktualizace, použijte [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).

### <a name="test-the-puttodoitem-method"></a>Test PutTodoItem – metoda

Aktualizovat položku seznamu úkolů, která má id = 1 a nastavte její název na "informačního kanálu ryb":

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

Následující obrázek ukazuje Postman aktualizace:

![Postman konzola znázorňující 204 (žádný obsah) odpovědi](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a>Přidejte metodu DeleteTodoItem

Přidejte následující `DeleteTodoItem` metody:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

`DeleteTodoItem` Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).

### <a name="test-the-deletetodoitem-method"></a>Test DeleteTodoItem – metoda

Pomocí nástroje Postman odstraňte položku úkolu:

* Nastavte jako metodu `DELETE`.
* Nastavte identifikátor URI objektu odstranit, například `https://localhost:5001/api/todo/1`
* Vyberte **odeslat**

Ukázkové aplikace můžete odstranit všechny položky, ale když se odstraní poslední položky, je vytvořen nový pomocí konstruktoru třídy modelu při příštím spuštění se volá rozhraní API.

## <a name="call-the-api-with-jquery"></a>Volání rozhraní API pomocí jQuery

V této části se přidá stránku HTML, který používá jQuery volat webové rozhraní api. jQuery zahájí požadavek a aktualizuje stránku s podrobnostmi o z odpovědi rozhraní API.

Nakonfiguruje aplikaci, aby [doručování statických souborů](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a [povolit výchozí mapování souboru](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_):

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

::: moniker range=">= aspnetcore-2.2"
Vytvoření *wwwroot* složku v adresáři projektu.
::: moniker-end

Přidat soubor HTML s názvem *index.html* k *wwwroot* adresáře. Nahraďte jeho obsah následujícím kódem:

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

Přidejte soubor JavaScriptu s názvem *site.js* k *wwwroot* adresáře. Nahraďte jeho obsah následujícím kódem:

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

Ke změně nastavení spouštěcí projekt ASP.NET Core může být nutné testovací stránka HTML místně:

* Otevřít *Properties\launchSettings.json*.
* Odeberte `launchUrl` vlastnost, aby se aplikace na *index.html*&mdash;výchozí soubor projektu.

Existuje několik způsobů, jak získat jQuery. V předchozím fragmentu kódu je načíst knihovnu ze sítě CDN.

Tato ukázka volá všechny metody CRUD rozhraní API. Následuje vysvětlení volání rozhraní API.

### <a name="get-a-list-of-to-do-items"></a>Získání seznamu úkolů

JQuery [ajax](https://api.jquery.com/jquery.ajax/) funkce odešle `GET` žádosti na rozhraní API, které vrací JSON představující pole položek úkolů. `success` Funkce zpětného volání je vyvolána, pokud neproběhne. Při zpětném volání modelu DOM se aktualizuje informacemi úkolů.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a>Přidat položku seznamu úkolů

[Ajax](https://api.jquery.com/jquery.ajax/) funkce odešle `POST` požadavek se úkol v textu požadavku. `accepts` a `contentType` možnosti jsou nastaveny na `application/json` zadat typ média, který se přijalo a odeslalo. Položky seznamu úkolů je převést na JSON pomocí [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify). Rozhraní API po návratu úspěšné stavový kód, `getData` funkce se vyvolala aktualizovat tabulku HTML.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a>Aktualizace položky seznamu úkolů

Aktualizace položky úkolu je podobné jako přidání jednoho. `url` Změny přidat jedinečný identifikátor položky a `type` je `PUT`.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a>Odstranit úkol

Odstranění položky úkolu se provádí tak, že nastavíte `type` při volání AJAX `DELETE` a zadáte jedinečný identifikátor položky v adrese URL.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

## <a name="additional-resources"></a>Další zdroje

[Zobrazení nebo stažení ukázkového kódu pro účely tohoto kurzu](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/samples). Zobrazit [stažení](xref:index#how-to-download-a-sample).

Další informace naleznete v následujících materiálech:

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/index>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořte projekt webového rozhraní api.
> * Přidejte třídu modelu.
> * Vytvořte kontext databáze.
> * Zaregistrujte kontext databáze.
> * Přidání kontroleru.
> * Přidejte metody CRUD.
> * Konfigurace směrování a cesty adresy URL.
> * Zadejte návratové hodnoty.
> * Volání webového rozhraní API pomocí nástroje Postman.
> * Volání webového rozhraní api pomocí jQuery.

Přejděte k dalšímu kurzu se naučíte generování stránek nápovědy rozhraní API:

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

---
title: 'Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core'
author: rick-anderson
description: Naučte se vytvářet webové rozhraní API pomocí ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
- Models
uid: tutorials/first-web-api
ms.openlocfilehash: 7f684d96ee9fd640abdc750503bed1b2a836a459
ms.sourcegitcommit: 62cc131969b2379f7a45c286a751e22d961dfbdb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90847733"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a>Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core

Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirka Larkin](https://twitter.com/serpent5)a [Jan Wasson](https://github.com/mikewasson)

V tomto kurzu se naučíte základy vytváření webového rozhraní API pomocí ASP.NET Core.

::: moniker range=">= aspnetcore-5.0"

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte projekt webového rozhraní API.
> * Přidejte třídu modelu a kontext databáze.
> * Generování uživatelského rozhraní pro kontroler pomocí metod CRUD
> * Nakonfigurujte směrování, cesty URL a návratové hodnoty.
> * Zavolejte webové rozhraní API pomocí post.

Na konci máte webové rozhraní API, které může spravovat položky "k tomu" uložené v databázi.

## <a name="overview"></a>Přehled

V tomto kurzu se vytvoří následující rozhraní API:

|Rozhraní API | Popis | Text požadavku | Text odpovědi |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | Získat všechny položky úkolů | Žádné | Pole položek úkolů|
|`GET /api/TodoItems/{id}` | Získat položku podle ID | Žádné | Položka úkolů|
|`POST /api/TodoItems` | Přidat novou položku | Položka úkolů | Položka úkolů |
|`PUT /api/TodoItems/{id}` | Aktualizovat existující položku &nbsp; | Položka úkolů | Žádné |
|`DELETE /api/TodoItems/{id}` &nbsp; &nbsp; | Odstranění položky &nbsp;&nbsp; | Žádné | Žádné|

V následujícím diagramu vidíte návrh aplikace.

![Klient je reprezentován polem vlevo. Odešle požadavek a přijme odpověď z aplikace, pole nakreslené na pravé straně. V poli aplikace tři čtverečky reprezentují kontroler, model a vrstvu přístupu k datům. Požadavek přichází do kontroleru aplikace a operace čtení/zápisu probíhají mezi kontrolérem a vrstvou přístupu k datům. Model je serializován a vrácen klientovi v odpovědi.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a>Vytvoření webového projektu

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V nabídce **soubor** vyberte možnost **Nový** > **projekt**.
* Vyberte šablonu **ASP.NET Core webové aplikace** a klikněte na tlačítko **Další**.
* Pojmenujte projekt *TodoApi* a klikněte na **vytvořit**.
* V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 5,0** . Vyberte šablonu **rozhraní API** a klikněte na **vytvořit**.

![Dialogové okno VS New Project](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Změňte adresáře ( `cd` ) na složku, která bude obsahovat složku projektu.
* Spusťte následující příkazy:

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* Pokud se zobrazí dialogové okno s dotazem, zda chcete přidat požadované prostředky do projektu, vyberte možnost **Ano**.

  Předchozí příkazy:

  * Vytvoří nový projekt webového rozhraní API a otevře ho v Visual Studio Code.
  * Přidá balíčky NuGet, které jsou nutné v další části.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Vyberte **soubor** > **nové řešení**.

  ![macOS nové řešení](first-web-api-mac/_static/sln.png)

* V Visual Studio pro Mac starší než verze 8,6 Vyberte rozhraní **.NET Core**  >  **App**  >  **API**  >  **Next**. V části verze 8,6 nebo novější vyberte **webové a konzolové**  >  **App**  >  **rozhraní API**aplikace  >  **Next**.

  ![Výběr šablony rozhraní API macOS](first-web-api-mac/_static/api_template.png)

* V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** vyberte nejnovější **verzi rozhraní .NET**Core 3. x. Vyberte **Další**.

* Jako **název projektu** zadejte *TodoApi* a pak vyberte **vytvořit**.

  ![Dialogové okno Konfigurace](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

Ve složce projektu otevřete terminál příkazu a spusťte následující příkazy:

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a>Testování projektu

Šablona projektu vytvoří `WeatherForecast` rozhraní API s podporou pro [Swagger](xref:tutorials/web-api-help-pages-using-swagger).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.

[!INCLUDE[](~/includes/trustCertVS.md)]

  Spustí se Visual Studio:

* IIS Express webový server.
* Výchozí prohlížeč a přejít na `https://localhost:<port>/https://localhost:5001/swagger/index.html` , kde `<port>` je náhodně zvolené číslo portu.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci. V prohlížeči přejít na následující adresu URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Vyberte **Spustit**  >  **ladění** a spusťte aplikaci. Visual Studio pro Mac spustí prohlížeč a přejde na `https://localhost:<port>` místo, kde `<port>` je náhodně zvolené číslo portu. Vrátí se chyba HTTP 404 (Nenalezeno). Připojit `/swagger` k adrese URL (změnit adresu URL na `https://localhost:<port>/swagger` )

---

Zobrazí se stránka Swagger `/swagger/index.html` . Vyberte **Get**  >  **Try**on  >  **Execute**. Stránka zobrazuje:

* Příkaz [složeného](https://curl.haxx.se/) pro otestování rozhraní WeatherForecast API.
* Adresa URL pro otestování rozhraní WeatherForecast API
* Kód odpovědi, tělo a záhlaví.
* Rozevírací seznam s typy médií a ukázkovou hodnotou a schématem.

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
Swagger se používá ke generování užitečné dokumentace a stránek s nápovědu pro webová rozhraní API. Tento kurz se zaměřuje na vytvoření webového rozhraní API. Další informace o Swagger najdete v tématu <xref:tutorials/web-api-help-pages-using-swagger> .

Zkopírujte a za **adresu URL žádosti** v prohlížeči:  `https://localhost:<port>/WeatherForecast`

Vrátí se JSON podobný následujícímu:

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

### <a name="update-the-launchurl"></a>Aktualizace launchUrl

V *Properties\launchSettings.jszapnuto*, aktualizovat `launchUrl` z `"swagger"` na `"api/TodoItems"` :

```json
"launchUrl": "api/TodoItems",
```

Protože Swagger byl odebrán, předchozí kód změní adresu URL, která se spustí do metody GET kontroleru přidané v následujících oddílech.

## <a name="add-a-model-class"></a>Přidat třídu modelu

*Model* je sada tříd, které reprezentují data, která aplikace spravuje. Model pro tuto aplikaci je jediná `TodoItem` Třída.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt. Vyberte **Přidat**  >  **novou složku**. Pojmenujte složku *Models* .

* Klikněte pravým tlačítkem na *Models* složku a vyberte **Přidat**  >  **třídu**. Pojmenujte třídu *TodoItem* a vyberte **Přidat**.

* Kód šablony nahraďte následujícím kódem:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Přidejte složku s názvem *Models* .

* Přidejte `TodoItem` třídu do *Models* složky s následujícím kódem:

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Klikněte pravým tlačítkem na projekt. Vyberte **Přidat**  >  **novou složku**. Pojmenujte složku *Models* .

  ![Nová složka](first-web-api-mac/_static/folder.png)

* Klikněte pravým tlačítkem na *Models* složku a vyberte **Přidat** > **nový soubor** > **Obecná** > **prázdná třída**.

* Pojmenujte třídu *TodoItem*a potom klikněte na **Nový**.

* Kód šablony nahraďte následujícím kódem:

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

`Id`Vlastnost funguje jako jedinečný klíč v relační databázi.

Třídy modelu mohou jít kdekoli v projektu, ale *Models* Složka je používána konvencí.

## <a name="add-a-database-context"></a>Přidání kontextu databáze

*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro datový model. Tato třída je vytvořena odvozením z <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> třídy.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="add-nuget-packages"></a>Přidání balíčků NuGet

* V nabídce **nástroje** vyberte **správce balíčků NuGet > spravovat balíčky NuGet pro řešení**.
* Vyberte kartu **Procházet** a pak zadejte * * Microsoft.
**EntityFrameworkCore. SqlServer** do vyhledávacího pole.
<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Delete this line at RTM -->
* Zaškrtněte políčko **zahrnout předběžné verze** , aby byla k dispozici verze 5,0 RC. 
* V levém podokně vyberte **Microsoft. EntityFrameworkCore. SqlServer** .
* Zaškrtněte políčko **projekt** v pravém podokně a pak vyberte **nainstalovat**.
* Pomocí předchozích pokynů přidejte balíček NuGet **Microsoft. EntityFrameworkCore. inMemory** .

<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Update this image at RTM -->
![Správce balíčků NuGet](first-web-api/_static/5/vsNuGet.png)

## <a name="add-the-todocontext-database-context"></a>Přidání kontextu databáze TodoContext

* Klikněte pravým tlačítkem na *Models* složku a vyberte **Přidat**  >  **třídu**. Pojmenujte třídu *TodoContext* a klikněte na **Přidat**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

* Přidejte `TodoContext` do *Models* složky třídu.

---

* Zadejte následující kód:

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>Registrace kontextu databáze

V ASP.NET Core musí být služby, jako je například kontext databáze, registrovány s kontejnerem [vkládání závislostí (di)](xref:fundamentals/dependency-injection) . Kontejner poskytuje službu pro řadiče.

*Startup.cs* aktualizujte pomocí následujícího kódu:

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

Předcházející kód:

* Odebere volání Swagger.
* Odebere nepoužívané `using` deklarace.
* Přidá kontext databáze do kontejneru DI.
* Určuje, že kontext databáze bude používat databázi v paměti.

## <a name="scaffold-a-controller"></a>Generování uživatelského rozhraní kontroleru

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klikněte pravým tlačítkem na složku *řadiče* .
* Vyberte **Přidat** > **novou vygenerované položky**.
* Vyberte možnost **kontroler API s akcemi, pomocí Entity Framework**a pak vyberte **Přidat**.
* V dialogovém okně **Přidání kontroleru rozhraní API pomocí akcí Entity Framework** :

  * V **třídě modelu**vyberte **TodoItem (TodoApi. Models )** .
  * Ve **třídě Context data**vyberte **TodoContext (TodoApi. Models )** .
  * Vyberte **Přidat**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

Spusťte následující příkazy:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

Předchozí příkazy:

* Přidejte balíčky NuGet vyžadované pro generování uživatelského rozhraní.
* Nainstaluje modul generování uživatelského rozhraní ( `dotnet-aspnet-codegenerator` ).
* Generování uživatelského rozhraní `TodoItemsController` .

---

Generovaný kód:

* Označí třídu [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributem. Tento atribut označuje, že kontroler reaguje na požadavky webového rozhraní API. Informace o konkrétním chování, které atribut povoluje, naleznete v tématu <xref:web-api/index> .
* Pomocí DI vloží kontext databáze ( `TodoContext` ) do kontroleru. Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v kontroleru.

Šablony ASP.NET Core pro:

* Řadiče se zobrazeními jsou zahrnuta `[action]` v šabloně trasy.
* Řadiče API neobsahují `[action]` šablonu trasy.

Pokud `[action]` token není v šabloně směrování, název [Akce](xref:mvc/controllers/routing#action) je vyloučený z trasy. To znamená, že název přidružené metody akce se ve shodě trasy nepoužívá.

## <a name="update-the-posttodoitem-create-method"></a>Aktualizace metody Create PostTodoItem

Nahraďte příkaz return v `PostTodoItem` operátoru k použití operátoru [nameof](/dotnet/csharp/language-reference/operators/nameof) :

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

Předchozí kód je metoda HTTP POST, jak je označena [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atributem. Metoda získá hodnotu položky k seřízení z těla požadavku HTTP.

Další informace najdete v tématu [Směrování atributů s atributy http [příkaz]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

<xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>Metoda:

* Pokud je úspěšná, vrátí [stavový kód HTTP 201](https://developer.mozilla.org/docs/Web/HTTP/Status/201) . HTTP 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru.
* Přidá hlavičku [umístění](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) k odpovědi. `Location`Záhlaví Určuje [identifikátor URI](https://developer.mozilla.org/docs/Glossary/URI) nově vytvořené položky. Další informace najdete v tématu [10.2.2 201 vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* Odkazuje na `GetTodoItem` akci vytvoření `Location` identifikátoru URI hlavičky. `nameof`Klíčové slovo jazyka C# se používá k zamezení hardwarového kódování názvu akce ve `CreatedAtAction` volání.

### <a name="install-postman"></a>Nainstalovat post

V tomto kurzu se používá post k testování webového rozhraní API.

* Nainstalovat [post](https://www.getpostman.com/downloads/)
* Spusťte webovou aplikaci.
* Spusťte post.
* Zakázat **ověřování certifikátu SSL**
  * V **File** > **Nastavení** souboru (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.
    > [!WARNING]
    > Po otestování kontroleru znovu povolte ověření certifikátu SSL.

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a>PostTodoItem testu s použitím post

* Vytvoří novou žádost.
* Nastavte metodu HTTP na `POST` .
* Nastavte identifikátor URI na `https://localhost:<port>/api/TodoItems` . Například, `https://localhost:5001/api/TodoItems`.
* Vyberte kartu **tělo** .
* Vyberte **nezpracovaný** přepínač.
* Nastavte typ na **JSON (Application/JSON)**.
* Do textu žádosti zadejte JSON pro položku úkolů:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* Vyberte **Poslat**.

  ![Poslat pomocí žádosti o vytvoření](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a>Otestování identifikátoru URI hlavičky umístění

Identifikátor URI hlaviček umístění je možné otestovat v prohlížeči. Zkopírujte identifikátor URI hlavičky umístění a vložte ho do prohlížeče.

Testování v nástroji post:

* V podokně **odpověď** vyberte kartu **hlavičky** .
* Zkopírujte hodnotu hlavičky **umístění** :

  ![Karta hlavičky v konzole pro odesílání](first-web-api/_static/3/create.png)

* Nastavte metodu HTTP na `GET` .
* Nastavte identifikátor URI na `https://localhost:<port>/api/TodoItems/1` . Například, `https://localhost:5001/api/TodoItems/1`.
* Vyberte **Poslat**.

## <a name="examine-the-get-methods"></a>Projděte si metody GET.

Jsou implementovány dva koncové body GET:

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

Otestujte aplikaci voláním dvou koncových bodů z prohlížeče nebo po odeslání. Příklad:

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

Odpověď podobná následující je vytvořena voláním metody `GetTodoItems` :

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a>Test Get s použitím metody post

* Vytvoří novou žádost.
* Nastavte metodu HTTP na **Get**.
* Nastavte identifikátor URI žádosti na `https://localhost:<port>/api/TodoItems` . Například, `https://localhost:5001/api/TodoItems`.
* Nastavte v příspěvku **dva zobrazení podokna** .
* Vyberte **Poslat**.

Tato aplikace používá databázi v paměti. Pokud se aplikace zastaví a spustí, předchozí požadavek GET nebude vracet žádná data. Pokud se nevrátí žádná data, [odešlete](#post) data do aplikace.

## <a name="routing-and-url-paths"></a>Směrování a cesty URL

[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)Atribut označuje metodu, která reaguje na požadavek HTTP GET. Cesta URL pro každou metodu je konstruována takto:

* Začněte s řetězcem šablony v `Route` atributu kontroleru:

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* Nahraďte `[controller]` názvem kontroleru, který je podle konvence názvem třídy kontroleru minus přípona Controller. V této ukázce je název třídy kontroleru **TodoItems**Controller, takže název kontroleru je "TodoItems". [Směrování](xref:mvc/controllers/routing) ASP.NET Core rozlišuje malá a velká písmena.
* Pokud `[HttpGet]` má atribut směrovací šablonu (například `[HttpGet("products")]` ), přidejte ji k cestě. Tato ukázka nepoužívá šablonu. Další informace najdete v tématu [Směrování atributů s atributy http [příkaz]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

V následující `GetTodoItem` metodě `"{id}"` je proměnná zástupného symbolu pro jedinečný identifikátor položky k provedení. Při `GetTodoItem` vyvolání je hodnota `"{id}"` v adrese URL poskytnuta metodě v `id` parametru.

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>Vrácené hodnoty

Návratový typ `GetTodoItems` `GetTodoItem` metod a je [ActionResult \<T> typ](xref:web-api/action-return-types#actionresultt-type). ASP.NET Core automaticky serializovat objekt do formátu [JSON](https://www.json.org/) a zapíše JSON do textu zprávy s odpovědí. Kód odpovědi pro tento návratový typ je [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), za předpokladu, že neexistují žádné neošetřené výjimky. Neošetřené výjimky jsou přeloženy na 5xx chyby.

`ActionResult` návratové typy mohou představovat široké spektrum stavových kódů HTTP. Například `GetTodoItem` může vracet dvě různé stavové hodnoty:

* Pokud žádná položka neodpovídá požadovanému ID, vrátí metoda kód chyby [stavu 404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> .
* V opačném případě metoda vrátí 200 text odpovědi JSON. `item`Výsledkem vrácení výsledků odpovědi HTTP 200.

## <a name="the-puttodoitem-method"></a>Metoda PutTodoItem

Prohlédněte si metodu `PutTodoItem`:

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

`PutTodoItem` se podobá `PostTodoItem` , s tím rozdílem, že používá Put http. Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). V souladu se specifikací HTTP vyžaduje požadavek PUT klientovi, aby odesílal celou aktualizovanou entitu, a ne jenom změny. K podpoře částečných aktualizací použijte [opravu http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).

Pokud se zobrazí chyba při volání `PutTodoItem` , `GET` zajistěte, aby byla položka v databázi.

### <a name="test-the-puttodoitem-method"></a>Test metody PutTodoItem

Tato ukázka používá databázi v paměti, která musí být inicializována při každém spuštění aplikace. Před provedením volání PUT musí existovat položka v databázi. Voláním metody GET zajistěte, aby byla položka v databázi před provedením volání PUT.

Aktualizujte položku úkolů s ID = 1 a nastavte její název na `"feed fish"` :

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

Na následujícím obrázku je znázorněná aktualizace po odeslání:

![Konzola pro publikování zobrazující 204 (bez obsahu)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a>Metoda DeleteTodoItem

Prohlédněte si metodu `DeleteTodoItem`:

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a>Test metody DeleteTodoItem

Odstranění položky úkolů pomocí metody post:

* Nastavte metodu na `DELETE` .
* Nastavte identifikátor URI objektu, který má být odstraněn (například `https://localhost:5001/api/TodoItems/1` ).
* Vyberte **Poslat**.

<a name="over-post"></a>

## <a name="prevent-over-posting"></a>Zabránit navýšení příspěvků

V současné době ukázková aplikace zveřejňuje celý `TodoItem` objekt. Produkční aplikace obvykle omezují zadaná data a vracejí je pomocí podmnožiny modelu. Je to několik důvodů na pozadí a zabezpečení je hlavní. Podmnožina modelu je obvykle označována jako objekt Přenos dat (DTO), vstupní model nebo model zobrazení. **DTO** se používá v tomto článku.

DTO se dá použít k těmto akcím:

* Zabránit přeúčtování.
* Skrytí vlastností, které klienti nemají zobrazovat.
* Vynechejte některé vlastnosti, aby se snížila velikost datové části.
* Ploché grafy objektů, které obsahují vnořené objekty. Ploché grafy objektů můžou být pro klienty pohodlnější.

Chcete-li předvést DTO přístup, aktualizujte `TodoItem` třídu tak, aby obsahovala tajné pole:

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

Pole tajného klíče musí být z této aplikace skryté, ale aplikace pro správu může tuto možnost vystavit.

Ověřte, že můžete publikovat a získat pole tajného klíče.

Vytvoření modelu DTO:

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

Aktualizujte, `TodoItemsController` aby se použil `TodoItemDTO` :

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

Ověřte, že nemůžete publikovat nebo získat pole tajného kódu.

## <a name="call-the-web-api-with-javascript"></a>Volání webového rozhraní API pomocí JavaScriptu

Viz [kurz: volání ASP.NET Core webového rozhraní API pomocí JavaScriptu](xref:tutorials/web-api-javascript).

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte projekt webového rozhraní API.
> * Přidejte třídu modelu a kontext databáze.
> * Generování uživatelského rozhraní pro kontroler pomocí metod CRUD
> * Nakonfigurujte směrování, cesty URL a návratové hodnoty.
> * Zavolejte webové rozhraní API pomocí post.

Na konci máte webové rozhraní API, které může spravovat položky "k tomu" uložené v databázi.

## <a name="overview"></a>Přehled

V tomto kurzu se vytvoří následující rozhraní API:

|Rozhraní API | Popis | Text požadavku | Text odpovědi |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | Získat všechny položky úkolů | Žádné | Pole položek úkolů|
|`GET /api/TodoItems/{id}` | Získat položku podle ID | Žádné | Položka úkolů|
|`POST /api/TodoItems` | Přidat novou položku | Položka úkolů | Položka úkolů |
|`PUT /api/TodoItems/{id}` | Aktualizovat existující položku &nbsp; | Položka úkolů | Žádné |
|`DELETE /api/TodoItems/{id}` &nbsp; &nbsp; | Odstranění položky &nbsp;&nbsp; | Žádné | Žádné|

V následujícím diagramu vidíte návrh aplikace.

![Klient je reprezentován polem vlevo. Odešle požadavek a přijme odpověď z aplikace, pole nakreslené na pravé straně. V poli aplikace tři čtverečky reprezentují kontroler, model a vrstvu přístupu k datům. Požadavek přichází do kontroleru aplikace a operace čtení/zápisu probíhají mezi kontrolérem a vrstvou přístupu k datům. Model je serializován a vrácen klientovi v odpovědi.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a>Vytvoření webového projektu

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V nabídce **soubor** vyberte možnost **Nový** > **projekt**.
* Vyberte šablonu **ASP.NET Core webové aplikace** a klikněte na tlačítko **Další**.
* Pojmenujte projekt *TodoApi* a klikněte na **vytvořit**.
* V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 3,1** . Vyberte šablonu **rozhraní API** a klikněte na **vytvořit**.

![Dialogové okno VS New Project](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Změňte adresáře ( `cd` ) na složku, která bude obsahovat složku projektu.
* Spusťte následující příkazy:

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* Pokud se zobrazí dialogové okno s dotazem, zda chcete přidat požadované prostředky do projektu, vyberte možnost **Ano**.

  Předchozí příkazy:

  * Vytvoří nový projekt webového rozhraní API a otevře ho v Visual Studio Code.
  * Přidá balíčky NuGet, které jsou nutné v další části.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Vyberte **soubor** > **nové řešení**.

  ![macOS nové řešení](first-web-api-mac/_static/sln.png)

* V Visual Studio pro Mac starší než verze 8,6 Vyberte rozhraní **.NET Core**  >  **App**  >  **API**  >  **Next**. V části verze 8,6 nebo novější vyberte **webové a konzolové**  >  **App**  >  **rozhraní API**aplikace  >  **Next**.

  ![Výběr šablony rozhraní API macOS](first-web-api-mac/_static/api_template.png)

* V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** vyberte nejnovější **verzi rozhraní .NET**Core 3. x. Vyberte **Další**.

* Jako **název projektu** zadejte *TodoApi* a pak vyberte **vytvořit**.

  ![Dialogové okno Konfigurace](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

Ve složce projektu otevřete terminál příkazu a spusťte následující příkazy:

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a>Testování rozhraní API

Šablona projektu vytvoří `WeatherForecast` rozhraní API. Voláním `Get` metody z prohlížeče otestujete aplikaci.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci. Visual Studio spustí prohlížeč a přejde na `https://localhost:<port>/WeatherForecast` místo, kde `<port>` je náhodně zvolené číslo portu.

Pokud se zobrazí dialogové okno s dotazem, jestli byste měli důvěřovat certifikátu IIS Express, vyberte **Ano**. V dialogovém okně **Upozornění zabezpečení** , které se zobrazí jako další, vyberte **Ano**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci. V prohlížeči přejdete na následující adresu URL: `https://localhost:5001/WeatherForecast` .

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Vyberte **Spustit**  >  **ladění** a spusťte aplikaci. Visual Studio pro Mac spustí prohlížeč a přejde na `https://localhost:<port>` místo, kde `<port>` je náhodně zvolené číslo portu. Vrátí se chyba HTTP 404 (Nenalezeno). Připojit `/WeatherForecast` k adrese URL (změnit adresu URL na `https://localhost:<port>/WeatherForecast` )

---

Vrátí se JSON podobný následujícímu:

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a>Přidat třídu modelu

*Model* je sada tříd, které reprezentují data, která aplikace spravuje. Model pro tuto aplikaci je jediná `TodoItem` Třída.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt. Vyberte **Přidat**  >  **novou složku**. Pojmenujte složku *Models* .

* Klikněte pravým tlačítkem na *Models* složku a vyberte **Přidat**  >  **třídu**. Pojmenujte třídu *TodoItem* a vyberte **Přidat**.

* Kód šablony nahraďte následujícím kódem:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Přidejte složku s názvem *Models* .

* Přidejte `TodoItem` třídu do *Models* složky s následujícím kódem:

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Klikněte pravým tlačítkem na projekt. Vyberte **Přidat**  >  **novou složku**. Pojmenujte složku *Models* .

  ![Nová složka](first-web-api-mac/_static/folder.png)

* Klikněte pravým tlačítkem na *Models* složku a vyberte **Přidat** > **nový soubor** > **Obecná** > **prázdná třída**.

* Pojmenujte třídu *TodoItem*a potom klikněte na **Nový**.

* Kód šablony nahraďte následujícím kódem:

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

`Id`Vlastnost funguje jako jedinečný klíč v relační databázi.

Třídy modelu mohou jít kdekoli v projektu, ale *Models* Složka je používána konvencí.

## <a name="add-a-database-context"></a>Přidání kontextu databáze

*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro datový model. Tato třída je vytvořena odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="add-nuget-packages"></a>Přidání balíčků NuGet

* V nabídce **nástroje** vyberte **správce balíčků NuGet > spravovat balíčky NuGet pro řešení**.
* Vyberte kartu **Procházet** a potom do vyhledávacího pole zadejte **Microsoft. EntityFrameworkCore. SqlServer** .
* V levém podokně vyberte **Microsoft. EntityFrameworkCore. SqlServer** .
* Zaškrtněte políčko **projekt** v pravém podokně a pak vyberte **nainstalovat**.
* Pomocí předchozích pokynů přidejte balíček NuGet **Microsoft. EntityFrameworkCore. inMemory** .

![Správce balíčků NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a>Přidání kontextu databáze TodoContext

* Klikněte pravým tlačítkem na *Models* složku a vyberte **Přidat**  >  **třídu**. Pojmenujte třídu *TodoContext* a klikněte na **Přidat**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

* Přidejte `TodoContext` do *Models* složky třídu.

---

* Zadejte následující kód:

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>Registrace kontextu databáze

V ASP.NET Core musí být služby, jako je například kontext databáze, registrovány s kontejnerem [vkládání závislostí (di)](xref:fundamentals/dependency-injection) . Kontejner poskytuje službu pro řadiče.

Aktualizujte *Startup.cs* o následující zvýrazněný kód:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

Předcházející kód:

* Odebere nepoužívané `using` deklarace.
* Přidá kontext databáze do kontejneru DI.
* Určuje, že kontext databáze bude používat databázi v paměti.

## <a name="scaffold-a-controller"></a>Generování uživatelského rozhraní kontroleru

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klikněte pravým tlačítkem na složku *řadiče* .
* Vyberte **Přidat** > **novou vygenerované položky**.
* Vyberte možnost **kontroler API s akcemi, pomocí Entity Framework**a pak vyberte **Přidat**.
* V dialogovém okně **Přidání kontroleru rozhraní API pomocí akcí Entity Framework** :

  * V **třídě modelu**vyberte **TodoItem (TodoApi. Models )** .
  * Ve **třídě Context data**vyberte **TodoContext (TodoApi. Models )** .
  * Vyberte **Přidat**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

Spusťte následující příkazy:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

Předchozí příkazy:

* Přidejte balíčky NuGet vyžadované pro generování uživatelského rozhraní.
* Nainstaluje modul generování uživatelského rozhraní ( `dotnet-aspnet-codegenerator` ).
* Generování uživatelského rozhraní `TodoItemsController` .

---

Generovaný kód:

* Označí třídu [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributem. Tento atribut označuje, že kontroler reaguje na požadavky webového rozhraní API. Informace o konkrétním chování, které atribut povoluje, naleznete v tématu <xref:web-api/index> .
* Pomocí DI vloží kontext databáze ( `TodoContext` ) do kontroleru. Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v kontroleru.

Šablony ASP.NET Core pro:

* Řadiče se zobrazeními jsou zahrnuta `[action]` v šabloně trasy.
* Řadiče API neobsahují `[action]` šablonu trasy.

Pokud `[action]` token není v šabloně směrování, název [Akce](xref:mvc/controllers/routing#action) je vyloučený z trasy. To znamená, že název přidružené metody akce se ve shodě trasy nepoužívá.

## <a name="examine-the-posttodoitem-create-method"></a>Projděte si metodu PostTodoItem Create.

Nahraďte příkaz return v `PostTodoItem` operátoru k použití operátoru [nameof](/dotnet/csharp/language-reference/operators/nameof) :

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

Předchozí kód je metoda HTTP POST, jak je označena [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atributem. Metoda získá hodnotu položky k seřízení z těla požadavku HTTP.

Další informace najdete v tématu [Směrování atributů s atributy http [příkaz]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

<xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>Metoda:

* Pokud je úspěšná, vrátí stavový kód HTTP 201. HTTP 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru.
* Přidá hlavičku [umístění](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) k odpovědi. `Location`Záhlaví Určuje [identifikátor URI](https://developer.mozilla.org/docs/Glossary/URI) nově vytvořené položky. Další informace najdete v tématu [10.2.2 201 vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* Odkazuje na `GetTodoItem` akci vytvoření `Location` identifikátoru URI hlavičky. `nameof`Klíčové slovo jazyka C# se používá k zamezení hardwarového kódování názvu akce ve `CreatedAtAction` volání.

### <a name="install-postman"></a>Nainstalovat post

V tomto kurzu se používá post k testování webového rozhraní API.

* Nainstalovat [post](https://www.getpostman.com/downloads/)
* Spusťte webovou aplikaci.
* Spusťte post.
* Zakázat **ověřování certifikátu SSL**
  * V **File** > **Nastavení** souboru (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.
    > [!WARNING]
    > Po otestování kontroleru znovu povolte ověření certifikátu SSL.

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a>PostTodoItem testu s použitím post

* Vytvoří novou žádost.
* Nastavte metodu HTTP na `POST` .
* Nastavte identifikátor URI na `https://localhost:<port>/api/TodoItems` . Například, `https://localhost:5001/api/TodoItems`.
* Vyberte kartu **tělo** .
* Vyberte **nezpracovaný** přepínač.
* Nastavte typ na **JSON (Application/JSON)**.
* Do textu žádosti zadejte JSON pro položku úkolů:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* Vyberte **Poslat**.

  ![Poslat pomocí žádosti o vytvoření](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a>Otestování identifikátoru URI hlaviček umístění pomocí metody post

* V podokně **odpověď** vyberte kartu **hlavičky** .
* Zkopírujte hodnotu hlavičky **umístění** :

  ![Karta hlavičky v konzole pro odesílání](first-web-api/_static/3/create.png)

* Nastavte metodu HTTP na `GET` .
* Nastavte identifikátor URI na `https://localhost:<port>/api/TodoItems/1` . Například, `https://localhost:5001/api/TodoItems/1`.
* Vyberte **Poslat**.

## <a name="examine-the-get-methods"></a>Projděte si metody GET.

Tyto metody implementují dva koncové body GET:

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

Otestujte aplikaci voláním dvou koncových bodů z prohlížeče nebo po odeslání. Příklad:

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

Odpověď podobná následující je vytvořena voláním metody `GetTodoItems` :

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a>Test Get s použitím metody post

* Vytvoří novou žádost.
* Nastavte metodu HTTP na **Get**.
* Nastavte identifikátor URI žádosti na `https://localhost:<port>/api/TodoItems` . Například, `https://localhost:5001/api/TodoItems`.
* Nastavte v příspěvku **dva zobrazení podokna** .
* Vyberte **Poslat**.

Tato aplikace používá databázi v paměti. Pokud se aplikace zastaví a spustí, předchozí požadavek GET nebude vracet žádná data. Pokud se nevrátí žádná data, [odešlete](#post) data do aplikace.

## <a name="routing-and-url-paths"></a>Směrování a cesty URL

[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)Atribut označuje metodu, která reaguje na požadavek HTTP GET. Cesta URL pro každou metodu je konstruována takto:

* Začněte s řetězcem šablony v `Route` atributu kontroleru:

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* Nahraďte `[controller]` názvem kontroleru, který je podle konvence názvem třídy kontroleru minus přípona Controller. V této ukázce je název třídy kontroleru **TodoItems**Controller, takže název kontroleru je "TodoItems". [Směrování](xref:mvc/controllers/routing) ASP.NET Core rozlišuje malá a velká písmena.
* Pokud `[HttpGet]` má atribut směrovací šablonu (například `[HttpGet("products")]` ), přidejte ji k cestě. Tato ukázka nepoužívá šablonu. Další informace najdete v tématu [Směrování atributů s atributy http [příkaz]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

V následující `GetTodoItem` metodě `"{id}"` je proměnná zástupného symbolu pro jedinečný identifikátor položky k provedení. Při `GetTodoItem` vyvolání je hodnota `"{id}"` v adrese URL poskytnuta metodě v `id` parametru.

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>Vrácené hodnoty 

Návratový typ `GetTodoItems` `GetTodoItem` metod a je [ActionResult \<T> typ](xref:web-api/action-return-types#actionresultt-type). ASP.NET Core automaticky serializovat objekt do formátu [JSON](https://www.json.org/) a zapíše JSON do textu zprávy s odpovědí. Kód odpovědi pro tento návratový typ je 200, za předpokladu, že neexistují žádné neošetřené výjimky. Neošetřené výjimky jsou přeloženy na 5xx chyby.

`ActionResult` návratové typy mohou představovat široké spektrum stavových kódů HTTP. Například `GetTodoItem` může vracet dvě různé stavové hodnoty:

* Pokud žádná položka neodpovídá požadovanému ID, vrátí metoda <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> kód chyby 404.
* V opačném případě metoda vrátí 200 text odpovědi JSON. `item`Výsledkem vrácení výsledků odpovědi HTTP 200.

## <a name="the-puttodoitem-method"></a>Metoda PutTodoItem

Prohlédněte si metodu `PutTodoItem`:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

`PutTodoItem` se podobá `PostTodoItem` , s tím rozdílem, že používá Put http. Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). V souladu se specifikací HTTP vyžaduje požadavek PUT klientovi, aby odesílal celou aktualizovanou entitu, a ne jenom změny. K podpoře částečných aktualizací použijte [opravu http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).

Pokud se zobrazí chyba při volání `PutTodoItem` , `GET` zajistěte, aby byla položka v databázi.

### <a name="test-the-puttodoitem-method"></a>Test metody PutTodoItem

Tato ukázka používá databázi v paměti, která musí být inicializována při každém spuštění aplikace. Před provedením volání PUT musí existovat položka v databázi. Voláním metody GET zajistěte, aby byla položka v databázi před provedením volání PUT.

Aktualizujte položku úkolů s ID = 1 a nastavte její název na "rybí ryby":

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

Na následujícím obrázku je znázorněná aktualizace po odeslání:

![Konzola pro publikování zobrazující 204 (bez obsahu)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a>Metoda DeleteTodoItem

Prohlédněte si metodu `DeleteTodoItem`:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a>Test metody DeleteTodoItem

Odstranění položky úkolů pomocí metody post:

* Nastavte metodu na `DELETE` .
* Nastavte identifikátor URI objektu, který má být odstraněn (například `https://localhost:5001/api/TodoItems/1` ).
* Vyberte **Poslat**.

<a name="over-post"></a>

## <a name="prevent-over-posting"></a>Zabránit navýšení příspěvků

V současné době ukázková aplikace zveřejňuje celý `TodoItem` objekt. Produkční aplikace obvykle omezují zadaná data a vracejí je pomocí podmnožiny modelu. Je to několik důvodů na pozadí a zabezpečení je hlavní. Podmnožina modelu je obvykle označována jako objekt Přenos dat (DTO), vstupní model nebo model zobrazení. **DTO** se používá v tomto článku.

DTO se dá použít k těmto akcím:

* Zabránit přeúčtování.
* Skrytí vlastností, které klienti nemají zobrazovat.
* Vynechejte některé vlastnosti, aby se snížila velikost datové části.
* Ploché grafy objektů, které obsahují vnořené objekty. Ploché grafy objektů můžou být pro klienty pohodlnější.

Chcete-li předvést DTO přístup, aktualizujte `TodoItem` třídu tak, aby obsahovala tajné pole:

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

Pole tajného klíče musí být z této aplikace skryté, ale aplikace pro správu může tuto možnost vystavit.

Ověřte, že můžete publikovat a získat pole tajného klíče.

Vytvoření modelu DTO:

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

Aktualizujte, `TodoItemsController` aby se použil `TodoItemDTO` :

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

Ověřte, že nemůžete publikovat nebo získat pole tajného kódu.

## <a name="call-the-web-api-with-javascript"></a>Volání webového rozhraní API pomocí JavaScriptu

Viz [kurz: volání ASP.NET Core webového rozhraní API pomocí JavaScriptu](xref:tutorials/web-api-javascript).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte projekt webového rozhraní API.
> * Přidejte třídu modelu a kontext databáze.
> * Přidejte kontroler.
> * Přidejte metody CRUD.
> * Nakonfigurujte směrování a cesty URL.
> * Zadejte návratové hodnoty.
> * Zavolejte webové rozhraní API pomocí post.
> * Zavolejte webové rozhraní API pomocí JavaScriptu.

Na konci máte webové rozhraní API, které může spravovat položky "k tomu" uložené v relační databázi.

## <a name="overview-21"></a>Přehled 2,1

V tomto kurzu se vytvoří následující rozhraní API:

|Rozhraní API | Popis | Text požadavku | Text odpovědi |
|--- | ---- | ---- | ---- |
|ZÍSKAT/api/TodoItems | Získat všechny položky úkolů | Žádné | Pole položek úkolů|
|ZÍSKAT/api/TodoItems/{id} | Získat položku podle ID | Žádné | Položka úkolů|
|PŘÍSPĚVEK/api/TodoItems | Přidat novou položku | Položka úkolů | Položka úkolů |
|Vložit/api/TodoItems/{id} | Aktualizovat existující položku &nbsp; | Položka úkolů | Žádné |
|Odstranit/api/TodoItems/{id} &nbsp;&nbsp; | Odstranění položky &nbsp;&nbsp; | Žádné | Žádné|

V následujícím diagramu vidíte návrh aplikace.

![Klient je reprezentován polem vlevo. Odešle požadavek a přijme odpověď z aplikace, pole nakreslené na pravé straně. V poli aplikace tři čtverečky reprezentují kontroler, model a vrstvu přístupu k datům. Požadavek přichází do kontroleru aplikace a operace čtení/zápisu probíhají mezi kontrolérem a vrstvou přístupu k datům. Model je serializován a vrácen klientovi v odpovědi.](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a>Požadavky 2,1

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a>Vytvoření webového projektu 2,1

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V nabídce **soubor** vyberte možnost **Nový** > **projekt**.
* Vyberte šablonu **ASP.NET Core webové aplikace** a klikněte na tlačítko **Další**.
* Pojmenujte projekt *TodoApi* a klikněte na **vytvořit**.
* V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 2,2** . Vyberte šablonu **rozhraní API** a klikněte na **vytvořit**. **Nevybírejte možnost** **Povolit podporu Docker**.

![Dialogové okno VS New Project](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Změňte adresáře ( `cd` ) na složku, která bude obsahovat složku projektu.
* Spusťte následující příkazy:

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  Tyto příkazy vytvoří nový projekt webového rozhraní API a otevřou novou instanci Visual Studio Code v nové složce projektu.

* Pokud se zobrazí dialogové okno s dotazem, zda chcete přidat požadované prostředky do projektu, vyberte možnost **Ano**.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Vyberte **soubor** > **nové řešení**.

  ![macOS nové řešení](first-web-api-mac/_static/sln.png)

* V Visual Studio pro Mac starší než verze 8,6 Vyberte rozhraní **.NET Core**  >  **App**  >  **API**  >  **Next**. V části verze 8,6 nebo novější vyberte **webové a konzolové**  >  **App**  >  **rozhraní API**aplikace  >  **Next**.
  
* V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** vyberte nejnovější **verzi rozhraní .NET**Core 2. x. Vyberte **Další**.

* Jako **název projektu** zadejte *TodoApi* a pak vyberte **vytvořit**.

  ![Dialogové okno Konfigurace](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a>Test rozhraní API 2,1

Šablona projektu vytvoří `values` rozhraní API. Voláním `Get` metody z prohlížeče otestujete aplikaci.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci. Visual Studio spustí prohlížeč a přejde na `https://localhost:<port>/api/values` místo, kde `<port>` je náhodně zvolené číslo portu.

Pokud se zobrazí dialogové okno s dotazem, jestli byste měli důvěřovat certifikátu IIS Express, vyberte **Ano**. V dialogovém okně **Upozornění zabezpečení** , které se zobrazí jako další, vyberte **Ano**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci. V prohlížeči přejdete na následující adresu URL: `https://localhost:5001/api/values` .

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Vyberte **Spustit**  >  **ladění** a spusťte aplikaci. Visual Studio pro Mac spustí prohlížeč a přejde na `https://localhost:<port>` místo, kde `<port>` je náhodně zvolené číslo portu. Vrátí se chyba HTTP 404 (Nenalezeno). Připojit `/api/values` k adrese URL (změnit adresu URL na `https://localhost:<port>/api/values` )

---

Vrátí se následující JSON:

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a>Přidat třídu modelu 2,1

*Model* je sada tříd, které reprezentují data, která aplikace spravuje. Model pro tuto aplikaci je jediná `TodoItem` Třída.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt. Vyberte **Přidat**  >  **novou složku**. Pojmenujte složku *Models* .

* Klikněte pravým tlačítkem na *Models* složku a vyberte **Přidat**  >  **třídu**. Pojmenujte třídu *TodoItem* a vyberte **Přidat**.

* Kód šablony nahraďte následujícím kódem:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Přidejte složku s názvem *Models* .

* Přidejte `TodoItem` třídu do *Models* složky s následujícím kódem:

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Klikněte pravým tlačítkem na projekt. Vyberte **Přidat**  >  **novou složku**. Pojmenujte složku *Models* .

  ![Nová složka](first-web-api-mac/_static/folder.png)

* Klikněte pravým tlačítkem na *Models* složku a vyberte **Přidat** > **nový soubor** > **Obecná** > **prázdná třída**.

* Pojmenujte třídu *TodoItem*a potom klikněte na **Nový**.

* Kód šablony nahraďte následujícím kódem:

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

`Id`Vlastnost funguje jako jedinečný klíč v relační databázi.

Třídy modelu mohou jít kdekoli v projektu, ale *Models* Složka je používána konvencí.

## <a name="add-a-database-context-21"></a>Přidání kontextu databáze 2,1

*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro datový model. Tato třída je vytvořena odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klikněte pravým tlačítkem na *Models* složku a vyberte **Přidat**  >  **třídu**. Pojmenujte třídu *TodoContext* a klikněte na **Přidat**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

* Přidejte `TodoContext` do *Models* složky třídu.

---

* Kód šablony nahraďte následujícím kódem:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a>Registrace kontextu databáze 2,1

V ASP.NET Core musí být služby, jako je například kontext databáze, registrovány s kontejnerem [vkládání závislostí (di)](xref:fundamentals/dependency-injection) . Kontejner poskytuje službu pro řadiče.

Aktualizujte *Startup.cs* o následující zvýrazněný kód:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

Předcházející kód:

* Odebere nepoužívané `using` deklarace.
* Přidá kontext databáze do kontejneru DI.
* Určuje, že kontext databáze bude používat databázi v paměti.

## <a name="add-a-controller-21"></a>Přidat kontroler 2,1

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klikněte pravým tlačítkem na složku *řadiče* .
* Vyberte možnost **Přidat** > **novou položku**.
* V dialogovém okně **Přidat novou položku** vyberte šablonu **Třída kontroleru rozhraní API** .
* Pojmenujte třídu *TodoController*a vyberte **Přidat**.

  ![Dialogové okno Přidat novou položku s řadičem ve vyhledávacím poli a vybraným kontrolérem webového rozhraní API](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

* Ve složce *Controllers* vytvořte třídu s názvem `TodoController` .

---

* Kód šablony nahraďte následujícím kódem:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

Předcházející kód:

* Definuje třídu kontroleru rozhraní API bez metod.
* Označí třídu [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributem. Tento atribut označuje, že kontroler reaguje na požadavky webového rozhraní API. Informace o konkrétním chování, které atribut povoluje, naleznete v tématu <xref:web-api/index> .
* Pomocí DI vloží kontext databáze ( `TodoContext` ) do kontroleru. Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v kontroleru.
* Přidá položku s názvem `Item1` do databáze, pokud je databáze prázdná. Tento kód je v konstruktoru, takže se spustí pokaždé, když se vytvoří nový požadavek HTTP. Pokud odstraníte všechny položky, konstruktor se znovu vytvoří `Item1` při příštím volání metody rozhraní API. Takže může vypadat, že odstranění nefungovalo, pokud skutečně fungovalo.

## <a name="add-get-methods-21"></a>Přidat metody Get 2,1

Chcete-li poskytnout rozhraní API, které načítá položky úkolů, přidejte do třídy následující metody `TodoController` :

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

Tyto metody implementují dva koncové body GET:

* `GET /api/todo`
* `GET /api/todo/{id}`

Pokud je pořád spuštěná, zastavte aplikaci. Pak ji znovu spusťte, aby obsahovala nejnovější změny.

Otestujte aplikaci voláním dvou koncových bodů z prohlížeče. Příklad:

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

Následující odpověď protokolu HTTP je vytvořena voláním metody `GetTodoItems` :

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a>Cesty směrování a adresy URL 2,1

[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)Atribut označuje metodu, která reaguje na požadavek HTTP GET. Cesta URL pro každou metodu je konstruována takto:

* Začněte s řetězcem šablony v `Route` atributu kontroleru:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* Nahraďte `[controller]` názvem kontroleru, který je podle konvence názvem třídy kontroleru minus přípona Controller. V této ukázce je názvem třídy kontroleru kontroler **TODO**, takže název kontroleru je "todo". [Směrování](xref:mvc/controllers/routing) ASP.NET Core rozlišuje malá a velká písmena.
* Pokud `[HttpGet]` má atribut směrovací šablonu (například `[HttpGet("products")]` ), přidejte ji k cestě. Tato ukázka nepoužívá šablonu. Další informace najdete v tématu [Směrování atributů s atributy http [příkaz]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

V následující `GetTodoItem` metodě `"{id}"` je proměnná zástupného symbolu pro jedinečný identifikátor položky k provedení. Při `GetTodoItem` vyvolání je hodnota `"{id}"` v adrese URL poskytnuta metodě v `id` parametru.

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a>Návratové hodnoty 2,1

Návratový typ `GetTodoItems` `GetTodoItem` metod a je [ActionResult \<T> typ](xref:web-api/action-return-types#actionresultt-type). ASP.NET Core automaticky serializovat objekt do formátu [JSON](https://www.json.org/) a zapíše JSON do textu zprávy s odpovědí. Kód odpovědi pro tento návratový typ je 200, za předpokladu, že neexistují žádné neošetřené výjimky. Neošetřené výjimky jsou přeloženy na 5xx chyby.

`ActionResult` návratové typy mohou představovat široké spektrum stavových kódů HTTP. Například `GetTodoItem` může vracet dvě různé stavové hodnoty:

* Pokud žádná položka neodpovídá požadovanému ID, vrátí metoda <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> kód chyby 404.
* V opačném případě metoda vrátí 200 text odpovědi JSON. `item`Výsledkem vrácení výsledků odpovědi HTTP 200.

## <a name="test-the-gettodoitems-method-21"></a>Otestujte metodu GetTodoItems 2,1

V tomto kurzu se používá post k testování webového rozhraní API.

* Nainstalujte [post](https://www.getpostman.com/downloads/).
* Spusťte webovou aplikaci.
* Spusťte post.
* Zakáže **ověřování certifikátu SSL**.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V **File** > **Nastavení** souboru (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

* V **Postman**možnosti  >  **předvolba** post (karta**Obecné** ) zakažte **ověřování certifikátu SSL**. Případně vyberte klíče a vyberte **Nastavení**a pak zakažte ověřování certifikátu SSL.

---
  
> [!WARNING]
> Po otestování kontroleru znovu povolte ověření certifikátu SSL.

* Vytvoří novou žádost.
  * Nastavte metodu HTTP na **Get**.
  * Nastavte identifikátor URI žádosti na `https://localhost:<port>/api/todo` . Například, `https://localhost:5001/api/todo`.
* Nastavte v příspěvku **dva zobrazení podokna** .
* Vyberte **Poslat**.

![Odeslání pomocí žádosti Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a>Přidejte metodu create 2,1

Přidejte následující `PostTodoItem` metodu do *Controllers/TodoController. cs*: 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

Předchozí kód je metoda HTTP POST, jak je označena [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atributem. Metoda získá hodnotu položky k seřízení z těla požadavku HTTP.

`CreatedAtAction`Metoda:

* Vrátí stavový kód HTTP 201, pokud bylo úspěšné. HTTP 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru.
* Přidá `Location` hlavičku k odpovědi. `Location`Záhlaví Určuje identifikátor URI nově vytvořené položky. Další informace najdete v tématu [10.2.2 201 vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* Odkazuje na `GetTodoItem` akci vytvoření `Location` identifikátoru URI hlavičky. `nameof`Klíčové slovo jazyka C# se používá k zamezení hardwarového kódování názvu akce ve `CreatedAtAction` volání.

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a>Otestujte metodu PostTodoItem 2,1

* Sestavte projekt.
* V části post nastavte metodu HTTP na `POST` .
* Nastavte identifikátor URI na `https://localhost:<port>/api/TodoItem` . Například, `https://localhost:5001/api/TodoItem`.
* Vyberte kartu **tělo** .
* Vyberte **nezpracovaný** přepínač.
* Nastavte typ na **JSON (Application/JSON)**.
* Do textu žádosti zadejte JSON pro položku úkolů:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* Vyberte **Poslat**.

  ![Poslat pomocí žádosti o vytvoření](first-web-api/_static/create.png)

  Pokud se zobrazí chyba metoda 405 není povolená, je pravděpodobné, že po přidání metody nebude projekt zkompilován `PostTodoItem` .

### <a name="test-the-location-header-uri-21"></a>Otestování identifikátoru URI hlavičky umístění 2,1

* V podokně **odpověď** vyberte kartu **hlavičky** .
* Zkopírujte hodnotu hlavičky **umístění** :

  ![Karta hlavičky v konzole pro odesílání](first-web-api/_static/pmc2.png)

* Nastavte metodu, která má být ZÍSKÁNa.
* Nastavte identifikátor URI na  `https://localhost:<port>/api/TodoItems/2` .Například  `https://localhost:5001/api/TodoItems/2` .
* Vyberte **Poslat**.

## <a name="add-a-puttodoitem-method-21"></a>Přidat metodu PutTodoItem 2,1

Přidejte následující `PutTodoItem` metodu:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

`PutTodoItem` se podobá `PostTodoItem` , s tím rozdílem, že používá Put http. Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). V souladu se specifikací HTTP vyžaduje požadavek PUT klientovi, aby odesílal celou aktualizovanou entitu, a ne jenom změny. K podpoře částečných aktualizací použijte [opravu http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).

Pokud se zobrazí chyba při volání `PutTodoItem` , `GET` zajistěte, aby byla položka v databázi.

### <a name="test-the-puttodoitem-method-21"></a>Otestujte metodu PutTodoItem 2,1

Tato ukázka používá databázi v paměti, která musí být inicializována při každém spuštění aplikace. Před provedením volání PUT musí existovat položka v databázi. Voláním metody GET zajistěte, aby byla položka v databázi před provedením volání PUT.

Aktualizujte položku úkolů s ID = 1 a nastavte její název na "rybí ryby":

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

Na následujícím obrázku je znázorněná aktualizace po odeslání:

![Konzola pro publikování zobrazující 204 (bez obsahu)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a>Přidat metodu DeleteTodoItem 2,1

Přidejte následující `DeleteTodoItem` metodu:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

`DeleteTodoItem`Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).

### <a name="test-the-deletetodoitem-method-21"></a>Otestujte metodu DeleteTodoItem 2,1

Odstranění položky úkolů pomocí metody post:

* Nastavte metodu na `DELETE` .
* Nastavte identifikátor URI objektu, který má být odstraněn (například `https://localhost:5001/api/todo/1` ).
* Vyberte **Poslat**.

Ukázková aplikace umožňuje odstranit všechny položky. Když je však poslední položka odstraněna, vytvoří se nový konstruktor třídy modelu při příštím volání rozhraní API.

## <a name="call-the-web-api-with-javascript-21"></a>Volání webového rozhraní API pomocí JavaScriptu 2,1

V této části se přidá stránka HTML, která pomocí JavaScriptu volá webové rozhraní API. jQuery inicializuje požadavek. JavaScript aktualizuje stránku s podrobnostmi z odpovědi webového rozhraní API.

Nakonfigurujte aplikaci tak, aby [sloužila pro statické soubory](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) , a [Povolte výchozí mapování souborů](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) aktualizací *Startup.cs* pomocí následujícího zvýrazněného kódu:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

Vytvořte složku *wwwroot* v adresáři projektu.

Do adresáře *wwwroot* přidejte soubor HTML s názvem *index.html* . Nahraďte jeho obsah následujícím kódem:

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

Do adresáře *wwwroot* přidejte soubor JavaScriptu s názvem *site.js* . Jeho obsah nahraďte následujícím kódem.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

Pro místní testování stránky HTML může být nutné změnit nastavení spouštění ASP.NET Core projektu:

* Otevřete *Properties\launchSettings.js*.
* Odeberte `launchUrl` vlastnost, která vynutí otevření aplikace na *index.html* &mdash; výchozího souboru projektu.

Tato ukázka volá všechny metody CRUD webového rozhraní API. Následují vysvětlení volání rozhraní API.

### <a name="get-a-list-of-to-do-items-21"></a>Získat seznam položek úkolů 2,1

jQuery pošle požadavek HTTP GET do webového rozhraní API, které vrátí JSON představující pole položek úkolů. `success`Funkce zpětného volání je vyvolána, pokud je požadavek úspěšný. Ve zpětném volání je DOM aktualizován pomocí informací o tom.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a>Přidat položku úkolů 2,1

jQuery pošle požadavek HTTP POST s položkou k žádosti v textu požadavku. `accepts`Možnosti a `contentType` jsou nastaveny na `application/json` zadání typu média, který se přijímá a odesílá. Položka úkolů se převede na JSON pomocí [JSON. stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify). Když rozhraní API vrátí stavový kód úspěšné, `getData` funkce se vyvolá, aby se aktualizovala tabulka HTML.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a>Aktualizace položky úkolů 2,1

Aktualizace položky úkolů je podobná přidání. `url`Změny pro přidání jedinečného identifikátoru položky a `type` jsou `PUT` .

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a>Odstranění položky úkolů 2,1

Odstranění položky úkolů je provedeno nastavením `type` při volání AJAX na `DELETE` a zadáním jedinečného identifikátoru položky v adrese URL.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a>Přidání podpory ověřování do webového rozhraní API 2,1

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a>Další zdroje 2,1

[Zobrazit nebo stáhnout vzorový kód pro tento kurz](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples). Viz [Jak stáhnout](xref:index#how-to-download-a-sample).

Další informace naleznete v následujících zdrojích:

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [Verze YouTube tohoto kurzu](https://www.youtube.com/watch?v=TTkhEyGBfAk)

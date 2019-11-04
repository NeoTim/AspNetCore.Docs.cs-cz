---
title: 'Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core'
author: rick-anderson
description: Naučte se vytvářet webové rozhraní API pomocí ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 09/29/2019
uid: tutorials/first-web-api
ms.openlocfilehash: abb55ea12583374639f28945037cb6aa41a5a32d
ms.sourcegitcommit: 77c8be22d5e88dd710f42c739748869f198865dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2019
ms.locfileid: "73427043"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a>Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core

Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Jan Wasson](https://github.com/mikewasson)

V tomto kurzu se naučíte základy vytváření webového rozhraní API pomocí ASP.NET Core.

::: moniker range=">= aspnetcore-3.0"

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

|rozhraní API | Popis | Text žádosti | Tělo odpovědi |
|--- | ---- | ---- | ---- |
|ZÍSKAT/api/TodoItems | Získat všechny položky úkolů | Žádné | Pole položek úkolů|
|ZÍSKAT/api/TodoItems/{id} | Získat položku podle ID | Žádné | Položka úkolů|
|PŘÍSPĚVEK/api/TodoItems | Přidat novou položku | Položka úkolů | Položka úkolů |
|Vložit/api/TodoItems/{id} | Aktualizovat existující &nbsp; položky | Položka úkolů | Žádné |
|Odstranit &nbsp;/api/TodoItems/{id} &nbsp; | Odstranění položky &nbsp; &nbsp; | Žádné | Žádné|

V následujícím diagramu vidíte návrh aplikace.

![Klient je reprezentován polem vlevo. Odešle požadavek a přijme odpověď z aplikace, pole nakreslené na pravé straně. V poli aplikace tři čtverečky reprezentují kontroler, model a vrstvu přístupu k datům. Požadavek přichází do kontroleru aplikace a operace čtení/zápisu probíhají mezi kontrolérem a vrstvou přístupu k datům. Model je serializován a vrácen klientovi v odpovědi.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-project"></a>Vytvoření webového projektu

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* V nabídce **soubor** vyberte **Nový** > **projekt**.
* Vyberte šablonu **ASP.NET Core webové aplikace** a klikněte na tlačítko **Další**.
* Pojmenujte projekt *TodoApi* a klikněte na **vytvořit**.
* V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 3,0** . Vyberte šablonu **rozhraní API** a klikněte na **vytvořit**.

![Dialogové okno VS New Project](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Změňte adresáře (`cd`) na složku, která bude obsahovat složku projektu.
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

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Vyberte **soubor** > **nové řešení**.

  ![macOS nové řešení](first-web-api-mac/_static/sln.png)

* V **části rozhraní .NET Core** > **App** > **API** > **Další**.

  ![dialog pro nový projekt v macOS](first-web-api-mac/_static/1.png)
  
* V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** vyberte **cílovou architekturu** * *.NET Core 3,0*.

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

Šablona projektu vytvoří rozhraní `WeatherForecast` API. Voláním metody `Get` z prohlížeče otestujete aplikaci.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci. Visual Studio spustí prohlížeč a přejde na `https://localhost:<port>/WeatherForecast`, kde `<port>` je náhodně zvolené číslo portu.

Pokud se zobrazí dialogové okno s dotazem, jestli byste měli důvěřovat certifikátu IIS Express, vyberte **Ano**. V dialogovém okně **Upozornění zabezpečení** , které se zobrazí jako další, vyberte **Ano**.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci. V prohlížeči přejdete na následující adresu URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Vyberte **spustit**  > **Spustit ladění** a spusťte tak aplikaci. Visual Studio pro Mac spustí prohlížeč a přejde na `https://localhost:<port>`, kde `<port>` je náhodně zvolené číslo portu. Vrátí se chyba HTTP 404 (Nenalezeno). Přidejte `/WeatherForecast` k adrese URL (změňte adresu URL na `https://localhost:<port>/WeatherForecast`).

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

*Model* je sada tříd, které reprezentují data, která aplikace spravuje. Model pro tuto aplikaci je jediná třída `TodoItem`.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt. Vyberte **přidat**  > **novou složku**. Pojmenujte *modely*složek.

* Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat**  > **třídy**. Pojmenujte třídu *TodoItem* a vyberte **Přidat**.

* Kód šablony nahraďte následujícím kódem:

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Přidejte složku s názvem *modely*.

* Přidejte třídu `TodoItem` do složky *modely* pomocí následujícího kódu:

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Klikněte pravým tlačítkem na projekt. Vyberte **přidat**  > **novou složku**. Pojmenujte *modely*složek.

  ![Nová složka](first-web-api-mac/_static/folder.png)

* Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **nový soubor** > **Obecné** > **prázdná třída**.

* Pojmenujte třídu *TodoItem*a potom klikněte na **Nový**.

* Kód šablony nahraďte následujícím kódem:

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

Vlastnost `Id` funguje jako jedinečný klíč v relační databázi.

Třídy modelu mohou jít kdekoli v projektu, ale složka *modely* je používána konvencí.

## <a name="add-a-database-context"></a>Přidání kontextu databáze

*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro datový model. Tato třída je vytvořena odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a>Přidat Microsoft. EntityFrameworkCore. SqlServer

* V nabídce **nástroje** vyberte **správce balíčků NuGet > spravovat balíčky NuGet pro řešení**.
* Vyberte kartu **Procházet** a potom do vyhledávacího pole zadejte **Microsoft. EntityFrameworkCore. SqlServer** .
* V levém podokně vyberte **Microsoft. EntityFrameworkCore. SqlServer** .
* Zaškrtněte políčko **projekt** v pravém podokně a pak vyberte **nainstalovat**.
* Pomocí předchozích pokynů přidejte balíček NuGet `Microsoft.EntityFrameworkCore.InMemory`.

![Správce balíčků NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a>Přidání kontextu databáze TodoContext

* Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat**  > **třídy**. Pojmenujte třídu *TodoContext* a klikněte na **Přidat**.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

* Přidejte třídu `TodoContext` do složky *modely* .

---

* Zadejte následující kód:

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>Registrace kontextu databáze

V ASP.NET Core musí být služby, jako je například kontext databáze, registrovány s kontejnerem [vkládání závislostí (di)](xref:fundamentals/dependency-injection) . Kontejner poskytuje službu pro řadiče.

Aktualizujte *Startup.cs* o následující zvýrazněný kód:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

Předchozí kód:

* Odebere nepoužívané deklarace `using`.
* Přidá kontext databáze do kontejneru DI.
* Určuje, že kontext databáze bude používat databázi v paměti.

## <a name="scaffold-a-controller"></a>Generování uživatelského rozhraní kontroleru

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klikněte pravým tlačítkem na složku *řadiče* .
* Vyberte **přidat** > **Nová vygenerovaná položka**.
* Vyberte možnost **kontroler API s akcemi, pomocí Entity Framework**a pak vyberte **Přidat**.
* V dialogovém okně **Přidání kontroleru rozhraní API pomocí akcí Entity Framework** :

  * V **třídě modelu**vyberte **TodoItem (TodoApi. Models)** .
  * Ve **třídě Context data**vyberte **TodoContext (TodoApi. Models)** .
  * Vyberte **Přidat**.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

Spusťte následující příkazy:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

Předchozí příkazy:

* Přidejte balíčky NuGet vyžadované pro generování uživatelského rozhraní.
* Nainstaluje modul generování uživatelského rozhraní (`dotnet-aspnet-codegenerator`).
* `TodoItemsController`generování uživatelského rozhraní.

---

Generovaný kód:

* Definuje třídu kontroleru rozhraní API bez metod.
* Upraví třídu atributem [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) . Tento atribut označuje, že kontroler reaguje na požadavky webového rozhraní API. Informace o konkrétním chování, které atribut povoluje, naleznete v tématu <xref:web-api/index>.
* Pomocí DI vloží kontext databáze (`TodoContext`) do kontroleru. Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v kontroleru.

## <a name="examine-the-posttodoitem-create-method"></a>Projděte si metodu PostTodoItem Create.

Nahraďte příkaz return v `PostTodoItem` pro použití operátoru [nameof](/dotnet/csharp/language-reference/operators/nameof) :

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

Předchozí kód je metoda HTTP POST, která je označena atributem [[HTTPPOST]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) . Metoda získá hodnotu položky k seřízení z těla požadavku HTTP.

Metoda <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:

* Pokud je úspěšná, vrátí stavový kód HTTP 201. HTTP 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru.
* Přidá hlavičku [umístění](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) k odpovědi. Hlavička `Location` Určuje [identifikátor URI](https://developer.mozilla.org/docs/Glossary/URI) nově vytvořené položky. Další informace najdete v tématu [10.2.2 201 vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* Odkazuje na akci `GetTodoItem` pro vytvoření identifikátoru URI `Location` hlavičky. Klíčové C# slovo `nameof` slouží k tomu, aby se předešlo zakódování názvu akce ve volání `CreatedAtAction`.

### <a name="install-postman"></a>Nainstalovat post

V tomto kurzu se používá post k testování webového rozhraní API.

* Nainstalovat [post](https://www.getpostman.com/downloads/)
* Spusťte webovou aplikaci.
* Spusťte post.
* Zakázat **ověřování certifikátu SSL**
* V **Nastavení** **souboru** > (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.
    > [!WARNING]
    > Po otestování kontroleru znovu povolte ověření certifikátu SSL.

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a>PostTodoItem testu s použitím post

* Vytvoří novou žádost.
* Nastavte metodu HTTP na `POST`.
* Vyberte kartu **tělo** .
* Vyberte **nezpracovaný** přepínač.
* Nastavte typ na **JSON (Application/JSON)** .
* Do textu žádosti zadejte JSON pro položku úkolů:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* Vyberte **Odeslat**.

  ![Poslat pomocí žádosti o vytvoření](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a>Otestování identifikátoru URI hlavičky umístění

* V podokně **odpověď** vyberte kartu **hlavičky** .
* Zkopírujte hodnotu hlavičky **umístění** :

  ![Karta hlavičky v konzole pro odesílání](first-web-api/_static/3/create.png)

* Nastavte metodu, která má být ZÍSKÁNa.
* Vložte identifikátor URI (například `https://localhost:5001/api/TodoItems/1`).
* Vyberte **Odeslat**.

## <a name="examine-the-get-methods"></a>Projděte si metody GET.

Tyto metody implementují dva koncové body GET:

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

Otestujte aplikaci voláním dvou koncových bodů z prohlížeče nebo po odeslání. Příklad:

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

Odpověď podobná následující je vytvořena voláním `GetTodoItems`:

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
* Nastavte adresu URL požadavku na `https://localhost:<port>/api/TodoItems`. Například `https://localhost:5001/api/TodoItems`.
* Nastavte v příspěvku **dva zobrazení podokna** .
* Vyberte **Odeslat**.

Tato aplikace používá databázi v paměti. Pokud se aplikace zastaví a spustí, předchozí požadavek GET nebude vracet žádná data. Pokud se nevrátí žádná data, [odešlete](#post) data do aplikace.

## <a name="routing-and-url-paths"></a>Směrování a cesty URL

Atribut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) označuje metodu, která reaguje na požadavek HTTP GET. Cesta URL pro každou metodu je konstruována takto:

* Začněte s řetězcem šablony v atributu `Route` kontroleru:

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* Nahraďte `[controller]` názvem kontroleru, který je podle konvence názvem třídy kontroleru minus přípona Controller. V této ukázce je název třídy kontroleru **TodoItems**Controller, takže název kontroleru je "TodoItems". [Směrování](xref:mvc/controllers/routing) ASP.NET Core rozlišuje malá a velká písmena.
* Pokud má atribut `[HttpGet]` šablonu směrování (například `[HttpGet("products")]`), přidejte ji k cestě. Tato ukázka nepoužívá šablonu. Další informace najdete v tématu [Směrování atributů s atributy http [příkaz]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

V následující metodě `GetTodoItem` `"{id}"` je zástupnou proměnnou pro jedinečný identifikátor položky k provedení. Když je vyvoláno `GetTodoItem`, hodnota `"{id}"` v adrese URL je poskytnuta metodě v parametru `id`.

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>Vrácené hodnoty

Návratový typ `GetTodoItems` a `GetTodoItem` metody je [ActionResult \<T > typ](xref:web-api/action-return-types#actionresultt-type). ASP.NET Core automaticky serializovat objekt do formátu [JSON](https://www.json.org/) a zapíše JSON do textu zprávy s odpovědí. Kód odpovědi pro tento návratový typ je 200, za předpokladu, že neexistují žádné neošetřené výjimky. Neošetřené výjimky jsou přeloženy na 5xx chyby.

návratové typy `ActionResult` mohou představovat široké spektrum stavových kódů HTTP. `GetTodoItem` může například vracet dvě různé stavové hodnoty:

* Pokud žádná položka neodpovídá požadovanému ID, vrátí metoda kód chyby 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) .
* V opačném případě metoda vrátí 200 text odpovědi JSON. Vrácení `item` způsobí odpověď HTTP 200.

## <a name="the-puttodoitem-method"></a>Metoda PutTodoItem

Projděte si metodu `PutTodoItem`:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

`PutTodoItem` se podobá `PostTodoItem`, s tím rozdílem, že používá PUT HTTP. Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). V souladu se specifikací HTTP vyžaduje požadavek PUT klientovi, aby odesílal celou aktualizovanou entitu, a ne jenom změny. K podpoře částečných aktualizací použijte [opravu http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).

Pokud se zobrazí chyba s voláním `PutTodoItem`, zavoláním `GET` zajistěte, aby v databázi byla nějaká položka.

### <a name="test-the-puttodoitem-method"></a>Test metody PutTodoItem

Tato ukázka používá databázi v paměti, která musí být inicializována při každém spuštění aplikace. Před provedením volání PUT musí existovat položka v databázi. Před provedením volání metody PUT zavolejte funkci GET k instalování položky v databázi.

Aktualizujte položku úkolů s ID = 1 a nastavte její název na "rybí ryby":

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

Na následujícím obrázku je znázorněná aktualizace po odeslání:

![Konzola pro publikování zobrazující 204 (bez obsahu)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a>Metoda DeleteTodoItem

Projděte si metodu `DeleteTodoItem`:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

`DeleteTodoItem` odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).

### <a name="test-the-deletetodoitem-method"></a>Test metody DeleteTodoItem

Odstranění položky úkolů pomocí metody post:

* Nastavte metodu na `DELETE`.
* Nastavte identifikátor URI objektu, který má být odstraněn (například `https://localhost:5001/api/TodoItems/1`).
* Vyberte **Odeslat**.

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

## <a name="overview"></a>Přehled

V tomto kurzu se vytvoří následující rozhraní API:

|rozhraní API | Popis | Text žádosti | Tělo odpovědi |
|--- | ---- | ---- | ---- |
|ZÍSKAT/api/TodoItems | Získat všechny položky úkolů | Žádné | Pole položek úkolů|
|ZÍSKAT/api/TodoItems/{id} | Získat položku podle ID | Žádné | Položka úkolů|
|PŘÍSPĚVEK/api/TodoItems | Přidat novou položku | Položka úkolů | Položka úkolů |
|Vložit/api/TodoItems/{id} | Aktualizovat existující &nbsp; položky | Položka úkolů | Žádné |
|Odstranit &nbsp;/api/TodoItems/{id} &nbsp; | Odstranění položky &nbsp; &nbsp; | Žádné | Žádné|

V následujícím diagramu vidíte návrh aplikace.

![Klient je reprezentován polem vlevo. Odešle požadavek a přijme odpověď z aplikace, pole nakreslené na pravé straně. V poli aplikace tři čtverečky reprezentují kontroler, model a vrstvu přístupu k datům. Požadavek přichází do kontroleru aplikace a operace čtení/zápisu probíhají mezi kontrolérem a vrstvou přístupu k datům. Model je serializován a vrácen klientovi v odpovědi.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a>Vytvoření webového projektu

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* V nabídce **soubor** vyberte **Nový** > **projekt**.
* Vyberte šablonu **ASP.NET Core webové aplikace** a klikněte na tlačítko **Další**.
* Pojmenujte projekt *TodoApi* a klikněte na **vytvořit**.
* V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 2,2** . Vyberte šablonu **rozhraní API** a klikněte na **vytvořit**. **Nevybírejte možnost** **Povolit podporu Docker**.

![Dialogové okno VS New Project](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Změňte adresáře (`cd`) na složku, která bude obsahovat složku projektu.
* Spusťte následující příkazy:

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  Tyto příkazy vytvoří nový projekt webového rozhraní API a otevřou novou instanci Visual Studio Code v nové složce projektu.

* Pokud se zobrazí dialogové okno s dotazem, zda chcete přidat požadované prostředky do projektu, vyberte možnost **Ano**.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Vyberte **soubor** > **nové řešení**.

  ![macOS nové řešení](first-web-api-mac/_static/sln.png)

* V **části rozhraní .NET Core** > **App** > **API** > **Další**.

  ![dialog pro nový projekt v macOS](first-web-api-mac/_static/1.png)
  
* V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** přijměte výchozí **cílovou** verzi rozhraní * *.NET Core 2,2*.

* Jako **název projektu** zadejte *TodoApi* a pak vyberte **vytvořit**.

  ![Dialogové okno Konfigurace](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a>Testování rozhraní API

Šablona projektu vytvoří rozhraní `values` API. Voláním metody `Get` z prohlížeče otestujete aplikaci.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci. Visual Studio spustí prohlížeč a přejde na `https://localhost:<port>/api/values`, kde `<port>` je náhodně zvolené číslo portu.

Pokud se zobrazí dialogové okno s dotazem, jestli byste měli důvěřovat certifikátu IIS Express, vyberte **Ano**. V dialogovém okně **Upozornění zabezpečení** , které se zobrazí jako další, vyberte **Ano**.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci. V prohlížeči přejdete na následující adresu URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Vyberte **spustit**  > **Spustit ladění** a spusťte tak aplikaci. Visual Studio pro Mac spustí prohlížeč a přejde na `https://localhost:<port>`, kde `<port>` je náhodně zvolené číslo portu. Vrátí se chyba HTTP 404 (Nenalezeno). Přidejte `/api/values` k adrese URL (změňte adresu URL na `https://localhost:<port>/api/values`).

---

Vrátí se následující kód JSON:

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a>Přidat třídu modelu

*Model* je sada tříd, které reprezentují data, která aplikace spravuje. Model pro tuto aplikaci je jediná třída `TodoItem`.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt. Vyberte **přidat**  > **novou složku**. Pojmenujte *modely*složek.

* Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat**  > **třídy**. Pojmenujte třídu *TodoItem* a vyberte **Přidat**.

* Kód šablony nahraďte následujícím kódem:

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Přidejte složku s názvem *modely*.

* Přidejte třídu `TodoItem` do složky *modely* pomocí následujícího kódu:

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Klikněte pravým tlačítkem na projekt. Vyberte **přidat**  > **novou složku**. Pojmenujte *modely*složek.

  ![Nová složka](first-web-api-mac/_static/folder.png)

* Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **nový soubor** > **Obecné** > **prázdná třída**.

* Pojmenujte třídu *TodoItem*a potom klikněte na **Nový**.

* Kód šablony nahraďte následujícím kódem:

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

Vlastnost `Id` funguje jako jedinečný klíč v relační databázi.

Třídy modelu mohou jít kdekoli v projektu, ale složka *modely* je používána konvencí.

## <a name="add-a-database-context"></a>Přidání kontextu databáze

*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro datový model. Tato třída je vytvořena odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat**  > **třídy**. Pojmenujte třídu *TodoContext* a klikněte na **Přidat**.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

* Přidejte třídu `TodoContext` do složky *modely* .

---

* Kód šablony nahraďte následujícím kódem:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>Registrace kontextu databáze

V ASP.NET Core musí být služby, jako je například kontext databáze, registrovány s kontejnerem [vkládání závislostí (di)](xref:fundamentals/dependency-injection) . Kontejner poskytuje službu pro řadiče.

Aktualizujte *Startup.cs* o následující zvýrazněný kód:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

Předchozí kód:

* Odebere nepoužívané deklarace `using`.
* Přidá kontext databáze do kontejneru DI.
* Určuje, že kontext databáze bude používat databázi v paměti.

## <a name="add-a-controller"></a>Přidání kontroleru

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klikněte pravým tlačítkem na složku *řadiče* .
* Vyberte **přidat** > **novou položku**.
* V dialogovém okně **Přidat novou položku** vyberte šablonu **Třída kontroleru rozhraní API** .
* Pojmenujte třídu *TodoController*a vyberte **Přidat**.

  ![Dialogové okno Přidat novou položku s řadičem ve vyhledávacím poli a vybraným kontrolérem webového rozhraní API](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

* Ve složce *Controllers* vytvořte třídu s názvem `TodoController`.

---

* Kód šablony nahraďte následujícím kódem:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

Předchozí kód:

* Definuje třídu kontroleru rozhraní API bez metod.
* Upraví třídu atributem [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) . Tento atribut označuje, že kontroler reaguje na požadavky webového rozhraní API. Informace o konkrétním chování, které atribut povoluje, naleznete v tématu <xref:web-api/index>.
* Pomocí DI vloží kontext databáze (`TodoContext`) do kontroleru. Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v kontroleru.
* Pokud je databáze prázdná, přidá do databáze položku s názvem `Item1`. Tento kód je v konstruktoru, takže se spustí pokaždé, když se vytvoří nový požadavek HTTP. Pokud odstraníte všechny položky, vytvoří konstruktor `Item1` znovu při příštím volání metody rozhraní API. Takže může vypadat, že odstranění nefungovalo, pokud skutečně fungovalo.

## <a name="add-get-methods"></a>Přidat metody Get

Chcete-li poskytnout rozhraní API, které načítá položky úkolů, přidejte následující metody do třídy `TodoController`:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

Tyto metody implementují dva koncové body GET:

* `GET /api/todo`
* `GET /api/todo/{id}`

Pokud je pořád spuštěná, zastavte aplikaci. Pak ji znovu spusťte, aby obsahovala nejnovější změny.

Otestujte aplikaci voláním dvou koncových bodů z prohlížeče. Příklad:

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

Následující odpověď protokolu HTTP je vytvořena voláním `GetTodoItems`:

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a>Směrování a cesty URL

Atribut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) označuje metodu, která reaguje na požadavek HTTP GET. Cesta URL pro každou metodu je konstruována takto:

* Začněte s řetězcem šablony v atributu `Route` kontroleru:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* Nahraďte `[controller]` názvem kontroleru, který je podle konvence názvem třídy kontroleru minus přípona Controller. V této ukázce je názvem třídy kontroleru kontroler **TODO**, takže název kontroleru je "todo". [Směrování](xref:mvc/controllers/routing) ASP.NET Core rozlišuje malá a velká písmena.
* Pokud má atribut `[HttpGet]` šablonu směrování (například `[HttpGet("products")]`), přidejte ji k cestě. Tato ukázka nepoužívá šablonu. Další informace najdete v tématu [Směrování atributů s atributy http [příkaz]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

V následující metodě `GetTodoItem` `"{id}"` je zástupnou proměnnou pro jedinečný identifikátor položky k provedení. Když je vyvoláno `GetTodoItem`, hodnota `"{id}"` v adrese URL je poskytnuta metodě v parametru `id`.

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>Vrácené hodnoty

Návratový typ `GetTodoItems` a `GetTodoItem` metody je [ActionResult \<T > typ](xref:web-api/action-return-types#actionresultt-type). ASP.NET Core automaticky serializovat objekt do formátu [JSON](https://www.json.org/) a zapíše JSON do textu zprávy s odpovědí. Kód odpovědi pro tento návratový typ je 200, za předpokladu, že neexistují žádné neošetřené výjimky. Neošetřené výjimky jsou přeloženy na 5xx chyby.

návratové typy `ActionResult` mohou představovat široké spektrum stavových kódů HTTP. `GetTodoItem` může například vracet dvě různé stavové hodnoty:

* Pokud žádná položka neodpovídá požadovanému ID, vrátí metoda kód chyby 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) .
* V opačném případě metoda vrátí 200 text odpovědi JSON. Vrácení `item` způsobí odpověď HTTP 200.

## <a name="test-the-gettodoitems-method"></a>Test metody GetTodoItems

V tomto kurzu se používá post k testování webového rozhraní API.

* Nainstalujte [post](https://www.getpostman.com/downloads/).
* Spusťte webovou aplikaci.
* Spusťte post.
* Zakáže **ověřování certifikátu SSL**.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* V **Nastavení** **souboru** > (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

* Na **kartě  > ** **Předvolby** (karta**Obecné** ) zakažte **ověřování certifikátu SSL**. Případně vyberte klíče a vyberte **Nastavení**a pak zakažte ověřování certifikátu SSL.

---
  
> [!WARNING]
> Po otestování kontroleru znovu povolte ověření certifikátu SSL.

* Vytvoří novou žádost.
  * Nastavte metodu HTTP na **Get**.
  * Nastavte adresu URL požadavku na `https://localhost:<port>/api/todo`. Například `https://localhost:5001/api/todo`.
* Nastavte v příspěvku **dva zobrazení podokna** .
* Vyberte **Odeslat**.

![Odeslání pomocí žádosti Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a>Přidání metody Create

Do *Controllers/TodoController. cs*přidejte následující metodu `PostTodoItem`: 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

Předchozí kód je metoda HTTP POST, která je označena atributem [[HTTPPOST]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) . Metoda získá hodnotu položky k seřízení z těla požadavku HTTP.

Metoda `CreatedAtAction`:

* Vrátí stavový kód HTTP 201, pokud bylo úspěšné. HTTP 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru.
* Přidá hlavičku `Location` k odpovědi. Hlavička `Location` Určuje identifikátor URI nově vytvořené položky. Další informace najdete v tématu [10.2.2 201 vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* Odkazuje na akci `GetTodoItem` pro vytvoření identifikátoru URI `Location` hlavičky. Klíčové C# slovo `nameof` slouží k tomu, aby se předešlo zakódování názvu akce ve volání `CreatedAtAction`.

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a>Test metody PostTodoItem

* Sestavte projekt.
* V poli post nastavte metodu HTTP na `POST`.
* Vyberte kartu **tělo** .
* Vyberte **nezpracovaný** přepínač.
* Nastavte typ na **JSON (Application/JSON)** .
* Do textu žádosti zadejte JSON pro položku úkolů:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* Vyberte **Odeslat**.

  ![Poslat pomocí žádosti o vytvoření](first-web-api/_static/create.png)

  Pokud se zobrazí chyba metoda 405 není povolená, je pravděpodobné, že po přidání metody `PostTodoItem` nezkompiluje projekt.

### <a name="test-the-location-header-uri"></a>Otestování identifikátoru URI hlavičky umístění

* V podokně **odpověď** vyberte kartu **hlavičky** .
* Zkopírujte hodnotu hlavičky **umístění** :

  ![Karta hlavičky v konzole pro odesílání](first-web-api/_static/pmc2.png)

* Nastavte metodu, která má být ZÍSKÁNa.
* Vložte identifikátor URI (například `https://localhost:5001/api/Todo/2`).
* Vyberte **Odeslat**.

## <a name="add-a-puttodoitem-method"></a>Přidat metodu PutTodoItem

Přidejte následující metodu `PutTodoItem`:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

`PutTodoItem` se podobá `PostTodoItem`, s tím rozdílem, že používá PUT HTTP. Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). V souladu se specifikací HTTP vyžaduje požadavek PUT klientovi, aby odesílal celou aktualizovanou entitu, a ne jenom změny. K podpoře částečných aktualizací použijte [opravu http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).

Pokud se zobrazí chyba s voláním `PutTodoItem`, zavoláním `GET` zajistěte, aby v databázi byla nějaká položka.

### <a name="test-the-puttodoitem-method"></a>Test metody PutTodoItem

Tato ukázka používá databázi v paměti, která musí být inicializována při každém spuštění aplikace. Před provedením volání PUT musí existovat položka v databázi. Před provedením volání metody PUT zavolejte funkci GET k instalování položky v databázi.

Aktualizujte položku úkolů s ID = 1 a nastavte její název na "rybí ryby":

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

Na následujícím obrázku je znázorněná aktualizace po odeslání:

![Konzola pro publikování zobrazující 204 (bez obsahu)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a>Přidat metodu DeleteTodoItem

Přidejte následující metodu `DeleteTodoItem`:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

`DeleteTodoItem` odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).

### <a name="test-the-deletetodoitem-method"></a>Test metody DeleteTodoItem

Odstranění položky úkolů pomocí metody post:

* Nastavte metodu na `DELETE`.
* Nastavte identifikátor URI objektu, který má být odstraněn (například `https://localhost:5001/api/todo/1`).
* Vyberte **Odeslat**.

Ukázková aplikace umožňuje odstranit všechny položky. Když je však poslední položka odstraněna, vytvoří se nový konstruktor třídy modelu při příštím volání rozhraní API.

## <a name="call-the-web-api-with-javascript"></a>Volání webového rozhraní API pomocí JavaScriptu

V této části se přidá stránka HTML, která pomocí JavaScriptu volá webové rozhraní API. jQuery inicializuje požadavek. JavaScript aktualizuje stránku s podrobnostmi z odpovědi webového rozhraní API.

Nakonfigurujte aplikaci tak, aby [sloužila pro statické soubory](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) , a [Povolte výchozí mapování souborů](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) aktualizací *Startup.cs* pomocí následujícího zvýrazněného kódu:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

Vytvořte složku *wwwroot* v adresáři projektu.

Do adresáře *wwwroot* přidejte soubor HTML s názvem *index. html* . Nahraďte jeho obsah následujícím kódem:

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

Do adresáře *wwwroot* přidejte soubor JavaScriptu s názvem *Web. js* . Nahraďte jeho obsah následujícím kódem:

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

Pro místní testování stránky HTML může být nutné změnit nastavení spouštění ASP.NET Core projektu:

* Otevřete *Properties\launchSettings.JSON*.
* Odebráním vlastnosti `launchUrl` vynutíte otevření aplikace v *indexu. html* &mdash;the výchozí soubor projektu.

Tato ukázka volá všechny metody CRUD webového rozhraní API. Následují vysvětlení volání rozhraní API.

### <a name="get-a-list-of-to-do-items"></a>Získat seznam úkolů

jQuery pošle požadavek HTTP GET do webového rozhraní API, které vrátí JSON představující pole položek úkolů. Funkce zpětného volání `success` se vyvolá, pokud je požadavek úspěšný. Ve zpětném volání je DOM aktualizován pomocí informací o tom.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a>Přidat položku úkolů

jQuery pošle požadavek HTTP POST s položkou k žádosti v textu požadavku. Možnosti `accepts` a `contentType` jsou nastaveny na `application/json` k určení typu média, který přijímá a odesílá. Položka úkolů se převede na JSON pomocí [JSON. stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify). Když rozhraní API vrátí úspěšný kód stavu, vyvolá se funkce `getData`, která aktualizuje tabulku HTML.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a>Aktualizace položky úkolů

Aktualizace položky úkolů je podobná přidání. `url` se změní, aby se přidal jedinečný identifikátor položky a `type` je `PUT`.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a>Odstranění položky úkolů

Odstranění položky úkolů je provedeno nastavením `type` v volání jazyka AJAX na `DELETE` a zadáním jedinečného identifikátoru položky v adrese URL.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a>Přidání podpory ověřování do webového rozhraní API

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a>Další zdroje

[Zobrazit nebo stáhnout vzorový kód pro tento kurz](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples). Viz [Jak stáhnout](xref:index#how-to-download-a-sample).

Další informace naleznete v následujících materiálech:

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [Verze YouTube tohoto kurzu](https://www.youtube.com/watch?v=TTkhEyGBfAk)

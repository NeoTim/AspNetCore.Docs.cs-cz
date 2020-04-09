---
title: 'Kurz: Vytvoření webového rozhraní API s ASP.NET jádrem'
author: rick-anderson
description: Přečtěte si, jak vytvořit webové rozhraní API s ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
uid: tutorials/first-web-api
ms.openlocfilehash: 7418e962076fae3ebdbb25381838757b09046578
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417663"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a>Kurz: Vytvoření webového rozhraní API s ASP.NET jádrem

[Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), a [Mike Wasson](https://github.com/mikewasson)

Tento kurz učí základy vytváření webového rozhraní API s ASP.NET Core.

::: moniker range=">= aspnetcore-3.0"

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte projekt webového rozhraní API.
> * Přidejte třídu modelu a kontext databáze.
> * Lešení regulátor s metodami CRUD.
> * Nakonfigurujte směrování, cesty adres URL a vrácené hodnoty.
> * Zavolejte webové rozhraní API s Postman.

Na konci máte webové rozhraní API, které můžete spravovat položky "to-do" uložené v databázi.

## <a name="overview"></a>Přehled

Tento kurz vytvoří následující rozhraní API:

|rozhraní API | Popis | Text požadavku | Text odpovědi |
|--- | ---- | ---- | ---- |
|GET /api/TodoItems | Získat všechny položky v satojáši | Žádný | Pole položek, které chcete provést|
|GET /api/TodoItems/{id} | Získání položky podle ID | Žádný | Položka k práci|
|POST /api/TodoItems | Přidání nové položky | Položka k práci | Položka k práci |
|PUT /api/TodoItems/{id} | Aktualizace existující položky&nbsp; | Položka k práci | Žádný |
|DELETE /api/TodoItems/{id} &nbsp;&nbsp; | Odstranění položky &nbsp;&nbsp; | Žádný | Žádný|

Následující diagram znázorňuje návrh aplikace.

![Klient je reprezentován polem vlevo. Odešle žádost a obdrží odpověď z žádosti, kolonka vytyčená vpravo. V krabici aplikace představují tři pole řadič, model a vrstvu přístupu k datům. Požadavek se dostane do řadiče aplikace a operace čtení a zápisu dojít mezi řadičem a vrstvy přístupu k datům. Model je serializován a vrácen klientovi v odpovědi.](first-web-api/_static/architecture.png)

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

* V nabídce **Soubor** vyberte **Nový** > **projekt**.
* Vyberte šablonu **ASP.NET Základní webová aplikace** a klepněte na tlačítko **Další**.
* Pojmenujte projekt *TodoApi* a klepněte na tlačítko **Vytvořit**.
* V **dialogovém okně Vytvořit novou ASP.NET základní webovou aplikaci** zkontrolujte, zda jsou vybrány základní a ASP.NET **jádra 3.1.** **.NET Core** Vyberte šablonu **rozhraní API** a klepněte na **tlačítko Vytvořit**.

![Dialogové okno nového projektu VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otevřete [integrovanou svorku](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Změňte adresáře (`cd`) na složku, která bude obsahovat složku projektu.
* Spusťte následující příkazy:

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* Když se dialogové okno zeptá, jestli chcete do projektu přidat požadované datové zdroje, vyberte **Ano**.

  Předchozí příkazy:

  * Vytvoří nový projekt webového rozhraní API a otevře jej v kódu sady Visual Studio.
  * Přidá Balíčky NuGet, které jsou požadovány v další části.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Vyberte **možnost Nové řešení souboru** > **New Solution**.

  ![macOS Nové řešení](first-web-api-mac/_static/sln.png)

* Vyberte **rozhraní API** > **aplikace** > **.NET Core** > **Next**.

  ![macOS Dialogové okno Nový projekt](first-web-api-mac/_static/1.png)
  
* V dialogovém **okně Konfigurovat nové webové rozhraní API ASP.NET** vyberte **Cílová architektura** **.NET Core 3.1*.

* Zadejte *TodoApi* pro **název projektu** a pak vyberte **Vytvořit**.

  ![config dialogové okno](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

Otevřete terminál příkazů ve složce projektu a spusťte následující příkazy:

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a>Testování rozhraní API

Šablona projektu `WeatherForecast` vytvoří rozhraní API. Volání `Get` metody z prohlížeče k testování aplikace.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Stisknutím kláves Ctrl+F5 aplikaci spusťte. Visual Studio spustí prohlížeč a `https://localhost:<port>/WeatherForecast`přejde `<port>` na , kde je náhodně vybrané číslo portu.

Pokud se zobrazí dialogové okno s dotazem, zda byste měli důvěřovat certifikátu IIS Express, vyberte **ano**. V dialogovém **okně Upozornění zabezpečení,** které se zobrazí jako další, vyberte **ano**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Stisknutím kláves Ctrl+F5 aplikaci spusťte. V prohlížeči přejděte na `https://localhost:5001/WeatherForecast`následující adresu URL: .

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Chcete-li aplikaci spustit**spouštět ladění,** vyberte spustit ladění. **Run** >  Visual Studio for Mac spustí prohlížeč `https://localhost:<port>`a `<port>` přejde na , kde je náhodně vybrané číslo portu. Je vrácena chyba HTTP 404 (Nebyla nalezena). Připojte `/WeatherForecast` adresu URL (změňte adresu URL na `https://localhost:<port>/WeatherForecast`).

---

JSON podobné následující je vrácena:

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

## <a name="add-a-model-class"></a>Přidání třídy modelu

*Model* je sada tříd, které představují data, která aplikace spravuje. Model pro tuto aplikaci `TodoItem` je jedna třída.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt. Vyberte **Přidat** > **novou složku**. Pojmenujte složku *Modely*.

* Klepněte pravým tlačítkem myši na složku *Modely* a vyberte **přidat** > **třídu**. Pojmenujte třídu *TodoItem* a vyberte **Přidat**.

* Nahraďte kód šablony následujícím kódem:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Přidejte složku s názvem *Modely*.

* Přidejte `TodoItem` třídu do složky *Modely* s následujícím kódem:

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Klikněte pravým tlačítkem myši na projekt. Vyberte **Přidat** > **novou složku**. Pojmenujte složku *Modely*.

  ![nová složka](first-web-api-mac/_static/folder.png)

* Klepněte pravým tlačítkem myši na složku *Modely* a vyberte **přidat** > novou **obecnou prázdnou** > **třídu** **souboru** > .

* Pojmenujte třídu *TodoItem*a klepněte na tlačítko **Nový**.

* Nahraďte kód šablony následujícím kódem:

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

Vlastnost `Id` funguje jako jedinečný klíč v relační databázi.

Třídy modelu může jít kdekoli v projektu, ale *modely* složky se používá konvence.

## <a name="add-a-database-context"></a>Přidání kontextu databáze

*Kontext databáze* je hlavní třída, která koordinuje entity framework funkce pro datový model. Tato třída je vytvořena odvozením `Microsoft.EntityFrameworkCore.DbContext` z třídy.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a>Přidat Microsoft.EntityFrameworkCore.SqlServer

* V nabídce **Nástroje** vyberte **Správce balíčků NuGet > Spravovat balíčky NuGet pro řešení**.
* Vyberte kartu **Procházet** a do vyhledávacího pole zadejte **Microsoft.EntityFrameworkCore.SqlServer.**
* V levém podokně vyberte **microsoft.EntityFrameworkCore.SqlServer.**
* Zaškrtněte **políčko Project** v pravém podokně a pak vyberte **Instalovat**.
* Pomocí předchozích pokynů přidejte balíček `Microsoft.EntityFrameworkCore.InMemory` NuGet.

![Správce balíčků NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a>Přidání kontextu databáze TodoContext

* Klepněte pravým tlačítkem myši na složku *Modely* a vyberte **přidat** > **třídu**. Pojmenujte třídu *TodoContext* a klepněte na tlačítko **Přidat**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

* Přidejte `TodoContext` třídu do složky *Modely.*

---

* Zadejte následující kód:

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>Registrace kontextu databáze

V ASP.NET Core, služby, jako je kontext DB musí být registrovány s kontejnerem [vkládání závislostí (DI).](xref:fundamentals/dependency-injection) Kontejner poskytuje službu řadičům.

Aktualizace *Startup.cs* s následujícím zvýrazněným kódem:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

Předcházející kód:

* Odebere nepoužívané `using` deklarace.
* Přidá kontext databáze do kontejneru DI.
* Určuje, že kontext databáze bude používat databázi v paměti.

## <a name="scaffold-a-controller"></a>Lešení regulátor

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klikněte pravým tlačítkem myši na složku *Řadiče.*
* Vyberte **Přidat** > **novou položku scaffolded .**
* Vyberte **řadič rozhraní API s akcemi pomocí entity Framework**a pak vyberte **Přidat**.
* V dialogovém **okně Přidat řadič rozhraní API s akcemi pomocí dialogového** okna Entity Framework:

  * Vyberte **položku TodoItem (TodoApi.Models)** ve **třídě Model**.
  * Vyberte **TodoContext (TodoApi.Models)** ve **třídě kontextu Data**.
  * Vyberte **Přidat**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

Spusťte následující příkazy:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

Předchozí příkazy:

* Přidejte balíčky NuGet potřebné pro lešení.
* Nainstaluje lešení motoru`dotnet-aspnet-codegenerator`( ).
* Lešení `TodoItemsController`.

---

Generovaný kód:

* Označí třídu atributem. [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) Tento atribut označuje, že řadič reaguje na požadavky webového rozhraní API. Informace o konkrétních chováních, které <xref:web-api/index>atribut povoluje, naleznete v tématu .
* Používá DI vložit kontext`TodoContext`databáze ( ) do řadiče. Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v řadiči.

Základní ASP.NET pro:

* Kontrolidy `[action]` se zobrazeními zahrnují v šabloně trasy.
* Řadiče rozhraní API `[action]` nezahrnují do šablony trasy.

Pokud `[action]` token není v šabloně trasy, název [akce](xref:mvc/controllers/routing#action) je vyloučen z trasy. To znamená, že název přidružené metody akce se nepoužívá v odpovídající trase.

## <a name="examine-the-posttodoitem-create-method"></a>Prozkoumejte metodu vytvoření posttodoitem

Nahraďte příkaz `PostTodoItem` return v příkazu použít [název operátoru:](/dotnet/csharp/language-reference/operators/nameof)

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

Předchozí kód je metoda HTTP POST, jak [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) je uvedeno atributem. Metoda získá hodnotu položky pro do z těla požadavku HTTP.

Metoda: <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>

* Vrátí stavový kód HTTP 201, pokud je úspěšný. HTTP 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru.
* Přidá k odpovědi hlavičku [Umístění.](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) Záhlaví `Location` určuje [identifikátor URI](https://developer.mozilla.org/docs/Glossary/URI) nově vytvořené položky pro práci. Další informace naleznete v tématu [10.2.2 201 Vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* Odkazuje na `GetTodoItem` akci k `Location` vytvoření identifikátoru URI záhlaví. Klíčové slovo `nameof` C# se používá k zabránění `CreatedAtAction` pevnékódování název akce v volání.

### <a name="install-postman"></a>Instalace Pošťáka

Tento kurz používá Postman k testování webového rozhraní API.

* Instalace [Pošťáka](https://www.getpostman.com/downloads/)
* Spusťte webovou aplikaci.
* Založ pošťáka.
* Zakázání **ověření certifikátu SSL**
  * Z **nastavení** **souboru** > **(karta Obecné)** zakažte **ověření certifikátu SSL**.
    > [!WARNING]
    > Po otestování řadiče znovu povolte ověření certifikátu SSL.

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a>Test PostTodoItem s pošťákem

* Vytvořte nový požadavek.
* Nastavte metodu `POST`HTTP na .
* Vyberte kartu **Tělo.**
* Vyberte **nezpracované** přepínací tlačítko.
* Nastavte typ na **JSON (aplikace/json)**.
* V těle požadavku zadejte JSON pro položku s cílem:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* Vyberte **Poslat**.

  ![Pošťák s požadavkem na vytvoření](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a>Testování identifikátoru URI záhlaví umístění

* V podokně **Odpověď** vyberte kartu **Záhlaví.**
* Zkopírujte hodnotu hlavičky **Umístění:**

  ![Karta Záhlaví konzoly Postman](first-web-api/_static/3/create.png)

* Nastavte metodu GET.
* Vložte identifikátor URI `https://localhost:5001/api/TodoItems/1`(například).
* Vyberte **Poslat**.

## <a name="examine-the-get-methods"></a>Prozkoumejte metody GET

Tyto metody implementují dva koncové body GET:

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

Otestujte aplikaci voláním dvou koncových bodů z prohlížeče nebo Pošťáka. Příklad:

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

Výzva k `GetTodoItems`:

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a>Test Get s Pošťák

* Vytvořte nový požadavek.
* Nastavte metodu HTTP na **GET**.
* Nastavte adresu URL `https://localhost:<port>/api/TodoItems`požadavku na . Například, `https://localhost:5001/api/TodoItems`.
* Nastavte **dvě zobrazení podokna** v Pošťákovi.
* Vyberte **Poslat**.

Tato aplikace používá databázi v paměti. Pokud je aplikace zastavena a spuštěna, předchozí požadavek GET nevrátí žádná data. Pokud nejsou vrácena žádná data, [post](#post) data do aplikace.

## <a name="routing-and-url-paths"></a>Cesty směrování a adresy URL

Atribut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) označuje metodu, která reaguje na požadavek HTTP GET. Cesta URL pro každou metodu je vytvořena takto:

* Začněte řetězcem šablony v `Route` atributu řadiče:

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* Nahraďte `[controller]` název řadiče, který podle konvence je název třídy řadiče mínus přípona "Controller". Pro tuto ukázku je název třídy řadiče **TodoItems**Controller, takže název řadiče je "TodoItems". ASP.NET [Směrování](xref:mvc/controllers/routing) jádra nerozlišuje malá a velká písmena.
* Pokud `[HttpGet]` má atribut šablonu trasy `[HttpGet("products")]`(například), připojte ji k cestě. Tato ukázka nepoužívá šablonu. Další informace naleznete v [tématu Směrování atributů s atributy Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

V následující `GetTodoItem` metodě `"{id}"` je zástupná proměnná pro jedinečný identifikátor položky s cílem. Při `GetTodoItem` vyvolání je hodnota `"{id}"` v adrese URL k dispozici `id` metodě v jejím parametru.

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>Vrácené hodnoty

Návratový typ `GetTodoItems` metody `GetTodoItem` a je [ActionResult\<T> typu](xref:web-api/action-return-types#actionresultt-type). ASP.NET Core automaticky serializuje objekt [JSON](https://www.json.org/) a zapíše JSON do těla zprávy odpovědi. Kód odpovědi pro tento návratový typ je 200, za předpokladu, že neexistují žádné neošetřené výjimky. Neošetřené výjimky jsou přeloženy do chyb 5xx.

`ActionResult`návratové typy mohou představovat širokou škálu stavových kódů HTTP. Můžete například `GetTodoItem` vrátit dvě různé hodnoty stavu:

* Pokud žádná položka neodpovídá požadovanému ID, vrátí metoda kód chyby 404 [NotFound.](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound)
* V opačném případě metoda vrátí 200 s tělo odezvy JSON. Vracející `item` se výsledky odpovědi HTTP 200.

## <a name="the-puttodoitem-method"></a>Metoda PutTodoItem

Zkontrolujte `PutTodoItem` metodu:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

`PutTodoItem`je podobný `PostTodoItem`, s výjimkou používá HTTP PUT. Odpověď je [204 (Žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). Podle specifikace HTTP požadavek PUT vyžaduje, aby klient odeslal celou aktualizovanou entitu, nikoli pouze změny. Chcete-li podporovat částečné aktualizace, použijte [protokol HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).

Pokud se zobrazí `PutTodoItem`chyba `GET` volání , volání zajistit, že je položka v databázi.

### <a name="test-the-puttodoitem-method"></a>Otestovat metodu PutTodoItem

Tato ukázka používá databázi v paměti, která musí být inicializována při každém spuštění aplikace. Před voláním PUT musí být v databázi položka. Volání GET pojistit, že je položka v databázi před provedením volání PUT.

Aktualizujte položku, která má ID = 1 a nastavte její název na "krmné ryby":

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

Následující obrázek znázorňuje pošťákovou aktualizaci:

![Pošťák konzole zobrazující 204 (žádný obsah) odpověď](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a>Metoda DeleteTodoItem

Zkontrolujte `DeleteTodoItem` metodu:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a>Otestovat metodu DeleteTodoItem

Pomocí pošťáku odstraňte položku, kterou chcete provést:

* Nastavte metodu `DELETE`na .
* Nastavte identifikátor URI objektu, který `https://localhost:5001/api/TodoItems/1`chcete odstranit (například).
* Vyberte **Poslat**.

<a name="over-post"></a>

## <a name="prevent-over-posting"></a>Zabránit nadměrnému zaúčtování

V současné době ukázková `TodoItem` aplikace zpřístupňuje celý objekt. Produkční aplikace obvykle omezují data, která je vstupní a vrácená pomocí podmnožiny modelu. Existuje několik důvodů za to a bezpečnost je hlavní. Podmnožina modelu se obvykle označuje jako objekt přenosu dat (DTO), vstupní model nebo model pohledu. **DTO** se používá v tomto článku.

DTO lze použít k:

* Zabránit nadměrnému zaúčtování.
* Skrýt vlastnosti, které klienti nemají zobrazit.
* Vynechejte některé vlastnosti, aby se zmenšila velikost datové části.
* Sloučí grafy objektů, které obsahují vnořené objekty. Grafy složených objektů mohou být pro klienty pohodlnější.

Chcete-li demonstrovat přístup `TodoItem` DTO, aktualizujte třídu tak, aby zahrnovala tajné pole:

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

Tajné pole musí být z této aplikace skryto, ale aplikace pro správu se může rozhodnout, že ji odhalí.

Ověřte, zda můžete zveřejnit a získat tajné pole.

Vytvořte model DTO:

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

Aktualizace `TodoItemsController` chcete-li použít `TodoItemDTO`:

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

Ověřte, zda nemůžete zveřejnit nebo získat tajné pole.

## <a name="call-the-web-api-with-javascript"></a>Volání webového rozhraní API pomocí JavaScriptu

Viz [výuka: Volání ASP.NET základního webového rozhraní API s JavaScriptem](xref:tutorials/web-api-javascript).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte projekt webového rozhraní API.
> * Přidejte třídu modelu a kontext databáze.
> * Přidejte ovladač.
> * Přidejte metody CRUD.
> * Nakonfigurujte cesty směrování a adresy URL.
> * Zadejte vrácené hodnoty.
> * Zavolejte webové rozhraní API s Postman.
> * Volání webového rozhraní API pomocí JavaScriptu.

Na konci máte webové rozhraní API, které můžete spravovat položky "to-do" uložené v relační databázi.

## <a name="overview"></a>Přehled

Tento kurz vytvoří následující rozhraní API:

|rozhraní API | Popis | Text požadavku | Text odpovědi |
|--- | ---- | ---- | ---- |
|GET /api/TodoItems | Získat všechny položky v satojáši | Žádný | Pole položek, které chcete provést|
|GET /api/TodoItems/{id} | Získání položky podle ID | Žádný | Položka k práci|
|POST /api/TodoItems | Přidání nové položky | Položka k práci | Položka k práci |
|PUT /api/TodoItems/{id} | Aktualizace existující položky&nbsp; | Položka k práci | Žádný |
|DELETE /api/TodoItems/{id} &nbsp;&nbsp; | Odstranění položky &nbsp;&nbsp; | Žádný | Žádný|

Následující diagram znázorňuje návrh aplikace.

![Klient je reprezentován polem vlevo. Odešle žádost a obdrží odpověď z žádosti, kolonka vytyčená vpravo. V krabici aplikace představují tři pole řadič, model a vrstvu přístupu k datům. Požadavek se dostane do řadiče aplikace a operace čtení a zápisu dojít mezi řadičem a vrstvy přístupu k datům. Model je serializován a vrácen klientovi v odpovědi.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a>Vytvoření webového projektu

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V nabídce **Soubor** vyberte **Nový** > **projekt**.
* Vyberte šablonu **ASP.NET Základní webová aplikace** a klepněte na tlačítko **Další**.
* Pojmenujte projekt *TodoApi* a klepněte na tlačítko **Vytvořit**.
* V **dialogovém okně Vytvořit novou ASP.NET základní webovou aplikaci** zkontrolujte, zda jsou vybrány základní a **ASP.NET jádra 2.2.Net.** **.NET Core** Vyberte šablonu **rozhraní API** a klepněte na **tlačítko Vytvořit**. **Nevybírejte** **povolit podporu Dockeru**.

![Dialogové okno nového projektu VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otevřete [integrovanou svorku](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Změňte adresáře (`cd`) na složku, která bude obsahovat složku projektu.
* Spusťte následující příkazy:

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  Tyto příkazy vytvoří nový projekt webového rozhraní API a otevřou novou instanci kódu sady Visual Studio v nové složce projektu.

* Když se dialogové okno zeptá, jestli chcete do projektu přidat požadované datové zdroje, vyberte **Ano**.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Vyberte **možnost Nové řešení souboru** > **New Solution**.

  ![macOS Nové řešení](first-web-api-mac/_static/sln.png)

* Vyberte **rozhraní API** > **aplikace** > **.NET Core** > **Next**.

  ![macOS Dialogové okno Nový projekt](first-web-api-mac/_static/1.png)
  
* V dialogovém **okně Konfigurovat nové ASP.NET základní webové rozhraní API** přijměte výchozí **cílovou architekturu** **.NET Core 2.2*.

* Zadejte *TodoApi* pro **název projektu** a pak vyberte **Vytvořit**.

  ![config dialogové okno](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a>Testování rozhraní API

Šablona projektu `values` vytvoří rozhraní API. Volání `Get` metody z prohlížeče k testování aplikace.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Stisknutím kláves Ctrl+F5 aplikaci spusťte. Visual Studio spustí prohlížeč a `https://localhost:<port>/api/values`přejde `<port>` na , kde je náhodně vybrané číslo portu.

Pokud se zobrazí dialogové okno s dotazem, zda byste měli důvěřovat certifikátu IIS Express, vyberte **ano**. V dialogovém **okně Upozornění zabezpečení,** které se zobrazí jako další, vyberte **ano**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Stisknutím kláves Ctrl+F5 aplikaci spusťte. V prohlížeči přejděte na `https://localhost:5001/api/values`následující adresu URL: .

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Chcete-li aplikaci spustit**spouštět ladění,** vyberte spustit ladění. **Run** >  Visual Studio for Mac spustí prohlížeč `https://localhost:<port>`a `<port>` přejde na , kde je náhodně vybrané číslo portu. Je vrácena chyba HTTP 404 (Nebyla nalezena). Připojte `/api/values` adresu URL (změňte adresu URL na `https://localhost:<port>/api/values`).

---

Vrátí se následující JSON:

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a>Přidání třídy modelu

*Model* je sada tříd, které představují data, která aplikace spravuje. Model pro tuto aplikaci `TodoItem` je jedna třída.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt. Vyberte **Přidat** > **novou složku**. Pojmenujte složku *Modely*.

* Klepněte pravým tlačítkem myši na složku *Modely* a vyberte **přidat** > **třídu**. Pojmenujte třídu *TodoItem* a vyberte **Přidat**.

* Nahraďte kód šablony následujícím kódem:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Přidejte složku s názvem *Modely*.

* Přidejte `TodoItem` třídu do složky *Modely* s následujícím kódem:

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Klikněte pravým tlačítkem myši na projekt. Vyberte **Přidat** > **novou složku**. Pojmenujte složku *Modely*.

  ![nová složka](first-web-api-mac/_static/folder.png)

* Klepněte pravým tlačítkem myši na složku *Modely* a vyberte **přidat** > novou **obecnou prázdnou** > **třídu** **souboru** > .

* Pojmenujte třídu *TodoItem*a klepněte na tlačítko **Nový**.

* Nahraďte kód šablony následujícím kódem:

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

Vlastnost `Id` funguje jako jedinečný klíč v relační databázi.

Třídy modelu může jít kdekoli v projektu, ale *modely* složky se používá konvence.

## <a name="add-a-database-context"></a>Přidání kontextu databáze

*Kontext databáze* je hlavní třída, která koordinuje entity framework funkce pro datový model. Tato třída je vytvořena odvozením `Microsoft.EntityFrameworkCore.DbContext` z třídy.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klepněte pravým tlačítkem myši na složku *Modely* a vyberte **přidat** > **třídu**. Pojmenujte třídu *TodoContext* a klepněte na tlačítko **Přidat**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

* Přidejte `TodoContext` třídu do složky *Modely.*

---

* Nahraďte kód šablony následujícím kódem:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>Registrace kontextu databáze

V ASP.NET Core, služby, jako je kontext DB musí být registrovány s kontejnerem [vkládání závislostí (DI).](xref:fundamentals/dependency-injection) Kontejner poskytuje službu řadičům.

Aktualizace *Startup.cs* s následujícím zvýrazněným kódem:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

Předcházející kód:

* Odebere nepoužívané `using` deklarace.
* Přidá kontext databáze do kontejneru DI.
* Určuje, že kontext databáze bude používat databázi v paměti.

## <a name="add-a-controller"></a>Přidání kontroleru

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klikněte pravým tlačítkem myši na složku *Řadiče.*
* Vyberte **Přidat** > **novou položku**.
* V dialogovém okně **Přidat novou položku** vyberte šablonu **třídy řadiče rozhraní API.**
* Pojmenujte třídu *TodoController*a vyberte **přidat**.

  ![Dialogové okno Přidat novou položku s ovladačem ve vyhledávacím poli a vybraným řadičem webového rozhraní API](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

* Ve složce *Controllers* vytvořte `TodoController`třídu s názvem .

---

* Nahraďte kód šablony následujícím kódem:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

Předcházející kód:

* Definuje třídu řadiče rozhraní API bez metod.
* Označí třídu atributem. [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) Tento atribut označuje, že řadič reaguje na požadavky webového rozhraní API. Informace o konkrétních chováních, které <xref:web-api/index>atribut povoluje, naleznete v tématu .
* Používá DI vložit kontext`TodoContext`databáze ( ) do řadiče. Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v řadiči.
* Přidá položku `Item1` pojmenovanou do databáze, pokud je databáze prázdná. Tento kód je v konstruktoru, takže se spustí pokaždé, když je nový požadavek HTTP. Pokud odstraníte všechny položky, `Item1` konstruktor vytvoří znovu při příštím volání metody rozhraní API. Takže to může vypadat, že odstranění nefungovalo, když to skutečně fungovalo.

## <a name="add-get-methods"></a>Přidat metody Get

Chcete-li poskytnout rozhraní API, které načítá položky, přidejte do `TodoController` třídy následující metody:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

Tyto metody implementují dva koncové body GET:

* `GET /api/todo`
* `GET /api/todo/{id}`

Pokud je aplikace stále spuštěná, zastavte ji. Pak jej spusťte znovu, aby zahrnovalnejnovější změny.

Otestujte aplikaci voláním dvou koncových bodů z prohlížeče. Příklad:

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

Volání mj. `GetTodoItems`

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a>Cesty směrování a adresy URL

Atribut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) označuje metodu, která reaguje na požadavek HTTP GET. Cesta URL pro každou metodu je vytvořena takto:

* Začněte řetězcem šablony v `Route` atributu řadiče:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* Nahraďte `[controller]` název řadiče, který podle konvence je název třídy řadiče mínus přípona "Controller". Pro tuto ukázku je název třídy řadiče **Todo**Controller, takže název řadiče je "todo". ASP.NET [Směrování](xref:mvc/controllers/routing) jádra nerozlišuje malá a velká písmena.
* Pokud `[HttpGet]` má atribut šablonu trasy `[HttpGet("products")]`(například), připojte ji k cestě. Tato ukázka nepoužívá šablonu. Další informace naleznete v [tématu Směrování atributů s atributy Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

V následující `GetTodoItem` metodě `"{id}"` je zástupná proměnná pro jedinečný identifikátor položky s cílem. Při `GetTodoItem` vyvolání je hodnota `"{id}"` v adrese URL k dispozici`id` metodě v jejím parametru.

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>Vrácené hodnoty

Návratový typ `GetTodoItems` metody `GetTodoItem` a je [ActionResult\<T> typu](xref:web-api/action-return-types#actionresultt-type). ASP.NET Core automaticky serializuje objekt [JSON](https://www.json.org/) a zapíše JSON do těla zprávy odpovědi. Kód odpovědi pro tento návratový typ je 200, za předpokladu, že neexistují žádné neošetřené výjimky. Neošetřené výjimky jsou přeloženy do chyb 5xx.

`ActionResult`návratové typy mohou představovat širokou škálu stavových kódů HTTP. Můžete například `GetTodoItem` vrátit dvě různé hodnoty stavu:

* Pokud žádná položka neodpovídá požadovanému ID, vrátí metoda kód chyby 404 [NotFound.](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound)
* V opačném případě metoda vrátí 200 s tělo odezvy JSON. Vracející `item` se výsledky odpovědi HTTP 200.

## <a name="test-the-gettodoitems-method"></a>Otestovat metodu GetTodoItems

Tento kurz používá Postman k testování webového rozhraní API.

* Nainstalujte [Pošťáka](https://www.getpostman.com/downloads/).
* Spusťte webovou aplikaci.
* Založ pošťáka.
* Zakažte **ověření certifikátu SSL**.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Z **nastavení** **souboru** > **(karta Obecné)** zakažte **ověření certifikátu SSL**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

* Z **předvoleb Pošťáka** > **Preferences** (**karta Obecné)** zakažte **ověření certifikátu SSL**. Případně vyberte klíč a vyberte **Nastavení**a zakažte ověření certifikátu SSL.

---
  
> [!WARNING]
> Po otestování řadiče znovu povolte ověření certifikátu SSL.

* Vytvořte nový požadavek.
  * Nastavte metodu HTTP na **GET**.
  * Nastavte adresu URL `https://localhost:<port>/api/todo`požadavku na . Například, `https://localhost:5001/api/todo`.
* Nastavte **dvě zobrazení podokna** v Pošťákovi.
* Vyberte **Poslat**.

![Pošťák s žádostí O get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a>Přidat metodu Vytvořit

Do `PostTodoItem` *controllers/TodoController.cs*přidejte následující metodu : 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

Předchozí kód je metoda HTTP POST, jak [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) je uvedeno atributem. Metoda získá hodnotu položky pro do z těla požadavku HTTP.

Metoda: `CreatedAtAction`

* Vrátí stavový kód HTTP 201, pokud je úspěšný. HTTP 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru.
* Přidá `Location` záhlaví k odpovědi. Záhlaví `Location` určuje identifikátor URI nově vytvořené položky pro práci. Další informace naleznete v tématu [10.2.2 201 Vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* Odkazuje na `GetTodoItem` akci k `Location` vytvoření identifikátoru URI záhlaví. Klíčové slovo `nameof` C# se používá k zabránění `CreatedAtAction` pevnékódování název akce v volání.

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a>Otestovat metodu PostTodoItem

* Sestavte projekt.
* V Pošťáku nastavte `POST`metodu HTTP na .
* Vyberte kartu **Tělo.**
* Vyberte **nezpracované** přepínací tlačítko.
* Nastavte typ na **JSON (aplikace/json)**.
* V těle požadavku zadejte JSON pro položku s cílem:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* Vyberte **Poslat**.

  ![Pošťák s požadavkem na vytvoření](first-web-api/_static/create.png)

  Pokud se zobrazí chyba 405 Method Not Allowed, je pravděpodobně výsledkem nekompilace projektu po přidání `PostTodoItem` metody.

### <a name="test-the-location-header-uri"></a>Testování identifikátoru URI záhlaví umístění

* V podokně **Odpověď** vyberte kartu **Záhlaví.**
* Zkopírujte hodnotu hlavičky **Umístění:**

  ![Karta Záhlaví konzoly Postman](first-web-api/_static/pmc2.png)

* Nastavte metodu GET.
* Vložte identifikátor URI `https://localhost:5001/api/Todo/2`(například).
* Vyberte **Poslat**.

## <a name="add-a-puttodoitem-method"></a>Přidat metodu PutTodoItem

Přidejte `PutTodoItem` následující metodu:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

`PutTodoItem`je podobný `PostTodoItem`, s výjimkou používá HTTP PUT. Odpověď je [204 (Žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). Podle specifikace HTTP požadavek PUT vyžaduje, aby klient odeslal celou aktualizovanou entitu, nikoli pouze změny. Chcete-li podporovat částečné aktualizace, použijte [protokol HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).

Pokud se zobrazí `PutTodoItem`chyba `GET` volání , volání zajistit, že je položka v databázi.

### <a name="test-the-puttodoitem-method"></a>Otestovat metodu PutTodoItem

Tato ukázka používá databázi v paměti, která musí být inicializována při každém spuštění aplikace. Před voláním PUT musí být v databázi položka. Volání GET pojistit, že je položka v databázi před provedením volání PUT.

Aktualizujte položku, která má id = 1 a nastavte její název na "krmné ryby":

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

Následující obrázek znázorňuje pošťákovou aktualizaci:

![Pošťák konzole zobrazující 204 (žádný obsah) odpověď](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a>Přidat metodu DeleteTodoItem

Přidejte `DeleteTodoItem` následující metodu:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

Odpověď `DeleteTodoItem` je [204 (Žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).

### <a name="test-the-deletetodoitem-method"></a>Otestovat metodu DeleteTodoItem

Pomocí pošťáku odstraňte položku, kterou chcete provést:

* Nastavte metodu `DELETE`na .
* Nastavte identifikátor URI objektu, který `https://localhost:5001/api/todo/1`chcete odstranit (například).
* Vyberte **Poslat**.

Ukázková aplikace umožňuje odstranit všechny položky. Však při odstranění poslední položky, je vytvořen nový konstruktor třídy modelu při příštím volání rozhraní API.

## <a name="call-the-web-api-with-javascript"></a>Volání webového rozhraní API pomocí JavaScriptu

V této části je přidána stránka HTML, která používá JavaScript k volání webového rozhraní API. jQuery iniciuje požadavek. JavaScript aktualizuje stránku podrobnostmi z odpovědi webového rozhraní API.

Nakonfigurujte aplikaci tak, aby [zobrazovala statické soubory,](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a [povolte výchozí mapování souborů](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) aktualizací *Startup.cs* s následujícím zvýrazněným kódem:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

Vytvořte složku *wwwroot* v adresáři projektu.

Přidejte soubor HTML s názvem *index.html* do adresáře *wwwroot.* Nahraďte jeho obsah následujícími značkami:

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

Přidejte soubor JavaScript s názvem *site.js* do adresáře *wwwroot.* Nahraďte jeho obsah následujícím kódem:

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

Ke místnímu testování stránky HTML může být nutná změna nastavení spuštění projektu ASP.NET Core:

* *Spusťte položku Properties\launchSettings.json*.
* Odeberte `launchUrl` vlastnost, chcete-li aplikaci vynutit otevření na *adrese index.html*&mdash;výchozího souboru projektu.

Tato ukázka volá všechny metody CRUD webového rozhraní API. Následují vysvětlení volání rozhraní API.

### <a name="get-a-list-of-to-do-items"></a>Získání seznamu položek úkolů

jQuery odešle požadavek HTTP GET do webového rozhraní API, které vrací JSON představující pole položek, které chcete. Funkce `success` zpětného volání je vyvolána, pokud je požadavek úspěšný. V zpětném volání dom je aktualizován s informacemi o do do.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a>Přidání položky s cílem

jQuery odešle požadavek HTTP POST s položkou k dokončení v textu požadavku. `accepts` Možnosti `contentType` a jsou `application/json` nastaveny tak, aby určovat typ média, které jsou přijímány a odesílány. Položka převodu je převedena na JSON pomocí [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify). Pokud rozhraní API vrátí úspěšný `getData` stavový kód, funkce je vyvolána k aktualizaci tabulky HTML.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a>Aktualizace položky s cílem

Aktualizace položky s cílem je podobná přidání. Změny `url` přidat jedinečný identifikátor položky a `type` je `PUT`.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a>Odstranění položky pro práci

Odstranění položky pro provedení se provádí nastavením `type` volání AJAX `DELETE` a zadáním jedinečného identifikátoru položky v adrese URL.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a>Přidání podpory ověřování do webového rozhraní API

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a>Další zdroje

[Zobrazit nebo stáhnout ukázkový kód pro tento kurz](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples). Podívejte [se, jak stáhnout](xref:index#how-to-download-a-sample).

Další informace najdete v následujících materiálech:

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [Verze tohoto kurzu pro YouTube](https://www.youtube.com/watch?v=TTkhEyGBfAk)

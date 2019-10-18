---
title: 'Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core'
author: rick-anderson
description: Naučte se vytvářet webové rozhraní API pomocí ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: tutorials/first-web-api
ms.openlocfilehash: b4c88f5dc7853396448a2a6122f3652f92079e68
ms.sourcegitcommit: dd026eceee79e943bd6b4a37b144803b50617583
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72541759"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a>Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core

Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Jan Wasson](https://github.com/mikewasson)

V tomto kurzu se naučíte základy vytváření webového rozhraní API pomocí ASP.NET Core.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte projekt webového rozhraní API.
> * Přidejte třídu modelu a kontext databáze.
> * Generování uživatelského rozhraní pro kontroler pomocí metod CRUD
> * Nakonfigurujte směrování, cesty URL a návratové hodnoty.
> * Zavolejte webové rozhraní API pomocí post.

Na konci máte webové rozhraní API, které může spravovat položky "k tomu" uložené v databázi.

## <a name="overview"></a>Přehled

V tomto kurzu se vytvoří webové rozhraní API obsahující následující koncové body:

|Koncový bod                  |Popis            |Text žádosti|Tělo odpovědi       |
|--------------------------|-----------------------|------------|--------------------|
|ZÍSKAT/api/TodoItems        |Získat všechny položky úkolů    |Žádné        |Pole položek úkolů|
|ZÍSKAT/api/TodoItems/{id}   |Získat položku podle ID      |Žádné        |Položka úkolů          |
|PŘÍSPĚVEK/api/TodoItems       |Přidat novou položku         |Položka úkolů  |Položka úkolů          |
|Vložit/api/TodoItems/{id}   |Aktualizovat existující položku|Položka úkolů  |Žádné                |
|Odstranit/api/TodoItems/{id}|Odstranění položky         |Žádné        |Žádné                |

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

1. V nabídce **soubor** vyberte **Nový**  > **projekt**.
1. Vyberte šablonu **ASP.NET Core webové aplikace** a klikněte na tlačítko **Další**.
1. Pojmenujte projekt *TodoApi* a klikněte na **vytvořit**.
1. V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 3,0** . Vyberte šablonu **rozhraní API** a klikněte na **vytvořit**.

![Dialogové okno VS New Project](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).
1. Změňte adresáře (`cd`) na složku, která bude obsahovat složku projektu.
1. Spusťte následující příkazy:

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

1. Pokud se zobrazí dialogové okno s dotazem, zda chcete přidat požadované prostředky do projektu, vyberte možnost **Ano**.

  Předchozí příkazy:

  * Vytvořte nový projekt webového rozhraní API a otevřete ho v Visual Studio Code.
  * Přidejte balíčky NuGet, které jsou nutné v další části.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

1. Vyberte **soubor**  > **nové řešení**.

    ![macOS nové řešení](first-web-api-mac/_static/sln.png)

1. V **části rozhraní .NET Core**  > **App**  > **API**  > **Další**.

    ![dialog pro nový projekt v macOS](first-web-api-mac/_static/1.png)
  
1. V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** vyberte **cílovou architekturu** * *.NET Core 3,0*.
1. Jako **název projektu** zadejte *TodoApi* a pak vyberte **vytvořit**.

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

Stisknutím <kbd>kombinace kláves CTRL + F5</kbd> spusťte aplikaci. Visual Studio spustí prohlížeč a přejde na `https://localhost:<port>/WeatherForecast`, kde `<port>` je náhodně zvolené číslo portu.

Pokud se zobrazí dialogové okno s dotazem, jestli byste měli důvěřovat certifikátu IIS Express, vyberte **Ano**. V dialogovém okně **Upozornění zabezpečení** , které se zobrazí jako další, vyberte **Ano**.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Stisknutím <kbd>kombinace kláves CTRL + F5</kbd> spusťte aplikaci. V prohlížeči přejdete na následující adresu URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).

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

1. V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt. Vyberte **přidat**  > **novou složku**. Pojmenujte *modely*složek.
1. Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat**  > **třídy**. Pojmenujte třídu *TodoItem* a vyberte **Přidat**.
1. Kód šablony nahraďte následujícím kódem:

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Přidejte složku s názvem *modely*.
1. Přidejte třídu `TodoItem` do složky *modely* pomocí následujícího kódu:

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

1. Klikněte pravým tlačítkem na projekt. Vyberte **přidat**  > **novou složku**. Pojmenujte *modely*složek.

    ![Nová složka](first-web-api-mac/_static/folder.png)

1. Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat**  > **nový soubor**  > **Obecné**  > **prázdná třída**.
1. Pojmenujte třídu *TodoItem*a potom klikněte na **Nový**.
1. Kód šablony nahraďte následujícím kódem:

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

Vlastnost `Id` funguje jako jedinečný klíč v relační databázi.

Třídy modelu mohou jít kdekoli v projektu, ale složka *modely* je používána konvencí.

## <a name="add-a-database-context"></a>Přidání kontextu databáze

*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro datový model. Tato třída je vytvořena odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a>Přidat Microsoft. EntityFrameworkCore. SqlServer

1. V nabídce **nástroje** vyberte **správce balíčků NuGet > spravovat balíčky NuGet pro řešení**.
1. Vyberte kartu **Procházet** a potom do vyhledávacího pole zadejte **Microsoft. EntityFrameworkCore. SqlServer** .
1. V levém podokně vyberte **Microsoft. EntityFrameworkCore. SqlServer** .
1. Zaškrtněte políčko **projekt** v pravém podokně a pak vyberte **nainstalovat**.
1. Pomocí předchozích pokynů přidejte balíček NuGet `Microsoft.EntityFrameworkCore.InMemory`.

![Správce balíčků NuGet](first-web-api/_static/vs3nuget.png)

## <a name="add-the-todocontext-database-context"></a>Přidání kontextu databáze TodoContext

* Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat**  > **třídy**. Pojmenujte třídu *TodoContext* a klikněte na **Přidat**.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

* Přidejte třídu `TodoContext` do složky *modely* .

---

* Zadejte následující kód:

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>Registrace kontextu databáze

V ASP.NET Core musí být služby, jako je například kontext databáze, registrovány pomocí kontejneru [Injektáže (di)](xref:fundamentals/dependency-injection) . Kontejner poskytuje službu pro řadiče.

Aktualizujte *Startup.cs* o následující zvýrazněný kód:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

Předchozí kód:

* Odebere nepoužívané deklarace `using`.
* Přidá kontext databáze do kontejneru DI.
* Určuje, že kontext databáze bude používat databázi v paměti.

## <a name="scaffold-a-controller"></a>Generování uživatelského rozhraní kontroleru

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Klikněte pravým tlačítkem na složku *řadiče* .
1. Vyberte **přidat**  > **Nová vygenerovaná položka**.
1. Vyberte možnost **kontroler API s akcemi, pomocí Entity Framework**a pak vyberte **Přidat**.
1. V dialogovém okně **Přidání kontroleru rozhraní API pomocí akcí Entity Framework** :
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
* @No__t_0 generování uživatelského rozhraní.

---

Generovaný kód:

* Definuje třídu kontroleru rozhraní API bez metod.
* Upraví třídu atributem [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) . Tento atribut označuje, že kontroler reaguje na požadavky webového rozhraní API. Informace o konkrétním chování, které atribut povoluje, naleznete v tématu <xref:web-api/index>.
* Pomocí DI vloží kontext databáze (`TodoContext`) do kontroleru. Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v kontroleru.

## <a name="examine-the-posttodoitem-create-method"></a>Projděte si metodu PostTodoItem Create.

Nahraďte příkaz return v `PostTodoItem` pro použití operátoru [nameof](/dotnet/csharp/language-reference/operators/nameof) :

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

Předchozí kód je metoda HTTP POST, která je označena atributem [[HTTPPOST]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) . Metoda získá hodnotu položky k seřízení z těla požadavku HTTP.

Metoda <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:

* Pokud je úspěšná, vrátí stavový kód HTTP 201. HTTP 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru.
* Přidá hlavičku [umístění](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) k odpovědi. Hlavička `Location` Určuje [identifikátor URI](https://developer.mozilla.org/docs/Glossary/URI) nově vytvořené položky. Další informace najdete v tématu [10.2.2 201 vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* Odkazuje na akci `GetTodoItem` pro vytvoření identifikátoru URI `Location` hlavičky. Klíčové C# slovo `nameof` slouží k tomu, aby se předešlo zakódování názvu akce ve volání `CreatedAtAction`.

### <a name="install-postman"></a>Nainstalovat post

V tomto kurzu se používá post k testování webového rozhraní API.

1. Nainstalovat [post](https://www.getpostman.com/downloads/)
1. Spusťte webovou aplikaci.
1. Spusťte post.
1. Zakázat **ověřování certifikátu SSL**
1. V**Nastavení** **souboru**  >  (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.

    > [!WARNING]
    > Po otestování kontroleru znovu povolte ověření certifikátu SSL.

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a>PostTodoItem testu s použitím post

1. Vytvoří novou žádost.
1. Nastavte metodu HTTP na `POST`.
1. Vyberte kartu **tělo** .
1. Vyberte **nezpracovaný** přepínač.
1. Nastavte typ na **JSON (Application/JSON)** .
1. Do textu žádosti zadejte JSON pro položku úkolů:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

1. Vyberte **Odeslat**.

  ![Poslat pomocí žádosti o vytvoření](first-web-api/_static/create.png)

### <a name="test-the-location-header-uri"></a>Otestování identifikátoru URI hlavičky umístění

1. V podokně **odpověď** vyberte kartu **hlavičky** .
1. Zkopírujte hodnotu hlavičky **umístění** :

    ![Karta hlavičky v konzole pro odesílání](first-web-api/_static/create.png)

1. Nastavte metodu, která má být ZÍSKÁNa.
1. Vložte identifikátor URI (například `https://localhost:5001/api/TodoItems/1`).
1. Vyberte **Odeslat**.

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

1. Vytvoří novou žádost.
1. Nastavte metodu HTTP na **Get**.
1. Nastavte adresu URL požadavku na `https://localhost:<port>/api/TodoItems`. Například `https://localhost:5001/api/TodoItems`.
1. Nastavte v příspěvku **dva zobrazení podokna** .
1. Vyberte **Odeslat**.

Tato aplikace používá databázi v paměti. Pokud je aplikace zastavená a spuštěná, předchozí požadavek GET nevrátí žádná data. Pokud se nevrátí žádná data, [odešlete](#post) data do aplikace.

## <a name="routing-and-url-paths"></a>Směrování a cesty URL

Atribut [[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) označuje metodu, která reaguje na požadavek HTTP GET. Cesta URL pro každou metodu je konstruována takto:

* Začněte s řetězcem šablony v atributu `Route` kontroleru:

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* Nahraďte `[controller]` názvem kontroleru, který je podle konvence názvem třídy kontroleru minus přípona Controller. V této ukázce je název třídy kontroleru **TodoItems**Controller, takže název kontroleru je "TodoItems". [Směrování](xref:mvc/controllers/routing) ASP.NET Core rozlišuje malá a velká písmena.
* Pokud má atribut `[HttpGet]` šablonu směrování (například `[HttpGet("products")]`), přidejte ji k cestě. Tato ukázka nepoužívá šablonu. Další informace najdete v tématu [Směrování atributů s atributy http [příkaz]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

V následující metodě `GetTodoItem` `"{id}"` je zástupnou proměnnou pro jedinečný identifikátor položky k provedení. Když je vyvoláno `GetTodoItem`, hodnota `"{id}"` v adrese URL je poskytnuta metodě v parametru `id`.

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>Vrácené hodnoty

Návratový typ `GetTodoItems` a `GetTodoItem` metody je [ActionResult \<T > typ](xref:web-api/action-return-types#actionresultt-type). ASP.NET Core automaticky serializovat objekt do formátu [JSON](https://www.json.org/) a zapíše JSON do textu zprávy s odpovědí. Kód odpovědi pro tento návratový typ je 200, za předpokladu, že neexistují žádné neošetřené výjimky. Neošetřené výjimky jsou přeloženy na 5xx chyby.

návratové typy `ActionResult` mohou představovat široké spektrum stavových kódů HTTP. @No__t_0 může například vracet dvě různé stavové hodnoty:

* Pokud žádná položka neodpovídá požadovanému ID, vrátí metoda kód chyby 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound>.
* V opačném případě metoda vrátí 200 text odpovědi JSON. Vrácení `item` způsobí odpověď HTTP 200.

## <a name="the-puttodoitem-method"></a>Metoda PutTodoItem

Projděte si metodu `PutTodoItem`:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

`PutTodoItem` se podobá `PostTodoItem`, s tím rozdílem, že používá PUT HTTP. Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). V souladu se specifikací HTTP vyžaduje požadavek PUT klientovi, aby odesílal celou aktualizovanou entitu, a ne jenom změny. K podpoře částečných aktualizací použijte [opravu http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).

Pokud se zobrazí chyba s voláním `PutTodoItem`, zavoláním `GET` zajistěte, aby v databázi byla nějaká položka.

### <a name="test-the-puttodoitem-method"></a>Test metody PutTodoItem

Tato ukázka používá databázi v paměti, která musí být inicializována při každém spuštění aplikace. Před provedením volání PUT musí existovat položka v databázi. Před provedením volání metody PUT zavolejte funkci GET k instalování položky v databázi.

Aktualizuje položku úkolů s ID 1. Nastavte jeho název na "informační ryby".

```json
{
  "ID":1,
  "name":"feed fish",
  "isComplete":true
}
```

Na následujícím obrázku je znázorněná aktualizace po odeslání:

![Konzola pro publikování zobrazující 204 (bez obsahu)](first-web-api/_static/pmcput.png)

## <a name="the-deletetodoitem-method"></a>Metoda DeleteTodoItem

Projděte si metodu `DeleteTodoItem`:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

@No__t_0 odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).

### <a name="test-the-deletetodoitem-method"></a>Test metody DeleteTodoItem

Odstranění položky úkolů pomocí metody post:

1. Nastavte metodu na `DELETE`.
1. Nastavte identifikátor URI objektu, který má být odstraněn (například `https://localhost:5001/api/TodoItems/1`).
1. Vyberte **Odeslat**.

## <a name="call-the-web-api-with-javascript"></a>Volání webového rozhraní API pomocí JavaScriptu

Viz [kurz: volání ASP.NET Core webového rozhraní API pomocí JavaScriptu](xref:tutorials/web-api-javascript).

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a>Přidání podpory ověřování do webového rozhraní API

Viz kurz k [IdentityServer4](https://identityserver4.readthedocs.io/en/latest/quickstarts/0_overview.html) .

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

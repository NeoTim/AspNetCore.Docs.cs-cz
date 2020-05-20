---
page_type: sample
description: V tomto kurzu se dozvíte, jak vytvořit webové rozhraní API ASP.NET Core pomocí databáze NoSQL v databázi MongoDB.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
urlFragment: aspnetcore-webapi-mongodb
ms.openlocfilehash: 6f6022bee678af92066f45032b43b6b87e5f901e
ms.sourcegitcommit: 7a42bc1e594de36c854fd4363c11821548a9efa7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83608665"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a>Vytvoření webového rozhraní API pomocí ASP.NET Core a MongoDB

V tomto kurzu se vytvoří webové rozhraní API, které provádí operace vytvoření, čtení, aktualizace a odstranění (CRUD) v databázi [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL.

V tomto kurzu:

* Konfigurace MongoDB
* Vytvoření databáze MongoDB
* Definování kolekce a schématu MongoDB
* Provádění operací CRUD MongoDB z webového rozhraní API
* Přizpůsobení serializace JSON

## <a name="prerequisites"></a>Požadavky

* [.NET Core SDK 3.0 nebo novější](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio 2019 Preview](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=community&ch=pre&rel=16&utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019preview) s úlohou **vývoje ASP.NET a webu**
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

## <a name="configure-mongodb"></a>Konfigurace MongoDB

Pokud používáte Windows, MongoDB se nainstaluje ve výchozím nastavení v *C: \\ Program Files \\ MongoDB* . Přidejte *C: \\ Program Files \\ MongoDB \\ Server \\ \< version_number>\\ bin* do `Path` proměnné prostředí. Tato změna umožňuje přístup k MongoDB odkudkoli na svém vývojovém počítači.

Pomocí prostředí Mongo v následujících krocích můžete vytvořit databázi, vytvořit kolekce a uložit dokumenty. Další informace o příkazech prostředí Mongo najdete v tématu [práce s prostředím Mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).

1. Pro uložení dat vyberte adresář na vývojovém počítači. Například *C: \\ BooksData* ve Windows. Pokud adresář neexistuje, vytvořte ho. Prostředí Mongo nevytváří nové adresáře.
1. Otevřete příkazové prostředí. Spusťte následující příkaz pro připojení k MongoDB na výchozím portu 27017. Nezapomeňte nahradit `<data_directory_path>` adresář, který jste zvolili v předchozím kroku.

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. Otevřete jinou instanci příkazového prostředí. Připojte se k výchozí testovací databázi spuštěním následujícího příkazu:

    ```console
    mongo
    ```

1. V příkazovém prostředí spusťte následující příkaz:

    ```console
    use BookstoreDb
    ```

    Pokud ještě neexistuje, vytvoří se databáze s názvem *BookstoreDb* . Pokud databáze existuje, připojení je otevřeno pro transakce.

1. Vytvořte `Books` kolekci pomocí následujícího příkazu:

    ```console
    db.createCollection('Books')
    ```

    Zobrazí se následující výsledek:

    ```console
    { "ok" : 1 }
    ```

1. Definujte schéma pro `Books` kolekci a vložte dva dokumenty pomocí následujícího příkazu:

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    Zobrazí se následující výsledek:

    ```console
    {
      "acknowledged" : true,
      "insertedIds" : [
        ObjectId("5bfd996f7b8e48dc15ff215d"),
        ObjectId("5bfd996f7b8e48dc15ff215e")
      ]
    }
    ```

  > [!NOTE]
  > ID zobrazené v tomto článku se při spuštění této ukázky neshoduje s ID.

1. Zobrazte dokumenty v databázi pomocí následujícího příkazu:

    ```console
    db.Books.find({}).pretty()
    ```

    Zobrazí se následující výsledek:

    ```console
    {
      "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
      "Name" : "Design Patterns",
      "Price" : 54.93,
      "Category" : "Computers",
      "Author" : "Ralph Johnson"
    }
    {
      "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
      "Name" : "Clean Code",
      "Price" : 43.15,
      "Category" : "Computers",
      "Author" : "Robert C. Martin"
    }
    ```

    Schéma přidá do každého dokumentu automaticky generovanou `_id` vlastnost typu `ObjectId` .

Databáze je připravena. Můžete začít vytvářet ASP.NET Core webového rozhraní API.

## <a name="create-the-aspnet-core-web-api-project"></a>Vytvoření projektu webového rozhraní API ASP.NET Core

1. Přejít na **soubor**  >  **Nový**  >  **projekt**.
1. Vyberte ASP.NET Core typ projektu **webové aplikace** a vyberte **Další**.
1. Pojmenujte projekt *BooksApi*a vyberte **vytvořit**.
1. Vyberte cílové rozhraní **.NET Core** a **ASP.NET Core 3,0**. Vyberte šablonu projektu **rozhraní API** a vyberte **vytvořit**.
1. Navštivte [galerii NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) a určete nejnovější stabilní verzi ovladače .NET pro MongoDB. V okně **konzoly Správce balíčků** přejděte do kořenového adresáře projektu. Spuštěním následujícího příkazu nainstalujte ovladač .NET pro MongoDB:

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

## <a name="add-an-entity-model"></a>Přidání modelu entity

1. Přidejte adresář *modelů* do kořenového adresáře projektu.
1. Přidejte `Book` třídu do adresáře *modelů* s následujícím kódem:

    ```csharp
    using MongoDB.Bson;
    using MongoDB.Bson.Serialization.Attributes;

    namespace BooksApi.Models
    {
        public class Book
        {
            [BsonId]
            [BsonRepresentation(BsonType.ObjectId)]
            public string Id { get; set; }

            [BsonElement("Name")]
            public string BookName { get; set; }

            public decimal Price { get; set; }

            public string Category { get; set; }

            public string Author { get; set; }
        }
    }
    ```

    V předchozí třídě `Id` vlastnost:

    * Je vyžadován pro mapování objektu modulu CLR (Common Language Runtime) na kolekci MongoDB.
    * Je s poznámkami [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) k označení této vlastnosti jako primárního klíče dokumentu.
    * Je opatřen s poznámkami, [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) aby bylo možné předat parametr jako typ `string` namísto struktury [objectID](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) . Mongo zpracovává převod z `string` na `ObjectId` .

    `BookName`Vlastnost je označena [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) atributem. Hodnota atributu `Name` představuje název vlastnosti v kolekci MongoDB.

## <a name="add-a-configuration-model"></a>Přidat konfigurační model

1. Do souboru *appSettings. JSON*přidejte následující hodnoty konfigurace databáze:

    ```javascript
    {
      "BookstoreDatabaseSettings": {
        "BooksCollectionName": "Books",
        "ConnectionString": "mongodb://localhost:27017",
        "DatabaseName": "BookstoreDb"
      },

    ```

1. Přidejte soubor *BookstoreDatabaseSettings.cs* do adresáře *Models* s následujícím kódem:

    ```csharp
    namespace BooksApi.Models
    {
        public class BookstoreDatabaseSettings : IBookstoreDatabaseSettings
        {
            public string BooksCollectionName { get; set; }
            public string ConnectionString { get; set; }
            public string DatabaseName { get; set; }
        }

        public interface IBookstoreDatabaseSettings
        {
            string BooksCollectionName { get; set; }
            string ConnectionString { get; set; }
            string DatabaseName { get; set; }
        }
    }
    ```

    Předchozí `BookstoreDatabaseSettings` Třída se používá k uložení hodnot vlastností souboru *appSettings. JSON* `BookstoreDatabaseSettings` . Názvy vlastností JSON a C# jsou pojmenovány stejně, aby bylo možné zjednodušit proces mapování.

1. Přidejte následující zvýrazněný kód do `Startup.ConfigureServices` :

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddControllers();
    }
    ```

    V předchozím kódu:

    * Instance konfigurace, na kterou se váže sekce souboru *appSettings. JSON* , `BookstoreDatabaseSettings` se registruje v kontejneru injektáže (di). Například `BookstoreDatabaseSettings` `ConnectionString` vlastnost objektu je naplněna `BookstoreDatabaseSettings:ConnectionString` vlastností v souboru *appSettings. JSON*.
    * `IBookstoreDatabaseSettings`Rozhraní je zaregistrované v di s [životností služby](xref:fundamentals/dependency-injection#service-lifetimes)typu singleton. Při vložení se instance rozhraní překládá na `BookstoreDatabaseSettings` objekt.

1. Pro vyřešení odkazů a přidejte následující kód na začátek *Startup.cs* `BookstoreDatabaseSettings` `IBookstoreDatabaseSettings` :

    ```csharp
    using BooksApi.Models;
    ```

## <a name="add-a-crud-operations-service"></a>Přidání služby operace CRUD

1. Přidejte adresář *služeb* do kořenového adresáře projektu.
1. `BookService`Do adresáře *služby* přidejte třídu s následujícím kódem:

    ```csharp
    using BooksApi.Models;
    using MongoDB.Driver;
    using System.Collections.Generic;
    using System.Linq;

    namespace BooksApi.Services
    {
        public class BookService
        {
            private readonly IMongoCollection<Book> _books;

            public BookService(IBookstoreDatabaseSettings settings)
            {
                var client = new MongoClient(settings.ConnectionString);
                var database = client.GetDatabase(settings.DatabaseName);

                _books = database.GetCollection<Book>(settings.BooksCollectionName);
            }

            public List<Book> Get() =>
                _books.Find(book => true).ToList();

            public Book Get(string id) =>
                _books.Find<Book>(book => book.Id == id).FirstOrDefault();

            public Book Create(Book book)
            {
                _books.InsertOne(book);
                return book;
            }

            public void Update(string id, Book bookIn) =>
                _books.ReplaceOne(book => book.Id == id, bookIn);

            public void Remove(Book bookIn) =>
                _books.DeleteOne(book => book.Id == bookIn.Id);

            public void Remove(string id) =>
                _books.DeleteOne(book => book.Id == id);
        }
    }
    ```

    V předchozím kódu `IBookstoreDatabaseSettings` je instance načtena z di přes injektáže konstruktoru. Tento postup poskytuje přístup k hodnotám konfigurace *appSettings. JSON* , které byly přidány do oddílu [přidat konfigurační model](#add-a-configuration-model) .

1. Přidejte následující zvýrazněný kód do `Startup.ConfigureServices` :

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddSingleton<BookService>();

        services.AddControllers();
    }
    ```

    V předchozím kódu `BookService` je třída registrována pomocí příkazu di k podpoře injektáže konstruktoru v rámci využívání tříd. Doba životnosti služby singleton je nejvhodnější `BookService` , protože používá přímou závislost na `MongoClient` . Podle oficiálních [zásad opětovného použití pro klienty Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use) `MongoClient` by se měla registrovat v di s životností služby typu singleton.

1. Přidejte následující kód na začátek *Startup.cs* k vyřešení `BookService` odkazu:


    ```csharp
    using BooksApi.Services;
    ```

`BookService`Třída používá následující `MongoDB.Driver` členy k provádění operací CRUD proti databázi:

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Přečte instanci serveru pro provádění databázových operací. K konstruktoru této třídy je poskytnutý připojovací řetězec MongoDB:

    ```csharp
    public BookService(IBookstoreDatabaseSettings settings)
    {
        var client = new MongoClient(settings.ConnectionString);
        var database = client.GetDatabase(settings.DatabaseName);

        _books = database.GetCollection<Book>(settings.BooksCollectionName);
    }
    ```

* [IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Představuje databázi Mongo pro provádění operací. V tomto kurzu se používá obecná metoda [GetCollection \< TDocument> (Collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) na rozhraní pro získání přístupu k datům v určité kolekci. Provede operace CRUD proti kolekci po volání této metody. Ve `GetCollection<TDocument>(collection)` volání metody:
  * `collection`představuje název kolekce.
  * `TDocument`představuje typ objektu CLR uložený v kolekci.

`GetCollection<TDocument>(collection)`Vrátí objekt [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) představující kolekci. V tomto kurzu jsou v kolekci vyvolány následující metody:

* [DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Odstraní jeden dokument, který odpovídá zadaným kritériím hledání.
* [Najít \< TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; vrátí všechny dokumenty v kolekci, které odpovídají zadaným kritériím hledání.
* [InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Vloží zadaný objekt jako nový dokument v kolekci.
* [ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Nahradí jediný dokument, který odpovídá zadaným kritériím hledání, zadaným objektem.

## <a name="add-a-controller"></a>Přidání kontroleru

Přidejte `BooksController` třídu do adresáře *Controllers* pomocí následujícího kódu:

```csharp
using BooksApi.Models;
using BooksApi.Services;
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;

namespace BooksApi.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class BooksController : ControllerBase
    {
        private readonly BookService _bookService;

        public BooksController(BookService bookService)
        {
            _bookService = bookService;
        }

        [HttpGet]
        public ActionResult<List<Book>> Get() =>
            _bookService.Get();

        [HttpGet("{id:length(24)}", Name = "GetBook")]
        public ActionResult<Book> Get(string id)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            return book;
        }

        [HttpPost]
        public ActionResult<Book> Create(Book book)
        {
            _bookService.Create(book);

            return CreatedAtRoute("GetBook", new { id = book.Id.ToString() }, book);
        }

        [HttpPut("{id:length(24)}")]
        public IActionResult Update(string id, Book bookIn)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            _bookService.Update(id, bookIn);

            return NoContent();
        }

        [HttpDelete("{id:length(24)}")]
        public IActionResult Delete(string id)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            _bookService.Remove(book.Id);

            return NoContent();
        }
    }
}
```

Předchozí kontroler webového rozhraní API:

* Používá `BookService` třídu k provádění operací CRUD.
* Obsahuje metody akcí, které podporují požadavky HTTP GET, POST, PUT a DELETE.
* Volání <xref:System.Web.Http.ApiController.CreatedAtRoute*> v `Create` metodě Action vrátí odpověď [http 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) . Stavový kód 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru. `CreatedAtRoute`Přidá také `Location` hlavičku do odpovědi. `Location`Hlavička Určuje identifikátor URI nově vytvořené knihy.

## <a name="test-the-web-api"></a>Testování webového rozhraní API

1. Sestavte a spusťte aplikaci.

1. Přejděte na `http://localhost:<port>/api/books` k otestování metody akce bez parametrů řadiče `Get` . Zobrazí se následující odpověď JSON:

    ```json
    [
      {
        "id":"5bfd996f7b8e48dc15ff215d",
        "bookName":"Design Patterns",
        "price":54.93,
        "category":"Computers",
        "author":"Ralph Johnson"
      },
      {
        "id":"5bfd996f7b8e48dc15ff215e",
        "bookName":"Clean Code",
        "price":43.15,
        "category":"Computers",
        "author":"Robert C. Martin"
      }
    ]
    ```

1. Přejděte na `http://localhost:<port>/api/books/{id here}` k otestování metody akce, která je přetížena řadičem `Get` . Zobrazí se následující odpověď JSON:

    ```json
    {
      "id":"{ID}",
      "bookName":"Clean Code",
      "price":43.15,
      "category":"Computers",
      "author":"Robert C. Martin"
    }
    ```

## <a name="configure-json-serialization-options"></a>Konfigurace možností serializace JSON

Existují dvě podrobnosti o tom, jak můžete změnit informace o odpovědích JSON vrácených v části [Test webového rozhraní API](#test-the-web-api) :

* Názvy vlastností výchozí ve stylu CamelCase velká a malá písmena by se měly změnit tak, aby odpovídaly názvu vlastností objektu CLR na velká písmena Pascal.
* `bookName`Vlastnost by měla být vrácena jako `Name` .

Chcete-li splnit předchozí požadavky, proveďte následující změny:

1. JSON.NET se odebral ze sdílené architektury ASP.NET. Přidejte odkaz na balíček do [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) .

1. V `Startup.ConfigureServices` , řetězení následujícího zvýrazněného kódu k `AddMvc` volání metody:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddSingleton<BookService>();

        services.AddControllers()
            .AddNewtonsoftJson(options => options.UseMemberCasing());
    }
    ```

    V předchozí změně názvy vlastností v serializovaných odpovědích JSON webového rozhraní API odpovídají jejich odpovídajícím názvům vlastností v typu objektu CLR. Například `Book` vlastnost třídy je `Author` serializována jako `Author` .

1. V části *Models/Book. cs*poznámku k `BookName` vlastnosti s následujícím [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) atributem:

    ```csharp
    [BsonElement("Name")]
    [JsonProperty("Name")]
    public string BookName { get; set; }
    ```

    `[JsonProperty]`Hodnota atributu `Name` představuje název vlastnosti v serializované odpovědi JSON webového rozhraní API.

1. Přidejte následující kód na začátek *modelů/Book. cs* pro vyřešení `[JsonProperty]` odkazu na atribut:

    ```csharp
    using Newtonsoft.Json;
    ```

1. Opakujte kroky definované v části [Test webového rozhraní API](#test-the-web-api) . Všimněte si rozdílu v názvech vlastností JSON.

## <a name="next-steps"></a>Další kroky

Další informace o vytváření ASP.NET Core webových rozhraní API najdete v následujících zdrojích informací:

* [YouTube verze tohoto článku](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* [Vytváření webových rozhraní API pomocí ASP.NET Core](https://docs.microsoft.com/aspnet/core/web-api/index?view=aspnetcore-3.0)

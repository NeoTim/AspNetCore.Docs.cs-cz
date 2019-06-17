---
title: Vytvoření webového rozhraní API pomocí ASP.NET Core využívající databázi MongoDB
author: prkhandelwal
description: Tento kurz ukazuje vytvoření webového rozhraní ASP.NET Core API pomocí databáze MongoDB NoSQL.
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 06/10/2019
uid: tutorials/first-mongo-app
ms.openlocfilehash: 426b4c0dee290153b9b1bf83deec14fa728183cb
ms.sourcegitcommit: f5762967df3be8b8c868229e679301f2f7954679
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67048079"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a>Vytvoření webového rozhraní API pomocí ASP.NET Core využívající databázi MongoDB

Podle [Pratik Khandelwal](https://twitter.com/K2Prk) a [Scott Addie](https://twitter.com/Scott_Addie)

Tento kurz vytvoří webového rozhraní API, který provádí operace vytvoření, čtení, aktualizace a odstranění (CRUD) [MongoDB](https://www.mongodb.com/what-is-mongodb) databáze NoSQL.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nakonfigurovat MongoDB
> * Vytvoření databáze MongoDB
> * Definování kolekce MongoDB a schématu
> * Provádění operací MongoDB CRUD z webového rozhraní API
> * Vlastní serializace JSON

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample) ([stažení](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* [.NET core SDK 2.2 nebo vyšší](https://www.microsoft.com/net/download/all)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s **vývoj pro ASP.NET a web** pracovního vytížení
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [.NET core SDK 2.2 nebo vyšší](https://www.microsoft.com/net/download/all)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* [.NET core SDK 2.2 nebo vyšší](https://www.microsoft.com/net/download/all)
* [Visual Studio pro Mac verze 7,7 nebo novější](https://visualstudio.microsoft.com/downloads/)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a>Nakonfigurovat MongoDB

Pokud používáte Windows, databáze MongoDB nainstaluje na *C:\\Program Files\\MongoDB* ve výchozím nastavení. Přidat *C:\\Program Files\\MongoDB\\Server\\\<version_number >\\bin* k `Path` proměnné prostředí. Tato změna umožňuje MongoDB přístup z libovolného místa na vývojovém počítači.

Použití prostředí mongo v následujících krocích k vytvoření databáze, ujistěte se, kolekce a ukládat dokumenty. Další informace o příkazech prostředí mongo naleznete v tématu [práce s mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).

1. Vyberte adresář na vývojovém počítači pro ukládání dat. Například *C:\\BooksData* na Windows. Vytvořte adresář, pokud neexistuje. Prostředí mongo nebude vytvářet nové adresáře.
1. Otevřete příkazové okno. Spusťte následující příkaz pro připojení k MongoDB na výchozím portu 27017. Nezapomeňte nahradit `<data_directory_path>` s adresáři, kterou jste zvolili v předchozím kroku.

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. Otevřete jiná instance příkazového prostředí. Připojení k databázi testu výchozí spuštěním následujícího příkazu:

    ```console
    mongo
    ```

1. Spuštěním následujícího příkazu v příkazovém řádku:

    ```console
    use BookstoreDb
    ```

    Pokud ještě neexistuje, databázi s názvem *BookstoreDb* se vytvoří. Pokud databáze neexistuje, je připojení otevřené transakce.

1. Vytvoření `Books` kolekce pomocí následujícího příkazu:

    ```console
    db.createCollection('Books')
    ```

    Zobrazí se následující výsledek:

    ```console
    { "ok" : 1 }
    ```

1. Definovat schéma pro `Books` kolekce a vložte dva dokumenty pomocí následujícího příkazu:

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

    Přidá automaticky generované schéma `_id` vlastnost typu `ObjectId` pro každý dokument.

Databáze je připravena. Můžete začít vytvářet webové rozhraní API ASP.NET Core.

## <a name="create-the-aspnet-core-web-api-project"></a>Vytvoření projektu webové rozhraní API ASP.NET Core

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Přejděte na **souboru** > **nové** > **projektu**.
1. Vyberte **webové aplikace ASP.NET Core** typ projektu a vyberte **Další**.
1. Pojmenujte projekt *BooksApi*a vyberte **vytvořit**.
1. Vyberte **.NET Core** Cílová architektura a **2.2 technologie ASP.NET Core**. Vyberte **API** šablony projektu a vyberte **vytvořit**.
1. Přejděte [Galerie NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) určit nejnovější stabilní verze ovladače .NET pro MongoDB. V **Konzola správce balíčků** okno, přejděte do kořenového adresáře projektu. Spusťte následující příkaz k instalaci ovladače .NET pro MongoDB:

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. V příkazovém řádku spusťte následující příkazy:

    ```console
    dotnet new webapi -o BooksApi
    code BooksApi
    ```

    Nový ASP.NET Core webové rozhraní API projekt cílí na .NET Core je generována a otevřít ve Visual Studio Code.

1. Po stavový řádek OmniSharp bezpečnostní opatření ikona se změní na zelenou, dialogové okno s dotazem **'BooksApi' chybí požadované prostředky pro sestavení a ladění. Přidat?** . Vyberte **Ano**.
1. Přejděte [Galerie NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) určit nejnovější stabilní verze ovladače .NET pro MongoDB. Otevřít **integrovaný terminál** a přejděte do kořenového adresáře projektu. Spusťte následující příkaz k instalaci ovladače .NET pro MongoDB:

    ```console
    dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
    ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. Přejděte na **souboru** > **nové řešení** >  **.NET Core** > **aplikace**.
1. Vyberte **webového rozhraní API ASP.NET Core** C# šablony projektu a vyberte **Další**.
1. Vyberte **.NET Core 2.2** z **Cílová architektura** rozevíracího seznamu a vyberte **Další**.
1. Zadejte *BooksApi* pro **název projektu**a vyberte **vytvořit**.
1. V **řešení** panel, klikněte pravým tlačítkem projekt **závislosti** uzel a vyberte možnost **přidat balíčky**.
1. Zadejte *MongoDB.Driver* do vyhledávacího pole, vyberte *MongoDB.Driver* balíček a vyberte **přidat balíček**.
1. Vyberte **přijmout** tlačítko **přijetí licence** dialogového okna.

---

## <a name="add-an-entity-model"></a>Přidání modelu entity

1. Přidat *modely* adresáře do kořenového adresáře projektu.
1. Přidat `Book` třídu *modely* adresáře s následujícím kódem:

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

    Ve třídě předchozí `Id` vlastnost:
    
    * Je vyžadován pro mapování objektu Common Language Runtime (CLR) ke kolekci MongoDB.
    * Je opatřen poznámkou [[BsonId]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) se označí jako primární klíč dokumentu této vlastnosti.
    * Je opatřen poznámkou [[BsonRepresentation(BsonType.ObjectId)]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) povolit předávání parametru jako typ `string` místo [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) struktury. Mongo zpracovává server převod z `string` k `ObjectId`.
    
    `BookName` Vlastnost je opatřen poznámkou [[BsonElement]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) atribut. Hodnota atributu `Name` představuje název vlastnosti v kolekci MongoDB.

## <a name="add-a-configuration-model"></a>Přidat konfigurační model

1. Přidejte následující hodnoty konfigurace databáze na *appsettings.json*:

    [!code-json[](first-mongo-app/sample/BooksApi/appsettings.json?highlight=2-6)]

1. Přidat *BookstoreDatabaseSettings.cs* do souboru *modely* adresáře s následujícím kódem:

    [!code-csharp[](first-mongo-app/sample/BooksApi/Models/BookstoreDatabaseSettings.cs)]

    Předchozí `BookstoreDatabaseSettings` třída se používá k ukládání *appsettings.json* souboru `BookstoreDatabaseSettings` hodnot vlastností. Ve formátu JSON a C# názvy vlastností jsou pojmenované stejně jako k usnadnění procesu mapování.

1. Přidejte následující zvýrazněný kód do `Startup.ConfigureServices`:

    [!code-csharp[](first-mongo-app/sample_snapshot/BooksApi/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

    V předchozím kódu:

    * Instance konfigurace, ke kterému *appsettings.json* souboru `BookstoreDatabaseSettings` oddílu vazby je registrován v kontejnerů Dependency Injection (DI). Například `BookstoreDatabaseSettings` objektu `ConnectionString` se vyplní vlastnost `BookstoreDatabaseSettings:ConnectionString` vlastnost *appsettings.json*.
    * `IBookstoreDatabaseSettings` Rozhraní je zaregistrován v DI jednotlivý prvek [doba platnosti služby](xref:fundamentals/dependency-injection#service-lifetimes). Při vložený, instanci rozhraní překládá `BookstoreDatabaseSettings` objektu.

1. Přidejte následující kód k hornímu okraji *Startup.cs* přeložit `BookstoreDatabaseSettings` a `IBookstoreDatabaseSettings` odkazy:

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a>Přidat službu operace CRUD

1. Přidat *služby* adresáře do kořenového adresáře projektu.
1. Přidat `BookService` třídu *služby* adresáře s následujícím kódem:

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceClass)]

    V předchozím kódu `IBookstoreDatabaseSettings` instance je načten z DI prostřednictvím konstruktoru vkládání. Tento postup poskytuje přístup k *appsettings.json* konfigurační hodnoty, které byly přidány [přidat konfigurační model](#add-a-configuration-model) části.

1. Přidejte následující zvýrazněný kód do `Startup.ConfigureServices`:

    [!code-csharp[](first-mongo-app/sample_snapshot/BooksApi/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

    V předchozím kódu `BookService` třída je registrována s DI pro podporu vkládání konstruktor přijímací třídy. Je nejvhodnější doba platnosti služby typu singleton protože `BookService` převezme přímou závislost `MongoClient`. Za official je přínosné pro [pokyny pro opakované použití klient Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` by měly být zaregistrovány v DI s životností služby typu singleton.

1. Přidejte následující kód k hornímu okraji *Startup.cs* přeložit `BookService` odkaz:

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_UsingBooksApiServices)]

`BookService` Třída používá následující `MongoDB.Driver` členy k provádění operací CRUD proti databázi:

* [Položky MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; načte instance serveru k provedení operace databáze. Konstruktor Tato třída poskytuje připojovacího řetězce MongoDB:

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* [IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; představuje databázi Mongodb pro provádění operací. Tento kurz používá Obecné [GetCollection\<TDocument > (kolekce)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) metoda v rozhraní k získání přístupu k datům v určité kolekci. Provádění operací CRUD proti kolekci po volání této metody. V `GetCollection<TDocument>(collection)` volání metody:
  * `collection` představuje název kolekce.
  * `TDocument` představuje typ objektu CLR uložená v kolekci.

`GetCollection<TDocument>(collection)` Vrátí [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) objekt představující kolekci. V tomto kurzu jsou vyvolány následující metody na kolekci:

* [DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; odstraní odpovídá kritériím hledání zadaný jednotlivý dokument.
* [Najít\<TDocument >](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; vrátí všechny dokumenty v kolekci odpovídá kritériím hledání zadaná.
* [InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; vloží zadaný objekt jako nový dokument v kolekci.
* [ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; nahradí jeden dokument, který odpovídá kritériím hledání zadaná pomocí zadaného objektu.

## <a name="add-a-controller"></a>Přidání kontroleru

Přidat `BooksController` třídu *řadiče* adresáře s následujícím kódem:

[!code-csharp[](first-mongo-app/sample/BooksApi/Controllers/BooksController.cs)]

Předchozí kontroler web API:

* Používá `BookService` pro provádění operací CRUD.
* Obsahuje metody akce, který podporuje požadavky GET, POST, PUT a DELETE HTTP.
* Volání <xref:System.Web.Http.ApiController.CreatedAtRoute*> v `Create` metoda akce se vraťte [HTTP 201](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) odpovědi. Stavový kód 201 je standardní odpověď pro metodu POST protokolu HTTP, která vytvoří nový prostředek na serveru. `CreatedAtRoute` Přidá také `Location` hlavičky odpovědi. `Location` Hlavičky určuje identifikátor URI nově vytvořeného knihy.

## <a name="test-the-web-api"></a>Test webové rozhraní API

1. Sestavte a spusťte aplikaci.

1. Přejděte do `http://localhost:<port>/api/books` kontroler testů je konstruktor bez parametrů `Get` metody akce. Zobrazí se následující odpověď JSON:

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

1. Přejděte do `http://localhost:<port>/api/books/5bfd996f7b8e48dc15ff215e` kontroler testů je přetížena `Get` metody akce. Zobrazí se následující odpověď JSON:

    ```json
    {
      "id":"5bfd996f7b8e48dc15ff215e",
      "bookName":"Clean Code",
      "price":43.15,
      "category":"Computers",
      "author":"Robert C. Martin"
    }
    ```

## <a name="configure-json-serialization-options"></a>Konfigurovat možnosti serializace JSON

Existují dva podrobnosti změnit o vrácená v odpovědi JSON [testování webového rozhraní API](#test-the-web-api) části:

* Velká a malá písmena názvy vlastností ve formátu camelCase výchozí by měla být změněna tak, aby odpovídaly Pascal malá a velká písmena názvů vlastností objektu CLR.
* `bookName` Vlastnost by měla být vrácena jako `Name`.

Tím se uspokojí předchozí požadavky, proveďte následující změny:

1. V `Startup.ConfigureServices`, zřetězení následující zvýrazněný kód do `AddMvc` volání metody:

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

    Předchozí změny názvy vlastností ve webovém rozhraní API serializovat shoda odpověď JSON odpovídající názvy vlastností v objektu typu CLR. Například `Book` třídy `Author` vlastnost serializuje jako `Author`.

1. V *Models/Book.cs*, opatřit poznámkami `BookName` vlastnost s tímto [[JsonProperty]](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) atribut:

    [!code-csharp[](first-mongo-app/sample/BooksApi/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

    `[JsonProperty]` Hodnotu atributu `Name` představuje název vlastnosti ve webovém rozhraní API serializovat odpověď JSON.

1. Přidejte následující kód k hornímu okraji *Models/Book.cs* přeložit `[JsonProperty]` odkaz na atribut:

    [!code-csharp[](first-mongo-app/sample/BooksApi/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. Opakujte kroky uvedené v [testování webového rozhraní API](#test-the-web-api) oddílu. Všimněte si rozdílu v názvech vlastností JSON.

## <a name="next-steps"></a>Další kroky

Další informace o vytváření webových rozhraní API ASP.NET Core naleznete na následujících odkazech:

* [Verzi tohoto článku na webu YouTube](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>

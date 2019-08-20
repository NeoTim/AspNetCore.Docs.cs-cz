---
title: Vytvoření webového rozhraní API pomocí ASP.NET Core využívající databázi MongoDB
author: prkhandelwal
description: V tomto kurzu se dozvíte, jak vytvořit webové rozhraní API ASP.NET Core pomocí databáze NoSQL v databázi MongoDB.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
uid: tutorials/first-mongo-app
ms.openlocfilehash: 2f7202945b3de03709b5f2e192a03549e55a04f7
ms.sourcegitcommit: 257cc3fe8c1d61341aa3b07e5bc0fa3d1c1c1d1c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69583645"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a>Vytvoření webového rozhraní API pomocí ASP.NET Core využívající databázi MongoDB

Podle [Pratik Khandelwal](https://twitter.com/K2Prk) a [Scott Addie](https://twitter.com/Scott_Addie)

::: moniker range=">= aspnetcore-3.0"

Tento kurz vytvoří webového rozhraní API, který provádí operace vytvoření, čtení, aktualizace a odstranění (CRUD) [MongoDB](https://www.mongodb.com/what-is-mongodb) databáze NoSQL.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nakonfigurovat MongoDB
> * Vytvoření databáze MongoDB
> * Definování kolekce MongoDB a schématu
> * Provádění operací MongoDB CRUD z webového rozhraní API
> * Přizpůsobení serializace JSON

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* [.NET Core SDK 3,0 nebo novější](https://www.microsoft.com/net/download/all)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s úlohou **vývoje ASP.NET a webu**
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [.NET Core SDK 3,0 nebo novější](https://www.microsoft.com/net/download/all)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* [.NET Core SDK 3,0 nebo novější](https://www.microsoft.com/net/download/all)
* [Visual Studio pro Mac verze 7,7 nebo novější](https://visualstudio.microsoft.com/downloads/)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a>Nakonfigurovat MongoDB

Pokud používáte Windows, MongoDB se nainstaluje ve výchozím nastavení v *C\\:\\Program Files MongoDB* . Přidejte *C:\\Program Files\\MongoDB\\server\\version_number>\\bin do proměnné prostředí.\<* `Path` Tato změna umožňuje MongoDB přístup z libovolného místa na vývojovém počítači.

Použití prostředí mongo v následujících krocích k vytvoření databáze, ujistěte se, kolekce a ukládat dokumenty. Další informace o příkazech prostředí mongo naleznete v tématu [práce s mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).

1. Vyberte adresář na vývojovém počítači pro ukládání dat. Například *\\C: BooksData* ve Windows. Vytvořte adresář, pokud neexistuje. Prostředí mongo nebude vytvářet nové adresáře.
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

   Přidá automaticky generované schéma `_id` vlastnost typu `ObjectId` pro každý dokument.

Databáze je připravena. Můžete začít vytvářet webové rozhraní API ASP.NET Core.

## <a name="create-the-aspnet-core-web-api-project"></a>Vytvoření projektu webové rozhraní API ASP.NET Core

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Přejít na **soubor** > **Nový** > **projekt**.
1. Vyberte ASP.NET Core typ projektu **webové aplikace** a vyberte **Další**.
1. Pojmenujte projekt *BooksApi*a vyberte **vytvořit**.
1. Vyberte cílové rozhraní **.NET Core** a **ASP.NET Core 3,0**. Vyberte šablonu projektu **rozhraní API** a vyberte **vytvořit**.
1. Navštivte galerii [NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) pro určení nejnovější stabilní verze ovladače .NET pro MongoDB. V **Konzola správce balíčků** okno, přejděte do kořenového adresáře projektu. Spusťte následující příkaz k instalaci ovladače .NET pro MongoDB:

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

1. Až se ikona plamene u OmniSharp stavového řádku změní na zelenou **, dialogové okno požádá o požadované prostředky k sestavení a ladění chybí v ' BooksApi '. Přidat je?** . Vyberte **Ano**.
1. Navštivte galerii [NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) pro určení nejnovější stabilní verze ovladače .NET pro MongoDB. Otevřít **integrovaný terminál** a přejděte do kořenového adresáře projektu. Spusťte následující příkaz k instalaci ovladače .NET pro MongoDB:

   ```console
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. Přejít na **soubor** > **nové řešení** > **.NET Core** > aplikace
1. Vyberte šablonu projektu **ASP.NET Core webového rozhraní API** C# a vyberte **Další**.
1. V rozevíracím seznamu **cílové rozhraní** vyberte **.NET Core 3,0** a vyberte **Další**.
1. Jako **název projektu**zadejte *BooksApi* a vyberte **vytvořit**.
1. V **řešení** panel, klikněte pravým tlačítkem projekt **závislosti** uzel a vyberte možnost **přidat balíčky**.
1. Do vyhledávacího pole zadejte *MongoDB. Driver* , vyberte balíček *MongoDB. Driver* a vyberte **Přidat balíček**.
1. V dialogu pro **přijetí licence** vyberte tlačítko **přijmout** .

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

   V předchozí třídě `Id` vlastnost:

   * Je vyžadován pro mapování objektu modulu CLR (Common Language Runtime) na kolekci MongoDB.
   * Je opatřen s poznámkami [[BsonId]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) k označení této vlastnosti jako primárního klíče dokumentu.
   * Je opatřen s poznámkami [[BsonRepresentation (BsonType. objectID)]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) , aby bylo možné předat parametr `string` jako typ namísto struktury [objectID](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) . Mongo zpracovává převod z `string` na. `ObjectId`

   Vlastnost je označena atributem [[BsonElement].](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) `BookName` Hodnota `Name` atributu představuje název vlastnosti v kolekci MongoDB.

## <a name="add-a-configuration-model"></a>Přidat konfigurační model

1. Do souboru *appSettings. JSON*přidejte následující hodnoty konfigurace databáze:

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. Přidejte soubor *BookstoreDatabaseSettings.cs* do adresáře *Models* s následujícím kódem:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   Předchozí `BookstoreDatabaseSettings` třída se používá k uložení hodnot `BookstoreDatabaseSettings` vlastností souboru *appSettings. JSON* . Názvy JSON a C# Property jsou pojmenovány stejně, aby bylo možné zjednodušit proces mapování.

1. Přidejte následující zvýrazněný kód do `Startup.ConfigureServices`:

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   V předchozím kódu:

   * Instance konfigurace, na kterou se váže `BookstoreDatabaseSettings` sekce souboru *appSettings. JSON* , se registruje v kontejneru injektáže (di). Například `BookstoreDatabaseSettings` vlastnost`ConnectionString`objektu je naplněna vlastnostívsouboruappSettings.`BookstoreDatabaseSettings:ConnectionString` JSON.
   * Rozhraní je zaregistrované v di s životností služby typu singleton. [](xref:fundamentals/dependency-injection#service-lifetimes) `IBookstoreDatabaseSettings` Při vložení se instance rozhraní překládá na `BookstoreDatabaseSettings` objekt.

1. Pro vyřešení `BookstoreDatabaseSettings` odkazů a `IBookstoreDatabaseSettings` přidejte následující kód na začátek *Startup.cs* :

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a>Přidání služby operace CRUD

1. Přidat *služby* adresáře do kořenového adresáře projektu.
1. Přidat `BookService` třídu *služby* adresáře s následujícím kódem:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   V předchozím kódu `IBookstoreDatabaseSettings` je instance načtena z di přes injektáže konstruktoru. Tento postup poskytuje přístup k hodnotám konfigurace *appSettings. JSON* , které byly přidány do oddílu [přidat konfigurační model](#add-a-configuration-model) .

1. Přidejte následující zvýrazněný kód do `Startup.ConfigureServices`:

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   V předchozím kódu `BookService` je třída registrována pomocí příkazu di k podpoře injektáže konstruktoru v rámci využívání tříd. Doba životnosti služby singleton je nejvhodnější, `BookService` protože používá přímou závislost na `MongoClient`. Podle `MongoClient` oficiálních [zásad opětovného použití pro klienty Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)by se měla registrovat v di s životností služby typu singleton.

1. Přidejte následující kód na začátek *Startup.cs* k vyřešení `BookService` odkazu:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

`BookService` Třída používá následující `MongoDB.Driver` členy k provádění operací CRUD proti databázi:

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Přečte instanci serveru pro provádění databázových operací. Konstruktor Tato třída poskytuje připojovacího řetězce MongoDB:

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* [IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Představuje databázi Mongo pro provádění operací. V tomto kurzu se používá obecná metoda [GetCollection\<TDocument > (Collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) na rozhraní pro získání přístupu k datům v určité kolekci. Provede operace CRUD proti kolekci po volání této metody. V `GetCollection<TDocument>(collection)` volání metody:

  * `collection` představuje název kolekce.
  * `TDocument` představuje typ objektu CLR uložená v kolekci.

`GetCollection<TDocument>(collection)`Vrátí objekt [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) představující kolekci. V tomto kurzu jsou vyvolány následující metody na kolekci:

* [DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Odstraní jeden dokument, který odpovídá zadaným kritériím hledání.
* [Hledání\<TDocument >](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; vrátí všechny dokumenty v kolekci, které odpovídají zadaným kritériím hledání.
* [InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Vloží zadaný objekt jako nový dokument v kolekci.
* [ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Nahradí jediný dokument, který odpovídá zadaným kritériím hledání, zadaným objektem.

## <a name="add-a-controller"></a>Přidání kontroleru

Přidat `BooksController` třídu *řadiče* adresáře s následujícím kódem:

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

Předchozí kontroler web API:

* Používá `BookService` pro provádění operací CRUD.
* Obsahuje metody akce, který podporuje požadavky GET, POST, PUT a DELETE HTTP.
* <xref:System.Web.Http.ApiController.CreatedAtRoute*> Volání`Create` v metodě Action vrátí odpověď [http 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) . Stavový kód 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru. `CreatedAtRoute`přidá `Location` také hlavičku do odpovědi. `Location` Hlavička Určuje identifikátor URI nově vytvořené knihy.

## <a name="test-the-web-api"></a>Testování webového rozhraní API

1. Sestavte a spusťte aplikaci.

1. Přejděte na `http://localhost:<port>/api/books` k otestování metody `Get` akce bez parametrů řadiče. Zobrazí se následující odpověď JSON:

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

1. Přejděte na `http://localhost:<port>/api/books/{id here}` k otestování metody `Get` akce, která je přetížena řadičem. Zobrazí se následující odpověď JSON:

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
* Vlastnost by měla být vrácena jako `Name`. `bookName`

Chcete-li splnit předchozí požadavky, proveďte následující změny:

1. JSON.NET se odebral ze sdílené architektury ASP.NET. Přidejte odkaz na balíček [Microsoft. AspNetCore. Mvc. NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).

1. V `Startup.ConfigureServices`, řetězení následujícího zvýrazněného kódu `AddMvc` k volání metody:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   V předchozí změně názvy vlastností v serializovaných odpovědích JSON webového rozhraní API odpovídají jejich odpovídajícím názvům vlastností v typu objektu CLR. Například `Book` vlastnost`Author` třídy je serializována jako `Author`.

1. V části *Models/Book. cs*poznámku k `BookName` vlastnosti s následujícím atributem [[JsonProperty]](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) :

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   `[JsonProperty]` Hodnotaatributupředstavujenázevvlastnostivserializovanéodpovědi`Name` JSON webového rozhraní API.

1. Přidejte následující kód na začátek *modelů/Book. cs* pro vyřešení odkazu na `[JsonProperty]` atribut:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. Opakujte kroky definované v části [Test webového rozhraní API](#test-the-web-api) . Všimněte si rozdílu v názvech vlastností JSON.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Tento kurz vytvoří webového rozhraní API, který provádí operace vytvoření, čtení, aktualizace a odstranění (CRUD) [MongoDB](https://www.mongodb.com/what-is-mongodb) databáze NoSQL.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nakonfigurovat MongoDB
> * Vytvoření databáze MongoDB
> * Definování kolekce MongoDB a schématu
> * Provádění operací MongoDB CRUD z webového rozhraní API
> * Přizpůsobení serializace JSON

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* [.NET Core SDK 2,2](https://www.microsoft.com/net/download/all)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s úlohou **vývoje ASP.NET a webu**
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [.NET Core SDK 2,2](https://www.microsoft.com/net/download/all)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* [.NET Core SDK 2,2](https://www.microsoft.com/net/download/all)
* [Visual Studio pro Mac verze 7,7 nebo novější](https://visualstudio.microsoft.com/downloads/)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a>Nakonfigurovat MongoDB

Pokud používáte Windows, MongoDB se nainstaluje ve výchozím nastavení v *C\\:\\Program Files MongoDB* . Přidejte *C:\\Program Files\\MongoDB\\server\\version_number>\\bin do proměnné prostředí.\<* `Path` Tato změna umožňuje MongoDB přístup z libovolného místa na vývojovém počítači.

Použití prostředí mongo v následujících krocích k vytvoření databáze, ujistěte se, kolekce a ukládat dokumenty. Další informace o příkazech prostředí mongo naleznete v tématu [práce s mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).

1. Vyberte adresář na vývojovém počítači pro ukládání dat. Například *\\C: BooksData* ve Windows. Vytvořte adresář, pokud neexistuje. Prostředí mongo nebude vytvářet nové adresáře.
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

   Přidá automaticky generované schéma `_id` vlastnost typu `ObjectId` pro každý dokument.

Databáze je připravena. Můžete začít vytvářet webové rozhraní API ASP.NET Core.

## <a name="create-the-aspnet-core-web-api-project"></a>Vytvoření projektu webové rozhraní API ASP.NET Core

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Přejít na **soubor** > **Nový** > **projekt**.
1. Vyberte ASP.NET Core typ projektu **webové aplikace** a vyberte **Další**.
1. Pojmenujte projekt *BooksApi*a vyberte **vytvořit**.
1. Vyberte cílové rozhraní **.NET Core** a **ASP.NET Core 2,2**. Vyberte šablonu projektu **rozhraní API** a vyberte **vytvořit**.
1. Navštivte galerii [NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) pro určení nejnovější stabilní verze ovladače .NET pro MongoDB. V **Konzola správce balíčků** okno, přejděte do kořenového adresáře projektu. Spusťte následující příkaz k instalaci ovladače .NET pro MongoDB:

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

1. Až se ikona plamene u OmniSharp stavového řádku změní na zelenou **, dialogové okno požádá o požadované prostředky k sestavení a ladění chybí v ' BooksApi '. Přidat je?** . Vyberte **Ano**.
1. Navštivte galerii [NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) pro určení nejnovější stabilní verze ovladače .NET pro MongoDB. Otevřít **integrovaný terminál** a přejděte do kořenového adresáře projektu. Spusťte následující příkaz k instalaci ovladače .NET pro MongoDB:

   ```console
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. Přejít na **soubor** > **nové řešení** > **.NET Core** > aplikace
1. Vyberte šablonu projektu **ASP.NET Core webového rozhraní API** C# a vyberte **Další**.
1. V rozevíracím seznamu **cílové rozhraní** vyberte **.NET Core 2,2** a vyberte **Další**.
1. Jako **název projektu**zadejte *BooksApi* a vyberte **vytvořit**.
1. V **řešení** panel, klikněte pravým tlačítkem projekt **závislosti** uzel a vyberte možnost **přidat balíčky**.
1. Do vyhledávacího pole zadejte *MongoDB. Driver* , vyberte balíček *MongoDB. Driver* a vyberte **Přidat balíček**.
1. V dialogu pro **přijetí licence** vyberte tlačítko **přijmout** .

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

   V předchozí třídě `Id` vlastnost:

   * Je vyžadován pro mapování objektu modulu CLR (Common Language Runtime) na kolekci MongoDB.
   * Je opatřen s poznámkami [[BsonId]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) k označení této vlastnosti jako primárního klíče dokumentu.
   * Je opatřen s poznámkami [[BsonRepresentation (BsonType. objectID)]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) , aby bylo možné předat parametr `string` jako typ namísto struktury [objectID](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) . Mongo zpracovává převod z `string` na. `ObjectId`

   Vlastnost je označena atributem [[BsonElement].](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) `BookName` Hodnota `Name` atributu představuje název vlastnosti v kolekci MongoDB.

## <a name="add-a-configuration-model"></a>Přidat konfigurační model

1. Do souboru *appSettings. JSON*přidejte následující hodnoty konfigurace databáze:

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. Přidejte soubor *BookstoreDatabaseSettings.cs* do adresáře *Models* s následujícím kódem:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   Předchozí `BookstoreDatabaseSettings` třída se používá k uložení hodnot `BookstoreDatabaseSettings` vlastností souboru *appSettings. JSON* . Názvy JSON a C# Property jsou pojmenovány stejně, aby bylo možné zjednodušit proces mapování.

1. Přidejte následující zvýrazněný kód do `Startup.ConfigureServices`:

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   V předchozím kódu:

   * Instance konfigurace, na kterou se váže `BookstoreDatabaseSettings` sekce souboru *appSettings. JSON* , se registruje v kontejneru injektáže (di). Například `BookstoreDatabaseSettings` vlastnost`ConnectionString`objektu je naplněna vlastnostívsouboruappSettings.`BookstoreDatabaseSettings:ConnectionString` JSON.
   * Rozhraní je zaregistrované v di s životností služby typu singleton. [](xref:fundamentals/dependency-injection#service-lifetimes) `IBookstoreDatabaseSettings` Při vložení se instance rozhraní překládá na `BookstoreDatabaseSettings` objekt.

1. Pro vyřešení `BookstoreDatabaseSettings` odkazů a `IBookstoreDatabaseSettings` přidejte následující kód na začátek *Startup.cs* :

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a>Přidání služby operace CRUD

1. Přidat *služby* adresáře do kořenového adresáře projektu.
1. Přidat `BookService` třídu *služby* adresáře s následujícím kódem:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   V předchozím kódu `IBookstoreDatabaseSettings` je instance načtena z di přes injektáže konstruktoru. Tento postup poskytuje přístup k hodnotám konfigurace *appSettings. JSON* , které byly přidány do oddílu [přidat konfigurační model](#add-a-configuration-model) .

1. Přidejte následující zvýrazněný kód do `Startup.ConfigureServices`:

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   V předchozím kódu `BookService` je třída registrována pomocí příkazu di k podpoře injektáže konstruktoru v rámci využívání tříd. Doba životnosti služby singleton je nejvhodnější, `BookService` protože používá přímou závislost na `MongoClient`. Podle `MongoClient` oficiálních [zásad opětovného použití pro klienty Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)by se měla registrovat v di s životností služby typu singleton.

1. Přidejte následující kód na začátek *Startup.cs* k vyřešení `BookService` odkazu:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

`BookService` Třída používá následující `MongoDB.Driver` členy k provádění operací CRUD proti databázi:

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Přečte instanci serveru pro provádění databázových operací. Konstruktor Tato třída poskytuje připojovacího řetězce MongoDB:

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* [IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Představuje databázi Mongo pro provádění operací. V tomto kurzu se používá obecná metoda [GetCollection\<TDocument > (Collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) na rozhraní pro získání přístupu k datům v určité kolekci. Provede operace CRUD proti kolekci po volání této metody. V `GetCollection<TDocument>(collection)` volání metody:

  * `collection` představuje název kolekce.
  * `TDocument` představuje typ objektu CLR uložená v kolekci.

`GetCollection<TDocument>(collection)`Vrátí objekt [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) představující kolekci. V tomto kurzu jsou vyvolány následující metody na kolekci:

* [DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Odstraní jeden dokument, který odpovídá zadaným kritériím hledání.
* [Hledání\<TDocument >](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; vrátí všechny dokumenty v kolekci, které odpovídají zadaným kritériím hledání.
* [InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Vloží zadaný objekt jako nový dokument v kolekci.
* [ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Nahradí jediný dokument, který odpovídá zadaným kritériím hledání, zadaným objektem.

## <a name="add-a-controller"></a>Přidání kontroleru

Přidat `BooksController` třídu *řadiče* adresáře s následujícím kódem:

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

Předchozí kontroler web API:

* Používá `BookService` pro provádění operací CRUD.
* Obsahuje metody akce, který podporuje požadavky GET, POST, PUT a DELETE HTTP.
* <xref:System.Web.Http.ApiController.CreatedAtRoute*> Volání`Create` v metodě Action vrátí odpověď [http 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) . Stavový kód 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru. `CreatedAtRoute`přidá `Location` také hlavičku do odpovědi. `Location` Hlavička Určuje identifikátor URI nově vytvořené knihy.

## <a name="test-the-web-api"></a>Testování webového rozhraní API

1. Sestavte a spusťte aplikaci.

1. Přejděte na `http://localhost:<port>/api/books` k otestování metody `Get` akce bez parametrů řadiče. Zobrazí se následující odpověď JSON:

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

1. Přejděte na `http://localhost:<port>/api/books/{id here}` k otestování metody `Get` akce, která je přetížena řadičem. Zobrazí se následující odpověď JSON:

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
* Vlastnost by měla být vrácena jako `Name`. `bookName`

Chcete-li splnit předchozí požadavky, proveďte následující změny:

1. V `Startup.ConfigureServices`, řetězení následujícího zvýrazněného kódu `AddMvc` k volání metody:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   V předchozí změně názvy vlastností v serializovaných odpovědích JSON webového rozhraní API odpovídají jejich odpovídajícím názvům vlastností v typu objektu CLR. Například `Book` vlastnost`Author` třídy je serializována jako `Author`.

1. V části *Models/Book. cs*poznámku k `BookName` vlastnosti s následujícím atributem [[JsonProperty]](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) :

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   `[JsonProperty]` Hodnotaatributupředstavujenázevvlastnostivserializovanéodpovědi`Name` JSON webového rozhraní API.

1. Přidejte následující kód na začátek *modelů/Book. cs* pro vyřešení odkazu na `[JsonProperty]` atribut:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. Opakujte kroky definované v části [Test webového rozhraní API](#test-the-web-api) . Všimněte si rozdílu v názvech vlastností JSON.

::: moniker-end

## <a name="next-steps"></a>Další kroky

Další informace o vytváření webových rozhraní API ASP.NET Core naleznete na následujících odkazech:

* [YouTube verze tohoto článku](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>

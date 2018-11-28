---
title: Vytvoření webového rozhraní API pomocí ASP.NET Core využívající databázi MongoDB
author: pratik-khandelwal
description: Tento kurz ukazuje vytvoření webového rozhraní ASP.NET Core API pomocí databáze MongoDB NoSQL.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/26/2018
uid: tutorials/first-mongo-app
ms.openlocfilehash: c4e00eeb2c4aecde9c70c6902e21d06853be7696
ms.sourcegitcommit: e7fafb153b9de7595c2558a0133f8d1c33a3bddb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52458490"
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

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample) ([stažení](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Požadavky

* [.NET core SDK 2.1 nebo novější](https://www.microsoft.com/net/download/all)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)
* [Visual Studio 2017](https://www.visualstudio.com/downloads/) verze 15.7.3 nebo novější s následujícími sadami funkcí:
  * **Vývoj pro různé platformy .NET core**
  * **Vývoj pro ASP.NET a web**

## <a name="configure-mongodb"></a>Nakonfigurovat MongoDB

Pokud používáte Windows, databáze MongoDB nainstaluje na *C:\Program Files\MongoDB* ve výchozím nastavení. Přidat *C:\Program Files\MongoDB\Server\<version_number > \bin* k `Path` proměnné prostředí. Tato změna umožňuje MongoDB přístup z libovolného místa na vývojovém počítači.

Použití prostředí mongo v následujících krocích k vytvoření databáze, ujistěte se, kolekce a ukládat dokumenty. Další informace o příkazech prostředí mongo naleznete v tématu [práce s mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).

1. Vyberte adresář na vývojovém počítači pro ukládání dat. Například *C:\BooksData* na Windows. Vytvořte adresář, pokud neexistuje. Prostředí mongo nebude vytvářet nové adresáře.
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

1. V sadě Visual Studio, přejděte na **souboru** > **nový** > **projektu**.
1. Vyberte **webové aplikace ASP.NET Core**, pojmenujte projekt *BookMongo*a klikněte na tlačítko **OK**.
1. Vyberte **.NET Core** Cílová architektura a **ASP.NET Core 2.1**. Vyberte **API** šablony projektu a klikněte na tlačítko **OK**:
1. V **Konzola správce balíčků** okno, přejděte do kořenového adresáře projektu. Spusťte následující příkaz k instalaci ovladače .NET pro MongoDB:

    ```powershell
    Install-Package MongoDB.Driver -Version 2.7.2
    ```

## <a name="add-a-model"></a>Přidání modelu

1. Přidat *modely* složku do kořenového adresáře projektu.
1. Přidat `Book` třídu *modely* složka s následujícím kódem:

    [!code-csharp[](first-mongo-app/sample/BookstoreAPI/Models/Book.cs)]

Ve třídě předchozí `Id` vlastnost se vyžaduje pro mapování objektu Common Language Runtime (CLR) ke kolekci MongoDB. Další vlastnosti ve třídě jsou vybaveny `[BsonElement]` atribut. Hodnota atributu představuje název vlastnosti v kolekci MongoDB.

## <a name="add-a-crud-operations-class"></a>Přidejte třídu operace CRUD

1. Přidat *služby* složku do kořenového adresáře projektu.
1. Přidat `BookService` třídu *služby* složka s následujícím kódem:

    [!code-csharp[](first-mongo-app/sample/BookstoreAPI/Services/BookService.cs?name=snippet_BookServiceClass)]

1. Přidat připojovací řetězec MongoDB do *appsettings.json*:

    [!code-csharp[](first-mongo-app/sample/BookstoreAPI/appsettings.json?highlight=2-4)]

    Předchozí `BookstoreDb` v získat přístup k vlastnosti `BookService` konstruktoru třídy.

1. V `Startup.ConfigureServices`, zaregistrujte `BookService` třídy systémem injektáž závislostí:

    [!code-csharp[](first-mongo-app/sample/BookstoreAPI/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

    Předchozí registrace služby je nezbytný pro podporu vkládání konstruktor přijímací třídy.

`BookService` Třída používá následující `MongoDB.Driver` členy k provádění operací CRUD proti databázi:

* `MongoClient` &ndash; Načte instance serveru k provedení operace databáze. Konstruktor Tato třída poskytuje připojovacího řetězce MongoDB:

    [!code-csharp[](first-mongo-app/sample/BookstoreAPI/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* `IMongoDatabase` &ndash; Reprezentuje databázi Mongodb pro provádění operací. Tento kurz používá Obecné `GetCollection<T>(collection)` metoda v rozhraní k získání přístupu k datům v určité kolekci. Operace CRUD lze provést proti kolekci, jakmile tato metoda je volána. V `GetCollection<T>(collection)` volání metody:
  * `collection` představuje název kolekce.
  * `T` představuje typ objektu CLR uložená v kolekci.

`GetCollection<T>(collection)` Vrátí `MongoCollection` objekt představující kolekci. V tomto kurzu jsou vyvolány následující metody na kolekci:

* `Find<T>` &ndash; Vrátí všechny dokumenty v kolekci odpovídá kritériím hledání zadaná.
* `InsertOne` &ndash; Vloží zadaný objekt jako nový dokument v kolekci.
* `ReplaceOne` &ndash; Nahrazuje jeden dokument, který odpovídá kritériím hledání zadaná pomocí zadaného objektu.
* `DeleteOne` &ndash; Odstraní jeden dokument, který odpovídá kritériím hledání zadaná.

## <a name="add-a-controller"></a>Přidání kontroleru

1. Klikněte pravým tlačítkem myši *řadiče* složky v **Průzkumníka řešení**. Vyberte **přidat** > **řadič**.
1. Zvolte **prázdný kontroler API –** šablony položky a klikněte na tlačítko **přidat**.
1. Zadejte *BooksController* v **názvu Kontroleru** textového pole a klikněte na tlačítko **přidat**.
1. Přidejte následující kód, který *BooksController.cs*:

    [!code-csharp[](first-mongo-app/sample/BookstoreAPI/Controllers/BooksController.cs)]

    Předchozí kontroler web API:

    * Používá `BookService` pro provádění operací CRUD.
    * Obsahuje metody akce, který podporuje požadavky GET, POST, PUT a DELETE HTTP.
1. Sestavte a spusťte aplikaci.
1. Přejděte na `http://localhost:<port>/api/books` v prohlížeči. Zobrazí se následující odpověď JSON:

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

## <a name="next-steps"></a>Další kroky

Další informace o vytváření webových rozhraní API ASP.NET Core naleznete na následujících odkazech:

* <xref:web-api/index>
* <xref:web-api/action-return-types>

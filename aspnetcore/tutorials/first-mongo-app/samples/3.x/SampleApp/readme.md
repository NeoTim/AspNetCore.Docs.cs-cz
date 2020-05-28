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
ms.openlocfilehash: 95a2a6fcda0a4f7148183981f7dbacd06388329d
ms.sourcegitcommit: 58722eb309767e462bdbf3082bd38737a4ef168f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84106517"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="291b7-102">Vytvoření webového rozhraní API pomocí ASP.NET Core a MongoDB</span><span class="sxs-lookup"><span data-stu-id="291b7-102">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="291b7-103">V tomto kurzu se vytvoří webové rozhraní API, které provádí operace vytvoření, čtení, aktualizace a odstranění (CRUD) v databázi [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL.</span><span class="sxs-lookup"><span data-stu-id="291b7-103">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="291b7-104">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="291b7-104">In this tutorial, you learn how to:</span></span>

* <span data-ttu-id="291b7-105">Konfigurace MongoDB</span><span class="sxs-lookup"><span data-stu-id="291b7-105">Configure MongoDB</span></span>
* <span data-ttu-id="291b7-106">Vytvoření databáze MongoDB</span><span class="sxs-lookup"><span data-stu-id="291b7-106">Create a MongoDB database</span></span>
* <span data-ttu-id="291b7-107">Definování kolekce a schématu MongoDB</span><span class="sxs-lookup"><span data-stu-id="291b7-107">Define a MongoDB collection and schema</span></span>
* <span data-ttu-id="291b7-108">Provádění operací CRUD MongoDB z webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="291b7-108">Perform MongoDB CRUD operations from a web API</span></span>
* <span data-ttu-id="291b7-109">Přizpůsobení serializace JSON</span><span class="sxs-lookup"><span data-stu-id="291b7-109">Customize JSON serialization</span></span>

## <a name="prerequisites"></a><span data-ttu-id="291b7-110">Požadavky</span><span class="sxs-lookup"><span data-stu-id="291b7-110">Prerequisites</span></span>

* [<span data-ttu-id="291b7-111">.NET Core SDK 3.0 nebo novější</span><span class="sxs-lookup"><span data-stu-id="291b7-111">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="291b7-112">[Visual Studio 2019 Preview](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=community&ch=pre&rel=16&utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019preview) s úlohou **vývoje ASP.NET a webu**</span><span class="sxs-lookup"><span data-stu-id="291b7-112">[Visual Studio 2019 Preview](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=community&ch=pre&rel=16&utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019preview) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="291b7-113">MongoDB</span><span class="sxs-lookup"><span data-stu-id="291b7-113">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

## <a name="configure-mongodb"></a><span data-ttu-id="291b7-114">Konfigurace MongoDB</span><span class="sxs-lookup"><span data-stu-id="291b7-114">Configure MongoDB</span></span>

<span data-ttu-id="291b7-115">Pokud používáte Windows, MongoDB se nainstaluje ve výchozím nastavení v *C: \\ Program Files \\ MongoDB* .</span><span class="sxs-lookup"><span data-stu-id="291b7-115">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="291b7-116">Přidejte *C: \\ Program Files \\ MongoDB \\ Server \\ \<version_number> \\ bin* do `Path` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="291b7-116">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="291b7-117">Tato změna umožňuje přístup k MongoDB odkudkoli na svém vývojovém počítači.</span><span class="sxs-lookup"><span data-stu-id="291b7-117">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="291b7-118">Pomocí prostředí Mongo v následujících krocích můžete vytvořit databázi, vytvořit kolekce a uložit dokumenty.</span><span class="sxs-lookup"><span data-stu-id="291b7-118">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="291b7-119">Další informace o příkazech prostředí Mongo najdete v tématu [práce s prostředím Mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="291b7-119">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="291b7-120">Pro uložení dat vyberte adresář na vývojovém počítači.</span><span class="sxs-lookup"><span data-stu-id="291b7-120">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="291b7-121">Například *C: \\ BooksData* ve Windows.</span><span class="sxs-lookup"><span data-stu-id="291b7-121">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="291b7-122">Pokud adresář neexistuje, vytvořte ho.</span><span class="sxs-lookup"><span data-stu-id="291b7-122">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="291b7-123">Prostředí Mongo nevytváří nové adresáře.</span><span class="sxs-lookup"><span data-stu-id="291b7-123">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="291b7-124">Otevřete příkazové prostředí.</span><span class="sxs-lookup"><span data-stu-id="291b7-124">Open a command shell.</span></span> <span data-ttu-id="291b7-125">Spusťte následující příkaz pro připojení k MongoDB na výchozím portu 27017.</span><span class="sxs-lookup"><span data-stu-id="291b7-125">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="291b7-126">Nezapomeňte nahradit `<data_directory_path>` adresář, který jste zvolili v předchozím kroku.</span><span class="sxs-lookup"><span data-stu-id="291b7-126">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. <span data-ttu-id="291b7-127">Otevřete jinou instanci příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="291b7-127">Open another command shell instance.</span></span> <span data-ttu-id="291b7-128">Připojte se k výchozí testovací databázi spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="291b7-128">Connect to the default test database by running the following command:</span></span>

    ```console
    mongo
    ```

1. <span data-ttu-id="291b7-129">V příkazovém prostředí spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="291b7-129">Run the following in a command shell:</span></span>

    ```console
    use BookstoreDb
    ```

    <span data-ttu-id="291b7-130">Pokud ještě neexistuje, vytvoří se databáze s názvem *BookstoreDb* .</span><span class="sxs-lookup"><span data-stu-id="291b7-130">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="291b7-131">Pokud databáze existuje, připojení je otevřeno pro transakce.</span><span class="sxs-lookup"><span data-stu-id="291b7-131">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="291b7-132">Vytvořte `Books` kolekci pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="291b7-132">Create a `Books` collection using following command:</span></span>

    ```console
    db.createCollection('Books')
    ```

    <span data-ttu-id="291b7-133">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="291b7-133">The following result is displayed:</span></span>

    ```console
    { "ok" : 1 }
    ```

1. <span data-ttu-id="291b7-134">Definujte schéma pro `Books` kolekci a vložte dva dokumenty pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="291b7-134">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    <span data-ttu-id="291b7-135">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="291b7-135">The following result is displayed:</span></span>

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
  > <span data-ttu-id="291b7-136">ID zobrazené v tomto článku se při spuštění této ukázky neshoduje s ID.</span><span class="sxs-lookup"><span data-stu-id="291b7-136">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="291b7-137">Zobrazte dokumenty v databázi pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="291b7-137">View the documents in the database using the following command:</span></span>

    ```console
    db.Books.find({}).pretty()
    ```

    <span data-ttu-id="291b7-138">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="291b7-138">The following result is displayed:</span></span>

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

    <span data-ttu-id="291b7-139">Schéma přidá do každého dokumentu automaticky generovanou `_id` vlastnost typu `ObjectId` .</span><span class="sxs-lookup"><span data-stu-id="291b7-139">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="291b7-140">Databáze je připravena.</span><span class="sxs-lookup"><span data-stu-id="291b7-140">The database is ready.</span></span> <span data-ttu-id="291b7-141">Můžete začít vytvářet ASP.NET Core webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="291b7-141">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="291b7-142">Vytvoření projektu webového rozhraní API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="291b7-142">Create the ASP.NET Core web API project</span></span>

1. <span data-ttu-id="291b7-143">Přejít na **soubor**  >  **Nový**  >  **projekt**.</span><span class="sxs-lookup"><span data-stu-id="291b7-143">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="291b7-144">Vyberte ASP.NET Core typ projektu **webové aplikace** a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="291b7-144">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="291b7-145">Pojmenujte projekt *BooksApi*a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="291b7-145">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="291b7-146">Vyberte cílové rozhraní **.NET Core** a **ASP.NET Core 3,0**.</span><span class="sxs-lookup"><span data-stu-id="291b7-146">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="291b7-147">Vyberte šablonu projektu **rozhraní API** a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="291b7-147">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="291b7-148">Navštivte [galerii NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) a určete nejnovější stabilní verzi ovladače .NET pro MongoDB.</span><span class="sxs-lookup"><span data-stu-id="291b7-148">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="291b7-149">V okně **konzoly Správce balíčků** přejděte do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="291b7-149">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="291b7-150">Spuštěním následujícího příkazu nainstalujte ovladač .NET pro MongoDB:</span><span class="sxs-lookup"><span data-stu-id="291b7-150">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

## <a name="add-an-entity-model"></a><span data-ttu-id="291b7-151">Přidání modelu entity</span><span class="sxs-lookup"><span data-stu-id="291b7-151">Add an entity model</span></span>

1. <span data-ttu-id="291b7-152">Přidejte adresář *modelů* do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="291b7-152">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="291b7-153">Přidejte `Book` třídu do adresáře *modelů* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="291b7-153">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

    <span data-ttu-id="291b7-154">V předchozí třídě `Id` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="291b7-154">In the preceding class, the `Id` property:</span></span>

    * <span data-ttu-id="291b7-155">Je vyžadován pro mapování objektu modulu CLR (Common Language Runtime) na kolekci MongoDB.</span><span class="sxs-lookup"><span data-stu-id="291b7-155">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
    * <span data-ttu-id="291b7-156">Je s poznámkami [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) k označení této vlastnosti jako primárního klíče dokumentu.</span><span class="sxs-lookup"><span data-stu-id="291b7-156">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
    * <span data-ttu-id="291b7-157">Je opatřen s poznámkami, [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) aby bylo možné předat parametr jako typ `string` namísto struktury [objectID](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) .</span><span class="sxs-lookup"><span data-stu-id="291b7-157">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="291b7-158">Mongo zpracovává převod z `string` na `ObjectId` .</span><span class="sxs-lookup"><span data-stu-id="291b7-158">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

    <span data-ttu-id="291b7-159">`BookName`Vlastnost je označena [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) atributem.</span><span class="sxs-lookup"><span data-stu-id="291b7-159">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="291b7-160">Hodnota atributu `Name` představuje název vlastnosti v kolekci MongoDB.</span><span class="sxs-lookup"><span data-stu-id="291b7-160">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="291b7-161">Přidat konfigurační model</span><span class="sxs-lookup"><span data-stu-id="291b7-161">Add a configuration model</span></span>

1. <span data-ttu-id="291b7-162">Do souboru *appSettings. JSON*přidejte následující hodnoty konfigurace databáze:</span><span class="sxs-lookup"><span data-stu-id="291b7-162">Add the following database configuration values to *appsettings.json*:</span></span>

    ```javascript
    {
      "BookstoreDatabaseSettings": {
        "BooksCollectionName": "Books",
        "ConnectionString": "mongodb://localhost:27017",
        "DatabaseName": "BookstoreDb"
      },

    ```

1. <span data-ttu-id="291b7-163">Přidejte soubor *BookstoreDatabaseSettings.cs* do adresáře *Models* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="291b7-163">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

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

    <span data-ttu-id="291b7-164">Předchozí `BookstoreDatabaseSettings` Třída se používá k uložení hodnot vlastností souboru *appSettings. JSON* `BookstoreDatabaseSettings` .</span><span class="sxs-lookup"><span data-stu-id="291b7-164">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="291b7-165">Názvy vlastností JSON a C# jsou pojmenovány stejně, aby bylo možné zjednodušit proces mapování.</span><span class="sxs-lookup"><span data-stu-id="291b7-165">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="291b7-166">Přidejte následující zvýrazněný kód do `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="291b7-166">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

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

    <span data-ttu-id="291b7-167">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="291b7-167">In the preceding code:</span></span>

    * <span data-ttu-id="291b7-168">Instance konfigurace, na kterou se váže sekce souboru *appSettings. JSON* , `BookstoreDatabaseSettings` se registruje v kontejneru injektáže (di).</span><span class="sxs-lookup"><span data-stu-id="291b7-168">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="291b7-169">Například `BookstoreDatabaseSettings` `ConnectionString` vlastnost objektu je naplněna `BookstoreDatabaseSettings:ConnectionString` vlastností v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="291b7-169">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
    * <span data-ttu-id="291b7-170">`IBookstoreDatabaseSettings`Rozhraní je zaregistrované v di s [životností služby](xref:fundamentals/dependency-injection#service-lifetimes)typu singleton.</span><span class="sxs-lookup"><span data-stu-id="291b7-170">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="291b7-171">Při vložení se instance rozhraní překládá na `BookstoreDatabaseSettings` objekt.</span><span class="sxs-lookup"><span data-stu-id="291b7-171">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="291b7-172">Pro vyřešení odkazů a přidejte následující kód na začátek *Startup.cs* `BookstoreDatabaseSettings` `IBookstoreDatabaseSettings` :</span><span class="sxs-lookup"><span data-stu-id="291b7-172">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

    ```csharp
    using BooksApi.Models;
    ```

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="291b7-173">Přidání služby operace CRUD</span><span class="sxs-lookup"><span data-stu-id="291b7-173">Add a CRUD operations service</span></span>

1. <span data-ttu-id="291b7-174">Přidejte adresář *služeb* do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="291b7-174">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="291b7-175">`BookService`Do adresáře *služby* přidejte třídu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="291b7-175">Add a `BookService` class to the *Services* directory with the following code:</span></span>

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

    <span data-ttu-id="291b7-176">V předchozím kódu `IBookstoreDatabaseSettings` je instance načtena z di přes injektáže konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="291b7-176">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="291b7-177">Tento postup poskytuje přístup k hodnotám konfigurace *appSettings. JSON* , které byly přidány do oddílu [přidat konfigurační model](#add-a-configuration-model) .</span><span class="sxs-lookup"><span data-stu-id="291b7-177">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="291b7-178">Přidejte následující zvýrazněný kód do `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="291b7-178">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

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

    <span data-ttu-id="291b7-179">V předchozím kódu `BookService` je třída registrována pomocí příkazu di k podpoře injektáže konstruktoru v rámci využívání tříd.</span><span class="sxs-lookup"><span data-stu-id="291b7-179">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="291b7-180">Doba životnosti služby singleton je nejvhodnější `BookService` , protože používá přímou závislost na `MongoClient` .</span><span class="sxs-lookup"><span data-stu-id="291b7-180">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="291b7-181">Podle oficiálních [zásad opětovného použití pro klienty Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use) `MongoClient` by se měla registrovat v di s životností služby typu singleton.</span><span class="sxs-lookup"><span data-stu-id="291b7-181">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="291b7-182">Přidejte následující kód na začátek *Startup.cs* k vyřešení `BookService` odkazu:</span><span class="sxs-lookup"><span data-stu-id="291b7-182">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>


    ```csharp
    using BooksApi.Services;
    ```

<span data-ttu-id="291b7-183">`BookService`Třída používá následující `MongoDB.Driver` členy k provádění operací CRUD proti databázi:</span><span class="sxs-lookup"><span data-stu-id="291b7-183">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="291b7-184">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): přečte instanci serveru pro provádění databázových operací.</span><span class="sxs-lookup"><span data-stu-id="291b7-184">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="291b7-185">K konstruktoru této třídy je poskytnutý připojovací řetězec MongoDB:</span><span class="sxs-lookup"><span data-stu-id="291b7-185">The constructor of this class is provided the MongoDB connection string:</span></span>

    ```csharp
    public BookService(IBookstoreDatabaseSettings settings)
    {
        var client = new MongoClient(settings.ConnectionString);
        var database = client.GetDatabase(settings.DatabaseName);

        _books = database.GetCollection<Book>(settings.BooksCollectionName);
    }
    ```

* <span data-ttu-id="291b7-186">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): představuje databázi Mongo pro provádění operací.</span><span class="sxs-lookup"><span data-stu-id="291b7-186">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="291b7-187">V tomto kurzu se používá obecná metoda [GetCollection \<TDocument> (Collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) na rozhraní pro získání přístupu k datům v určité kolekci.</span><span class="sxs-lookup"><span data-stu-id="291b7-187">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="291b7-188">Provede operace CRUD proti kolekci po volání této metody.</span><span class="sxs-lookup"><span data-stu-id="291b7-188">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="291b7-189">Ve `GetCollection<TDocument>(collection)` volání metody:</span><span class="sxs-lookup"><span data-stu-id="291b7-189">In the `GetCollection<TDocument>(collection)` method call:</span></span>
  * <span data-ttu-id="291b7-190">`collection`představuje název kolekce.</span><span class="sxs-lookup"><span data-stu-id="291b7-190">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="291b7-191">`TDocument`představuje typ objektu CLR uložený v kolekci.</span><span class="sxs-lookup"><span data-stu-id="291b7-191">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="291b7-192">`GetCollection<TDocument>(collection)`Vrátí objekt [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) představující kolekci.</span><span class="sxs-lookup"><span data-stu-id="291b7-192">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="291b7-193">V tomto kurzu jsou v kolekci vyvolány následující metody:</span><span class="sxs-lookup"><span data-stu-id="291b7-193">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="291b7-194">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Odstraní jeden dokument, který odpovídá zadaným kritériím hledání.</span><span class="sxs-lookup"><span data-stu-id="291b7-194">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="291b7-195">[Find \<TDocument> ](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): vrátí všechny dokumenty v kolekci, které odpovídají zadaným kritériím hledání.</span><span class="sxs-lookup"><span data-stu-id="291b7-195">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="291b7-196">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Vloží zadaný objekt jako nový dokument v kolekci.</span><span class="sxs-lookup"><span data-stu-id="291b7-196">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="291b7-197">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): nahradí jediný dokument, který odpovídá zadaným kritériím hledání, dodanému objektu.</span><span class="sxs-lookup"><span data-stu-id="291b7-197">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="291b7-198">Přidání kontroleru</span><span class="sxs-lookup"><span data-stu-id="291b7-198">Add a controller</span></span>

<span data-ttu-id="291b7-199">Přidejte `BooksController` třídu do adresáře *Controllers* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="291b7-199">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

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

<span data-ttu-id="291b7-200">Předchozí kontroler webového rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="291b7-200">The preceding web API controller:</span></span>

* <span data-ttu-id="291b7-201">Používá `BookService` třídu k provádění operací CRUD.</span><span class="sxs-lookup"><span data-stu-id="291b7-201">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="291b7-202">Obsahuje metody akcí, které podporují požadavky HTTP GET, POST, PUT a DELETE.</span><span class="sxs-lookup"><span data-stu-id="291b7-202">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="291b7-203">Volání <xref:System.Web.Http.ApiController.CreatedAtRoute*> v `Create` metodě Action vrátí odpověď [http 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) .</span><span class="sxs-lookup"><span data-stu-id="291b7-203">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="291b7-204">Stavový kód 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="291b7-204">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="291b7-205">`CreatedAtRoute`Přidá také `Location` hlavičku do odpovědi.</span><span class="sxs-lookup"><span data-stu-id="291b7-205">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="291b7-206">`Location`Hlavička Určuje identifikátor URI nově vytvořené knihy.</span><span class="sxs-lookup"><span data-stu-id="291b7-206">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="291b7-207">Testování webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="291b7-207">Test the web API</span></span>

1. <span data-ttu-id="291b7-208">Sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="291b7-208">Build and run the app.</span></span>

1. <span data-ttu-id="291b7-209">Přejděte na `http://localhost:<port>/api/books` k otestování metody akce bez parametrů řadiče `Get` .</span><span class="sxs-lookup"><span data-stu-id="291b7-209">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="291b7-210">Zobrazí se následující odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="291b7-210">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="291b7-211">Přejděte na `http://localhost:<port>/api/books/{id here}` k otestování metody akce, která je přetížena řadičem `Get` .</span><span class="sxs-lookup"><span data-stu-id="291b7-211">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="291b7-212">Zobrazí se následující odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="291b7-212">The following JSON response is displayed:</span></span>

    ```json
    {
      "id":"{ID}",
      "bookName":"Clean Code",
      "price":43.15,
      "category":"Computers",
      "author":"Robert C. Martin"
    }
    ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="291b7-213">Konfigurace možností serializace JSON</span><span class="sxs-lookup"><span data-stu-id="291b7-213">Configure JSON serialization options</span></span>

<span data-ttu-id="291b7-214">Existují dvě podrobnosti o tom, jak můžete změnit informace o odpovědích JSON vrácených v části [Test webového rozhraní API](#test-the-web-api) :</span><span class="sxs-lookup"><span data-stu-id="291b7-214">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="291b7-215">Názvy vlastností výchozí ve stylu CamelCase velká a malá písmena by se měly změnit tak, aby odpovídaly názvu vlastností objektu CLR na velká písmena Pascal.</span><span class="sxs-lookup"><span data-stu-id="291b7-215">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="291b7-216">`bookName`Vlastnost by měla být vrácena jako `Name` .</span><span class="sxs-lookup"><span data-stu-id="291b7-216">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="291b7-217">Chcete-li splnit předchozí požadavky, proveďte následující změny:</span><span class="sxs-lookup"><span data-stu-id="291b7-217">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="291b7-218">JSON.NET se odebral ze sdílené architektury ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="291b7-218">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="291b7-219">Přidejte odkaz na balíček do [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) .</span><span class="sxs-lookup"><span data-stu-id="291b7-219">Add a package reference to [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="291b7-220">V `Startup.ConfigureServices` , řetězení následujícího zvýrazněného kódu k `AddMvc` volání metody:</span><span class="sxs-lookup"><span data-stu-id="291b7-220">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

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

    <span data-ttu-id="291b7-221">V předchozí změně názvy vlastností v serializovaných odpovědích JSON webového rozhraní API odpovídají jejich odpovídajícím názvům vlastností v typu objektu CLR.</span><span class="sxs-lookup"><span data-stu-id="291b7-221">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="291b7-222">Například `Book` vlastnost třídy je `Author` serializována jako `Author` .</span><span class="sxs-lookup"><span data-stu-id="291b7-222">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="291b7-223">V části *Models/Book. cs*poznámku k `BookName` vlastnosti s následujícím [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) atributem:</span><span class="sxs-lookup"><span data-stu-id="291b7-223">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

    ```csharp
    [BsonElement("Name")]
    [JsonProperty("Name")]
    public string BookName { get; set; }
    ```

    <span data-ttu-id="291b7-224">`[JsonProperty]`Hodnota atributu `Name` představuje název vlastnosti v serializované odpovědi JSON webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="291b7-224">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="291b7-225">Přidejte následující kód na začátek *modelů/Book. cs* pro vyřešení `[JsonProperty]` odkazu na atribut:</span><span class="sxs-lookup"><span data-stu-id="291b7-225">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

    ```csharp
    using Newtonsoft.Json;
    ```

1. <span data-ttu-id="291b7-226">Opakujte kroky definované v části [Test webového rozhraní API](#test-the-web-api) .</span><span class="sxs-lookup"><span data-stu-id="291b7-226">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="291b7-227">Všimněte si rozdílu v názvech vlastností JSON.</span><span class="sxs-lookup"><span data-stu-id="291b7-227">Notice the difference in JSON property names.</span></span>

## <a name="next-steps"></a><span data-ttu-id="291b7-228">Další kroky</span><span class="sxs-lookup"><span data-stu-id="291b7-228">Next steps</span></span>

<span data-ttu-id="291b7-229">Další informace o vytváření ASP.NET Core webových rozhraní API najdete v následujících zdrojích informací:</span><span class="sxs-lookup"><span data-stu-id="291b7-229">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="291b7-230">YouTube verze tohoto článku</span><span class="sxs-lookup"><span data-stu-id="291b7-230">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* [<span data-ttu-id="291b7-231">Vytváření webových rozhraní API pomocí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="291b7-231">Create web APIs with ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/web-api/index?view=aspnetcore-3.0)

---
page_type: sample
description: Tento kurz ukazuje, jak vytvořit ASP.NET základní webové rozhraní API pomocí databáze MongoDB NoSQL.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
urlFragment: aspnetcore-webapi-mongodb
ms.openlocfilehash: 09d73e25667822b8748a00cc76ad6d4f0e5fe290
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511402"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="1a2a8-102">Vytvoření webového rozhraní API s ASP.NET Core a MongoDB</span><span class="sxs-lookup"><span data-stu-id="1a2a8-102">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="1a2a8-103">Tento kurz vytvoří webové rozhraní API, které provádí operace Vytvoření, Čtení, Aktualizace a Odstranění (CRUD) v databázi [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-103">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="1a2a8-104">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="1a2a8-104">In this tutorial, you learn how to:</span></span>

* <span data-ttu-id="1a2a8-105">Konfigurace mongoDB</span><span class="sxs-lookup"><span data-stu-id="1a2a8-105">Configure MongoDB</span></span>
* <span data-ttu-id="1a2a8-106">Vytvoření databáze MongoDB</span><span class="sxs-lookup"><span data-stu-id="1a2a8-106">Create a MongoDB database</span></span>
* <span data-ttu-id="1a2a8-107">Definování kolekce a schématu MongoDB</span><span class="sxs-lookup"><span data-stu-id="1a2a8-107">Define a MongoDB collection and schema</span></span>
* <span data-ttu-id="1a2a8-108">Provádění operací MongoDB CRUD z webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="1a2a8-108">Perform MongoDB CRUD operations from a web API</span></span>
* <span data-ttu-id="1a2a8-109">Přizpůsobení serializace JSON</span><span class="sxs-lookup"><span data-stu-id="1a2a8-109">Customize JSON serialization</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1a2a8-110">Požadavky</span><span class="sxs-lookup"><span data-stu-id="1a2a8-110">Prerequisites</span></span>

* [<span data-ttu-id="1a2a8-111">.NET Core SDK 3.0 nebo novější</span><span class="sxs-lookup"><span data-stu-id="1a2a8-111">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="1a2a8-112">[Visual Studio 2019 Preview](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=community&ch=pre&rel=16&utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019preview) s **ASP.NET a zatížením pro vývoj webů**</span><span class="sxs-lookup"><span data-stu-id="1a2a8-112">[Visual Studio 2019 Preview](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=community&ch=pre&rel=16&utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019preview) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="1a2a8-113">MongoDB</span><span class="sxs-lookup"><span data-stu-id="1a2a8-113">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

## <a name="configure-mongodb"></a><span data-ttu-id="1a2a8-114">Konfigurace mongoDB</span><span class="sxs-lookup"><span data-stu-id="1a2a8-114">Configure MongoDB</span></span>

<span data-ttu-id="1a2a8-115">Pokud používáte systém Windows, MongoDB je nainstalován na *C:\\Program Files\\MongoDB* ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-115">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="1a2a8-116">Přidat *C:\\\\Program Files\\MongoDB\\\<Server version_number>\\přihrádku* `Path` do proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-116">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="1a2a8-117">Tato změna umožňuje mongoDB přístup z libovolného místa na vývojovém počítači.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-117">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="1a2a8-118">Pomocí prostředí mongo v následujících krocích vytvořte databázi, vytvořte kolekce a uložte dokumenty.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-118">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="1a2a8-119">Další informace o příkazech mongo Shell naleznete [v tématu Práce s mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="1a2a8-119">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="1a2a8-120">Zvolte adresář ve vývojovém počítači pro ukládání dat.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-120">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="1a2a8-121">Například *C:\\BooksData v* systému Windows.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-121">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="1a2a8-122">Vytvořte adresář, pokud neexistuje.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-122">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="1a2a8-123">Mongo Shell nevytváří nové adresáře.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-123">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="1a2a8-124">Otevřete příkazové prostředí.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-124">Open a command shell.</span></span> <span data-ttu-id="1a2a8-125">Spusťte následující příkaz pro připojení k MongoDB na výchozím portu 27017.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-125">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="1a2a8-126">Nezapomeňte nahradit `<data_directory_path>` adresář, který jste zvolili v předchozím kroku.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-126">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. <span data-ttu-id="1a2a8-127">Otevřete jinou instanci příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-127">Open another command shell instance.</span></span> <span data-ttu-id="1a2a8-128">Připojte se k výchozí testovací databázi spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="1a2a8-128">Connect to the default test database by running the following command:</span></span>

    ```console
    mongo
    ```

1. <span data-ttu-id="1a2a8-129">V příkazovém prostředí spusťte následující:</span><span class="sxs-lookup"><span data-stu-id="1a2a8-129">Run the following in a command shell:</span></span>

    ```console
    use BookstoreDb
    ```

    <span data-ttu-id="1a2a8-130">Pokud ještě neexistuje, je vytvořena databáze s názvem *BookstoreDb.*</span><span class="sxs-lookup"><span data-stu-id="1a2a8-130">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="1a2a8-131">Pokud databáze existuje, její připojení je otevřen pro transakce.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-131">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="1a2a8-132">Vytvořte `Books` kolekci pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="1a2a8-132">Create a `Books` collection using following command:</span></span>

    ```console
    db.createCollection('Books')
    ```

    <span data-ttu-id="1a2a8-133">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="1a2a8-133">The following result is displayed:</span></span>

    ```console
    { "ok" : 1 }
    ```

1. <span data-ttu-id="1a2a8-134">Definujte schéma kolekce `Books` a vložte dva dokumenty pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="1a2a8-134">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    <span data-ttu-id="1a2a8-135">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="1a2a8-135">The following result is displayed:</span></span>

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
  > <span data-ttu-id="1a2a8-136">ID je uvedeno v tomto článku nebude odpovídat ID při spuštění této ukázky.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-136">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="1a2a8-137">Dokumenty v databázi můžete zobrazit pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="1a2a8-137">View the documents in the database using the following command:</span></span>

    ```console
    db.Books.find({}).pretty()
    ```

    <span data-ttu-id="1a2a8-138">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="1a2a8-138">The following result is displayed:</span></span>

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

    <span data-ttu-id="1a2a8-139">Schéma přidá automaticky vygenerované `_id` vlastnosti typu `ObjectId` pro každý dokument.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-139">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="1a2a8-140">Databáze je připravena.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-140">The database is ready.</span></span> <span data-ttu-id="1a2a8-141">Můžete začít vytvářet ASP.NET základní webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-141">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="1a2a8-142">Vytvoření projektu webového rozhraní API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1a2a8-142">Create the ASP.NET Core web API project</span></span>

1. <span data-ttu-id="1a2a8-143">Přejděte do **souboru** > **nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-143">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="1a2a8-144">Vyberte typ projektu **základní webové aplikace ASP.NET** a vyberte **další**.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-144">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="1a2a8-145">Pojmenujte projekt *BooksApi*a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-145">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="1a2a8-146">Vyberte cílovou architekturu **.NET Core** a **ASP.NET Core 3.0**.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-146">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="1a2a8-147">Vyberte šablonu projektu **rozhraní API** a vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-147">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="1a2a8-148">Navštivte [Galerii NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) a zjistěte nejnovější stabilní verzi ovladače .NET pro MongoDB.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-148">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="1a2a8-149">V okně **Konzola Správce balíčků** přejděte do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-149">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="1a2a8-150">Spuštěním následujícího příkazu nainstalujte ovladač .NET pro MongoDB:</span><span class="sxs-lookup"><span data-stu-id="1a2a8-150">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

## <a name="add-an-entity-model"></a><span data-ttu-id="1a2a8-151">Přidání modelu entity</span><span class="sxs-lookup"><span data-stu-id="1a2a8-151">Add an entity model</span></span>

1. <span data-ttu-id="1a2a8-152">Přidejte adresář *Models* do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-152">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="1a2a8-153">Přidejte `Book` třídu do adresáře *Models* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="1a2a8-153">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

    <span data-ttu-id="1a2a8-154">V předchozí třídě `Id` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="1a2a8-154">In the preceding class, the `Id` property:</span></span>

    * <span data-ttu-id="1a2a8-155">Je vyžadováno pro mapování objektu CLR (Common Language Runtime) do kolekce MongoDB.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-155">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
    * <span data-ttu-id="1a2a8-156">Je anotován [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) s určit tuto vlastnost jako primární klíč dokumentu.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-156">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
    * <span data-ttu-id="1a2a8-157">Je anotován [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) s povolit předávání `string` parametru jako typ namísto [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) struktury.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-157">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="1a2a8-158">Mongo zpracovává převod `string` z `ObjectId`do .</span><span class="sxs-lookup"><span data-stu-id="1a2a8-158">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

    <span data-ttu-id="1a2a8-159">Vlastnost `BookName` je anotována [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) s atributem.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-159">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="1a2a8-160">Hodnota atributu `Name` představuje název vlastnosti v kolekci MongoDB.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-160">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="1a2a8-161">Přidání konfiguračního modelu</span><span class="sxs-lookup"><span data-stu-id="1a2a8-161">Add a configuration model</span></span>

1. <span data-ttu-id="1a2a8-162">Přidejte do *souboru appsettings.json*následující hodnoty konfigurace databáze :</span><span class="sxs-lookup"><span data-stu-id="1a2a8-162">Add the following database configuration values to *appsettings.json*:</span></span>

    ```javascript
    {
      "BookstoreDatabaseSettings": {
        "BooksCollectionName": "Books",
        "ConnectionString": "mongodb://localhost:27017",
        "DatabaseName": "BookstoreDb"
      },

    ```

1. <span data-ttu-id="1a2a8-163">Přidejte *soubor BookstoreDatabaseSettings.cs* do adresáře *Models* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="1a2a8-163">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

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

    <span data-ttu-id="1a2a8-164">Předchozí `BookstoreDatabaseSettings` třída se používá k uložení hodnot `BookstoreDatabaseSettings` vlastností souboru *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="1a2a8-164">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="1a2a8-165">Názvy vlastností JSON a C# jsou pojmenovány identicky pro usnadnění procesu mapování.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-165">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="1a2a8-166">Do položky přidejte `Startup.ConfigureServices`následující zvýrazněný kód :</span><span class="sxs-lookup"><span data-stu-id="1a2a8-166">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

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

    <span data-ttu-id="1a2a8-167">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="1a2a8-167">In the preceding code:</span></span>

    * <span data-ttu-id="1a2a8-168">Instance konfigurace, na kterou se váže `BookstoreDatabaseSettings` oddíl souboru *appsettings.json,* je registrována v kontejneru DI vkládání závislostí (DI).</span><span class="sxs-lookup"><span data-stu-id="1a2a8-168">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="1a2a8-169">`BookstoreDatabaseSettings` Například `ConnectionString` vlastnost objektu je naplněna `BookstoreDatabaseSettings:ConnectionString` vlastností v *souboru appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-169">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
    * <span data-ttu-id="1a2a8-170">Rozhraní `IBookstoreDatabaseSettings` je registrováno v DI s [životností](xref:fundamentals/dependency-injection#service-lifetimes)singleton .</span><span class="sxs-lookup"><span data-stu-id="1a2a8-170">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="1a2a8-171">Při vložení instance rozhraní překládá `BookstoreDatabaseSettings` na objekt.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-171">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="1a2a8-172">Přidejte následující kód na *Startup.cs* začátek `BookstoreDatabaseSettings` Startup.cs `IBookstoreDatabaseSettings` chcete vyřešit a odkazy:</span><span class="sxs-lookup"><span data-stu-id="1a2a8-172">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

    ```csharp
    using BooksApi.Models;
    ```

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="1a2a8-173">Přidání provozní služby CRUD</span><span class="sxs-lookup"><span data-stu-id="1a2a8-173">Add a CRUD operations service</span></span>

1. <span data-ttu-id="1a2a8-174">Přidejte adresář *služby* do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-174">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="1a2a8-175">Přidejte `BookService` třídu do *adresáře Služby* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="1a2a8-175">Add a `BookService` class to the *Services* directory with the following code:</span></span>

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

    <span data-ttu-id="1a2a8-176">V předchozím kódu `IBookstoreDatabaseSettings` instance je načten z DI prostřednictvím vkládání konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-176">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="1a2a8-177">Tato technika poskytuje přístup k hodnotám konfigurace *appsettings.json,* které byly přidány v části [Přidat model konfigurace.](#add-a-configuration-model)</span><span class="sxs-lookup"><span data-stu-id="1a2a8-177">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="1a2a8-178">Do položky přidejte `Startup.ConfigureServices`následující zvýrazněný kód :</span><span class="sxs-lookup"><span data-stu-id="1a2a8-178">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

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

    <span data-ttu-id="1a2a8-179">V předchozím kódu je `BookService` třída registrována u DI pro podporu vkládání konstruktoru ve spotřebních třídách.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-179">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="1a2a8-180">Životnost služby singleton je `BookService` nejvhodnější, protože `MongoClient`má přímou závislost na .</span><span class="sxs-lookup"><span data-stu-id="1a2a8-180">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="1a2a8-181">Podle oficiálních pokynů pro opakované `MongoClient` použití [klienta Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)by měly být registrovány v DI s životností singleton.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-181">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="1a2a8-182">Chcete-li `BookService` odkaz vyřešit, přidejte na začátek *Startup.cs* následující kód:</span><span class="sxs-lookup"><span data-stu-id="1a2a8-182">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>


    ```csharp
    using BooksApi.Services;
    ```

<span data-ttu-id="1a2a8-183">Třída `BookService` používá následující `MongoDB.Driver` členy k provádění operací CRUD proti databázi:</span><span class="sxs-lookup"><span data-stu-id="1a2a8-183">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="1a2a8-184">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Přečte instanci serveru pro provádění databázových operací.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-184">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="1a2a8-185">Konstruktor této třídy je k dispozici připojovací řetězec MongoDB:</span><span class="sxs-lookup"><span data-stu-id="1a2a8-185">The constructor of this class is provided the MongoDB connection string:</span></span>

    ```csharp
    public BookService(IBookstoreDatabaseSettings settings)
    {
        var client = new MongoClient(settings.ConnectionString);
        var database = client.GetDatabase(settings.DatabaseName);

        _books = database.GetCollection<Book>(settings.BooksCollectionName);
    }
    ```

* <span data-ttu-id="1a2a8-186">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Představuje databázi Mongo pro provádění operací.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-186">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="1a2a8-187">Tento kurz používá obecnou metodu [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) v rozhraní k získání přístupu k datům v konkrétní kolekci.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-187">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="1a2a8-188">Proveďte operace CRUD proti kolekci po volání této metody.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-188">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="1a2a8-189">Ve `GetCollection<TDocument>(collection)` volání metody:</span><span class="sxs-lookup"><span data-stu-id="1a2a8-189">In the `GetCollection<TDocument>(collection)` method call:</span></span>
  * <span data-ttu-id="1a2a8-190">`collection`představuje název kolekce.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-190">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="1a2a8-191">`TDocument`představuje typ objektu CLR uložené v kolekci.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-191">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="1a2a8-192">`GetCollection<TDocument>(collection)`vrátí [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) objekt představující kolekci.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-192">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="1a2a8-193">V tomto kurzu jsou v kolekci vyvolány následující metody:</span><span class="sxs-lookup"><span data-stu-id="1a2a8-193">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="1a2a8-194">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Odstraní jeden dokument odpovídající zadaným kritériím hledání.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-194">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="1a2a8-195">[Najít\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Vrátí všechny dokumenty v kolekci odpovídající zadaným kritériím hledání.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-195">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="1a2a8-196">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Vloží zadaný objekt jako nový dokument v kolekci.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-196">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="1a2a8-197">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Nahradí jeden dokument odpovídající zadaným kritériím hledání s poskytnutým objektem.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-197">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="1a2a8-198">Přidání kontroleru</span><span class="sxs-lookup"><span data-stu-id="1a2a8-198">Add a controller</span></span>

<span data-ttu-id="1a2a8-199">Přidejte `BooksController` třídu do *adresáře Řadiče* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="1a2a8-199">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

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

<span data-ttu-id="1a2a8-200">Předchozí řadič webového rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="1a2a8-200">The preceding web API controller:</span></span>

* <span data-ttu-id="1a2a8-201">Používá `BookService` třídu k provádění operací CRUD.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-201">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="1a2a8-202">Obsahuje metody akce pro podporu požadavků HTTP GET, POST, PUT a DELETE.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-202">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="1a2a8-203">Volání <xref:System.Web.Http.ApiController.CreatedAtRoute*> v `Create` metodě akce vrátit odpověď [HTTP 201.](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)</span><span class="sxs-lookup"><span data-stu-id="1a2a8-203">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="1a2a8-204">Stavový kód 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-204">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="1a2a8-205">`CreatedAtRoute`také přidá `Location` záhlaví odpovědi.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-205">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="1a2a8-206">Záhlaví `Location` určuje identifikátor URI nově vytvořené knihy.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-206">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="1a2a8-207">Testování webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="1a2a8-207">Test the web API</span></span>

1. <span data-ttu-id="1a2a8-208">Sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-208">Build and run the app.</span></span>

1. <span data-ttu-id="1a2a8-209">Přejděte `http://localhost:<port>/api/books` na otestovat ovladač `Get` bez parametry akce metody.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-209">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="1a2a8-210">Zobrazí se následující odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="1a2a8-210">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="1a2a8-211">Přejděte `http://localhost:<port>/api/books/{id here}` na otestovat přetížené `Get` metody akce řadiče.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-211">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="1a2a8-212">Zobrazí se následující odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="1a2a8-212">The following JSON response is displayed:</span></span>

    ```json
    {
      "id":"{ID}",
      "bookName":"Clean Code",
      "price":43.15,
      "category":"Computers",
      "author":"Robert C. Martin"
    }
    ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="1a2a8-213">Konfigurace možností serializace JSON</span><span class="sxs-lookup"><span data-stu-id="1a2a8-213">Configure JSON serialization options</span></span>

<span data-ttu-id="1a2a8-214">Existují dva podrobnosti změnit o odpovědi JSON vrácené v [části Test webové rozhraní API:](#test-the-web-api)</span><span class="sxs-lookup"><span data-stu-id="1a2a8-214">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="1a2a8-215">Výchozí camel ové pouzdro názvů vlastností by mělo být změněno tak, aby odpovídalo pouzdrům Pascal názvů vlastností objektu CLR.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-215">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="1a2a8-216">Vlastnost `bookName` by měla `Name`být vrácena jako .</span><span class="sxs-lookup"><span data-stu-id="1a2a8-216">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="1a2a8-217">Chcete-li splnit předchozí požadavky, proveďte následující změny:</span><span class="sxs-lookup"><span data-stu-id="1a2a8-217">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="1a2a8-218">JSON.NET byla odebrána ze sdíleného rámce ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-218">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="1a2a8-219">Přidejte odkaz na balíček [microsoft.aspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span><span class="sxs-lookup"><span data-stu-id="1a2a8-219">Add a package reference to [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="1a2a8-220">V `Startup.ConfigureServices`, řetěz následující zvýrazněný `AddMvc` kód na volání metody:</span><span class="sxs-lookup"><span data-stu-id="1a2a8-220">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

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

    <span data-ttu-id="1a2a8-221">S předchozí změnou názvy vlastností v serializované odpovědi JSON webového rozhraní API odpovídají jejich odpovídajícím názvům vlastností v typu objektu CLR.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-221">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="1a2a8-222">Například `Book` `Author` vlastnost třídy serializuje jako `Author`.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-222">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="1a2a8-223">V *části Models/Book.cs*opatří `BookName` vlastnost poznámkami s následujícím [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) atributem:</span><span class="sxs-lookup"><span data-stu-id="1a2a8-223">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

    ```csharp
    [BsonElement("Name")]
    [JsonProperty("Name")]
    public string BookName { get; set; }
    ```

    <span data-ttu-id="1a2a8-224">Hodnota `[JsonProperty]` atributu `Name` představuje název vlastnosti v serializované odpovědi JSON webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-224">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="1a2a8-225">Přidejte následující kód do horní části *Models/Book.cs* vyřešit odkaz na `[JsonProperty]` atribut:</span><span class="sxs-lookup"><span data-stu-id="1a2a8-225">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

    ```csharp
    using Newtonsoft.Json;
    ```

1. <span data-ttu-id="1a2a8-226">Opakujte kroky definované v části [Otestovat webové rozhraní API.](#test-the-web-api)</span><span class="sxs-lookup"><span data-stu-id="1a2a8-226">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="1a2a8-227">Všimněte si rozdílu v názvech vlastností JSON.</span><span class="sxs-lookup"><span data-stu-id="1a2a8-227">Notice the difference in JSON property names.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1a2a8-228">Další kroky</span><span class="sxs-lookup"><span data-stu-id="1a2a8-228">Next steps</span></span>

<span data-ttu-id="1a2a8-229">Další informace o vytváření ASP.NET základní webová api naleznete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="1a2a8-229">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="1a2a8-230">Verze tohoto článku na YouTube</span><span class="sxs-lookup"><span data-stu-id="1a2a8-230">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* [<span data-ttu-id="1a2a8-231">Vytváření webových api s ASP.NET jádrem</span><span class="sxs-lookup"><span data-stu-id="1a2a8-231">Create web APIs with ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/web-api/index?view=aspnetcore-3.0)

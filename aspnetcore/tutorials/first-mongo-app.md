---
title: Vytvoření webového rozhraní API s ASP.NET Core a MongoDB
author: prkhandelwal
description: Tento kurz ukazuje, jak vytvořit ASP.NET základní webové rozhraní API pomocí databáze MongoDB NoSQL.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
uid: tutorials/first-mongo-app
ms.openlocfilehash: d5ce4a1dc3c00b2b12edc12e26f482caa97df6b3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511415"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="1953d-103">Vytvoření webového rozhraní API s ASP.NET Core a MongoDB</span><span class="sxs-lookup"><span data-stu-id="1953d-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="1953d-104">Podle [Pratik Khandelwal](https://twitter.com/K2Prk) a [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="1953d-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1953d-105">Tento kurz vytvoří webové rozhraní API, které provádí operace Vytvoření, Čtení, Aktualizace a Odstranění (CRUD) v databázi [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL.</span><span class="sxs-lookup"><span data-stu-id="1953d-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="1953d-106">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="1953d-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1953d-107">Konfigurace mongoDB</span><span class="sxs-lookup"><span data-stu-id="1953d-107">Configure MongoDB</span></span>
> * <span data-ttu-id="1953d-108">Vytvoření databáze MongoDB</span><span class="sxs-lookup"><span data-stu-id="1953d-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="1953d-109">Definování kolekce a schématu MongoDB</span><span class="sxs-lookup"><span data-stu-id="1953d-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="1953d-110">Provádění operací MongoDB CRUD z webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="1953d-110">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="1953d-111">Přizpůsobení serializace JSON</span><span class="sxs-lookup"><span data-stu-id="1953d-111">Customize JSON serialization</span></span>

<span data-ttu-id="1953d-112">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="1953d-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1953d-113">Požadavky</span><span class="sxs-lookup"><span data-stu-id="1953d-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1953d-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1953d-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="1953d-115">.NET Core SDK 3.0 nebo novější</span><span class="sxs-lookup"><span data-stu-id="1953d-115">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="1953d-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s **ASP.NET a zatížením vývoje webu**</span><span class="sxs-lookup"><span data-stu-id="1953d-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="1953d-117">MongoDB</span><span class="sxs-lookup"><span data-stu-id="1953d-117">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="1953d-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1953d-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="1953d-119">.NET Core SDK 3.0 nebo novější</span><span class="sxs-lookup"><span data-stu-id="1953d-119">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="1953d-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1953d-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="1953d-121">C# pro Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1953d-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="1953d-122">MongoDB</span><span class="sxs-lookup"><span data-stu-id="1953d-122">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1953d-123">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="1953d-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="1953d-124">.NET Core SDK 3.0 nebo novější</span><span class="sxs-lookup"><span data-stu-id="1953d-124">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="1953d-125">Visual Studio pro Mac verze 7.7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="1953d-125">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="1953d-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="1953d-126">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="1953d-127">Konfigurace mongoDB</span><span class="sxs-lookup"><span data-stu-id="1953d-127">Configure MongoDB</span></span>

<span data-ttu-id="1953d-128">Pokud používáte systém Windows, MongoDB je nainstalován na *C:\\Program Files\\MongoDB* ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="1953d-128">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="1953d-129">Přidat *C:\\\\Program Files\\MongoDB\\\<Server version_number>\\přihrádku* `Path` do proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="1953d-129">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="1953d-130">Tato změna umožňuje mongoDB přístup z libovolného místa na vývojovém počítači.</span><span class="sxs-lookup"><span data-stu-id="1953d-130">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="1953d-131">Pomocí prostředí mongo v následujících krocích vytvořte databázi, vytvořte kolekce a uložte dokumenty.</span><span class="sxs-lookup"><span data-stu-id="1953d-131">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="1953d-132">Další informace o příkazech mongo Shell naleznete [v tématu Práce s mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="1953d-132">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="1953d-133">Zvolte adresář ve vývojovém počítači pro ukládání dat.</span><span class="sxs-lookup"><span data-stu-id="1953d-133">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="1953d-134">Například *C:\\BooksData v* systému Windows.</span><span class="sxs-lookup"><span data-stu-id="1953d-134">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="1953d-135">Vytvořte adresář, pokud neexistuje.</span><span class="sxs-lookup"><span data-stu-id="1953d-135">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="1953d-136">Mongo Shell nevytváří nové adresáře.</span><span class="sxs-lookup"><span data-stu-id="1953d-136">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="1953d-137">Otevřete příkazové prostředí.</span><span class="sxs-lookup"><span data-stu-id="1953d-137">Open a command shell.</span></span> <span data-ttu-id="1953d-138">Spusťte následující příkaz pro připojení k MongoDB na výchozím portu 27017.</span><span class="sxs-lookup"><span data-stu-id="1953d-138">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="1953d-139">Nezapomeňte nahradit `<data_directory_path>` adresář, který jste zvolili v předchozím kroku.</span><span class="sxs-lookup"><span data-stu-id="1953d-139">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="1953d-140">Otevřete jinou instanci příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="1953d-140">Open another command shell instance.</span></span> <span data-ttu-id="1953d-141">Připojte se k výchozí testovací databázi spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="1953d-141">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="1953d-142">V příkazovém prostředí spusťte následující:</span><span class="sxs-lookup"><span data-stu-id="1953d-142">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="1953d-143">Pokud ještě neexistuje, je vytvořena databáze s názvem *BookstoreDb.*</span><span class="sxs-lookup"><span data-stu-id="1953d-143">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="1953d-144">Pokud databáze existuje, její připojení je otevřen pro transakce.</span><span class="sxs-lookup"><span data-stu-id="1953d-144">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="1953d-145">Vytvořte `Books` kolekci pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="1953d-145">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="1953d-146">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="1953d-146">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="1953d-147">Definujte schéma kolekce `Books` a vložte dva dokumenty pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="1953d-147">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="1953d-148">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="1953d-148">The following result is displayed:</span></span>

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
   > <span data-ttu-id="1953d-149">ID je uvedeno v tomto článku nebude odpovídat ID při spuštění této ukázky.</span><span class="sxs-lookup"><span data-stu-id="1953d-149">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="1953d-150">Dokumenty v databázi můžete zobrazit pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="1953d-150">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="1953d-151">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="1953d-151">The following result is displayed:</span></span>

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

   <span data-ttu-id="1953d-152">Schéma přidá automaticky vygenerované `_id` vlastnosti typu `ObjectId` pro každý dokument.</span><span class="sxs-lookup"><span data-stu-id="1953d-152">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="1953d-153">Databáze je připravena.</span><span class="sxs-lookup"><span data-stu-id="1953d-153">The database is ready.</span></span> <span data-ttu-id="1953d-154">Můžete začít vytvářet ASP.NET základní webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="1953d-154">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="1953d-155">Vytvoření projektu webového rozhraní API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1953d-155">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1953d-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1953d-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="1953d-157">Přejděte do **souboru** > **nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="1953d-157">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="1953d-158">Vyberte typ projektu **základní webové aplikace ASP.NET** a vyberte **další**.</span><span class="sxs-lookup"><span data-stu-id="1953d-158">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="1953d-159">Pojmenujte projekt *BooksApi*a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="1953d-159">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="1953d-160">Vyberte cílovou architekturu **.NET Core** a **ASP.NET Core 3.0**.</span><span class="sxs-lookup"><span data-stu-id="1953d-160">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="1953d-161">Vyberte šablonu projektu **rozhraní API** a vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="1953d-161">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="1953d-162">Navštivte [Galerii NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) a zjistěte nejnovější stabilní verzi ovladače .NET pro MongoDB.</span><span class="sxs-lookup"><span data-stu-id="1953d-162">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="1953d-163">V okně **Konzola Správce balíčků** přejděte do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="1953d-163">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="1953d-164">Spuštěním následujícího příkazu nainstalujte ovladač .NET pro MongoDB:</span><span class="sxs-lookup"><span data-stu-id="1953d-164">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="1953d-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1953d-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="1953d-166">Spusťte v příkazovém prostředí následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="1953d-166">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="1953d-167">Nový ASP.NET core web API projekt cílení .NET Core je generována a otevřena v kódu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1953d-167">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="1953d-168">Poté, co ikona plamene OmniSharp na stavovém řádku zezelenaje, dialogové okno požádá **požadované datové zdroje k sestavení a ladění v rozhraní BooksApi. Přidat je?**.</span><span class="sxs-lookup"><span data-stu-id="1953d-168">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="1953d-169">Vyberte **ano**.</span><span class="sxs-lookup"><span data-stu-id="1953d-169">Select **Yes**.</span></span>
1. <span data-ttu-id="1953d-170">Navštivte [Galerii NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) a zjistěte nejnovější stabilní verzi ovladače .NET pro MongoDB.</span><span class="sxs-lookup"><span data-stu-id="1953d-170">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="1953d-171">Otevřete **integrovaný terminál** a přejděte do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="1953d-171">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="1953d-172">Spuštěním následujícího příkazu nainstalujte ovladač .NET pro MongoDB:</span><span class="sxs-lookup"><span data-stu-id="1953d-172">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1953d-173">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="1953d-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="1953d-174">Přejděte na soubor > **novéřešení** > **.NET Core** > **File** **App**.</span><span class="sxs-lookup"><span data-stu-id="1953d-174">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="1953d-175">Vyberte šablonu projektu **core webového rozhraní API** C# ASP.NET a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="1953d-175">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="1953d-176">V rozevíracím seznamu **Cílová architektura** vyberte **.NET Core 3.0** a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="1953d-176">Select **.NET Core 3.0** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="1953d-177">Zadejte *BooksApi* pro **název projektu**a vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="1953d-177">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="1953d-178">Na panelu **Řešení** klepněte pravým tlačítkem myši na uzel **Závislosti** projektu a vyberte přidat **balíčky**.</span><span class="sxs-lookup"><span data-stu-id="1953d-178">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="1953d-179">Do vyhledávacího pole zadejte *MongoDB.Driver,* vyberte balíček *MongoDB.Driver* a vyberte **Přidat balíček**.</span><span class="sxs-lookup"><span data-stu-id="1953d-179">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="1953d-180">V dialogovém okně **Přijetí licence** vyberte tlačítko **Přijmout.**</span><span class="sxs-lookup"><span data-stu-id="1953d-180">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="1953d-181">Přidání modelu entity</span><span class="sxs-lookup"><span data-stu-id="1953d-181">Add an entity model</span></span>

1. <span data-ttu-id="1953d-182">Přidejte adresář *Models* do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="1953d-182">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="1953d-183">Přidejte `Book` třídu do adresáře *Models* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="1953d-183">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

   <span data-ttu-id="1953d-184">V předchozí třídě `Id` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="1953d-184">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="1953d-185">Je vyžadováno pro mapování objektu CLR (Common Language Runtime) do kolekce MongoDB.</span><span class="sxs-lookup"><span data-stu-id="1953d-185">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="1953d-186">Je anotován [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) s určit tuto vlastnost jako primární klíč dokumentu.</span><span class="sxs-lookup"><span data-stu-id="1953d-186">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="1953d-187">Je anotován [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) s povolit předávání `string` parametru jako typ namísto [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) struktury.</span><span class="sxs-lookup"><span data-stu-id="1953d-187">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="1953d-188">Mongo zpracovává převod `string` z `ObjectId`do .</span><span class="sxs-lookup"><span data-stu-id="1953d-188">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="1953d-189">Vlastnost `BookName` je anotována [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) s atributem.</span><span class="sxs-lookup"><span data-stu-id="1953d-189">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="1953d-190">Hodnota atributu `Name` představuje název vlastnosti v kolekci MongoDB.</span><span class="sxs-lookup"><span data-stu-id="1953d-190">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="1953d-191">Přidání konfiguračního modelu</span><span class="sxs-lookup"><span data-stu-id="1953d-191">Add a configuration model</span></span>

1. <span data-ttu-id="1953d-192">Přidejte do *souboru appsettings.json*následující hodnoty konfigurace databáze :</span><span class="sxs-lookup"><span data-stu-id="1953d-192">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="1953d-193">Přidejte *soubor BookstoreDatabaseSettings.cs* do adresáře *Models* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="1953d-193">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="1953d-194">Předchozí `BookstoreDatabaseSettings` třída se používá k uložení hodnot `BookstoreDatabaseSettings` vlastností souboru *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="1953d-194">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="1953d-195">Názvy vlastností JSON a C# jsou pojmenovány identicky pro usnadnění procesu mapování.</span><span class="sxs-lookup"><span data-stu-id="1953d-195">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="1953d-196">Do položky přidejte `Startup.ConfigureServices`následující zvýrazněný kód :</span><span class="sxs-lookup"><span data-stu-id="1953d-196">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   <span data-ttu-id="1953d-197">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="1953d-197">In the preceding code:</span></span>

   * <span data-ttu-id="1953d-198">Instance konfigurace, na kterou se váže `BookstoreDatabaseSettings` oddíl souboru *appsettings.json,* je registrována v kontejneru DI vkládání závislostí (DI).</span><span class="sxs-lookup"><span data-stu-id="1953d-198">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="1953d-199">`BookstoreDatabaseSettings` Například `ConnectionString` vlastnost objektu je naplněna `BookstoreDatabaseSettings:ConnectionString` vlastností v *souboru appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="1953d-199">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="1953d-200">Rozhraní `IBookstoreDatabaseSettings` je registrováno v DI s [životností](xref:fundamentals/dependency-injection#service-lifetimes)singleton .</span><span class="sxs-lookup"><span data-stu-id="1953d-200">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="1953d-201">Při vložení instance rozhraní překládá `BookstoreDatabaseSettings` na objekt.</span><span class="sxs-lookup"><span data-stu-id="1953d-201">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="1953d-202">Přidejte následující kód na *Startup.cs* začátek `BookstoreDatabaseSettings` Startup.cs `IBookstoreDatabaseSettings` chcete vyřešit a odkazy:</span><span class="sxs-lookup"><span data-stu-id="1953d-202">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="1953d-203">Přidání provozní služby CRUD</span><span class="sxs-lookup"><span data-stu-id="1953d-203">Add a CRUD operations service</span></span>

1. <span data-ttu-id="1953d-204">Přidejte adresář *služby* do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="1953d-204">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="1953d-205">Přidejte `BookService` třídu do *adresáře Služby* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="1953d-205">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="1953d-206">V předchozím kódu `IBookstoreDatabaseSettings` instance je načten z DI prostřednictvím vkládání konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="1953d-206">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="1953d-207">Tato technika poskytuje přístup k hodnotám konfigurace *appsettings.json,* které byly přidány v části [Přidat model konfigurace.](#add-a-configuration-model)</span><span class="sxs-lookup"><span data-stu-id="1953d-207">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="1953d-208">Do položky přidejte `Startup.ConfigureServices`následující zvýrazněný kód :</span><span class="sxs-lookup"><span data-stu-id="1953d-208">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="1953d-209">V předchozím kódu je `BookService` třída registrována u DI pro podporu vkládání konstruktoru ve spotřebních třídách.</span><span class="sxs-lookup"><span data-stu-id="1953d-209">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="1953d-210">Životnost služby singleton je `BookService` nejvhodnější, protože `MongoClient`má přímou závislost na .</span><span class="sxs-lookup"><span data-stu-id="1953d-210">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="1953d-211">Podle oficiálních pokynů pro opakované `MongoClient` použití [klienta Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)by měly být registrovány v DI s životností singleton.</span><span class="sxs-lookup"><span data-stu-id="1953d-211">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="1953d-212">Chcete-li `BookService` odkaz vyřešit, přidejte na začátek *Startup.cs* následující kód:</span><span class="sxs-lookup"><span data-stu-id="1953d-212">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="1953d-213">Třída `BookService` používá následující `MongoDB.Driver` členy k provádění operací CRUD proti databázi:</span><span class="sxs-lookup"><span data-stu-id="1953d-213">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="1953d-214">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Přečte instanci serveru pro provádění databázových operací.</span><span class="sxs-lookup"><span data-stu-id="1953d-214">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="1953d-215">Konstruktor této třídy je k dispozici připojovací řetězec MongoDB:</span><span class="sxs-lookup"><span data-stu-id="1953d-215">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="1953d-216">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Představuje databázi Mongo pro provádění operací.</span><span class="sxs-lookup"><span data-stu-id="1953d-216">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="1953d-217">Tento kurz používá obecnou metodu [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) v rozhraní k získání přístupu k datům v konkrétní kolekci.</span><span class="sxs-lookup"><span data-stu-id="1953d-217">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="1953d-218">Proveďte operace CRUD proti kolekci po volání této metody.</span><span class="sxs-lookup"><span data-stu-id="1953d-218">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="1953d-219">Ve `GetCollection<TDocument>(collection)` volání metody:</span><span class="sxs-lookup"><span data-stu-id="1953d-219">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="1953d-220">`collection`představuje název kolekce.</span><span class="sxs-lookup"><span data-stu-id="1953d-220">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="1953d-221">`TDocument`představuje typ objektu CLR uložené v kolekci.</span><span class="sxs-lookup"><span data-stu-id="1953d-221">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="1953d-222">`GetCollection<TDocument>(collection)`vrátí [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) objekt představující kolekci.</span><span class="sxs-lookup"><span data-stu-id="1953d-222">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="1953d-223">V tomto kurzu jsou v kolekci vyvolány následující metody:</span><span class="sxs-lookup"><span data-stu-id="1953d-223">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="1953d-224">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Odstraní jeden dokument odpovídající zadaným kritériím hledání.</span><span class="sxs-lookup"><span data-stu-id="1953d-224">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="1953d-225">[Najít\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Vrátí všechny dokumenty v kolekci odpovídající zadaným kritériím hledání.</span><span class="sxs-lookup"><span data-stu-id="1953d-225">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="1953d-226">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Vloží zadaný objekt jako nový dokument v kolekci.</span><span class="sxs-lookup"><span data-stu-id="1953d-226">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="1953d-227">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Nahradí jeden dokument odpovídající zadaným kritériím hledání s poskytnutým objektem.</span><span class="sxs-lookup"><span data-stu-id="1953d-227">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="1953d-228">Přidání kontroleru</span><span class="sxs-lookup"><span data-stu-id="1953d-228">Add a controller</span></span>

<span data-ttu-id="1953d-229">Přidejte `BooksController` třídu do *adresáře Řadiče* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="1953d-229">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="1953d-230">Předchozí řadič webového rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="1953d-230">The preceding web API controller:</span></span>

* <span data-ttu-id="1953d-231">Používá `BookService` třídu k provádění operací CRUD.</span><span class="sxs-lookup"><span data-stu-id="1953d-231">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="1953d-232">Obsahuje metody akce pro podporu požadavků HTTP GET, POST, PUT a DELETE.</span><span class="sxs-lookup"><span data-stu-id="1953d-232">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="1953d-233">Volání <xref:System.Web.Http.ApiController.CreatedAtRoute*> v `Create` metodě akce vrátit odpověď [HTTP 201.](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)</span><span class="sxs-lookup"><span data-stu-id="1953d-233">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="1953d-234">Stavový kód 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="1953d-234">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="1953d-235">`CreatedAtRoute`také přidá `Location` záhlaví odpovědi.</span><span class="sxs-lookup"><span data-stu-id="1953d-235">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="1953d-236">Záhlaví `Location` určuje identifikátor URI nově vytvořené knihy.</span><span class="sxs-lookup"><span data-stu-id="1953d-236">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="1953d-237">Testování webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="1953d-237">Test the web API</span></span>

1. <span data-ttu-id="1953d-238">Sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1953d-238">Build and run the app.</span></span>

1. <span data-ttu-id="1953d-239">Přejděte `http://localhost:<port>/api/books` na otestovat ovladač `Get` bez parametry akce metody.</span><span class="sxs-lookup"><span data-stu-id="1953d-239">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="1953d-240">Zobrazí se následující odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="1953d-240">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="1953d-241">Přejděte `http://localhost:<port>/api/books/{id here}` na otestovat přetížené `Get` metody akce řadiče.</span><span class="sxs-lookup"><span data-stu-id="1953d-241">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="1953d-242">Zobrazí se následující odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="1953d-242">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="1953d-243">Konfigurace možností serializace JSON</span><span class="sxs-lookup"><span data-stu-id="1953d-243">Configure JSON serialization options</span></span>

<span data-ttu-id="1953d-244">Existují dva podrobnosti změnit o odpovědi JSON vrácené v [části Test webové rozhraní API:](#test-the-web-api)</span><span class="sxs-lookup"><span data-stu-id="1953d-244">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="1953d-245">Výchozí camel ové pouzdro názvů vlastností by mělo být změněno tak, aby odpovídalo pouzdrům Pascal názvů vlastností objektu CLR.</span><span class="sxs-lookup"><span data-stu-id="1953d-245">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="1953d-246">Vlastnost `bookName` by měla `Name`být vrácena jako .</span><span class="sxs-lookup"><span data-stu-id="1953d-246">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="1953d-247">Chcete-li splnit předchozí požadavky, proveďte následující změny:</span><span class="sxs-lookup"><span data-stu-id="1953d-247">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="1953d-248">JSON.NET byla odebrána ze sdíleného rámce ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="1953d-248">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="1953d-249">Přidejte odkaz na balíček [microsoft.aspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span><span class="sxs-lookup"><span data-stu-id="1953d-249">Add a package reference to [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="1953d-250">V `Startup.ConfigureServices`, řetěz následující zvýrazněný `AddControllers` kód na volání metody:</span><span class="sxs-lookup"><span data-stu-id="1953d-250">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddControllers` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="1953d-251">S předchozí změnou názvy vlastností v serializované odpovědi JSON webového rozhraní API odpovídají jejich odpovídajícím názvům vlastností v typu objektu CLR.</span><span class="sxs-lookup"><span data-stu-id="1953d-251">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="1953d-252">Například `Book` `Author` vlastnost třídy serializuje jako `Author`.</span><span class="sxs-lookup"><span data-stu-id="1953d-252">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="1953d-253">V *části Models/Book.cs*opatří `BookName` vlastnost poznámkami s následujícím [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) atributem:</span><span class="sxs-lookup"><span data-stu-id="1953d-253">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="1953d-254">Hodnota `[JsonProperty]` atributu `Name` představuje název vlastnosti v serializované odpovědi JSON webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="1953d-254">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="1953d-255">Přidejte následující kód do horní části *Models/Book.cs* vyřešit odkaz na `[JsonProperty]` atribut:</span><span class="sxs-lookup"><span data-stu-id="1953d-255">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="1953d-256">Opakujte kroky definované v části [Otestovat webové rozhraní API.](#test-the-web-api)</span><span class="sxs-lookup"><span data-stu-id="1953d-256">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="1953d-257">Všimněte si rozdílu v názvech vlastností JSON.</span><span class="sxs-lookup"><span data-stu-id="1953d-257">Notice the difference in JSON property names.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1953d-258">Tento kurz vytvoří webové rozhraní API, které provádí operace Vytvoření, Čtení, Aktualizace a Odstranění (CRUD) v databázi [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL.</span><span class="sxs-lookup"><span data-stu-id="1953d-258">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="1953d-259">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="1953d-259">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1953d-260">Konfigurace mongoDB</span><span class="sxs-lookup"><span data-stu-id="1953d-260">Configure MongoDB</span></span>
> * <span data-ttu-id="1953d-261">Vytvoření databáze MongoDB</span><span class="sxs-lookup"><span data-stu-id="1953d-261">Create a MongoDB database</span></span>
> * <span data-ttu-id="1953d-262">Definování kolekce a schématu MongoDB</span><span class="sxs-lookup"><span data-stu-id="1953d-262">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="1953d-263">Provádění operací MongoDB CRUD z webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="1953d-263">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="1953d-264">Přizpůsobení serializace JSON</span><span class="sxs-lookup"><span data-stu-id="1953d-264">Customize JSON serialization</span></span>

<span data-ttu-id="1953d-265">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="1953d-265">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1953d-266">Požadavky</span><span class="sxs-lookup"><span data-stu-id="1953d-266">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1953d-267">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1953d-267">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="1953d-268">Sada .NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="1953d-268">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="1953d-269">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s **ASP.NET a zatížením vývoje webu**</span><span class="sxs-lookup"><span data-stu-id="1953d-269">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="1953d-270">MongoDB</span><span class="sxs-lookup"><span data-stu-id="1953d-270">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="1953d-271">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1953d-271">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="1953d-272">Sada .NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="1953d-272">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="1953d-273">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1953d-273">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="1953d-274">C# pro Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1953d-274">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="1953d-275">MongoDB</span><span class="sxs-lookup"><span data-stu-id="1953d-275">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1953d-276">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="1953d-276">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="1953d-277">Sada .NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="1953d-277">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="1953d-278">Visual Studio pro Mac verze 7.7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="1953d-278">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="1953d-279">MongoDB</span><span class="sxs-lookup"><span data-stu-id="1953d-279">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="1953d-280">Konfigurace mongoDB</span><span class="sxs-lookup"><span data-stu-id="1953d-280">Configure MongoDB</span></span>

<span data-ttu-id="1953d-281">Pokud používáte systém Windows, MongoDB je nainstalován na *C:\\Program Files\\MongoDB* ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="1953d-281">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="1953d-282">Přidat *C:\\\\Program Files\\MongoDB\\\<Server version_number>\\přihrádku* `Path` do proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="1953d-282">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="1953d-283">Tato změna umožňuje mongoDB přístup z libovolného místa na vývojovém počítači.</span><span class="sxs-lookup"><span data-stu-id="1953d-283">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="1953d-284">Pomocí prostředí mongo v následujících krocích vytvořte databázi, vytvořte kolekce a uložte dokumenty.</span><span class="sxs-lookup"><span data-stu-id="1953d-284">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="1953d-285">Další informace o příkazech mongo Shell naleznete [v tématu Práce s mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="1953d-285">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="1953d-286">Zvolte adresář ve vývojovém počítači pro ukládání dat.</span><span class="sxs-lookup"><span data-stu-id="1953d-286">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="1953d-287">Například *C:\\BooksData v* systému Windows.</span><span class="sxs-lookup"><span data-stu-id="1953d-287">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="1953d-288">Vytvořte adresář, pokud neexistuje.</span><span class="sxs-lookup"><span data-stu-id="1953d-288">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="1953d-289">Mongo Shell nevytváří nové adresáře.</span><span class="sxs-lookup"><span data-stu-id="1953d-289">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="1953d-290">Otevřete příkazové prostředí.</span><span class="sxs-lookup"><span data-stu-id="1953d-290">Open a command shell.</span></span> <span data-ttu-id="1953d-291">Spusťte následující příkaz pro připojení k MongoDB na výchozím portu 27017.</span><span class="sxs-lookup"><span data-stu-id="1953d-291">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="1953d-292">Nezapomeňte nahradit `<data_directory_path>` adresář, který jste zvolili v předchozím kroku.</span><span class="sxs-lookup"><span data-stu-id="1953d-292">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="1953d-293">Otevřete jinou instanci příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="1953d-293">Open another command shell instance.</span></span> <span data-ttu-id="1953d-294">Připojte se k výchozí testovací databázi spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="1953d-294">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="1953d-295">V příkazovém prostředí spusťte následující:</span><span class="sxs-lookup"><span data-stu-id="1953d-295">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="1953d-296">Pokud ještě neexistuje, je vytvořena databáze s názvem *BookstoreDb.*</span><span class="sxs-lookup"><span data-stu-id="1953d-296">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="1953d-297">Pokud databáze existuje, její připojení je otevřen pro transakce.</span><span class="sxs-lookup"><span data-stu-id="1953d-297">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="1953d-298">Vytvořte `Books` kolekci pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="1953d-298">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="1953d-299">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="1953d-299">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="1953d-300">Definujte schéma kolekce `Books` a vložte dva dokumenty pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="1953d-300">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="1953d-301">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="1953d-301">The following result is displayed:</span></span>

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
   > <span data-ttu-id="1953d-302">ID je uvedeno v tomto článku nebude odpovídat ID při spuštění této ukázky.</span><span class="sxs-lookup"><span data-stu-id="1953d-302">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="1953d-303">Dokumenty v databázi můžete zobrazit pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="1953d-303">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="1953d-304">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="1953d-304">The following result is displayed:</span></span>

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

   <span data-ttu-id="1953d-305">Schéma přidá automaticky vygenerované `_id` vlastnosti typu `ObjectId` pro každý dokument.</span><span class="sxs-lookup"><span data-stu-id="1953d-305">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="1953d-306">Databáze je připravena.</span><span class="sxs-lookup"><span data-stu-id="1953d-306">The database is ready.</span></span> <span data-ttu-id="1953d-307">Můžete začít vytvářet ASP.NET základní webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="1953d-307">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="1953d-308">Vytvoření projektu webového rozhraní API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1953d-308">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1953d-309">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1953d-309">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="1953d-310">Přejděte do **souboru** > **nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="1953d-310">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="1953d-311">Vyberte typ projektu **základní webové aplikace ASP.NET** a vyberte **další**.</span><span class="sxs-lookup"><span data-stu-id="1953d-311">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="1953d-312">Pojmenujte projekt *BooksApi*a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="1953d-312">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="1953d-313">Vyberte cílovou architekturu **.NET Core** a **ASP.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="1953d-313">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="1953d-314">Vyberte šablonu projektu **rozhraní API** a vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="1953d-314">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="1953d-315">Navštivte [Galerii NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) a zjistěte nejnovější stabilní verzi ovladače .NET pro MongoDB.</span><span class="sxs-lookup"><span data-stu-id="1953d-315">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="1953d-316">V okně **Konzola Správce balíčků** přejděte do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="1953d-316">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="1953d-317">Spuštěním následujícího příkazu nainstalujte ovladač .NET pro MongoDB:</span><span class="sxs-lookup"><span data-stu-id="1953d-317">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="1953d-318">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1953d-318">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="1953d-319">Spusťte v příkazovém prostředí následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="1953d-319">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="1953d-320">Nový ASP.NET core web API projekt cílení .NET Core je generována a otevřena v kódu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1953d-320">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="1953d-321">Poté, co ikona plamene OmniSharp na stavovém řádku zezelenaje, dialogové okno požádá **požadované datové zdroje k sestavení a ladění v rozhraní BooksApi. Přidat je?**.</span><span class="sxs-lookup"><span data-stu-id="1953d-321">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="1953d-322">Vyberte **ano**.</span><span class="sxs-lookup"><span data-stu-id="1953d-322">Select **Yes**.</span></span>
1. <span data-ttu-id="1953d-323">Navštivte [Galerii NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) a zjistěte nejnovější stabilní verzi ovladače .NET pro MongoDB.</span><span class="sxs-lookup"><span data-stu-id="1953d-323">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="1953d-324">Otevřete **integrovaný terminál** a přejděte do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="1953d-324">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="1953d-325">Spuštěním následujícího příkazu nainstalujte ovladač .NET pro MongoDB:</span><span class="sxs-lookup"><span data-stu-id="1953d-325">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1953d-326">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="1953d-326">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="1953d-327">Přejděte na soubor > **novéřešení** > **.NET Core** > **File** **App**.</span><span class="sxs-lookup"><span data-stu-id="1953d-327">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="1953d-328">Vyberte šablonu projektu **core webového rozhraní API** C# ASP.NET a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="1953d-328">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="1953d-329">V rozevíracím seznamu **Cílová architektura** vyberte **.NET Core 2.2** a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="1953d-329">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="1953d-330">Zadejte *BooksApi* pro **název projektu**a vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="1953d-330">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="1953d-331">Na panelu **Řešení** klepněte pravým tlačítkem myši na uzel **Závislosti** projektu a vyberte přidat **balíčky**.</span><span class="sxs-lookup"><span data-stu-id="1953d-331">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="1953d-332">Do vyhledávacího pole zadejte *MongoDB.Driver,* vyberte balíček *MongoDB.Driver* a vyberte **Přidat balíček**.</span><span class="sxs-lookup"><span data-stu-id="1953d-332">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="1953d-333">V dialogovém okně **Přijetí licence** vyberte tlačítko **Přijmout.**</span><span class="sxs-lookup"><span data-stu-id="1953d-333">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="1953d-334">Přidání modelu entity</span><span class="sxs-lookup"><span data-stu-id="1953d-334">Add an entity model</span></span>

1. <span data-ttu-id="1953d-335">Přidejte adresář *Models* do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="1953d-335">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="1953d-336">Přidejte `Book` třídu do adresáře *Models* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="1953d-336">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

   <span data-ttu-id="1953d-337">V předchozí třídě `Id` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="1953d-337">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="1953d-338">Je vyžadováno pro mapování objektu CLR (Common Language Runtime) do kolekce MongoDB.</span><span class="sxs-lookup"><span data-stu-id="1953d-338">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="1953d-339">Je anotován [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) s určit tuto vlastnost jako primární klíč dokumentu.</span><span class="sxs-lookup"><span data-stu-id="1953d-339">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="1953d-340">Je anotován [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) s povolit předávání `string` parametru jako typ namísto [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) struktury.</span><span class="sxs-lookup"><span data-stu-id="1953d-340">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="1953d-341">Mongo zpracovává převod `string` z `ObjectId`do .</span><span class="sxs-lookup"><span data-stu-id="1953d-341">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="1953d-342">Vlastnost `BookName` je anotována [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) s atributem.</span><span class="sxs-lookup"><span data-stu-id="1953d-342">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="1953d-343">Hodnota atributu `Name` představuje název vlastnosti v kolekci MongoDB.</span><span class="sxs-lookup"><span data-stu-id="1953d-343">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="1953d-344">Přidání konfiguračního modelu</span><span class="sxs-lookup"><span data-stu-id="1953d-344">Add a configuration model</span></span>

1. <span data-ttu-id="1953d-345">Přidejte do *souboru appsettings.json*následující hodnoty konfigurace databáze :</span><span class="sxs-lookup"><span data-stu-id="1953d-345">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="1953d-346">Přidejte *soubor BookstoreDatabaseSettings.cs* do adresáře *Models* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="1953d-346">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="1953d-347">Předchozí `BookstoreDatabaseSettings` třída se používá k uložení hodnot `BookstoreDatabaseSettings` vlastností souboru *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="1953d-347">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="1953d-348">Názvy vlastností JSON a C# jsou pojmenovány identicky pro usnadnění procesu mapování.</span><span class="sxs-lookup"><span data-stu-id="1953d-348">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="1953d-349">Do položky přidejte `Startup.ConfigureServices`následující zvýrazněný kód :</span><span class="sxs-lookup"><span data-stu-id="1953d-349">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="1953d-350">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="1953d-350">In the preceding code:</span></span>

   * <span data-ttu-id="1953d-351">Instance konfigurace, na kterou se váže `BookstoreDatabaseSettings` oddíl souboru *appsettings.json,* je registrována v kontejneru DI vkládání závislostí (DI).</span><span class="sxs-lookup"><span data-stu-id="1953d-351">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="1953d-352">`BookstoreDatabaseSettings` Například `ConnectionString` vlastnost objektu je naplněna `BookstoreDatabaseSettings:ConnectionString` vlastností v *souboru appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="1953d-352">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="1953d-353">Rozhraní `IBookstoreDatabaseSettings` je registrováno v DI s [životností](xref:fundamentals/dependency-injection#service-lifetimes)singleton .</span><span class="sxs-lookup"><span data-stu-id="1953d-353">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="1953d-354">Při vložení instance rozhraní překládá `BookstoreDatabaseSettings` na objekt.</span><span class="sxs-lookup"><span data-stu-id="1953d-354">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="1953d-355">Přidejte následující kód na *Startup.cs* začátek `BookstoreDatabaseSettings` Startup.cs `IBookstoreDatabaseSettings` chcete vyřešit a odkazy:</span><span class="sxs-lookup"><span data-stu-id="1953d-355">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="1953d-356">Přidání provozní služby CRUD</span><span class="sxs-lookup"><span data-stu-id="1953d-356">Add a CRUD operations service</span></span>

1. <span data-ttu-id="1953d-357">Přidejte adresář *služby* do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="1953d-357">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="1953d-358">Přidejte `BookService` třídu do *adresáře Služby* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="1953d-358">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="1953d-359">V předchozím kódu `IBookstoreDatabaseSettings` instance je načten z DI prostřednictvím vkládání konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="1953d-359">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="1953d-360">Tato technika poskytuje přístup k hodnotám konfigurace *appsettings.json,* které byly přidány v části [Přidat model konfigurace.](#add-a-configuration-model)</span><span class="sxs-lookup"><span data-stu-id="1953d-360">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="1953d-361">Do položky přidejte `Startup.ConfigureServices`následující zvýrazněný kód :</span><span class="sxs-lookup"><span data-stu-id="1953d-361">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="1953d-362">V předchozím kódu je `BookService` třída registrována u DI pro podporu vkládání konstruktoru ve spotřebních třídách.</span><span class="sxs-lookup"><span data-stu-id="1953d-362">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="1953d-363">Životnost služby singleton je `BookService` nejvhodnější, protože `MongoClient`má přímou závislost na .</span><span class="sxs-lookup"><span data-stu-id="1953d-363">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="1953d-364">Podle oficiálních pokynů pro opakované `MongoClient` použití [klienta Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)by měly být registrovány v DI s životností singleton.</span><span class="sxs-lookup"><span data-stu-id="1953d-364">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="1953d-365">Chcete-li `BookService` odkaz vyřešit, přidejte na začátek *Startup.cs* následující kód:</span><span class="sxs-lookup"><span data-stu-id="1953d-365">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="1953d-366">Třída `BookService` používá následující `MongoDB.Driver` členy k provádění operací CRUD proti databázi:</span><span class="sxs-lookup"><span data-stu-id="1953d-366">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="1953d-367">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Přečte instanci serveru pro provádění databázových operací.</span><span class="sxs-lookup"><span data-stu-id="1953d-367">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="1953d-368">Konstruktor této třídy je k dispozici připojovací řetězec MongoDB:</span><span class="sxs-lookup"><span data-stu-id="1953d-368">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="1953d-369">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Představuje databázi Mongo pro provádění operací.</span><span class="sxs-lookup"><span data-stu-id="1953d-369">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="1953d-370">Tento kurz používá obecnou metodu [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) v rozhraní k získání přístupu k datům v konkrétní kolekci.</span><span class="sxs-lookup"><span data-stu-id="1953d-370">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="1953d-371">Proveďte operace CRUD proti kolekci po volání této metody.</span><span class="sxs-lookup"><span data-stu-id="1953d-371">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="1953d-372">Ve `GetCollection<TDocument>(collection)` volání metody:</span><span class="sxs-lookup"><span data-stu-id="1953d-372">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="1953d-373">`collection`představuje název kolekce.</span><span class="sxs-lookup"><span data-stu-id="1953d-373">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="1953d-374">`TDocument`představuje typ objektu CLR uložené v kolekci.</span><span class="sxs-lookup"><span data-stu-id="1953d-374">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="1953d-375">`GetCollection<TDocument>(collection)`vrátí [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) objekt představující kolekci.</span><span class="sxs-lookup"><span data-stu-id="1953d-375">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="1953d-376">V tomto kurzu jsou v kolekci vyvolány následující metody:</span><span class="sxs-lookup"><span data-stu-id="1953d-376">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="1953d-377">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Odstraní jeden dokument odpovídající zadaným kritériím hledání.</span><span class="sxs-lookup"><span data-stu-id="1953d-377">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="1953d-378">[Najít\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Vrátí všechny dokumenty v kolekci odpovídající zadaným kritériím hledání.</span><span class="sxs-lookup"><span data-stu-id="1953d-378">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="1953d-379">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Vloží zadaný objekt jako nový dokument v kolekci.</span><span class="sxs-lookup"><span data-stu-id="1953d-379">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="1953d-380">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Nahradí jeden dokument odpovídající zadaným kritériím hledání s poskytnutým objektem.</span><span class="sxs-lookup"><span data-stu-id="1953d-380">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="1953d-381">Přidání kontroleru</span><span class="sxs-lookup"><span data-stu-id="1953d-381">Add a controller</span></span>

<span data-ttu-id="1953d-382">Přidejte `BooksController` třídu do *adresáře Řadiče* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="1953d-382">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="1953d-383">Předchozí řadič webového rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="1953d-383">The preceding web API controller:</span></span>

* <span data-ttu-id="1953d-384">Používá `BookService` třídu k provádění operací CRUD.</span><span class="sxs-lookup"><span data-stu-id="1953d-384">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="1953d-385">Obsahuje metody akce pro podporu požadavků HTTP GET, POST, PUT a DELETE.</span><span class="sxs-lookup"><span data-stu-id="1953d-385">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="1953d-386">Volání <xref:System.Web.Http.ApiController.CreatedAtRoute*> v `Create` metodě akce vrátit odpověď [HTTP 201.](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)</span><span class="sxs-lookup"><span data-stu-id="1953d-386">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="1953d-387">Stavový kód 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="1953d-387">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="1953d-388">`CreatedAtRoute`také přidá `Location` záhlaví odpovědi.</span><span class="sxs-lookup"><span data-stu-id="1953d-388">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="1953d-389">Záhlaví `Location` určuje identifikátor URI nově vytvořené knihy.</span><span class="sxs-lookup"><span data-stu-id="1953d-389">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="1953d-390">Testování webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="1953d-390">Test the web API</span></span>

1. <span data-ttu-id="1953d-391">Sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1953d-391">Build and run the app.</span></span>

1. <span data-ttu-id="1953d-392">Přejděte `http://localhost:<port>/api/books` na otestovat ovladač `Get` bez parametry akce metody.</span><span class="sxs-lookup"><span data-stu-id="1953d-392">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="1953d-393">Zobrazí se následující odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="1953d-393">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="1953d-394">Přejděte `http://localhost:<port>/api/books/{id here}` na otestovat přetížené `Get` metody akce řadiče.</span><span class="sxs-lookup"><span data-stu-id="1953d-394">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="1953d-395">Zobrazí se následující odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="1953d-395">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="1953d-396">Konfigurace možností serializace JSON</span><span class="sxs-lookup"><span data-stu-id="1953d-396">Configure JSON serialization options</span></span>

<span data-ttu-id="1953d-397">Existují dva podrobnosti změnit o odpovědi JSON vrácené v [části Test webové rozhraní API:](#test-the-web-api)</span><span class="sxs-lookup"><span data-stu-id="1953d-397">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="1953d-398">Výchozí camel ové pouzdro názvů vlastností by mělo být změněno tak, aby odpovídalo pouzdrům Pascal názvů vlastností objektu CLR.</span><span class="sxs-lookup"><span data-stu-id="1953d-398">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="1953d-399">Vlastnost `bookName` by měla `Name`být vrácena jako .</span><span class="sxs-lookup"><span data-stu-id="1953d-399">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="1953d-400">Chcete-li splnit předchozí požadavky, proveďte následující změny:</span><span class="sxs-lookup"><span data-stu-id="1953d-400">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="1953d-401">V `Startup.ConfigureServices`, řetěz následující zvýrazněný `AddMvc` kód na volání metody:</span><span class="sxs-lookup"><span data-stu-id="1953d-401">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="1953d-402">S předchozí změnou názvy vlastností v serializované odpovědi JSON webového rozhraní API odpovídají jejich odpovídajícím názvům vlastností v typu objektu CLR.</span><span class="sxs-lookup"><span data-stu-id="1953d-402">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="1953d-403">Například `Book` `Author` vlastnost třídy serializuje jako `Author`.</span><span class="sxs-lookup"><span data-stu-id="1953d-403">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="1953d-404">V *části Models/Book.cs*opatří `BookName` vlastnost poznámkami s následujícím [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) atributem:</span><span class="sxs-lookup"><span data-stu-id="1953d-404">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="1953d-405">Hodnota `[JsonProperty]` atributu `Name` představuje název vlastnosti v serializované odpovědi JSON webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="1953d-405">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="1953d-406">Přidejte následující kód do horní části *Models/Book.cs* vyřešit odkaz na `[JsonProperty]` atribut:</span><span class="sxs-lookup"><span data-stu-id="1953d-406">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="1953d-407">Opakujte kroky definované v části [Otestovat webové rozhraní API.](#test-the-web-api)</span><span class="sxs-lookup"><span data-stu-id="1953d-407">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="1953d-408">Všimněte si rozdílu v názvech vlastností JSON.</span><span class="sxs-lookup"><span data-stu-id="1953d-408">Notice the difference in JSON property names.</span></span>

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="1953d-409">Přidání podpory ověřování do webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="1953d-409">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a><span data-ttu-id="1953d-410">Další kroky</span><span class="sxs-lookup"><span data-stu-id="1953d-410">Next steps</span></span>

<span data-ttu-id="1953d-411">Další informace o vytváření ASP.NET základní webová api naleznete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="1953d-411">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="1953d-412">Verze tohoto článku na YouTube</span><span class="sxs-lookup"><span data-stu-id="1953d-412">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>

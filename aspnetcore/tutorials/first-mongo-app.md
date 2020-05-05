---
title: Vytvoření webového rozhraní API pomocí ASP.NET Core a MongoDB
author: prkhandelwal
description: V tomto kurzu se dozvíte, jak vytvořit webové rozhraní API ASP.NET Core pomocí databáze NoSQL v databázi MongoDB.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mongo-app
ms.openlocfilehash: 4d1c2d915c646dd1c8fcadd25bcd420a0a749dc9
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774766"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="38ed2-103">Vytvoření webového rozhraní API pomocí ASP.NET Core a MongoDB</span><span class="sxs-lookup"><span data-stu-id="38ed2-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="38ed2-104">Od [Pratik Khandelwal](https://twitter.com/K2Prk) a [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="38ed2-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="38ed2-105">V tomto kurzu se vytvoří webové rozhraní API, které provádí operace vytvoření, čtení, aktualizace a odstranění (CRUD) v databázi [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL.</span><span class="sxs-lookup"><span data-stu-id="38ed2-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="38ed2-106">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="38ed2-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="38ed2-107">Konfigurace MongoDB</span><span class="sxs-lookup"><span data-stu-id="38ed2-107">Configure MongoDB</span></span>
> * <span data-ttu-id="38ed2-108">Vytvoření databáze MongoDB</span><span class="sxs-lookup"><span data-stu-id="38ed2-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="38ed2-109">Definování kolekce a schématu MongoDB</span><span class="sxs-lookup"><span data-stu-id="38ed2-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="38ed2-110">Provádění operací CRUD MongoDB z webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="38ed2-110">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="38ed2-111">Přizpůsobení serializace JSON</span><span class="sxs-lookup"><span data-stu-id="38ed2-111">Customize JSON serialization</span></span>

<span data-ttu-id="38ed2-112">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="38ed2-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="38ed2-113">Požadavky</span><span class="sxs-lookup"><span data-stu-id="38ed2-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="38ed2-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="38ed2-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="38ed2-115">.NET Core SDK 3.0 nebo novější</span><span class="sxs-lookup"><span data-stu-id="38ed2-115">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="38ed2-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s úlohou **vývoje ASP.NET a webu**</span><span class="sxs-lookup"><span data-stu-id="38ed2-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="38ed2-117">MongoDB</span><span class="sxs-lookup"><span data-stu-id="38ed2-117">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="38ed2-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="38ed2-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="38ed2-119">.NET Core SDK 3.0 nebo novější</span><span class="sxs-lookup"><span data-stu-id="38ed2-119">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="38ed2-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="38ed2-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="38ed2-121">C# pro Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="38ed2-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="38ed2-122">MongoDB</span><span class="sxs-lookup"><span data-stu-id="38ed2-122">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="38ed2-123">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="38ed2-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="38ed2-124">.NET Core SDK 3.0 nebo novější</span><span class="sxs-lookup"><span data-stu-id="38ed2-124">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="38ed2-125">Visual Studio pro Mac verze 7,7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="38ed2-125">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="38ed2-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="38ed2-126">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="38ed2-127">Konfigurace MongoDB</span><span class="sxs-lookup"><span data-stu-id="38ed2-127">Configure MongoDB</span></span>

<span data-ttu-id="38ed2-128">Pokud používáte Windows, MongoDB se nainstaluje ve výchozím nastavení v *\\C\\: Program Files MongoDB* .</span><span class="sxs-lookup"><span data-stu-id="38ed2-128">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="38ed2-129">Přidejte *C:\\Program Files\\MongoDB\\server\\\<version_number>\\bin* do proměnné `Path` prostředí.</span><span class="sxs-lookup"><span data-stu-id="38ed2-129">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="38ed2-130">Tato změna umožňuje přístup k MongoDB odkudkoli na svém vývojovém počítači.</span><span class="sxs-lookup"><span data-stu-id="38ed2-130">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="38ed2-131">Pomocí prostředí Mongo v následujících krocích můžete vytvořit databázi, vytvořit kolekce a uložit dokumenty.</span><span class="sxs-lookup"><span data-stu-id="38ed2-131">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="38ed2-132">Další informace o příkazech prostředí Mongo najdete v tématu [práce s prostředím Mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="38ed2-132">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="38ed2-133">Pro uložení dat vyberte adresář na vývojovém počítači.</span><span class="sxs-lookup"><span data-stu-id="38ed2-133">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="38ed2-134">Například *C:\\BooksData* ve Windows.</span><span class="sxs-lookup"><span data-stu-id="38ed2-134">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="38ed2-135">Pokud adresář neexistuje, vytvořte ho.</span><span class="sxs-lookup"><span data-stu-id="38ed2-135">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="38ed2-136">Prostředí Mongo nevytváří nové adresáře.</span><span class="sxs-lookup"><span data-stu-id="38ed2-136">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="38ed2-137">Otevřete příkazové prostředí.</span><span class="sxs-lookup"><span data-stu-id="38ed2-137">Open a command shell.</span></span> <span data-ttu-id="38ed2-138">Spusťte následující příkaz pro připojení k MongoDB na výchozím portu 27017.</span><span class="sxs-lookup"><span data-stu-id="38ed2-138">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="38ed2-139">Nezapomeňte nahradit `<data_directory_path>` adresář, který jste zvolili v předchozím kroku.</span><span class="sxs-lookup"><span data-stu-id="38ed2-139">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="38ed2-140">Otevřete jinou instanci příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="38ed2-140">Open another command shell instance.</span></span> <span data-ttu-id="38ed2-141">Připojte se k výchozí testovací databázi spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="38ed2-141">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="38ed2-142">V příkazovém prostředí spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="38ed2-142">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="38ed2-143">Pokud ještě neexistuje, vytvoří se databáze s názvem *BookstoreDb* .</span><span class="sxs-lookup"><span data-stu-id="38ed2-143">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="38ed2-144">Pokud databáze existuje, připojení je otevřeno pro transakce.</span><span class="sxs-lookup"><span data-stu-id="38ed2-144">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="38ed2-145">Vytvořte `Books` kolekci pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="38ed2-145">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="38ed2-146">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="38ed2-146">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="38ed2-147">Definujte schéma pro `Books` kolekci a vložte dva dokumenty pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="38ed2-147">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="38ed2-148">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="38ed2-148">The following result is displayed:</span></span>

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
   > <span data-ttu-id="38ed2-149">ID zobrazené v tomto článku se při spuštění této ukázky neshoduje s ID.</span><span class="sxs-lookup"><span data-stu-id="38ed2-149">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="38ed2-150">Zobrazte dokumenty v databázi pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="38ed2-150">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="38ed2-151">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="38ed2-151">The following result is displayed:</span></span>

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

   <span data-ttu-id="38ed2-152">Schéma přidá do každého dokumentu automaticky generovanou `_id` vlastnost typu `ObjectId` .</span><span class="sxs-lookup"><span data-stu-id="38ed2-152">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="38ed2-153">Databáze je připravena.</span><span class="sxs-lookup"><span data-stu-id="38ed2-153">The database is ready.</span></span> <span data-ttu-id="38ed2-154">Můžete začít vytvářet ASP.NET Core webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="38ed2-154">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="38ed2-155">Vytvoření projektu webového rozhraní API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="38ed2-155">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="38ed2-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="38ed2-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="38ed2-157">Přejít na **soubor** > **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="38ed2-157">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="38ed2-158">Vyberte ASP.NET Core typ projektu **webové aplikace** a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="38ed2-158">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="38ed2-159">Pojmenujte projekt *BooksApi*a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="38ed2-159">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="38ed2-160">Vyberte cílové rozhraní **.NET Core** a **ASP.NET Core 3,0**.</span><span class="sxs-lookup"><span data-stu-id="38ed2-160">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="38ed2-161">Vyberte šablonu projektu **rozhraní API** a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="38ed2-161">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="38ed2-162">Navštivte [galerii NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) a určete nejnovější stabilní verzi ovladače .NET pro MongoDB.</span><span class="sxs-lookup"><span data-stu-id="38ed2-162">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="38ed2-163">V okně **konzoly Správce balíčků** přejděte do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="38ed2-163">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="38ed2-164">Spuštěním následujícího příkazu nainstalujte ovladač .NET pro MongoDB:</span><span class="sxs-lookup"><span data-stu-id="38ed2-164">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="38ed2-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="38ed2-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="38ed2-166">V příkazovém prostředí spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="38ed2-166">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="38ed2-167">Vygeneruje se nový projekt ASP.NET Core webového rozhraní API cílící na .NET Core a otevře se v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="38ed2-167">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="38ed2-168">Až se ikona plamene u OmniSharp stavového řádku změní na zelenou, dialogové okno požádá **o požadované prostředky k sestavení a ladění chybí v ' BooksApi '. Přidat je?**.</span><span class="sxs-lookup"><span data-stu-id="38ed2-168">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="38ed2-169">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="38ed2-169">Select **Yes**.</span></span>
1. <span data-ttu-id="38ed2-170">Navštivte [galerii NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) a určete nejnovější stabilní verzi ovladače .NET pro MongoDB.</span><span class="sxs-lookup"><span data-stu-id="38ed2-170">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="38ed2-171">Otevřete **integrovaný terminál** a přejděte do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="38ed2-171">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="38ed2-172">Spuštěním následujícího příkazu nainstalujte ovladač .NET pro MongoDB:</span><span class="sxs-lookup"><span data-stu-id="38ed2-172">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="38ed2-173">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="38ed2-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="38ed2-174">Přejít na **soubor** > **nové řešení** > **.NET Core** > **aplikace**</span><span class="sxs-lookup"><span data-stu-id="38ed2-174">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="38ed2-175">Vyberte šablonu projektu **ASP.NET Core webového rozhraní API** jazyka C# a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="38ed2-175">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="38ed2-176">V rozevíracím seznamu **cílové rozhraní** vyberte **.NET Core 3,0** a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="38ed2-176">Select **.NET Core 3.0** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="38ed2-177">Jako **název projektu**zadejte *BooksApi* a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="38ed2-177">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="38ed2-178">Na panelu **řešení** klikněte pravým tlačítkem myši na uzel **závislosti** projektu a vyberte možnost **Přidat balíčky**.</span><span class="sxs-lookup"><span data-stu-id="38ed2-178">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="38ed2-179">Do vyhledávacího pole zadejte *MongoDB. Driver* , vyberte balíček *MongoDB. Driver* a vyberte **Přidat balíček**.</span><span class="sxs-lookup"><span data-stu-id="38ed2-179">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="38ed2-180">V dialogu pro **přijetí licence** vyberte tlačítko **přijmout** .</span><span class="sxs-lookup"><span data-stu-id="38ed2-180">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="38ed2-181">Přidání modelu entity</span><span class="sxs-lookup"><span data-stu-id="38ed2-181">Add an entity model</span></span>

1. <span data-ttu-id="38ed2-182">Přidejte adresář *modelů* do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="38ed2-182">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="38ed2-183">Přidejte `Book` třídu do adresáře *modelů* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="38ed2-183">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

   <span data-ttu-id="38ed2-184">V předchozí třídě `Id` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="38ed2-184">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="38ed2-185">Je vyžadován pro mapování objektu modulu CLR (Common Language Runtime) na kolekci MongoDB.</span><span class="sxs-lookup"><span data-stu-id="38ed2-185">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="38ed2-186">Je s [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) poznámkami k označení této vlastnosti jako primárního klíče dokumentu.</span><span class="sxs-lookup"><span data-stu-id="38ed2-186">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="38ed2-187">Je opatřen s [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) poznámkami, aby bylo možné předat parametr `string` jako typ namísto struktury [objectID](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) .</span><span class="sxs-lookup"><span data-stu-id="38ed2-187">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="38ed2-188">Mongo zpracovává převod z `string` na. `ObjectId`</span><span class="sxs-lookup"><span data-stu-id="38ed2-188">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="38ed2-189">`BookName` Vlastnost je označena [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) atributem.</span><span class="sxs-lookup"><span data-stu-id="38ed2-189">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="38ed2-190">Hodnota atributu `Name` představuje název vlastnosti v kolekci MongoDB.</span><span class="sxs-lookup"><span data-stu-id="38ed2-190">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="38ed2-191">Přidat konfigurační model</span><span class="sxs-lookup"><span data-stu-id="38ed2-191">Add a configuration model</span></span>

1. <span data-ttu-id="38ed2-192">Do souboru *appSettings. JSON*přidejte následující hodnoty konfigurace databáze:</span><span class="sxs-lookup"><span data-stu-id="38ed2-192">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="38ed2-193">Přidejte soubor *BookstoreDatabaseSettings.cs* do adresáře *Models* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="38ed2-193">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="38ed2-194">Předchozí `BookstoreDatabaseSettings` třída se používá k uložení hodnot `BookstoreDatabaseSettings` vlastností souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="38ed2-194">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="38ed2-195">Názvy vlastností JSON a C# jsou pojmenovány stejně, aby bylo možné zjednodušit proces mapování.</span><span class="sxs-lookup"><span data-stu-id="38ed2-195">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="38ed2-196">Přidejte následující zvýrazněný kód do `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="38ed2-196">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   <span data-ttu-id="38ed2-197">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="38ed2-197">In the preceding code:</span></span>

   * <span data-ttu-id="38ed2-198">Instance konfigurace, na kterou se váže `BookstoreDatabaseSettings` sekce souboru *appSettings. JSON* , se registruje v kontejneru injektáže (di).</span><span class="sxs-lookup"><span data-stu-id="38ed2-198">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="38ed2-199">Například `BookstoreDatabaseSettings` `ConnectionString` vlastnost objektu je naplněna `BookstoreDatabaseSettings:ConnectionString` vlastností v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="38ed2-199">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="38ed2-200">`IBookstoreDatabaseSettings` Rozhraní je zaregistrované v di s [životností služby](xref:fundamentals/dependency-injection#service-lifetimes)typu singleton.</span><span class="sxs-lookup"><span data-stu-id="38ed2-200">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="38ed2-201">Při vložení se instance rozhraní překládá na `BookstoreDatabaseSettings` objekt.</span><span class="sxs-lookup"><span data-stu-id="38ed2-201">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="38ed2-202">Pro vyřešení odkazů `BookstoreDatabaseSettings` a `IBookstoreDatabaseSettings` přidejte následující kód na začátek *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="38ed2-202">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="38ed2-203">Přidání služby operace CRUD</span><span class="sxs-lookup"><span data-stu-id="38ed2-203">Add a CRUD operations service</span></span>

1. <span data-ttu-id="38ed2-204">Přidejte adresář *služeb* do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="38ed2-204">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="38ed2-205">Do adresáře `BookService` *služby* přidejte třídu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="38ed2-205">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="38ed2-206">V předchozím kódu je `IBookstoreDatabaseSettings` instance načtena z di přes injektáže konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="38ed2-206">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="38ed2-207">Tento postup poskytuje přístup k hodnotám konfigurace *appSettings. JSON* , které byly přidány do oddílu [přidat konfigurační model](#add-a-configuration-model) .</span><span class="sxs-lookup"><span data-stu-id="38ed2-207">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="38ed2-208">Přidejte následující zvýrazněný kód do `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="38ed2-208">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="38ed2-209">V předchozím kódu je `BookService` třída registrována pomocí příkazu di k podpoře injektáže konstruktoru v rámci využívání tříd.</span><span class="sxs-lookup"><span data-stu-id="38ed2-209">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="38ed2-210">Doba životnosti služby singleton je nejvhodnější, `BookService` protože používá přímou závislost na `MongoClient`.</span><span class="sxs-lookup"><span data-stu-id="38ed2-210">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="38ed2-211">Podle oficiálních [zásad opětovného použití pro klienty Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use) `MongoClient` by se měla registrovat v di s životností služby typu singleton.</span><span class="sxs-lookup"><span data-stu-id="38ed2-211">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="38ed2-212">Přidejte následující kód na začátek *Startup.cs* k vyřešení `BookService` odkazu:</span><span class="sxs-lookup"><span data-stu-id="38ed2-212">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="38ed2-213">`BookService` Třída používá následující `MongoDB.Driver` členy k provádění operací CRUD proti databázi:</span><span class="sxs-lookup"><span data-stu-id="38ed2-213">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="38ed2-214">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; přečte instanci serveru pro provádění databázových operací.</span><span class="sxs-lookup"><span data-stu-id="38ed2-214">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="38ed2-215">K konstruktoru této třídy je poskytnutý připojovací řetězec MongoDB:</span><span class="sxs-lookup"><span data-stu-id="38ed2-215">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="38ed2-216">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; představuje databázi Mongo pro provádění operací.</span><span class="sxs-lookup"><span data-stu-id="38ed2-216">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="38ed2-217">V tomto kurzu se používá obecná metoda [GetCollection\<TDocument> (Collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) na rozhraní pro získání přístupu k datům v určité kolekci.</span><span class="sxs-lookup"><span data-stu-id="38ed2-217">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="38ed2-218">Provede operace CRUD proti kolekci po volání této metody.</span><span class="sxs-lookup"><span data-stu-id="38ed2-218">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="38ed2-219">Ve volání `GetCollection<TDocument>(collection)` metody:</span><span class="sxs-lookup"><span data-stu-id="38ed2-219">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="38ed2-220">`collection`představuje název kolekce.</span><span class="sxs-lookup"><span data-stu-id="38ed2-220">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="38ed2-221">`TDocument`představuje typ objektu CLR uložený v kolekci.</span><span class="sxs-lookup"><span data-stu-id="38ed2-221">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="38ed2-222">`GetCollection<TDocument>(collection)`Vrátí objekt [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) představující kolekci.</span><span class="sxs-lookup"><span data-stu-id="38ed2-222">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="38ed2-223">V tomto kurzu jsou v kolekci vyvolány následující metody:</span><span class="sxs-lookup"><span data-stu-id="38ed2-223">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="38ed2-224">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; odstraní jeden dokument, který odpovídá zadaným kritériím hledání.</span><span class="sxs-lookup"><span data-stu-id="38ed2-224">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="38ed2-225">[Hledání\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; vrátí všechny dokumenty v kolekci, které odpovídají zadaným kritériím hledání.</span><span class="sxs-lookup"><span data-stu-id="38ed2-225">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="38ed2-226">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Vloží zadaný objekt jako nový dokument v kolekci.</span><span class="sxs-lookup"><span data-stu-id="38ed2-226">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="38ed2-227">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; nahrazuje jediný dokument, který odpovídá zadaným kritériím hledání, s dodaným objektem.</span><span class="sxs-lookup"><span data-stu-id="38ed2-227">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="38ed2-228">Přidání kontroleru</span><span class="sxs-lookup"><span data-stu-id="38ed2-228">Add a controller</span></span>

<span data-ttu-id="38ed2-229">Přidejte `BooksController` třídu do adresáře *Controllers* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="38ed2-229">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="38ed2-230">Předchozí kontroler webového rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="38ed2-230">The preceding web API controller:</span></span>

* <span data-ttu-id="38ed2-231">Používá `BookService` třídu k provádění operací CRUD.</span><span class="sxs-lookup"><span data-stu-id="38ed2-231">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="38ed2-232">Obsahuje metody akcí, které podporují požadavky HTTP GET, POST, PUT a DELETE.</span><span class="sxs-lookup"><span data-stu-id="38ed2-232">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="38ed2-233">Volání <xref:System.Web.Http.ApiController.CreatedAtRoute*> v metodě `Create` action vrátí odpověď [http 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) .</span><span class="sxs-lookup"><span data-stu-id="38ed2-233">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="38ed2-234">Stavový kód 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="38ed2-234">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="38ed2-235">`CreatedAtRoute`Přidá také `Location` hlavičku do odpovědi.</span><span class="sxs-lookup"><span data-stu-id="38ed2-235">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="38ed2-236">`Location` Hlavička Určuje identifikátor URI nově vytvořené knihy.</span><span class="sxs-lookup"><span data-stu-id="38ed2-236">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="38ed2-237">Testování webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="38ed2-237">Test the web API</span></span>

1. <span data-ttu-id="38ed2-238">Sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="38ed2-238">Build and run the app.</span></span>

1. <span data-ttu-id="38ed2-239">Přejděte na `http://localhost:<port>/api/books` k otestování metody `Get` akce bez parametrů řadiče.</span><span class="sxs-lookup"><span data-stu-id="38ed2-239">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="38ed2-240">Zobrazí se následující odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="38ed2-240">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="38ed2-241">Přejděte na `http://localhost:<port>/api/books/{id here}` k otestování metody `Get` akce, která je přetížena řadičem.</span><span class="sxs-lookup"><span data-stu-id="38ed2-241">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="38ed2-242">Zobrazí se následující odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="38ed2-242">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="38ed2-243">Konfigurace možností serializace JSON</span><span class="sxs-lookup"><span data-stu-id="38ed2-243">Configure JSON serialization options</span></span>

<span data-ttu-id="38ed2-244">Existují dvě podrobnosti o tom, jak můžete změnit informace o odpovědích JSON vrácených v části [Test webového rozhraní API](#test-the-web-api) :</span><span class="sxs-lookup"><span data-stu-id="38ed2-244">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="38ed2-245">Názvy vlastností výchozí ve stylu CamelCase velká a malá písmena by se měly změnit tak, aby odpovídaly názvu vlastností objektu CLR na velká písmena Pascal.</span><span class="sxs-lookup"><span data-stu-id="38ed2-245">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="38ed2-246">`bookName` Vlastnost by měla být vrácena jako `Name`.</span><span class="sxs-lookup"><span data-stu-id="38ed2-246">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="38ed2-247">Chcete-li splnit předchozí požadavky, proveďte následující změny:</span><span class="sxs-lookup"><span data-stu-id="38ed2-247">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="38ed2-248">JSON.NET se odebral ze sdílené architektury ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="38ed2-248">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="38ed2-249">Přidejte odkaz na balíček [Microsoft. AspNetCore. Mvc. NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span><span class="sxs-lookup"><span data-stu-id="38ed2-249">Add a package reference to [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="38ed2-250">V `Startup.ConfigureServices`, řetězení následujícího zvýrazněného kódu k volání `AddControllers` metody:</span><span class="sxs-lookup"><span data-stu-id="38ed2-250">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddControllers` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="38ed2-251">V předchozí změně názvy vlastností v serializovaných odpovědích JSON webového rozhraní API odpovídají jejich odpovídajícím názvům vlastností v typu objektu CLR.</span><span class="sxs-lookup"><span data-stu-id="38ed2-251">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="38ed2-252">Například `Book` `Author` vlastnost třídy je serializována jako `Author`.</span><span class="sxs-lookup"><span data-stu-id="38ed2-252">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="38ed2-253">V části *Models/Book. cs*poznámku k `BookName` vlastnosti s následujícím [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) atributem:</span><span class="sxs-lookup"><span data-stu-id="38ed2-253">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="38ed2-254">Hodnota `[JsonProperty]` atributu `Name` představuje název vlastnosti v serializované odpovědi JSON webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="38ed2-254">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="38ed2-255">Přidejte následující kód na začátek *modelů/Book. cs* pro vyřešení odkazu na `[JsonProperty]` atribut:</span><span class="sxs-lookup"><span data-stu-id="38ed2-255">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="38ed2-256">Opakujte kroky definované v části [Test webového rozhraní API](#test-the-web-api) .</span><span class="sxs-lookup"><span data-stu-id="38ed2-256">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="38ed2-257">Všimněte si rozdílu v názvech vlastností JSON.</span><span class="sxs-lookup"><span data-stu-id="38ed2-257">Notice the difference in JSON property names.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="38ed2-258">V tomto kurzu se vytvoří webové rozhraní API, které provádí operace vytvoření, čtení, aktualizace a odstranění (CRUD) v databázi [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL.</span><span class="sxs-lookup"><span data-stu-id="38ed2-258">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="38ed2-259">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="38ed2-259">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="38ed2-260">Konfigurace MongoDB</span><span class="sxs-lookup"><span data-stu-id="38ed2-260">Configure MongoDB</span></span>
> * <span data-ttu-id="38ed2-261">Vytvoření databáze MongoDB</span><span class="sxs-lookup"><span data-stu-id="38ed2-261">Create a MongoDB database</span></span>
> * <span data-ttu-id="38ed2-262">Definování kolekce a schématu MongoDB</span><span class="sxs-lookup"><span data-stu-id="38ed2-262">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="38ed2-263">Provádění operací CRUD MongoDB z webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="38ed2-263">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="38ed2-264">Přizpůsobení serializace JSON</span><span class="sxs-lookup"><span data-stu-id="38ed2-264">Customize JSON serialization</span></span>

<span data-ttu-id="38ed2-265">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="38ed2-265">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="38ed2-266">Požadavky</span><span class="sxs-lookup"><span data-stu-id="38ed2-266">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="38ed2-267">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="38ed2-267">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="38ed2-268">.NET Core SDK 2,2</span><span class="sxs-lookup"><span data-stu-id="38ed2-268">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="38ed2-269">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s úlohou **vývoje ASP.NET a webu**</span><span class="sxs-lookup"><span data-stu-id="38ed2-269">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="38ed2-270">MongoDB</span><span class="sxs-lookup"><span data-stu-id="38ed2-270">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="38ed2-271">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="38ed2-271">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="38ed2-272">.NET Core SDK 2,2</span><span class="sxs-lookup"><span data-stu-id="38ed2-272">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="38ed2-273">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="38ed2-273">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="38ed2-274">C# pro Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="38ed2-274">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="38ed2-275">MongoDB</span><span class="sxs-lookup"><span data-stu-id="38ed2-275">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="38ed2-276">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="38ed2-276">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="38ed2-277">.NET Core SDK 2,2</span><span class="sxs-lookup"><span data-stu-id="38ed2-277">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="38ed2-278">Visual Studio pro Mac verze 7,7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="38ed2-278">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="38ed2-279">MongoDB</span><span class="sxs-lookup"><span data-stu-id="38ed2-279">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="38ed2-280">Konfigurace MongoDB</span><span class="sxs-lookup"><span data-stu-id="38ed2-280">Configure MongoDB</span></span>

<span data-ttu-id="38ed2-281">Pokud používáte Windows, MongoDB se nainstaluje ve výchozím nastavení v *\\C\\: Program Files MongoDB* .</span><span class="sxs-lookup"><span data-stu-id="38ed2-281">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="38ed2-282">Přidejte *C:\\Program Files\\MongoDB\\server\\\<version_number>\\bin* do proměnné `Path` prostředí.</span><span class="sxs-lookup"><span data-stu-id="38ed2-282">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="38ed2-283">Tato změna umožňuje přístup k MongoDB odkudkoli na svém vývojovém počítači.</span><span class="sxs-lookup"><span data-stu-id="38ed2-283">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="38ed2-284">Pomocí prostředí Mongo v následujících krocích můžete vytvořit databázi, vytvořit kolekce a uložit dokumenty.</span><span class="sxs-lookup"><span data-stu-id="38ed2-284">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="38ed2-285">Další informace o příkazech prostředí Mongo najdete v tématu [práce s prostředím Mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="38ed2-285">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="38ed2-286">Pro uložení dat vyberte adresář na vývojovém počítači.</span><span class="sxs-lookup"><span data-stu-id="38ed2-286">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="38ed2-287">Například *C:\\BooksData* ve Windows.</span><span class="sxs-lookup"><span data-stu-id="38ed2-287">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="38ed2-288">Pokud adresář neexistuje, vytvořte ho.</span><span class="sxs-lookup"><span data-stu-id="38ed2-288">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="38ed2-289">Prostředí Mongo nevytváří nové adresáře.</span><span class="sxs-lookup"><span data-stu-id="38ed2-289">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="38ed2-290">Otevřete příkazové prostředí.</span><span class="sxs-lookup"><span data-stu-id="38ed2-290">Open a command shell.</span></span> <span data-ttu-id="38ed2-291">Spusťte následující příkaz pro připojení k MongoDB na výchozím portu 27017.</span><span class="sxs-lookup"><span data-stu-id="38ed2-291">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="38ed2-292">Nezapomeňte nahradit `<data_directory_path>` adresář, který jste zvolili v předchozím kroku.</span><span class="sxs-lookup"><span data-stu-id="38ed2-292">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="38ed2-293">Otevřete jinou instanci příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="38ed2-293">Open another command shell instance.</span></span> <span data-ttu-id="38ed2-294">Připojte se k výchozí testovací databázi spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="38ed2-294">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="38ed2-295">V příkazovém prostředí spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="38ed2-295">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="38ed2-296">Pokud ještě neexistuje, vytvoří se databáze s názvem *BookstoreDb* .</span><span class="sxs-lookup"><span data-stu-id="38ed2-296">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="38ed2-297">Pokud databáze existuje, připojení je otevřeno pro transakce.</span><span class="sxs-lookup"><span data-stu-id="38ed2-297">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="38ed2-298">Vytvořte `Books` kolekci pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="38ed2-298">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="38ed2-299">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="38ed2-299">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="38ed2-300">Definujte schéma pro `Books` kolekci a vložte dva dokumenty pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="38ed2-300">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="38ed2-301">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="38ed2-301">The following result is displayed:</span></span>

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
   > <span data-ttu-id="38ed2-302">ID zobrazené v tomto článku se při spuštění této ukázky neshoduje s ID.</span><span class="sxs-lookup"><span data-stu-id="38ed2-302">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="38ed2-303">Zobrazte dokumenty v databázi pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="38ed2-303">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="38ed2-304">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="38ed2-304">The following result is displayed:</span></span>

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

   <span data-ttu-id="38ed2-305">Schéma přidá do každého dokumentu automaticky generovanou `_id` vlastnost typu `ObjectId` .</span><span class="sxs-lookup"><span data-stu-id="38ed2-305">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="38ed2-306">Databáze je připravena.</span><span class="sxs-lookup"><span data-stu-id="38ed2-306">The database is ready.</span></span> <span data-ttu-id="38ed2-307">Můžete začít vytvářet ASP.NET Core webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="38ed2-307">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="38ed2-308">Vytvoření projektu webového rozhraní API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="38ed2-308">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="38ed2-309">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="38ed2-309">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="38ed2-310">Přejít na **soubor** > **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="38ed2-310">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="38ed2-311">Vyberte ASP.NET Core typ projektu **webové aplikace** a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="38ed2-311">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="38ed2-312">Pojmenujte projekt *BooksApi*a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="38ed2-312">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="38ed2-313">Vyberte cílové rozhraní **.NET Core** a **ASP.NET Core 2,2**.</span><span class="sxs-lookup"><span data-stu-id="38ed2-313">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="38ed2-314">Vyberte šablonu projektu **rozhraní API** a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="38ed2-314">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="38ed2-315">Navštivte [galerii NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) a určete nejnovější stabilní verzi ovladače .NET pro MongoDB.</span><span class="sxs-lookup"><span data-stu-id="38ed2-315">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="38ed2-316">V okně **konzoly Správce balíčků** přejděte do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="38ed2-316">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="38ed2-317">Spuštěním následujícího příkazu nainstalujte ovladač .NET pro MongoDB:</span><span class="sxs-lookup"><span data-stu-id="38ed2-317">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="38ed2-318">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="38ed2-318">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="38ed2-319">V příkazovém prostředí spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="38ed2-319">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="38ed2-320">Vygeneruje se nový projekt ASP.NET Core webového rozhraní API cílící na .NET Core a otevře se v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="38ed2-320">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="38ed2-321">Až se ikona plamene u OmniSharp stavového řádku změní na zelenou, dialogové okno požádá **o požadované prostředky k sestavení a ladění chybí v ' BooksApi '. Přidat je?**.</span><span class="sxs-lookup"><span data-stu-id="38ed2-321">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="38ed2-322">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="38ed2-322">Select **Yes**.</span></span>
1. <span data-ttu-id="38ed2-323">Navštivte [galerii NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) a určete nejnovější stabilní verzi ovladače .NET pro MongoDB.</span><span class="sxs-lookup"><span data-stu-id="38ed2-323">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="38ed2-324">Otevřete **integrovaný terminál** a přejděte do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="38ed2-324">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="38ed2-325">Spuštěním následujícího příkazu nainstalujte ovladač .NET pro MongoDB:</span><span class="sxs-lookup"><span data-stu-id="38ed2-325">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="38ed2-326">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="38ed2-326">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="38ed2-327">Přejít na **soubor** > **nové řešení** > **.NET Core** > **aplikace**</span><span class="sxs-lookup"><span data-stu-id="38ed2-327">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="38ed2-328">Vyberte šablonu projektu **ASP.NET Core webového rozhraní API** jazyka C# a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="38ed2-328">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="38ed2-329">V rozevíracím seznamu **cílové rozhraní** vyberte **.NET Core 2,2** a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="38ed2-329">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="38ed2-330">Jako **název projektu**zadejte *BooksApi* a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="38ed2-330">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="38ed2-331">Na panelu **řešení** klikněte pravým tlačítkem myši na uzel **závislosti** projektu a vyberte možnost **Přidat balíčky**.</span><span class="sxs-lookup"><span data-stu-id="38ed2-331">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="38ed2-332">Do vyhledávacího pole zadejte *MongoDB. Driver* , vyberte balíček *MongoDB. Driver* a vyberte **Přidat balíček**.</span><span class="sxs-lookup"><span data-stu-id="38ed2-332">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="38ed2-333">V dialogu pro **přijetí licence** vyberte tlačítko **přijmout** .</span><span class="sxs-lookup"><span data-stu-id="38ed2-333">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="38ed2-334">Přidání modelu entity</span><span class="sxs-lookup"><span data-stu-id="38ed2-334">Add an entity model</span></span>

1. <span data-ttu-id="38ed2-335">Přidejte adresář *modelů* do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="38ed2-335">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="38ed2-336">Přidejte `Book` třídu do adresáře *modelů* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="38ed2-336">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

   <span data-ttu-id="38ed2-337">V předchozí třídě `Id` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="38ed2-337">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="38ed2-338">Je vyžadován pro mapování objektu modulu CLR (Common Language Runtime) na kolekci MongoDB.</span><span class="sxs-lookup"><span data-stu-id="38ed2-338">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="38ed2-339">Je s [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) poznámkami k označení této vlastnosti jako primárního klíče dokumentu.</span><span class="sxs-lookup"><span data-stu-id="38ed2-339">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="38ed2-340">Je opatřen s [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) poznámkami, aby bylo možné předat parametr `string` jako typ namísto struktury [objectID](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) .</span><span class="sxs-lookup"><span data-stu-id="38ed2-340">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="38ed2-341">Mongo zpracovává převod z `string` na. `ObjectId`</span><span class="sxs-lookup"><span data-stu-id="38ed2-341">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="38ed2-342">`BookName` Vlastnost je označena [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) atributem.</span><span class="sxs-lookup"><span data-stu-id="38ed2-342">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="38ed2-343">Hodnota atributu `Name` představuje název vlastnosti v kolekci MongoDB.</span><span class="sxs-lookup"><span data-stu-id="38ed2-343">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="38ed2-344">Přidat konfigurační model</span><span class="sxs-lookup"><span data-stu-id="38ed2-344">Add a configuration model</span></span>

1. <span data-ttu-id="38ed2-345">Do souboru *appSettings. JSON*přidejte následující hodnoty konfigurace databáze:</span><span class="sxs-lookup"><span data-stu-id="38ed2-345">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="38ed2-346">Přidejte soubor *BookstoreDatabaseSettings.cs* do adresáře *Models* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="38ed2-346">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="38ed2-347">Předchozí `BookstoreDatabaseSettings` třída se používá k uložení hodnot `BookstoreDatabaseSettings` vlastností souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="38ed2-347">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="38ed2-348">Názvy vlastností JSON a C# jsou pojmenovány stejně, aby bylo možné zjednodušit proces mapování.</span><span class="sxs-lookup"><span data-stu-id="38ed2-348">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="38ed2-349">Přidejte následující zvýrazněný kód do `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="38ed2-349">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="38ed2-350">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="38ed2-350">In the preceding code:</span></span>

   * <span data-ttu-id="38ed2-351">Instance konfigurace, na kterou se váže `BookstoreDatabaseSettings` sekce souboru *appSettings. JSON* , se registruje v kontejneru injektáže (di).</span><span class="sxs-lookup"><span data-stu-id="38ed2-351">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="38ed2-352">Například `BookstoreDatabaseSettings` `ConnectionString` vlastnost objektu je naplněna `BookstoreDatabaseSettings:ConnectionString` vlastností v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="38ed2-352">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="38ed2-353">`IBookstoreDatabaseSettings` Rozhraní je zaregistrované v di s [životností služby](xref:fundamentals/dependency-injection#service-lifetimes)typu singleton.</span><span class="sxs-lookup"><span data-stu-id="38ed2-353">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="38ed2-354">Při vložení se instance rozhraní překládá na `BookstoreDatabaseSettings` objekt.</span><span class="sxs-lookup"><span data-stu-id="38ed2-354">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="38ed2-355">Pro vyřešení odkazů `BookstoreDatabaseSettings` a `IBookstoreDatabaseSettings` přidejte následující kód na začátek *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="38ed2-355">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="38ed2-356">Přidání služby operace CRUD</span><span class="sxs-lookup"><span data-stu-id="38ed2-356">Add a CRUD operations service</span></span>

1. <span data-ttu-id="38ed2-357">Přidejte adresář *služeb* do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="38ed2-357">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="38ed2-358">Do adresáře `BookService` *služby* přidejte třídu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="38ed2-358">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="38ed2-359">V předchozím kódu je `IBookstoreDatabaseSettings` instance načtena z di přes injektáže konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="38ed2-359">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="38ed2-360">Tento postup poskytuje přístup k hodnotám konfigurace *appSettings. JSON* , které byly přidány do oddílu [přidat konfigurační model](#add-a-configuration-model) .</span><span class="sxs-lookup"><span data-stu-id="38ed2-360">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="38ed2-361">Přidejte následující zvýrazněný kód do `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="38ed2-361">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="38ed2-362">V předchozím kódu je `BookService` třída registrována pomocí příkazu di k podpoře injektáže konstruktoru v rámci využívání tříd.</span><span class="sxs-lookup"><span data-stu-id="38ed2-362">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="38ed2-363">Doba životnosti služby singleton je nejvhodnější, `BookService` protože používá přímou závislost na `MongoClient`.</span><span class="sxs-lookup"><span data-stu-id="38ed2-363">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="38ed2-364">Podle oficiálních [zásad opětovného použití pro klienty Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use) `MongoClient` by se měla registrovat v di s životností služby typu singleton.</span><span class="sxs-lookup"><span data-stu-id="38ed2-364">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="38ed2-365">Přidejte následující kód na začátek *Startup.cs* k vyřešení `BookService` odkazu:</span><span class="sxs-lookup"><span data-stu-id="38ed2-365">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="38ed2-366">`BookService` Třída používá následující `MongoDB.Driver` členy k provádění operací CRUD proti databázi:</span><span class="sxs-lookup"><span data-stu-id="38ed2-366">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="38ed2-367">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; přečte instanci serveru pro provádění databázových operací.</span><span class="sxs-lookup"><span data-stu-id="38ed2-367">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="38ed2-368">K konstruktoru této třídy je poskytnutý připojovací řetězec MongoDB:</span><span class="sxs-lookup"><span data-stu-id="38ed2-368">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="38ed2-369">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; představuje databázi Mongo pro provádění operací.</span><span class="sxs-lookup"><span data-stu-id="38ed2-369">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="38ed2-370">V tomto kurzu se používá obecná metoda [GetCollection\<TDocument> (Collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) na rozhraní pro získání přístupu k datům v určité kolekci.</span><span class="sxs-lookup"><span data-stu-id="38ed2-370">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="38ed2-371">Provede operace CRUD proti kolekci po volání této metody.</span><span class="sxs-lookup"><span data-stu-id="38ed2-371">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="38ed2-372">Ve volání `GetCollection<TDocument>(collection)` metody:</span><span class="sxs-lookup"><span data-stu-id="38ed2-372">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="38ed2-373">`collection`představuje název kolekce.</span><span class="sxs-lookup"><span data-stu-id="38ed2-373">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="38ed2-374">`TDocument`představuje typ objektu CLR uložený v kolekci.</span><span class="sxs-lookup"><span data-stu-id="38ed2-374">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="38ed2-375">`GetCollection<TDocument>(collection)`Vrátí objekt [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) představující kolekci.</span><span class="sxs-lookup"><span data-stu-id="38ed2-375">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="38ed2-376">V tomto kurzu jsou v kolekci vyvolány následující metody:</span><span class="sxs-lookup"><span data-stu-id="38ed2-376">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="38ed2-377">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; odstraní jeden dokument, který odpovídá zadaným kritériím hledání.</span><span class="sxs-lookup"><span data-stu-id="38ed2-377">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="38ed2-378">[Hledání\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; vrátí všechny dokumenty v kolekci, které odpovídají zadaným kritériím hledání.</span><span class="sxs-lookup"><span data-stu-id="38ed2-378">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="38ed2-379">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Vloží zadaný objekt jako nový dokument v kolekci.</span><span class="sxs-lookup"><span data-stu-id="38ed2-379">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="38ed2-380">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; nahrazuje jediný dokument, který odpovídá zadaným kritériím hledání, s dodaným objektem.</span><span class="sxs-lookup"><span data-stu-id="38ed2-380">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="38ed2-381">Přidání kontroleru</span><span class="sxs-lookup"><span data-stu-id="38ed2-381">Add a controller</span></span>

<span data-ttu-id="38ed2-382">Přidejte `BooksController` třídu do adresáře *Controllers* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="38ed2-382">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="38ed2-383">Předchozí kontroler webového rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="38ed2-383">The preceding web API controller:</span></span>

* <span data-ttu-id="38ed2-384">Používá `BookService` třídu k provádění operací CRUD.</span><span class="sxs-lookup"><span data-stu-id="38ed2-384">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="38ed2-385">Obsahuje metody akcí, které podporují požadavky HTTP GET, POST, PUT a DELETE.</span><span class="sxs-lookup"><span data-stu-id="38ed2-385">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="38ed2-386">Volání <xref:System.Web.Http.ApiController.CreatedAtRoute*> v metodě `Create` action vrátí odpověď [http 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) .</span><span class="sxs-lookup"><span data-stu-id="38ed2-386">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="38ed2-387">Stavový kód 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="38ed2-387">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="38ed2-388">`CreatedAtRoute`Přidá také `Location` hlavičku do odpovědi.</span><span class="sxs-lookup"><span data-stu-id="38ed2-388">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="38ed2-389">`Location` Hlavička Určuje identifikátor URI nově vytvořené knihy.</span><span class="sxs-lookup"><span data-stu-id="38ed2-389">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="38ed2-390">Testování webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="38ed2-390">Test the web API</span></span>

1. <span data-ttu-id="38ed2-391">Sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="38ed2-391">Build and run the app.</span></span>

1. <span data-ttu-id="38ed2-392">Přejděte na `http://localhost:<port>/api/books` k otestování metody `Get` akce bez parametrů řadiče.</span><span class="sxs-lookup"><span data-stu-id="38ed2-392">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="38ed2-393">Zobrazí se následující odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="38ed2-393">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="38ed2-394">Přejděte na `http://localhost:<port>/api/books/{id here}` k otestování metody `Get` akce, která je přetížena řadičem.</span><span class="sxs-lookup"><span data-stu-id="38ed2-394">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="38ed2-395">Zobrazí se následující odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="38ed2-395">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="38ed2-396">Konfigurace možností serializace JSON</span><span class="sxs-lookup"><span data-stu-id="38ed2-396">Configure JSON serialization options</span></span>

<span data-ttu-id="38ed2-397">Existují dvě podrobnosti o tom, jak můžete změnit informace o odpovědích JSON vrácených v části [Test webového rozhraní API](#test-the-web-api) :</span><span class="sxs-lookup"><span data-stu-id="38ed2-397">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="38ed2-398">Názvy vlastností výchozí ve stylu CamelCase velká a malá písmena by se měly změnit tak, aby odpovídaly názvu vlastností objektu CLR na velká písmena Pascal.</span><span class="sxs-lookup"><span data-stu-id="38ed2-398">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="38ed2-399">`bookName` Vlastnost by měla být vrácena jako `Name`.</span><span class="sxs-lookup"><span data-stu-id="38ed2-399">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="38ed2-400">Chcete-li splnit předchozí požadavky, proveďte následující změny:</span><span class="sxs-lookup"><span data-stu-id="38ed2-400">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="38ed2-401">V `Startup.ConfigureServices`, řetězení následujícího zvýrazněného kódu k volání `AddMvc` metody:</span><span class="sxs-lookup"><span data-stu-id="38ed2-401">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="38ed2-402">V předchozí změně názvy vlastností v serializovaných odpovědích JSON webového rozhraní API odpovídají jejich odpovídajícím názvům vlastností v typu objektu CLR.</span><span class="sxs-lookup"><span data-stu-id="38ed2-402">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="38ed2-403">Například `Book` `Author` vlastnost třídy je serializována jako `Author`.</span><span class="sxs-lookup"><span data-stu-id="38ed2-403">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="38ed2-404">V části *Models/Book. cs*poznámku k `BookName` vlastnosti s následujícím [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) atributem:</span><span class="sxs-lookup"><span data-stu-id="38ed2-404">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="38ed2-405">Hodnota `[JsonProperty]` atributu `Name` představuje název vlastnosti v serializované odpovědi JSON webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="38ed2-405">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="38ed2-406">Přidejte následující kód na začátek *modelů/Book. cs* pro vyřešení odkazu na `[JsonProperty]` atribut:</span><span class="sxs-lookup"><span data-stu-id="38ed2-406">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="38ed2-407">Opakujte kroky definované v části [Test webového rozhraní API](#test-the-web-api) .</span><span class="sxs-lookup"><span data-stu-id="38ed2-407">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="38ed2-408">Všimněte si rozdílu v názvech vlastností JSON.</span><span class="sxs-lookup"><span data-stu-id="38ed2-408">Notice the difference in JSON property names.</span></span>

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="38ed2-409">Přidání podpory ověřování do webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="38ed2-409">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a><span data-ttu-id="38ed2-410">Další kroky</span><span class="sxs-lookup"><span data-stu-id="38ed2-410">Next steps</span></span>

<span data-ttu-id="38ed2-411">Další informace o vytváření ASP.NET Core webových rozhraní API najdete v následujících zdrojích informací:</span><span class="sxs-lookup"><span data-stu-id="38ed2-411">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="38ed2-412">YouTube verze tohoto článku</span><span class="sxs-lookup"><span data-stu-id="38ed2-412">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>

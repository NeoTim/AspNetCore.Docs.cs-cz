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
ms.openlocfilehash: 46607fc92670bb46a155ddf3248bc8a36b600a4a
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452249"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="192ad-103">Vytvoření webového rozhraní API pomocí ASP.NET Core a MongoDB</span><span class="sxs-lookup"><span data-stu-id="192ad-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="192ad-104">Od [Pratik Khandelwal](https://twitter.com/K2Prk) a [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="192ad-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="192ad-105">V tomto kurzu se vytvoří webové rozhraní API, které provádí operace vytvoření, čtení, aktualizace a odstranění (CRUD) v databázi [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL.</span><span class="sxs-lookup"><span data-stu-id="192ad-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="192ad-106">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="192ad-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="192ad-107">Konfigurace MongoDB</span><span class="sxs-lookup"><span data-stu-id="192ad-107">Configure MongoDB</span></span>
> * <span data-ttu-id="192ad-108">Vytvoření databáze MongoDB</span><span class="sxs-lookup"><span data-stu-id="192ad-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="192ad-109">Definování kolekce a schématu MongoDB</span><span class="sxs-lookup"><span data-stu-id="192ad-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="192ad-110">Provádění operací CRUD MongoDB z webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="192ad-110">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="192ad-111">Přizpůsobení serializace JSON</span><span class="sxs-lookup"><span data-stu-id="192ad-111">Customize JSON serialization</span></span>

<span data-ttu-id="192ad-112">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="192ad-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="192ad-113">Požadavky</span><span class="sxs-lookup"><span data-stu-id="192ad-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="192ad-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="192ad-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="192ad-115">.NET Core SDK 3.0 nebo novější</span><span class="sxs-lookup"><span data-stu-id="192ad-115">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="192ad-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s úlohou **vývoje ASP.NET a webu**</span><span class="sxs-lookup"><span data-stu-id="192ad-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="192ad-117">MongoDB</span><span class="sxs-lookup"><span data-stu-id="192ad-117">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="192ad-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="192ad-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="192ad-119">.NET Core SDK 3.0 nebo novější</span><span class="sxs-lookup"><span data-stu-id="192ad-119">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="192ad-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="192ad-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="192ad-121">C# pro Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="192ad-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="192ad-122">MongoDB</span><span class="sxs-lookup"><span data-stu-id="192ad-122">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="192ad-123">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="192ad-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="192ad-124">.NET Core SDK 3.0 nebo novější</span><span class="sxs-lookup"><span data-stu-id="192ad-124">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="192ad-125">Visual Studio pro Mac verze 7,7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="192ad-125">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="192ad-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="192ad-126">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="192ad-127">Konfigurace MongoDB</span><span class="sxs-lookup"><span data-stu-id="192ad-127">Configure MongoDB</span></span>

<span data-ttu-id="192ad-128">Pokud používáte Windows, MongoDB se nainstaluje ve výchozím nastavení v *C: \\ Program Files \\ MongoDB* .</span><span class="sxs-lookup"><span data-stu-id="192ad-128">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="192ad-129">Přidejte *C: \\ Program Files \\ MongoDB \\ Server \\ \<version_number> \\ bin* do `Path` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="192ad-129">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="192ad-130">Tato změna umožňuje přístup k MongoDB odkudkoli na svém vývojovém počítači.</span><span class="sxs-lookup"><span data-stu-id="192ad-130">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="192ad-131">Pomocí prostředí Mongo v následujících krocích můžete vytvořit databázi, vytvořit kolekce a uložit dokumenty.</span><span class="sxs-lookup"><span data-stu-id="192ad-131">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="192ad-132">Další informace o příkazech prostředí Mongo najdete v tématu [práce s prostředím Mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="192ad-132">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="192ad-133">Pro uložení dat vyberte adresář na vývojovém počítači.</span><span class="sxs-lookup"><span data-stu-id="192ad-133">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="192ad-134">Například *C: \\ BooksData* ve Windows.</span><span class="sxs-lookup"><span data-stu-id="192ad-134">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="192ad-135">Pokud adresář neexistuje, vytvořte ho.</span><span class="sxs-lookup"><span data-stu-id="192ad-135">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="192ad-136">Prostředí Mongo nevytváří nové adresáře.</span><span class="sxs-lookup"><span data-stu-id="192ad-136">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="192ad-137">Otevřete příkazové prostředí.</span><span class="sxs-lookup"><span data-stu-id="192ad-137">Open a command shell.</span></span> <span data-ttu-id="192ad-138">Spusťte následující příkaz pro připojení k MongoDB na výchozím portu 27017.</span><span class="sxs-lookup"><span data-stu-id="192ad-138">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="192ad-139">Nezapomeňte nahradit `<data_directory_path>` adresář, který jste zvolili v předchozím kroku.</span><span class="sxs-lookup"><span data-stu-id="192ad-139">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="192ad-140">Otevřete jinou instanci příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="192ad-140">Open another command shell instance.</span></span> <span data-ttu-id="192ad-141">Připojte se k výchozí testovací databázi spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="192ad-141">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="192ad-142">V příkazovém prostředí spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="192ad-142">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="192ad-143">Pokud ještě neexistuje, vytvoří se databáze s názvem *BookstoreDb* .</span><span class="sxs-lookup"><span data-stu-id="192ad-143">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="192ad-144">Pokud databáze existuje, připojení je otevřeno pro transakce.</span><span class="sxs-lookup"><span data-stu-id="192ad-144">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="192ad-145">Vytvořte `Books` kolekci pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="192ad-145">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="192ad-146">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="192ad-146">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="192ad-147">Definujte schéma pro `Books` kolekci a vložte dva dokumenty pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="192ad-147">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="192ad-148">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="192ad-148">The following result is displayed:</span></span>

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
   > <span data-ttu-id="192ad-149">ID zobrazené v tomto článku se při spuštění této ukázky neshoduje s ID.</span><span class="sxs-lookup"><span data-stu-id="192ad-149">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="192ad-150">Zobrazte dokumenty v databázi pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="192ad-150">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="192ad-151">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="192ad-151">The following result is displayed:</span></span>

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

   <span data-ttu-id="192ad-152">Schéma přidá do každého dokumentu automaticky generovanou `_id` vlastnost typu `ObjectId` .</span><span class="sxs-lookup"><span data-stu-id="192ad-152">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="192ad-153">Databáze je připravena.</span><span class="sxs-lookup"><span data-stu-id="192ad-153">The database is ready.</span></span> <span data-ttu-id="192ad-154">Můžete začít vytvářet ASP.NET Core webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="192ad-154">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="192ad-155">Vytvoření projektu webového rozhraní API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="192ad-155">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="192ad-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="192ad-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="192ad-157">Přejít na **soubor** > **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="192ad-157">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="192ad-158">Vyberte ASP.NET Core typ projektu **webové aplikace** a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="192ad-158">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="192ad-159">Pojmenujte projekt *BooksApi*a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="192ad-159">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="192ad-160">Vyberte cílové rozhraní **.NET Core** a **ASP.NET Core 3,0**.</span><span class="sxs-lookup"><span data-stu-id="192ad-160">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="192ad-161">Vyberte šablonu projektu **rozhraní API** a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="192ad-161">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="192ad-162">Navštivte [galerii NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) a určete nejnovější stabilní verzi ovladače .NET pro MongoDB.</span><span class="sxs-lookup"><span data-stu-id="192ad-162">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="192ad-163">V okně **konzoly Správce balíčků** přejděte do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="192ad-163">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="192ad-164">Spuštěním následujícího příkazu nainstalujte ovladač .NET pro MongoDB:</span><span class="sxs-lookup"><span data-stu-id="192ad-164">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="192ad-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="192ad-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="192ad-166">V příkazovém prostředí spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="192ad-166">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="192ad-167">Vygeneruje se nový projekt ASP.NET Core webového rozhraní API cílící na .NET Core a otevře se v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="192ad-167">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="192ad-168">Až se ikona plamene u OmniSharp stavového řádku změní na zelenou, dialogové okno požádá **o požadované prostředky k sestavení a ladění chybí v ' BooksApi '. Přidat je?**.</span><span class="sxs-lookup"><span data-stu-id="192ad-168">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="192ad-169">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="192ad-169">Select **Yes**.</span></span>
1. <span data-ttu-id="192ad-170">Navštivte [galerii NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) a určete nejnovější stabilní verzi ovladače .NET pro MongoDB.</span><span class="sxs-lookup"><span data-stu-id="192ad-170">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="192ad-171">Otevřete **integrovaný terminál** a přejděte do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="192ad-171">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="192ad-172">Spuštěním následujícího příkazu nainstalujte ovladač .NET pro MongoDB:</span><span class="sxs-lookup"><span data-stu-id="192ad-172">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="192ad-173">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="192ad-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="192ad-174">V Visual Studio pro Mac starších než verze 8,6 vyberte **soubor**  >  **Nový řešení**  >  **.NET Core**  >  **aplikace** z tohoto bočního panelu.</span><span class="sxs-lookup"><span data-stu-id="192ad-174">In Visual Studio for Mac earlier than version 8.6, select **File** > **New Solution** > **.NET Core** > **App** from the sidebar.</span></span> <span data-ttu-id="192ad-175">Ve verzi 8,6 nebo novější vyberte v **File**  >  postranním panelu soubor**nové řešení**  >  **web a konzolová**  >  **aplikace** .</span><span class="sxs-lookup"><span data-stu-id="192ad-175">In version 8.6 or later, select **File** > **New Solution** > **Web and Console** > **App** from the sidebar.</span></span>
1. <span data-ttu-id="192ad-176">Vyberte šablonu projektu **ASP.NET Core** > **API** jazyka C# a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="192ad-176">Select the **ASP.NET Core** > **API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="192ad-177">V rozevíracím seznamu **cílové rozhraní** vyberte **.NET Core 3,1** a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="192ad-177">Select **.NET Core 3.1** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="192ad-178">Jako **název projektu**zadejte *BooksApi* a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="192ad-178">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="192ad-179">Na panelu **řešení** klikněte pravým tlačítkem myši na uzel **závislosti** projektu a vyberte možnost **Přidat balíčky**.</span><span class="sxs-lookup"><span data-stu-id="192ad-179">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="192ad-180">Do vyhledávacího pole zadejte *MongoDB. Driver* , vyberte balíček *MongoDB. Driver* a vyberte **Přidat balíček**.</span><span class="sxs-lookup"><span data-stu-id="192ad-180">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="192ad-181">V dialogu pro **přijetí licence** vyberte tlačítko **přijmout** .</span><span class="sxs-lookup"><span data-stu-id="192ad-181">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="192ad-182">Přidání modelu entity</span><span class="sxs-lookup"><span data-stu-id="192ad-182">Add an entity model</span></span>

1. <span data-ttu-id="192ad-183">Přidejte adresář *modelů* do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="192ad-183">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="192ad-184">Přidejte `Book` třídu do adresáře *modelů* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="192ad-184">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

   <span data-ttu-id="192ad-185">V předchozí třídě `Id` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="192ad-185">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="192ad-186">Je vyžadován pro mapování objektu modulu CLR (Common Language Runtime) na kolekci MongoDB.</span><span class="sxs-lookup"><span data-stu-id="192ad-186">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="192ad-187">Je s poznámkami [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) k označení této vlastnosti jako primárního klíče dokumentu.</span><span class="sxs-lookup"><span data-stu-id="192ad-187">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="192ad-188">Je opatřen s poznámkami, [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) aby bylo možné předat parametr jako typ `string` namísto struktury [objectID](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) .</span><span class="sxs-lookup"><span data-stu-id="192ad-188">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="192ad-189">Mongo zpracovává převod z `string` na `ObjectId` .</span><span class="sxs-lookup"><span data-stu-id="192ad-189">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="192ad-190">`BookName`Vlastnost je označena [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) atributem.</span><span class="sxs-lookup"><span data-stu-id="192ad-190">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="192ad-191">Hodnota atributu `Name` představuje název vlastnosti v kolekci MongoDB.</span><span class="sxs-lookup"><span data-stu-id="192ad-191">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="192ad-192">Přidat konfigurační model</span><span class="sxs-lookup"><span data-stu-id="192ad-192">Add a configuration model</span></span>

1. <span data-ttu-id="192ad-193">Do souboru *appSettings. JSON*přidejte následující hodnoty konfigurace databáze:</span><span class="sxs-lookup"><span data-stu-id="192ad-193">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="192ad-194">Přidejte soubor *BookstoreDatabaseSettings.cs* do adresáře *Models* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="192ad-194">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="192ad-195">Předchozí `BookstoreDatabaseSettings` Třída se používá k uložení hodnot vlastností souboru *appSettings. JSON* `BookstoreDatabaseSettings` .</span><span class="sxs-lookup"><span data-stu-id="192ad-195">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="192ad-196">Názvy vlastností JSON a C# jsou pojmenovány stejně, aby bylo možné zjednodušit proces mapování.</span><span class="sxs-lookup"><span data-stu-id="192ad-196">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="192ad-197">Přidejte následující zvýrazněný kód do `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="192ad-197">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   <span data-ttu-id="192ad-198">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="192ad-198">In the preceding code:</span></span>

   * <span data-ttu-id="192ad-199">Instance konfigurace, na kterou se váže sekce souboru *appSettings. JSON* , `BookstoreDatabaseSettings` se registruje v kontejneru injektáže (di).</span><span class="sxs-lookup"><span data-stu-id="192ad-199">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="192ad-200">Například `BookstoreDatabaseSettings` `ConnectionString` vlastnost objektu je naplněna `BookstoreDatabaseSettings:ConnectionString` vlastností v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="192ad-200">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="192ad-201">`IBookstoreDatabaseSettings`Rozhraní je zaregistrované v di s [životností služby](xref:fundamentals/dependency-injection#service-lifetimes)typu singleton.</span><span class="sxs-lookup"><span data-stu-id="192ad-201">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="192ad-202">Při vložení se instance rozhraní překládá na `BookstoreDatabaseSettings` objekt.</span><span class="sxs-lookup"><span data-stu-id="192ad-202">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="192ad-203">Pro vyřešení odkazů a přidejte následující kód na začátek *Startup.cs* `BookstoreDatabaseSettings` `IBookstoreDatabaseSettings` :</span><span class="sxs-lookup"><span data-stu-id="192ad-203">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="192ad-204">Přidání služby operace CRUD</span><span class="sxs-lookup"><span data-stu-id="192ad-204">Add a CRUD operations service</span></span>

1. <span data-ttu-id="192ad-205">Přidejte adresář *služeb* do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="192ad-205">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="192ad-206">`BookService`Do adresáře *služby* přidejte třídu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="192ad-206">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="192ad-207">V předchozím kódu `IBookstoreDatabaseSettings` je instance načtena z di přes injektáže konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="192ad-207">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="192ad-208">Tento postup poskytuje přístup k hodnotám konfigurace *appSettings. JSON* , které byly přidány do oddílu [přidat konfigurační model](#add-a-configuration-model) .</span><span class="sxs-lookup"><span data-stu-id="192ad-208">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="192ad-209">Přidejte následující zvýrazněný kód do `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="192ad-209">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="192ad-210">V předchozím kódu `BookService` je třída registrována pomocí příkazu di k podpoře injektáže konstruktoru v rámci využívání tříd.</span><span class="sxs-lookup"><span data-stu-id="192ad-210">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="192ad-211">Doba životnosti služby singleton je nejvhodnější `BookService` , protože používá přímou závislost na `MongoClient` .</span><span class="sxs-lookup"><span data-stu-id="192ad-211">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="192ad-212">Podle oficiálních [zásad opětovného použití pro klienty Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use) `MongoClient` by se měla registrovat v di s životností služby typu singleton.</span><span class="sxs-lookup"><span data-stu-id="192ad-212">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="192ad-213">Přidejte následující kód na začátek *Startup.cs* k vyřešení `BookService` odkazu:</span><span class="sxs-lookup"><span data-stu-id="192ad-213">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="192ad-214">`BookService`Třída používá následující `MongoDB.Driver` členy k provádění operací CRUD proti databázi:</span><span class="sxs-lookup"><span data-stu-id="192ad-214">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="192ad-215">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): přečte instanci serveru pro provádění databázových operací.</span><span class="sxs-lookup"><span data-stu-id="192ad-215">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="192ad-216">K konstruktoru této třídy je poskytnutý připojovací řetězec MongoDB:</span><span class="sxs-lookup"><span data-stu-id="192ad-216">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="192ad-217">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): představuje databázi Mongo pro provádění operací.</span><span class="sxs-lookup"><span data-stu-id="192ad-217">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="192ad-218">V tomto kurzu se používá obecná metoda [GetCollection \<TDocument> (Collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) na rozhraní pro získání přístupu k datům v určité kolekci.</span><span class="sxs-lookup"><span data-stu-id="192ad-218">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="192ad-219">Provede operace CRUD proti kolekci po volání této metody.</span><span class="sxs-lookup"><span data-stu-id="192ad-219">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="192ad-220">Ve `GetCollection<TDocument>(collection)` volání metody:</span><span class="sxs-lookup"><span data-stu-id="192ad-220">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="192ad-221">`collection`představuje název kolekce.</span><span class="sxs-lookup"><span data-stu-id="192ad-221">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="192ad-222">`TDocument`představuje typ objektu CLR uložený v kolekci.</span><span class="sxs-lookup"><span data-stu-id="192ad-222">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="192ad-223">`GetCollection<TDocument>(collection)`Vrátí objekt [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) představující kolekci.</span><span class="sxs-lookup"><span data-stu-id="192ad-223">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="192ad-224">V tomto kurzu jsou v kolekci vyvolány následující metody:</span><span class="sxs-lookup"><span data-stu-id="192ad-224">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="192ad-225">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Odstraní jeden dokument, který odpovídá zadaným kritériím hledání.</span><span class="sxs-lookup"><span data-stu-id="192ad-225">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="192ad-226">[Find \<TDocument> ](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): vrátí všechny dokumenty v kolekci, které odpovídají zadaným kritériím hledání.</span><span class="sxs-lookup"><span data-stu-id="192ad-226">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="192ad-227">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Vloží zadaný objekt jako nový dokument v kolekci.</span><span class="sxs-lookup"><span data-stu-id="192ad-227">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="192ad-228">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): nahradí jediný dokument, který odpovídá zadaným kritériím hledání, dodanému objektu.</span><span class="sxs-lookup"><span data-stu-id="192ad-228">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="192ad-229">Přidání kontroleru</span><span class="sxs-lookup"><span data-stu-id="192ad-229">Add a controller</span></span>

<span data-ttu-id="192ad-230">Přidejte `BooksController` třídu do adresáře *Controllers* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="192ad-230">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="192ad-231">Předchozí kontroler webového rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="192ad-231">The preceding web API controller:</span></span>

* <span data-ttu-id="192ad-232">Používá `BookService` třídu k provádění operací CRUD.</span><span class="sxs-lookup"><span data-stu-id="192ad-232">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="192ad-233">Obsahuje metody akcí, které podporují požadavky HTTP GET, POST, PUT a DELETE.</span><span class="sxs-lookup"><span data-stu-id="192ad-233">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="192ad-234">Volání <xref:System.Web.Http.ApiController.CreatedAtRoute*> v `Create` metodě Action vrátí odpověď [http 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) .</span><span class="sxs-lookup"><span data-stu-id="192ad-234">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="192ad-235">Stavový kód 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="192ad-235">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="192ad-236">`CreatedAtRoute`Přidá také `Location` hlavičku do odpovědi.</span><span class="sxs-lookup"><span data-stu-id="192ad-236">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="192ad-237">`Location`Hlavička Určuje identifikátor URI nově vytvořené knihy.</span><span class="sxs-lookup"><span data-stu-id="192ad-237">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="192ad-238">Testování webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="192ad-238">Test the web API</span></span>

1. <span data-ttu-id="192ad-239">Sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="192ad-239">Build and run the app.</span></span>

1. <span data-ttu-id="192ad-240">Přejděte na `http://localhost:<port>/api/books` k otestování metody akce bez parametrů řadiče `Get` .</span><span class="sxs-lookup"><span data-stu-id="192ad-240">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="192ad-241">Zobrazí se následující odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="192ad-241">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="192ad-242">Přejděte na `http://localhost:<port>/api/books/{id here}` k otestování metody akce, která je přetížena řadičem `Get` .</span><span class="sxs-lookup"><span data-stu-id="192ad-242">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="192ad-243">Zobrazí se následující odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="192ad-243">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="192ad-244">Konfigurace možností serializace JSON</span><span class="sxs-lookup"><span data-stu-id="192ad-244">Configure JSON serialization options</span></span>

<span data-ttu-id="192ad-245">Existují dvě podrobnosti o tom, jak můžete změnit informace o odpovědích JSON vrácených v části [Test webového rozhraní API](#test-the-web-api) :</span><span class="sxs-lookup"><span data-stu-id="192ad-245">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="192ad-246">Názvy vlastností výchozí ve stylu CamelCase velká a malá písmena by se měly změnit tak, aby odpovídaly názvu vlastností objektu CLR na velká písmena Pascal.</span><span class="sxs-lookup"><span data-stu-id="192ad-246">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="192ad-247">`bookName`Vlastnost by měla být vrácena jako `Name` .</span><span class="sxs-lookup"><span data-stu-id="192ad-247">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="192ad-248">Chcete-li splnit předchozí požadavky, proveďte následující změny:</span><span class="sxs-lookup"><span data-stu-id="192ad-248">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="192ad-249">JSON.NET se odebral ze sdílené architektury ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="192ad-249">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="192ad-250">Přidejte odkaz na balíček do [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) .</span><span class="sxs-lookup"><span data-stu-id="192ad-250">Add a package reference to [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="192ad-251">V `Startup.ConfigureServices` , řetězení následujícího zvýrazněného kódu k `AddControllers` volání metody:</span><span class="sxs-lookup"><span data-stu-id="192ad-251">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddControllers` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="192ad-252">V předchozí změně názvy vlastností v serializovaných odpovědích JSON webového rozhraní API odpovídají jejich odpovídajícím názvům vlastností v typu objektu CLR.</span><span class="sxs-lookup"><span data-stu-id="192ad-252">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="192ad-253">Například `Book` vlastnost třídy je `Author` serializována jako `Author` .</span><span class="sxs-lookup"><span data-stu-id="192ad-253">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="192ad-254">V části *Models/Book. cs*poznámku k `BookName` vlastnosti s následujícím [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) atributem:</span><span class="sxs-lookup"><span data-stu-id="192ad-254">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="192ad-255">`[JsonProperty]`Hodnota atributu `Name` představuje název vlastnosti v serializované odpovědi JSON webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="192ad-255">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="192ad-256">Přidejte následující kód na začátek *modelů/Book. cs* pro vyřešení `[JsonProperty]` odkazu na atribut:</span><span class="sxs-lookup"><span data-stu-id="192ad-256">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="192ad-257">Opakujte kroky definované v části [Test webového rozhraní API](#test-the-web-api) .</span><span class="sxs-lookup"><span data-stu-id="192ad-257">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="192ad-258">Všimněte si rozdílu v názvech vlastností JSON.</span><span class="sxs-lookup"><span data-stu-id="192ad-258">Notice the difference in JSON property names.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="192ad-259">V tomto kurzu se vytvoří webové rozhraní API, které provádí operace vytvoření, čtení, aktualizace a odstranění (CRUD) v databázi [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL.</span><span class="sxs-lookup"><span data-stu-id="192ad-259">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="192ad-260">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="192ad-260">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="192ad-261">Konfigurace MongoDB</span><span class="sxs-lookup"><span data-stu-id="192ad-261">Configure MongoDB</span></span>
> * <span data-ttu-id="192ad-262">Vytvoření databáze MongoDB</span><span class="sxs-lookup"><span data-stu-id="192ad-262">Create a MongoDB database</span></span>
> * <span data-ttu-id="192ad-263">Definování kolekce a schématu MongoDB</span><span class="sxs-lookup"><span data-stu-id="192ad-263">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="192ad-264">Provádění operací CRUD MongoDB z webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="192ad-264">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="192ad-265">Přizpůsobení serializace JSON</span><span class="sxs-lookup"><span data-stu-id="192ad-265">Customize JSON serialization</span></span>

<span data-ttu-id="192ad-266">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="192ad-266">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="192ad-267">Požadavky</span><span class="sxs-lookup"><span data-stu-id="192ad-267">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="192ad-268">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="192ad-268">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="192ad-269">.NET Core SDK 2,2</span><span class="sxs-lookup"><span data-stu-id="192ad-269">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="192ad-270">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s úlohou **vývoje ASP.NET a webu**</span><span class="sxs-lookup"><span data-stu-id="192ad-270">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="192ad-271">MongoDB</span><span class="sxs-lookup"><span data-stu-id="192ad-271">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="192ad-272">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="192ad-272">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="192ad-273">.NET Core SDK 2,2</span><span class="sxs-lookup"><span data-stu-id="192ad-273">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="192ad-274">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="192ad-274">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="192ad-275">C# pro Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="192ad-275">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="192ad-276">MongoDB</span><span class="sxs-lookup"><span data-stu-id="192ad-276">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="192ad-277">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="192ad-277">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="192ad-278">.NET Core SDK 2,2</span><span class="sxs-lookup"><span data-stu-id="192ad-278">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="192ad-279">Visual Studio pro Mac verze 7,7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="192ad-279">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="192ad-280">MongoDB</span><span class="sxs-lookup"><span data-stu-id="192ad-280">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="192ad-281">Konfigurace MongoDB</span><span class="sxs-lookup"><span data-stu-id="192ad-281">Configure MongoDB</span></span>

<span data-ttu-id="192ad-282">Pokud používáte Windows, MongoDB se nainstaluje ve výchozím nastavení v *C: \\ Program Files \\ MongoDB* .</span><span class="sxs-lookup"><span data-stu-id="192ad-282">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="192ad-283">Přidejte *C: \\ Program Files \\ MongoDB \\ Server \\ \<version_number> \\ bin* do `Path` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="192ad-283">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="192ad-284">Tato změna umožňuje přístup k MongoDB odkudkoli na svém vývojovém počítači.</span><span class="sxs-lookup"><span data-stu-id="192ad-284">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="192ad-285">Pomocí prostředí Mongo v následujících krocích můžete vytvořit databázi, vytvořit kolekce a uložit dokumenty.</span><span class="sxs-lookup"><span data-stu-id="192ad-285">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="192ad-286">Další informace o příkazech prostředí Mongo najdete v tématu [práce s prostředím Mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="192ad-286">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="192ad-287">Pro uložení dat vyberte adresář na vývojovém počítači.</span><span class="sxs-lookup"><span data-stu-id="192ad-287">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="192ad-288">Například *C: \\ BooksData* ve Windows.</span><span class="sxs-lookup"><span data-stu-id="192ad-288">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="192ad-289">Pokud adresář neexistuje, vytvořte ho.</span><span class="sxs-lookup"><span data-stu-id="192ad-289">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="192ad-290">Prostředí Mongo nevytváří nové adresáře.</span><span class="sxs-lookup"><span data-stu-id="192ad-290">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="192ad-291">Otevřete příkazové prostředí.</span><span class="sxs-lookup"><span data-stu-id="192ad-291">Open a command shell.</span></span> <span data-ttu-id="192ad-292">Spusťte následující příkaz pro připojení k MongoDB na výchozím portu 27017.</span><span class="sxs-lookup"><span data-stu-id="192ad-292">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="192ad-293">Nezapomeňte nahradit `<data_directory_path>` adresář, který jste zvolili v předchozím kroku.</span><span class="sxs-lookup"><span data-stu-id="192ad-293">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="192ad-294">Otevřete jinou instanci příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="192ad-294">Open another command shell instance.</span></span> <span data-ttu-id="192ad-295">Připojte se k výchozí testovací databázi spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="192ad-295">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="192ad-296">V příkazovém prostředí spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="192ad-296">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="192ad-297">Pokud ještě neexistuje, vytvoří se databáze s názvem *BookstoreDb* .</span><span class="sxs-lookup"><span data-stu-id="192ad-297">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="192ad-298">Pokud databáze existuje, připojení je otevřeno pro transakce.</span><span class="sxs-lookup"><span data-stu-id="192ad-298">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="192ad-299">Vytvořte `Books` kolekci pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="192ad-299">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="192ad-300">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="192ad-300">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="192ad-301">Definujte schéma pro `Books` kolekci a vložte dva dokumenty pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="192ad-301">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="192ad-302">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="192ad-302">The following result is displayed:</span></span>

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
   > <span data-ttu-id="192ad-303">ID zobrazené v tomto článku se při spuštění této ukázky neshoduje s ID.</span><span class="sxs-lookup"><span data-stu-id="192ad-303">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="192ad-304">Zobrazte dokumenty v databázi pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="192ad-304">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="192ad-305">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="192ad-305">The following result is displayed:</span></span>

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

   <span data-ttu-id="192ad-306">Schéma přidá do každého dokumentu automaticky generovanou `_id` vlastnost typu `ObjectId` .</span><span class="sxs-lookup"><span data-stu-id="192ad-306">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="192ad-307">Databáze je připravena.</span><span class="sxs-lookup"><span data-stu-id="192ad-307">The database is ready.</span></span> <span data-ttu-id="192ad-308">Můžete začít vytvářet ASP.NET Core webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="192ad-308">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="192ad-309">Vytvoření projektu webového rozhraní API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="192ad-309">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="192ad-310">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="192ad-310">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="192ad-311">Přejít na **soubor** > **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="192ad-311">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="192ad-312">Vyberte ASP.NET Core typ projektu **webové aplikace** a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="192ad-312">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="192ad-313">Pojmenujte projekt *BooksApi*a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="192ad-313">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="192ad-314">Vyberte cílové rozhraní **.NET Core** a **ASP.NET Core 2,2**.</span><span class="sxs-lookup"><span data-stu-id="192ad-314">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="192ad-315">Vyberte šablonu projektu **rozhraní API** a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="192ad-315">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="192ad-316">Navštivte [galerii NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) a určete nejnovější stabilní verzi ovladače .NET pro MongoDB.</span><span class="sxs-lookup"><span data-stu-id="192ad-316">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="192ad-317">V okně **konzoly Správce balíčků** přejděte do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="192ad-317">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="192ad-318">Spuštěním následujícího příkazu nainstalujte ovladač .NET pro MongoDB:</span><span class="sxs-lookup"><span data-stu-id="192ad-318">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="192ad-319">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="192ad-319">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="192ad-320">V příkazovém prostředí spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="192ad-320">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="192ad-321">Vygeneruje se nový projekt ASP.NET Core webového rozhraní API cílící na .NET Core a otevře se v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="192ad-321">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="192ad-322">Až se ikona plamene u OmniSharp stavového řádku změní na zelenou, dialogové okno požádá **o požadované prostředky k sestavení a ladění chybí v ' BooksApi '. Přidat je?**.</span><span class="sxs-lookup"><span data-stu-id="192ad-322">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="192ad-323">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="192ad-323">Select **Yes**.</span></span>
1. <span data-ttu-id="192ad-324">Navštivte [galerii NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) a určete nejnovější stabilní verzi ovladače .NET pro MongoDB.</span><span class="sxs-lookup"><span data-stu-id="192ad-324">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="192ad-325">Otevřete **integrovaný terminál** a přejděte do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="192ad-325">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="192ad-326">Spuštěním následujícího příkazu nainstalujte ovladač .NET pro MongoDB:</span><span class="sxs-lookup"><span data-stu-id="192ad-326">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="192ad-327">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="192ad-327">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="192ad-328">V Visual Studio pro Mac starších než verze 8,6 vyberte **soubor**  >  **Nový řešení**  >  **.NET Core**  >  **aplikace** z tohoto bočního panelu.</span><span class="sxs-lookup"><span data-stu-id="192ad-328">In Visual Studio for Mac earlier than version 8.6, select **File** > **New Solution** > **.NET Core** > **App** from the sidebar.</span></span> <span data-ttu-id="192ad-329">Ve verzi 8,6 nebo novější vyberte v **File**  >  postranním panelu soubor**nové řešení**  >  **web a konzolová**  >  **aplikace** .</span><span class="sxs-lookup"><span data-stu-id="192ad-329">In version 8.6 or later, select **File** > **New Solution** > **Web and Console** > **App** from the sidebar.</span></span>
1. <span data-ttu-id="192ad-330">Vyberte šablonu projektu **ASP.NET Core webového rozhraní API** jazyka C# a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="192ad-330">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="192ad-331">V rozevíracím seznamu **cílové rozhraní** vyberte **.NET Core 2,2** a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="192ad-331">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="192ad-332">Jako **název projektu**zadejte *BooksApi* a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="192ad-332">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="192ad-333">Na panelu **řešení** klikněte pravým tlačítkem myši na uzel **závislosti** projektu a vyberte možnost **Přidat balíčky**.</span><span class="sxs-lookup"><span data-stu-id="192ad-333">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="192ad-334">Do vyhledávacího pole zadejte *MongoDB. Driver* , vyberte balíček *MongoDB. Driver* a vyberte **Přidat balíček**.</span><span class="sxs-lookup"><span data-stu-id="192ad-334">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="192ad-335">V dialogu pro **přijetí licence** vyberte tlačítko **přijmout** .</span><span class="sxs-lookup"><span data-stu-id="192ad-335">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="192ad-336">Přidání modelu entity</span><span class="sxs-lookup"><span data-stu-id="192ad-336">Add an entity model</span></span>

1. <span data-ttu-id="192ad-337">Přidejte adresář *modelů* do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="192ad-337">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="192ad-338">Přidejte `Book` třídu do adresáře *modelů* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="192ad-338">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

   <span data-ttu-id="192ad-339">V předchozí třídě `Id` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="192ad-339">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="192ad-340">Je vyžadován pro mapování objektu modulu CLR (Common Language Runtime) na kolekci MongoDB.</span><span class="sxs-lookup"><span data-stu-id="192ad-340">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="192ad-341">Je s poznámkami [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) k označení této vlastnosti jako primárního klíče dokumentu.</span><span class="sxs-lookup"><span data-stu-id="192ad-341">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="192ad-342">Je opatřen s poznámkami, [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) aby bylo možné předat parametr jako typ `string` namísto struktury [objectID](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) .</span><span class="sxs-lookup"><span data-stu-id="192ad-342">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="192ad-343">Mongo zpracovává převod z `string` na `ObjectId` .</span><span class="sxs-lookup"><span data-stu-id="192ad-343">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="192ad-344">`BookName`Vlastnost je označena [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) atributem.</span><span class="sxs-lookup"><span data-stu-id="192ad-344">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="192ad-345">Hodnota atributu `Name` představuje název vlastnosti v kolekci MongoDB.</span><span class="sxs-lookup"><span data-stu-id="192ad-345">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="192ad-346">Přidat konfigurační model</span><span class="sxs-lookup"><span data-stu-id="192ad-346">Add a configuration model</span></span>

1. <span data-ttu-id="192ad-347">Do souboru *appSettings. JSON*přidejte následující hodnoty konfigurace databáze:</span><span class="sxs-lookup"><span data-stu-id="192ad-347">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="192ad-348">Přidejte soubor *BookstoreDatabaseSettings.cs* do adresáře *Models* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="192ad-348">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="192ad-349">Předchozí `BookstoreDatabaseSettings` Třída se používá k uložení hodnot vlastností souboru *appSettings. JSON* `BookstoreDatabaseSettings` .</span><span class="sxs-lookup"><span data-stu-id="192ad-349">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="192ad-350">Názvy vlastností JSON a C# jsou pojmenovány stejně, aby bylo možné zjednodušit proces mapování.</span><span class="sxs-lookup"><span data-stu-id="192ad-350">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="192ad-351">Přidejte následující zvýrazněný kód do `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="192ad-351">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="192ad-352">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="192ad-352">In the preceding code:</span></span>

   * <span data-ttu-id="192ad-353">Instance konfigurace, na kterou se váže sekce souboru *appSettings. JSON* , `BookstoreDatabaseSettings` se registruje v kontejneru injektáže (di).</span><span class="sxs-lookup"><span data-stu-id="192ad-353">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="192ad-354">Například `BookstoreDatabaseSettings` `ConnectionString` vlastnost objektu je naplněna `BookstoreDatabaseSettings:ConnectionString` vlastností v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="192ad-354">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="192ad-355">`IBookstoreDatabaseSettings`Rozhraní je zaregistrované v di s [životností služby](xref:fundamentals/dependency-injection#service-lifetimes)typu singleton.</span><span class="sxs-lookup"><span data-stu-id="192ad-355">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="192ad-356">Při vložení se instance rozhraní překládá na `BookstoreDatabaseSettings` objekt.</span><span class="sxs-lookup"><span data-stu-id="192ad-356">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="192ad-357">Pro vyřešení odkazů a přidejte následující kód na začátek *Startup.cs* `BookstoreDatabaseSettings` `IBookstoreDatabaseSettings` :</span><span class="sxs-lookup"><span data-stu-id="192ad-357">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="192ad-358">Přidání služby operace CRUD</span><span class="sxs-lookup"><span data-stu-id="192ad-358">Add a CRUD operations service</span></span>

1. <span data-ttu-id="192ad-359">Přidejte adresář *služeb* do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="192ad-359">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="192ad-360">`BookService`Do adresáře *služby* přidejte třídu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="192ad-360">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="192ad-361">V předchozím kódu `IBookstoreDatabaseSettings` je instance načtena z di přes injektáže konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="192ad-361">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="192ad-362">Tento postup poskytuje přístup k hodnotám konfigurace *appSettings. JSON* , které byly přidány do oddílu [přidat konfigurační model](#add-a-configuration-model) .</span><span class="sxs-lookup"><span data-stu-id="192ad-362">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="192ad-363">Přidejte následující zvýrazněný kód do `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="192ad-363">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="192ad-364">V předchozím kódu `BookService` je třída registrována pomocí příkazu di k podpoře injektáže konstruktoru v rámci využívání tříd.</span><span class="sxs-lookup"><span data-stu-id="192ad-364">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="192ad-365">Doba životnosti služby singleton je nejvhodnější `BookService` , protože používá přímou závislost na `MongoClient` .</span><span class="sxs-lookup"><span data-stu-id="192ad-365">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="192ad-366">Podle oficiálních [zásad opětovného použití pro klienty Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use) `MongoClient` by se měla registrovat v di s životností služby typu singleton.</span><span class="sxs-lookup"><span data-stu-id="192ad-366">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="192ad-367">Přidejte následující kód na začátek *Startup.cs* k vyřešení `BookService` odkazu:</span><span class="sxs-lookup"><span data-stu-id="192ad-367">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="192ad-368">`BookService`Třída používá následující `MongoDB.Driver` členy k provádění operací CRUD proti databázi:</span><span class="sxs-lookup"><span data-stu-id="192ad-368">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="192ad-369">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): přečte instanci serveru pro provádění databázových operací.</span><span class="sxs-lookup"><span data-stu-id="192ad-369">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="192ad-370">K konstruktoru této třídy je poskytnutý připojovací řetězec MongoDB:</span><span class="sxs-lookup"><span data-stu-id="192ad-370">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="192ad-371">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): představuje databázi Mongo pro provádění operací.</span><span class="sxs-lookup"><span data-stu-id="192ad-371">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="192ad-372">V tomto kurzu se používá obecná metoda [GetCollection \<TDocument> (Collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) na rozhraní pro získání přístupu k datům v určité kolekci.</span><span class="sxs-lookup"><span data-stu-id="192ad-372">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="192ad-373">Provede operace CRUD proti kolekci po volání této metody.</span><span class="sxs-lookup"><span data-stu-id="192ad-373">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="192ad-374">Ve `GetCollection<TDocument>(collection)` volání metody:</span><span class="sxs-lookup"><span data-stu-id="192ad-374">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="192ad-375">`collection`představuje název kolekce.</span><span class="sxs-lookup"><span data-stu-id="192ad-375">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="192ad-376">`TDocument`představuje typ objektu CLR uložený v kolekci.</span><span class="sxs-lookup"><span data-stu-id="192ad-376">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="192ad-377">`GetCollection<TDocument>(collection)`Vrátí objekt [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) představující kolekci.</span><span class="sxs-lookup"><span data-stu-id="192ad-377">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="192ad-378">V tomto kurzu jsou v kolekci vyvolány následující metody:</span><span class="sxs-lookup"><span data-stu-id="192ad-378">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="192ad-379">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Odstraní jeden dokument, který odpovídá zadaným kritériím hledání.</span><span class="sxs-lookup"><span data-stu-id="192ad-379">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="192ad-380">[Find \<TDocument> ](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): vrátí všechny dokumenty v kolekci, které odpovídají zadaným kritériím hledání.</span><span class="sxs-lookup"><span data-stu-id="192ad-380">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="192ad-381">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Vloží zadaný objekt jako nový dokument v kolekci.</span><span class="sxs-lookup"><span data-stu-id="192ad-381">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="192ad-382">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): nahradí jediný dokument, který odpovídá zadaným kritériím hledání, dodanému objektu.</span><span class="sxs-lookup"><span data-stu-id="192ad-382">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="192ad-383">Přidání kontroleru</span><span class="sxs-lookup"><span data-stu-id="192ad-383">Add a controller</span></span>

<span data-ttu-id="192ad-384">Přidejte `BooksController` třídu do adresáře *Controllers* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="192ad-384">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="192ad-385">Předchozí kontroler webového rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="192ad-385">The preceding web API controller:</span></span>

* <span data-ttu-id="192ad-386">Používá `BookService` třídu k provádění operací CRUD.</span><span class="sxs-lookup"><span data-stu-id="192ad-386">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="192ad-387">Obsahuje metody akcí, které podporují požadavky HTTP GET, POST, PUT a DELETE.</span><span class="sxs-lookup"><span data-stu-id="192ad-387">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="192ad-388">Volání <xref:System.Web.Http.ApiController.CreatedAtRoute*> v `Create` metodě Action vrátí odpověď [http 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) .</span><span class="sxs-lookup"><span data-stu-id="192ad-388">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="192ad-389">Stavový kód 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="192ad-389">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="192ad-390">`CreatedAtRoute`Přidá také `Location` hlavičku do odpovědi.</span><span class="sxs-lookup"><span data-stu-id="192ad-390">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="192ad-391">`Location`Hlavička Určuje identifikátor URI nově vytvořené knihy.</span><span class="sxs-lookup"><span data-stu-id="192ad-391">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="192ad-392">Testování webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="192ad-392">Test the web API</span></span>

1. <span data-ttu-id="192ad-393">Sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="192ad-393">Build and run the app.</span></span>

1. <span data-ttu-id="192ad-394">Přejděte na `http://localhost:<port>/api/books` k otestování metody akce bez parametrů řadiče `Get` .</span><span class="sxs-lookup"><span data-stu-id="192ad-394">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="192ad-395">Zobrazí se následující odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="192ad-395">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="192ad-396">Přejděte na `http://localhost:<port>/api/books/{id here}` k otestování metody akce, která je přetížena řadičem `Get` .</span><span class="sxs-lookup"><span data-stu-id="192ad-396">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="192ad-397">Zobrazí se následující odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="192ad-397">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="192ad-398">Konfigurace možností serializace JSON</span><span class="sxs-lookup"><span data-stu-id="192ad-398">Configure JSON serialization options</span></span>

<span data-ttu-id="192ad-399">Existují dvě podrobnosti o tom, jak můžete změnit informace o odpovědích JSON vrácených v části [Test webového rozhraní API](#test-the-web-api) :</span><span class="sxs-lookup"><span data-stu-id="192ad-399">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="192ad-400">Názvy vlastností výchozí ve stylu CamelCase velká a malá písmena by se měly změnit tak, aby odpovídaly názvu vlastností objektu CLR na velká písmena Pascal.</span><span class="sxs-lookup"><span data-stu-id="192ad-400">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="192ad-401">`bookName`Vlastnost by měla být vrácena jako `Name` .</span><span class="sxs-lookup"><span data-stu-id="192ad-401">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="192ad-402">Chcete-li splnit předchozí požadavky, proveďte následující změny:</span><span class="sxs-lookup"><span data-stu-id="192ad-402">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="192ad-403">V `Startup.ConfigureServices` , řetězení následujícího zvýrazněného kódu k `AddMvc` volání metody:</span><span class="sxs-lookup"><span data-stu-id="192ad-403">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="192ad-404">V předchozí změně názvy vlastností v serializovaných odpovědích JSON webového rozhraní API odpovídají jejich odpovídajícím názvům vlastností v typu objektu CLR.</span><span class="sxs-lookup"><span data-stu-id="192ad-404">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="192ad-405">Například `Book` vlastnost třídy je `Author` serializována jako `Author` .</span><span class="sxs-lookup"><span data-stu-id="192ad-405">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="192ad-406">V části *Models/Book. cs*poznámku k `BookName` vlastnosti s následujícím [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) atributem:</span><span class="sxs-lookup"><span data-stu-id="192ad-406">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="192ad-407">`[JsonProperty]`Hodnota atributu `Name` představuje název vlastnosti v serializované odpovědi JSON webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="192ad-407">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="192ad-408">Přidejte následující kód na začátek *modelů/Book. cs* pro vyřešení `[JsonProperty]` odkazu na atribut:</span><span class="sxs-lookup"><span data-stu-id="192ad-408">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="192ad-409">Opakujte kroky definované v části [Test webového rozhraní API](#test-the-web-api) .</span><span class="sxs-lookup"><span data-stu-id="192ad-409">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="192ad-410">Všimněte si rozdílu v názvech vlastností JSON.</span><span class="sxs-lookup"><span data-stu-id="192ad-410">Notice the difference in JSON property names.</span></span>

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="192ad-411">Přidání podpory ověřování do webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="192ad-411">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a><span data-ttu-id="192ad-412">Další kroky</span><span class="sxs-lookup"><span data-stu-id="192ad-412">Next steps</span></span>

<span data-ttu-id="192ad-413">Další informace o vytváření ASP.NET Core webových rozhraní API najdete v následujících zdrojích informací:</span><span class="sxs-lookup"><span data-stu-id="192ad-413">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="192ad-414">YouTube verze tohoto článku</span><span class="sxs-lookup"><span data-stu-id="192ad-414">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>

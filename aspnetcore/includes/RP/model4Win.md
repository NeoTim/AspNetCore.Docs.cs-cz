---
ms.openlocfilehash: 7fb5e956264f3cc7c04c4023ce69a058e5d0c0ed
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58265366"
---
<a name="scaffold"></a>

### <a name="scaffold-the-movie-model"></a><span data-ttu-id="78216-101">Vygenerované uživatelské rozhraní Video modelu</span><span class="sxs-lookup"><span data-stu-id="78216-101">Scaffold the Movie model</span></span>

* <span data-ttu-id="78216-102">Spusťte následující příkaz z příkazového řádku (v adresáři projektu, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory):</span><span class="sxs-lookup"><span data-stu-id="78216-102">Run the following from the command line (in the project directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files):</span></span>

  ```console
  dotnet restore
  dotnet aspnet-codegenerator razorpage -m Movie -dc MovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

<span data-ttu-id="78216-103">Pokud dojde k chybě:</span><span class="sxs-lookup"><span data-stu-id="78216-103">If you get the error:</span></span>

  ```
  No executable found matching command "dotnet-aspnet-codegenerator"
  ```

<span data-ttu-id="78216-104">Předchozí chybu se stane, když jsou do nesprávného adresáře.</span><span class="sxs-lookup"><span data-stu-id="78216-104">The preceding error happens when you are in the wrong directory.</span></span> <span data-ttu-id="78216-105">Otevřete příkazové okno k adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory), a pak spusťte ve výstupu předchozího příkazu.</span><span class="sxs-lookup"><span data-stu-id="78216-105">Open a command shell to the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files), and then run the preceding command.</span></span>

<span data-ttu-id="78216-106">Pokud dojde k chybě:</span><span class="sxs-lookup"><span data-stu-id="78216-106">If you get the error:</span></span>

  ```
  The process cannot access the file
 'RazorPagesMovie/bin/Debug/netcoreapp2.0/RazorPagesMovie.dll'
  because it is being used by another process.
  ```

<span data-ttu-id="78216-107">Ukončení sady Visual Studio a spusťte příkaz znovu.</span><span class="sxs-lookup"><span data-stu-id="78216-107">Exit Visual Studio and run the command again.</span></span>

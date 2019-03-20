---
ms.openlocfilehash: 7fb5e956264f3cc7c04c4023ce69a058e5d0c0ed
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58265366"
---
<a name="scaffold"></a>

### <a name="scaffold-the-movie-model"></a>Vygenerované uživatelské rozhraní Video modelu

* Spusťte následující příkaz z příkazového řádku (v adresáři projektu, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory):

  ```console
  dotnet restore
  dotnet aspnet-codegenerator razorpage -m Movie -dc MovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

Pokud dojde k chybě:

  ```
  No executable found matching command "dotnet-aspnet-codegenerator"
  ```

Předchozí chybu se stane, když jsou do nesprávného adresáře. Otevřete příkazové okno k adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory), a pak spusťte ve výstupu předchozího příkazu.

Pokud dojde k chybě:

  ```
  The process cannot access the file
 'RazorPagesMovie/bin/Debug/netcoreapp2.0/RazorPagesMovie.dll'
  because it is being used by another process.
  ```

Ukončení sady Visual Studio a spusťte příkaz znovu.

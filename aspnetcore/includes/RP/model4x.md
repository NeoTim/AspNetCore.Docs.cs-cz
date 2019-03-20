---
ms.openlocfilehash: 9ac005f3381e02ad31c0b1b87f8d55a05cc0b6cb
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58265310"
---
<a name="scaffold"></a>

### <a name="scaffold-the-movie-model"></a>Vygenerované uživatelské rozhraní Video modelu

* Spusťte následující příkaz z příkazového řádku (v adresáři projektu, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory):

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc MovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

Pokud dojde k chybě:

  ```
  No executable found matching command "dotnet-aspnet-codegenerator"
  ```

Otevřete příkazové okno k adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).

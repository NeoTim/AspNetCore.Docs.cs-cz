---
title: Použití webového rozhraní API analyzátorů
author: pranavkm
description: Další informace o analyzátorech webové rozhraní API v Microsoft.AspNetCore.Mvc.Api.Analyzers.
monikerRange: '>= aspnetcore-2.2'
ms.author: prkrishn
ms.custom: mvc
ms.date: 12/14/2018
uid: web-api/advanced/analyzers
ms.openlocfilehash: 2aaef738ab2a64f85cb85708f63d2375c04cacb5
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538570"
---
# <a name="use-web-api-analyzers"></a>Použití webového rozhraní API analyzátorů

ASP.NET Core 2.2 a novější obsahuje [Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) balíček NuGet obsahující analyzátory pro webová rozhraní API. Analyzátory práci s řadiči opatřen poznámkou <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>, při vytváření [API konvence](xref:web-api/advanced/conventions).

## <a name="package-installation"></a>Instalace balíčku

`Microsoft.AspNetCore.Mvc.Api.Analyzers` lze přidat pomocí jednoho z následujících postupů:

### <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Z **Konzola správce balíčků** okno:
  * Přejděte na **zobrazení** > **jiných Windows** > **Konzola správce balíčků**.
  * Přejděte do adresáře, ve kterém *ApiConventions.csproj* soubor existuje.
  * Spusťte následující příkaz:

    ```powershell
    Install-Package Microsoft.AspNetCore.Mvc.Api.Analyzers
    ```

* Z **spravovat balíčky NuGet** dialogové okno:
  * Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** > **spravovat balíčky NuGet**.
  * Nastavte **zdroj balíčku** do "nuget.org".
  * Do vyhledávacího pole zadejte "Microsoft.AspNetCore.Mvc.Api.Analyzers".
  * Vyberte balíček "Microsoft.AspNetCore.Mvc.Api.Analyzers" z **Procházet** kartě a klikněte na tlačítko **nainstalovat**.

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Klikněte pravým tlačítkem myši *balíčky* složky v **oblasti řešení** > **přidat balíčky...** .
* Nastavte **přidat balíčky** okna **zdroj** rozevíracího seznamu "nuget.org".
* Do vyhledávacího pole zadejte "Microsoft.AspNetCore.Mvc.Api.Analyzers".
* Vyberte v podokně výsledků "Microsoft.AspNetCore.Mvc.Api.Analyzers" balíček a klikněte na tlačítko **přidat balíček**.

### <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Spuštěním následujícího příkazu z **integrovaný terminál**:

```console
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

### <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Spusťte následující příkaz:

```console
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

---

## <a name="analyzers-for-api-conventions"></a>Analyzátory pro vytváření rozhraní API

OpenAPI dokumenty obsahují stavové kódy a typů odpovědi, které můžou vrátit akci. V ASP.NET Core MVC, atributy, jako <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> a <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> se používají k dokumentu akci. <xref:tutorials/web-api-help-pages-using-swagger> obsahuje další podrobnosti o dokumentaci rozhraní API.

Jeden z analyzátory v balíčku zkontroluje řadiče opatřen poznámkou <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> a identifikuje akce, které není úplně dokumentu jejich odpovědi. Vezměte v úvahu v následujícím příkladu:

[!code-csharp[](conventions/sample/Controllers/ContactsController.cs?name=missing404docs&highlight=9)]

Předchozí akci dokumenty HTTP 200 úspěch návratový typ, ale ne dokumentu stavový kód chyby HTTP 404. Analyzátor ohlásí chybí dokumentaci stavový kód HTTP 404, jako varování. Je k dispozici možnost tento problém vyřešit.

![Analyzátor reporting upozornění](conventions/_static/Analyzer.gif)

## <a name="additional-resources"></a>Další zdroje

* <xref:web-api/advanced/conventions>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:web-api/index>

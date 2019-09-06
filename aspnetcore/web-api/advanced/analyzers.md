---
title: Použití analyzátorů webového rozhraní API
author: pranavkm
description: Přečtěte si o balíčku ASP.NET Core MVC Web API Analyzer.
monikerRange: '>= aspnetcore-2.2'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/05/2019
uid: web-api/advanced/analyzers
ms.openlocfilehash: 1568eb0304a58758caa5f82249dc42872f5c36b9
ms.sourcegitcommit: 116bfaeab72122fa7d586cdb2e5b8f456a2dc92a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70384872"
---
# <a name="use-web-api-analyzers"></a>Použití analyzátorů webového rozhraní API

ASP.NET Core 2,2 a novější poskytuje balíček pro analyzátory MVC, který je určený pro použití s projekty webového rozhraní API. Analyzátory při sestavování <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> [konvencí webového rozhraní API](xref:web-api/advanced/conventions)pracují s řadiči, které jsou v systému poznámy.

Balíček analyzátory vás upozorní na jakoukoli akci kontroleru, která:

* Vrátí nedeklarovaný stavový kód.
* Vrátí nedeklarovaný výsledek úspěch.
* Dokumentuje stavový kód, který se nevrátí.
* Zahrnuje kontrolu explicitního ověření modelu.

::: moniker range=">= aspnetcore-3.0"

## <a name="reference-the-analyzer-package"></a>Odkaz na balíček analyzátoru

V ASP.NET Core 3,0 nebo novějších jsou analyzátory zahrnuté v .NET Core SDK. Chcete-li povolit analyzátor v projektu, zahrňte `IncludeOpenAPIAnalyzers` vlastnost do souboru projektu:

```xml
<PropertyGroup>
 <IncludeOpenAPIAnalyzers>true</IncludeOpenAPIAnalyzers>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

## <a name="package-installation"></a>Instalace balíčku

Nainstalujte balíček NuGet [Microsoft. AspNetCore. Mvc. API. analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) s jedním z následujících přístupů:

### <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

V okně **konzoly Správce balíčků** :
  * Přejít k **zobrazení** > **jiné** > **konzoly Správce balíčků**Windows.
  * Přejděte do adresáře, ve kterém existuje soubor *ApiConventions. csproj* .
  * Spusťte následující příkaz:

    ```powershell
    Install-Package Microsoft.AspNetCore.Mvc.Api.Analyzers
    ```

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Klikněte pravým tlačítkem na složku *balíčky* v **oblast řešení** > **Přidat balíčky...** .
* Nastavte rozevírací seznam pro **zdroj** okna **Přidat balíčky** na hodnotu "NuGet.org".
* Do vyhledávacího pole zadejte Microsoft. AspNetCore. Mvc. API. Analyzer.
* V podokně výsledků vyberte balíček Microsoft. AspNetCore. Mvc. API. Analyzer a klikněte na **Přidat balíček**.

### <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Z **integrovaného terminálu**spusťte následující příkaz:

```console
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

### <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Spusťte následující příkaz:

```console
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

---

::: moniker-end

## <a name="analyzers-for-web-api-conventions"></a>Analyzátory pro konvence webového rozhraní API

Dokumenty OpenAPI obsahují stavové kódy a typy odezvy, které může akce vracet. Ve ASP.NET Core MVC jsou atributy jako <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> a <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> použity k dokumentaci akce. <xref:tutorials/web-api-help-pages-using-swagger>přejdete k podrobnostem o dokumentaci k webovému rozhraní API.

Jeden z analyzátorů v balíčku kontroluje řadiče s <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> poznámkami a identifikuje akce, které neobsahují odpovědi. Vezměte v úvahu v následujícím příkladu:

[!code-csharp[](conventions/sample/Controllers/ContactsController.cs?name=missing404docs&highlight=10)]

Předchozí akce dokumentuje návratový typ úspěšného protokolu HTTP 200, ale nedokumentuje stavový kód chyby HTTP 404. Analyzátor nahlásí chybějící dokumentaci pro stavový kód HTTP 404 jako upozornění. K dispozici je možnost, jak problém vyřešit.

![vyhlášení výstrahy analyzátorem](conventions/_static/Analyzer.gif)

## <a name="additional-resources"></a>Další zdroje

* <xref:web-api/advanced/conventions>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:web-api/index>

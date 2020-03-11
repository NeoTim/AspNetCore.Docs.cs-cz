---
title: Použití analyzátorů webového rozhraní API
author: pranavkm
description: Přečtěte si o balíčku ASP.NET Core MVC Web API Analyzer.
monikerRange: '>= aspnetcore-2.2'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/05/2019
uid: web-api/advanced/analyzers
ms.openlocfilehash: 7b6a7328deb8718a2a1c67c104cec359a4f13497
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78661915"
---
# <a name="use-web-api-analyzers"></a>Použití analyzátorů webového rozhraní API

ASP.NET Core 2,2 a novější poskytuje balíček pro analyzátory MVC, který je určený pro použití s projekty webového rozhraní API. Analyzátory při vytváření [konvencí webového rozhraní API](xref:web-api/advanced/conventions)pracují s řadiči poznámkou s <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>.

Balíček analyzátory vás upozorní na jakoukoli akci kontroleru, která:

* Vrátí nedeklarovaný stavový kód.
* Vrátí nedeklarovaný výsledek úspěch.
* Dokumentuje stavový kód, který se nevrátí.
* Zahrnuje kontrolu explicitního ověření modelu.

::: moniker range=">= aspnetcore-3.0"

## <a name="reference-the-analyzer-package"></a>Odkaz na balíček analyzátoru

V ASP.NET Core 3,0 nebo novějších jsou analyzátory zahrnuté v .NET Core SDK. Chcete-li povolit analyzátor v projektu, zahrňte do souboru projektu vlastnost `IncludeOpenAPIAnalyzers`:

```xml
<PropertyGroup>
 <IncludeOpenAPIAnalyzers>true</IncludeOpenAPIAnalyzers>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

## <a name="package-installation"></a>Instalace balíčku

Nainstalujte balíček NuGet [Microsoft. AspNetCore. Mvc. API. analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) s jedním z následujících přístupů:

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

V okně **konzoly Správce balíčků** :
  * Přejít na **zobrazení** > **Další** > **konzoly Správce balíčků**Windows.
  * Přejděte do adresáře, ve kterém existuje soubor *ApiConventions. csproj* .
  * Spusťte následující příkaz:

    ```powershell
    Install-Package Microsoft.AspNetCore.Mvc.Api.Analyzers
    ```

### <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Klikněte pravým tlačítkem na složku *balíčky* v **oblast řešení** > **Přidat balíčky...** .
* Nastavte rozevírací seznam pro **zdroj** okna **Přidat balíčky** na hodnotu "NuGet.org".
* Do vyhledávacího pole zadejte Microsoft. AspNetCore. Mvc. API. Analyzer.
* V podokně výsledků vyberte balíček Microsoft. AspNetCore. Mvc. API. Analyzer a klikněte na **Přidat balíček**.

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Z **integrovaného terminálu**spusťte následující příkaz:

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

### <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Spusťte následující příkaz:

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

---

::: moniker-end

## <a name="analyzers-for-web-api-conventions"></a>Analyzátory pro konvence webového rozhraní API

Dokumenty OpenAPI obsahují stavové kódy a typy odezvy, které může akce vracet. V ASP.NET Core MVC se k dokumentování akce používají atributy jako <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> a <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>. <xref:tutorials/web-api-help-pages-using-swagger> se podrobněji doprovází do dokumentu webového rozhraní API.

Jeden z analyzátorů v balíčku kontroluje řadiče s poznámkou <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> a identifikuje akce, které neobsahují své odpovědi zcela. Vezměte v úvahu v následujícím příkladu:

[!code-csharp[](conventions/sample/Controllers/ContactsController.cs?name=missing404docs&highlight=10)]

Předchozí akce dokumentuje návratový typ úspěšného protokolu HTTP 200, ale nedokumentuje stavový kód chyby HTTP 404. Analyzátor nahlásí chybějící dokumentaci pro stavový kód HTTP 404 jako upozornění. K dispozici je možnost, jak problém vyřešit.

![vyhlášení výstrahy analyzátorem](conventions/_static/Analyzer.gif)

## <a name="additional-resources"></a>Další zdroje

* <xref:web-api/advanced/conventions>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:web-api/index>

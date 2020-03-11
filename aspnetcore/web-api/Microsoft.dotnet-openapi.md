---
title: Vývoj aplikací pro ASP.NET Core pomocí OpenAPI
author: ryanbrandenburg
description: Ukazuje, jak pomocí nástroje Microsoft. dotnet-openapi přidat odkazy na soubory OpenAPI.
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: 079e36511b63c186ffa7726bdb1e3c3bcbda9d34
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655265"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a>Vývoj aplikací pro ASP.NET Core pomocí nástrojů pro OpenAPI

Služba Ryan Brandenburg

[Microsoft. dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) je [globální nástroj .NET Core](/dotnet/core/tools/global-tools) pro správu odkazů [openapi](https://github.com/OAI/OpenAPI-Specification) v rámci projektu.

## <a name="installation"></a>Instalace

Pokud chcete nainstalovat `Microsoft.dotnet-openapi`, spusťte následující příkaz:

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a>Přidat

Přidání odkazu OpenAPI pomocí kteréhokoli z příkazů na této stránce přidá prvek `<OpenApiReference />` podobný následujícímu souboru *. csproj* :

```xml
<OpenApiReference Include="openapi.json" />
```

Předchozí odkaz je vyžadován, aby aplikace volala generovaný kód klienta.

<!-- TODO: Restore after https://github.com/dotnet/AspNetCore/issues/12738
### Add Project

#### Options

| Short option | Long option | Description | Example |
|-------|------|-------|---------|
| -p|--project | The project to operate on. |dotnet openapi add project *--project .\Ref.csproj* ../Ref/ProjRef.csproj |

#### Arguments

|  Argument  | Description | Example |
|-------------|-------------|---------|
| source-file | The source to create a reference from. Must be a project file. |dotnet openapi add project *../Ref/ProjRef.csproj* | -->

### <a name="add-file"></a>Přidat soubor

#### <a name="options"></a>Možnosti

| Krátká možnost| Možnost Long| Popis | Příklad |
|-------|------|-------|---------|
| -p|--updateProject | Projekt, na kterém má být provozován. |dotnet openapi přidat soubor *--updateProject .\Ref.csproj* .\OpenAPI.JSON |
| -c|--generátor kódu| Generátor kódu, který se má použít na odkaz Možnosti jsou `NSwagCSharp` a `NSwagTypeScript`. Pokud `--code-generator` není zadán, výchozí nastavení nástrojů `NSwagCSharp`.|dotnet openapi přidat soubor .\OpenApi.json--generátor kódu
| -h|--Help|Zobrazit informace o nápovědě|dotnet openapi přidat soubor--Help|

#### <a name="arguments"></a>Argumenty

|  Argument  | Popis | Příklad |
|-------------|-------------|---------|
| zdrojový soubor | Zdroj, ze kterého má být vytvořen odkaz. Musí se jednat o soubor OpenAPI. |dotnet openapi přidat soubor *.\OpenAPI.JSON* |

### <a name="add-url"></a>Přidat adresu URL

#### <a name="options"></a>Možnosti

| Krátká možnost| Možnost Long| Popis | Příklad |
|-------|------|-------------|---------|
| -p|--updateProject | Projekt, na kterém má být provozován. |dotnet openapi Add URL *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json` |
| -o|--výstupní soubor | Kam umístit místní kopii souboru OpenAPI. |dotnet openapi přidání adresy URL `https://contoso.com/openapi.json` *--Output-File MyClient. JSON* |
| -c|--generátor kódu| Generátor kódu, který se má použít na odkaz Možnosti jsou `NSwagCSharp` a `NSwagTypeScript`. |dotnet openapi přidat soubor .\OpenApi.json--generátor kódu
| -h|--Help|Zobrazit informace o nápovědě|dotnet openapi přidat URL – Help|

#### <a name="arguments"></a>Argumenty

|  Argument  | Popis | Příklad |
|-------------|-------------|---------|
| Adresa URL zdroje | Zdroj, ze kterého má být vytvořen odkaz. Musí se jednat o adresu URL. |dotnet openapi přidání adresy URL `https://contoso.com/openapi.json` |

## <a name="remove"></a>Odebrat

Odebere odkaz OpenAPI shodný se zadaným názvem souboru ze souboru *. csproj* . Po odebrání odkazu na OpenAPI se nebudou generovat klienti. Soubory Local *. JSON* a *. yaml* jsou odstraněny.

### <a name="options"></a>Možnosti

| Krátká možnost| Možnost Long| Popis| Příklad |
|-------|------|------------|---------|
| -p|--updateProject | Projekt, na kterém má být provozován. |dotnet openapi Remove *--updateProject .\Ref.csproj* .\OpenAPI.JSON |
| -h|--Help|Zobrazit informace o nápovědě|dotnet openapi Remove--Help|

### <a name="arguments"></a>Argumenty

|  Argument  | Popis| Příklad |
| ------------|------------|---------|
| zdrojový soubor | Zdroj, na který se má odebrat odkaz |dotnet openapi odebrat *.\OpenAPI.JSON* |

## <a name="refresh"></a>Obnovení

Aktualizuje místní verzi souboru, který byl stažen pomocí nejnovějšího obsahu z adresy URL pro stahování.

### <a name="options"></a>Možnosti

| Krátká možnost| Možnost Long| Popis | Příklad |
|-------|------|-------------|---------|
| -p|--updateProject | Projekt, na kterém má být provozován. | dotnet openapi Refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json` |
| -h|--Help|Zobrazit informace o nápovědě|dotnet openapi Refresh--Help|

### <a name="arguments"></a>Argumenty

|  Argument  | Popis | Příklad |
| ------------|-------------|---------|
| Adresa URL zdroje | Adresa URL, ze které se má aktualizovat odkaz | dotnet openapi Refresh `https://contoso.com/openapi.json` |

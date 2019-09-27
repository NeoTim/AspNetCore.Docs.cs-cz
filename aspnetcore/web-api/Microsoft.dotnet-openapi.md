---
title: Vývoj aplikací pro ASP.NET Core pomocí OpenAPI
author: ryanbrandenburg
description: Ukazuje, jak pomocí nástroje Microsoft. dotnet-openapi přidat odkazy na soubory OpenAPI.
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: f5eae9e871bc8efc30d500769adb845ff244a90c
ms.sourcegitcommit: e644258c95dd50a82284f107b9bf3becbc43b2b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71317776"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a>Vývoj aplikací pro ASP.NET Core pomocí nástrojů pro OpenAPI

Služba Ryan Brandenburg

[Microsoft. dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) je [globální nástroj .NET Core](/dotnet/core/tools/global-tools) pro správu odkazů [openapi](https://github.com/OAI/OpenAPI-Specification) v rámci projektu.

## <a name="installation"></a>Instalace

K instalaci `Microsoft.dotnet-openapi`spusťte následující příkaz:

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a>Přidat

Přidání odkazu openapi pomocí kteréhokoli z příkazů na této stránce přidá `<OpenApiReference />` do souboru *. csproj* prvek podobný následujícímu:

```xml
<OpenApiReference Include="openapi.json" />
```

Předchozí odkaz je vyžadován, aby aplikace volala generovaný kód klienta.

<!-- TODO: Restore after https://github.com/aspnet/AspNetCore/issues/12738
### Add Project

#### Options

| Short option | Long option | Description | Example |
|-------|------|-------|---------|
| -v|--verbose | Show verbose output. |dotnet openapi add project *-v* ../Ref/ProjRef.csproj |
| -p|--project | The project to operate on. |dotnet openapi add project *--project .\Ref.csproj* ../Ref/ProjRef.csproj |

#### Arguments

|  Argument  | Description | Example |
|-------------|-------------|---------|
| source-file | The source to create a reference from. Must be a project file. |dotnet openapi add project *../Ref/ProjRef.csproj* | -->

### <a name="add-file"></a>Přidat soubor

#### <a name="options"></a>Možnosti

| Krátká možnost| Možnost Long| Popis | Příklad |
|-------|------|-------|---------|
| -v|--verbose | Zobrazit podrobný výstup. |dotnet openapi přidat soubor *-v* .\OpenAPI.JSON |
| -p|--updateProject | Projekt, na kterém má být provozován. |dotnet openapi přidat soubor *--updateProject .\Ref.csproj* .\OpenAPI.JSON |
| -c|--generátor kódu| Generátor kódu, který se má použít na odkaz Možnosti jsou `NSwagCSharp` a `NSwagTypeScript`. Pokud `--code-generator` parametr není zadán, je `NSwagCSharp`výchozím nastavením nástrojů.|dotnet openapi přidat soubor .\OpenApi.json--generátor kódu
| -h|--Help|Zobrazit informace o nápovědě|dotnet openapi přidat soubor--Help|

#### <a name="arguments"></a>Argumenty

|  Argument  | Popis | Příklad |
|-------------|-------------|---------|
| zdrojový soubor | Zdroj, ze kterého má být vytvořen odkaz. Musí se jednat o soubor OpenAPI. |dotnet openapi přidat soubor *.\OpenAPI.JSON* |

### <a name="add-url"></a>Přidat adresu URL

#### <a name="options"></a>Možnosti

| Krátká možnost| Možnost Long| Popis | Příklad |
|-------|------|-------------|---------|
| -v|--verbose | Zobrazit podrobný výstup. |dotnet openapi přidat URL *-v*`https://contoso.com/openapi.json` |
| -p|--updateProject | Projekt, na kterém má být provozován. |dotnet openapi Add URL *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json` |
| -o|--výstupní soubor | Kam umístit místní kopii souboru OpenAPI. |dotnet openapi Add URL `https://contoso.com/openapi.json` *--Output-File MyClient. JSON* |
| -c|--generátor kódu| Generátor kódu, který se má použít na odkaz Možnosti jsou `NSwagCSharp` a `NSwagTypeScript`. |dotnet openapi přidat soubor .\OpenApi.json--generátor kódu
| -h|--Help|Zobrazit informace o nápovědě|dotnet openapi přidat URL – Help|

#### <a name="arguments"></a>Argumenty

|  Argument  | Popis | Příklad |
|-------------|-------------|---------|
| Adresa URL zdroje | Zdroj, ze kterého má být vytvořen odkaz. Musí se jednat o adresu URL. |dotnet openapi přidat adresu URL`https://contoso.com/openapi.json` |

## <a name="remove"></a>odebrat

Odebere odkaz OpenAPI shodný se zadaným názvem souboru ze souboru *. csproj* . Po odebrání odkazu na OpenAPI se nebudou generovat klienti. Soubory Local *. JSON* a *. yaml* jsou odstraněny.

### <a name="options"></a>Možnosti

| Krátká možnost| Možnost Long| Popis| Příklad |
|-------|------|------------|---------|
| -v|--verbose | Zobrazit podrobný výstup. |dotnet openapi Remove *-v*|
| -p|--updateProject | Projekt, na kterém má být provozován. |dotnet openapi Remove *--updateProject .\Ref.csproj* .\OpenAPI.JSON |
| -h|--Help|Zobrazit informace o nápovědě|dotnet openapi Remove--Help|

### <a name="arguments"></a>Argumenty

|  Argument  | Popis| Příklad |
| ------------|------------|---------|
| zdrojový soubor | Zdroj, na který se má odebrat odkaz |dotnet openapi odebrat *.\OpenAPI.JSON* |

## <a name="refresh"></a>Aktualizovat

Aktualizuje místní verzi souboru, který byl stažen pomocí nejnovějšího obsahu z adresy URL pro stahování.

### <a name="options"></a>Možnosti

| Krátká možnost| Možnost Long| Popis | Příklad |
|-------|------|-------------|---------|
| -v|--verbose | Zobrazit podrobný výstup. | dotnet openapi Refresh *-v*`https://contoso.com/openapi.json` |
| -p|--updateProject | Projekt, na kterém má být provozován. | dotnet openapi Refresh *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json` |
| -h|--Help|Zobrazit informace o nápovědě|dotnet openapi Refresh--Help|

### <a name="arguments"></a>Argumenty

|  Argument  | Popis | Příklad |
| ------------|-------------|---------|
| Adresa URL zdroje | Adresa URL, ze které se má aktualizovat odkaz | dotnet – aktualizace openapi`https://contoso.com/openapi.json` |

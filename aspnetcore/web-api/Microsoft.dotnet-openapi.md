---
title: Vývoj aplikací pro ASP.NET Core pomocí OpenAPI
author: ryanbrandenburg
description: Ukazuje, jak pomocí nástroje Microsoft. dotnet-openapi přidat odkazy na soubory OpenAPI.
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: 45921deb35452876b0a92a8731da68539a880c1d
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626555"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a>Vývoj aplikací pro ASP.NET Core pomocí nástrojů pro OpenAPI

Služba Ryan Brandenburg

[Microsoft. dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) je [globální nástroj .NET Core](/dotnet/core/tools/global-tools) pro správu odkazů [openapi](https://github.com/OAI/OpenAPI-Specification) v rámci projektu.

## <a name="installation"></a>Instalace

K instalaci `Microsoft.dotnet-openapi` Spusťte následující příkaz:

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a>Přidat

Přidání odkazu OpenAPI pomocí kteréhokoli z příkazů na této stránce přidá do `<OpenApiReference />` souboru *. csproj* prvek podobný následujícímu:

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
| -p|--updateProject | Projekt, na kterém má být provozován. |dotnet openapi přidat soubor *--updateProject .\Ref.csproj* .\OpenAPI.json |
| -c|--generátor kódu| Generátor kódu, který se má použít na odkaz Možnosti jsou `NSwagCSharp` a `NSwagTypeScript` . Pokud `--code-generator` parametr není zadán, je výchozím nastavením nástrojů `NSwagCSharp` .|dotnet openapi přidání souboru .\OpenApi.jsdo generátoru kódu
| -h|--Help|Zobrazit informace o nápovědě|dotnet openapi přidat soubor--Help|

#### <a name="arguments"></a>Arguments

|  Argument  | Popis | Příklad |
|-------------|-------------|---------|
| zdrojový soubor | Zdroj, ze kterého má být vytvořen odkaz. Musí se jednat o soubor OpenAPI. |dotnet openapi přidat soubor *.\OpenAPI.jsna* |

### <a name="add-url"></a>Přidat adresu URL

#### <a name="options"></a>Možnosti

| Krátká možnost| Možnost Long| Popis | Příklad |
|-------|------|-------------|---------|
| -p|--updateProject | Projekt, na kterém má být provozován. |dotnet openapi Add URL *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json` |
| -o|--výstupní soubor | Kam umístit místní kopii souboru OpenAPI. |dotnet openapi přidat URL `https://contoso.com/openapi.json` *--Output-File myclient.json* |
| -c|--generátor kódu| Generátor kódu, který se má použít na odkaz Možnosti jsou `NSwagCSharp` a `NSwagTypeScript` . |dotnet openapi přidání souboru .\OpenApi.jsdo generátoru kódu
| -h|--Help|Zobrazit informace o nápovědě|dotnet openapi přidat URL – Help|

#### <a name="arguments"></a>Arguments

|  Argument  | Popis | Příklad |
|-------------|-------------|---------|
| Adresa URL zdroje | Zdroj, ze kterého má být vytvořen odkaz. Musí se jednat o adresu URL. |dotnet openapi přidat adresu URL `https://contoso.com/openapi.json` |

## <a name="remove"></a>Odebrat

Odebere odkaz OpenAPI shodný se zadaným názvem souboru ze souboru *. csproj* . Po odebrání odkazu na OpenAPI se nebudou generovat klienti. Soubory Local *. JSON* a *. yaml* jsou odstraněny.

### <a name="options"></a>Možnosti

| Krátká možnost| Možnost Long| Popis| Příklad |
|-------|------|------------|---------|
| -p|--updateProject | Projekt, na kterém má být provozován. |dotnet openapi Remove *--updateProject .\Ref.csproj* .\OpenAPI.json |
| -h|--Help|Zobrazit informace o nápovědě|dotnet openapi Remove--Help|

### <a name="arguments"></a>Arguments

|  Argument  | Popis| Příklad |
| ------------|------------|---------|
| zdrojový soubor | Zdroj, na který se má odebrat odkaz |dotnet openapi odebrat *.\OpenAPI.js* |

## <a name="refresh"></a>Aktualizovat

Aktualizuje místní verzi souboru, který byl stažen pomocí nejnovějšího obsahu z adresy URL pro stahování.

### <a name="options"></a>Možnosti

| Krátká možnost| Možnost Long| Popis | Příklad |
|-------|------|-------------|---------|
| -p|--updateProject | Projekt, na kterém má být provozován. | dotnet openapi Refresh *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json` |
| -h|--Help|Zobrazit informace o nápovědě|dotnet openapi Refresh--Help|

### <a name="arguments"></a>Arguments

|  Argument  | Popis | Příklad |
| ------------|-------------|---------|
| Adresa URL zdroje | Adresa URL, ze které se má aktualizovat odkaz | dotnet – aktualizace openapi `https://contoso.com/openapi.json` |

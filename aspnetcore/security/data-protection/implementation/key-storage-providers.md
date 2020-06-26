---
title: Zprostředkovatelé úložiště klíčů v ASP.NET Core
author: rick-anderson
description: Přečtěte si o poskytovatelích úložiště klíčů v ASP.NET Core a o tom, jak nakonfigurovat umístění úložiště klíčů.
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: 1bbea6f16d57d5cc107c95293e2788271bfce601
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408042"
---
# <a name="key-storage-providers-in-aspnet-core"></a>Zprostředkovatelé úložiště klíčů v ASP.NET Core

Systém ochrany dat [ve výchozím nastavení využívá mechanismus zjišťování](xref:security/data-protection/configuration/default-settings) , aby určil, kde by měly být zachovány kryptografické klíče. Vývojář může přepsat výchozí mechanismus zjišťování a ručně zadat umístění.

> [!WARNING]
> Pokud zadáte explicitní umístění trvalosti klíčů, systém ochrany dat zruší registraci výchozího šifrování klíče v mechanismu REST, takže klíče už nebudou zašifrované v klidovém stavu. Pro produkční nasazení doporučujeme zadat také [explicitní šifrovací mechanismus klíčů](xref:security/data-protection/implementation/key-encryption-at-rest) .

## <a name="file-system"></a>Systém souborů

Chcete-li nakonfigurovat úložiště klíčů založené na systému souborů, zavolejte konfigurační rutinu [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) , jak je znázorněno níže. Zadejte příkaz [DirectoryInfo](/dotnet/api/system.io.directoryinfo) ukazující na úložiště, kde se mají ukládat klíče:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

`DirectoryInfo`Může odkazovat na adresář v místním počítači nebo může ukazovat na složku ve sdílené síťové složce. Pokud odkazujete na adresář v místním počítači (a scénář je, že pouze aplikace na místním počítači vyžadují přístup k používání tohoto úložiště), zvažte použití rozhraní [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (ve Windows) k šifrování neaktivních klíčů. V opačném případě zvažte použití [certifikátu X. 509](xref:security/data-protection/implementation/key-encryption-at-rest) k šifrování neaktivních klíčů.

## <a name="azure-storage"></a>Azure Storage

Balíček [Microsoft. AspNetCore. DataProtection. AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) umožňuje ukládat klíče ochrany dat v Azure Blob Storage. Klíče lze sdílet mezi několika instancemi webové aplikace. Aplikace můžou sdílet soubory cookie ověřování nebo CSRF ochranu napříč několika servery.

Pokud chcete nakonfigurovat poskytovatele služby Azure Blob Storage, zavolejte jedno z přetížení [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

Pokud je webová aplikace spuštěná jako služba Azure, ověřovací tokeny se dají automaticky vytvořit pomocí [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/).

```csharp
var tokenProvider = new AzureServiceTokenProvider();
var token = await tokenProvider.GetAccessTokenAsync("https://storage.azure.com/");
var credentials = new StorageCredentials(new TokenCredential(token));
var storageAccount = new CloudStorageAccount(credentials, "mystorageaccount", "core.windows.net", useHttps: true);
var client = storageAccount.CreateCloudBlobClient();
var container = client.GetContainerReference("my-key-container");

// optional - provision the container automatically
await container.CreateIfNotExistsAsync();

services.AddDataProtection()
    .PersistKeysToAzureBlobStorage(container, "keys.xml");
```

Přečtěte si [Další podrobnosti o konfiguraci ověřování služba-služba.](/azure/key-vault/service-to-service-authentication)

## <a name="redis"></a>Redis

::: moniker range=">= aspnetcore-2.2"

Balíček [Microsoft. AspNetCore. DataProtection. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) umožňuje ukládat klíče ochrany dat do mezipaměti Redis. Klíče lze sdílet mezi několika instancemi webové aplikace. Aplikace můžou sdílet soubory cookie ověřování nebo CSRF ochranu napříč několika servery.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Balíček [Microsoft. AspNetCore. DataProtection. Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) umožňuje ukládat klíče ochrany dat do mezipaměti Redis. Klíče lze sdílet mezi několika instancemi webové aplikace. Aplikace můžou sdílet soubory cookie ověřování nebo CSRF ochranu napříč několika servery.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Pro konfiguraci v Redis zavolejte jedno z přetížení [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToStackExchangeRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Pro konfiguraci v Redis zavolejte jedno z přetížení [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

Další informace najdete v následujících tématech:

* [StackExchange. Redis ConnectionMultiplexer](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
* [Azure Redis Cache](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [Ukázky ASP.NET Core DataProtection](https://github.com/dotnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a>Registr

**Platí jenom pro nasazení systému Windows.**

Někdy může aplikace mít k systému souborů přístup pro zápis. Vezměte v úvahu scénář, ve kterém je aplikace spuštěná jako účet virtuální služby (například identita fondu aplikací *w3wp.exe*). V těchto případech může správce zřídit klíč registru, který je přístupný pro identitu účtu služby. Zavolejte metodu rozšíření [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) , jak je znázorněno níže. Zadejte [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) ukazující na místo, kde se mají ukládat kryptografické klíče:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
}
```

> [!IMPORTANT]
> K šifrování neaktivních klíčů doporučujeme použít rozhraní [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) .

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a>Entity Framework Core

Balíček [Microsoft. AspNetCore. DataProtection. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) poskytuje mechanismus pro ukládání klíčů ochrany dat do databáze pomocí Entity Framework Core. `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore`Balíček NuGet se musí přidat do souboru projektu, není součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).

S tímto balíčkem je možné klíče sdílet mezi několika instancemi webové aplikace.

Chcete-li nakonfigurovat poskytovatele EF Core, zavolejte [metodu \<TContext> PersistKeysToDbContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) :

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-20)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Obecný parametr, `TContext` , musí dědit z [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) a implementovat [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

Vytvořte `DataProtectionKeys` tabulku.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

V okně **konzoly Správce balíčků** (PMC) spusťte následující příkazy:

```powershell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

V příkazovém prostředí spusťte následující příkazy:

```dotnetcli
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

`MyKeysContext`je `DbContext` definován v předchozí ukázce kódu. Pokud používáte `DbContext` s jiným názvem, nahraďte své `DbContext` jméno pro `MyKeysContext` .

`DataProtectionKeys`Třída nebo entita přijímá strukturu zobrazenou v následující tabulce.

| Vlastnost nebo pole | Typ CLR | Typ SQL              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | `int`, PK, `IDENTITY(1,1)` , not null   |
| `FriendlyName` | `string` | `nvarchar(MAX)`, null |
| `Xml`          | `string` | `nvarchar(MAX)`, null |

::: moniker-end

## <a name="custom-key-repository"></a>Vlastní úložiště klíčů

Pokud nejsou mechanismy v krabici vhodné, může vývojář určit vlastní mechanismus trvalosti klíčů tím, že poskytuje vlastní [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository).

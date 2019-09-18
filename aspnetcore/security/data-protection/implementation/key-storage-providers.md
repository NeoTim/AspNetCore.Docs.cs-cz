---
title: Zprostředkovatelé úložiště klíčů v ASP.NET Core
author: rick-anderson
description: Další informace o poskytovatele úložiště klíčů v ASP.NET Core a jak konfigurovat umístění úložiště klíčů.
ms.author: riande
ms.date: 06/11/2019
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: d5d15779d89a2d746ca2165abab2840232ae0128
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082045"
---
# <a name="key-storage-providers-in-aspnet-core"></a>Zprostředkovatelé úložiště klíčů v ASP.NET Core

Systém ochrany dat [využívá mechanismus zjišťování ve výchozím nastavení](xref:security/data-protection/configuration/default-settings) k určení, kde by měl trvalé kryptografických klíčů. Vývojář můžete přepsat výchozí mechanismus pro zjišťování a ručně zadejte jeho umístění.

> [!WARNING]
> Pokud chcete zadat umístění služby explicitní trvalost klíče, systém ochrany dat deregisters výchozí šifrování klíčů v rest mechanismus, takže klíče jsou už v klidovém stavu zašifrovaná. Doporučujeme vám dále [zadejte mechanismus explicitní šifrování klíče](xref:security/data-protection/implementation/key-encryption-at-rest) pro nasazení v produkčním prostředí.

## <a name="file-system"></a>Systém souborů

Chcete-li konfigurovat klíče úložiště založené na systému souborů, zavolejte [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) konfigurace rutiny, jak je znázorněno níže. Zadejte [DirectoryInfo](/dotnet/api/system.io.directoryinfo) bude odkazovat na úložiště ukládat klíče:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

`DirectoryInfo` Může odkazovat na adresáře na místním počítači, nebo můžete přejít do složky ve sdílené síťové složce. Pokud přejdete do adresáře v místním počítači (a tento scénář je, že jenom aplikace v místním počítači vyžaduje přístup k použití tohoto úložiště), zvažte použití [rozhraní Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (on Windows) k šifrování klíčů v klidovém stavu. V opačném případě zvažte použití [certifikát X.509](xref:security/data-protection/implementation/key-encryption-at-rest) šifrování klíčů v klidovém stavu.

## <a name="azure-storage"></a>Azure Storage

Balíček [Microsoft. AspNetCore. DataProtection. AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) umožňuje ukládat klíče ochrany dat v Azure Blob Storage. Klíče mohou být sdíleny napříč několika instancemi webové aplikace. Aplikace můžete sdílet soubory cookie pro ověřování nebo CSRF ochrany napříč několika servery.

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

Balíček [Microsoft. AspNetCore. DataProtection. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) umožňuje ukládat klíče ochrany dat do mezipaměti Redis. Klíče mohou být sdíleny napříč několika instancemi webové aplikace. Aplikace můžete sdílet soubory cookie pro ověřování nebo CSRF ochrany napříč několika servery.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Balíček [Microsoft. AspNetCore. DataProtection. Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) umožňuje ukládat klíče ochrany dat do mezipaměti Redis. Klíče mohou být sdíleny napříč několika instancemi webové aplikace. Aplikace můžete sdílet soubory cookie pro ověřování nebo CSRF ochrany napříč několika servery.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Ke konfiguraci na Redis zavoláním jednoho z [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) přetížení:

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

Ke konfiguraci na Redis zavoláním jednoho z [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) přetížení:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

Další informace naleznete v následujících tématech:

* [StackExchange.Redis ConnectionMultiplexer](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
* [Azure Redis Cache](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [Ukázky ASPNET/DataProtection](https://github.com/aspnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a>Registru

**Platí jenom pro nasazení Windows.**

Aplikace v některých případech nemusí mít oprávnění k zápisu do systému souborů. Představte si třeba situaci, ve kterém je aplikace spuštěna jako účet virtuální služby (například *w3wp.exe*pro identitu fondu aplikací). V těchto případech můžete zřizovat správce klíč registru, který je přístupný pro identitu účtu služby. Volání [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) rozšiřující metoda, jak je znázorněno níže. Zadejte [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) odkazující na umístění pro uložení kryptografických klíčů:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
}
```

> [!IMPORTANT]
> Doporučujeme používat [rozhraní Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) šifrování klíčů v klidovém stavu.

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a>Entity Framework Core

[Microsoft.AspNetCore.DataProtection.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) balíček poskytuje mechanismus pro ukládání klíčů ochrany dat k databázi pomocí Entity Framework Core. `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` Balíček NuGet musí být přidán do souboru projektu není součástí [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).

S tímto balíčkem klíče mohou být sdíleny napříč několika instancemi webové aplikace.

Chcete-li nakonfigurovat poskytovatele EF Core, zavolejte [ `PersistKeysToDbContext<TContext>` ](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) metody:

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-15)]

Obecný parametr, `TContext`, musí dědit z [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) a implementovat [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

`DataProtectionKeys` Vytvořte tabulku.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

V okně **konzoly Správce balíčků** (PMC) spusťte následující příkazy:

```PowerShell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

V příkazovém prostředí spusťte následující příkazy:

```dotnetcli
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

`MyKeysContext``DbContext` je definován v předchozí ukázce kódu. Pokud používáte `DbContext` s jiným názvem, nahraďte své `DbContext` jméno pro `MyKeysContext`.

`DataProtectionKeys` Třída nebo entita přijímá strukturu zobrazenou v následující tabulce.

| Vlastnost nebo pole | Typ CLR | Typ SQL              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | `int`, PK, not null   |
| `FriendlyName` | `string` | `nvarchar(MAX)`, null |
| `Xml`          | `string` | `nvarchar(MAX)`, null |

::: moniker-end

## <a name="custom-key-repository"></a>Vlastní klíče úložiště

Pokud nejsou integrované mechanismy vhodné, Vývojář můžete zadat vlastní mechanismus trvalost klíče poskytnutím vlastní [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository).

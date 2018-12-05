---
title: Moduly IIS s ASP.NET Core
author: guardrex
description: Zjistěte aktivní i neaktivní moduly IIS pro aplikace ASP.NET Core a jak spravovat moduly služby IIS.
ms.author: riande
ms.custom: mvc
ms.date: 11/30/2018
uid: host-and-deploy/iis/modules
ms.openlocfilehash: c6a6cc9b6b3410267c6f5034f824648a1ebbe10f
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52862236"
---
# <a name="iis-modules-with-aspnet-core"></a>Moduly IIS s ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex)

Některé z nativních modulů IIS a všechny moduly služby IIS spravované nejsou schopná zpracovat žádosti o aplikace ASP.NET Core. V mnoha případech ASP.NET Core nabízí alternativu ke scénářům zákazníky a vyřešené ve službě IIS nativní a spravované moduly.

## <a name="native-modules"></a>Nativní moduly

Tabulka udává nativní moduly služby IIS, které fungují s aplikací ASP.NET Core a že modul ASP.NET Core.

| Modul | Funkční aplikace ASP.NET Core | Možnost ASP.NET Core |
| --- | :---: | --- |
| **Anonymní ověřování**<br>`AnonymousAuthenticationModule`                                  | Ano | |
| **Základní ověřování**<br>`BasicAuthenticationModule`                                          | Ano | |
| **Ověřování pomocí mapování klientských certifikace**<br>`CertificateMappingAuthenticationModule`      | Ano | |
| **CGI**<br>`CgiModule`                                                                           | Ne  | |
| **Ověření konfigurace**<br>`ConfigurationValidationModule`                                  | Ano | |
| **Chyby protokolu HTTP**<br>`CustomErrorModule`                                                           | Ne  | [Middleware stránky stavový kód](xref:fundamentals/error-handling#configure-status-code-pages) |
| **Vlastní protokolování**<br>`CustomLoggingModule`                                                      | Ano | |
| **Výchozí dokument**<br>`DefaultDocumentModule`                                                  | Ne  | [Výchozí soubory middlewaru](xref:fundamentals/static-files#serve-a-default-document) |
| **Ověřování algoritmem Digest**<br>`DigestAuthenticationModule`                                        | Ano | |
| **Procházení adresářů**<br>`DirectoryListingModule`                                               | Ne  | [Middleware pro procházení adresáře](xref:fundamentals/static-files#enable-directory-browsing) |
| **Dynamické komprese**<br>`DynamicCompressionModule`                                            | Ano | [Middleware pro kompresi odpovědí](xref:performance/response-compression) |
| **Trasování**<br>`FailedRequestsTracingModule`                                                     | Ano | [Protokolování ASP.NET Core](xref:fundamentals/logging/index#tracesource-provider) |
| **Ukládání souborů do mezipaměti**<br>`FileCacheModule`                                                            | Ne  | [Middleware pro ukládání odpovědí do mezipaměti](xref:performance/caching/middleware) |
| **Ukládání do mezipaměti pomocí protokolu HTTP**<br>`HttpCacheModule`                                                            | Ne  | [Middleware pro ukládání odpovědí do mezipaměti](xref:performance/caching/middleware) |
| **Protokolování HTTP**<br>`HttpLoggingModule`                                                          | Ano | [Protokolování ASP.NET Core](xref:fundamentals/logging/index) |
| **Přesměrování protokolu HTTP**<br>`HttpRedirectionModule`                                                  | Ano | [Middleware pro přepis adres URL](xref:fundamentals/url-rewriting) |
| **Ověřování pomocí mapování klientských certifikátů služby IIS**<br>`IISCertificateMappingAuthenticationModule` | Ano | |
| **Omezení domény a IP**<br>`IpRestrictionModule`                                          | Ano | |
| **Filtry ISAPI**<br>`IsapiFilterModule`                                                         | Ano | [Middleware](xref:fundamentals/middleware/index) |
| **ROZHRANÍ ISAPI**<br>`IsapiModule`                                                                       | Ano | [Middleware](xref:fundamentals/middleware/index) |
| **Podpora protokolu**<br>`ProtocolSupportModule`                                                  | Ano | |
| **Filtrování žádostí**<br>`RequestFilteringModule`                                                | Ano | [Middleware pro přepis adres URL `IRule`](xref:fundamentals/url-rewriting#irule-based-rule) |
| **Monitorování žádostí**<br>`RequestMonitorModule`                                                    | Ano | |
| **Přepisování adres URL**&#8224;<br>`RewriteModule`                                                      | Ano | [Middleware pro přepis adres URL](xref:fundamentals/url-rewriting) |
| **Začlenění na straně serveru**<br>`ServerSideIncludeModule`                                            | Ne  | |
| **Statická komprese**<br>`StaticCompressionModule`                                              | Ne  | [Middleware pro kompresi odpovědí](xref:performance/response-compression) |
| **Statický obsah**<br>`StaticFileModule`                                                         | Ne  | [Middleware se statickými soubory](xref:fundamentals/static-files) |
| **Ukládání tokenu do mezipaměti**<br>`TokenCacheModule`                                                          | Ano | |
| **Ukládání do mezipaměti identifikátorů URI**<br>`UriCacheModule`                                                              | Ano | |
| **Autorizace adres URL**<br>`UrlAuthorizationModule`                                                | Ano | [ASP.NET Core Identity](xref:security/authentication/identity) |
| **Ověřování Windows**<br>`WindowsAuthenticationModule`                                      | Ano | |

&#8224;Modul přepisování adres URL na `isFile` a `isDirectory` shoda typy nefunguje s aplikací ASP.NET Core z důvodu změn v [adresářovou strukturu](xref:host-and-deploy/directory-structure).

## <a name="managed-modules"></a>Spravované moduly

Spravované moduly jsou *není* díky hostované aplikace ASP.NET Core, pokud je fond aplikací .NET CLR verze nastavena na **bez spravovaného kódu**. ASP.NET Core nabízí alternativy middlewaru v několika případech.

| Modul                  | Možnost ASP.NET Core |
| ----------------------- | ------------------- |
| AnonymousIdentification | |
| DefaultAuthentication   | |
| FileAuthorization       | |
| Ověřování pomocí formulářů     | [Middleware ověřování souborů cookie](xref:security/authentication/cookie) |
| outputCache             | [Middleware pro ukládání odpovědí do mezipaměti](xref:performance/caching/middleware) |
| Profil                 | |
| RoleManager             | |
| ScriptModule 4.0        | |
| Relace                 | [Relace middlewaru](xref:fundamentals/app-state) |
| UrlAuthorization        | |
| UrlMappingsModule       | [Middleware pro přepis adres URL](xref:fundamentals/url-rewriting) |
| UrlRoutingModule 4.0    | [ASP.NET Core Identity](xref:security/authentication/identity) |
| WindowsAuthentication   | |

## <a name="iis-manager-application-changes"></a>Změny aplikace Správce služby IIS

Při použití Správce služby IIS ke konfiguraci nastavení, *web.config* změny souboru aplikace. Pokud nasazení aplikace a včetně *web.config*, všechny změny provedené pomocí Správce služby IIS jsou přepsány podle nasazených *web.config* souboru. Když se změní na server *web.config* soubor, zkopíruje aktualizovaná *web.config* souboru na serveru pro místní projekt okamžitě.

## <a name="disabling-iis-modules"></a>Zakázání moduly služby IIS

Pokud modul IIS je nakonfigurována na úrovni serveru, který musí být zakázáno pro aplikace, doplněk aplikace *web.config* souboru můžete zakázat v modulu. Nechte modul na místě a deaktivovat pomocí nastavení konfigurace (Pokud je k dispozici) nebo příslušný modul odeberete z aplikace.

### <a name="module-deactivation"></a>Modul deaktivaci

Mnoho modulů nabízejí nastavení konfigurace, která umožňuje jejich zakázat bez nutnosti odebírání modulu z aplikace. Toto je nejjednodušší a nejrychlejší způsob, jak deaktivovat modulu. Například můžete zakázat modulu Přesměrování protokolu HTTP s `<httpRedirect>` prvek *web.config*:

```xml
<configuration>
  <system.webServer>
    <httpRedirect enabled="false" />
  </system.webServer>
</configuration>
```

Další informace o zakázání modulů s nastavením konfigurace, použijte odkazy v *podřízené prvky* část [IIS \<system.webServer >](/iis/configuration/system.webServer/).

### <a name="module-removal"></a>Odebrání modulu

Pokud se vyžadují pro odebrání modulu s nastavením v *web.config*odemknout modulu a odemknutí `<modules>` část *web.config* první:

1. Odemknete modul na úrovni serveru. Server služby IIS vyberte ve Správci služby IIS **připojení** bočním panelu. Otevřít **moduly** v **IIS** oblasti. V seznamu vyberte modul. V **akce** bočního panelu na pravé straně vyberte **odemknout**. Odemknout libovolný počet modulů, jak máte v úmyslu odebrat z *web.config* později.

2. Nasazení aplikace bez `<modules>` tématu *web.config*. Pokud je aplikace nasazená s *web.config* obsahující `<modules>` část bez nutnosti odemknout části nejprve v Správce služby IIS, Configuration Manager dojde k výjimce při pokusu o odemknutí části. Proto nasadit aplikaci bez `<modules>` oddílu.

3. Odemknout `<modules>` část *web.config*. V **připojení** bočním panelu vyberte web v **lokality**. V **správu** oblasti, otevřete **Editor konfigurace**. Použít ovládací prvky navigace a vyberte `system.webServer/modules` oddílu. V **akce** bočního panelu na pravé straně vyberte **odemknout** části.

4. V tomto okamžiku `<modules>` části mohou být přidány do *web.config* soubor s `<remove>` element příslušný modul odeberete z aplikace. Více `<remove>` elementy lze přidat více modulů odebrat. Pokud *web.config* změn na serveru, okamžitě provést stejné změny do projektu *web.config* soubor místně. Odebrání modulu díky tomu nebude mít vliv na používání modulu s jinými aplikacemi na serveru.

   ```xml
   <configuration>
    <system.webServer>
      <modules>
        <remove name="MODULE_NAME" />
      </modules>
    </system.webServer>
   </configuration>
   ```

Modul služby IIS může být odebrán také s *Appcmd.exe*. Zadejte `MODULE_NAME` a `APPLICATION_NAME` v příkazu:

```console
Appcmd.exe delete module MODULE_NAME /app.name:APPLICATION_NAME
```

Například odebrat `DynamicCompressionModule` z výchozí webový server:

```console
%windir%\system32\inetsrv\appcmd.exe delete module DynamicCompressionModule /app.name:"Default Web Site"
```

## <a name="minimum-module-configuration"></a>Minimální konfigurací modulů

Pouze moduly, které jsou potřebné ke spuštění aplikace ASP.NET Core je modul anonymní ověřování a že modul ASP.NET Core.

Modul ukládání do mezipaměti identifikátorů URI (`UriCacheModule`) umožňuje konfiguraci webu mezipaměti na úrovni adresy URL služby IIS. Bez tohoto modulu IIS čtení a analyzovat konfiguraci u každého požadavku, i v případě, že stejná adresa URL je požadováno opakovaně. Analýza konfigurace snížení výkonu výsledkem každého požadavku. *I když modul ukládání do mezipaměti identifikátorů URI není bezpodmínečně nutné pro spouštění hostované aplikace ASP.NET Core, doporučujeme, aby modul ukládání do mezipaměti identifikátorů URI bylo povoleno pro všechna nasazení v ASP.NET Core.*

Modul HTTP ukládání do mezipaměti (`HttpCacheModule`) implementuje výstupní mezipaměť služby IIS a také logiku pro ukládání do mezipaměti položky v mezipaměti HTTP.sys. Bez tohoto modulu obsah je již ukládá do mezipaměti v režimu jádra a profily mezipaměti jsou ignorovány. Odebrání modulu HTTP ukládání do mezipaměti obvykle má nepříznivý vliv na výkon a využití prostředků. *I když modul ukládání do mezipaměti HTTP není bezpodmínečně nutné pro spouštění hostované aplikace ASP.NET Core, doporučujeme, aby modul ukládání do mezipaměti HTTP povolit pro všechna nasazení ASP.NET Core.*

## <a name="additional-resources"></a>Další zdroje

* <xref:host-and-deploy/iis/index>
* [Úvod do architektury služby IIS: moduly ve službě IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#modules-in-iis)
* [Přehled moduly služby IIS](/iis/get-started/introduction-to-iis/iis-modules-overview)
* [Vlastní nastavení služby IIS 7.0 role a moduly](https://technet.microsoft.com/library/cc627313.aspx)
* [SLUŽBA IIS `<system.webServer>`](/iis/configuration/system.webServer/)

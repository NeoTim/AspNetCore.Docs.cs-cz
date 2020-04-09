---
title: Moduly IIS s ASP.NET jádrem
author: rick-anderson
description: Objevte aktivní a neaktivní moduly služby IIS pro ASP.NET aplikace Core a způsob správy modulů služby IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
uid: host-and-deploy/iis/modules
ms.openlocfilehash: 0f13ef3eb1da03960ef1fa54d33532b6ebbdc128
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657904"
---
# <a name="iis-modules-with-aspnet-core"></a>Moduly IIS s ASP.NET jádrem

Některé nativní moduly služby IIS a všechny moduly spravované službou IIS nejsou schopny zpracovávat požadavky na ASP.NET základní aplikace. V mnoha případech ASP.NET Core nabízí alternativu ke scénářům, které řeší nativní a spravované moduly iis.

## <a name="native-modules"></a>Nativní moduly

V tabulce jsou uvedeny nativní moduly služby IIS, které jsou funkční s aplikacemi ASP.NET Core a ASP.NET Core Module.

| Modul | Funkční s aplikacemi ASP.NET Core | ASP.NET základní možnost |
| --- | :---: | --- |
| **Anonymní ověřování**<br>`AnonymousAuthenticationModule`                                  | Ano | |
| **Základní ověření**<br>`BasicAuthenticationModule`                                          | Ano | |
| **Ověřování mapování certifikace klientů**<br>`CertificateMappingAuthenticationModule`      | Ano | |
| **CGI**<br>`CgiModule`                                                                           | Ne  | |
| **Ověření konfigurace**<br>`ConfigurationValidationModule`                                  | Ano | |
| **Chyby protokolu HTTP**<br>`CustomErrorModule`                                                           | Ne  | [Middleware stránky stavu](xref:fundamentals/error-handling#usestatuscodepages) |
| **Vlastní protokolování**<br>`CustomLoggingModule`                                                      | Ano | |
| **Výchozí dokument**<br>`DefaultDocumentModule`                                                  | Ne  | [Výchozí soubor middlewar](xref:fundamentals/static-files#serve-a-default-document) |
| **Ověřování hodnotou hash**<br>`DigestAuthenticationModule`                                        | Ano | |
| **Procházení adresářů**<br>`DirectoryListingModule`                                               | Ne  | [Middleware pro procházení adresářů](xref:fundamentals/static-files#enable-directory-browsing) |
| **Dynamická komprese**<br>`DynamicCompressionModule`                                            | Ano | [Middleware komprese odpovědí](xref:performance/response-compression) |
| **Trasování neúspěšných požadavků**<br>`FailedRequestsTracingModule`                                     | Ano | [ASP.NET základní protokolování](xref:fundamentals/logging/index#tracesource-provider) |
| **Ukládání souborů do mezipaměti**<br>`FileCacheModule`                                                            | Ne  | [Middleware pro ukládání odpovědí do mezipaměti](xref:performance/caching/middleware) |
| **Ukládání do mezipaměti HTTP**<br>`HttpCacheModule`                                                            | Ne  | [Middleware pro ukládání odpovědí do mezipaměti](xref:performance/caching/middleware) |
| **Protokolování HTTP**<br>`HttpLoggingModule`                                                          | Ano | [ASP.NET základní protokolování](xref:fundamentals/logging/index) |
| **Přesměrování protokolu HTTP**<br>`HttpRedirectionModule`                                                  | Ano | [Middleware pro přepis adres URL](xref:fundamentals/url-rewriting) |
| **Trasování HTTP**<br>`TracingModule`                                                              | Ano | |
| **Ověřování klientských certifikátů služby IIS**<br>`IISCertificateMappingAuthenticationModule` | Ano | |
| **Omezení IP adres a domény**<br>`IpRestrictionModule`                                          | Ano | |
| **Filtry ISAPI**<br>`IsapiFilterModule`                                                         | Ano | [Middleware](xref:fundamentals/middleware/index) |
| **Isapi**<br>`IsapiModule`                                                                       | Ano | [Middleware](xref:fundamentals/middleware/index) |
| **Podpora protokolu**<br>`ProtocolSupportModule`                                                  | Ano | |
| **Filtrování požadavků**<br>`RequestFilteringModule`                                                | Ano | [Přepisování middlewaru adres URL`IRule`](xref:fundamentals/url-rewriting#irule-based-rule) |
| **Sledování požadavků**<br>`RequestMonitorModule`                                                    | Ano | |
| **Přepisování adres URL**&#8224;<br>`RewriteModule`                                                      | Ano | [Middleware pro přepis adres URL](xref:fundamentals/url-rewriting) |
| **Zahrnuje na straně serveru**<br>`ServerSideIncludeModule`                                            | Ne  | |
| **Statická komprese**<br>`StaticCompressionModule`                                              | Ne  | [Middleware komprese odpovědí](xref:performance/response-compression) |
| **Statický obsah**<br>`StaticFileModule`                                                         | Ne  | [Middleware statického souboru](xref:fundamentals/static-files) |
| **Ukládání tokenů do mezipaměti**<br>`TokenCacheModule`                                                          | Ano | |
| **Ukládání do mezipaměti identifikátoru URI**<br>`UriCacheModule`                                                              | Ano | |
| **Autorizace adresy URL**<br>`UrlAuthorizationModule`                                                | Ano | [ASP.NET základní identity](xref:security/authentication/identity) |
| **Ověřování systému Windows**<br>`WindowsAuthenticationModule`                                      | Ano | |

&#8224;Typy `isFile` modulu přepisování `isDirectory` adres URL a shody nefungují s aplikacemi ASP.NET Core kvůli změnám ve [struktuře adresářů](xref:host-and-deploy/directory-structure).

## <a name="managed-modules"></a>Spravované moduly

Spravované moduly *nejsou* funkční s hostovanými aplikacemi ASP.NET Core, pokud je verze .NET CLR fondu aplikací nastavena na **žádný spravovaný kód**. ASP.NET Core nabízí middleware alternativy v několika případech.

| Modul                  | ASP.NET základní možnost |
| ----------------------- | ------------------- |
| Anonymousidentification | |
| Výchozí ověřování   | |
| Autorizace souboru       | |
| Ověřování formulářů     | [Middleware pro ověřování souborů cookie](xref:security/authentication/cookie) |
| Outputcache             | [Middleware pro ukládání odpovědí do mezipaměti](xref:performance/caching/middleware) |
| Profil                 | |
| Rolemanager             | |
| Modul skriptu-4.0        | |
| Relace                 | [Middleware relace](xref:fundamentals/app-state) |
| UrlAuthorization        | |
| UrlMappingsModul       | [Middleware pro přepis adres URL](xref:fundamentals/url-rewriting) |
| UrlRoutingModule-4.0    | [ASP.NET základní identity](xref:security/authentication/identity) |
| Ověřování systému Windows   | |

## <a name="iis-manager-application-changes"></a>Změny aplikací Správce služby IIS

Při konfiguraci nastavení pomocí Správce služby IIS se změní soubor *web.config* aplikace. Pokud nasazujete aplikaci a zahrnujete *web.config*, všechny změny provedené ve Správci služby IIS jsou přepsány nasazeným souborem *web.config.* Pokud jsou v souboru *web.config* serveru provedeny změny, zkopírujte okamžitě aktualizovaný soubor *web.config* na serveru do místního projektu.

## <a name="disabling-iis-modules"></a>Zakázání modulů iIS

Pokud je modul služby IIS nakonfigurován na úrovni serveru, který musí být pro aplikaci zakázán, může modul zakázat doplněk k souboru *web.config* aplikace. Buď nechte modul na místě a deaktivujte jej pomocí nastavení konfigurace (pokud je k dispozici), nebo modul z aplikace odeberte.

### <a name="module-deactivation"></a>Deaktivace modulu

Mnoho modulů nabízí nastavení konfigurace, které umožňuje jejich zakázání bez odebrání modulu z aplikace. Jedná se o nejjednodušší a nejrychlejší způsob deaktivace modulu. Modul přesměrování HTTP lze například zakázat `<httpRedirect>` pomocí prvku v *souboru web.config*:

```xml
<configuration>
  <system.webServer>
    <httpRedirect enabled="false" />
  </system.webServer>
</configuration>
```

Další informace o zakázání modulů s nastavením konfigurace naleznete v odkazech v části *Podřízené prvky* [ \<systému IIS system.webServer>](/iis/configuration/system.webServer/).

### <a name="module-removal"></a>Odebrání modulu

Pokud se rozhodnete odstranit modul s nastavením v *web.config* `<modules>` , odemknout modul a odemknout sekci *web.config* první:

1. Odemkněte modul na úrovni serveru. V postranním panelu **Připojení** správce služby IIS vyberte server Služby IIS. Otevřete **moduly** v oblasti **iis.** Vyberte modul v seznamu. V postranním panelu **Akce** vpravo vyberte **Odemknout**. Pokud se položka akce pro modul zobrazí jako **zámek**, modul je již odemčen a není vyžadována žádná akce. Odemkněte tolik modulů, kolik chcete později odebrat z *web.config.*

2. Nasazení aplikace bez `<modules>` oddílu v *web.config*. Pokud je aplikace nasazená s *web.config* obsahující `<modules>` oddíl bez odemknutí oddílu jako první ve Správci služby IIS, správce konfigurace vyvolá výjimku při pokusu o odemknutí oddílu. Proto nasadit aplikaci `<modules>` bez oddílu.

3. Odemkněte `<modules>` sekci *web.config*. Na postranním panelu **Připojení** vyberte web v **části Weby**. V oblasti **Správa** otevřete **Editor konfigurace**. Pomocí navigačních ovládacích `system.webServer/modules` prvků vyberte oddíl. V postranním panelu **Akce** vpravo vyberte **odemknutí** oddílu. Pokud se položka akce pro oddíl modulu zobrazí jako **Zamknout oddíl**, část modulu je již odemčena a není vyžadována žádná akce.

4. Přidejte `<modules>` oddíl do místního souboru *web.config* aplikace s `<remove>` prvkem, který modul z aplikace odebere. Přidejte `<remove>` více prvků pro odebrání více modulů. Pokud jsou na serveru provedeny změny *web.config,* okamžitě proveďte stejné změny v souboru *web.config* projektu místně. Odebrání modulu pomocí tohoto přístupu nemá vliv na použití modulu s jinými aplikacemi na serveru.

   ```xml
   <configuration>
    <system.webServer>
      <modules>
        <remove name="MODULE_NAME" />
      </modules>
    </system.webServer>
   </configuration>
   ```

Chcete-li přidat nebo odebrat moduly pro službu IIS Express pomocí *souboru web.config*, upravte *soubor applicationHost.config:* `<modules>`

1. Otevřete *soubor\\{APPLICATION ROOT} .vs\config\applicationhost.config*.

1. Vyhledejte `<section>` prvek pro moduly `overrideModeDefault` iis a změňte z `Deny` na `Allow`:

   ```xml
   <section name="modules"
            allowDefinition="MachineToApplication"
            overrideModeDefault="Allow" />
   ```

1. Vyhledejte část `<location path="" overrideMode="Allow"><system.webServer><modules>`. Pro všechny moduly, které chcete `lockItem` `true` odebrat, nastavte z na . `false` V následujícím příkladu je modul CGI odemčen:

   ```xml
   <add name="CgiModule" lockItem="false" />
   ```

1. Po `<modules>` odemknutí oddílu a jednotlivých modulů můžete přidat nebo odebrat moduly služby IIS pomocí souboru *web.config* aplikace pro spuštění aplikace ve službě IIS Express.

Modul iis lze také odebrat pomocí *programu Appcmd.exe*. Zadejte `MODULE_NAME` `APPLICATION_NAME` a v příkazu:

```console
Appcmd.exe delete module MODULE_NAME /app.name:APPLICATION_NAME
```

Můžete například `DynamicCompressionModule` odebrat výchozí web:

```console
%windir%\system32\inetsrv\appcmd.exe delete module DynamicCompressionModule /app.name:"Default Web Site"
```

## <a name="minimum-module-configuration"></a>Minimální konfigurace modulu

Jediné moduly potřebné ke spuštění aplikace ASP.NET Core jsou modul anonymního ověřování a ASP.NET core modul.

Modul ukládání do mezipaměti URI (`UriCacheModule`) umožňuje službu IIS ukládat do mezipaměti konfiguraci webu na úrovni adresy URL. Bez tohoto modulu musí služba IIS číst a analyzovat konfiguraci při každém požadavku, i když je opakovaně požadována stejná adresa URL. Analýza konfigurace každý požadavek má za následek významné snížení výkonu. *Přestože modul ukládání do mezipaměti URI není nezbytně nutné pro hostované aplikace ASP.NET Core ke spuštění, doporučujeme, aby byl povolen modul ukládání do mezipaměti URI pro všechna ASP.NET nasazení jádra.*

Modul ukládání do mezipaměti HTTP (`HttpCacheModule`) implementuje výstupní mezipaměť iis a také logiku pro ukládání položek do mezipaměti HTTP.sys. Bez tohoto modulu již není obsah ukládán do mezipaměti v režimu jádra a profily mezipaměti jsou ignorovány. Odebrání modulu ukládání do mezipaměti HTTP má obvykle nepříznivé účinky na výkon a využití prostředků. *Přestože modul ukládání do mezipaměti HTTP není nezbytně nutné pro hostované ASP.NET aplikace Core ke spuštění, doporučujeme, aby modul ukládání do mezipaměti HTTP být povolena pro všechny ASP.NET nasazení jádra.*

## <a name="additional-resources"></a>Další zdroje

* [Úvod do architektur IIS: Moduly ve SB](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#modules-in-iis)
* [Přehled modulů iIS](/iis/get-started/introduction-to-iis/iis-modules-overview)
* [Přizpůsobení rolí a modulů iIS 7.0](https://technet.microsoft.com/library/cc627313.aspx)
* [>\<serveru System.webServer služby IIS](/iis/configuration/system.webServer/)

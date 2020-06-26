---
title: Moduly IIS s ASP.NET Core
author: rick-anderson
description: Objevte aktivní a neaktivní moduly IIS pro aplikace ASP.NET Core a Správa modulů IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/modules
ms.openlocfilehash: 124f2a629ebd14210cb21351a720e007bba48f02
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404012"
---
# <a name="iis-modules-with-aspnet-core"></a>Moduly IIS s ASP.NET Core

Některé z nativních modulů služby IIS a všechny spravované moduly služby IIS nemohou zpracovávat požadavky na aplikace ASP.NET Core. V mnoha případech ASP.NET Core nabízí alternativu k scénářům řešeným nativními a spravovanými moduly služby IIS.

## <a name="native-modules"></a>Nativní moduly

Tato tabulka uvádí nativní moduly služby IIS, které jsou funkční s ASP.NET Core aplikacemi a modulem ASP.NET Core.

| Modul | Funkce ASP.NET Corech aplikací | Možnost ASP.NET Core |
| --- | :---: | --- |
| **Anonymní ověřování**<br>`AnonymousAuthenticationModule`                                  | Yes | |
| **Základní ověření**<br>`BasicAuthenticationModule`                                          | Yes | |
| **Ověřování mapování certifikace klientů**<br>`CertificateMappingAuthenticationModule`      | Yes | |
| **CGI**<br>`CgiModule`                                                                           | No  | |
| **Ověření konfigurace**<br>`ConfigurationValidationModule`                                  | Yes | |
| **Chyby protokolu HTTP**<br>`CustomErrorModule`                                                           | No  | [Middleware stránky stavového kódu](xref:fundamentals/error-handling#usestatuscodepages) |
| **Vlastní protokolování**<br>`CustomLoggingModule`                                                      | Yes | |
| **Výchozí dokument**<br>`DefaultDocumentModule`                                                  | No  | [Middleware výchozích souborů](xref:fundamentals/static-files#serve-a-default-document) |
| **Ověřování hodnotou hash**<br>`DigestAuthenticationModule`                                        | Yes | |
| **Procházení adresářů**<br>`DirectoryListingModule`                                               | No  | [Middleware procházení adresářů](xref:fundamentals/static-files#enable-directory-browsing) |
| **Dynamická komprese**<br>`DynamicCompressionModule`                                            | Yes | [Middleware pro kompresi odpovědí](xref:performance/response-compression) |
| **Trasování chybných žádostí**<br>`FailedRequestsTracingModule`                                     | Yes | [Protokolování ASP.NET Core](xref:fundamentals/logging/index#tracesource-provider) |
| **Ukládání souborů do mezipaměti**<br>`FileCacheModule`                                                            | No  | [Middleware pro ukládání odpovědí do mezipaměti](xref:performance/caching/middleware) |
| **Ukládání HTTP do mezipaměti**<br>`HttpCacheModule`                                                            | No  | [Middleware pro ukládání odpovědí do mezipaměti](xref:performance/caching/middleware) |
| **Protokolování HTTP**<br>`HttpLoggingModule`                                                          | Yes | [Protokolování ASP.NET Core](xref:fundamentals/logging/index) |
| **Přesměrování protokolu HTTP**<br>`HttpRedirectionModule`                                                  | Yes | [Middleware pro přepis adres URL](xref:fundamentals/url-rewriting) |
| **Trasování HTTP**<br>`TracingModule`                                                              | Yes | |
| **Ověřování mapování klientských certifikátů služby IIS**<br>`IISCertificateMappingAuthenticationModule` | Yes | |
| **Omezení podle IP adresy a domény**<br>`IpRestrictionModule`                                          | Yes | |
| **Filtry ISAPI**<br>`IsapiFilterModule`                                                         | Yes | [Middleware](xref:fundamentals/middleware/index) |
| **ISAPI**<br>`IsapiModule`                                                                       | Yes | [Middleware](xref:fundamentals/middleware/index) |
| **Podpora protokolů**<br>`ProtocolSupportModule`                                                  | Yes | |
| **Filtrování požadavků**<br>`RequestFilteringModule`                                                | Yes | [Middleware pro přepis adres URL`IRule`](xref:fundamentals/url-rewriting#irule-based-rule) |
| **Sledování požadavků**<br>`RequestMonitorModule`                                                    | Yes | |
| &#8224; **přepsání adresy URL**<br>`RewriteModule`                                                      | Yes | [Middleware pro přepis adres URL](xref:fundamentals/url-rewriting) |
| **Začlenění na straně serveru**<br>`ServerSideIncludeModule`                                            | No  | |
| **Statická komprese**<br>`StaticCompressionModule`                                              | No  | [Middleware pro kompresi odpovědí](xref:performance/response-compression) |
| **Statický obsah**<br>`StaticFileModule`                                                         | No  | [Middleware statických souborů](xref:fundamentals/static-files) |
| **Ukládání tokenů do mezipaměti**<br>`TokenCacheModule`                                                          | Yes | |
| **Ukládání identifikátorů URI do mezipaměti**<br>`UriCacheModule`                                                              | Yes | |
| **Autorizace adres URL**<br>`UrlAuthorizationModule`                                                | Yes | [ASP.NET CoreIdentity](xref:security/authentication/identity) |
| **Ověřování systému Windows**<br>`WindowsAuthenticationModule`                                      | Yes | |

&#8224;typy a porovnávání v rámci adresy `isFile` URL `isDirectory` nefungují s ASP.NET Core aplikacemi z důvodu změn ve [struktuře adresářové](xref:host-and-deploy/directory-structure)služby.

## <a name="managed-modules"></a>Spravované moduly

Spravované moduly nejsou *funkční u* hostovaných ASP.NET Core aplikací, pokud je verze .NET CLR fondu aplikací nastavená na **žádný spravovaný kód**. ASP.NET Core nabízí alternativy middlewaru v několika případech.

| Modul                  | Možnost ASP.NET Core |
| ----------------------- | ------------------- |
| AnonymousIdentification | |
| DefaultAuthentication   | |
| Autorace       | |
| FormsAuthentication     | [Middleware pro ověřování souborů cookie](xref:security/authentication/cookie) |
| OutputCache             | [Middleware pro ukládání odpovědí do mezipaměti](xref:performance/caching/middleware) |
| Profil                 | |
| RoleManager             | |
| ScriptModule-4,0        | |
| Relace                 | [Middleware relace](xref:fundamentals/app-state) |
| UrlAuthorization        | |
| UrlMappingsModule       | [Middleware pro přepis adres URL](xref:fundamentals/url-rewriting) |
| UrlRoutingModule-4,0    | [ASP.NET CoreIdentity](xref:security/authentication/identity) |
| WindowsAuthentication   | |

## <a name="iis-manager-application-changes"></a>Změny aplikace Správce služby IIS

Při použití Správce služby IIS ke konfiguraci nastavení se změní *web.config* soubor aplikace. Pokud nasazujete aplikaci a zahrnete *web.config*, všechny změny provedené pomocí Správce služby IIS se přepíší nasazeným *web.configm* souborem. Pokud jsou provedeny změny v souboru *web.config* serveru, Zkopírujte aktualizovaný soubor *web.config* na server do místního projektu hned.

## <a name="disabling-iis-modules"></a>Zakázání modulů IIS

Pokud je modul IIS nakonfigurovaný na úrovni serveru, která musí být pro aplikaci zakázaná, může se tento modul zakázat přidáním souboru *web.config* aplikace. Ponechte modul na místě a deaktivujte ho pomocí nastavení konfigurace (Pokud je dostupné) nebo odeberte modul z aplikace.

### <a name="module-deactivation"></a>Deaktivace modulu

Mnoho modulů nabízí konfigurační nastavení, které umožňuje jejich zakázání bez odebrání modulu z aplikace. Toto je nejjednodušší a nejrychlejší způsob, jak deaktivovat modul. Například modul Přesměrování HTTP lze zakázat s `<httpRedirect>` prvkem v *web.config*:

```xml
<configuration>
  <system.webServer>
    <httpRedirect enabled="false" />
  </system.webServer>
</configuration>
```

Další informace o zakázání modulů s nastavením konfigurace najdete v tématu odkazy v části *podřízené prvky* [služby IIS \<system.webServer> ](/iis/configuration/system.webServer/).

### <a name="module-removal"></a>Odebrání modulu

Pokud se rozhodnete odebrat modul s nastavením v *web.config*, odemkněte modul a `<modules>` nejdříve nejprve odemkněte oddíl *web.config* :

1. Odemkněte modul na úrovni serveru. Vyberte server IIS na postranním panelu **připojení** Správce služby IIS. Otevřete **moduly** v oblasti **IIS** . Vyberte modul v seznamu. Na bočním panelu **Akce** na pravé straně vyberte **odemknout**. Pokud se položka akce pro modul zobrazí jako **Zámek**, modul už je odemčený a nevyžaduje se žádná akce. Odemkněte tolik modulů, než se chystáte odebrat z *web.config* později.

2. Nasaďte aplikaci bez `<modules>` části v *web.config*. Pokud je aplikace nasazená s *web.config* obsahující `<modules>` oddíl, aniž byste nejdřív odemkli oddíl ve Správci služby IIS, Configuration Manager při pokusu o odemknutí této části vyvolá výjimku. Proto aplikaci nasaďte bez `<modules>` části.

3. Odemkněte `<modules>` část *web.config*. Na bočním panelu **připojení** vyberte web na **webech**. V oblasti **Správa** otevřete **Editor konfigurací**. K výběru oddílu použijte navigační ovládací prvky `system.webServer/modules` . Na bočním panelu **Akce** na pravé straně vyberte možnost **odemknout** oddíl. Pokud se položka akce pro modul zobrazuje jako **Zámek**, oddíl Module už je odemčený a nevyžaduje se žádná akce.

4. Pokud chcete `<modules>` modul z aplikace odebrat, přidejte do souboru místního *web.config* aplikace oddíl `<remove>` . Přidejte více `<remove>` prvků pro odebrání více modulů. Pokud jsou na serveru provedeny *web.config* změny, okamžitě proveďte stejné změny v souboru *web.config* projektu místně. Odebrání modulu s použitím tohoto přístupu neovlivní použití modulu s jinými aplikacemi na serveru.

   ```xml
   <configuration>
    <system.webServer>
      <modules>
        <remove name="MODULE_NAME" />
      </modules>
    </system.webServer>
   </configuration>
   ```

Chcete-li přidat nebo odebrat moduly pro IIS Express pomocí *web.config*, upravte *applicationHost.config* k odemknutí `<modules>` části:

1. Otevřete *aplikaci {root} \\.vs\config\applicationhost.config*.

1. Vyhledejte `<section>` element pro moduly IIS a změňte `overrideModeDefault` z `Deny` na `Allow` :

   ```xml
   <section name="modules"
            allowDefinition="MachineToApplication"
            overrideModeDefault="Allow" />
   ```

1. Vyhledejte část `<location path="" overrideMode="Allow"><system.webServer><modules>`. U všech modulů, které chcete odebrat, nastavte `lockItem` z `true` na `false` . V následujícím příkladu je modul CGI odemčený:

   ```xml
   <add name="CgiModule" lockItem="false" />
   ```

1. Po `<modules>` odemčení oddílu a jednotlivých modulů můžete přidat nebo odebrat moduly IIS pomocí *web.config* souboru aplikace pro spuštění aplikace v IIS Express.

Modul IIS se dá odebrat taky pomocí *Appcmd.exe*. Zadejte `MODULE_NAME` a `APPLICATION_NAME` v příkazu:

```console
Appcmd.exe delete module MODULE_NAME /app.name:APPLICATION_NAME
```

Odeberte například `DynamicCompressionModule` z výchozího webu:

```console
%windir%\system32\inetsrv\appcmd.exe delete module DynamicCompressionModule /app.name:"Default Web Site"
```

## <a name="minimum-module-configuration"></a>Minimální konfigurace modulu

Jediným modulem požadovaným pro spuštění aplikace ASP.NET Core jsou modul anonymního ověřování a modul ASP.NET Core.

Modul ukládání identifikátorů URI ( `UriCacheModule` ) umožňuje službě IIS ukládat do mezipaměti konfiguraci webu na úrovni URL. Bez tohoto modulu musí služba IIS číst a analyzovat konfiguraci každého požadavku, a to i v případě, že se stejná adresa URL opakovaně požaduje. Analýza konfigurace každé žádosti vede k výraznému snížení výkonu. *I když je modul pro ukládání identifikátorů URI do mezipaměti bezpodmínečně vyžadován pro spuštění hostované aplikace ASP.NET Core, doporučujeme, aby byl modul mezipaměti identifikátoru URI povolen pro všechna nasazení ASP.NET Core.*

Modul HTTP cache ( `HttpCacheModule` ) implementuje výstupní mezipaměť služby IIS a také logiku pro ukládání položek do mezipaměti HTTP.sys mezipaměti. Bez tohoto modulu už není obsah uložený v mezipaměti v režimu jádra a profily mezipaměti se ignorují. Odebrání modulu mezipaměti HTTP má obvykle negativní vliv na výkon a využití prostředků. *I když modul mezipaměti HTTP není nezbytně nutný pro spuštění hostované aplikace ASP.NET Core, doporučujeme, aby byl modul mezipaměti protokolu HTTP povolen pro všechna nasazení ASP.NET Core.*

## <a name="additional-resources"></a>Další zdroje

* [Seznámení s architekturami služby IIS: moduly ve službě IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#modules-in-iis)
* [Přehled modulů služby IIS](/iis/get-started/introduction-to-iis/iis-modules-overview)
* [Přizpůsobení rolí a modulů služby IIS 7,0](https://technet.microsoft.com/library/cc627313.aspx)
* [SLUŽBU\<system.webServer>](/iis/configuration/system.webServer/)

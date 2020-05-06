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
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/modules
ms.openlocfilehash: 7262b9ea18e4cf6acd278d087fcc44262f8f9c80
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775944"
---
# <a name="iis-modules-with-aspnet-core"></a>Moduly IIS s ASP.NET Core

Některé z nativních modulů služby IIS a všechny spravované moduly služby IIS nemohou zpracovávat požadavky na aplikace ASP.NET Core. V mnoha případech ASP.NET Core nabízí alternativu k scénářům řešeným nativními a spravovanými moduly služby IIS.

## <a name="native-modules"></a>Nativní moduly

Tato tabulka uvádí nativní moduly služby IIS, které jsou funkční s ASP.NET Core aplikacemi a modulem ASP.NET Core.

| Modul | Funkce ASP.NET Corech aplikací | Možnost ASP.NET Core |
| --- | :---: | --- |
| **Anonymní ověřování**<br>`AnonymousAuthenticationModule`                                  | Ano | |
| **Základní ověření**<br>`BasicAuthenticationModule`                                          | Ano | |
| **Ověřování mapování certifikace klientů**<br>`CertificateMappingAuthenticationModule`      | Ano | |
| **CGI**<br>`CgiModule`                                                                           | Ne  | |
| **Ověření konfigurace**<br>`ConfigurationValidationModule`                                  | Ano | |
| **Chyby protokolu HTTP**<br>`CustomErrorModule`                                                           | Ne  | [Middleware stránky stavového kódu](xref:fundamentals/error-handling#usestatuscodepages) |
| **Vlastní protokolování**<br>`CustomLoggingModule`                                                      | Ano | |
| **Výchozí dokument**<br>`DefaultDocumentModule`                                                  | Ne  | [Middleware výchozích souborů](xref:fundamentals/static-files#serve-a-default-document) |
| **Ověřování hodnotou hash**<br>`DigestAuthenticationModule`                                        | Ano | |
| **Procházení adresářů**<br>`DirectoryListingModule`                                               | Ne  | [Middleware procházení adresářů](xref:fundamentals/static-files#enable-directory-browsing) |
| **Dynamická komprese**<br>`DynamicCompressionModule`                                            | Ano | [Middleware pro kompresi odpovědí](xref:performance/response-compression) |
| **Trasování chybných žádostí**<br>`FailedRequestsTracingModule`                                     | Ano | [Protokolování ASP.NET Core](xref:fundamentals/logging/index#tracesource-provider) |
| **Ukládání souborů do mezipaměti**<br>`FileCacheModule`                                                            | Ne  | [Middleware pro ukládání odpovědí do mezipaměti](xref:performance/caching/middleware) |
| **Ukládání HTTP do mezipaměti**<br>`HttpCacheModule`                                                            | Ne  | [Middleware pro ukládání odpovědí do mezipaměti](xref:performance/caching/middleware) |
| **Protokolování HTTP**<br>`HttpLoggingModule`                                                          | Ano | [Protokolování ASP.NET Core](xref:fundamentals/logging/index) |
| **Přesměrování protokolu HTTP**<br>`HttpRedirectionModule`                                                  | Ano | [Middleware pro přepis adres URL](xref:fundamentals/url-rewriting) |
| **Trasování HTTP**<br>`TracingModule`                                                              | Ano | |
| **Ověřování mapování klientských certifikátů služby IIS**<br>`IISCertificateMappingAuthenticationModule` | Ano | |
| **Omezení podle IP adresy a domény**<br>`IpRestrictionModule`                                          | Ano | |
| **Filtry ISAPI**<br>`IsapiFilterModule`                                                         | Ano | [Middleware](xref:fundamentals/middleware/index) |
| **ISAPI**<br>`IsapiModule`                                                                       | Ano | [Middleware](xref:fundamentals/middleware/index) |
| **Podpora protokolů**<br>`ProtocolSupportModule`                                                  | Ano | |
| **Filtrování požadavků**<br>`RequestFilteringModule`                                                | Ano | [Middleware pro přepis adres URL`IRule`](xref:fundamentals/url-rewriting#irule-based-rule) |
| **Sledování požadavků**<br>`RequestMonitorModule`                                                    | Ano | |
| &#8224; **přepsání adresy URL**<br>`RewriteModule`                                                      | Ano | [Middleware pro přepis adres URL](xref:fundamentals/url-rewriting) |
| **Začlenění na straně serveru**<br>`ServerSideIncludeModule`                                            | Ne  | |
| **Statická komprese**<br>`StaticCompressionModule`                                              | Ne  | [Middleware pro kompresi odpovědí](xref:performance/response-compression) |
| **Statický obsah**<br>`StaticFileModule`                                                         | Ne  | [Middleware statických souborů](xref:fundamentals/static-files) |
| **Ukládání tokenů do mezipaměti**<br>`TokenCacheModule`                                                          | Ano | |
| **Ukládání identifikátorů URI do mezipaměti**<br>`UriCacheModule`                                                              | Ano | |
| **Autorizace adres URL**<br>`UrlAuthorizationModule`                                                | Ano | [ASP.NET Core identity](xref:security/authentication/identity) |
| **Ověřování systému Windows**<br>`WindowsAuthenticationModule`                                      | Ano | |

&#8224;typy `isFile` a `isDirectory` porovnávání v rámci adresy URL nefungují s ASP.NET Core aplikacemi z důvodu změn ve [struktuře adresářové](xref:host-and-deploy/directory-structure)služby.

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

Při použití Správce služby IIS ke konfiguraci nastavení se změní soubor *Web. config* aplikace. Pokud nasazujete aplikaci a včetně *souboru Web. config*, všechny změny provedené ve Správci služby IIS se přepíší nasazeným souborem *Web. config* . Pokud provedete změny v souboru *Web. config* serveru, Zkopírujte aktualizovaný soubor *Web. config* na server do místního projektu hned.

## <a name="disabling-iis-modules"></a>Zakázání modulů IIS

Pokud je modul IIS nakonfigurovaný na úrovni serveru, která musí být pro aplikaci zakázaná, může se tento modul zakázat přidáním do souboru *Web. config* aplikace. Ponechte modul na místě a deaktivujte ho pomocí nastavení konfigurace (Pokud je dostupné) nebo odeberte modul z aplikace.

### <a name="module-deactivation"></a>Deaktivace modulu

Mnoho modulů nabízí konfigurační nastavení, které umožňuje jejich zakázání bez odebrání modulu z aplikace. Toto je nejjednodušší a nejrychlejší způsob, jak deaktivovat modul. Například modul Přesměrování HTTP může být zakázán pomocí `<httpRedirect>` elementu v *souboru Web. config*:

```xml
<configuration>
  <system.webServer>
    <httpRedirect enabled="false" />
  </system.webServer>
</configuration>
```

Další informace o zakázání modulů s nastavením konfigurace najdete v tématu odkazy v části *podřízené prvky* v rámci [služby IIS \<System. webServer>](/iis/configuration/system.webServer/).

### <a name="module-removal"></a>Odebrání modulu

Pokud se rozhodnete odebrat modul s nastavením v *souboru Web. config*, odemkněte modul a nejdříve odemkněte `<modules>` oddíl *Web. config* :

1. Odemkněte modul na úrovni serveru. Vyberte server IIS na postranním panelu **připojení** Správce služby IIS. Otevřete **moduly** v oblasti **IIS** . Vyberte modul v seznamu. Na bočním panelu **Akce** na pravé straně vyberte **odemknout**. Pokud se položka akce pro modul zobrazí jako **Zámek**, modul už je odemčený a nevyžaduje se žádná akce. V případě, že plánujete odebrat ze *souboru Web. config* , odemkněte tolik modulů.

2. Nasaďte aplikaci bez `<modules>` části v *souboru Web. config*. Pokud je aplikace nasazená pomocí *souboru Web. config* , který `<modules>` obsahuje oddíl, aniž byste nejdřív odemkli oddíl ve správci služby IIS, Configuration Manager při pokusu o odemknutí této části výjimku vyvolá. Proto aplikaci nasaďte bez `<modules>` části.

3. Odemkněte `<modules>` oddíl souboru *Web. config*. Na bočním panelu **připojení** vyberte web na **webech**. V oblasti **Správa** otevřete **Editor konfigurací**. K výběru `system.webServer/modules` oddílu použijte navigační ovládací prvky. Na bočním panelu **Akce** na pravé straně vyberte možnost **odemknout** oddíl. Pokud se položka akce pro modul zobrazuje jako **Zámek**, oddíl Module už je odemčený a nevyžaduje se žádná akce.

4. Přidejte `<modules>` oddíl do místního souboru *Web. config* aplikace s `<remove>` elementem, aby se modul z aplikace odebral. Přidejte více `<remove>` prvků pro odebrání více modulů. Pokud jsou na serveru provedeny změny *Web. config* , okamžitě proveďte stejné změny v souboru *Web. config* projektu místně. Odebrání modulu s použitím tohoto přístupu neovlivní použití modulu s jinými aplikacemi na serveru.

   ```xml
   <configuration>
    <system.webServer>
      <modules>
        <remove name="MODULE_NAME" />
      </modules>
    </system.webServer>
   </configuration>
   ```

Chcete-li přidat nebo odebrat moduly pro IIS Express pomocí souboru *Web. config*, upravte soubor *ApplicationHost. config* a `<modules>` odemkněte oddíl:

1. Otevřete *aplikaci {root}\\. vs\config\applicationhost.config*.

1. Vyhledejte `<section>` element pro moduly IIS a změňte `overrideModeDefault` z `Deny` na: `Allow`

   ```xml
   <section name="modules"
            allowDefinition="MachineToApplication"
            overrideModeDefault="Allow" />
   ```

1. Vyhledejte část `<location path="" overrideMode="Allow"><system.webServer><modules>`. U všech modulů, které chcete odebrat, nastavte `lockItem` z `true` na. `false` V následujícím příkladu je modul CGI odemčený:

   ```xml
   <add name="CgiModule" lockItem="false" />
   ```

1. Po odemčení `<modules>` oddílu a jednotlivých modulů můžete přidat nebo odebrat moduly IIS pomocí souboru *Web. config* aplikace pro spuštění aplikace na IIS Express.

Pomocí nástroje *Appcmd. exe*lze také odebrat modul IIS. Zadejte `MODULE_NAME` a `APPLICATION_NAME` v příkazu:

```console
Appcmd.exe delete module MODULE_NAME /app.name:APPLICATION_NAME
```

Odeberte například `DynamicCompressionModule` z výchozího webu:

```console
%windir%\system32\inetsrv\appcmd.exe delete module DynamicCompressionModule /app.name:"Default Web Site"
```

## <a name="minimum-module-configuration"></a>Minimální konfigurace modulu

Jediným modulem požadovaným pro spuštění aplikace ASP.NET Core jsou modul anonymního ověřování a modul ASP.NET Core.

Modul ukládání identifikátorů URI`UriCacheModule`() umožňuje službě IIS ukládat do mezipaměti konfiguraci webu na úrovni URL. Bez tohoto modulu musí služba IIS číst a analyzovat konfiguraci každého požadavku, a to i v případě, že se stejná adresa URL opakovaně požaduje. Analýza konfigurace každé žádosti vede k výraznému snížení výkonu. *I když je modul pro ukládání identifikátorů URI do mezipaměti bezpodmínečně vyžadován pro spuštění hostované aplikace ASP.NET Core, doporučujeme, aby byl modul mezipaměti identifikátoru URI povolen pro všechna nasazení ASP.NET Core.*

Modul HTTP cache (`HttpCacheModule`) implementuje výstupní mezipaměť služby IIS a také logiku pro ukládání položek do mezipaměti HTTP. sys. Bez tohoto modulu už není obsah uložený v mezipaměti v režimu jádra a profily mezipaměti se ignorují. Odebrání modulu mezipaměti HTTP má obvykle negativní vliv na výkon a využití prostředků. *I když modul mezipaměti HTTP není nezbytně nutný pro spuštění hostované aplikace ASP.NET Core, doporučujeme, aby byl modul mezipaměti protokolu HTTP povolen pro všechna nasazení ASP.NET Core.*

## <a name="additional-resources"></a>Další materiály a zdroje informací

* [Seznámení s architekturami služby IIS: moduly ve službě IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#modules-in-iis)
* [Přehled modulů služby IIS](/iis/get-started/introduction-to-iis/iis-modules-overview)
* [Přizpůsobení rolí a modulů služby IIS 7,0](https://technet.microsoft.com/library/cc627313.aspx)
* [>\<IIS System. webServer](/iis/configuration/system.webServer/)

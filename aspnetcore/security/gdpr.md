---
title: Podpora Obecné nařízení o ochraně osobních údajů (GDPR) v ASP.NET Core
author: rick-anderson
description: Naučte se přistupovat k bodům rozšíření GDPR ve webové aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
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
uid: security/gdpr
ms.openlocfilehash: 35a12cb8d2a9617e51d886e798cff5ee60b0a8ad
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634706"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a>Podpora GDPR (EU Obecné nařízení o ochraně osobních údajů) v ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core poskytuje rozhraní API a šablony, které vám pomůžou splnit některé z požadavků [EU obecné nařízení o ochraně osobních údajů (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) :

::: moniker range=">= aspnetcore-3.0"

* Šablony projektů obsahují Rozšiřovací body a podložit značky, které můžete nahradit zásadami ochrany osobních údajů a cookie používání.
* Zobrazení stránky */soukromí. cshtml* nebo *zobrazení/ochrana osobních údajů. cshtml* poskytuje stránku s podrobnostmi o zásadách ochrany osobních údajů vašeho webu.

Pokud chcete povolit cookie funkci výchozího souhlasu, kterou najdete v šablonách ASP.NET Core 2,2 v aplikaci ASP.NET Core 3,0 vygenerované šablonou:

* Přidejte `using Microsoft.AspNetCore.Http` do seznamu direktiv using.
* Přidat [ Cookie PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) do `Startup.ConfigureServices` a [použít Cookie zásady](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) pro `Startup.Configure` :

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* Přidejte do cookie souboru *_Layout. cshtml* část souhlasu:

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* Přidejte do projektu soubor * \_ Cookie ConsentPartial. cshtml* :

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* Pokud si chcete přečíst informace o funkci pro vyjádření souhlasu, vyberte verzi tohoto článku ASP.NET Core 2,2 cookie .

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* Šablony projektů obsahují Rozšiřovací body a podložit značky, které můžete nahradit zásadami ochrany osobních údajů a cookie používání.
* cookieFunkce pro vyjádření souhlasu vám umožní požádat o udělení souhlasu (a sledovat), aby uživatelé mohli ukládat osobní údaje. Pokud uživatel nesouhlasí s shromažďováním dat a aplikace má [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) nastavenou na `true` , nepostradatelné cookie zprávy se neodesílají do prohlížeče.
* Cookies může být označena jako základní. Základní cookie s se odesílají do prohlížeče i v případě, že uživatel není odsouhlasený a sledování je zakázané.
* [TempData a relace cookie ](#tempdata) nejsou funkční, když je sledování zakázané.
* Stránka [ Identity Správa](#pd) poskytuje odkaz na stažení a odstranění uživatelských dat.

[Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) umožňuje testovat většinu bodů rozšíření GDPR a rozhraní API přidaných do šablon ASP.NET Core 2,1. Pokyny k testování najdete v souboru [Readme](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) .

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a>Podpora ASP.NET Core GDPR v kódu generovaném šablonou

Razor Stránky a projekty MVC vytvořené pomocí šablon projektů zahrnují následující podporu GDPR:

* [ Cookie Zásady](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) [ Cookie PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) a use jsou nastaveny ve `Startup` třídě.
* [Částečné zobrazení](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) * \_ Cookie ConsentPartial. cshtml* . V tomto souboru je zahrnuté tlačítko **přijmout** . Když uživatel klikne na tlačítko **přijmout** , je k dispozici souhlas s uložením cookie s.
* Zobrazení stránky */soukromí. cshtml* nebo *zobrazení/ochrana osobních údajů. cshtml* poskytuje stránku s podrobnostmi o zásadách ochrany osobních údajů vašeho webu. Soubor * \_ Cookie ConsentPartial. cshtml* vygeneruje odkaz na stránku ochrany osobních údajů.
* Pro aplikace vytvořené pomocí individuálních uživatelských účtů poskytují stránky Správa odkazy na stažení a odstranění [osobních uživatelských dat](#pd).

### <a name="no-loccookiepolicyoptions-and-useno-loccookiepolicy"></a>CookiePolicyOptions a použití Cookie zásad

[ Cookie PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) jsou inicializovány v `Startup.ConfigureServices` :

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

[Použití Cookie Zásady](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) se volají v `Startup.Configure` :

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_no-loccookieconsentpartialcshtml-partial-view"></a>\_CookieConsentPartial. cshtml – částečné zobrazení

Částečné zobrazení * \_ Cookie ConsentPartial. cshtml* :

[!code-cshtml[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

Tato částečná:

* Získá stav sledování pro uživatele. Pokud je aplikace nakonfigurovaná tak, aby vyžadovala souhlas, musí uživatel souhlasit, než cookie bude možné sledovat. Je-li vyžadován souhlas, cookie je panel souhlasu opraven v horní části navigačního panelu vytvořeného souborem * \_ layout. cshtml* .
* Poskytuje prvek HTML `<p>` pro Shrnutí ochrany osobních údajů a cookie používání zásad.
* Poskytuje odkaz na stránku nebo zobrazení ochrany osobních údajů, kde můžete podrobně určit zásady ochrany osobních údajů vašeho webu.

## <a name="essential-no-loccookies"></a>Základní cookie s

Pokud se nezadá souhlas s úložištěm cookie , cookie pošle se do prohlížeče jenom s označením důležité. Následující kód je cookie základem:

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-no-loccookies-arent-essential"></a>Poskytovatel TempData a stav relace cookie s nejsou nezbytné.

[Poskytovatel TempData](xref:fundamentals/app-state#tempdata) cookie není nezbytný. Pokud je sledování zakázané, zprostředkovatel TempData není funkční. Pokud chcete poskytovatele TempData povolit, když je sledování zakázané, označte TempData cookie jako základní v `Startup.ConfigureServices` :

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

[Stav relace](xref:fundamentals/app-state) cookie s nejsou důležité. Stav relace není funkční, pokud je zakázané sledování. Následující kód provede relaci cookie v podstatě:

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a>Osobní údaje

Aplikace ASP.NET Core vytvořené pomocí individuálních uživatelských účtů obsahují kód ke stažení a odstranění osobních údajů.

Vyberte uživatelské jméno a pak vyberte **osobní údaje**:

![Stránka spravovat osobní data](gdpr/_static/pd.png)

Poznámky:

* Chcete-li vygenerovat `Account/Manage` kód, viz [generování uživatelského rozhraní Identity ](xref:security/authentication/scaffold-identity).
* Odkazy na **odstranění** a **stažení** se chovají jenom s výchozími daty identity. Aplikace, které vytvářejí vlastní uživatelská data, je nutné rozšířit tak, aby se odstranily nebo stáhly vlastní uživatelská data. Další informace najdete v tématu [Přidání, stažení a odstranění vlastních uživatelských dat na Identity ](xref:security/authentication/add-user-data).
* Uložené tokeny pro uživatele, které jsou uložené v Identity tabulce databáze, `AspNetUserTokens` se odstraní, když se uživatel odstraní prostřednictvím chování při Kaskádovém odstraňování z důvodu [cizího klíče](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).
* [Ověřování externího poskytovatele](xref:security/authentication/social/index), jako je Facebook a Google, není k dispozici před cookie přijetím zásad.

::: moniker-end

## <a name="encryption-at-rest"></a>Šifrování neaktivních uložených dat

Některé databáze a mechanismy úložiště umožňují šifrování v klidovém umístění. Šifrování v klidovém umístění:

* Automaticky šifruje uložená data.
* Šifruje bez konfigurace, programování nebo jiné práce pro software, který přistupuje k datům.
* Je nejjednodušší a nejbezpečnější možnost.
* Umožňuje databázi spravovat klíče a šifrování.

Příklad:

* Microsoft SQL a Azure SQL poskytují [transparentní šifrování dat](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).
* [SQL Azure šifruje databázi ve výchozím nastavení.](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* [Objekty blob Azure, soubory, tabulky a Queue Storage jsou ve výchozím nastavení šifrované](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).

Pro databáze, které neposkytují integrované šifrování v klidovém provozu, můžete k zajištění stejné ochrany použít šifrování disku. Příklad:

* [BitLocker pro Windows Server](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* Linux:
  * [eCryptfs](https://launchpad.net/ecryptfs)
  * [EncFS](https://github.com/vgough/encfs).

## <a name="additional-resources"></a>Další zdroje informací

* [Microsoft.com/GDPR](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [GDPR – přidání tlačítka pro odvolání souhlasu v ASP.NET Core](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)

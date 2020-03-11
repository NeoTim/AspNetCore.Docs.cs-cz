---
title: Podpora Obecné nařízení o ochraně osobních údajů (GDPR) v ASP.NET Core
author: rick-anderson
description: Naučte se přistupovat k bodům rozšíření GDPR ve webové aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
uid: security/gdpr
ms.openlocfilehash: 2ccba780ba81bd805d08c9b898617387a879bed3
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78660543"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a>Podpora GDPR (EU Obecné nařízení o ochraně osobních údajů) v ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core poskytuje rozhraní API a šablony, které vám pomůžou splnit některé z požadavků [EU obecné nařízení o ochraně osobních údajů (GDPR)](https://www.eugdpr.org/) :

::: moniker range=">= aspnetcore-3.0"

* Šablony projektů obsahují Rozšiřovací body a podložit značky, které můžete nahradit pomocí zásad ochrany osobních údajů a souborů cookie.
* Zobrazení stránky */soukromí. cshtml* nebo *zobrazení/ochrana osobních údajů. cshtml* poskytuje stránku s podrobnostmi o zásadách ochrany osobních údajů vašeho webu.

Pokud chcete povolit funkci vyjádření souhlasu s výchozím souborem cookie, třeba v šablonách ASP.NET Core 2,2 ve vygenerované aplikaci ASP.NET Core 3,0:

* Přidejte `using Microsoft.AspNetCore.Http` do seznamu direktiv using.
* Přidejte [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) do `Startup.Configure``Startup.ConfigureServices` a [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) :

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* Přidejte do souboru *_Layout. cshtml* část souhlasu souboru cookie:

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* Přidejte do projektu soubor *\_CookieConsentPartial. cshtml* :

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* Pokud chcete získat informace o funkci pro vyjádření souhlasu souborů cookie, vyberte verzi tohoto článku ASP.NET Core 2,2.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* Šablony projektů obsahují Rozšiřovací body a podložit značky, které můžete nahradit pomocí zásad ochrany osobních údajů a souborů cookie.
* Funkce pro vyjádření souhlasu souborů cookie vám umožní požádat o souhlas uživatelů o vyjádření (a sledovat), aby mohli ukládat osobní údaje. Pokud uživatel nesouhlasí s shromažďováním dat a aplikace má [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) nastaveno na `true`, soubory cookie, které nejsou nezbytné, nejsou odesílány do prohlížeče.
* Soubory cookie lze označit jako nezbytné. Základní soubory cookie se odesílají do prohlížeče i v případě, že uživatel není odsouhlasený a sledování je zakázané.
* [Soubory cookie TempData a Session](#tempdata) nejsou funkční, pokud je sledování zakázané.
* Stránka [Správa identit](#pd) poskytuje odkaz na stažení a odstranění uživatelských dat.

[Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) umožňuje testovat většinu bodů rozšíření GDPR a rozhraní API přidaných do šablon ASP.NET Core 2,1. Pokyny k testování najdete v souboru [Readme](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) .

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a>Podpora ASP.NET Core GDPR v kódu generovaném šablonou

Projekty Razor Pages a MVC vytvořené pomocí šablon projektů zahrnují následující podporu GDPR:

* [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) a [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) jsou nastaveny ve třídě `Startup`.
* [Částečné zobrazení](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) *\_CookieConsentPartial. cshtml* . V tomto souboru je zahrnuté tlačítko **přijmout** . Pokud uživatel klikne na tlačítko **přijmout** , je k dispozici souhlas s uložením souborů cookie.
* Zobrazení stránky */soukromí. cshtml* nebo *zobrazení/ochrana osobních údajů. cshtml* poskytuje stránku s podrobnostmi o zásadách ochrany osobních údajů vašeho webu. Soubor *\_CookieConsentPartial. cshtml* vygeneruje odkaz na stránku ochrany osobních údajů.
* Pro aplikace vytvořené pomocí individuálních uživatelských účtů poskytují stránky Správa odkazy na stažení a odstranění [osobních uživatelských dat](#pd).

### <a name="cookiepolicyoptions-and-usecookiepolicy"></a>CookiePolicyOptions a UseCookiePolicy

[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) jsou inicializovány v `Startup.ConfigureServices`:

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) se volá v `Startup.Configure`:

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_cookieconsentpartialcshtml-partial-view"></a>částečné zobrazení \_CookieConsentPartial. cshtml

Částečné zobrazení *\_CookieConsentPartial. cshtml* :

[!code-html[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

Tato částečná:

* Získá stav sledování pro uživatele. Pokud je aplikace nakonfigurovaná tak, aby vyžadovala souhlas, musí uživatel souhlasit, než bude možné sledovat soubory cookie. Je-li vyžadován souhlas, je panel pro vyjádření souhlasu s souborem cookie opraven v horní části navigačního panelu vytvořeného souborem *\_layout. cshtml* .
* Poskytuje prvek HTML `<p>` pro shrnutí vašich osobních údajů a zásad použití souborů cookie.
* Poskytuje odkaz na stránku nebo zobrazení ochrany osobních údajů, kde můžete podrobně určit zásady ochrany osobních údajů vašeho webu.

## <a name="essential-cookies"></a>Základní soubory cookie

Pokud se nezadá souhlas s uložením souborů cookie, pošle se do prohlížeče jenom soubory cookie označené jako nezbytné. Následující kód provede základní soubor cookie:

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-cookies-arent-essential"></a>Soubory cookie poskytovatele TempData a stavu relace nejsou nezbytné.

Soubor cookie [poskytovatele TempData](xref:fundamentals/app-state#tempdata) není nezbytný. Pokud je sledování zakázané, zprostředkovatel TempData není funkční. Pokud chcete poskytovatele TempData povolit, pokud je zakázané sledování, označte soubor cookie TempData jako nezbytný v `Startup.ConfigureServices`:

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

Soubory cookie [stavu relace](xref:fundamentals/app-state) nejsou nezbytné. Stav relace není funkční, pokud je zakázané sledování. Následující kód vytváří soubory cookie relace jako nezbytné:

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a>Osobní data

Aplikace ASP.NET Core vytvořené pomocí individuálních uživatelských účtů obsahují kód ke stažení a odstranění osobních údajů.

Vyberte uživatelské jméno a pak vyberte **osobní údaje**:

![Stránka spravovat osobní data](gdpr/_static/pd.png)

Poznámky:

* Chcete-li vygenerovat kód `Account/Manage`, přečtěte si téma [Identita uživatelského rozhraní](xref:security/authentication/scaffold-identity).
* Odkazy na **odstranění** a **stažení** se chovají jenom s výchozími daty identity. Aplikace, které vytvářejí vlastní uživatelská data, je nutné rozšířit tak, aby se odstranily nebo stáhly vlastní uživatelská data. Další informace najdete v tématu [Přidání, stažení a odstranění vlastních uživatelských dat do identity](xref:security/authentication/add-user-data).
* Uložené tokeny pro uživatele, kteří jsou uloženy v tabulce databáze identity `AspNetUserTokens` jsou odstraněny při odstranění uživatele prostřednictvím chování při Kaskádovém odstraňování z důvodu [cizího klíče](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).
* [Ověřování externího poskytovatele](xref:security/authentication/social/index), jako je Facebook a Google, není k dispozici před přijetím zásad souborů cookie.

::: moniker-end

## <a name="encryption-at-rest"></a>Šifrování v klidovém stavu

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

## <a name="additional-resources"></a>Další zdroje

* [Microsoft.com/GDPR](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [GDPR – přidání tlačítka pro odvolání souhlasu v ASP.NET Core](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)

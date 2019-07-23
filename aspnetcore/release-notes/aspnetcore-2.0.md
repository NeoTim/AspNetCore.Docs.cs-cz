---
title: Co je nového v ASP.NET Core 2.0
author: rick-anderson
description: Informace o nových funkcích v ASP.NET Core 2.0.
ms.author: riande
ms.custom: mvc
ms.date: 05/28/2019
uid: aspnetcore-2.0
ms.openlocfilehash: dac5a48042a71774cb884e30a166be68bee69691
ms.sourcegitcommit: f5762967df3be8b8c868229e679301f2f7954679
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67048107"
---
# <a name="whats-new-in-aspnet-core-20"></a>Co je nového v ASP.NET Core 2.0

Tento článek se soustředí na nejdůležitější změny provedené v ASP.NET Core 2.0, odkazy na relevantní dokumentaci.

## <a name="razor-pages"></a>Razor Pages

Stránky Razor je nová funkce služby ASP.NET Core MVC, která provádí kódování zaměřené na stránce scénáře jednodušší a produktivnější.

Další informace najdete v tématu Úvod a kurzu:

* [Úvod do Razor Pages](xref:razor-pages/index)
* [Začínáme se stránkami Razor](xref:tutorials/razor-pages/razor-pages-start)

## <a name="aspnet-core-metapackage"></a>ASP.NET Core Microsoft.aspnetcore.all

Nové Microsoft.aspnetcore.all ASP.NET Core zahrnuje všechny balíčky provedli a podporuje ASP.NET Core a Entity Framework Core týmy společně s jejich závislostmi interní a 3. stran. Už nemusíte vybrat jednotlivé ASP.NET Core funkce balíčkem. Všechny funkce jsou součástí [metabalíček](https://www.nuget.org/packages/Microsoft.AspNetCore.All) balíčku. Tento balíček použít výchozí šablony.

Další informace najdete v tématu [metabalíček Microsoft.aspnetcore.all pro ASP.NET Core 2.0](xref:fundamentals/metapackage).

## <a name="runtime-store"></a>Modul runtime Store

Aplikace, které používají `Microsoft.AspNetCore.All` Microsoft.aspnetcore.all automaticky využívat nové Store Runtime jádra .NET. Store obsahuje všechny prostředky modulu runtime potřebné ke spouštění aplikací ASP.NET Core 2.0. Při použití `Microsoft.AspNetCore.All` Microsoft.aspnetcore.all žádné prostředky z balíčků odkazovaných ASP.NET Core NuGet jsou nasazeny s aplikací, protože se již nacházejí v cílovém systému. Prostředky v modulu Runtime Store jsou také předkompilované zlepšit dobu spuštění aplikace.

Další informace najdete v tématu [úložiště modulu Runtime](/dotnet/core/deploying/runtime-store)

## <a name="net-standard-20"></a>.NET Standard 2.0

ASP.NET Core 2.0 balíčky cílit na .NET Standard 2.0. Balíčky mohou odkazovat další knihovny .NET Standard 2.0, a můžete spustit na .NET Standard 2.0 vyhovující implementace .NET, včetně .NET Core 2.0 a .NET Framework 4.6.1. 

`Microsoft.AspNetCore.All` Microsoft.aspnetcore.all cílí na .NET Core 2.0 pouze, protože je určen pro použití s Store modulu Runtime .NET Core 2.0.

## <a name="configuration-update"></a>Aktualizace konfigurace

`IConfiguration` Instance služby kontejneru přidá ve výchozím nastavení v ASP.NET Core 2.0. `IConfiguration` ve službách kontejneru usnadňuje pro aplikace pro načtení hodnoty konfigurace z kontejneru.

Informace o stavu plánované dokumentaci najdete v tématu [problém Githubu](https://github.com/aspnet/AspNetCore.Docs/issues/3387).

## <a name="logging-update"></a>Protokolování aktualizace

V technologii ASP.NET Core 2.0 je ve výchozím nastavení protokolování součástí závislost injektor (DI). Přidat poskytovatele a konfigurovat filtrování v *Program.cs* místo v souboru *Startup.cs* souboru. A ve výchozím nastavení `ILoggerFactory` podporuje filtrování způsobem, který vám umožní používat flexibilní jedním z přístupů pro poskytovatele křížové filtrování a filtrování konkrétního zprostředkovatele.

Další informace najdete v tématu [Úvod k protokolování](xref:fundamentals/logging/index).

## <a name="authentication-update"></a>Aktualizace ověřování

Konfigurace ověřování pro aplikaci s využitím DI usnadňuje nový model ověřování.

Nové šablony jsou dostupné pro konfiguraci ověřování pro webové aplikace a webové rozhraní API pomocí [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/).

Informace o stavu plánované dokumentaci najdete v tématu [problém Githubu](https://github.com/aspnet/AspNetCore.Docs/issues/3054).

## <a name="identity-update"></a>Aktualizace identity

Provedli jsme je snazší zajistit zabezpečení webového rozhraní API pomocí Identity v ASP.NET Core 2.0. Můžete získat přístupové tokeny pro přístup pomocí webového rozhraní API [Microsoft Authentication Library (MSAL)](https://www.nuget.org/packages/Microsoft.Identity.Client).

Další informace o ověřování změny ve verzi 2.0 naleznete na následujících odkazech:

* [Potvrzení účtu a obnovení hesla v ASP.NET Core](xref:security/authentication/accconfirm)
* [Povolit generování kódu QR pro aplikace v ASP.NET Core](xref:security/authentication/identity-enable-qrcodes)
* [Ověřování a identitu migrovat do ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x)

## <a name="spa-templates"></a>Šablon SPA

Jeden šablony projektu stránka aplikace (SPA) pro Angular, Aurelia, knihovnou Knockout.js, React.js a React.js s Reduxem jsou k dispozici. Aktualizovali jsme Angular šablony Angular 4. Jsou k dispozici ve výchozím nastavení; šablony Angular a React informace o tom, jak získat další šablony najdete v tématu [vytvořte nový projekt SPA](xref:client-side/spa-services#create-a-new-project). Informace o tom, jak vytvářet aplikace SPA v ASP.NET Core najdete v tématu <xref:client-side/spa-services>.

## <a name="kestrel-improvements"></a>Vylepšení kestrel

Webový server Kestrel má nové funkce, které usnadňují vhodnější jako server přístupem k Internetu. Počet možnosti konfigurace serveru omezení přidány `KestrelServerOptions` vaší třídy nový `Limits` vlastnost. Přidáte omezení pro následující:

* Maximální počet klientských připojení
* Velikost textu maximální požadavku
* Minimální požadavek tělo přenosová rychlost

Další informace najdete v tématu [Kestrel webového serveru provedení v ASP.NET Core](xref:fundamentals/servers/kestrel).

## <a name="weblistener-renamed-to-httpsys"></a>Přejmenovat soubor HTTP.sys WebListener

Balíčky `Microsoft.AspNetCore.Server.WebListener` a `Microsoft.Net.Http.Server` byly sloučeny do nového balíčku `Microsoft.AspNetCore.Server.HttpSys`. Obory názvů jsou aktualizované tak, aby odpovídaly.

Další informace najdete v tématu [implementaci serveru HTTP.sys web v ASP.NET Core](xref:fundamentals/servers/httpsys).

## <a name="enhanced-http-header-support"></a>Vylepšená podpora hlavičky protokolu HTTP

Při použití MVC přenášet `FileStreamResult` nebo `FileContentResult`, teď máte možnost nastavit `ETag` nebo `LastModified` datum přenosu obsahu. Můžete nastavit tyto hodnoty na vrácený obsah s kódem podobný následujícímu:

```csharp
var data = Encoding.UTF8.GetBytes("This is a sample text from a binary array");
var entityTag = new EntityTagHeaderValue("\"MyCalculatedEtagValue\"");
return File(data, "text/plain", "downloadName.txt", lastModified: DateTime.UtcNow.AddSeconds(-5), entityTag: entityTag);
```

Soubor vrátil návštěvnících bude doplněn správné hlavičky HTTP pro `ETag` a `LastModified` hodnoty.

Pokud návštěvníka žádosti o obsah aplikace se požadavek na zjištění rozsahu, ASP.NET Core rozpozná požadavek a zpracovává hlavičky. Pokud požadovaný obsah může doručit částečně, ASP.NET Core odpovídajícím způsobem přeskočí a vrátí jenom požadované sadě bajtů. Není nutné k zápisu do metody pro přizpůsobení nebo zpracování této funkce; všechny speciální obslužné rutiny To se automaticky postará služba za vás.

## <a name="hosting-startup-and-application-insights"></a>Hostování spuštění a Application Insights

Hostitelská prostředí lze nyní vložení navíc závislosti a spouštění kódu během spuštění aplikace, aniž by museli explicitně věděly, nebo volat všechny metody aplikace. Tato funkce slouží k povolení určitých prostředí pro funkce "světla nahoru" jedinečné do daného prostředí bez aplikace nepotřebuje vědět předem. 

V technologii ASP.NET Core 2.0, tato funkce slouží k automaticky povolit diagnostiku Application Insights při ladění v sadě Visual Studio a (po vyjádření výslovného souhlasu) při spuštění v Azure App Services. V důsledku toho šablony projektu už přidat balíčky Application Insights a kódu ve výchozím nastavení.

Informace o stavu plánované dokumentaci najdete v tématu [problém Githubu](https://github.com/aspnet/AspNetCore.Docs/issues/3389).

## <a name="automatic-use-of-anti-forgery-tokens"></a>Automatické použití tokenů proti padělání

ASP.NET Core se povedlo vždy s kódováním HTML obsah ve výchozím nastavení, ale s novou verzí pořízení umožňující přidat další krok útokům podvržení žádosti padělání (XSRF). ASP.NET Core se teď generování tokenů proti padělání ve výchozím nastavení a ověření na akce POST formuláře a stránky bez další konfigurace.

Další informace najdete v tématu [útokům zabránilo webů žádosti padělání (XSRF/CSRF)](xref:security/anti-request-forgery).

## <a name="automatic-precompilation"></a>Automatická předkompilace

Předkompilace Razor zobrazení je standardně během publikování, snížení velikosti výstup publikování a aplikace času spuštění.

Další informace najdete v tématu [kompilace zobrazení Razor a předkompilace v ASP.NET Core](xref:mvc/views/view-compilation).

## <a name="razor-support-for-c-71"></a>Razor podporu pro C# 7.1

Pro práci s nový kompilátor Roslyn se aktualizoval zobrazovací modul Razor. Který zahrnuje podporu pro funkce C# 7.1 jako výchozí výrazy odvodit názvy řazené kolekce členů a porovnávání vzorů s obecnými typy. Použití jazyka C# 7.1 ve vašem projektu, přidejte následující vlastnost v souboru projektu a pak znovu načíst řešení:

```xml
<LangVersion>latest</LangVersion>
```

Informace o stavu funkce jazyka C# 7.1, naleznete v tématu [úložiště Roslyn GitHub](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md).

## <a name="other-documentation-updates-for-20"></a>Další aktualizace dokumentace pro 2.0

* [Visual Studio publikačních profilů pro nasazení aplikace ASP.NET Core](xref:host-and-deploy/visual-studio-publish-profiles)
* [Správa klíčů](xref:security/data-protection/implementation/key-management)
* [Konfigurace ověřování sítě Facebook](xref:security/authentication/facebook-logins)
* [Konfigurace ověřování Twitteru](xref:security/authentication/twitter-logins)
* [Konfigurace ověřování Google](xref:security/authentication/google-logins)
* [Konfigurace ověřování Account Microsoft](xref:security/authentication/microsoft-logins)

## <a name="migration-guidance"></a>Pokyny k migraci

Informace o tom, jak migrovat aplikace ASP.NET Core 1.x do ASP.NET Core 2.0 naleznete v následujících zdrojích:

* [Migrace z technologie ASP.NET Core 1.x do ASP.NET Core 2.0](xref:migration/1x-to-2x/index)
* [Ověřování a identitu migrovat do ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x)

## <a name="additional-information"></a>Další informace

Úplný seznam změn, najdete v článku [poznámky k verzi pro ASP.NET Core 2.0](https://github.com/aspnet/Home/releases/tag/2.0.0).

Spojte se s vývojovým týmem ASP.NET Core průběh a plány, Nalaďte si [rychlá schůzka komunitě ASP.NET](https://live.asp.net/).

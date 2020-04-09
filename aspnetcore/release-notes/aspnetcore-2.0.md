---
title: Co je nového v ASP.NET Core 2.0
author: rick-anderson
description: Seznamte se s novými funkcemi v ASP.NET Core 2.0.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: aspnetcore-2.0
ms.openlocfilehash: 5ca43bab1496aa9fda65282cbb0b1177ad8689eb
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667256"
---
# <a name="whats-new-in-aspnet-core-20"></a>Co je nového v ASP.NET Core 2.0

Tento článek upozorňuje na nejvýznamnější změny v ASP.NET Core 2.0 s odkazy na příslušnou dokumentaci.

## <a name="razor-pages"></a>Razor Pages

Razor Pages je nová funkce ASP.NET Core MVC, která usnadňuje a zkušeněji kódování scénářů zaměřených na stránky.

Další informace naleznete v úvodu a kurzu:

* [Úvod do Razor Pages](xref:razor-pages/index)
* [Začínáme se stránkami Razor](xref:tutorials/razor-pages/razor-pages-start)

## <a name="aspnet-core-metapackage"></a>metabalíček ASP.NET Core

Nový metabalíček ASP.NET Core zahrnuje všechny balíčky vyrobené a podporované týmy ASP.NET core a entity framework core spolu s jejich vnitřními závislostmi a závislostmi třetích stran. Již nemusíte vybírat jednotlivé funkce ASP.NET Core podle balíčku. Všechny funkce jsou součástí balíčku [Microsoft.AspNetCore.All.](https://www.nuget.org/packages/Microsoft.AspNetCore.All) Výchozí šablony používají tento balíček.

Další informace naleznete v [metabalíčku Microsoft.AspNetCore.All pro ASP.NET Core 2.0](xref:fundamentals/metapackage).

## <a name="runtime-store"></a>Runtime Obchod

Aplikace, které `Microsoft.AspNetCore.All` používají metabalíček, automaticky využívají výhod nového úložiště .NET Core Runtime Store. Úložiště obsahuje všechny datové zdroje za běhu potřebné ke spuštění ASP.NET aplikací Core 2.0. Při použití `Microsoft.AspNetCore.All` metabalíčku žádné prostředky z odkazované ASP.NET balíčky Core NuGet jsou nasazeny s aplikací, protože již jsou umístěny v cílovém systému. Prostředky v runtime store jsou také předkompilovány ke zlepšení doby spuštění aplikace.

Další informace naleznete [v tématu Runtime store](/dotnet/core/deploying/runtime-store)

## <a name="net-standard-20"></a>.NET Standard 2.0

Balíčky ASP.NET Core 2.0 cílí na standard .NET Standard 2.0. Na balíčky lze odkazovat jinými knihovnami .NET Standard 2.0 a lze je spouštět v implementacích rozhraní .NET Standard 2.0 rozhraní .NET, včetně rozhraní .NET Core 2.0 a rozhraní .NET Framework 4.6.1. 

Metapackage `Microsoft.AspNetCore.All` cílí pouze na rozhraní .NET Core 2.0, protože je určen pro použití s úložištěm runtime .NET Core 2.0.

## <a name="configuration-update"></a>Aktualizace konfigurace

Instance `IConfiguration` je přidána do kontejneru služeb ve výchozím nastavení v ASP.NET Core 2.0. `IConfiguration`v kontejneru služeb usnadňuje aplikacím načíst hodnoty konfigurace z kontejneru.

Informace o stavu plánované dokumentace naleznete v [tématu problém GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/3387).

## <a name="logging-update"></a>Aktualizace protokolování

V ASP.NET Core 2.0 protokolování je začleněna do systému vkládání závislostí (DI) ve výchozím nastavení. Můžete přidat zprostředkovatele a nakonfigurovat filtrování v *souboru Program.cs* namísto v *souboru Startup.cs.* A výchozí `ILoggerFactory` podporuje filtrování způsobem, který umožňuje použít jeden flexibilní přístup pro filtrování mezi zprostředkovateli a filtrování konkrétního zprostředkovatele.

Další informace naleznete [v tématu Úvod do protokolování](xref:fundamentals/logging/index).

## <a name="authentication-update"></a>Aktualizace ověřování

Nový model ověřování usnadňuje konfiguraci ověřování pro aplikaci pomocí DI.

Nové šablony jsou k dispozici pro konfiguraci ověřování pro webové aplikace a webová rozhraní API pomocí [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/).

Informace o stavu plánované dokumentace naleznete v [tématu problém GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/3054).

## <a name="identity-update"></a>Aktualizace identity

Usnadnili jsme vytváření zabezpečených webových api pomocí identity v ASP.NET Core 2.0. Přístupové tokeny pro přístup k webovým rozhraním API můžete získat pomocí [knihovny Microsoft Authentication Library (MSAL).](https://www.nuget.org/packages/Microsoft.Identity.Client)

Další informace o změnách ověřování v protokolu 2.0 naleznete v následujících zdrojích:

* [Potvrzení účtu a obnovení hesla v ASP.NET Core](xref:security/authentication/accconfirm)
* [Povolit generování QR kódu pro ověřovací aplikace v ASP.NET Core](xref:security/authentication/identity-enable-qrcodes)
* [Migrace ověřování a identity do ASP.NET jádra 2.0](xref:migration/1x-to-2x/identity-2x)

## <a name="spa-templates"></a>Šablony SPA

K dispozici jsou šablony projektu Jednostránkové aplikace (SPA) pro Angular, Aurelia, Knockout.js, React.js a React.js s Redux. Úhlová šablona byla aktualizována na Úhlové 4. Šablony Angular a React jsou ve výchozím nastavení k dispozici. Informace o tom, jak získat další šablony, naleznete v [tématu Vytvoření nového projektu SPA](xref:client-side/spa-services#create-a-new-project). Informace o tom, jak vytvořit spa <xref:client-side/spa-services>v ASP.NET Core, naleznete v tématu .

## <a name="kestrel-improvements"></a>Zlepšení kestrelu

Webový server Kestrel má nové funkce, díky kterým je vhodnější jako internetový server. Do nové `KestrelServerOptions` `Limits` vlastnosti třídy je přidáno několik možností konfigurace omezení serveru. Přidejte omezení pro následující:

* Maximální počet připojení klientů
* Maximální velikost těla požadavku
* Minimální přenosová rychlost tělo požadavku

Další informace naleznete [v tématu Kestrel implementace webového serveru v ASP.NET Core](xref:fundamentals/servers/kestrel).

## <a name="weblistener-renamed-to-httpsys"></a>WebListener přejmenován na HTTP.sys

Balíčky `Microsoft.AspNetCore.Server.WebListener` `Microsoft.Net.Http.Server` a byly sloučeny `Microsoft.AspNetCore.Server.HttpSys`do nového balíčku . Obory názvů byly aktualizovány tak, aby odpovídaly.

Další informace naleznete [v tématu implementace webového serveru HTTP.sys v ASP.NET Core](xref:fundamentals/servers/httpsys).

## <a name="enhanced-http-header-support"></a>Rozšířená podpora záhlaví protokolu HTTP

Při použití MVC `FileStreamResult` k `FileContentResult`přenosu nebo , nyní máte `ETag` možnost `LastModified` nastavit nebo datum na obsah, který přenášíte. Tyto hodnoty můžete nastavit na vrácený obsah s kódem podobným následující:

```csharp
var data = Encoding.UTF8.GetBytes("This is a sample text from a binary array");
var entityTag = new EntityTagHeaderValue("\"MyCalculatedEtagValue\"");
return File(data, "text/plain", "downloadName.txt", lastModified: DateTime.UtcNow.AddSeconds(-5), entityTag: entityTag);
```

Soubor vrácený návštěvníkům má příslušná http `ETag` `LastModified` záhlaví pro hodnoty a.

Pokud návštěvník aplikace požaduje obsah s hlavičkou požadavku rozsahu, ASP.NET Core požadavek rozpozná a zzpracovává záhlaví. Pokud požadovaný obsah lze částečně doručit, ASP.NET Core odpovídajícím způsobem přeskočí a vrátí pouze požadovanou sadu bajtů. Není nutné zapisovat žádné speciální obslužné rutiny do metod přizpůsobit nebo zpracovat tuto funkci; je automaticky zpracována za vás.

## <a name="hosting-startup-and-application-insights"></a>Hostování spouštění a přehledů aplikací

Hostitelská prostředí nyní můžete vložit další balíček závislostí a spustit kód při spuštění aplikace, aniž by aplikace nutnost explicitně trvat závislost nebo volání jakékoli metody. Tuto funkci lze použít k povolení některých prostředí "light-up" funkce jedinečné pro toto prostředí bez aplikace potřebují vědět dopředu. 

V ASP.NET Core 2.0 se tato funkce používá k automatickému povolení diagnostiky Application Insights při ladění v sadě Visual Studio a (po přihlášení) při spuštění ve službě Azure App Services. V důsledku toho šablony projektu již nepřidávají balíčky Application Insights a kód ve výchozím nastavení.

Informace o stavu plánované dokumentace naleznete v [tématu problém GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/3389).

## <a name="automatic-use-of-anti-forgery-tokens"></a>Automatické používání antipadělkových žetonů

ASP.NET Core vždy pomáhal html-kódovat obsah ve výchozím nastavení, ale s novou verzí je přijatdalší krok, aby se zabránilo útokům padělání žádostí mezi weby (XSRF). ASP.NET Core nyní ve výchozím nastavení vyzařují tokeny anti-padělanosti a ověřují je na akcích a stránkách formuláře POST bez další konfigurace.

Další informace naleznete [v tématu Zabránění útokům na vyžádání mezi servery (XSRF/CSRF).](xref:security/anti-request-forgery)

## <a name="automatic-precompilation"></a>Automatická předkompilace

Razor view pre-kompilace je povolena během publikování ve výchozím nastavení, což snižuje velikost výstupu publikování a čas spuštění aplikace.

Další informace naleznete [v tématu Razor view compilation and precompilation in ASP.NET Core](xref:mvc/views/view-compilation).

## <a name="razor-support-for-c-71"></a>Podpora holicího strojku pro C# 7.1

Stroj zobrazení Razor byl aktualizován pro práci s novým kompilátorem Roslyn. To zahrnuje podporu pro c# 7.1 funkce, jako jsou výchozí výrazy, odvozené názvy tuple a porovnávání vzorů s obecnými typy. Chcete-li v projektu použít c# 7.1, přidejte do souboru projektu následující vlastnost a znovu načtěte řešení:

```xml
<LangVersion>latest</LangVersion>
```

Informace o stavu funkcí jazyka C# 7.1 naleznete [v úložišti Roslyn GitHub](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md).

## <a name="other-documentation-updates-for-20"></a>Další aktualizace dokumentace pro 2.0

* [Visual Studio publikuje profily pro nasazení aplikace ASP.NET Core](xref:host-and-deploy/visual-studio-publish-profiles)
* [Správa klíčů](xref:security/data-protection/implementation/key-management)
* [Konfigurace ověřování na Facebooku](xref:security/authentication/facebook-logins)
* [Konfigurace ověřování na Twitteru](xref:security/authentication/twitter-logins)
* [Konfigurace ověřování Google](xref:security/authentication/google-logins)
* [Konfigurace ověřování účtu Microsoft](xref:security/authentication/microsoft-logins)

## <a name="migration-guidance"></a>Pokyny k migraci

Pokyny k migraci ASP.NET aplikací core 1.x do ASP.NET jádra 2.0 naleznete v následujících zdrojích:

* [Migrace z ASP.NET jádra 1.x do ASP.NET Jádra 2.0](xref:migration/1x-to-2x/index)
* [Migrace ověřování a identity do ASP.NET jádra 2.0](xref:migration/1x-to-2x/identity-2x)

## <a name="additional-information"></a>Další informace

Úplný seznam změn naleznete v [ASP.NET poznámky k verzi Core 2.0](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0).

Chcete-li se spojit s pokrokem a plány vývojového týmu ASP.NET Core, nalaďte se na [ASP.NET komunitního standupu](https://live.asp.net/).

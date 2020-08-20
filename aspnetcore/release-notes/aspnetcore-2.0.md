---
title: Co je nového v ASP.NET Core 2,0
author: rick-anderson
description: Seznamte se s novými funkcemi v ASP.NET Core 2,0.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: aspnetcore-2.0
ms.openlocfilehash: 9d8c33ef10825baa212466fe683d4c1dc582f7eb
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632457"
---
# <a name="whats-new-in-aspnet-core-20"></a>Co je nového v ASP.NET Core 2,0

Tento článek zvýrazňuje nejvýznamnější změny v ASP.NET Core 2,0 s odkazy na příslušnou dokumentaci.

## <a name="no-locrazor-pages"></a>Razor Stránky

Razor Stránky jsou novou funkcí ASP.NET Core MVC, která usnadňuje a produktivnější vytváření kódu pro scénáře zaměřené na stránku.

Další informace najdete v úvodu a v kurzu:

* [Úvod ke Razor stránkám](xref:razor-pages/index)
* [Začínáme se Razor stránkami](xref:tutorials/razor-pages/razor-pages-start)

## <a name="aspnet-core-metapackage"></a>ASP.NET Core Metapackage

Nový ASP.NET Core Metapackage zahrnuje všechny balíčky vytvořené a podporované ASP.NET Core a Entity Framework Core týmy a jejich interní závislosti a závislosti třetích stran. Už nemusíte volit jednotlivé funkce ASP.NET Core podle balíčku. Všechny funkce jsou součástí balíčku [Microsoft. AspNetCore. All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) . Výchozí šablony používají tento balíček.

Další informace najdete v tématu [Microsoft. AspNetCore. All Metapackage for ASP.NET Core 2,0](xref:fundamentals/metapackage).

## <a name="runtime-store"></a>Běhové úložiště

Aplikace, které používají `Microsoft.AspNetCore.All` Metapackage, automaticky využijí nové úložiště runtime .NET Core. Úložiště obsahuje všechny prostředky modulu runtime potřebné ke spouštění aplikací ASP.NET Core 2,0. Při použití Metapackage se `Microsoft.AspNetCore.All` s aplikací nasadí žádné prostředky z odkazovaného ASP.NET Core balíčků NuGet, protože už jsou umístěné v cílovém systému. Prostředky v úložišti za běhu jsou také předem kompilovány pro zlepšení času spuštění aplikace.

Další informace najdete v tématu [běhové úložiště](/dotnet/core/deploying/runtime-store) .

## <a name="net-standard-20"></a>.NET Standard 2,0

Cílová ASP.NET Core 2,0 balíčků .NET Standard 2,0. Na balíčky můžou odkazovat jiné knihovny .NET Standard 2,0 a můžou běžet na .NET Standard 2,0 implementace platformy .NET, včetně .NET Core 2,0 a .NET Framework 4.6.1. 

`Microsoft.AspNetCore.All`Metapackage cílí jenom na .NET core 2,0, protože je určené pro použití s úložištěm runtime .NET Core 2,0.

## <a name="configuration-update"></a>Aktualizace konfigurace

`IConfiguration`Instance je ve výchozím nastavení přidána do kontejneru Services v ASP.NET Core 2,0. `IConfiguration` v kontejneru služby usnadňuje aplikacím načítání konfiguračních hodnot z kontejneru.

Informace o stavu plánované dokumentace najdete v tématu [problém GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/3387).

## <a name="logging-update"></a>Aktualizace protokolování

V ASP.NET Core 2,0 je ve výchozím nastavení do systému vkládání závislostí začleněno protokolování (DI). Přidáte poskytovatele a nakonfigurujete filtrování v souboru *program.cs* místo v souboru *Startup.cs* . A ve výchozím nastavení `ILoggerFactory` podporuje filtrování způsobem, který umožňuje použití jednoho flexibilního přístupu pro filtrování mezi poskytovateli a filtrování specifického poskytovatele.

Další informace najdete v tématu [Úvod do protokolování](xref:fundamentals/logging/index).

## <a name="authentication-update"></a>Aktualizace ověřování

Nový model ověřování usnadňuje konfiguraci ověřování pro aplikaci pomocí typu DI.

Nové šablony jsou k dispozici pro konfiguraci ověřování pro webové aplikace a webová rozhraní API pomocí [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/).

Informace o stavu plánované dokumentace najdete v tématu [problém GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/3054).

## <a name="no-locidentity-update"></a>Identity Update

Usnadnili jsme vytváření zabezpečených webových rozhraní API pomocí Identity ASP.NET Core 2,0. Přístupové tokeny pro přístup k webovým rozhraním API můžete získat pomocí [knihovny Microsoft Authentication Library (MSAL)](https://www.nuget.org/packages/Microsoft.Identity.Client).

Další informace o změnách ověřování v 2,0 najdete v následujících zdrojích informací:

* [Potvrzení účtu a obnovení hesla v ASP.NET Core](xref:security/authentication/accconfirm)
* [Povolit generování kódu QR pro ověřovací aplikace v ASP.NET Core](xref:security/authentication/identity-enable-qrcodes)
* [Migrace ověřování a Identity ASP.NET Core 2,0](xref:migration/1x-to-2x/identity-2x)

## <a name="spa-templates"></a>Šablony SPA

K dispozici jsou šablony projektů s jednou stránkou (SPA) pro úhlové, Aurelia, Knockout.js, React.js a React.js s Redux. Úhlová šablona byla aktualizována na úhlové 4. Šablony úhlů a reakce jsou k dispozici ve výchozím nastavení. informace o tom, jak získat další šablony, najdete v tématu [Vytvoření nového projektu Spa](xref:client-side/spa-services#create-a-new-project). Informace o tom, jak vytvořit SPA v ASP.NET Core, najdete v tématu <xref:client-side/spa-services> .

## <a name="kestrel-improvements"></a>Vylepšení Kestrel

Webový server Kestrel má nové funkce, díky kterým je vhodnější jako internetový server. V nové vlastnosti třídy je přidáno několik možností konfigurace omezení serveru `KestrelServerOptions` `Limits` . Přidejte omezení pro následující:

* Maximální počet připojení klientů
* Maximální velikost textu požadavku
* Minimální rychlost těla zprávy požadavku

Další informace najdete v tématu [implementace webového serveru Kestrel v ASP.NET Core](xref:fundamentals/servers/kestrel).

## <a name="weblistener-renamed-to-httpsys"></a>Weblisten byl přejmenován na HTTP.sys

Balíčky `Microsoft.AspNetCore.Server.WebListener` a `Microsoft.Net.Http.Server` byly sloučeny do nového balíčku `Microsoft.AspNetCore.Server.HttpSys` . Obory názvů byly aktualizovány tak, aby odpovídaly.

Další informace najdete v tématu [ implementace webového serveruHTTP.sys v ASP.NET Core](xref:fundamentals/servers/httpsys).

## <a name="enhanced-http-header-support"></a>Rozšířená podpora hlaviček protokolu HTTP

Při použití MVC k přenosu `FileStreamResult` nebo a máte `FileContentResult` nyní možnost nastavit `ETag` nebo zadat `LastModified` Datum přenášeného obsahu. Můžete nastavit tyto hodnoty vráceného obsahu s podobným kódem jako v následujícím příkladu:

```csharp
var data = Encoding.UTF8.GetBytes("This is a sample text from a binary array");
var entityTag = new EntityTagHeaderValue("\"MyCalculatedEtagValue\"");
return File(data, "text/plain", "downloadName.txt", lastModified: DateTime.UtcNow.AddSeconds(-5), entityTag: entityTag);
```

Soubor vrácený návštěvníkům má odpovídající hlavičku protokolu HTTP pro `ETag` `LastModified` hodnoty a.

Pokud návštěvník aplikace požaduje obsah s hlavičkou požadavku na rozsah, ASP.NET Core rozpoznává požadavek a zpracuje hlavičku. Pokud lze požadovaný obsah částečně doručovat, ASP.NET Core odpovídajícím způsobem přeskočí a vrátí pouze požadovanou sadu bajtů. Nemusíte psát žádné speciální obslužné rutiny do metod pro přizpůsobení nebo zpracování této funkce; automaticky se za vás zpracuje.

## <a name="hosting-startup-and-application-insights"></a>Hostování spuštění a Application Insights

Hostující prostředí teď můžou vkládat dodatečné závislosti balíčků a spouštět kód při spuštění aplikace, aniž by aplikace musela explicitně převzít závislost nebo volat jakékoli metody. Tato funkce se dá použít k tomu, aby určitá prostředí mohla být pro toto prostředí jedinečná, aniž by aplikace musela znát čas dopředu. 

V ASP.NET Core 2,0 se tato funkce používá k automatickému povolení Application Insights diagnostiky při ladění v aplikaci Visual Studio a při spuštění v Azure App Services. V důsledku toho šablony projektu již ve výchozím nastavení nepřidává Application Insights balíčky a kód.

Informace o stavu plánované dokumentace najdete v tématu [problém GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/3389).

## <a name="automatic-use-of-anti-forgery-tokens"></a>Automatické použití odolných tokenů proti padělání

ASP.NET Core ve výchozím nastavení vždycky pomohl obsah s kódováním HTML, ale s novou verzí je potřeba další krok, který pomáhá zabránit útokům na více lokalitám proti falšování. Služba ASP.NET Core nyní ve výchozím nastavení generuje tokeny proti padělání a ověří je v akcích a stránkách vydaných formulářem bez dodatečné konfigurace.

Další informace naleznete v tématu <xref:security/anti-request-forgery>.

## <a name="automatic-precompilation"></a>Automatická předkompilace

Razor zobrazení předkompilace je ve výchozím nastavení povoleno, což snižuje velikost výstupu publikování a čas spuštění aplikace.

Další informace naleznete v tématu [ Razor zobrazení kompilace a předkompilace v ASP.NET Core](xref:mvc/views/view-compilation).

## <a name="no-locrazor-support-for-c-71"></a>Razor Podpora pro C# 7,1

RazorModul zobrazení byl aktualizován tak, aby fungoval s novým kompilátorem Roslyn. Který zahrnuje podporu pro funkce C# 7,1 jako výchozí výrazy, odvoditelné názvy řazené kolekce členů a porovnávání vzorů s obecnými typy. Chcete-li v projektu použít C# 7,1, přidejte do souboru projektu následující vlastnost a pak znovu načtěte toto řešení:

```xml
<LangVersion>latest</LangVersion>
```

Informace o stavu funkcí C# 7,1 najdete [v úložišti GitHub Roslyn](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md).

## <a name="other-documentation-updates-for-20"></a>Další aktualizace dokumentace pro 2,0

* [Publikační profily sady Visual Studio pro nasazení aplikace ASP.NET Core](xref:host-and-deploy/visual-studio-publish-profiles)
* [Správa klíčů](xref:security/data-protection/implementation/key-management)
* [Konfigurace ověřování na Facebooku](xref:security/authentication/facebook-logins)
* [Konfigurace ověřování na Twitteru](xref:security/authentication/twitter-logins)
* [Konfigurace ověřování Google](xref:security/authentication/google-logins)
* [Konfigurace ověřování účtu Microsoft](xref:security/authentication/microsoft-logins)

## <a name="migration-guidance"></a>Pokyny k migraci

Pokyny k migraci ASP.NET Corech aplikací 1. x na ASP.NET Core 2,0 naleznete v následujících zdrojích informací:

* [Migrace z ASP.NET Core 1. x na ASP.NET Core 2,0](xref:migration/1x-to-2x/index)
* [Migrace ověřování a Identity ASP.NET Core 2,0](xref:migration/1x-to-2x/identity-2x)

## <a name="additional-information"></a>Další informace

Úplný seznam změn najdete v [poznámkách k verzi pro ASP.NET Core 2,0](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0).

Pokud se chcete připojit k vývoji a plánům vývojového týmu ASP.NET Core, prolaďte si [rychlou schůzku komunitu ASP.NET](https://live.asp.net/).

---
title: Co je nového v ASP.NET Core 2,0
author: rick-anderson
description: Seznamte se s novými funkcemi v ASP.NET Core 2,0.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: aspnetcore-2.0
ms.openlocfilehash: 5ca43bab1496aa9fda65282cbb0b1177ad8689eb
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667256"
---
# <a name="whats-new-in-aspnet-core-20"></a>Co je nového v ASP.NET Core 2,0

Tento článek zvýrazňuje nejvýznamnější změny v ASP.NET Core 2,0 s odkazy na příslušnou dokumentaci.

## <a name="razor-pages"></a>Razor Pages

Razor Pages je nová funkce ASP.NET Core MVC, která usnadňuje a produktivnější vytváření kódu pro scénáře zaměřené na stránku.

Další informace najdete v úvodu a v kurzu:

* [Úvod do Razor Pages](xref:razor-pages/index)
* [Začínáme se stránkami Razor](xref:tutorials/razor-pages/razor-pages-start)

## <a name="aspnet-core-metapackage"></a>ASP.NET Core Metapackage

Nový ASP.NET Core Metapackage zahrnuje všechny balíčky vytvořené a podporované ASP.NET Core a Entity Framework Core týmy a jejich interní závislosti a závislosti třetích stran. Už nemusíte volit jednotlivé funkce ASP.NET Core podle balíčku. Všechny funkce jsou součástí balíčku [Microsoft. AspNetCore. All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) . Výchozí šablony používají tento balíček.

Další informace najdete v tématu [Microsoft. AspNetCore. All Metapackage for ASP.NET Core 2,0](xref:fundamentals/metapackage).

## <a name="runtime-store"></a>Běhové úložiště

Aplikace, které používají `Microsoft.AspNetCore.All` Metapackage, automaticky využívají nové úložiště runtime .NET Core. Úložiště obsahuje všechny prostředky modulu runtime potřebné ke spouštění aplikací ASP.NET Core 2,0. Při použití `Microsoft.AspNetCore.All` Metapackage se s aplikací nasazují žádné prostředky z odkazovaných balíčků NuGet ASP.NET Core, protože už jsou umístěné v cílovém systému. Prostředky v úložišti za běhu jsou také předem kompilovány pro zlepšení času spuštění aplikace.

Další informace najdete v tématu [běhové úložiště](/dotnet/core/deploying/runtime-store) .

## <a name="net-standard-20"></a>.NET Standard 2,0

Cílová ASP.NET Core 2,0 balíčků .NET Standard 2,0. Na balíčky můžou odkazovat jiné knihovny .NET Standard 2,0 a můžou běžet na .NET Standard 2,0 implementace platformy .NET, včetně .NET Core 2,0 a .NET Framework 4.6.1. 

`Microsoft.AspNetCore.All` Metapackage cílí pouze na rozhraní .NET Core 2,0, protože je určeno pro použití s úložištěm runtime .NET Core 2,0.

## <a name="configuration-update"></a>Aktualizace konfigurace

Do kontejneru Services je ve výchozím nastavení přidána instance `IConfiguration` v ASP.NET Core 2,0. `IConfiguration` v kontejneru Services usnadňuje aplikacím načítání konfiguračních hodnot z kontejneru.

Informace o stavu plánované dokumentace najdete v tématu [problém GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/3387).

## <a name="logging-update"></a>Aktualizace protokolování

V ASP.NET Core 2,0 je ve výchozím nastavení do systému vkládání závislostí začleněno protokolování (DI). Přidáte poskytovatele a nakonfigurujete filtrování v souboru *program.cs* místo v souboru *Startup.cs* . Výchozí `ILoggerFactory` podporuje filtrování způsobem, který umožňuje použít jeden flexibilní přístup pro filtrování křížového zprostředkovatele i pro filtrování specifického zprostředkovatele.

Další informace najdete v tématu [Úvod do protokolování](xref:fundamentals/logging/index).

## <a name="authentication-update"></a>Aktualizace ověřování

Nový model ověřování usnadňuje konfiguraci ověřování pro aplikaci pomocí typu DI.

Nové šablony jsou k dispozici pro konfiguraci ověřování pro webové aplikace a webová rozhraní API pomocí [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/).

Informace o stavu plánované dokumentace najdete v tématu [problém GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/3054).

## <a name="identity-update"></a>Aktualizace identity

Usnadnili jsme vytváření zabezpečených webových rozhraní API pomocí identity v ASP.NET Core 2,0. Přístupové tokeny pro přístup k webovým rozhraním API můžete získat pomocí [knihovny Microsoft Authentication Library (MSAL)](https://www.nuget.org/packages/Microsoft.Identity.Client).

Další informace o změnách ověřování v 2,0 najdete v následujících zdrojích informací:

* [Potvrzení účtu a obnovení hesla v ASP.NET Core](xref:security/authentication/accconfirm)
* [Povolit generování kódu QR pro ověřovací aplikace v ASP.NET Core](xref:security/authentication/identity-enable-qrcodes)
* [Migrace ověřování a identity na ASP.NET Core 2,0](xref:migration/1x-to-2x/identity-2x)

## <a name="spa-templates"></a>Šablony SPA

K dispozici jsou šablony projektů s jednou stránkou (SPA) pro úhlové, Aurelia, vyseknutí. js, reagují. js a reagují. js s Redux. Úhlová šablona byla aktualizována na úhlové 4. Šablony úhlů a reakce jsou k dispozici ve výchozím nastavení. informace o tom, jak získat další šablony, najdete v tématu [Vytvoření nového projektu Spa](xref:client-side/spa-services#create-a-new-project). Informace o tom, jak vytvořit SPA v ASP.NET Core, najdete v článku <xref:client-side/spa-services>.

## <a name="kestrel-improvements"></a>Vylepšení Kestrel

Webový server Kestrel má nové funkce, díky kterým je vhodnější jako internetový server. Do vlastnosti New `Limits` třídy `KestrelServerOptions` se přidalo několik možností konfigurace omezení serveru. Přidejte omezení pro následující:

* Maximální počet připojení klientů
* Maximální velikost textu požadavku
* Minimální rychlost těla zprávy požadavku

Další informace najdete v tématu [implementace webového serveru Kestrel v ASP.NET Core](xref:fundamentals/servers/kestrel).

## <a name="weblistener-renamed-to-httpsys"></a>Weblisten byl přejmenován na HTTP. sys

Balíčky `Microsoft.AspNetCore.Server.WebListener` a `Microsoft.Net.Http.Server` byly sloučeny do nového balíčku `Microsoft.AspNetCore.Server.HttpSys`. Obory názvů byly aktualizovány tak, aby odpovídaly.

Další informace najdete v tématu [implementace webového serveru http. sys v ASP.NET Core](xref:fundamentals/servers/httpsys).

## <a name="enhanced-http-header-support"></a>Rozšířená podpora hlaviček protokolu HTTP

Při použití MVC k přenosu `FileStreamResult` nebo `FileContentResult`teď máte možnost nastavit `ETag` nebo `LastModified` datum na přenášeném obsahu. Můžete nastavit tyto hodnoty vráceného obsahu s podobným kódem jako v následujícím příkladu:

```csharp
var data = Encoding.UTF8.GetBytes("This is a sample text from a binary array");
var entityTag = new EntityTagHeaderValue("\"MyCalculatedEtagValue\"");
return File(data, "text/plain", "downloadName.txt", lastModified: DateTime.UtcNow.AddSeconds(-5), entityTag: entityTag);
```

Soubor vrácený návštěvníkům má vhodné hlavičky protokolu HTTP pro `ETag` a `LastModified` hodnoty.

Pokud návštěvník aplikace požaduje obsah s hlavičkou požadavku na rozsah, ASP.NET Core rozpoznává požadavek a zpracuje hlavičku. Pokud lze požadovaný obsah částečně doručovat, ASP.NET Core odpovídajícím způsobem přeskočí a vrátí pouze požadovanou sadu bajtů. Nemusíte psát žádné speciální obslužné rutiny do metod pro přizpůsobení nebo zpracování této funkce; automaticky se za vás zpracuje.

## <a name="hosting-startup-and-application-insights"></a>Hostování spuštění a Application Insights

Hostující prostředí teď můžou vkládat dodatečné závislosti balíčků a spouštět kód při spuštění aplikace, aniž by aplikace musela explicitně převzít závislost nebo volat jakékoli metody. Tato funkce se dá použít k tomu, aby určitá prostředí mohla být pro toto prostředí jedinečná, aniž by aplikace musela znát čas dopředu. 

V ASP.NET Core 2,0 se tato funkce používá k automatickému povolení Application Insights diagnostiky při ladění v aplikaci Visual Studio a při spuštění v Azure App Services. V důsledku toho šablony projektu již ve výchozím nastavení nepřidává Application Insights balíčky a kód.

Informace o stavu plánované dokumentace najdete v tématu [problém GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/3389).

## <a name="automatic-use-of-anti-forgery-tokens"></a>Automatické použití odolných tokenů proti padělání

ASP.NET Core ve výchozím nastavení vždycky pomohl obsah s kódováním HTML, ale s novou verzí je potřeba další krok, který pomáhá zabránit útokům na více lokalitám proti falšování. Služba ASP.NET Core nyní ve výchozím nastavení generuje tokeny proti padělání a ověří je v akcích a stránkách vydaných formulářem bez dodatečné konfigurace.

Další informace najdete v tématu [prevence útoků proti falšování (XSRF/CSRF) žádostí mezi weby](xref:security/anti-request-forgery).

## <a name="automatic-precompilation"></a>Automatická předkompilace

Předkompilace zobrazení Razor je ve výchozím nastavení povolená, takže se zmenší velikost výstupu publikování a čas spuštění aplikace.

Další informace najdete v tématu [kompilace zobrazení Razor a předkompilace v ASP.NET Core](xref:mvc/views/view-compilation).

## <a name="razor-support-for-c-71"></a>Podpora Razor pro C# 7,1

Modul zobrazení Razor byl aktualizován tak, aby fungoval s novým kompilátorem Roslyn. Který zahrnuje podporu pro C# funkce 7,1, jako jsou výchozí výrazy, odvoditelné názvy řazené kolekce členů a porovnávání vzorů s obecnými typy. Pokud chcete C# v projektu použít 7,1, přidejte do souboru projektu následující vlastnost a pak znovu načtěte řešení:

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
* [Migrace ověřování a identity na ASP.NET Core 2,0](xref:migration/1x-to-2x/identity-2x)

## <a name="additional-information"></a>Další informace

Úplný seznam změn najdete v [poznámkách k verzi pro ASP.NET Core 2,0](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0).

Pokud se chcete připojit k vývoji a plánům vývojového týmu ASP.NET Core, prolaďte si [rychlou schůzku komunitu ASP.NET](https://live.asp.net/).

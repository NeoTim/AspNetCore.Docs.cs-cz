---
title: Migrace z ASP.NET na ASP.NET Core 2,0
author: isaac2004
description: Dostanou pokyny k migraci stávajících ASP.NET MVC nebo webových aplikací API na ASP.NET Core 2,0.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/24/2018
uid: migration/mvc2
ms.openlocfilehash: 11bd3b948afaedc675ac4249099969382683f653
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78664610"
---
# <a name="migrate-from-aspnet-to-aspnet-core-20"></a>Migrace z ASP.NET na ASP.NET Core 2,0

Od [Petr Levin](https://isaaclevin.com)

Tento článek slouží jako referenční příručka pro migraci ASP.NETch aplikací na ASP.NET Core 2,0.

## <a name="prerequisites"></a>Předpoklady

Nainstalujte **jednu** z následujících možností ze [souborů ke stažení pro rozhraní .NET: Windows](https://www.microsoft.com/net/download/windows):

* Sada .NET Core SDK
* Visual Studio pro Windows
  * **ASP.NET a webové vývojové** úlohy
  * **Vývojová úloha .NET Core pro různé platformy**

## <a name="target-frameworks"></a>Cílové architektury

Projekty ASP.NET Core 2,0 nabízejí vývojářům flexibilitu při cílení na rozhraní .NET Core, .NET Framework nebo obojí. V tématu [Výběr mezi .NET Core a .NET Framework pro serverové aplikace](/dotnet/standard/choosing-core-framework-server) určíte, které cílové rozhraní je nejvhodnější.

Při cílení na .NET Framework musí projekty odkazovat na jednotlivé balíčky NuGet.

Cílení na .NET Core vám umožní eliminovat mnoho explicitních odkazů na balíčky díky ASP.NET Core 2,0 [Metapackage](xref:fundamentals/metapackage). Do projektu nainstalujte `Microsoft.AspNetCore.All` Metapackage:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.9" />
</ItemGroup>
```

Při použití Metapackage se s aplikací nasadí žádné balíčky, na které se neodkazuje v Metapackage. Běhové úložiště .NET Core zahrnuje tyto prostředky a jsou předkompilovány pro zlepšení výkonu. Další podrobnosti najdete v tématu <xref:fundamentals/metapackage>.

## <a name="project-structure-differences"></a>Rozdíly struktury projektu

Formát souboru *. csproj* byl v ASP.NET Core zjednodušený. Mezi významné změny patří:

* Explicitní zahrnutí souborů není nutné, aby byly považovány za součást projektu. Tím se snižuje riziko konfliktů při sloučení XML při práci na velkých týmech.
* Neexistují žádné odkazy založené na identifikátorech GUID na jiné projekty, což vylepšuje čitelnost souboru.
* Soubor lze upravovat bez jeho uvolnění v aplikaci Visual Studio:

  ![Možnost úpravy kontextové nabídky příkazu CSPROJ v aplikaci Visual Studio 2017](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a>Nahrazení souboru Global. asax

ASP.NET Core zavádí nový mechanismus pro zavedení aplikace. Vstupním bodem pro aplikace ASP.NET je soubor *Global. asax* . Úlohy, jako je konfigurace směrování a registrace do oblasti, jsou zpracovávány v souboru *Global. asax* .

[!code-csharp[](samples/globalasax-sample.cs)]

Tento přístup Couples aplikaci a server, na který je nasazený, způsobem, který je v konfliktu s implementací. Ve snaze oddělit se [Owin](https://owin.org/) zavedla k tomu, aby poskytovala čisticí způsob, jak používat víc platforem dohromady. OWIN poskytuje kanál pro přidání pouze těch potřebných modulů. Hostující prostředí přijímá [spouštěcí](xref:fundamentals/startup) funkci ke konfiguraci služeb a kanálu požadavků aplikace. `Startup` registruje sadu middlewaru s aplikací. Pro každý požadavek aplikace volá každou součást middlewaru s ukazatelem na hlavní seznam s ukazatelem na existující sadu obslužných rutin. Každá součást middleware může do kanálu zpracování požadavků přidat jednu nebo více obslužných rutin. To je dosaženo vrácením odkazu na obslužnou rutinu, která je nového záhlaví seznamu. Každá obslužná rutina zodpovídá za zapamatování a vyvolání další obslužné rutiny v seznamu. Při ASP.NET Core je vstupním bodem aplikace `Startup`a již nemusíte mít závislost na *Global. asax*. Při použití OWIN s .NET Framework použijte jako kanál něco podobného jako v následujícím tvaru:

[!code-csharp[](samples/webapi-owin.cs)]

Tím se nakonfigurují výchozí trasy a ve výchozím nastavení se XmlSerialization přes JSON. Podle potřeby přidejte do tohoto kanálu další middleware (načítají se služby, nastavení konfigurace, statické soubory atd.).

ASP.NET Core používá podobný přístup, ale nespoléhá na OWIN k tomu, aby záznam zpracoval. Místo toho se provádí pomocí metody *Program.cs* `Main` (podobně jako u konzolových aplikací) a `Startup` se načítají přes něj.

[!code-csharp[](samples/program.cs)]

`Startup` musí zahrnovat metodu `Configure`. V `Configure`do kanálu přidejte potřebný middleware. V následujícím příkladu (z výchozí šablony webu) se pro konfiguraci kanálu s podporou pro použít několik metod rozšíření:

* [BrowserLink](https://vswebessentials.com/features/browserlink)
* Chybové stránky
* Statické soubory
* ASP.NET Core MVC
* Identita

[!code-csharp[](../../common/samples/WebApplication1/Startup.cs?highlight=8,9,10,14,17,19,21&start=58&end=84)]

Hostitel a aplikace se odlišily, což poskytuje flexibilitu přesunu na jinou platformu v budoucnu.

Podrobnější informace o ASP.NET Core spouštění a middlewaru najdete v článku <xref:fundamentals/startup>.

## <a name="storing-configurations"></a>Ukládání konfigurací

ASP.NET podporuje ukládání nastavení. Toto nastavení se používá například pro podporu prostředí, do kterého byly aplikace nasazeny. Běžný postup je uložit všechny vlastní páry klíč-hodnota do `<appSettings>` části souboru *Web. config* :

[!code-xml[](samples/webconfig-sample.xml)]

Aplikace čtou tato nastavení pomocí kolekce `ConfigurationManager.AppSettings` v oboru názvů `System.Configuration`:

[!code-csharp[](samples/read-webconfig.cs)]

ASP.NET Core může ukládat konfigurační data pro aplikaci do libovolného souboru a načíst je jako součást zavádění middlewaru. Výchozím souborem použitým v šablonách projektu je *appSettings. JSON*:

[!code-json[](samples/appsettings-sample.json)]

Načtení tohoto souboru do instance `IConfiguration` v rámci aplikace se provádí v *Startup.cs*:

[!code-csharp[](samples/startup-builder.cs)]

Aplikace načte z `Configuration`, aby získala nastavení:

[!code-csharp[](samples/read-appsettings.cs)]

Existují rozšíření tohoto přístupu, aby byl proces robustnější, jako je například použití [vkládání závislostí](xref:fundamentals/dependency-injection) (di) k načtení služby s těmito hodnotami. Metoda DI má k dispozici sadu konfiguračních objektů se silným typem.

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

**Poznámka:** Podrobnější informace o konfiguraci ASP.NET Core najdete v tématu <xref:fundamentals/configuration/index>.

## <a name="native-dependency-injection"></a>Vkládání nativních závislostí

Důležitým cílem při sestavování rozsáhlých škálovatelných aplikací je volně přizpůsobitelné spojování komponent a služeb. [Vkládání závislostí](xref:fundamentals/dependency-injection) je oblíbená technika pro dosažení tohoto a je to nativní součást ASP.NET Core.

V aplikacích ASP.NET se vývojáři spoléhají na knihovnu třetí strany, která implementuje vkládání závislostí. Jedna taková knihovna je [Unity](https://github.com/unitycontainer/unity), kterou poskytují vzory Microsoftu & postupy.

Příkladem nastavení vstřiku závislosti s Unity je implementace `IDependencyResolver`, která zabalí `UnityContainer`:

[!code-csharp[](samples/sample8.cs)]

Vytvořte instanci `UnityContainer`, zaregistrujte službu a nastavte překladač závislostí `HttpConfiguration` na novou instanci `UnityResolver` pro váš kontejner:

[!code-csharp[](samples/sample9.cs)]

Vložení `IProductRepository` tam, kde je potřeba:

[!code-csharp[](samples/sample5.cs)]

Protože je vkládání závislostí součástí ASP.NET Core, můžete do `Startup.ConfigureServices`přidat svou službu:

[!code-csharp[](samples/configure-services.cs)]

Úložiště může být vloženo kdekoli, stejně jako u Unity.

Další informace o vkládání závislostí v ASP.NET Core naleznete v tématu <xref:fundamentals/dependency-injection>.

## <a name="serving-static-files"></a>Obsluha statických souborů

Důležitou součástí vývoje webu je schopnost poskytovat statické prostředky na straně klienta. Nejběžnějšími příklady statických souborů jsou HTML, CSS, JavaScript a image. Tyto soubory musí být uloženy v publikovaném umístění aplikace (nebo CDN) a odkazovány, aby je bylo možné načíst pomocí žádosti. Tento proces se v ASP.NET Core změnil.

V ASP.NET jsou statické soubory uloženy v různých adresářích a odkazovány v zobrazeních.

V ASP.NET Core jsou statické soubory uloženy v kořenu "Web root" ( *&lt;kořenovém obsahu&gt;/wwwroot*), pokud nejsou nakonfigurovány jinak. Soubory jsou načteny do kanálu požadavků vyvoláním metody rozšíření `UseStaticFiles` z `Startup.Configure`:

[!code-csharp[](../../fundamentals/static-files/samples/1x/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

**Poznámka:** Pokud cílíte .NET Framework, nainstalujte balíček NuGet `Microsoft.AspNetCore.StaticFiles`.

Například prostředek obrázku ve složce *wwwroot/images* je přístupný prohlížeči v umístění, jako je například `http://<app>/images/<imageFileName>`.

**Poznámka:** Podrobný odkaz na obsluhu statických souborů v ASP.NET Core najdete v tématu <xref:fundamentals/static-files>.

## <a name="additional-resources"></a>Další zdroje

* [Portování knihoven do .NET Core](/dotnet/core/porting/libraries)

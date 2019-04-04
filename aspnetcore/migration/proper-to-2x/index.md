---
title: Migrace z ASP.NET do ASP.NET Core
author: isaac2004
description: Získat pokyny pro migraci stávajících rozhraní ASP.NET MVC nebo webového rozhraní API aplikací na Core.web technologie ASP.NET
ms.author: scaddie
ms.date: 12/11/2018
uid: migration/proper-to-2x/index
ms.openlocfilehash: 7b6aec621efa5e1400fcfd4396a2322733113ec1
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58209133"
---
# <a name="migrate-from-aspnet-to-aspnet-core"></a>Migrace z ASP.NET do ASP.NET Core

Podle [Petr Levin](https://isaaclevin.com)

Tento článek slouží jako referenční příručka pro migraci aplikace v ASP.NET do ASP.NET Core.

## <a name="prerequisites"></a>Požadavky

[.NET core SDK 2.2 nebo vyšší](https://www.microsoft.com/net/download)

## <a name="target-frameworks"></a>Cílové architektury

Projekty ASP.NET Core nabízí vývojářům flexibilitu cílí na .NET Core a .NET Framework. Zobrazit [volba mezi .NET Core a .NET Framework pro serverové aplikace](/dotnet/standard/choosing-core-framework-server) k určení, která Cílová architektura je nejvhodnější.

Při cílení na rozhraní .NET Framework, projektech muset odkazovat na jednotlivé balíčky NuGet.

Cílení na .NET Core umožňuje eliminovat řadu odkazy na balíček explicitní, díky technologii ASP.NET Core [Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app). Nainstalujte `Microsoft.AspNetCore.App` Microsoft.aspnetcore.all ve vašem projektu:

```xml
<ItemGroup>
   <PackageReference Include="Microsoft.AspNetCore.App" />
</ItemGroup>
```

Při použití Microsoft.aspnetcore.all žádné balíčky odkazuje Microsoft.aspnetcore.all nasazených s aplikací. Store modulu Runtime .NET Core zahrnuje tyto prostředky a že předkompilovaný ke zlepšení výkonu. Zobrazit [Microsoft.AspNetCore.App Microsoft.aspnetcore.all pro ASP.NET Core](xref:fundamentals/metapackage-app) další podrobnosti.

## <a name="project-structure-differences"></a>Rozdíly struktura projektu

*.Csproj* zjednodušili jsme formát souborů v ASP.NET Core. Některé důležité změny patří:

- Explicitní zařazení souborů není nezbytné pro ně být považováno za součást projektu. Tím se snižuje riziko konfliktů sloučení XML při práci na velkých týmů.
- Neexistují žádné na základě identifikátoru GUID odkazy na jiné projekty, což zlepšuje čitelnost souboru.
- Tento soubor lze upravovat bez uvolnění v sadě Visual Studio:

    ![Edit CSPROJ context menu option in Visual Studio 2017](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a>Nahrazení souboru Global.asax

ASP.NET Core zavedl nový mechanismus pro spuštění aplikace. Vstupní bod pro aplikace ASP.NET je *Global.asax* souboru. Úlohy, jako je konfigurace směrování a filtrování a oblasti registrace zachází *Global.asax* souboru.

[!code-csharp[](samples/globalasax-sample.cs)]

Tento přístup páry v odstupu aplikace a serveru, na kterém je nasazená tak, že dochází ke kolizím s implementací. Abyste mohli oddělit [OWIN](http://owin.org/) se seznámili s čistší způsob, jak použít více platforem současně. OWIN poskytuje kanál přidat pouze moduly, které jsou potřeba. Hostitelské prostředí přijímá [spuštění](xref:fundamentals/startup) funkce ke konfiguraci služby a kanál žádosti o aplikace. `Startup` registruje sadu middlewaru v aplikaci. Pro každý požadavek aplikace volá všechny komponenty middlewaru pomocí hlavního ukazatele odkazovaného seznamu do existující sady rutin. Každá komponenta middlewaru můžete přidat jeden nebo více obslužných rutin k žádosti o zpracování kanálu. Toho dosahuje tak, že vrací odkaz na obslužnou rutinu, která je na novou hlavičku seznamu. Každá obslužná rutina zodpovídá za zapamatování a vyvolání další obslužná rutina v seznamu. Pomocí ASP.NET Core je vstupním bodem k aplikaci `Startup`, a už nebude mít závislost *Global.asax*. Při použití s rozhraním .NET Framework OWIN, použijte jako kanál nějak takto:

[!code-csharp[](samples/webapi-owin.cs)]

Nakonfiguruje výchozí trasy a výchozí hodnota je XmlSerialization přes Json. Podle potřeby (načítání služeb, nastavení konfigurace, statické soubory, atd.), přidejte do tohoto kanálu další Middleware.

ASP.NET Core používá podobný přístup, ale nemusí spoléhat na OWIN pro zpracování vstupu. Místo toho, který se provádí prostřednictvím *Program.cs* `Main` – metoda (podobně jako konzolové aplikace) a `Startup` je načteno do něj.

[!code-csharp[](samples/program.cs)]

`Startup` musí obsahovat `Configure` metoda. V `Configure`, přidat nezbytné middleware do kanálu. V následujícím příkladu (z výchozí šablony webové stránky) konfiguraci metody rozšíření kanálu s podporou:

- Chybové stránky
- Zabezpečení striktní přenosu HTTP
- Přesměrování protokolu HTTP na HTTPS
- ASP.NET Core MVC

[!code-csharp[](samples/startup.cs)]

Host a aplikace byla odděleném poskytující možnost přechodu na různé platformy v budoucnu.

> [!NOTE]
> Najdete podrobnější referenční dokumentace k ASP.NET Core spuštění a Middleware, naleznete v tématu [při spuštění v ASP.NET Core](xref:fundamentals/startup)

## <a name="store-configurations"></a>Konfigurace Store

Podporuje ASP.NET ukládat nastavení. Tato nastavení se používají, například pro podporu prostředí, do kterého byly nasazené aplikace. Běžnou praxí je pro uložení všech vlastních páry klíč hodnota v `<appSettings>` část *Web.config* souboru:

[!code-xml[](samples/webconfig-sample.xml)]

Číst nastavení pomocí aplikací `ConfigurationManager.AppSettings` kolekce `System.Configuration` obor názvů:

[!code-csharp[](samples/read-webconfig.cs)]

ASP.NET Core můžete uložit konfigurační data pro aplikaci na jakýkoli soubor a načíst jako součást spuštění middlewaru. Je výchozí soubor použitý v šablonách projektů *appsettings.json*:

[!code-json[](samples/appsettings-sample.json)]

Načítají se tento soubor do instance `IConfiguration` uvnitř vaší aplikace se provádí v *Startup.cs*:

[!code-csharp[](samples/startup-builder.cs)]

Aplikace načte z `Configuration` zobrazíte nastavení:

[!code-csharp[](samples/read-appsettings.cs)]

Rozšíření tohoto přístupu, aby proces robustnější, jako je třeba použití [injektáž závislostí](xref:fundamentals/dependency-injection) (DI) pro načtení služby s těmito hodnotami. DI přístup poskytuje sadu objektů konfigurace silného typu.

````csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
````

> [!NOTE]
> Podrobnější odkazu ke konfiguraci ASP.NET Core, najdete v části [konfigurace v ASP.NET Core](xref:fundamentals/configuration/index).

## <a name="native-dependency-injection"></a>Injektáž závislostí nativní

Důležité cíle, při vytváření velké a škálovatelné aplikace je volné párování komponent a služeb. [Injektáž závislostí](xref:fundamentals/dependency-injection) je technika oblíbených pro dosažení tohoto cíle a jedná se o nativní součást ASP.NET Core.

V aplikacích technologie ASP.NET vývojáři spoléhají na knihovny třetích stran k implementaci vkládání závislostí. Jeden takový knihovna je [Unity](https://github.com/unitycontainer/unity), k dispozici ve Microsoft Patterns a postupy.

Příklad nastavení injektáž závislostí v Unity je implementace `IDependencyResolver` , která zabalí `UnityContainer`:

[!code-csharp[](samples/sample8.cs)]

Vytvoření instance vaší `UnityContainer`, zaregistrujte vaši službu a nastavit překladač závislostí `HttpConfiguration` nové instance `UnityResolver` kontejneru:

[!code-csharp[](samples/sample9.cs)]

Vložit `IProductRepository` místech:

[!code-csharp[](samples/sample5.cs)]

Protože injektáž závislostí je součástí ASP.NET Core, můžete přidat služby v `ConfigureServices` metoda *Startup.cs*:

[!code-csharp[](samples/configure-services.cs)]

Úložiště lze vloženy kdekoliv, protože dřív platilo pomocí Unity.

> [!NOTE]
> Další informace o vkládání závislostí, naleznete v tématu [injektáž závislostí](xref:fundamentals/dependency-injection).

## <a name="serve-static-files"></a>Doručování statických souborů

Důležitou součástí vývoje pro web je schopnost poskytovat statické a na straně klienta prostředky. Většina běžných příkladů statické soubory jsou HTML, CSS, Javascript a bitové kopie. Tyto soubory musí uložený v publikované umístění aplikace (nebo CDN) a odkazovat tak je možné načíst žádostí. Tento proces byl změněn v ASP.NET Core.

V technologii ASP.NET jsou statické soubory uložené v různých adresářích a odkazované v zobrazeních.

V ASP.NET Core, statické soubory se ukládají do "kořenový adresář webové" (*&lt;obsahu kořenové&gt;/wwwroot*), pokud se nenakonfiguruje. Soubory se načtou do kanálu požadavku vyvoláním `UseStaticFiles` rozšiřující metoda z `Startup.Configure`:

[!code-csharp[](../../fundamentals/static-files/samples/1x/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

> [!NOTE]
> Pokud se zaměřujete na rozhraní .NET Framework, nainstalujte balíček NuGet `Microsoft.AspNetCore.StaticFiles`.

Například prostředek obrázku v *wwwroot/imagí* , jako je přístupná v prohlížeči v umístění složka `http://<app>/images/<imageFileName>`.

> [!NOTE]
> Podrobnější odkaz na zpracování statických souborů v ASP.NET Core, najdete v části [statické soubory](xref:fundamentals/static-files).

## <a name="additional-resources"></a>Další zdroje

- [Přenos knihoven pro .NET Core](/dotnet/core/porting/libraries)

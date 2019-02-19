---
title: Injektáž závislostí do kontrolerů v ASP.NET Core
author: ardalis
description: Zjistěte, jak ASP.NET Core MVC řadiče vyžádat jejich závislosti explicitně prostřednictvím jejich konstruktory s injektáž závislostí v ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: 9d9d0a68927da62fad8df72c868eaf4b8ada440d
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410268"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a>Injektáž závislostí do kontrolerů v ASP.NET Core

<a name="dependency-injection-controllers"></a>

Podle [Steve Smith](https://ardalis.com/)

Kontrolery ASP.NET Core MVC by měl požádat o jejich závislosti explicitně prostřednictvím jejich konstruktory. V některých případech akce jednotlivých kontroleru může vyžadovat služby a nemusí mít smysl požadavku na úrovni kontroleru. V takovém případě můžete vložit službu jako parametr v metodě akce.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([stažení](xref:index#how-to-download-a-sample))

## <a name="dependency-injection"></a>Injektáž závislostí

Má integrovanou podporu pro ASP.NET Core [injektáž závislostí](../../fundamentals/dependency-injection.md), což usnadňuje aplikace pro testování a udržovat.

## <a name="constructor-injection"></a>Vkládání konstruktor

ASP.NET Core integrovanou podporu pro vkládání závislostí na základě konstruktoru rozšiřuje na kontrolery MVC. Pouhým přidáním typ služby k řadiči jako parametr konstruktoru, ASP.NET Core se pokus o vyřešení tohoto typu pomocí jeho sestavení v kontejneru služby. Služby jsou obvykle, ale ne vždy definovány, pomocí rozhraní. Například pokud aplikace obsahuje obchodní logiky, která závisí na aktuální čas, můžete vložit službu, která načte čas (nezadávejte napevno-ji), který by umožnilo testy a zajistěte tak předání implementace, které používají nastavit čas.

[!code-csharp[](dependency-injection/sample/src/ControllerDI/Interfaces/IDateTime.cs)]


Je triviální implementace rozhraní jako je ten tak, aby používala systémové hodiny za běhu:

[!code-csharp[](dependency-injection/sample/src/ControllerDI/Services/SystemDateTime.cs)]


Díky tomu na místě můžete použít službu v kontroleru. V tomto případě jsme přidali nějaké logiky do `HomeController` `Index` metodu pro zobrazení pozdrav uživateli na základě času dne.

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Controllers/HomeController.cs?highlight=8,10,12,17,18,19,20,21,22,23,24,25,26,27,28,29,30&range=1-31,51-52)]

Pokud provozujeme aplikace nyní, jsme se pravděpodobně dojde k chybě:

```
An unhandled exception occurred while processing the request.

InvalidOperationException: Unable to resolve service for type 'ControllerDI.Interfaces.IDateTime' while attempting to activate 'ControllerDI.Controllers.HomeController'.
Microsoft.Extensions.DependencyInjection.ActivatorUtilities.GetService(IServiceProvider sp, Type type, Type requiredBy, Boolean isDefaultParameterRequired)
```

K této chybě dochází, když jsme neprovedli konfiguraci služby ve službě `ConfigureServices` metoda v našich `Startup` třídy. K určení, že žádosti pro `IDateTime` měla být vyřešena pomocí instance `SystemDateTime`, přidejte zvýrazněný řádek v ukázce níže na vaše `ConfigureServices` metody:

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Startup.cs?highlight=4&range=26-27,42-44)]

> [!NOTE]
> Tento konkrétní službu může implementovat některou z několika možností různé doby života (`Transient`, `Scoped`, nebo `Singleton`). Zobrazit [injektáž závislostí](../../fundamentals/dependency-injection.md) pochopit, jak každá z těchto možností oboru ovlivní chování služby.

Jakmile služba byla nakonfigurována, spuštění aplikace a přejdete na domovskou stránku by měla zobrazit zprávy podle času podle očekávání:

![Pozdrav serveru](dependency-injection/_static/server-greeting.png)

>[!TIP]
> V tématu [testovací kontroler logiku](testing.md) se naučíte usnadňují otestovat explicitní vyžádání závislostí do kontrolerů kódu.

Vkládání předdefinovaných závislostí ASP.NET Core podporuje s pouze jediný konstruktor třídy žádosti o služby. Pokud máte více než jeden konstruktor, můžete být s oznámením výjimce:

```
An unhandled exception occurred while processing the request.

InvalidOperationException: Multiple constructors accepting all given argument types have been found in type 'ControllerDI.Controllers.HomeController'. There should only be one applicable constructor.
Microsoft.Extensions.DependencyInjection.ActivatorUtilities.FindApplicableConstructor(Type instanceType, Type[] argumentTypes, ConstructorInfo& matchingConstructor, Nullable`1[]& parameterMap)
```

Jak uvádí chybová zpráva, můžete opravit tento problém s použitím jediný konstruktor. Můžete také [nahraďte výchozí kontejner vkládání závislostí třetích stran implementace](xref:fundamentals/dependency-injection#default-service-container-replacement), řadu, které podporují víc konstruktorů.

## <a name="action-injection-with-fromservices"></a>Vkládání akce s FromServices

Někdy není nutné služba pro více než jednu akci v rámci vašeho kontroleru. V takovém případě může být vhodné vložit službu jako parametr do metody akce. Uděláte to pomocí označení parametr s atributem `[FromServices]` jak je znázorněno zde:

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Controllers/HomeController.cs?highlight=1&range=33-38)]

## <a name="accessing-settings-from-a-controller"></a>Přístup k nastavení z Kontroleru

Přístup k aplikaci nebo konfigurace nastavení z v rámci kontroleru je běžný vzor. Tento přístup měli použít možnosti vzor, podle [konfigurace](xref:fundamentals/configuration/index). Obecně by neměl požadovat nastavení přímo z řadiče pomocí vkládání závislostí. Lepším řešením je žádost `IOptions<T>` instance, kde `T` je třída konfigurace budete potřebovat.

Pro práci s možností vzoru, musíte vytvořit třídu, která představuje možnosti, jako je například tento:

[!code-csharp[](dependency-injection/sample/src/ControllerDI/Model/SampleWebSettings.cs)]

Pak je potřeba nakonfigurovat aplikaci pro použití možnosti modelu a přidat do kolekce služby ve své třídě configuration `ConfigureServices`:

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Startup.cs?highlight=3,4,5,6,9,16,19&range=14-44)]

> [!NOTE]
> V seznamu výše jsme se konfigurace aplikace pro čtení nastavení ze souboru ve formátu JSON. Můžete také nakonfigurovat nastavení zcela v kódu, jak je uvedeno ve výše uvedeném komentářem kódu. Zobrazit [konfigurace](xref:fundamentals/configuration/index) pro další možnosti konfigurace.

Po zadání objekt konfigurace silného typu (v tomto případě `SampleWebSettings`) a jeho přidání do kolekce služeb, můžete vyžádejte si ho z jakékoli Kontroleru nebo metodě akce požaduje instanci `IOptions<T>` (v tomto případě `IOptions<SampleWebSettings>`) . Následující kód ukazuje, jak jeden požadovat nastavení z kontroleru:

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Controllers/SettingsController.cs?highlight=3,5,7&range=7-22)]

Následující vzorek možnosti umožňuje nastavení a konfigurace odděleném od sebe a zajišťuje následující kontroler [oddělení oblastí zájmu](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns), protože nepotřebuje vědět, jak a kde najít nastavení informace. Také udržuje kontroler usnadňuje [Jednotkový test](testing.md), protože neexistuje žádné přímé vytváření instancí třídy nastavení v rámci třídy kontroleru.

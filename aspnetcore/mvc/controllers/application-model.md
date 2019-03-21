---
title: Práce s modelem aplikace v ASP.NET Core
author: ardalis
description: Zjistěte, jak číst a manipulace s modelem aplikace k úpravě chování prvky MVC v ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: mvc/controllers/application-model
ms.openlocfilehash: 6b0591a877c0d82e0ee6ab002eb6a6650753677b
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208593"
---
# <a name="work-with-the-application-model-in-aspnet-core"></a>Práce s modelem aplikace v ASP.NET Core

Podle [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC definuje *aplikační model* představující součásti aplikace MVC. Může číst a manipulaci s Tento model k úpravě chování prvky MVC. Ve výchozím nastavení MVC dodržovat určité konvence určíte, které třídy jsou považovány za řadičích, které metody na tyto třídy jsou akce a chování parametry a směrování. Toto chování tak, aby odpovídaly potřebám vaší aplikace tak, že vytvoření vlastní zásady odvíjející a jejich použití globálně, nebo jako atributy můžete přizpůsobit.

## <a name="models-and-providers"></a>Modely a poskytovatelé

Model aplikace ASP.NET Core MVC obsahovat abstraktní rozhraní a třídy konkrétní implementace, které popisují aplikaci MVC. Tento model je výsledkem zjišťování kontrolerů, akce, parametry akce, trasy a filtry podle výchozích konvencí aplikace MVC. Při práci s modelem aplikace, můžete upravit svou aplikaci do různých odpovídají konvencím výchozí chování MVC. Parametry, názvy, cesty a filtry se používají jako konfigurační data pro akce a kontrolery.

Model aplikace ASP.NET Core MVC zahrnuje následující strukturu:

* ApplicationModel
  * Kontrolery (ControllerModel)
    * Akce (ActionModel)
      * Parametry (ParameterModel)

Každou úroveň modelu má přístup do společného `Properties` kolekce a nižších úrovních můžete používat a přepsat hodnoty vlastností nastavené ve vyšší úrovně v hierarchii. Vlastnosti jsou zachované `ActionDescriptor.Properties` při vytváření akce. Potom při zpracování požadavku, všechny vlastnosti konvence přidá nebo upraví přístupné prostřednictvím `ActionContext.ActionDescriptor.Properties`. Pomocí vlastností je skvělý způsob, jak nakonfigurovat na základě akcích filtry, vazače modelů a podobně.

> [!NOTE]
> `ActionDescriptor.Properties` Kolekce není bezpečná (pro zápis) po dokončení spuštění aplikace. Konvence jsou nejlepší způsob, jak bezpečně přidat data do této kolekce.

### <a name="iapplicationmodelprovider"></a>IApplicationModelProvider

ASP.NET Core MVC načte aplikaci modelu s použitím vzoru zprostředkovatele určené [IApplicationModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelprovider) rozhraní. Tato část popisuje některé podrobnosti interní implementace toho, jak tato funkce poskytovatele. Toto je rozšířená – většina aplikací, které využívají model aplikace by to tak, že práce s konvencí.

Implementace `IApplicationModelProvider` rozhraní "zabalení" mezi sebou, s každé volání implementace `OnProvidersExecuting` ve vzestupném pořadí na základě jeho `Order` vlastnost. `OnProvidersExecuted` Metoda je volána poté v obráceném pořadí. Rozhraní definuje několik poskytovatelů:

První (`Order=-1000`):

* [`DefaultApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.defaultapplicationmodelprovider)

Potom (`Order=-990`):

* [`AuthorizationApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.authorizationapplicationmodelprovider)
* [`CorsApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.cors.internal.corsapplicationmodelprovider)

> [!NOTE]
> Pořadí, ve které dva poskytovatelé se stejnou hodnotou pro `Order` se nazývají není definována a proto byste se neměli spoléhat.

> [!NOTE]
> `IApplicationModelProvider` je rozšířené koncept pro autory framework rozšířit. Obecně platí aplikace by měly používat konvence a rozhraní by měl používat poskytovatele. Klíče rozdíl je, že poskytovatelů vždy spuštěn konvence.

`DefaultApplicationModelProvider` Zavádí řadu výchozí chování používat ASP.NET Core MVC. Mezi jeho povinnosti patří:

* Přidání globální filtry do kontextu
* Přidání řadiče do kontextu
* Přidání kontroleru veřejné metody jako akce
* Přidání parametrů metody akce v kontextu
* Použití postupu a dalších atributů

Některé vestavěným chováním jsou implementované `DefaultApplicationModelProvider`. Tento zprostředkovatel je zodpovědný za vytváření [ `ControllerModel` ](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.controllermodel), které zase odkazuje [ `ActionModel` ](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.actionmodel#Microsoft_AspNetCore_Mvc_ApplicationModels_ActionModel), [ `PropertyModel` ](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.propertymodel), a [ `ParameterModel` ](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.parametermodel#Microsoft_AspNetCore_Mvc_ApplicationModels_ParameterModel) instancí. `DefaultApplicationModelProvider` Třída je podrobnosti implementace interní rámec, můžete a bude v budoucnu změnit. 

`AuthorizationApplicationModelProvider` Zodpovídá za použití chování asociovaných s `AuthorizeFilter` a `AllowAnonymousFilter` atributy. [Další informace o těchto atributů](xref:security/authorization/simple).

`CorsApplicationModelProvider` Implementuje chování asociovaných s `IEnableCorsAttribute` a `IDisableCorsAttribute`a `DisableCorsAuthorizationFilter`. [Další informace o CORS](xref:security/cors).

## <a name="conventions"></a>Konvence

Aplikační model definuje abstrakce konvence, které poskytují jednodušší způsob, jak přizpůsobit chování modely než přepisování celého modelu nebo zprostředkovatele. Tato abstrakce jsou doporučeným způsobem, jak změnit chování vaší aplikace. Konvence poskytují způsob, jak můžete napsat kód, který se bude dynamicky použít vlastní nastavení. Zatímco [filtry](xref:mvc/controllers/filters) umožňují měnit chování rozhraní framework, přizpůsobení umožňují řídit, jak společně drátové celé aplikace.

K dispozici jsou následující konvence:

* [`IApplicationModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelconvention)
* [`IControllerModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.icontrollermodelconvention)
* [`IActionModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iactionmodelconvention)
* [`IParameterModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iparametermodelconvention)

Konvence aplikují jejich přidáním do možnosti MVC nebo implementací `Attribute`s a jejich použití kontrolerů, akce nebo parametry akce (podobně jako [ `Filters` ](xref:mvc/controllers/filters)). Na rozdíl od filtry provádějí pouze konvence při spuštění aplikace, nikoli jako součást každého požadavku.

### <a name="sample-modifying-the-applicationmodel"></a>Ukázka: Úpravy ApplicationModel

Tato konvence slouží k přidání vlastnosti do aplikačního modelu. 

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ApplicationDescription.cs)]

Vytváření modelu aplikací se použijí jako možnosti při přidání MVC v `ConfigureServices` v `Startup`.

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=5)]

Vlastnosti jsou přístupné `ActionDescriptor` kolekci vlastností v rámci akce kontroleru:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/AppModelController.cs?name=AppModelController)]

### <a name="sample-modifying-the-controllermodel-description"></a>Ukázka: Úprava popisu ControllerModel

Stejně jako v předchozím příkladu je model řadič také upravit zahrnout vlastní vlastnosti. Toto přepíše existující vlastnosti se stejným názvem zadáno v modelu aplikací. Následující atribut konvence přidá popis na úrovni kontroleru:

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ControllerDescriptionAttribute.cs)]

Tato konvence je použitý jako atribut na kontroleru.

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=ControllerDescription&highlight=1)]

Vlastnost "description" pracuje stejným způsobem jako v předchozích příkladech.

### <a name="sample-modifying-the-actionmodel-description"></a>Ukázka: Úprava popisu ActionModel

Konvence samostatný atribut lze použít pro jednotlivé akce přepsání nastavení už používá na úrovni aplikace nebo kontroleru.

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ActionDescriptionAttribute.cs)]

Použití na akci v rámci kontroleru předchozí příklad ukazuje, jak přepíše konvence úrovni kontroleru:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=DescriptionAttributesController&highlight=9)]

### <a name="sample-modifying-the-parametermodel"></a>Ukázka: Úpravy ParameterModel

Tato konvence můžete použít pro parametry akce k úpravě svých `BindingInfo`. Tato konvence vyžaduje, aby parametr trasa parameter; Další možné zdroje vazby (například hodnoty řetězce dotazu) jsou ignorovány.

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/MustBeInRouteParameterModelConvention.cs)]

Atribut může být použitý u parametru všechny akce:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/ParameterModelController.cs?name=ParameterModelController&highlight=5)]

### <a name="sample-modifying-the-actionmodel-name"></a>Ukázka: Změna názvu ActionModel

Upraví následující konvence `ActionModel` aktualizovat *název* akce, na který se použije. Nový název je zadat jako parametr atributu. Tento nový název se používá ve směrování, takže bude mít vliv na trasy použité k dosažení této metodě akce.

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/CustomActionNameAttribute.cs)]

Tento atribut se používá pro metodu akce v `HomeController`:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/HomeController.cs?name=ActionModelConvention&highlight=2)]

I když je název metody `SomeName`, atribut přepíše konvenci MVC pomocí názvu metody a nahradí název akce s `MyCoolAction`. Díky tomu se trasy použité k dosažení této akce je `/Home/MyCoolAction`.

> [!NOTE]
> V tomto příkladu je v podstatě totéž jako použití integrovaného [ActionName](/dotnet/api/microsoft.aspnetcore.mvc.actionnameattribute) atribut.

### <a name="sample-custom-routing-convention"></a>Ukázka: Vlastní konvenci směrování

Můžete použít `IApplicationModelConvention` přizpůsobit způsob směrování funguje. Například následující konvence bude obsahovat řadiče obory názvů do jejich trasy nahrazení `.` v oboru názvů s `/` v postupu:

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/NamespaceRoutingConvention.cs)]

Tato konvence je přidán jako možnost v při spuštění.

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=6)]

> [!TIP]
> Můžete přidat konvence mají vaše [middleware](xref:fundamentals/middleware/index) díky přístupu do `MvcOptions` pomocí `services.Configure<MvcOptions>(c => c.Conventions.Add(YOURCONVENTION));`

Tento příklad se týká tato konvence trasy, které nepoužívají atribut směrování, kdy má "Namespace" v názvu kontroleru. Následující kontroler ukazuje tato konvence:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/NamespaceRoutingController.cs?highlight=7-8)]

## <a name="application-model-usage-in-webapicompatshim"></a>Používání modelu aplikací v WebApiCompatShim

ASP.NET Core MVC používá jinou sadu konvence z ASP.NET Web API 2. Pomocí vlastních konvencí, můžete upravit aplikaci ASP.NET Core MVC chování, aby byla konzistentní se u aplikace webového rozhraní API. Microsoft se dodává [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim/) speciálně pro tento účel.

> [!NOTE]
> Další informace o [migrace z rozhraní ASP.NET Web API](xref:migration/webapi).

Chcete-li použít překrytí kompatibility webové rozhraní API, budete muset přidat do projektu balíček a pak přidejte konvencí do MVC voláním `AddWebApiConventions` v `Startup`:

```csharp
services.AddMvc().AddWebApiConventions();
```

Konvence poskytované překrytí se použijí jenom k různým částem aplikace, které jste využili určité atributy použité na ně. Následující čtyři atributy se používají pro ovládací prvek, který by měl jejich konvence Autor změny překrytí konvence mají řadiče:

* [UseWebApiActionConventionsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiactionconventionsattribute)
* [UseWebApiOverloadingAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapioverloadingattribute)
* [UseWebApiParameterConventionsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiparameterconventionsattribute)
* [UseWebApiRoutesAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiroutesattribute)

### <a name="action-conventions"></a>Akce konvence

`UseWebApiActionConventionsAttribute` Se používá k mapování metody HTTP akce na základě jejich názvu (například `Get` by mapování na `HttpGet`). Platí jenom pro akce, které nepoužívají směrováním atributů.

### <a name="overloading"></a>Přetížení

`UseWebApiOverloadingAttribute` Se používá k aplikování `WebApiOverloadingApplicationModelConvention` konvence. Tato konvence přidá `OverloadActionConstraint` akce výběru procesu, což omezuje Release candidate akce pro ty, pro které požadavek splňuje všechny povinné parametry.

### <a name="parameter-conventions"></a>Parametr konvence

`UseWebApiParameterConventionsAttribute` Se používá k aplikování `WebApiParameterConventionsApplicationModelConvention` konvence akce. Tato konvence Určuje, že jednoduché typy použité jako parametry akce jsou vázány z identifikátoru URI ve výchozím nastavení, zatímco komplexní typy, které jsou vázány z textu požadavku.

### <a name="routes"></a>Trasy

`UseWebApiRoutesAttribute` Ovládací prvky, zda `WebApiApplicationModelConvention` použity konvence kontroleru. Pokud povolena, tato konvence slouží k přidání podpory pro [oblasti](xref:mvc/controllers/areas) trasy.

Kromě sadu pravidel, obsahuje balíček kompatibility `System.Web.Http.ApiController` základní třída, která nahradí poskytuje webové rozhraní API. To umožňuje kontrolerům napsané pro webové rozhraní API a dědí z jeho `ApiController` fungovat jako byly navrženy, při spuštění na rozhraní ASP.NET Core MVC. Tato třída base kontroleru je upravený se všemi `UseWebApi*` atributy uvedené výše. `ApiController` Zveřejňuje vlastnosti, metody a typy výsledků, které jsou kompatibilní s výstrahám nacházejícím se v rozhraní Web API.

## <a name="using-apiexplorer-to-document-your-app"></a>Pomocí ApiExplorer do dokumentu aplikace

Zpřístupňuje aplikačního modelu [ `ApiExplorer` ](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.apiexplorermodel) vlastnosti na každé úrovni, který lze použít k procházení struktury aplikace. To je možné použít k [generování stránek nápovědy pro webová rozhraní API pomocí nástroje jako Swagger](xref:tutorials/web-api-help-pages-using-swagger). `ApiExplorer` Zpřístupňuje vlastnost `IsVisible` vlastnost, která lze použít k určení, které části modelu vaší aplikace by měly být zveřejněny. Můžete nakonfigurovat tato nastavení pomocí konvence:

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/EnableApiExplorerApplicationConvention.cs)]

Pomocí tohoto přístupu (a další vytváření názvů v případě potřeby), můžete povolit nebo zakázat viditelnost rozhraní API na libovolné úrovni v rámci vaší aplikace. 

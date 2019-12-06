---
title: Práce s modelem aplikace v ASP.NET Core
author: ardalis
description: Naučte se číst model aplikace a manipulovat s ním, abyste mohli změnit způsob, jakým se prvky MVC chovají v ASP.NET Core.
ms.author: riande
ms.date: 12/05/2019
uid: mvc/controllers/application-model
ms.openlocfilehash: 4b6c978e5752eb320412a1c204df8e3d288fe4a1
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74881095"
---
# <a name="work-with-the-application-model-in-aspnet-core"></a>Práce s modelem aplikace v ASP.NET Core

[Steve Smith](https://ardalis.com/)

ASP.NET Core MVC definuje *model aplikace* , který představuje komponenty aplikace MVC. Tento model můžete číst a manipulovat s úpravami způsobu, jakým se chovají prvky MVC. Ve výchozím nastavení MVC používá určité konvence k určení, které třídy jsou považovány za řadiče, které metody na těchto třídách jsou akce a jak se chovají parametry a směrování. Toto chování můžete přizpůsobit tak, aby vyhovovalo potřebám vaší aplikace vytvořením vlastních konvencí a jejich použitím globálně nebo jako atributů.

## <a name="models-and-providers"></a>Modely a zprostředkovatelé

Model aplikace ASP.NET Core MVC zahrnuje jak abstraktní rozhraní, tak konkrétní implementační třídy, které popisují aplikaci MVC. Tento model je výsledkem, který MVC zjišťuje řadiče aplikace, akce, parametry akcí, trasy a filtry v závislosti na výchozích konvencích. Když pracujete s modelem aplikace, můžete upravit svou aplikaci tak, aby následovala různé konvence od výchozího chování MVC. Parametry, názvy, trasy a filtry se používají jako konfigurační data pro akce a řadiče.

Model aplikace ASP.NET Core MVC má následující strukturu:

* ApplicationModel
  * Řadiče (ControllerModel)
    * Akce (ActionModel)
      * Parametry (ParameterModel)

Každá úroveň modelu má přístup ke společné kolekci `Properties` a nižší úrovně mají přístup k hodnotám vlastností nastaveným pomocí vyšších úrovní v hierarchii a můžou je přepsat. Vlastnosti jsou trvale uložené v `ActionDescriptor.Properties` při vytváření akcí. Po zpracování žádosti se pak k jakýmkoli vlastnostem, ke které je přidaný nebo upravená konvence, dostanete prostřednictvím `ActionContext.ActionDescriptor.Properties`. Použití vlastností je skvělý způsob konfigurace filtrů, vazeb modelů atd. na základě jednotlivých akcí.

> [!NOTE]
> Kolekce `ActionDescriptor.Properties` není bezpečná pro přístup z více vláken (pro zápisy) po dokončení spuštění aplikace. Konvence představují nejlepší způsob, jak bezpečně přidat data do této kolekce.

### <a name="iapplicationmodelprovider"></a>IApplicationModelProvider

ASP.NET Core MVC načte aplikační model pomocí vzoru poskytovatele definovaného rozhraním [IApplicationModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelprovider) . Tato část se zabývá některými interními podrobnými informacemi o tom, jak tento poskytovatel funguje. Toto je pokročilé téma – většina aplikací, které využívají aplikační model, by tak měly fungovat pomocí konvencí.

Implementace rozhraní `IApplicationModelProvider` "Wrap" navzájem a každá implementace volá `OnProvidersExecuting` ve vzestupném pořadí podle jeho vlastnosti `Order`. Metoda `OnProvidersExecuted` je pak volána v obráceném pořadí. Rozhraní definuje několik zprostředkovatelů:

První (`Order=-1000`):

* [`DefaultApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.defaultapplicationmodelprovider)

Pak (`Order=-990`):

* [`AuthorizationApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.authorizationapplicationmodelprovider)
* [`CorsApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.cors.internal.corsapplicationmodelprovider)

> [!NOTE]
> Pořadí, ve kterém jsou volány dva zprostředkovatelé se stejnou hodnotou pro `Order`, nejsou definovány, a proto by se neměly spoléhat na.

> [!NOTE]
> `IApplicationModelProvider` je pokročilý koncept pro rozšíření pro autory architektury. Obecně platí, že aplikace by měly používat konvence a rozhraní, které by měly používat poskytovatele. Klíčovým rozdílem je to, že poskytovatelé se vždycky spouštějí před konvencemi.

`DefaultApplicationModelProvider` zavádí mnoho výchozích chování používaných ASP.NET Core MVC. Mezi tyto povinnosti patří:

* Přidávání globálních filtrů do kontextu
* Přidání řadičů do kontextu
* Přidání metod veřejného kontroleru jako akcí
* Přidání parametrů metody akce do kontextu
* Použití směrování a dalších atributů

Některá Vestavěná chování jsou implementována `DefaultApplicationModelProvider`. Tento zprostředkovatel zodpovídá za vytváření [`ControllerModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.controllermodel), což zase odkazuje na instance [`ActionModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.actionmodel), [`PropertyModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.propertymodel)a [`ParameterModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.parametermodel) . Třída `DefaultApplicationModelProvider` je podrobnosti implementace interního rozhraní, které se mohou v budoucnu změnit. 

`AuthorizationApplicationModelProvider` zodpovídá za použití chování přidruženého k atributům `AuthorizeFilter` a `AllowAnonymousFilter`. [Přečtěte si další informace o těchto atributech](xref:security/authorization/simple).

`CorsApplicationModelProvider` implementuje chování spojené s `IEnableCorsAttribute` a `IDisableCorsAttribute`a `DisableCorsAuthorizationFilter`. [Přečtěte si další informace o CORS](xref:security/cors).

## <a name="conventions"></a>Konvence

Model aplikace definuje abstrakce konvence, které poskytují jednodušší způsob přizpůsobení chování modelů, než je přepsání celého modelu nebo poskytovatele. Tyto abstrakce jsou doporučeným způsobem, jak upravit chování vaší aplikace. Konvence poskytují způsob, jak napsat kód, který bude dynamicky používat vlastní nastavení. [Filtry](xref:mvc/controllers/filters) sice poskytují prostředky pro úpravu chování rozhraní. vlastní nastavení vám umožní řídit, jak celá aplikace funguje dohromady.

K dispozici jsou následující konvence:

* [`IApplicationModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelconvention)
* [`IControllerModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.icontrollermodelconvention)
* [`IActionModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iactionmodelconvention)
* [`IParameterModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iparametermodelconvention)

Konvence se aplikují přidáním do možností MVC nebo implementací `Attribute`s a jejich aplikováním na řadiče, akce nebo parametry akce (podobně jako [`Filters`](xref:mvc/controllers/filters)). Na rozdíl od filtrů se konvence spouští jenom při spuštění aplikace, ne jako součást každé žádosti.

### <a name="sample-modifying-the-applicationmodel"></a>Ukázka: Změna ApplicationModel

Následující konvence se používá k přidání vlastnosti do aplikačního modelu. 

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ApplicationDescription.cs)]

Konvence aplikačního modelu se aplikují jako možnosti při přidání MVC do `ConfigureServices` v `Startup`.

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=5)]

Vlastnosti jsou přístupné z kolekce vlastností `ActionDescriptor` v rámci akcí kontroleru:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/AppModelController.cs?name=AppModelController)]

### <a name="sample-modifying-the-controllermodel-description"></a>Ukázka: Změna popisu ControllerModel

Jak je uvedeno v předchozím příkladu, model kontroleru se taky dá upravit tak, aby zahrnoval vlastní vlastnosti. Přepíší se stávající vlastnosti se stejným názvem zadaným v aplikačním modelu. Následující atribut konvence přidá popis na úrovni kontroleru:

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ControllerDescriptionAttribute.cs)]

Tato konvence se používá jako atribut na řadiči.

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=ControllerDescription&highlight=1)]

K vlastnosti Description je přistup stejným způsobem jako v předchozích příkladech.

### <a name="sample-modifying-the-actionmodel-description"></a>Ukázka: Změna popisu ActionModel

Samostatnou konvenci atributu lze použít na jednotlivé akce, přepisující chování, které již bylo použito na úrovni aplikace nebo kontroleru.

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ActionDescriptionAttribute.cs)]

Použití této akce u kontroleru v předchozím příkladu ukazuje, jak Přepisuje konvenci na úrovni řadiče:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=DescriptionAttributesController&highlight=9)]

### <a name="sample-modifying-the-parametermodel"></a>Ukázka: Změna ParameterModel

Následující konvenci lze použít na parametry akcí pro úpravu jejich `BindingInfo`. Následující konvence vyžaduje, aby parametr byl parametrem trasy; Další možné zdroje vazeb (například hodnoty řetězce dotazu) se ignorují.

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/MustBeInRouteParameterModelConvention.cs)]

Atribut lze použít pro libovolný parametr akce:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/ParameterModelController.cs?name=ParameterModelController&highlight=5)]

### <a name="sample-modifying-the-actionmodel-name"></a>Ukázka: Změna názvu ActionModel

Následující konvence upraví `ActionModel`, aby aktualizovala *název* akce, na kterou se aplikuje. Nový název je zadán jako parametr atributu. Tento nový název je používán směrováním, takže bude mít vliv na trasu použitou k dosažení této metody akce.

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/CustomActionNameAttribute.cs)]

Tento atribut se používá pro metodu akce v `HomeController`:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/HomeController.cs?name=ActionModelConvention&highlight=2)]

I když je název metody `SomeName`, atribut přepíše konvenci MVC pomocí názvu metody a nahradí název akce `MyCoolAction`. Proto je trasa použitá k dosažení této akce `/Home/MyCoolAction`.

> [!NOTE]
> Tento příklad je v podstatě stejný jako při použití předdefinovaného atributu [Action](/dotnet/api/microsoft.aspnetcore.mvc.actionnameattribute) .

### <a name="sample-custom-routing-convention"></a>Ukázka: vlastní konvence směrování

K přizpůsobení fungování směrování můžete použít `IApplicationModelConvention`. Například následující konvence zahrne do svých tras obory názvů Controllers a nahradí `.` v oboru názvů pomocí `/` v trase:

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/NamespaceRoutingConvention.cs)]

Konvence je přidána jako možnost při spuštění.

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=6)]

> [!TIP]
> K vašemu [middlewaru](xref:fundamentals/middleware/index) můžete přidat konvence tím, že získáte přístup k `MvcOptions` pomocí `services.Configure<MvcOptions>(c => c.Conventions.Add(YOURCONVENTION));`

Tato ukázka používá tuto konvenci na trasy, které nepoužívají směrování atributů, kde Controller má v názvu "Namespace". Následující kontroler znázorňuje tuto konvenci:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/NamespaceRoutingController.cs?highlight=7-8)]

## <a name="application-model-usage-in-webapicompatshim"></a>Využití aplikačního modelu v WebApiCompatShim

ASP.NET Core MVC používá jinou sadu konvencí z webového rozhraní API 2 pro ASP.NET. Pomocí vlastních konvencí můžete upravit chování aplikace ASP.NET Core MVC tak, aby byla konzistentní s tím, jak je aplikace webového rozhraní API. Společnost Microsoft dodává [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim/) speciálně pro tento účel.

> [!NOTE]
> Přečtěte si další informace o [migraci z webového rozhraní API ASP.NET](xref:migration/webapi).

Chcete-li použít překrytí kompatibility webového rozhraní API, je nutné přidat balíček do projektu a pak přidat konvence do MVC voláním `AddWebApiConventions` v `Startup`:

```csharp
services.AddMvc().AddWebApiConventions();
```

Konvence překrytí se aplikují jenom na části aplikace, u kterých se používají určité atributy. Následující čtyři atributy slouží k řízení, které řadiče by měly mít své konvence upravené konvencemi překrytí:

* [UseWebApiActionConventionsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiactionconventionsattribute)
* [UseWebApiOverloadingAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapioverloadingattribute)
* [UseWebApiParameterConventionsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiparameterconventionsattribute)
* [UseWebApiRoutesAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiroutesattribute)

### <a name="action-conventions"></a>Konvence akcí

`UseWebApiActionConventionsAttribute` slouží k mapování metody HTTP na akce založené na jejich názvu (například `Get` by se namapovalo na `HttpGet`). Vztahuje se pouze na akce, které nepoužívají směrování atributů.

### <a name="overloading"></a>Přetížení

`UseWebApiOverloadingAttribute` slouží k použití konvence `WebApiOverloadingApplicationModelConvention`. Tato konvence přidává `OverloadActionConstraint` do procesu výběru akce, který omezuje akce kandidátů na ty, pro které požadavek splňuje všechny nepovinné parametry.

### <a name="parameter-conventions"></a>Konvence parametrů

`UseWebApiParameterConventionsAttribute` slouží k použití konvence akce `WebApiParameterConventionsApplicationModelConvention`. Tato konvence určuje, že jednoduché typy používané jako parametry akce jsou ve výchozím nastavení vázány z identifikátoru URI, zatímco komplexní typy jsou vázány z textu žádosti.

### <a name="routes"></a>Trasy

`UseWebApiRoutesAttribute` určuje, zda je použita konvence kontroleru `WebApiApplicationModelConvention`. V případě povolení se tato konvence používá k přidání podpory pro [oblasti](xref:mvc/controllers/areas) do trasy.

Kromě sady konvencí zahrnuje balíček kompatibility `System.Web.Http.ApiController` základní třídu, která nahrazuje rozhraní API, které poskytuje webové rozhraní API. To umožňuje řadičům napsaným pro webové rozhraní API a dědění z jeho `ApiController`, aby fungovalo tak, jak byly navrženy a běžely na ASP.NET Core MVC. Všechny výše uvedené atributy `UseWebApi*` jsou aplikovány na základní třídu kontroleru. `ApiController` zpřístupňuje vlastnosti, metody a typy výsledků, které jsou kompatibilní s typy nalezenými ve webovém rozhraní API.

## <a name="using-apiexplorer-to-document-your-app"></a>Použití ApiExplorer k dokumentování aplikace

Aplikační model zpřístupňuje vlastnost [`ApiExplorer`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.apiexplorermodel) na každé úrovni, kterou lze použít k procházení struktury aplikace. To lze použít ke [generování stránek s nápovědu pro vaše webová rozhraní API pomocí nástrojů jako Swagger](xref:tutorials/web-api-help-pages-using-swagger). Vlastnost `ApiExplorer` zpřístupňuje vlastnost `IsVisible`, kterou lze nastavit tak, aby určovala, které části modelu vaší aplikace by měly být vystaveny. Toto nastavení můžete nakonfigurovat pomocí konvence:

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/EnableApiExplorerApplicationConvention.cs)]

Pomocí tohoto přístupu (a v případě potřeby další konvence) můžete viditelnost rozhraní API povolit nebo zakázat v libovolné úrovni aplikace. 

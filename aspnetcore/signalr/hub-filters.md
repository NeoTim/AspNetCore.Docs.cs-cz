---
title: Použití filtrů centra v ASP.NET CoreSignalR
author: brecon
description: Naučte se používat filtry centra v ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-5.0'
ms.author: brecon
ms.custom: mvc
ms.date: 05/22/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/hub-filters
ms.openlocfilehash: afdb52039c0eff53a421038518c687c78e1d509b
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2020
ms.locfileid: "84756064"
---
# <a name="use-hub-filters-in-aspnet-core-signalr"></a>Použití filtrů centra v ASP.NET CoreSignalR

Filtry centra:

* Jsou k dispozici v ASP.NET Core 5,0 nebo novějším.
* Povolí spuštění logiky před a po vyvoláním metod centra klienty.

Tento článek poskytuje pokyny pro psaní a používání filtrů centra.

## <a name="configure-hub-filters"></a>Konfigurace filtrů centra

Filtry centra lze použít globálně nebo podle typu rozbočovače. Pořadí, ve kterém jsou filtry přidány, je pořadí, ve kterém jsou filtry spouštěny. Globální filtry centra se spouštějí před filtry místních rozbočovačů.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(options =>
    {
        // Global filters will run first
        options.AddFilter<CustomFilter>();
    }).AddHubOptions<ChatHub>(options =>
    {
        // Local filters will run second
        options.AddFilter<CustomFilter2>();
    });
}
```

Filtr centra lze přidat jedním z následujících způsobů:

* Přidejte filtr podle konkrétního typu:

    ```csharp
    hubOptions.AddFilter<TFilter>();
    ```

    Tato akce bude vyřešena z vkládání závislostí (DI) nebo aktivovaného typu.

* Přidat filtr podle typu modulu runtime:

    ```csharp
    hubOptions.AddFilter(typeof(TFilter));
    ```

    Tato akce bude vyřešena z typu DI nebo aktivované.

* Přidejte filtr podle instance:

    ```csharp
    hubOptions.AddFilter(new MyFilter());
    ```

    Tato instance bude použita jako typ singleton. Všechna volání metod centra budou používat stejnou instanci.

Filtry centra jsou vytvářeny a uvolněny pro každé vyvolání centra. Pokud chcete uložit globální stav do filtru nebo žádný stav, přidejte typ filtru centra jako typ "DI" jako typ singleton pro lepší výkon. Případně přidejte filtr jako instanci, pokud můžete.

## <a name="create-hub-filters"></a>Vytváření filtrů centra

Vytvořte filtr tím, že deklarujete třídu, která dědí z `IHubFilter` , a přidejte `InvokeMethodAsync` metodu. K dispozici je také `OnConnectedAsync` `OnDisconnectedAsync` možnost, která může být volitelně implementována pro zabalení `OnConnectedAsync` metody a v `OnDisconnectedAsync` uvedeném pořadí.

```csharp
public class CustomFilter : IHubFilter
{
    public async ValueTask<object> InvokeMethodAsync(
        HubInvocationContext invocationContext, Func<HubInvocationContext, ValueTask<object>> next)
    {
        Console.WriteLine($"Calling hub method '{invocationContext.HubMethodName}'");
        try
        {
            return await next(invocationContext);
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Exception calling '{invocationContext.HubMethodName}'");
            throw ex;
        }
    }

    // Optional method
    public Task OnConnectedAsync(HubLifetimeContext context, Func<HubLifetimeContext, Task> next)
    {
        return next(context);
    }

    // Optional method
    public Task OnDisconnectedAsync(
        HubLifetimeContext context, Exception exception, Func<HubLifetimeContext, Exception, Task> next)
    {
        return next(context, exception);
    }
}
```

Filtry jsou velmi podobné middlewaru. `next`Metoda vyvolá další filtr. Výsledný filtr vyvolá metodu hub. Filtry mohou také uložit výsledek z `next` logice and run Logic po volání metody centra před vrácením výsledku z `next` .

Chcete-li přeskočit vyvolání metody centra ve filtru, vyvolejte výjimku typu `HubException` místo volání `next` . Klient obdrží chybu, pokud očekával výsledek.

## <a name="use-hub-filters"></a>Použití filtrů centra

Při psaní logiky filtru se pokuste vytvořit obecné pomocí atributů na metodách rozbočovače namísto kontroly názvů metod rozbočovače.

Vezměte v úvahu filtr, který zkontroluje argument metody centra pro zakázané fráze a nahradí všechny fráze, které nalezne `***` .
V tomto příkladu předpokládáme, že `LanguageFilterAttribute` je definována třída. Třída má vlastnost s názvem `FilterArgument` , která může být nastavena při použití atributu.

1. Umístěte atribut na metodu centra s řetězcovým argumentem, který se má vyčistit:

    ```csharp
    public class ChatHub
    {
        [LanguageFilter(filterArgument: 0)]
        public async Task SendMessage(string message, string username)
        {
            await Clients.All.SendAsync("SendMessage", $"{username} says: {message}");
        }
    }
    ```

1. Definujte filtr centra pro kontrolu atributu a nahraďte zakázané fráze v argumentu metody centra pomocí `***` :

    ```csharp
    public class LanguageFilter : IHubFilter
    {
        // populated from a file or inline
        private List<string> bannedPhrases = new List<string> { "async void", ".Result" };

        public async ValueTask<object> InvokeMethodAsync(HubInvocationContext invocationContext, 
            Func<HubInvocationContext, ValueTask<object>> next)
        {
            var languageFilter = (LanguageFilterAttribute)Attribute.GetCustomAttribute(
                invocationContext.HubMethod, typeof(LanguageFilterAttribute));
            if (languageFilter != null &&
                invocationContext.HubMethodArguments.Count > languageFilter.FilterArgument &&
                invocationContext.HubMethodArguments[languageFilter.FilterArgument] is string str)
            {
                foreach (var bannedPhrase in bannedPhrases)
                {
                    str = str.Replace(bannedPhrase, "***");
                }

                arguments = invocationContext.HubMethodArguments.ToArray();
                arguments[languageFilter.FilterArgument] = str;
                invocationContext = new HubInvocationContext(invocationContext.Context,
                    invocationContext.ServiceProvider,
                    invocationContext.Hub,
                    invocationContext.HubMethod,
                    arguments);
            }

            return await next(invocationContext);
        }
    }
    ```

1. Zaregistrujte filtr centra v `Startup.ConfigureServices` metodě. Aby se zabránilo opětovné inicializaci seznamu zakázaných frází pro každé vyvolání, je filtr rozbočovače zaregistrován jako typ singleton:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR(hubOptions =>
        {
            hubOptions.AddFilter<LanguageFilter>();
        });
    
        services.AddSingleton<LanguageFilter>();
    }
    ```

## <a name="the-hubinvocationcontext-object"></a>Objekt HubInvocationContext

`HubInvocationContext`Obsahuje informace pro aktuální vyvolání metody centra.

| Vlastnost | Popis | Typ |
| ------ | ------ | ----------- |
| `Context ` | `HubCallerContext`Obsahuje informace o připojení. | `HubCallerContext` |
| `Hub` | Instance centra, která se používá pro volání této metody rozbočovače. | `Hub` |
| `HubMethodName` | Název vyvolané metody centra. | `string` |
| `HubMethodArguments` | Seznam argumentů předávaných metodě centra | `IReadOnlyList<string>` |
| `ServiceProvider` | Poskytovatel služeb v oboru pro toto vyvolání metody rozbočovače. | `IServiceProvider` |
| `HubMethod` | Informace o metodě centra | `MethodInfo` |

## <a name="the-hublifetimecontext-object"></a>Objekt HubLifetimeContext

`HubLifetimeContext`Obsahuje informace pro `OnConnectedAsync` `OnDisconnectedAsync` metody a centra.

| Vlastnost | Popis | Typ |
| ------ | ------ | ----------- |
| `Context ` | `HubCallerContext`Obsahuje informace o připojení. | `HubCallerContext` |
| `Hub` | Instance centra, která se používá pro volání této metody rozbočovače. | `Hub` |
| `ServiceProvider` | Poskytovatel služeb v oboru pro toto vyvolání metody rozbočovače. | `IServiceProvider` |

## <a name="authorization-and-filters"></a>Autorizace a filtry

[Autorizace atributů v metodách centra](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization) se spouští před filtry centra.

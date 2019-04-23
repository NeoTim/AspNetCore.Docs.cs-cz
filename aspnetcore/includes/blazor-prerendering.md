---
ms.openlocfilehash: 03ed846171097e30e4954cf632875c0249697665
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "59983062"
---
Při aplikaci na straně serveru Blazor se před vykreslením, nejsou některé akce, jako je například volání do jazyka JavaScript, je to možné, protože nebyl navázat připojení s prohlížečem. Součásti nutné k vykreslení odlišně při předkreslených.

Zpoždění JavaScript volání interop až po navázání připojení s prohlížečem můžete použít `OnAfterRenderAsync` událostí součástí životního cyklu. Tato událost je volána pouze po aplikace plně vykreslením a připojení klienta.

Podmíněně vykreslit rozdílný obsah založen na tom, jestli aplikace je aktuálně před vykreslením obsahu, použijte `IsConnected` vlastnost `IComponentContext` služby. Při spuštění serveru straně `IsConnected` vrátí jenom `true` Pokud neexistuje aktivní připojení ke klientovi. Vždy vrátí `true` při spuštění na straně klienta.

```cshtml
@page "/isconnected-example"
@using Microsoft.AspNetCore.Components.Services
@inject IComponentContext ComponentContext

<h1>IsConnected Example</h1>

<p>
    Current state:
    <strong id="connected-state">
        @(ComponentContext.IsConnected ? "connected" : "not connected")
    </strong>
</p>

<p>
    Clicks:
    <strong id="count">@count</strong>
    <button id="increment-count" onclick="@(() => count++)">Click me</button>
</p>

@functions {
    private int count;
}
```

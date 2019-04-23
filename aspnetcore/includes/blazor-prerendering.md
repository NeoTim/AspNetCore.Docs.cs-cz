---
ms.openlocfilehash: 03ed846171097e30e4954cf632875c0249697665
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "59983062"
---
<span data-ttu-id="eb2a8-101">Při aplikaci na straně serveru Blazor se před vykreslením, nejsou některé akce, jako je například volání do jazyka JavaScript, je to možné, protože nebyl navázat připojení s prohlížečem.</span><span class="sxs-lookup"><span data-stu-id="eb2a8-101">While a Blazor server-side app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="eb2a8-102">Součásti nutné k vykreslení odlišně při předkreslených.</span><span class="sxs-lookup"><span data-stu-id="eb2a8-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="eb2a8-103">Zpoždění JavaScript volání interop až po navázání připojení s prohlížečem můžete použít `OnAfterRenderAsync` událostí součástí životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="eb2a8-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the `OnAfterRenderAsync` component lifecycle event.</span></span> <span data-ttu-id="eb2a8-104">Tato událost je volána pouze po aplikace plně vykreslením a připojení klienta.</span><span class="sxs-lookup"><span data-stu-id="eb2a8-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

<span data-ttu-id="eb2a8-105">Podmíněně vykreslit rozdílný obsah založen na tom, jestli aplikace je aktuálně před vykreslením obsahu, použijte `IsConnected` vlastnost `IComponentContext` služby.</span><span class="sxs-lookup"><span data-stu-id="eb2a8-105">To conditionally render different content based on whether the app is currently prerendering content, use the `IsConnected` property on the `IComponentContext` service.</span></span> <span data-ttu-id="eb2a8-106">Při spuštění serveru straně `IsConnected` vrátí jenom `true` Pokud neexistuje aktivní připojení ke klientovi.</span><span class="sxs-lookup"><span data-stu-id="eb2a8-106">When running server-side, `IsConnected` only returns `true` if there's an active connection to the client.</span></span> <span data-ttu-id="eb2a8-107">Vždy vrátí `true` při spuštění na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="eb2a8-107">It always returns `true` when running client-side.</span></span>

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

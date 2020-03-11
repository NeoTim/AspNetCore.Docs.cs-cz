---
title: Použijte protokol MessagePack hub v SignalR pro ASP.NET Core
author: bradygaster
description: Přidejte protokol MessagePack hub pro ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 3c2a4285945d3fdc6bba195e3160da8b9dcbba44
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78666563"
---
# <a name="use-messagepack-hub-protocol-in-opno-locsignalr-for-aspnet-core"></a><span data-ttu-id="22fb9-103">Použijte protokol MessagePack hub v SignalR pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="22fb9-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="22fb9-104">Od [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="22fb9-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="22fb9-105">Tento článek předpokládá, že čtenář je obeznámen s tématy popsanými v tématu [Začínáme](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="22fb9-105">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="22fb9-106">Co je MessagePack?</span><span class="sxs-lookup"><span data-stu-id="22fb9-106">What is MessagePack?</span></span>

<span data-ttu-id="22fb9-107">[MessagePack](https://msgpack.org/index.html) je binární Serializační formát, který je rychlý a kompaktní.</span><span class="sxs-lookup"><span data-stu-id="22fb9-107">[MessagePack](https://msgpack.org/index.html) is a binary serialization format that is fast and compact.</span></span> <span data-ttu-id="22fb9-108">To je užitečné v případě, že je důležité mít vliv na výkon a šířku pásma, protože vytváří menší zprávy ve srovnání se [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="22fb9-108">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="22fb9-109">Vzhledem k tomu, že se jedná o binární formát, zprávy jsou nečitelný při prohlížení síťových trasování a protokolů, pokud nejsou bajty předávány pomocí MessagePack analyzátoru.</span><span class="sxs-lookup"><span data-stu-id="22fb9-109">Because it's a binary format, messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="22fb9-110"> má integrovanou podporu pro formát MessagePack a poskytuje rozhraní API pro použití klientem a serverem.</span><span class="sxs-lookup"><span data-stu-id="22fb9-110"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="22fb9-111">Konfigurace MessagePack na serveru</span><span class="sxs-lookup"><span data-stu-id="22fb9-111">Configure MessagePack on the server</span></span>

<span data-ttu-id="22fb9-112">Pokud chcete na serveru povolit protokol MessagePack hub, nainstalujte do své aplikace balíček `Microsoft.AspNetCore.SignalR.Protocols.MessagePack`.</span><span class="sxs-lookup"><span data-stu-id="22fb9-112">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="22fb9-113">Do souboru Startup.cs přidejte `AddMessagePackProtocol` volání `AddSignalR` a povolte podporu MessagePack na serveru.</span><span class="sxs-lookup"><span data-stu-id="22fb9-113">In the Startup.cs file add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="22fb9-114">Ve výchozím nastavení je povolený formát JSON.</span><span class="sxs-lookup"><span data-stu-id="22fb9-114">JSON is enabled by default.</span></span> <span data-ttu-id="22fb9-115">Přidání MessagePack umožňuje podporu pro klienty JSON i MessagePack.</span><span class="sxs-lookup"><span data-stu-id="22fb9-115">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="22fb9-116">Pokud chcete přizpůsobit způsob, jakým aplikace MessagePack naformátuje data, `AddMessagePackProtocol` převezme delegáta pro konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="22fb9-116">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="22fb9-117">V tomto delegátu lze vlastnost `FormatterResolvers` použít ke konfiguraci možností serializace MessagePack.</span><span class="sxs-lookup"><span data-stu-id="22fb9-117">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="22fb9-118">Další informace o tom, jak překladače fungují, najdete v knihovně MessagePack na adrese [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="22fb9-118">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="22fb9-119">Atributy lze použít pro objekty, které chcete serializovat, chcete-li definovat, jak by měly být zpracovány.</span><span class="sxs-lookup"><span data-stu-id="22fb9-119">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="22fb9-120">Důrazně doporučujeme zkontrolovat [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) a použít Doporučené opravy.</span><span class="sxs-lookup"><span data-stu-id="22fb9-120">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="22fb9-121">Například nastavení vlastnosti `MessagePackSecurity.Active` static na `MessagePackSecurity.UntrustedData`.</span><span class="sxs-lookup"><span data-stu-id="22fb9-121">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="22fb9-122">Nastavení `MessagePackSecurity.Active` vyžaduje ruční instalaci [MessagePack verze 1.9. x](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="22fb9-122">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="22fb9-123">Instalace `MessagePack` 1.9. x upgraduje SignalR používá verzi.</span><span class="sxs-lookup"><span data-stu-id="22fb9-123">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="22fb9-124">Pokud `MessagePackSecurity.Active` není nastavená na `MessagePackSecurity.UntrustedData`, může škodlivý klient způsobit odepření služby.</span><span class="sxs-lookup"><span data-stu-id="22fb9-124">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="22fb9-125">Nastavte `MessagePackSecurity.Active` v `Program.Main`, jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="22fb9-125">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="22fb9-126">Konfigurace MessagePack na klientovi</span><span class="sxs-lookup"><span data-stu-id="22fb9-126">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="22fb9-127">Formát JSON je ve výchozím nastavení povolen pro podporované klienty.</span><span class="sxs-lookup"><span data-stu-id="22fb9-127">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="22fb9-128">Klienti můžou podporovat jenom jeden protokol.</span><span class="sxs-lookup"><span data-stu-id="22fb9-128">Clients can only support a single protocol.</span></span> <span data-ttu-id="22fb9-129">Přidání podpory MessagePack nahradí všechny dříve nakonfigurované protokoly.</span><span class="sxs-lookup"><span data-stu-id="22fb9-129">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="22fb9-130">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="22fb9-130">.NET client</span></span>

<span data-ttu-id="22fb9-131">Pokud chcete povolit MessagePack v klientovi .NET, nainstalujte balíček `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` a zavolejte `AddMessagePackProtocol` na `HubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="22fb9-131">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="22fb9-132">Toto `AddMessagePackProtocol` volání přebírá delegáta pro konfiguraci možností, jako je server.</span><span class="sxs-lookup"><span data-stu-id="22fb9-132">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="22fb9-133">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="22fb9-133">JavaScript client</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="22fb9-134">Podpora MessagePack pro klienta JavaScriptu je poskytována balíčkem `@microsoft/signalr-protocol-msgpack` npm.</span><span class="sxs-lookup"><span data-stu-id="22fb9-134">MessagePack support for the JavaScript client is provided by the `@microsoft/signalr-protocol-msgpack` npm package.</span></span> <span data-ttu-id="22fb9-135">Nainstalujte balíček spuštěním následujícího příkazu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="22fb9-135">Install the package by executing the following command in a command shell:</span></span>

```console
npm install @microsoft/signalr-protocol-msgpack
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="22fb9-136">Podpora MessagePack pro klienta JavaScriptu je poskytována balíčkem `@aspnet/signalr-protocol-msgpack` npm.</span><span class="sxs-lookup"><span data-stu-id="22fb9-136">MessagePack support for the JavaScript client is provided by the `@aspnet/signalr-protocol-msgpack` npm package.</span></span> <span data-ttu-id="22fb9-137">Nainstalujte balíček spuštěním následujícího příkazu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="22fb9-137">Install the package by executing the following command in a command shell:</span></span>

```console
npm install @aspnet/signalr-protocol-msgpack
```

::: moniker-end

<span data-ttu-id="22fb9-138">Po instalaci balíčku npm lze modul použít přímo pomocí zavaděče modulu JavaScript nebo importovat do prohlížeče odkazem na následující soubor:</span><span class="sxs-lookup"><span data-stu-id="22fb9-138">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="22fb9-139">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="22fb9-139">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="22fb9-140">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="22fb9-140">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

::: moniker-end

<span data-ttu-id="22fb9-141">V prohlížeči musí být také odkazována na knihovnu `msgpack5`.</span><span class="sxs-lookup"><span data-stu-id="22fb9-141">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="22fb9-142">Použijte značku `<script>` k vytvoření odkazu.</span><span class="sxs-lookup"><span data-stu-id="22fb9-142">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="22fb9-143">Knihovnu najdete na adrese *node_modules \msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="22fb9-143">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="22fb9-144">Při použití prvku `<script>` je pořadí důležité.</span><span class="sxs-lookup"><span data-stu-id="22fb9-144">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="22fb9-145">Pokud se na *SignalR-Protocol-msgpack. js* odkazuje před *msgpack5. js*, při pokusu o připojení pomocí MessagePack dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="22fb9-145">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="22fb9-146">*návěstí. js* je také vyžadováno před *SignalR-Protocol-msgpack. js*.</span><span class="sxs-lookup"><span data-stu-id="22fb9-146">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="22fb9-147">Přidání `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` do `HubConnectionBuilder` nakonfiguruje klienta tak, aby používal protokol MessagePack při připojování k serveru.</span><span class="sxs-lookup"><span data-stu-id="22fb9-147">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="22fb9-148">V tuto chvíli nejsou k dispozici žádné možnosti konfigurace pro protokol MessagePack pro klienta jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="22fb9-148">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="22fb9-149">MessagePack adaptivní</span><span class="sxs-lookup"><span data-stu-id="22fb9-149">MessagePack quirks</span></span>

<span data-ttu-id="22fb9-150">Při používání protokolu centra MessagePack je potřeba vědět o několika problémech.</span><span class="sxs-lookup"><span data-stu-id="22fb9-150">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="22fb9-151">MessagePack rozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="22fb9-151">MessagePack is case-sensitive</span></span>

<span data-ttu-id="22fb9-152">Protokol MessagePack rozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="22fb9-152">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="22fb9-153">Zvažte například následující C# třídu:</span><span class="sxs-lookup"><span data-stu-id="22fb9-153">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="22fb9-154">Při odesílání z klienta jazyka JavaScript je nutné použít `PascalCased` názvů vlastností, protože velikost písmen se musí přesně shodovat C# s třídou.</span><span class="sxs-lookup"><span data-stu-id="22fb9-154">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="22fb9-155">Příklad:</span><span class="sxs-lookup"><span data-stu-id="22fb9-155">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="22fb9-156">Použití názvů `camelCased` se nebude správně navazovat C# na třídu.</span><span class="sxs-lookup"><span data-stu-id="22fb9-156">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="22fb9-157">Můžete to obejít pomocí atributu `Key` a zadat jiný název pro vlastnost MessagePack.</span><span class="sxs-lookup"><span data-stu-id="22fb9-157">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="22fb9-158">Další informace najdete [v dokumentaci k MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="22fb9-158">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="22fb9-159">DateTime. Kind se nezachová při serializaci nebo deserializaci.</span><span class="sxs-lookup"><span data-stu-id="22fb9-159">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="22fb9-160">Protokol MessagePack neposkytuje způsob, jak zakódovat `Kind` hodnotu `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="22fb9-160">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="22fb9-161">Výsledkem je, že při deserializaci data MessagePack hub Protocol předpokládá, že příchozí datum je ve formátu UTC.</span><span class="sxs-lookup"><span data-stu-id="22fb9-161">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="22fb9-162">Pokud pracujete s `DateTime` hodnotami v místním čase, doporučujeme před odesláním převést na čas UTC.</span><span class="sxs-lookup"><span data-stu-id="22fb9-162">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="22fb9-163">Převeďte je ze standardu UTC na místní čas, když je přijmete.</span><span class="sxs-lookup"><span data-stu-id="22fb9-163">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="22fb9-164">Další informace o tomto omezení najdete v tématu věnovaném problému GitHubu [ASPNET/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="22fb9-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="22fb9-165">DateTime. MinValue není podporován MessagePack v JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="22fb9-165">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="22fb9-166">Knihovna [msgpack5](https://github.com/mcollina/msgpack5) používaná klientem SignalR JavaScript nepodporuje typ `timestamp96` v MessagePack.</span><span class="sxs-lookup"><span data-stu-id="22fb9-166">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="22fb9-167">Tento typ se používá ke kódování velmi velkých hodnot kalendářních dat (zcela brzy v minulosti nebo velmi daleko v budoucnosti).</span><span class="sxs-lookup"><span data-stu-id="22fb9-167">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="22fb9-168">Hodnota `DateTime.MinValue` je `January 1, 0001`, která musí být kódována v `timestamp96` hodnotě.</span><span class="sxs-lookup"><span data-stu-id="22fb9-168">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="22fb9-169">Z tohoto důvodu se odeslání `DateTime.MinValue` do klienta jazyka JavaScript nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="22fb9-169">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="22fb9-170">Když klient jazyka JavaScript obdrží `DateTime.MinValue`, je vyvolána následující chyba:</span><span class="sxs-lookup"><span data-stu-id="22fb9-170">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="22fb9-171">K zakódování chybějící nebo `null`é hodnoty se obvykle používá `DateTime.MinValue`.</span><span class="sxs-lookup"><span data-stu-id="22fb9-171">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="22fb9-172">Pokud potřebujete tuto hodnotu v MessagePack kódovat, použijte `DateTime` hodnotu s možnou hodnotou null (`DateTime?`) nebo zakódováním samostatné `bool` hodnoty, která označuje, jestli je k dispozici datum.</span><span class="sxs-lookup"><span data-stu-id="22fb9-172">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="22fb9-173">Další informace o tomto omezení najdete v tématu věnovaném problému GitHubu [ASPNET/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="22fb9-173">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="22fb9-174">Podpora MessagePack v prostředí kompilace "před a za běhu"</span><span class="sxs-lookup"><span data-stu-id="22fb9-174">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="22fb9-175">Knihovna [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8) , kterou používá klient a Server .NET, používá generování kódu k optimalizaci serializace.</span><span class="sxs-lookup"><span data-stu-id="22fb9-175">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="22fb9-176">V důsledku toho není podporován ve výchozím nastavení v prostředích, která používají "předdobu" kompilace (například Xamarin iOS nebo Unity).</span><span class="sxs-lookup"><span data-stu-id="22fb9-176">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="22fb9-177">V těchto prostředích je možné používat MessagePack pomocí "předběžného generování" kódu serializátoru/deserializace.</span><span class="sxs-lookup"><span data-stu-id="22fb9-177">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="22fb9-178">Další informace najdete [v dokumentaci k MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="22fb9-178">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="22fb9-179">Jakmile jste předem vygenerovali serializátory, můžete je zaregistrovat pomocí delegáta konfigurace předaného `AddMessagePackProtocol`:</span><span class="sxs-lookup"><span data-stu-id="22fb9-179">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="22fb9-180">Kontroly typů jsou v MessagePack přísnější.</span><span class="sxs-lookup"><span data-stu-id="22fb9-180">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="22fb9-181">Protokol centra JSON provede převody typů během deserializace.</span><span class="sxs-lookup"><span data-stu-id="22fb9-181">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="22fb9-182">Například pokud má příchozí objekt hodnotu vlastnosti, která je číslo (`{ foo: 42 }`), ale vlastnost třídy .NET je typu `string`, hodnota bude převedena.</span><span class="sxs-lookup"><span data-stu-id="22fb9-182">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="22fb9-183">MessagePack však tento převod neprovede a vyvolá výjimku, kterou lze zobrazit v protokolech na straně serveru (a v konzole nástroje):</span><span class="sxs-lookup"><span data-stu-id="22fb9-183">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="22fb9-184">Další informace o tomto omezení najdete v tématu věnovaném problému GitHubu [ASPNET/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="22fb9-184">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="22fb9-185">Související prostředky</span><span class="sxs-lookup"><span data-stu-id="22fb9-185">Related resources</span></span>

* [<span data-ttu-id="22fb9-186">Začínáme</span><span class="sxs-lookup"><span data-stu-id="22fb9-186">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="22fb9-187">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="22fb9-187">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="22fb9-188">Javascriptový klient</span><span class="sxs-lookup"><span data-stu-id="22fb9-188">JavaScript client</span></span>](xref:signalr/javascript-client)

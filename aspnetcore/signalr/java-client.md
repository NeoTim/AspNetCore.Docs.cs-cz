---
title: Klient ASP.NET Core SignalR Java
author: mikaelm12
description: Naučte se používat klienta ASP.NET Core SignalR Java.
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/java-client
ms.openlocfilehash: 6919eabf454f16887e012161a454a4848c45002b
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78660515"
---
# <a name="aspnet-core-opno-locsignalr-java-client"></a><span data-ttu-id="eb070-103">Klient ASP.NET Core SignalR Java</span><span class="sxs-lookup"><span data-stu-id="eb070-103">ASP.NET Core SignalR Java client</span></span>

<span data-ttu-id="eb070-104">Od [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="eb070-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="eb070-105">Klient Java umožňuje připojení k serveru ASP.NET Core SignalR z kódu Java, včetně aplikací pro Android.</span><span class="sxs-lookup"><span data-stu-id="eb070-105">The Java client enables connecting to an ASP.NET Core SignalR server from Java code, including Android apps.</span></span> <span data-ttu-id="eb070-106">Podobně jako [Klient jazyka JavaScript](xref:signalr/javascript-client) a [klient .NET](xref:signalr/dotnet-client)umožňuje klient Java přijímat a odesílat zprávy do centra v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="eb070-106">Like the [JavaScript client](xref:signalr/javascript-client) and the [.NET client](xref:signalr/dotnet-client), the Java client enables you to receive and send messages to a hub in real time.</span></span> <span data-ttu-id="eb070-107">Klient Java je k dispozici v ASP.NET Core 2,2 a novějším.</span><span class="sxs-lookup"><span data-stu-id="eb070-107">The Java client is available in ASP.NET Core 2.2 and later.</span></span>

<span data-ttu-id="eb070-108">Ukázková aplikace konzoly Java, na kterou se odkazuje v tomto článku, používá klienta SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="eb070-108">The sample Java console app referenced in this article uses the SignalR Java client.</span></span>

<span data-ttu-id="eb070-109">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="eb070-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-opno-locsignalr-java-client-package"></a><span data-ttu-id="eb070-110">Instalace balíčku klienta SignalR Java</span><span class="sxs-lookup"><span data-stu-id="eb070-110">Install the SignalR Java client package</span></span>

<span data-ttu-id="eb070-111">Soubor JAR *– 1.0.0* jar umožňuje klientům připojit se k rozbočovačům SignalR.</span><span class="sxs-lookup"><span data-stu-id="eb070-111">The *signalr-1.0.0* JAR file allows clients to connect to SignalR hubs.</span></span> <span data-ttu-id="eb070-112">Nejnovější číslo verze souboru JAR najdete na stránce [výsledků hledání Maven](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span><span class="sxs-lookup"><span data-stu-id="eb070-112">To find the latest JAR file version number, see the [Maven search results](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span></span>

<span data-ttu-id="eb070-113">Pokud používáte Gradle, přidejte následující řádek do oddílu `dependencies` souboru *Build. Gradle* :</span><span class="sxs-lookup"><span data-stu-id="eb070-113">If using Gradle, add the following line to the `dependencies` section of your *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

<span data-ttu-id="eb070-114">Pokud používáte Maven, přidejte následující řádky do prvku `<dependencies>` souboru *pom. XML* :</span><span class="sxs-lookup"><span data-stu-id="eb070-114">If using Maven, add the following lines inside the `<dependencies>` element of your *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a><span data-ttu-id="eb070-115">Připojení k rozbočovači</span><span class="sxs-lookup"><span data-stu-id="eb070-115">Connect to a hub</span></span>

<span data-ttu-id="eb070-116">Chcete-li vytvořit `HubConnection`, měla by být použita `HubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="eb070-116">To establish a `HubConnection`, the `HubConnectionBuilder` should be used.</span></span> <span data-ttu-id="eb070-117">Adresa URL centra a úroveň protokolu se dají nakonfigurovat při sestavování připojení.</span><span class="sxs-lookup"><span data-stu-id="eb070-117">The hub URL and log level can be configured while building a connection.</span></span> <span data-ttu-id="eb070-118">Nakonfigurujte požadované možnosti voláním libovolné metody `HubConnectionBuilder` před `build`.</span><span class="sxs-lookup"><span data-stu-id="eb070-118">Configure any required options by calling any of the `HubConnectionBuilder` methods before `build`.</span></span> <span data-ttu-id="eb070-119">Spusťte připojení pomocí `start`.</span><span class="sxs-lookup"><span data-stu-id="eb070-119">Start the connection with `start`.</span></span>

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="eb070-120">Volání metod rozbočovače na z klienta</span><span class="sxs-lookup"><span data-stu-id="eb070-120">Call hub methods from client</span></span>

<span data-ttu-id="eb070-121">Volání `send` vyvolá metodu rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="eb070-121">A call to `send` invokes a hub method.</span></span> <span data-ttu-id="eb070-122">Předejte název metody centra a všechny argumenty definované v metodě hub pro `send`.</span><span class="sxs-lookup"><span data-stu-id="eb070-122">Pass the hub method name and any arguments defined in the hub method to `send`.</span></span>

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> <span data-ttu-id="eb070-123">Pokud používáte službu Azure SignalR v režimu bez *serveru*, nemůžete volat metody centra z klienta.</span><span class="sxs-lookup"><span data-stu-id="eb070-123">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="eb070-124">Další informace najdete v dokumentaci ke [služběSignalR](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="eb070-124">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="eb070-125">Volání metody klienta od rozbočovače</span><span class="sxs-lookup"><span data-stu-id="eb070-125">Call client methods from hub</span></span>

<span data-ttu-id="eb070-126">Použijte `hubConnection.on` k definování metod v klientovi, které může centrum volat.</span><span class="sxs-lookup"><span data-stu-id="eb070-126">Use `hubConnection.on` to define methods on the client that the hub can call.</span></span> <span data-ttu-id="eb070-127">Definujte metody po sestavení, ale před zahájením připojení.</span><span class="sxs-lookup"><span data-stu-id="eb070-127">Define the methods after building but before starting the connection.</span></span>

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a><span data-ttu-id="eb070-128">Přidat protokolování</span><span class="sxs-lookup"><span data-stu-id="eb070-128">Add logging</span></span>

<span data-ttu-id="eb070-129">SignalR klient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) .</span><span class="sxs-lookup"><span data-stu-id="eb070-129">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="eb070-130">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="eb070-130">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="eb070-131">Následující fragment kódu ukazuje, jak použít `java.util.logging` s klientem SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="eb070-131">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="eb070-132">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="eb070-132">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="eb070-133">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="eb070-133">This can safely be ignored.</span></span>

## <a name="android-development-notes"></a><span data-ttu-id="eb070-134">Poznámky k vývoji pro Android</span><span class="sxs-lookup"><span data-stu-id="eb070-134">Android development notes</span></span>

<span data-ttu-id="eb070-135">V souvislosti s Android SDK kompatibilitou funkcí SignalR klienta zvažte při zadání cílové verze Android SDK následující položky:</span><span class="sxs-lookup"><span data-stu-id="eb070-135">With regards to Android SDK compatibility for the SignalR client features, consider the following items when specifying your target Android SDK version:</span></span>

* <span data-ttu-id="eb070-136">Klient SignalR Java se spustí na úrovni rozhraní Android API 16 a novějších.</span><span class="sxs-lookup"><span data-stu-id="eb070-136">The SignalR Java Client will run on Android API Level 16 and later.</span></span>
* <span data-ttu-id="eb070-137">Připojení prostřednictvím služby Azure SignalR bude vyžadovat rozhraní Android API úrovně 20 a novější, protože [Služba Azure SignalR](/azure/azure-signalr/signalr-overview) vyžaduje TLS 1,2 a nepodporuje šifrovací sady založené na algoritmu SHA-1.</span><span class="sxs-lookup"><span data-stu-id="eb070-137">Connecting through the Azure SignalR Service will require Android API Level 20 and later because the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) requires TLS 1.2 and doesn't support SHA-1-based cipher suites.</span></span> <span data-ttu-id="eb070-138">Android [přidal podporu šifrovacích sad SHA-256 (a vyšších)](https://developer.android.com/reference/javax/net/ssl/SSLSocket) v rozhraní API úrovně 20.</span><span class="sxs-lookup"><span data-stu-id="eb070-138">Android [added support for SHA-256 (and above) cipher suites](https://developer.android.com/reference/javax/net/ssl/SSLSocket) in API Level 20.</span></span>

## <a name="configure-bearer-token-authentication"></a><span data-ttu-id="eb070-139">Konfigurace ověřování nosných tokenů</span><span class="sxs-lookup"><span data-stu-id="eb070-139">Configure bearer token authentication</span></span>

<span data-ttu-id="eb070-140">V SignalR klienta Java můžete nakonfigurovat nosný token, který se má použít pro ověřování, a to tak, že do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)zadáte "přístupovou továrnu tokenu".</span><span class="sxs-lookup"><span data-stu-id="eb070-140">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an "access token factory" to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="eb070-141">Použijte [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) k poskytnutí [> jednoho\<ového řetězce](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava) .</span><span class="sxs-lookup"><span data-stu-id="eb070-141">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="eb070-142">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="eb070-142">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

## <a name="known-limitations"></a><span data-ttu-id="eb070-143">Známá omezení</span><span class="sxs-lookup"><span data-stu-id="eb070-143">Known limitations</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="eb070-144">Podporován je pouze protokol JSON.</span><span class="sxs-lookup"><span data-stu-id="eb070-144">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="eb070-145">Přenos záložního přenosu a přenos událostí odeslaných serverem není podporován.</span><span class="sxs-lookup"><span data-stu-id="eb070-145">Transport fallback and the Server Sent Events transport aren't supported.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="eb070-146">Podporován je pouze protokol JSON.</span><span class="sxs-lookup"><span data-stu-id="eb070-146">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="eb070-147">Podporován je pouze přenos WebSockets.</span><span class="sxs-lookup"><span data-stu-id="eb070-147">Only the WebSockets transport is supported.</span></span>
* <span data-ttu-id="eb070-148">Streamování se ještě nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="eb070-148">Streaming isn't supported yet.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="eb070-149">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="eb070-149">Additional resources</span></span>

* [<span data-ttu-id="eb070-150">Referenční dokumentace k rozhraní API v Javě</span><span class="sxs-lookup"><span data-stu-id="eb070-150">Java API reference</span></span>](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* <span data-ttu-id="eb070-151">[Dokumentace k serveru služby Azure SignalR bez serveru](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="eb070-151">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>

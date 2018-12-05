---
title: Klientskou sadou Java základní funkce SignalR technologie ASP.NET
author: mikaelm12
description: Zjistěte, jak používat klientskou sadou Java funkce SignalR technologie ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 11/07/2018
uid: signalr/java-client
ms.openlocfilehash: 78ffdf7488c95b1cf84a249d6d08b6acd23ec208
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52861755"
---
# <a name="aspnet-core-signalr-java-client"></a><span data-ttu-id="609f9-103">Klientskou sadou Java základní funkce SignalR technologie ASP.NET</span><span class="sxs-lookup"><span data-stu-id="609f9-103">ASP.NET Core SignalR Java client</span></span>

<span data-ttu-id="609f9-104">Podle [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="609f9-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="609f9-105">Klientskou sadou Java umožňuje připojení k serveru funkce SignalR technologie ASP.NET Core z kódu Java, včetně aplikací pro Android.</span><span class="sxs-lookup"><span data-stu-id="609f9-105">The Java client enables connecting to an ASP.NET Core SignalR server from Java code, including Android apps.</span></span> <span data-ttu-id="609f9-106">Podobně jako [javascriptový klient](xref:signalr/javascript-client) a [klienta .NET](xref:signalr/dotnet-client), klientskou sadou Java umožňuje příjem a odesílání zpráv do centra v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="609f9-106">Like the [JavaScript client](xref:signalr/javascript-client) and the [.NET client](xref:signalr/dotnet-client), the Java client enables you to receive and send messages to a hub in real time.</span></span> <span data-ttu-id="609f9-107">Klientskou sadou Java je k dispozici v ASP.NET Core 2.2 a novější.</span><span class="sxs-lookup"><span data-stu-id="609f9-107">The Java client is available in ASP.NET Core 2.2 and later.</span></span>

<span data-ttu-id="609f9-108">Konzolovou aplikaci Java vzorku, který odkazuje tento článek používá klientskou sadou SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="609f9-108">The sample Java console app referenced in this article uses the SignalR Java client.</span></span>

<span data-ttu-id="609f9-109">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/java-client/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="609f9-109">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/java-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-signalr-java-client-package"></a><span data-ttu-id="609f9-110">Instalace balíčku pro klienta SignalR Java</span><span class="sxs-lookup"><span data-stu-id="609f9-110">Install the SignalR Java client package</span></span>

<span data-ttu-id="609f9-111">*Signalr 1.0.0 preview3 35501* soubor JAR umožňuje klientům připojení k rozbočovačům SignalR.</span><span class="sxs-lookup"><span data-stu-id="609f9-111">The *signalr-1.0.0-preview3-35501* JAR file allows clients to connect to SignalR hubs.</span></span> <span data-ttu-id="609f9-112">Číslo verze nejnovější soubor JAR, najdete v tématu [výsledky hledání Maven](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span><span class="sxs-lookup"><span data-stu-id="609f9-112">To find the latest JAR file version number, see the [Maven search results](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span></span>

<span data-ttu-id="609f9-113">Pokud používáte Gradle, přidejte následující řádek, který `dependencies` část vaší *build.gradle* souboru:</span><span class="sxs-lookup"><span data-stu-id="609f9-113">If using Gradle, add the following line to the `dependencies` section of your *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0-preview3-35501'
implementation 'io.reactivex.rxjava2:rxjava:2.2.2'
```

<span data-ttu-id="609f9-114">Pokud pomocí nástroje Maven, přidejte následující řádky uvnitř `<dependencies>` prvek vaše *pom.xml* souboru:</span><span class="sxs-lookup"><span data-stu-id="609f9-114">If using Maven, add the following lines inside the `<dependencies>` element of your *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a><span data-ttu-id="609f9-115">Připojení k rozbočovači</span><span class="sxs-lookup"><span data-stu-id="609f9-115">Connect to a hub</span></span>

<span data-ttu-id="609f9-116">K navázání `HubConnection`, `HubConnectionBuilder` by měla sloužit.</span><span class="sxs-lookup"><span data-stu-id="609f9-116">To establish a `HubConnection`, the `HubConnectionBuilder` should be used.</span></span> <span data-ttu-id="609f9-117">Při vytváření připojení se dá nakonfigurovat úrovně rozbočovače adresy URL a protokolu.</span><span class="sxs-lookup"><span data-stu-id="609f9-117">The hub URL and log level can be configured while building a connection.</span></span> <span data-ttu-id="609f9-118">Nakonfigurujte veškeré požadované možnosti voláním některé z `HubConnectionBuilder` metody před `build`.</span><span class="sxs-lookup"><span data-stu-id="609f9-118">Configure any required options by calling any of the `HubConnectionBuilder` methods before `build`.</span></span> <span data-ttu-id="609f9-119">Zahájit připojení s `start`.</span><span class="sxs-lookup"><span data-stu-id="609f9-119">Start the connection with `start`.</span></span>

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="609f9-120">Volání metod rozbočovače na z klienta</span><span class="sxs-lookup"><span data-stu-id="609f9-120">Call hub methods from client</span></span>

<span data-ttu-id="609f9-121">Volání `send` volá metodu rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="609f9-121">A call to `send` invokes a hub method.</span></span> <span data-ttu-id="609f9-122">Předat název metody rozbočovače a argumentů podle metody rozbočovače na `send`.</span><span class="sxs-lookup"><span data-stu-id="609f9-122">Pass the hub method name and any arguments defined in the hub method to `send`.</span></span>

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="609f9-123">Volání metody klienta od rozbočovače</span><span class="sxs-lookup"><span data-stu-id="609f9-123">Call client methods from hub</span></span>

<span data-ttu-id="609f9-124">Použití `hubConnection.on` definovat metody na straně klienta, která může volat rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="609f9-124">Use `hubConnection.on` to define methods on the client that the hub can call.</span></span> <span data-ttu-id="609f9-125">Definujte metody po sestavení, ale před spuštěním připojení.</span><span class="sxs-lookup"><span data-stu-id="609f9-125">Define the methods after building but before starting the connection.</span></span>

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a><span data-ttu-id="609f9-126">Přidání protokolování</span><span class="sxs-lookup"><span data-stu-id="609f9-126">Add logging</span></span>

<span data-ttu-id="609f9-127">Používá klientskou sadou SignalR Java [SLF4J](https://www.slf4j.org/) knihovny pro protokolování.</span><span class="sxs-lookup"><span data-stu-id="609f9-127">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="609f9-128">Jde o rozhraní API vysoké úrovně protokolování, který umožňuje uživatelům knihovny zvolili vlastní implementace protokolování konkrétní díky možnostem protokolování konkrétní závislost.</span><span class="sxs-lookup"><span data-stu-id="609f9-128">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="609f9-129">Následující fragment kódu ukazuje, jak používat `java.util.logging` s klientskou sadou SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="609f9-129">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="609f9-130">Pokud nenakonfigurujete přihlášení závislostí, načte SLF4J protokolovacího nástroje výchozí žádné operace s tímto upozorněním:</span><span class="sxs-lookup"><span data-stu-id="609f9-130">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="609f9-131">To můžete bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="609f9-131">This can safely be ignored.</span></span>

## <a name="android-development-notes"></a><span data-ttu-id="609f9-132">Poznámky k vývoji pro Android</span><span class="sxs-lookup"><span data-stu-id="609f9-132">Android development notes</span></span>

<span data-ttu-id="609f9-133">Při zadávání vaši cílovou verzi sady Android SDK, s ohledem na Kompatibilita sady Android SDK pro funkce klienta SignalR, zvažte následující body:</span><span class="sxs-lookup"><span data-stu-id="609f9-133">With regards to Android SDK compatibility for the SignalR client features, consider the following items when specifying your target Android SDK version:</span></span>

* <span data-ttu-id="609f9-134">Funkce SignalR klientskou sadou Java se spustí na Android API úrovně 16 a později.</span><span class="sxs-lookup"><span data-stu-id="609f9-134">The SignalR Java Client will run on Android API Level 16 and later.</span></span>
* <span data-ttu-id="609f9-135">Připojení prostřednictvím služby Azure SignalR bude vyžadovat Android API úrovně 20 a novější vzhledem k tomu, [služby Azure SignalR](/azure/azure-signalr/signalr-overview) vyžaduje protokol TLS 1.2 a nepodporuje algoritmus SHA-1-based šifrovací sady.</span><span class="sxs-lookup"><span data-stu-id="609f9-135">Connecting through the Azure SignalR Service will require Android API Level 20 and later because the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) requires TLS 1.2 and doesn't support SHA-1-based cipher suites.</span></span> <span data-ttu-id="609f9-136">Android [přidali podporu pro SHA-256 (a novější) šifrovacích sad](https://developer.android.com/reference/javax/net/ssl/SSLSocket) v rozhraní API úrovně 20.</span><span class="sxs-lookup"><span data-stu-id="609f9-136">Android [added support for SHA-256 (and above) cipher suites](https://developer.android.com/reference/javax/net/ssl/SSLSocket) in API Level 20.</span></span>

## <a name="configure-bearer-token-authentication"></a><span data-ttu-id="609f9-137">Konfigurace ověřování nosného tokenu</span><span class="sxs-lookup"><span data-stu-id="609f9-137">Configure bearer token authentication</span></span>

<span data-ttu-id="609f9-138">V klientovi SignalR Java můžete konfigurovat nosný token pro účely ověření zadáním "access token továrnu" [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="609f9-138">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an "access token factory" to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="609f9-139">Použití [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) k poskytování [RxJava](https://github.com/ReactiveX/RxJava) [jeden<String>](http://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="609f9-139">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single<String>](http://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="609f9-140">Voláním [Single.defer](http://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), můžou psát logiku k vytvoření přístupové tokeny pro vašeho klienta.</span><span class="sxs-lookup"><span data-stu-id="609f9-140">With a call to [Single.defer](http://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

## <a name="known-limitations"></a><span data-ttu-id="609f9-141">Známá omezení</span><span class="sxs-lookup"><span data-stu-id="609f9-141">Known limitations</span></span>

<span data-ttu-id="609f9-142">Toto je verze preview služby klientskou sadou Java.</span><span class="sxs-lookup"><span data-stu-id="609f9-142">This is a preview release of the Java client.</span></span> <span data-ttu-id="609f9-143">Některé funkce nejsou podporovány:</span><span class="sxs-lookup"><span data-stu-id="609f9-143">Some features aren't supported:</span></span>

* <span data-ttu-id="609f9-144">Pouze protokol JSON je podporován.</span><span class="sxs-lookup"><span data-stu-id="609f9-144">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="609f9-145">Je podporován pouze přenosu objekty Websocket.</span><span class="sxs-lookup"><span data-stu-id="609f9-145">Only the WebSockets transport is supported.</span></span>
* <span data-ttu-id="609f9-146">Streamování se ještě nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="609f9-146">Streaming isn't supported yet.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="609f9-147">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="609f9-147">Additional resources</span></span>

* [<span data-ttu-id="609f9-148">Referenční dokumentace k rozhraní API v Javě</span><span class="sxs-lookup"><span data-stu-id="609f9-148">Java API reference</span></span>](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>

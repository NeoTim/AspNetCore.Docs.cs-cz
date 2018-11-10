---
title: Klientskou sadou Java základní funkce SignalR technologie ASP.NET
author: mikaelm12
description: Zjistěte, jak používat klientskou sadou Java funkce SignalR technologie ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 11/06/2018
uid: signalr/java-client
ms.openlocfilehash: 4ee4e61fc301ebeec4d95b1167f94f16c38f3ac5
ms.sourcegitcommit: fc7eb4243188950ae1f1b52669edc007e9d0798d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51225418"
---
# <a name="aspnet-core-signalr-java-client"></a><span data-ttu-id="008cf-103">Klientskou sadou Java základní funkce SignalR technologie ASP.NET</span><span class="sxs-lookup"><span data-stu-id="008cf-103">ASP.NET Core SignalR Java client</span></span>

<span data-ttu-id="008cf-104">Podle [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="008cf-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="008cf-105">Klientskou sadou Java umožňuje připojení k serveru funkce SignalR technologie ASP.NET Core z kódu Java, včetně aplikací pro Android.</span><span class="sxs-lookup"><span data-stu-id="008cf-105">The Java client enables connecting to an ASP.NET Core SignalR server from Java code, including Android apps.</span></span> <span data-ttu-id="008cf-106">Podobně jako [javascriptový klient](xref:signalr/javascript-client) a [klienta .NET](xref:signalr/dotnet-client), klientskou sadou Java umožňuje příjem a odesílání zpráv do centra v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="008cf-106">Like the [JavaScript client](xref:signalr/javascript-client) and the [.NET client](xref:signalr/dotnet-client), the Java client enables you to receive and send messages to a hub in real time.</span></span> <span data-ttu-id="008cf-107">Klientskou sadou Java je k dispozici v ASP.NET Core 2.2 a novější.</span><span class="sxs-lookup"><span data-stu-id="008cf-107">The Java client is available in ASP.NET Core 2.2 and later.</span></span>

<span data-ttu-id="008cf-108">Konzolovou aplikaci Java vzorku, který odkazuje tento článek používá klientskou sadou SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="008cf-108">The sample Java console app referenced in this article uses the SignalR Java client.</span></span>

<span data-ttu-id="008cf-109">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/java-client/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="008cf-109">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/java-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-signalr-java-client-package"></a><span data-ttu-id="008cf-110">Instalace balíčku pro klienta SignalR Java</span><span class="sxs-lookup"><span data-stu-id="008cf-110">Install the SignalR Java client package</span></span>

<span data-ttu-id="008cf-111">*Signalr 1.0.0 preview3 35501* soubor JAR umožňuje klientům připojení k rozbočovačům SignalR.</span><span class="sxs-lookup"><span data-stu-id="008cf-111">The *signalr-1.0.0-preview3-35501* JAR file allows clients to connect to SignalR hubs.</span></span> <span data-ttu-id="008cf-112">Číslo verze nejnovější soubor JAR, najdete v tématu [výsledky hledání Maven](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span><span class="sxs-lookup"><span data-stu-id="008cf-112">To find the latest JAR file version number, see the [Maven search results](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span></span>

<span data-ttu-id="008cf-113">Pokud používáte Gradle, přidejte následující řádek, který `dependencies` část vaší *build.gradle* souboru:</span><span class="sxs-lookup"><span data-stu-id="008cf-113">If using Gradle, add the following line to the `dependencies` section of your *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0-preview3-35501'
implementation 'io.reactivex.rxjava2:rxjava:2.2.2'
```

<span data-ttu-id="008cf-114">Pokud pomocí nástroje Maven, přidejte následující řádky uvnitř `<dependencies>` prvek vaše *pom.xml* souboru:</span><span class="sxs-lookup"><span data-stu-id="008cf-114">If using Maven, add the following lines inside the `<dependencies>` element of your *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a><span data-ttu-id="008cf-115">Připojení k rozbočovači</span><span class="sxs-lookup"><span data-stu-id="008cf-115">Connect to a hub</span></span>

<span data-ttu-id="008cf-116">K navázání `HubConnection`, `HubConnectionBuilder` by měla sloužit.</span><span class="sxs-lookup"><span data-stu-id="008cf-116">To establish a `HubConnection`, the `HubConnectionBuilder` should be used.</span></span> <span data-ttu-id="008cf-117">Při vytváření připojení se dá nakonfigurovat úrovně rozbočovače adresy URL a protokolu.</span><span class="sxs-lookup"><span data-stu-id="008cf-117">The hub URL and log level can be configured while building a connection.</span></span> <span data-ttu-id="008cf-118">Nakonfigurujte veškeré požadované možnosti voláním některé z `HubConnectionBuilder` metody před `build`.</span><span class="sxs-lookup"><span data-stu-id="008cf-118">Configure any required options by calling any of the `HubConnectionBuilder` methods before `build`.</span></span> <span data-ttu-id="008cf-119">Zahájit připojení s `start`.</span><span class="sxs-lookup"><span data-stu-id="008cf-119">Start the connection with `start`.</span></span>

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="008cf-120">Volání metod rozbočovače na z klienta</span><span class="sxs-lookup"><span data-stu-id="008cf-120">Call hub methods from client</span></span>

<span data-ttu-id="008cf-121">Volání `send` volá metodu rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="008cf-121">A call to `send` invokes a hub method.</span></span> <span data-ttu-id="008cf-122">Předat název metody rozbočovače a argumentů podle metody rozbočovače na `send`.</span><span class="sxs-lookup"><span data-stu-id="008cf-122">Pass the hub method name and any arguments defined in the hub method to `send`.</span></span>

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="008cf-123">Volání metody klienta od rozbočovače</span><span class="sxs-lookup"><span data-stu-id="008cf-123">Call client methods from hub</span></span>

<span data-ttu-id="008cf-124">Použití `hubConnection.on` definovat metody na straně klienta, která může volat rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="008cf-124">Use `hubConnection.on` to define methods on the client that the hub can call.</span></span> <span data-ttu-id="008cf-125">Definujte metody po sestavení, ale před spuštěním připojení.</span><span class="sxs-lookup"><span data-stu-id="008cf-125">Define the methods after building but before starting the connection.</span></span>

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a><span data-ttu-id="008cf-126">Přidání protokolování</span><span class="sxs-lookup"><span data-stu-id="008cf-126">Add logging</span></span>

<span data-ttu-id="008cf-127">Používá klientskou sadou SignalR Java [SLF4J](https://www.slf4j.org/) knihovny pro protokolování.</span><span class="sxs-lookup"><span data-stu-id="008cf-127">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="008cf-128">Jde o rozhraní API vysoké úrovně protokolování, který umožňuje uživatelům knihovny zvolili vlastní implementace protokolování konkrétní díky možnostem protokolování konkrétní závislost.</span><span class="sxs-lookup"><span data-stu-id="008cf-128">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="008cf-129">Následující fragment kódu ukazuje, jak používat `java.util.logging` s klientskou sadou SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="008cf-129">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="008cf-130">Pokud nenakonfigurujete přihlášení závislostí, načte SLF4J protokolovacího nástroje výchozí žádné operace s tímto upozorněním:</span><span class="sxs-lookup"><span data-stu-id="008cf-130">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="008cf-131">To můžete bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="008cf-131">This can safely be ignored.</span></span>


## <a name="configure-bearer-token-authentication"></a><span data-ttu-id="008cf-132">Konfigurace ověřování nosného tokenu</span><span class="sxs-lookup"><span data-stu-id="008cf-132">Configure bearer token authentication</span></span>

<span data-ttu-id="008cf-133">V klientovi SignalR Java můžete konfigurovat nosný token pro účely ověření zadáním "access token továrnu" [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="008cf-133">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an "access token factory" to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="008cf-134">Použití [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) k poskytování [RxJava](https://github.com/ReactiveX/RxJava) [jeden<String>](http://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="008cf-134">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single<String>](http://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="008cf-135">Voláním [Single.defer](http://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), můžou psát logiku k vytvoření přístupové tokeny pro vašeho klienta.</span><span class="sxs-lookup"><span data-stu-id="008cf-135">With a call to [Single.defer](http://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

## <a name="known-limitations"></a><span data-ttu-id="008cf-136">Známá omezení</span><span class="sxs-lookup"><span data-stu-id="008cf-136">Known limitations</span></span>

<span data-ttu-id="008cf-137">Toto je verze preview služby klientskou sadou Java.</span><span class="sxs-lookup"><span data-stu-id="008cf-137">This is a preview release of the Java client.</span></span> <span data-ttu-id="008cf-138">Některé funkce nejsou podporovány:</span><span class="sxs-lookup"><span data-stu-id="008cf-138">Some features aren't supported:</span></span>

* <span data-ttu-id="008cf-139">Pouze protokol JSON je podporován.</span><span class="sxs-lookup"><span data-stu-id="008cf-139">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="008cf-140">Je podporován pouze přenosu objekty Websocket.</span><span class="sxs-lookup"><span data-stu-id="008cf-140">Only the WebSockets transport is supported.</span></span>
* <span data-ttu-id="008cf-141">Streamování se ještě nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="008cf-141">Streaming isn't supported yet.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="008cf-142">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="008cf-142">Additional resources</span></span>

* [<span data-ttu-id="008cf-143">Referenční dokumentace k rozhraní API v Javě</span><span class="sxs-lookup"><span data-stu-id="008cf-143">Java API reference</span></span>](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>

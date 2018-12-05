---
title: Klientskou sadou Java základní funkce SignalR technologie ASP.NET
author: mikaelm12
description: Zjistěte, jak používat klientskou sadou Java funkce SignalR technologie ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 11/07/2018
uid: signalr/java-client
ms.openlocfilehash: d0eff38c1f622b896ed1dc3002238aec7b6bfd38
ms.sourcegitcommit: 8a65f6c2cbe290fb2418eed58f60fb74c95392c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2018
ms.locfileid: "52892091"
---
# <a name="aspnet-core-signalr-java-client"></a>Klientskou sadou Java základní funkce SignalR technologie ASP.NET

Podle [Mikael Mengistu](https://twitter.com/MikaelM_12)

Klientskou sadou Java umožňuje připojení k serveru funkce SignalR technologie ASP.NET Core z kódu Java, včetně aplikací pro Android. Podobně jako [javascriptový klient](xref:signalr/javascript-client) a [klienta .NET](xref:signalr/dotnet-client), klientskou sadou Java umožňuje příjem a odesílání zpráv do centra v reálném čase. Klientskou sadou Java je k dispozici v ASP.NET Core 2.2 a novější.

Konzolovou aplikaci Java vzorku, který odkazuje tento článek používá klientskou sadou SignalR Java.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/java-client/sample) ([stažení](xref:index#how-to-download-a-sample))

## <a name="install-the-signalr-java-client-package"></a>Instalace balíčku pro klienta SignalR Java

*Signalr 1.0.0* soubor JAR umožňuje klientům připojení k rozbočovačům SignalR. Číslo verze nejnovější soubor JAR, najdete v tématu [výsledky hledání Maven](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).

Pokud používáte Gradle, přidejte následující řádek, který `dependencies` část vaší *build.gradle* souboru:

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

Pokud pomocí nástroje Maven, přidejte následující řádky uvnitř `<dependencies>` prvek vaše *pom.xml* souboru:

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a>Připojení k rozbočovači

K navázání `HubConnection`, `HubConnectionBuilder` by měla sloužit. Při vytváření připojení se dá nakonfigurovat úrovně rozbočovače adresy URL a protokolu. Nakonfigurujte veškeré požadované možnosti voláním některé z `HubConnectionBuilder` metody před `build`. Zahájit připojení s `start`.

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a>Volání metod rozbočovače na z klienta

Volání `send` volá metodu rozbočovače. Předat název metody rozbočovače a argumentů podle metody rozbočovače na `send`.

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

## <a name="call-client-methods-from-hub"></a>Volání metody klienta od rozbočovače

Použití `hubConnection.on` definovat metody na straně klienta, která může volat rozbočovače. Definujte metody po sestavení, ale před spuštěním připojení.

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a>Přidání protokolování

Používá klientskou sadou SignalR Java [SLF4J](https://www.slf4j.org/) knihovny pro protokolování. Jde o rozhraní API vysoké úrovně protokolování, který umožňuje uživatelům knihovny zvolili vlastní implementace protokolování konkrétní díky možnostem protokolování konkrétní závislost. Následující fragment kódu ukazuje, jak používat `java.util.logging` s klientskou sadou SignalR Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Pokud nenakonfigurujete přihlášení závislostí, načte SLF4J protokolovacího nástroje výchozí žádné operace s tímto upozorněním:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

To můžete bezpečně ignorovat.

## <a name="android-development-notes"></a>Poznámky k vývoji pro Android

Při zadávání vaši cílovou verzi sady Android SDK, s ohledem na Kompatibilita sady Android SDK pro funkce klienta SignalR, zvažte následující body:

* Funkce SignalR klientskou sadou Java se spustí na Android API úrovně 16 a později.
* Připojení prostřednictvím služby Azure SignalR bude vyžadovat Android API úrovně 20 a novější vzhledem k tomu, [služby Azure SignalR](/azure/azure-signalr/signalr-overview) vyžaduje protokol TLS 1.2 a nepodporuje algoritmus SHA-1-based šifrovací sady. Android [přidali podporu pro SHA-256 (a novější) šifrovacích sad](https://developer.android.com/reference/javax/net/ssl/SSLSocket) v rozhraní API úrovně 20.

## <a name="configure-bearer-token-authentication"></a>Konfigurace ověřování nosného tokenu

V klientovi SignalR Java můžete konfigurovat nosný token pro účely ověření zadáním "access token továrnu" [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Použití [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) k poskytování [RxJava](https://github.com/ReactiveX/RxJava) [jeden<String>](http://reactivex.io/documentation/single.html). Voláním [Single.defer](http://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), můžou psát logiku k vytvoření přístupové tokeny pro vašeho klienta.

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

## <a name="known-limitations"></a>Známá omezení

* Pouze protokol JSON je podporován.
* Je podporován pouze přenosu objekty Websocket.
* Streamování se ještě nepodporuje.

## <a name="additional-resources"></a>Další zdroje

* [Referenční dokumentace k rozhraní API v Javě](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>

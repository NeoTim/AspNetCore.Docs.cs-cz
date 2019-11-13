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
ms.openlocfilehash: d7143b2c22ecdc4e68f484aa4c244e1c520beae0
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963786"
---
# <a name="aspnet-core-opno-locsignalr-java-client"></a>Klient ASP.NET Core SignalR Java

Od [Mikael Mengistu](https://twitter.com/MikaelM_12)

Klient Java umožňuje připojení k serveru ASP.NET Core SignalR z kódu Java, včetně aplikací pro Android. Podobně jako [Klient jazyka JavaScript](xref:signalr/javascript-client) a [klient .NET](xref:signalr/dotnet-client)umožňuje klient Java přijímat a odesílat zprávy do centra v reálném čase. Klient Java je k dispozici v ASP.NET Core 2,2 a novějším.

Ukázková aplikace konzoly Java, na kterou se odkazuje v tomto článku, používá klienta SignalR Java.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="install-the-opno-locsignalr-java-client-package"></a>Instalace balíčku klienta SignalR Java

Soubor JAR *– 1.0.0* jar umožňuje klientům připojit se k rozbočovačům SignalR. Nejnovější číslo verze souboru JAR najdete na stránce [výsledků hledání Maven](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).

Pokud používáte Gradle, přidejte následující řádek do oddílu `dependencies` souboru *Build. Gradle* :

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

Pokud používáte Maven, přidejte následující řádky do prvku `<dependencies>` souboru *pom. XML* :

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a>Připojení k centru

Chcete-li vytvořit `HubConnection`, měla by být použita `HubConnectionBuilder`. Adresa URL centra a úroveň protokolu se dají nakonfigurovat při sestavování připojení. Nakonfigurujte požadované možnosti voláním libovolné metody `HubConnectionBuilder` před `build`. Spusťte připojení pomocí `start`.

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a>Volání metod centra z klienta

Volání `send` vyvolá metodu rozbočovače. Předejte název metody centra a všechny argumenty definované v metodě hub pro `send`.

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> Pokud používáte službu Azure SignalR v režimu bez *serveru*, nemůžete volat metody centra z klienta. Další informace najdete v dokumentaci ke [služběSignalR](/azure/azure-signalr/signalr-concept-serverless-development-config).

## <a name="call-client-methods-from-hub"></a>Volání metod klienta z centra

Použijte `hubConnection.on` k definování metod v klientovi, které může centrum volat. Definujte metody po sestavení, ale před zahájením připojení.

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a>Přidat protokolování

SignalR klient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) . Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí. Následující fragment kódu ukazuje, jak použít `java.util.logging` s klientem SignalR Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Tuto možnost lze bezpečně ignorovat.

## <a name="android-development-notes"></a>Poznámky k vývoji pro Android

V souvislosti s Android SDK kompatibilitou funkcí SignalR klienta zvažte při zadání cílové verze Android SDK následující položky:

* Klient SignalR Java se spustí na úrovni rozhraní Android API 16 a novějších.
* Připojení prostřednictvím služby Azure SignalR bude vyžadovat rozhraní Android API úrovně 20 a novější, protože [Služba Azure SignalR](/azure/azure-signalr/signalr-overview) vyžaduje TLS 1,2 a nepodporuje šifrovací sady založené na algoritmu SHA-1. Android [přidal podporu šifrovacích sad SHA-256 (a vyšších)](https://developer.android.com/reference/javax/net/ssl/SSLSocket) v rozhraní API úrovně 20.

## <a name="configure-bearer-token-authentication"></a>Konfigurace ověřování nosných tokenů

V SignalR klienta Java můžete nakonfigurovat nosný token, který se má použít pro ověřování, a to tak, že do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)zadáte "přístupovou továrnu tokenu". Použijte [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) k poskytnutí [> jednoho\<ového řetězce](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava) . Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

## <a name="known-limitations"></a>Známá omezení

::: moniker range=">= aspnetcore-3.0"

* Podporován je pouze protokol JSON.
* Přenos záložního přenosu a přenos událostí odeslaných serverem není podporován.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Podporován je pouze protokol JSON.
* Podporován je pouze přenos WebSockets.
* Streamování se ještě nepodporuje.

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* [Referenční dokumentace k rozhraní API v Javě](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [Dokumentace k serveru služby Azure SignalR bez serveru](/azure/azure-signalr/signalr-concept-serverless-development-config)

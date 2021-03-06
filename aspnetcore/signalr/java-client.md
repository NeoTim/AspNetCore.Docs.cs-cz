---
title: ASP.NET Core SignalR klient Java
author: mikaelm12
description: Naučte se používat ASP.NET Core SignalR klienta Java.
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/java-client
ms.openlocfilehash: 5351bcf5adb4a79efc993f48acbb959f113c814f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634953"
---
# <a name="aspnet-core-no-locsignalr-java-client"></a>ASP.NET Core SignalR klient Java

Od [Mikael Mengistu](https://twitter.com/MikaelM_12)

Klient Java umožňuje připojení k SignalR serveru ASP.NET Core z kódu Java, včetně aplikací pro Android. Podobně jako [Klient jazyka JavaScript](xref:signalr/javascript-client) a [klient .NET](xref:signalr/dotnet-client)umožňuje klient Java přijímat a odesílat zprávy do centra v reálném čase. Klient Java je k dispozici v ASP.NET Core 2,2 a novějším.

Ukázková aplikace konzoly Java, na kterou se odkazuje v tomto článku, používá SignalR klienta Java.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="install-the-no-locsignalr-java-client-package"></a>Instalace SignalR balíčku klienta Java

Soubor JAR *– 1.0.0* jar umožňuje klientům připojit se k SignalR rozbočovačům. Nejnovější číslo verze souboru JAR najdete na stránce [výsledků hledání Maven](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).

Pokud používáte Gradle, přidejte následující řádek do `dependencies` části souboru *Build. Gradle* :

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

Pokud používáte Maven, přidejte následující řádky do `<dependencies>` prvku *pom.xml* souboru:

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a>Připojení k centru

Chcete-li vytvořit `HubConnection` , je `HubConnectionBuilder` třeba použít. Adresa URL centra a úroveň protokolu se dají nakonfigurovat při sestavování připojení. Nakonfigurujte všechny požadované možnosti voláním některé z `HubConnectionBuilder` metod `build` . Spusťte připojení pomocí `start` .

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a>Volání metod centra z klienta

Volání `send` metody vyvolá metodu centra. Předat název metody centra a všechny argumenty definované v metodě hub pro `send` .

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> Volání metod rozbočovače z klienta je podporováno, pouze pokud používáte SignalR službu Azure ve *výchozím* režimu. Další informace najdete v tématu [Nejčastější dotazy (úložiště GitHub Azure-Signal)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

## <a name="call-client-methods-from-hub"></a>Volání metod klienta z centra

Použijte `hubConnection.on` k definování metod v klientovi, které může centrum volat. Definujte metody po sestavení, ale před zahájením připojení.

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a>Přidání protokolování

SignalRKlient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) . Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí. Následující fragment kódu ukazuje, jak používat `java.util.logging` s SignalR klientem Java.

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

Z hlediska kompatibility Android SDK pro SignalR klientské funkce zvažte při určení cílové Android SDK verze následující položky:

* SignalRKlient Java bude běžet na úrovni rozhraní Android API 16 a novějších.
* Připojení prostřednictvím služby Azure SignalR bude vyžadovat rozhraní Android API úrovně 20 a novější, protože [ SignalR Služba Azure](/azure/azure-signalr/signalr-overview) vyžaduje TLS 1,2 a nepodporuje šifrovací sady založené na algoritmu SHA-1. Android [přidal podporu šifrovacích sad SHA-256 (a vyšších)](https://developer.android.com/reference/javax/net/ssl/SSLSocket) v rozhraní API úrovně 20.

## <a name="configure-bearer-token-authentication"></a>Konfigurace ověřování nosných tokenů

V SignalR klientovi Java můžete nakonfigurovat nosný token, který se má použít pro ověřování, a to tak, že do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)zadáte "přístupovou továrnu tokenu". Pomocí [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) můžete zadat [RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html). Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.

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

## <a name="additional-resources"></a>Další zdroje informací

* [Referenční dokumentace k rozhraní API v Javě](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [SignalRDokumentace k serveru se službou Azure](/azure/azure-signalr/signalr-concept-serverless-development-config)

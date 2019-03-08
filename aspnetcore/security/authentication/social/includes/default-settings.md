---
ms.openlocfilehash: 2fe12027e7a5233cf01e6c412f7ee536d479facd
ms.sourcegitcommit: 191d21c1e37b56f0df0187e795d9a56388bbf4c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2019
ms.locfileid: "57665783"
---
<!--Don't update this for 2.2, use the 2.2 version --> Volání [AddDefaultIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionuiextensions.adddefaultidentity) nakonfiguruje výchozí nastavení schéma. [AddAuthentication(String)](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_String_) přetížení sad [DefaultScheme](/dotnet/api/microsoft.aspnetcore.authentication.authenticationoptions.defaultscheme) vlastnost. [AddAuthentication (akce&lt;AuthenticationOptions&gt;)](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) přetížení umožňuje konfigurovat možnosti ověřování, které je možné použít k nastavení výchozí schémata ověřování pro různé účely. Následující volání `AddAuthentication` přepsání, které jste dříve nakonfigurovali [AuthenticationOptions](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions) vlastnosti.

[AuthenticationBuilder](/dotnet/api/microsoft.aspnetcore.authentication.authenticationbuilder) rozšiřující metody, které se zaregistrovat obslužnou rutinu ověřování může být volána pouze jednou za schéma ověřování. Existují přetížení, které umožňují konfiguraci vlastností schéma, název schématu a zobrazovaný název.

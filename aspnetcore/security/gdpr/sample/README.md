# <a name="gdpr-sample"></a>Ukázka GDPR

* V souboru *appSettings. JSON*nastavte `CheckNotConsentNeeded` `false` tak, aby vyžadoval souhlas; jinak nastavte na hodnotu true nebo vynechat. Otestujte aplikaci s `CheckNotConsentNeeded` nastavenou na `false` a nastavte na `true`.
* Vytvářejte základní a nepostradateln soubory cookie s každou variací `CheckConsentNeeded` a uděleného souhlasu.
* Zaregistrujte uživatele.
* Odstraňte soubory cookie.

# <a name="gdpr-sample"></a>Ukázka GDPR

* V *appsettings.json*, nastavte `CheckNotConsentNeeded` k `false` vyžadovat souhlas; v opačném případě nastavte na hodnotu true nebo vynechejte. Testování aplikace s `CheckNotConsentNeeded` nastavena na `false` a nastavte na `true`.
* Vytvoření základní a které není nezbytné soubory cookie s každou změnu `CheckConsentNeeded` a udělit souhlas.
* Registrace uživatele.
* Odstranění souborů cookie.

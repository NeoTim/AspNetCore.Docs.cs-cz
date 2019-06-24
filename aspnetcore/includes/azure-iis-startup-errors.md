## <a name="app-startup-errors"></a>Chyby při spuštění aplikace

::: moniker range=">= aspnetcore-2.2"

V sadě Visual Studio projekt ASP.NET Core výchozí hodnota je [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění. A *502.5 – selhání procesu* nebo *500.30 - Start selhání* , která nastane, pokud ladění místně může být troubleshooted pomocí doporučení v tomto tématu.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

V sadě Visual Studio projekt ASP.NET Core výchozí hodnota je [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění. A *502.5 selhání procesu* , která nastane, pokud ladění místně může být troubleshooted pomocí doporučení v tomto tématu.

::: moniker-end

### <a name="500-internal-server-error"></a>Chyba 500 interní Server

Spuštění aplikace, ale chybu brání splnění žádosti. na serveru.

Při spuštění nebo při vytváření odpovědi, k této chybě dochází v kódu aplikace. Odpověď může obsahovat žádný obsah nebo se může zobrazit odpovědi *500 – Interní chyba serveru* v prohlížeči. V protokolu událostí aplikace obvykle hlásí, že aplikace se normálně spustit. Z pohledu serveru, který je správný. Aplikace začal, ale nemůže generovat platnou odpověď. Spuštění aplikace příkazového řádku na serveru nebo [povolit protokol stdout modul ASP.NET Core](#aspnet-core-module-stdout-log) k vyřešení tohoto problému.

::: moniker range="= aspnetcore-2.2"

### <a name="5000-in-process-handler-load-failure"></a>500.0 v procesu selhání načtení obslužné rutiny

Pracovní proces se nezdaří. Aplikace se nespustí.

[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) nepodaří najít .NET Core CLR a najít obslužnou rutinu požadavků v procesu (*aspnetcorev2_inprocess.dll*). Zkontrolujte, jestli:

* Aplikace cílí na buď [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) balíček NuGet nebo [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).
* Verze rozhraní framework ASP.NET Core sdílené cíle, které aplikace nainstalované v cílovém počítači.

### <a name="5000-out-of-process-handler-load-failure"></a>500.0 Chyba načtení out-Of-Process obslužné rutiny

Pracovní proces se nezdaří. Aplikace se nespustí.

[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) nenajde žádné obslužné rutiny hostování požadavku na více instancí procesu. Ujistěte se, *aspnetcorev2_outofprocess.dll* je k dispozici v podsložce vedle *aspnetcorev2.dll*.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="5000-in-process-handler-load-failure"></a>500.0 v procesu selhání načtení obslužné rutiny

Pracovní proces se nezdaří. Aplikace se nespustí.

Došlo k neznámé chybě při načítání [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) komponenty. Proveďte jednu z následujících akcí:

* Kontakt [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (vyberte **vývojářské nástroje** pak **ASP.NET Core**).
* Položte dotaz na Stack Overflow.
* Založte problém na našem [úložiště GitHub](https://github.com/aspnet/AspNetCore).

### <a name="50030-in-process-startup-failure"></a>500.30 v procesu selhání spuštění

Pracovní proces se nezdaří. Aplikace se nespustí.

[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) pokusy o spuštění .NET Core CLR v procesu, ale nepovede spustit. Příčinu selhání spuštění procesu lze určit obvykle položky [protokolu událostí aplikace](#application-event-log) a [protokolů stdout modul ASP.NET Core](#aspnet-core-module-stdout-log).

Běžné chyby je, že aplikace je špatně nakonfigurovaný. kvůli cílení na určitou verzi rozhraní framework sdílené ASP.NET Core, který není k dispozici. Zkontrolujte, jaké verze rozhraní framework ASP.NET Core sdílené jsou nainstalovány v cílovém počítači.

### <a name="50031-ancm-failed-to-find-native-dependencies"></a>500.31 nepovedlo se najít nativní závislosti ANCM

Pracovní proces se nezdaří. Aplikace se nespustí.

[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) pokusy o spuštění .NET Core runtime v procesu, ale nepovede spustit. Nejčastější příčinou této chyby při spuštění je, když `Microsoft.NETCore.App` nebo `Microsoft.AspNetCore.App` není nainstalován modul CLR. Pokud nasazení aplikace do cíle ASP.NET Core 3.0 a tuto verzi neexistuje na počítači, dojde k této chybě. Následuje příklad chybová zpráva:

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

Chybová zpráva obsahuje seznam všech nainstalovaných verzí rozhraní .NET Core a verze požadované aplikace. Chcete-li tuto chybu opravit, buď:

* Nainstalujte odpovídající verzi .NET Core na počítači.
* Změna aplikace pro cílení na verzi .NET Core, který je k dispozici na počítači.
* Publikování aplikací jako [samostatná nasazení](/dotnet/core/deploying/#self-contained-deployments-scd).

Při spuštění ve vývoji ( `ASPNETCORE_ENVIRONMENT` proměnná prostředí je nastavená na `Development`), konkrétní chyba zapsána do odpovědi HTTP. Příčinu selhání spuštění procesu je také součástí [protokolu událostí aplikace](#application-event-log).

### <a name="50032-ancm-failed-to-load-dll"></a>500.32 ANCM nepovedlo se načíst knihovnu dll

Pracovní proces se nezdaří. Aplikace se nespustí.

Nejčastější příčinou této chyby je, že aplikace je publikována pro architekturu nekompatibilní procesor. Pokud pracovní proces běží jako 32bitová aplikace a aplikace byla publikována na cíl 64-bit, dojde k této chybě.

Chcete-li tuto chybu opravit, buď:

* Znovu publikujte aplikaci pro stejnou architekturu procesorů jako pracovní proces.
* Publikování aplikací jako [nasazení závisí na architektuře](/dotnet/core/deploying/#framework-dependent-executables-fde).

### <a name="50033-ancm-request-handler-load-failure"></a>500.33 Chyba načtení obslužná rutina požadavku ANCM

Pracovní proces se nezdaří. Aplikace se nespustí.

Aplikace nebyla odkazovat `Microsoft.AspNetCore.App` rozhraní framework. Jenom aplikace, jejichž cílem `Microsoft.AspNetCore.App` framework mohou být hostovány systémem [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

Chcete-li tuto chybu opravit, zkontrolujte, že aplikace je cílen na verzi `Microsoft.AspNetCore.App` rozhraní framework. Zkontrolujte `.runtimeconfig.json` ověření rozhraní framework aplikace cílí.

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a>500.34 ANCM smíšené modelech hostování není podporován

Pracovní proces nelze spustit aplikaci v rámci procesu a aplikace na více instancí procesu ve stejném procesu.

Chcete-li vyřešit tuto chybu, spouštění aplikací v samostatné fondy aplikací IIS.

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a>500.35 ANCM několik aplikací v rámci procesu ve stejném procesu

Pracovní proces nelze spustit aplikaci v rámci procesu a aplikace na více instancí procesu ve stejném procesu.

Chcete-li vyřešit tuto chybu, spouštění aplikací v samostatné fondy aplikací IIS.

### <a name="50036-ancm-out-of-process-handler-load-failure"></a>500.36 Chyba načtení ANCM obslužné rutiny na více instancí procesu

Obslužné rutiny požadavku na více instancí procesu *aspnetcorev2_outofprocess.dll*, vedle položky není *aspnetcorev2.dll* souboru. To znamená poškozená instalace [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

Chcete-li vyřešit tuto chybu, opravte instalaci [sady hostování rozhraní .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (pro IIS) nebo Visual Studio (pro službu IIS Express).

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a>500.37 nepovedlo se spustit v rámci časový Limit spuštění ANCM

Spuštění v rámci časový limit spuštění poskytují ANCM se nezdařilo. Výchozí časový limit je 120 sekund.

Této chybě může dojít při spuštění velkého počtu aplikací ve stejném počítači. Zkontrolujte provozní špičky využití procesoru nebo paměti na serveru při spuštění. Budete muset rozvrhnout procesu spuštění více aplikací.

::: moniker-end

### <a name="5025-process-failure"></a>502.5 zpracovat selhání

Pracovní proces se nezdaří. Aplikace se nespustí.

[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) pokusy o spuštění pracovního procesu, ale nepovede spustit. Příčinu selhání spuštění procesu lze určit obvykle položky [protokolu událostí aplikace](#application-event-log) a [protokolů stdout modul ASP.NET Core](#aspnet-core-module-stdout-log).

Běžné chyby je, že aplikace je špatně nakonfigurovaný. kvůli cílení na určitou verzi rozhraní framework sdílené ASP.NET Core, který není k dispozici. Zkontrolujte, jaké verze rozhraní framework ASP.NET Core sdílené jsou nainstalovány v cílovém počítači. *Sdílené architektuře* je sada sestavení ( *.dll* soubory), které jsou na počítači nainstalovaný a odkazuje Microsoft.aspnetcore.all například `Microsoft.AspNetCore.App`. Odkaz na Microsoft.aspnetcore.all můžete určit minimální požadovaná verze. Další informace najdete v tématu [sdílené architektuře](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

*502.5 selhání procesu* při hostování nebo aplikace chybná konfigurace způsobí, že se pracovní proces selže, vrátí se chybová stránka:

### <a name="failed-to-start-application-errorcode-0x800700c1"></a>Nepovedlo se spustit aplikaci (kód chyby "0x800700c1")

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

Aplikaci se nepodařilo spustit, protože sestavení aplikace ( *.dll*) nelze načíst.

Tato chyba nastane, pokud došlo k neshodě bitové verze mezi publikované aplikace a proces w3wp/iisexpress.

Ověřte správnost nastavení 32-bit fondu aplikací:

1. Vyberte fond aplikací ve Správci služby IIS na **fondy aplikací**.
1. Vyberte **Upřesnit nastavení** pod **upravit fond aplikací** v **akce** panelu.
1. Nastavte **povolit 32bitové aplikace**:
   * Pokud nasazení (x86) 32bitové aplikace, nastavte hodnotu na `True`.
   * Pokud nasazení (x64) 64bitové aplikace, nastavte hodnotu na `False`.

### <a name="connection-reset"></a>Obnovení připojení

Pokud dojde k chybě po odeslání hlavičky, bude příliš pozdě pro server k odeslání **500 – Interní chyba serveru** , když dojde k chybě. Často se to stane, když dojde k chybě při serializaci složitých objektů pro odpověď. Tento typ chyby se zobrazí jako *obnovení připojení* chyba na straně klienta. [Protokolování aplikací](xref:fundamentals/logging/index) mohou pomoci při řešení těchto typů chyb.

## <a name="default-startup-limits"></a>Výchozí omezení při spuštění

[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) má nakonfigurovanou výchozí *startupTimeLimit* 120 sekund. Když necháte na výchozí hodnotu, aplikace může trvat až dvě minuty, spusťte před modulu protokoly selhání procesu. Informace o konfiguraci modulu najdete v tématu [atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

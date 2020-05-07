Blazor serverové aplikace jsou v paměti serveru v provozu. To znamená, že v rámci jednoho procesu je hostováno více aplikací. Pro každou relaci aplikace Blazor spustí okruh s vlastním oborem typu DI Container. To znamená, že vymezené služby jsou pro každou relaci Blazor jedinečné.

> [!WARNING]
> Nedoporučujeme, aby se aplikace na stejném stavu sdílené složky na stejném serveru používaly, pokud se nepoužije extrémní péče, protože to může způsobit ohrožení zabezpečení, jako je například nevracení stavu uživatele napříč okruhy.

V aplikacích Blazor můžete použít stavové služby typu Singleton, pokud jsou pro ně konkrétně navržené. Například je možné použít mezipaměť paměti jako typ singleton, protože pro přístup k dané položce vyžaduje klíč, za předpokladu, že uživatelé nemají kontrolu nad tím, jaké klíče mezipaměti jsou používány.

**Z bezpečnostních důvodů navíc nesmíte v aplikacích Blazor používat <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> .** Aplikace Blazor běží mimo kontext kanálu ASP.NET Core a není zaručena <xref:Microsoft.AspNetCore.Http.HttpContext> <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>, že by měla být k dispozici v rámci, ani zaručit, že by měla být v kontextu spuštěná aplikace Blazor.

Doporučeným způsobem, jak předat stav žádosti do aplikace Blazor, je prostřednictvím parametrů kořenové součásti při počátečním vykreslování aplikace:

* Definujte třídu se všemi daty, která chcete předat aplikaci Blazor.
* Naplňte tato data ze stránky Razor pomocí <xref:Microsoft.AspNetCore.Http.HttpContext> dostupného času.
* Předejte data do aplikace Blazor jako parametr do kořenové součásti (aplikace).
* Definujte v kořenové součásti parametr, který bude obsahovat data předávaná do aplikace.
* Použijte data specifická pro uživatele v rámci aplikace. případně můžete tato data zkopírovat do vymezené služby v rámci <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> , aby ji bylo možné použít v rámci aplikace.

Další informace a příklady kódu naleznete v tématu <xref:security/blazor/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.

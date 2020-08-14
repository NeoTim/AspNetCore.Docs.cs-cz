<span data-ttu-id="6635a-101">Rozhraní je ve výchozím nastavení automaticky otevíraná okna přihlášení a přejde zpět na přesměrování režimu přihlášení, pokud nelze otevřít automaticky otevírané okno.</span><span class="sxs-lookup"><span data-stu-id="6635a-101">The framework defaults to pop-up login mode and falls back to redirect login mode if a pop-up can't be opened.</span></span> <span data-ttu-id="6635a-102">Nakonfigurujte MSAL pro použití režimu přesměrování přihlášení nastavením `LoginMode` vlastnosti <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> na `redirect` :</span><span class="sxs-lookup"><span data-stu-id="6635a-102">Configure MSAL to use redirect login mode by setting the `LoginMode` property of <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> to `redirect`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.LoginMode = "redirect";
});
```

<span data-ttu-id="6635a-103">Výchozí nastavení je `popup` a hodnota řetězce nerozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="6635a-103">The default setting is `popup`, and the string value isn't case sensitive.</span></span>

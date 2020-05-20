> [!NOTE]
> <span data-ttu-id="11351-101">Pokud Azure Portal poskytne identifikátor URI oboru pro aplikaci a aplikace vyvolá neošetřenou výjimku, když dostane z rozhraní API *neautorizovanou odpověď 401* , zkuste použít identifikátor URI oboru, který neobsahuje schéma a hostitele.</span><span class="sxs-lookup"><span data-stu-id="11351-101">If the Azure portal provides the scope URI for the app and the app throws an unhandled exception when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="11351-102">Azure Portal například může poskytovat jeden z následujících formátů identifikátoru URI oboru:</span><span class="sxs-lookup"><span data-stu-id="11351-102">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="11351-103">Zkuste uvést identifikátor URI oboru bez schématu a hostitele:</span><span class="sxs-lookup"><span data-stu-id="11351-103">Try supplying the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

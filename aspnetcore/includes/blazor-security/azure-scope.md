> [!NOTE]
> Pokud Azure Portal poskytne identifikátor URI oboru pro aplikaci a aplikace vyvolá neošetřenou výjimku, když dostane z rozhraní API *neautorizovanou odpověď 401* , zkuste použít identifikátor URI oboru, který neobsahuje schéma a hostitele. Azure Portal například může poskytovat jeden z následujících formátů identifikátoru URI oboru:
>
> * `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{SERVER API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> Zkuste uvést identifikátor URI oboru bez schématu a hostitele:
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{SERVER API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```
>
> Příklad:
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
> ```

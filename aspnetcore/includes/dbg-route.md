## <a name="debug-diagnostics"></a><span data-ttu-id="33a5e-101">Diagnostika ladění</span><span class="sxs-lookup"><span data-stu-id="33a5e-101">Debug diagnostics</span></span>

<span data-ttu-id="33a5e-102">Pro podrobný výstup diagnostiky směrování nastavte `Logging:LogLevel:Microsoft` na `Debug`.</span><span class="sxs-lookup"><span data-stu-id="33a5e-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="33a5e-103">Například ve vývojovém prostředí nastavte *appSettings. Vývoj. JSON*:</span><span class="sxs-lookup"><span data-stu-id="33a5e-103">For example, in the development environment, set *appsettings.Development.json*:</span></span>

```JSON
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Debug",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
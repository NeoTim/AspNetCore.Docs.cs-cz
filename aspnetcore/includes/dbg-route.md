## <a name="debug-diagnostics"></a><span data-ttu-id="5cdda-101">Diagnostika ladění</span><span class="sxs-lookup"><span data-stu-id="5cdda-101">Debug diagnostics</span></span>

<span data-ttu-id="5cdda-102">Pro podrobný výstup diagnostiky směrování nastavte `Logging:LogLevel:Microsoft` na `Debug` .</span><span class="sxs-lookup"><span data-stu-id="5cdda-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="5cdda-103">Ve vývojovém prostředí nastavte úroveň protokolu v *appsettings.Development.jsna*:</span><span class="sxs-lookup"><span data-stu-id="5cdda-103">In the development environment, set the log level in *appsettings.Development.json*:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Debug",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```

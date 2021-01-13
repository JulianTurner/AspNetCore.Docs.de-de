## <a name="usermanager-and-signinmanager"></a><span data-ttu-id="fbcf0-101">UserManager und SignInManager</span><span class="sxs-lookup"><span data-stu-id="fbcf0-101">UserManager and SignInManager</span></span>

<span data-ttu-id="fbcf0-102">Legen Sie den Benutzer-ID-Anspruchstyp fest, wenn eine Server-App Folgendes erfordert:</span><span class="sxs-lookup"><span data-stu-id="fbcf0-102">Set the user identifier claim type when a Server app requires:</span></span>

* <span data-ttu-id="fbcf0-103"><xref:Microsoft.AspNetCore.Identity.UserManager%601> oder <xref:Microsoft.AspNetCore.Identity.SignInManager%601> in einem API-Endpunkt.</span><span class="sxs-lookup"><span data-stu-id="fbcf0-103"><xref:Microsoft.AspNetCore.Identity.UserManager%601> or <xref:Microsoft.AspNetCore.Identity.SignInManager%601> in an API endpoint.</span></span>
* <span data-ttu-id="fbcf0-104"><xref:Microsoft.AspNetCore.Identity.IdentityUser>-Details wie Benutzername, E-Mail-Adresse oder Ende der Sperrung.</span><span class="sxs-lookup"><span data-stu-id="fbcf0-104"><xref:Microsoft.AspNetCore.Identity.IdentityUser> details, such as the user's name, email address, or lockout end time.</span></span>

<span data-ttu-id="fbcf0-105">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="fbcf0-105">In `Startup.ConfigureServices`:</span></span>

```csharp
using System.Security.Claims;

...

services.Configure<IdentityOptions>(options => 
    options.ClaimsIdentity.UserIdClaimType = ClaimTypes.NameIdentifier);
```

<span data-ttu-id="fbcf0-106">In den folgenden `WeatherForecastController`-Protokollen wird der <xref:Microsoft.AspNetCore.Identity.IdentityUser%601.UserName> protokolliert, wenn die `Get`-Methode aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="fbcf0-106">The following `WeatherForecastController` logs the <xref:Microsoft.AspNetCore.Identity.IdentityUser%601.UserName> when the `Get` method is called:</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Identity;
using Microsoft.Extensions.Logging;
using {APP NAMESPACE}.Server.Models;
using {APP NAMESPACE}.Shared;

namespace {APP NAMESPACE}.Server.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        private readonly UserManager<ApplicationUser> userManager;

        private static readonly string[] Summaries = new[]
        {
            "Freezing", "Bracing", "Chilly", "Cool", "Mild", "Warm", 
            "Balmy", "Hot", "Sweltering", "Scorching"
        };

        private readonly ILogger<WeatherForecastController> logger;

        public WeatherForecastController(ILogger<WeatherForecastController> logger, 
            UserManager<ApplicationUser> userManager)
        {
            this.logger = logger;
            this.userManager = userManager;
        }

        [HttpGet]
        public async Task<IEnumerable<WeatherForecast>> Get()
        {
            var rng = new Random();

            var user = await userManager.GetUserAsync(User);

            if (user != null)
            {
                logger.LogInformation($"User.Identity.Name: {user.UserName}");
            }

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = DateTime.Now.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = Summaries[rng.Next(Summaries.Length)]
            })
            .ToArray();
        }
    }
}
```

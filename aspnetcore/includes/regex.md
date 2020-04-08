> [!WARNING]
> Übergeben Sie ein Timeout, wenn Sie <xref:System.Text.RegularExpressions> zum Verarbeiten nicht vertrauenswürdiger Eingaben verwenden. Ein böswilliger Benutzer kann Eingaben für `RegularExpressions` angeben, um einen [Denial-of-Service-Angriff](https://www.us-cert.gov/ncas/tips/ST04-015) durchzuführen. ASP.NET Core-Framework-APIs, die `RegularExpressions` verwenden, übergeben ein Timeout.
**JWT** es un estándar abierto (RFC 7519) que define un formato compacto y autónomo para transmitir información entre partes como un objeto JSON. Estos tokens pueden ser utilizados para autenticar a los usuarios y proporcionar acceso a recursos en una API.

**Componentes de JWT:**
- **Header (Encabezado):** Contiene el tipo de token (JWT) y el algoritmo de firma (por ejemplo, HMAC SHA256).
- **Payload (Carga útil):** Contiene las afirmaciones (claims) que pueden incluir información del usuario, como el ID del usuario y los roles.
- **Signature (Firma):** Se utiliza para verificar que el mensaje no ha sido alterado. La firma se genera combinando el encabezado codificado y la carga útil codificada, y luego se firma con una clave secreta.

**Ejemplo de Implementación de JWT en .NET Core:**

1. **Generación de Token:**

   ```csharp
   using System;
   using System.IdentityModel.Tokens.Jwt;
   using System.Security.Claims;
   using System.Text;
   using Microsoft.IdentityModel.Tokens;

   public class TokenService
   {
       private readonly string _secretKey = "your_secret_key_here";

       public string GenerateToken(string userId)
       {
           var claims = new[]
           {
               new Claim(JwtRegisteredClaimNames.Sub, userId),
               new Claim(JwtRegisteredClaimNames.Jti, Guid.NewGuid().ToString())
           };

           var key = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(_secretKey));
           var creds = new SigningCredentials(key, SecurityAlgorithms.HmacSha256);

           var token = new JwtSecurityToken(
               issuer: "your_issuer",
               audience: "your_audience",
               claims: claims,
               expires: DateTime.Now.AddHours(1),
               signingCredentials: creds);

           return new JwtSecurityTokenHandler().WriteToken(token);
       }
   }
   ```

2. **Validación del Token en un Middleware:**

   ```csharp
   using Microsoft.AspNetCore.Authentication.JwtBearer;
   using Microsoft.Extensions.DependencyInjection;

   public class Startup
   {
       public void ConfigureServices(IServiceCollection services)
       {
           services.AddAuthentication(x =>
           {
               x.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
               x.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
           })
           .AddJwtBearer(x =>
           {
               x.TokenValidationParameters = new TokenValidationParameters
               {
                   ValidateIssuer = true,
                   ValidateAudience = true,
                   ValidateLifetime = true,
                   ValidateIssuerSigningKey = true,
                   ValidIssuer = "your_issuer",
                   ValidAudience = "your_audience",
                   IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("your_secret_key_here"))
               };
           });

           services.AddControllers();
       }
   }
   ```

**Ventajas de JWT:**
- **Autónomo:** El token contiene toda la información necesaria para la autenticación.
- **Escalable:** No requiere almacenamiento en el servidor.

**Desventajas de JWT:**
- **Tamaño del Token:** Los tokens pueden ser grandes, especialmente con muchos claims.
- **Revocación:** Los tokens no pueden ser revocados fácilmente antes de su expiración.

---

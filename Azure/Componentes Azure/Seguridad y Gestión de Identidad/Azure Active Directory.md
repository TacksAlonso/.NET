Azure Active Directory (Azure AD) es un servicio de administración de identidades y acceso basado en la nube de Microsoft, que ayuda a tus empleados a iniciar sesión y acceder a recursos externos (como Microsoft 365, el portal de Azure, y miles de otras aplicaciones SaaS) y recursos internos (como aplicaciones en la red corporativa e intranet). A continuación se presenta una explicación detallada sobre cómo integrar y utilizar Azure AD con aplicaciones .NET Core.

### 1. Conceptos Clave de Azure AD

#### 1.1. **Inquilino de Azure AD**
Un inquilino de Azure AD es una instancia dedicada de Azure AD que recibe una organización y donde se almacenan las identidades de los usuarios.

#### 1.2. **Aplicación Registrada**
Una aplicación registrada en Azure AD es una representación de una aplicación que necesita autenticarse con Azure AD para obtener tokens y acceder a recursos.

#### 1.3. **Grupos y Roles**
Los grupos y roles en Azure AD permiten la administración y autorización de usuarios, facilitando el acceso basado en roles (RBAC).

### 2. Registro de la Aplicación en Azure AD

1. Inicia sesión en el [Portal de Azure](https://portal.azure.com/).
2. Navega a **Azure Active Directory** > **Registros de aplicaciones** > **Nuevo registro**.
3. Completa los detalles de la aplicación (nombre, URL de redirección, etc.) y selecciona **Registrar**.

### 3. Configuración de la Aplicación .NET Core

#### 3.1. Instalar Paquetes NuGet

Agrega los paquetes necesarios para integrar Azure AD en tu aplicación .NET Core:

```bash
dotnet add package Microsoft.Identity.Web
dotnet add package Microsoft.Identity.Web.MicrosoftGraph
dotnet add package Microsoft.AspNetCore.Authentication.OpenIdConnect
```

#### 3.2. Configurar `appsettings.json`

Añade la configuración de Azure AD en tu archivo `appsettings.json`:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "yourdomain.onmicrosoft.com",
    "TenantId": "your-tenant-id",
    "ClientId": "your-client-id",
    "ClientSecret": "your-client-secret",
    "CallbackPath": "/signin-oidc"
  }
}
```

#### 3.3. Configurar la Autenticación en `Startup.cs`

Configura la autenticación y autorización en tu aplicación .NET Core.

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.Identity.Web;
using Microsoft.Identity.Web.UI;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
            .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"));

        services.AddControllersWithViews()
            .AddMicrosoftIdentityUI();
        
        services.AddAuthorization(options =>
        {
            // Configura políticas de autorización aquí
        });
    }

    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }
        else
        {
            app.UseExceptionHandler("/Home/Error");
            app.UseHsts();
        }

        app.UseHttpsRedirection();
        app.UseStaticFiles();
        app.UseRouting();
        app.UseAuthentication();
        app.UseAuthorization();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllerRoute(
                name: "default",
                pattern: "{controller=Home}/{action=Index}/{id?}");
            endpoints.MapRazorPages();
        });
    }
}
```

### 4. Uso de Microsoft Graph

Microsoft Graph proporciona una API unificada para acceder a datos de Microsoft 365. Puedes usarlo para obtener información del usuario autenticado.

#### 4.1. Configurar Permisos en Azure AD

1. En el portal de Azure, navega a **Azure AD** > **Registros de aplicaciones** > selecciona tu aplicación.
2. En **Permisos de API**, agrega los permisos necesarios (por ejemplo, `User.Read`).

#### 4.2. Usar Microsoft Graph en tu Aplicación

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Graph;
using Microsoft.Identity.Web;
using Microsoft.Identity.Web.Resource;

[Authorize]
public class ProfileController : Controller
{
    private readonly GraphServiceClient _graphServiceClient;

    public ProfileController(GraphServiceClient graphServiceClient)
    {
        _graphServiceClient = graphServiceClient;
    }

    [AuthorizeForScopes(Scopes = new[] { "User.Read" })]
    public async Task<IActionResult> Index()
    {
        var user = await _graphServiceClient.Me.Request().GetAsync();
        return View(user);
    }
}
```

### 5. Autorización Basada en Roles

Azure AD permite la autorización basada en roles para controlar el acceso a partes de tu aplicación.

#### 5.1. Configurar Roles en Azure AD

1. En el portal de Azure, navega a **Azure AD** > **Registros de aplicaciones** > selecciona tu aplicación.
2. En **Roles y administradores** o **Manifiesto**, agrega los roles necesarios.

#### 5.2. Usar Roles en tu Aplicación

```csharp
[Authorize(Roles = "Admin")]
public class AdminController : Controller
{
    public IActionResult Index()
    {
        return View();
    }
}
```

### 6. Seguridad Adicional

#### 6.1. Validación de Tokens

Puedes agregar validación adicional para los tokens emitidos por Azure AD.

```csharp
services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"))
    .EnableTokenAcquisitionToCallDownstreamApi()
    .AddMicrosoftGraph(Configuration.GetSection("Graph"))
    .AddInMemoryTokenCaches();
```

### Resumen

1. **Registro de Aplicaciones**: Aprende a registrar tu aplicación en Azure AD y configurar los detalles necesarios.
2. **Configuración de .NET Core**: Configura tu aplicación .NET Core para usar Azure AD con autenticación y autorización.
3. **Microsoft Graph**: Utiliza Microsoft Graph para acceder a datos de Microsoft 365.
4. **Autorización Basada en Roles**: Configura y usa roles para controlar el acceso en tu aplicación.
5. **Seguridad Adicional**: Implementa validaciones adicionales para los tokens de Azure AD.

Azure AD proporciona una solución robusta para la gestión de identidades y acceso, facilitando la integración de autenticación y autorización en tus aplicaciones .NET Core.
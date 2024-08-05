Las Azure Virtual Machines (VMs) son un servicio de computación en la nube que proporciona máquinas virtuales de Windows y Linux escalables bajo demanda. Las VMs permiten ejecutar aplicaciones y servicios en la nube sin necesidad de adquirir y mantener hardware físico. A continuación, se explica cómo utilizar Azure Virtual Machines con .NET Core, cubriendo la creación, configuración y despliegue de aplicaciones en una VM.

### Creación y Configuración de Azure Virtual Machines

1. **Crear una Máquina Virtual en Azure**

   Primero, necesitas una cuenta de Azure. Una vez que tengas una, puedes seguir estos pasos para crear una VM:

   - Ve al [portal de Azure](https://portal.azure.com/).
   - Haz clic en "Crear un recurso" y selecciona "Máquina virtual".
   - Configura las opciones básicas, como el nombre de la VM, el sistema operativo (Windows o Linux), el tamaño y las credenciales de administrador.
   - Configura las opciones de red, almacenamiento y otros ajustes según tus necesidades.
   - Revisa y crea la VM.

2. **Conectar a la VM**

   - **Windows**: Puedes usar RDP (Remote Desktop Protocol) para conectarte a una VM de Windows.
     ```bash
     mstsc /v:YourPublicIP
     ```
   - **Linux**: Puedes usar SSH para conectarte a una VM de Linux.
     ```bash
     ssh YourUsername@YourPublicIP
     ```

### Desplegar una Aplicación .NET Core en una Azure Virtual Machine

1. **Preparar la Aplicación .NET Core**

   Crea una aplicación .NET Core o usa una existente. Asegúrate de que la aplicación esté configurada para ejecutarse en el sistema operativo de tu VM (Windows o Linux).

   ```bash
   dotnet new webapp -o MyWebApp
   cd MyWebApp
   dotnet publish -c Release -o out
   ```

2. **Configurar el Entorno en la VM**

   - **Windows**: Instala .NET Core Runtime/SDK.
     ```powershell
     # Conéctate a la VM y ejecuta:
     Invoke-WebRequest -Uri https://dotnet.microsoft.com/download/dotnet-core/scripts/v1/dotnet-install.ps1 -OutFile dotnet-install.ps1
     .\dotnet-install.ps1 -Channel 5.0
     ```

   - **Linux**: Instala .NET Core Runtime/SDK.
     ```bash
     # Conéctate a la VM y ejecuta:
     wget https://dotnet.microsoft.com/download/dotnet-core/scripts/v1/dotnet-install.sh
     chmod +x dotnet-install.sh
     ./dotnet-install.sh --channel 5.0
     ```

3. **Desplegar la Aplicación en la VM**

   - **Windows**: Copia los archivos publicados a la VM y configura IIS (si es necesario).
     ```bash
     # Usa SCP para copiar archivos:
     scp -r ./out/* YourUsername@YourPublicIP:C:\path\to\app
     ```

     Configura IIS para servir la aplicación o usa `dotnet` para ejecutar la aplicación directamente:
     ```powershell
     cd C:\path\to\app
     dotnet MyWebApp.dll
     ```

   - **Linux**: Copia los archivos publicados a la VM y configura un servidor web (Nginx, Apache, etc.).
     ```bash
     # Usa SCP para copiar archivos:
     scp -r ./out/* YourUsername@YourPublicIP:/var/www/mywebapp
     ```

     Configura Nginx para servir la aplicación:
     ```bash
     # Instalar Nginx:
     sudo apt-get update
     sudo apt-get install nginx

     # Configurar Nginx:
     sudo nano /etc/nginx/sites-available/default
     ```

     Agrega la configuración para tu aplicación .NET Core:
     ```nginx
     server {
         listen 80;
         server_name your_domain_or_ip;

         location / {
             proxy_pass http://localhost:5000;
             proxy_http_version 1.1;
             proxy_set_header Upgrade $http_upgrade;
             proxy_set_header Connection keep-alive;
             proxy_set_header Host $host;
             proxy_cache_bypass $http_upgrade;
         }
     }
     ```

     Reinicia Nginx y ejecuta tu aplicación:
     ```bash
     sudo systemctl restart nginx
     cd /var/www/mywebapp
     dotnet MyWebApp.dll
     ```

### Monitoreo y Escalado

1. **Monitoreo**

   Azure proporciona varias herramientas para monitorear el rendimiento y el estado de tus VMs:

   - **Azure Monitor**: Servicio para recopilar y analizar datos de telemetría de tus VMs.
   - **Log Analytics**: Herramienta para consultas avanzadas de logs y datos de monitoreo.
   - **Azure Application Insights**: Servicio para monitorear el rendimiento de aplicaciones .NET Core.

2. **Escalado**

   Las VMs en Azure pueden ser escaladas manualmente o automáticamente según las necesidades:

   - **Escalado Manual**: Cambia el tamaño de la VM o aumenta el número de instancias a través del portal de Azure.
   - **Escalado Automático**: Configura reglas de escalado automático basadas en métricas de rendimiento.

### Ejemplo Completo

Aquí hay un ejemplo completo de cómo desplegar una aplicación ASP.NET Core en una VM de Linux en Azure:

1. **Crear y publicar la aplicación ASP.NET Core**

   ```bash
   dotnet new webapp -o MyWebApp
   cd MyWebApp
   dotnet publish -c Release -o out
   ```

2. **Configurar y conectar a la VM**

   ```bash
   ssh YourUsername@YourPublicIP
   ```

3. **Instalar .NET Core en la VM**

   ```bash
   wget https://dotnet.microsoft.com/download/dotnet-core/scripts/v1/dotnet-install.sh
   chmod +x dotnet-install.sh
   ./dotnet-install.sh --channel 5.0
   ```

4. **Instalar y configurar Nginx**

   ```bash
   sudo apt-get update
   sudo apt-get install nginx
   sudo nano /etc/nginx/sites-available/default
   ```

   Configura Nginx para la aplicación:

   ```nginx
   server {
       listen 80;
       server_name your_domain_or_ip;

       location / {
           proxy_pass http://localhost:5000;
           proxy_http_version 1.1;
           proxy_set_header Upgrade $http_upgrade;
           proxy_set_header Connection keep-alive;
           proxy_set_header Host $host;
           proxy_cache_bypass $http_upgrade;
       }
   }
   ```

5. **Copiar archivos y ejecutar la aplicación**

   ```bash
   scp -r ./out/* YourUsername@YourPublicIP:/var/www/mywebapp
   sudo systemctl restart nginx
   cd /var/www/mywebapp
   dotnet MyWebApp.dll
   ```

Con estos pasos, tu aplicación ASP.NET Core estará desplegada y funcionando en una Azure Virtual Machine.
Azure DevOps es un conjunto de servicios para colaborar en el desarrollo de software, implementando prácticas de DevOps para automatizar la entrega de aplicaciones. Proporciona herramientas para la planificación, desarrollo, entrega y monitoreo de aplicaciones. Los componentes principales incluyen Azure Repos, Azure Pipelines, Azure Boards, Azure Test Plans y Azure Artifacts. A continuación, se proporciona una explicación detallada de cómo integrar y usar Azure DevOps con .NET Core.

### Componentes de Azure DevOps

1. **Azure Repos**: Proporciona repositorios Git privados para la gestión del código fuente.
2. **Azure Pipelines**: Herramientas de CI/CD para la automatización de la construcción, prueba y despliegue de aplicaciones.
3. **Azure Boards**: Tableros Kanban, informes y herramientas de seguimiento para la gestión de proyectos.
4. **Azure Test Plans**: Herramientas de prueba para la planificación, rastreo y ejecución de pruebas manuales y automatizadas.
5. **Azure Artifacts**: Gestión y compartición de paquetes de código (NuGet, npm, etc.).

### Integración de Azure DevOps con .NET Core

#### 1. Configuración del Proyecto

1. **Crear un Proyecto en Azure DevOps**

   - Navega a [Azure DevOps](https://dev.azure.com/).
   - Crea una nueva organización y un nuevo proyecto.

2. **Configurar un Repositorio Git en Azure Repos**

   - Clona el repositorio creado en Azure Repos a tu máquina local.

     ```bash
     git clone <your-repo-url>
     cd <your-repo-folder>
     ```

3. **Añadir un Proyecto .NET Core**

   - Crea una nueva aplicación .NET Core.

     ```bash
     dotnet new webapp -n MyWebApp
     cd MyWebApp
     dotnet new sln -n MySolution
     dotnet sln add MyWebApp/MyWebApp.csproj
     ```

   - Añade y confirma los cambios en el repositorio.

     ```bash
     git add .
     git commit -m "Initial commit"
     git push origin master
     ```

#### 2. Configurar Azure Pipelines

1. **Crear un Pipeline de Construcción**

   - En el portal de Azure DevOps, navega a Pipelines > Pipelines > New pipeline.
   - Selecciona tu repositorio y elige YAML para definir tu pipeline.
   - Usa la siguiente configuración YAML para construir y probar tu aplicación .NET Core.

     ```yaml
     trigger:
       - master

     pool:
       vmImage: 'ubuntu-latest'

     steps:
     - task: UseDotNet@2
       inputs:
         packageType: 'sdk'
         version: '5.x'
         installationPath: $(Agent.ToolsDirectory)/dotnet

     - script: dotnet build --configuration Release
       displayName: 'Build project'

     - script: dotnet test --no-build --verbosity normal
       displayName: 'Run tests'
     ```

2. **Guardar y Ejecutar el Pipeline**

   - Guarda el archivo como `azure-pipelines.yml` en el repositorio y ejecuta el pipeline.
   - Verifica que el pipeline se ejecute correctamente.

#### 3. Configurar Azure Releases

1. **Crear un Pipeline de Release**

   - En el portal de Azure DevOps, navega a Pipelines > Releases > New pipeline.
   - Define una nueva etapa (Stage) y elige una plantilla de Azure App Service Deployment.
   - Configura la conexión a tu Azure App Service.

2. **Configurar los Artefactos**

   - En la pestaña de artefactos, selecciona el artefacto del pipeline de construcción.

3. **Definir la Tarea de Despliegue**

   - Configura las tareas necesarias para desplegar tu aplicación .NET Core.

     ```bash
     - task: AzureRmWebAppDeployment@4
       inputs:
         ConnectionType: 'AzureRM'
         azureSubscription: '<your-azure-subscription>'
         appType: 'webApp'
         WebAppName: '<your-app-name>'
         packageForLinux: '$(System.DefaultWorkingDirectory)/**/*.zip'
     ```

4. **Guardar y Ejecutar el Release**

   - Guarda el pipeline y ejecuta un nuevo release para desplegar la aplicación.

#### 4. Gestión y Monitoreo

1. **Azure Boards**

   - Usa Azure Boards para planificar y rastrear el trabajo con tableros Kanban, backlogs y sprints.
   - Crea work items, tareas, bugs y epics para gestionar tu proyecto.

2. **Azure Test Plans**

   - Crea y ejecuta planes de prueba para asegurarte de que tu aplicación cumple con los requisitos.
   - Integra pruebas automatizadas en tus pipelines de CI/CD.

3. **Azure Artifacts**

   - Publica y comparte paquetes de código (NuGet, npm) utilizando Azure Artifacts.
   - Usa feeds privados para gestionar las dependencias de tu proyecto.

### Ejemplo de Integración de Azure DevOps con .NET Core

#### `azure-pipelines.yml`

```yaml
trigger:
- master

pool:
  vmImage: 'ubuntu-latest'

variables:
  buildConfiguration: 'Release'

steps:
- task: UseDotNet@2
  inputs:
    packageType: 'sdk'
    version: '5.x'
    installationPath: $(Agent.ToolsDirectory)/dotnet

- script: dotnet restore
  displayName: 'Restore dependencies'

- script: dotnet build --configuration $(buildConfiguration)
  displayName: 'Build project'

- script: dotnet test --no-build --verbosity normal
  displayName: 'Run tests'

- task: ArchiveFiles@2
  inputs:
    rootFolderOrFile: '$(Build.ArtifactStagingDirectory)'
    includeRootFolder: false
    archiveType: 'zip'
    archiveFile: '$(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip'
    replaceExistingArchive: true

- publish: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
  artifact: drop
```

Este archivo YAML configura un pipeline de CI que:

1. Restaura las dependencias del proyecto.
2. Compila el proyecto en modo Release.
3. Ejecuta las pruebas unitarias.
4. Archiva los archivos de salida.
5. Publica el artefacto generado.

### Conclusión

Azure DevOps ofrece un conjunto completo de herramientas para la implementación de prácticas de DevOps en proyectos .NET Core. Desde la gestión del código fuente y la planificación del proyecto hasta la construcción y el despliegue automatizado, Azure DevOps facilita la entrega continua de software de alta calidad. Al integrar Azure DevOps con .NET Core, puedes aprovechar las capacidades de CI/CD, gestionar eficientemente tu ciclo de vida de desarrollo y mejorar la colaboración dentro del equipo de desarrollo.
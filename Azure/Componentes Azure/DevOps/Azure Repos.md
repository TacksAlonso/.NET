Azure Repos es un conjunto de herramientas de control de versiones que puedes utilizar para gestionar tu código fuente. Ofrece repositorios Git privados y repositorios de control de versiones de Team Foundation (TFVC), proporcionando una solución escalable y gestionada para compartir código y colaborar en proyectos de software. A continuación, se presenta una explicación más amplia y detallada sobre cómo usar Azure Repos con .NET Core.

### 1. Crear un Repositorio en Azure Repos

#### 1.1. Crear un Proyecto en Azure DevOps

1. Inicia sesión en [Azure DevOps](https://dev.azure.com/).
2. Crea un nuevo proyecto o selecciona uno existente.
3. Navega a la sección **Repos** en el menú lateral.

#### 1.2. Crear un Repositorio Git

1. En la sección **Repos**, haz clic en **Files** y selecciona **New Repository**.
2. Elige el tipo de repositorio (Git es el más común).
3. Proporciona un nombre para tu repositorio y haz clic en **Create**.

### 2. Clonar y Trabajar con un Repositorio en .NET Core

#### 2.1. Clonar el Repositorio

1. Copia la URL del repositorio que acabas de crear.
2. En tu terminal o línea de comandos, clona el repositorio:

```bash
git clone https://dev.azure.com/your-organization/your-project/_git/your-repository
```

#### 2.2. Crear una Aplicación .NET Core

1. Navega al directorio del repositorio clonado:

```bash
cd your-repository
```

2. Crea una nueva aplicación .NET Core:

```bash
dotnet new webapi -n MyApi
cd MyApi
```

3. Añade los archivos generados al control de versiones:

```bash
git add .
git commit -m "Initial commit with .NET Core Web API"
```

4. Empuja los cambios al repositorio:

```bash
git push origin main
```

### 3. Integrar con Azure Pipelines

Azure Pipelines permite automatizar la construcción, pruebas y despliegue de tu aplicación. Aquí se muestra cómo configurar una canalización para una aplicación .NET Core.

#### 3.1. Crear una Pipeline

1. En tu proyecto de Azure DevOps, navega a **Pipelines** y selecciona **New Pipeline**.
2. Conecta tu repositorio seleccionando **Azure Repos Git**.
3. Selecciona el repositorio y configura tu pipeline.

#### 3.2. Configurar el Pipeline YAML

1. Azure DevOps sugerirá un archivo YAML de ejemplo. Puedes personalizarlo como se muestra a continuación:

```yaml
trigger:
- main

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

- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(Build.ArtifactStagingDirectory)'
    ArtifactName: 'drop'
```

2. Guarda y ejecuta la pipeline.

### 4. Gestión de Pull Requests (PR)

Azure Repos facilita la gestión de PRs para revisar y aprobar cambios antes de fusionarlos en la rama principal.

#### 4.1. Crear un Pull Request

1. Realiza cambios en una rama nueva:

```bash
git checkout -b feature/my-feature
# Realiza cambios y haz commits
git push origin feature/my-feature
```

2. En Azure Repos, navega a **Repos > Pull Requests** y selecciona **New Pull Request**.
3. Completa los detalles del PR y envíalo para revisión.

### 5. Políticas de Rama y Revisión de Código

Configura políticas de rama para asegurar la calidad del código.

#### 5.1. Configurar Políticas de Rama

1. En **Repos > Branches**, selecciona la rama donde deseas aplicar políticas (por ejemplo, `main`).
2. Configura políticas como:
   - Revisión de código por al menos un revisor.
   - Compilación exitosa antes de completar el PR.
   - Verificación de la resolución de conflictos de fusión.

### Resumen

1. **Crear y Clonar Repositorios**: Aprende a crear y clonar repositorios en Azure Repos.
2. **Trabajar con .NET Core**: Configura una aplicación .NET Core y realiza operaciones básicas de Git.
3. **Integración con Azure Pipelines**: Configura una canalización CI/CD para construir, probar y desplegar tu aplicación.
4. **Gestión de Pull Requests**: Utiliza PRs para revisar y aprobar cambios.
5. **Políticas de Rama**: Implementa políticas de rama para asegurar la calidad del código.

Azure Repos proporciona una solución robusta para gestionar código fuente, facilitando la colaboración y la integración continua en tus proyectos .NET Core.
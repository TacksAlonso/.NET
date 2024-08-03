**1. Configuración de Pipelines de CI/CD**

**Azure DevOps Pipelines** permite la automatización de procesos de construcción y despliegue para aplicaciones. La configuración de pipelines en Azure DevOps generalmente se realiza a través de archivos YAML o mediante una interfaz gráfica en el portal de Azure DevOps.

**Conceptos Clave:**
- **CI (Integración Continua):** Automatiza el proceso de construcción y pruebas de una aplicación cada vez que se realizan cambios en el código.
- **CD (Despliegue Continuo):** Automatiza el despliegue de una aplicación en ambientes de producción o pre-producción.

**Ejemplo de Pipeline para una Aplicación .NET:**

Supongamos que tienes una aplicación .NET Core y deseas configurar un pipeline de CI/CD en Azure DevOps.

1. **Crear un Pipeline en Azure DevOps:**

   - **Accede al Portal de Azure DevOps.**
   - **Navega a tu proyecto** y selecciona **Pipelines** en el menú.
   - **Haz clic en "New Pipeline"** para crear un nuevo pipeline.

2. **Seleccionar el Repositorio:**

   - Elige el repositorio donde está almacenado tu código (GitHub, Azure Repos, etc.).

3. **Configurar el Pipeline usando YAML:**

   Aquí tienes un ejemplo de archivo YAML para un pipeline básico de CI/CD para una aplicación .NET Core:

   ```yaml
   trigger:
   - main

   pool:
     vmImage: 'ubuntu-latest'

   steps:
   - task: UseDotNet@2
     inputs:
       packageType: 'sdk'
       version: '7.x'
       installationPath: $(Agent.ToolsDirectory)/dotnet

   - script: dotnet build --configuration Release
     displayName: 'Build the project'

   - script: dotnet test --configuration Release
     displayName: 'Run tests'

   - task: PublishBuildArtifacts@1
     inputs:
       pathtoPublish: '$(Build.ArtifactStagingDirectory)'
       artifactName: 'drop'
   ```

   **Explicación del YAML:**
   - **trigger:** Define las ramas que disparan el pipeline (por ejemplo, `main`).
   - **pool:** Define la imagen de máquina virtual que se usará (por ejemplo, `ubuntu-latest`).
   - **steps:** Define las tareas a realizar: usar el SDK de .NET, construir el proyecto, ejecutar pruebas y publicar los artefactos.

4. **Guardar y Ejecutar el Pipeline:**
   - Guarda el archivo YAML y ejecuta el pipeline. Azure DevOps construirá, probará y empaquetará tu aplicación automáticamente.

**2. Gestión de Repositorios con Git**

**Azure Repos** proporciona servicios de control de versiones para el código fuente utilizando Git o Team Foundation Version Control (TFVC).

**Conceptos Clave:**
- **Branching:** Manejo de diferentes ramas del código, como `main`, `develop` y ramas de características.
- **Pull Requests (PRs):** Solicitudes para fusionar cambios en ramas específicas.
- **Gitflow:** Estrategia de branching que utiliza ramas específicas para desarrollo, características y liberaciones.

**Ejemplo de Implementación de Gitflow:**

1. **Crear Ramas:**
   Utiliza la línea de comandos o una herramienta GUI para crear ramas siguiendo la estrategia de Gitflow.

   ```bash
   # Crear una rama de desarrollo
   git checkout -b develop

   # Crear una rama de características
   git checkout -b feature/feature-name develop

   # Crear una rama de liberación
   git checkout -b release/1.0 develop

   # Crear una rama de corrección de errores
   git checkout -b hotfix/1.0.1 main
   ```

2. **Trabajar en una Rama de Características:**
   Realiza cambios en la rama de características, realiza commits y empuja los cambios al repositorio remoto.

   ```bash
   # Cambiar a la rama de características
   git checkout feature/feature-name

   # Hacer cambios y realizar commit
   git add .
   git commit -m "Implementar nueva característica"

   # Empujar cambios al repositorio remoto
   git push origin feature/feature-name
   ```

3. **Crear un Pull Request:**
   - En el portal de Azure DevOps, navega a **Repos** y selecciona la pestaña **Pull Requests**.
   - Crea un nuevo pull request para fusionar la rama de características en `develop`.
   - Revisa el pull request, añade comentarios si es necesario y completa la fusión.

4. **Desplegar Cambios:**
   Una vez que los cambios han sido aprobados y fusionados en `develop`, puedes configurar el pipeline para desplegar automáticamente los cambios en el ambiente de prueba o producción.

#### **Práctica**

**Configurar un Pipeline Básico para una Aplicación .NET:**

1. **Preparar el Proyecto:**
   Asegúrate de tener una aplicación .NET Core con un archivo de solución y pruebas unitarias.

2. **Configurar el Pipeline en Azure DevOps:**
   Utiliza el archivo YAML proporcionado anteriormente para definir los pasos de construcción y prueba. Puedes agregar tareas adicionales según las necesidades de tu aplicación, como despliegue a un servidor o contenedor.

**Implementar Gitflow en tus Proyectos:**

1. **Establecer el Flujo de Trabajo:**
   Define las ramas principales (`main` y `develop`) y ramas de características (`feature/*`), liberaciones (`release/*`), y corrección de errores (`hotfix/*`).

2. **Documentar el Proceso:**
   Documenta el flujo de trabajo de Gitflow en tu equipo para asegurar que todos sigan la misma estrategia al trabajar en el código.

3. **Integrar con Pipelines:**
   Configura los pipelines para ejecutar automáticamente construcciones y pruebas para cada rama, y para desplegar cambios desde ramas específicas.

### **Resumen**

- **Pipelines de CI/CD:** Automatizan la construcción, prueba y despliegue de aplicaciones. Puedes configurarlos usando archivos YAML en Azure DevOps.
- **Gestión de Repositorios con Git:** Utiliza estrategias como Gitflow para gestionar el código fuente, ramas y pull requests en Azure Repos.

Implementar estos principios en tu flujo de trabajo de desarrollo asegura que el código se construya y despliegue de manera consistente y automatizada, y facilita una colaboración efectiva dentro de equipos de desarrollo.
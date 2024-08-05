Azure Kubernetes Service (AKS) es un servicio gestionado de Kubernetes que simplifica la implementación, administración y operación de clústeres de Kubernetes. AKS proporciona una forma escalable y altamente disponible de ejecutar aplicaciones en contenedores. A continuación, se detalla cómo utilizar AKS con una aplicación .NET Core, cubriendo la configuración del entorno, la creación de clústeres, el despliegue de aplicaciones y la administración de clústeres.

### Características Clave de Azure Kubernetes Service

1. **Despliegue y Gestión Simplificada**: AKS automatiza las operaciones de Kubernetes, como la actualización, la reparación, la supervisión y la escalabilidad.
2. **Seguridad y Gobernanza**: Integración con Azure Active Directory (AAD) y control de acceso basado en roles (RBAC).
3. **Integración con Servicios de Azure**: Conexión con otros servicios como Azure Monitor, Azure DevOps, Azure Container Registry (ACR) y más.
4. **Alta Disponibilidad y Escalabilidad**: Proporciona un entorno resistente y escalable para aplicaciones de misión crítica.
5. **Automatización y CI/CD**: Integración con pipelines de CI/CD para despliegues automatizados.

### Configuración del Entorno

1. **Instalar Herramientas Necesarias**

   Asegúrate de tener instaladas las siguientes herramientas en tu máquina local:

   - [Azure CLI](https://docs.microsoft.com/es-es/cli/azure/install-azure-cli)
   - [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
   - [Docker](https://docs.docker.com/get-docker/)

2. **Autenticarse en Azure**

   Inicia sesión en tu cuenta de Azure:

   ```bash
   az login
   ```

### Crear un Clúster de AKS

1. **Crear un Grupo de Recursos**

   ```bash
   az group create --name MyResourceGroup --location eastus
   ```

2. **Crear un Clúster de AKS**

   ```bash
   az aks create --resource-group MyResourceGroup --name MyAKSCluster --node-count 1 --enable-addons monitoring --generate-ssh-keys
   ```

3. **Configurar kubectl para Usar el Clúster**

   ```bash
   az aks get-credentials --resource-group MyResourceGroup --name MyAKSCluster
   ```

### Desplegar una Aplicación .NET Core en AKS

1. **Crear una Aplicación .NET Core**

   Crea una aplicación .NET Core o usa una existente:

   ```bash
   dotnet new webapi -o MyWebApi
   cd MyWebApi
   ```

2. **Crear un Dockerfile**

   En el directorio de tu proyecto, crea un archivo `Dockerfile`:

   ```dockerfile
   FROM mcr.microsoft.com/dotnet/aspnet:5.0 AS base
   WORKDIR /app
   EXPOSE 80
   EXPOSE 443

   FROM mcr.microsoft.com/dotnet/sdk:5.0 AS build
   WORKDIR /src
   COPY ["MyWebApi.csproj", "."]
   RUN dotnet restore "./MyWebApi.csproj"
   COPY . .
   WORKDIR "/src/."
   RUN dotnet build "MyWebApi.csproj" -c Release -o /app/build

   FROM build AS publish
   RUN dotnet publish "MyWebApi.csproj" -c Release -o /app/publish

   FROM base AS final
   WORKDIR /app
   COPY --from=publish /app/publish .
   ENTRYPOINT ["dotnet", "MyWebApi.dll"]
   ```

3. **Construir y Etiquetar la Imagen Docker**

   ```bash
   docker build -t mywebapi:v1 .
   ```

4. **Subir la Imagen a Azure Container Registry (ACR)**

   - **Crear un ACR**

     ```bash
     az acr create --resource-group MyResourceGroup --name MyACR --sku Basic
     ```

   - **Iniciar Sesión en ACR**

     ```bash
     az acr login --name MyACR
     ```

   - **Etiquetar y Subir la Imagen**

     ```bash
     docker tag mywebapi:v1 MyACR.azurecr.io/mywebapi:v1
     docker push MyACR.azurecr.io/mywebapi:v1
     ```

5. **Crear un Archivo de Despliegue de Kubernetes**

   Crea un archivo `deployment.yaml`:

   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: mywebapi-deployment
   spec:
     replicas: 2
     selector:
       matchLabels:
         app: mywebapi
     template:
       metadata:
         labels:
           app: mywebapi
       spec:
         containers:
         - name: mywebapi
           image: MyACR.azurecr.io/mywebapi:v1
           ports:
           - containerPort: 80
   ---
   apiVersion: v1
   kind: Service
   metadata:
     name: mywebapi-service
   spec:
     type: LoadBalancer
     ports:
     - port: 80
     selector:
       app: mywebapi
   ```

6. **Desplegar la Aplicación en AKS**

   ```bash
   kubectl apply -f deployment.yaml
   ```

### Administración del Clúster

1. **Escalado de Pods**

   Escala el número de réplicas de tu despliegue:

   ```bash
   kubectl scale --replicas=3 deployment/mywebapi-deployment
   ```

2. **Actualización de la Imagen del Contenedor**

   Actualiza la imagen de tu aplicación:

   ```bash
   docker build -t mywebapi:v2 .
   docker tag mywebapi:v2 MyACR.azurecr.io/mywebapi:v2
   docker push MyACR.azurecr.io/mywebapi:v2
   kubectl set image deployment/mywebapi-deployment mywebapi=MyACR.azurecr.io/mywebapi:v2
   ```

3. **Monitoreo del Clúster**

   Azure Monitor para contenedores proporciona supervisión para el clúster de AKS:

   - Configura el monitoreo desde el portal de Azure en la sección de AKS bajo "Insights".
   - Utiliza Azure Monitor para ver métricas y logs de los pods y nodos.

4. **Seguridad y Acceso**

   Utiliza Azure Active Directory (AAD) para autenticar el acceso al clúster de Kubernetes:

   ```bash
   az aks update -g MyResourceGroup -n MyAKSCluster --enable-aad
   az aks get-credentials -g MyResourceGroup -n MyAKSCluster --admin
   ```

5. **Actualización de AKS**

   Actualiza el clúster de AKS a una nueva versión de Kubernetes:

   ```bash
   az aks upgrade --resource-group MyResourceGroup --name MyAKSCluster --kubernetes-version 1.21.2
   ```

### Ejemplo Completo

A continuación, se muestra un ejemplo completo de cómo desplegar y gestionar una aplicación .NET Core en Azure Kubernetes Service:

1. **Preparar la Aplicación y Crear la Imagen Docker**

   ```bash
   dotnet new webapi -o MyWebApi
   cd MyWebApi
   docker build -t mywebapi:v1 .
   ```

2. **Crear y Configurar ACR**

   ```bash
   az acr create --resource-group MyResourceGroup --name MyACR --sku Basic
   az acr login --name MyACR
   docker tag mywebapi:v1 MyACR.azurecr.io/mywebapi:v1
   docker push MyACR.azurecr.io/mywebapi:v1
   ```

3. **Crear y Configurar el Clúster de AKS**

   ```bash
   az aks create --resource-group MyResourceGroup --name MyAKSCluster --node-count 1 --enable-addons monitoring --generate-ssh-keys
   az aks get-credentials --resource-group MyResourceGroup --name MyAKSCluster
   ```

4. **Crear y Aplicar el Archivo de Despliegue**

   ```yaml
   # deployment.yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: mywebapi-deployment
   spec:
     replicas: 2
     selector:
       matchLabels:
         app: mywebapi
     template:
       metadata:
         labels:
           app: mywebapi
       spec:
         containers:
         - name: mywebapi
           image: MyACR.azurecr.io/mywebapi:v1
           ports:
           - containerPort: 80
   ---
   apiVersion: v1
   kind: Service
   metadata:
     name: mywebapi-service
   spec:
     type: LoadBalancer
     ports:
     - port: 80
     selector:
       app: mywebapi
   ```

   ```bash
   kubectl apply -f deployment.yaml
   ```

5. **Monitorear y Gestionar el Clúster**

   - Escalar el despliegue:

     ```bash
     kubectl scale --replicas=3 deployment/mywebapi-deployment
     ```

   - Actualizar la imagen del contenedor:

     ```bash
     docker build -t mywebapi:v2 .
     docker tag mywebapi

:v2 MyACR.azurecr.io/mywebapi:v2
     docker push MyACR.azurecr.io/mywebapi:v2
     kubectl set image deployment/mywebapi-deployment mywebapi=MyACR.azurecr.io/mywebapi:v2
     ```

   - Configurar y utilizar Azure Monitor para supervisar el clúster.

Este es un resumen detallado del uso de Azure Kubernetes Service con una aplicación .NET Core. La integración de estos servicios permite gestionar aplicaciones en contenedores de manera eficiente y escalable en la nube.
---
title: 'Tutorial: Aplicación de Ruby para Linux con Postgres'
description: Aprenda a poner en funcionamiento una aplicación de Ruby para Linux en Azure App Service, con conexión a una base de datos de PostgreSQL en Azure. Rails se utiliza en el tutorial.
ms.devlang: ruby
ms.topic: tutorial
ms.date: 03/27/2019
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: 2bc30786ccd0bccfba438fa6e553fdcbbf7fdde1
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085795"
---
# <a name="build-a-ruby-and-postgres-app-in-azure-app-service-on-linux"></a>Compilación de una aplicación Ruby y Postgres en Azure App Service en Linux

[App Service en Linux](app-service-linux-intro.md) proporciona un servicio de hospedaje web muy escalable y con aplicación automática de revisiones utilizando el sistema operativo Linux. En este tutorial se muestra cómo crear una aplicación Ruby y conectarla a una base de datos PostgreSQL. Cuando haya terminado, tendrá una aplicación [Ruby on Rails](https://rubyonrails.org/) que se ejecuta en App Service en Linux.

![Aplicación Ruby on Rails que se ejecuta en Azure App Service](./media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Creación de una base de datos PostgreSQL en Azure
> * Conectar una aplicación Ruby on Rails a PostgreSQL
> * Implementar la aplicación en Azure
> * Actualizar el modelo de datos y volver a implementar la aplicación
> * Transmitir registros de diagnóstico desde Azure
> * Administrar la aplicación en Azure Portal

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerrequisitos

Para completar este tutorial:

* [Instalación de Git](https://git-scm.com/)
* [Instalación de Ruby 2.3](https://www.ruby-lang.org/en/documentation/installation/)
* [Instalación de Ruby on Rails 5.1](https://guides.rubyonrails.org/v5.1/getting_started.html)
* [Instalación y ejecución de PostgreSQL](https://www.postgresql.org/download/)

## <a name="prepare-local-postgres"></a>Preparación de Postgres local

En este paso, creará una base de datos en el servidor Postgres local para usarlo en este tutorial.

### <a name="connect-to-local-postgres-server"></a>Conexión con el servidor Postgres local

Abra la ventana del terminal y ejecute `psql` para conectarse al servidor de Postgres local.

```bash
sudo -u postgres psql
```

Si la conexión se realiza correctamente, significa que la base de datos Postgres está en ejecución. De lo contrario, asegúrese de que se ha iniciado la base de datos Postgres local, para lo que debe seguir los pasos que se indican en [Downloads - PostgreSQL Core Distribution](https://www.postgresql.org/download/) (Descargas: Distribución del núcleo de PostgreSQL).

Escriba `\q` para salir del cliente de Postgres. 

Cree un usuario Postgres que pueda crear bases de datos ejecutando el siguiente comando, usando el nombre de usuario con el que haya iniciado la sesión de Linux.

```bash
sudo -u postgres createuser -d <signed-in-user>
```

<a name="step2"></a>

## <a name="create-a-ruby-on-rails-app-locally"></a>Creación de una aplicación Ruby on Rails local
En este paso, obtendrá una aplicación Ruby on Rails de ejemplo, configurará la conexión a base de datos correspondiente y la ejecutará de forma local. 

### <a name="clone-the-sample"></a>Clonación del ejemplo

En la ventana del terminal, use `cd` para cambiar a un directorio de trabajo.

Ejecute el comando siguiente para clonar el repositorio de ejemplo.

```bash
git clone https://github.com/Azure-Samples/rubyrails-tasks.git
```

`cd` en el directorio clonado. Instale los paquetes requeridos.

```bash
cd rubyrails-tasks
bundle install --path vendor/bundle
```

### <a name="run-the-sample-locally"></a>Ejecución local del código

Ejecute las [migraciones de Rails](https://guides.rubyonrails.org/active_record_migrations.html#running-migrations) para crear las tablas que necesita la aplicación. Para ver qué tablas se crean en las migraciones, mire en el directorio _db/migrate_ del repositorio de Git.

```bash
rake db:create
rake db:migrate
```

Ejecute la aplicación.

```bash
rails server
```

Vaya a `http://localhost:3000` en un explorador. Agregue algunas tareas a la página.

![Conexión correcta de Ruby on Rails a Postgres](./media/tutorial-ruby-postgres-app/postgres-connect-success.png)

Para detener el servidor Rails, escriba `Ctrl + C` en el terminal.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-postgres-in-azure"></a>Creación de Postgres en Azure

En este paso, creará una base de datos Postgres en [Azure Database for PostgreSQL](/azure/postgresql/). Posteriormente, configurará la aplicación Ruby on Rails para que se conecte a esta base de datos.

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-a-postgres-server"></a>Creación de un servidor de Postgres

Cree un servidor de PostgreSQL con el comando [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create).

Ejecute el siguiente comando en Cloud Shell y sustituya el marcador de posición *\<postgres-server-name>* por un nombre de servidor único. El nombre del servidor tiene que ser único entre todos los servidores de Azure. 

```azurecli-interactive
az postgres server create --location "West Europe" --resource-group myResourceGroup --name <postgres-server-name> --admin-user adminuser --admin-password My5up3r$tr0ngPa$w0rd! --sku-name GP_Gen4_2
```

Cuando se crea la base de datos de Azure para el servidor de PostgreSQL, la CLI de Azure muestra información similar a la del siguiente ejemplo:

<pre>
{
  "administratorLogin": "adminuser",
  "earliestRestoreDate": "2018-06-15T12:38:25.280000+00:00",
  "fullyQualifiedDomainName": "&lt;postgres-server-name&gt;.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/&lt;postgres-server-name&gt;",
  "location": "westeurope",
  "name": "&lt;postgres-server-name&gt;",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  &lt; Output has been truncated for readability &gt;
}
</pre>

### <a name="configure-server-firewall"></a>Configuración del firewall del servidor

En Cloud Shell, cree una regla de firewall para que el servidor Postgres permita conexiones de cliente con el comando [`az postgres server firewall-rule create`](/cli/azure/postgres/server/firewall-rule?view=azure-cli-latest#az-postgres-server-firewall-rule-create). Cuando tanto la dirección IP de inicio como final están establecidas en 0.0.0.0., el firewall solo se abre para otros recursos de Azure. Sustituya con un nombre de servidor único el marcador de posición *\<postgres-server-name>* .

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server <postgres-server-name> --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Puede ser incluso más restrictivo con su regla de firewall [usando solo las direcciones IP de salida que utiliza su aplicación](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

### <a name="connect-to-production-postgres-server-locally"></a>Conexión local al servidor Postgres de producción

En Cloud Shell, conéctese al servidor Postgres en Azure. Use el valor que especificó anteriormente para los marcadores de posición _&lt;postgres-server-name>_ .

```bash
psql -U adminuser@<postgres-server-name> -h <postgres-server-name>.postgres.database.azure.com postgres
```

Cuando se le solicite una contraseña, utilice _My5up3r$tr0ngPa$w0rd!_ , que es la que especificó al crear el servidor de bases de datos.

### <a name="create-a-production-database"></a>Creación de una base de datos de producción

En el símbolo del sistema de `postgres`, cree una base de datos.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Creación de un usuario con permisos

Cree un usuario de base de datos denominado _railsappuser_ y concédale todos los privilegios de la base de datos `sampledb`.

```sql
CREATE USER railsappuser WITH PASSWORD 'MyPostgresAzure2017';
GRANT ALL PRIVILEGES ON DATABASE sampledb TO railsappuser;
```

Escriba `\q` para salir de la conexión del servidor.

## <a name="connect-app-to-azure-postgres"></a>Conexión de una aplicación a Azure Postgres

En este paso, conectará la aplicación Ruby on Rails a la base de datos Postgres que creó en Azure Database for PostgreSQL.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Configuración de la conexión de base de datos

En el repositorio, abra _config/database.yml_. En la parte inferior del archivo, reemplace las variables de producción por el código siguiente. 

```txt
production:
  <<: *default
  host: <%= ENV['DB_HOST'] %>
  database: <%= ENV['DB_DATABASE'] %>
  username: <%= ENV['DB_USERNAME'] %>
  password: <%= ENV['DB_PASSWORD'] %>
```

Guarde los cambios.

### <a name="test-the-application-locally"></a>Prueba de la aplicación de forma local

En el terminal local, establezca las variables de entorno siguientes:

```bash
export DB_HOST=<postgres-server-name>.postgres.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=railsappuser@<postgres-server-name>
export DB_PASSWORD=MyPostgresAzure2017
```

Ejecute migraciones de bases de datos Rails con los valores de producción que acaba de configurar para crear las tablas de la base de datos Postgres en Azure Database for PostgreSQL.

```bash
rake db:migrate RAILS_ENV=production
```

Cuando se ejecuta en el entorno de producción, la aplicación Rails necesita recursos precompilados. Genere los recursos necesarios con el comando siguiente:

```bash
rake assets:precompile
```

El entorno de producción Rails también usa secretos para administrar la seguridad. Genere una clave secreta.

```bash
rails secret
```

Guarde la clave secreta en las variables respectivas que usa el entorno de producción Rails. Para mayor comodidad, se usa la misma clave para ambas variables.

```bash
export RAILS_MASTER_KEY=<output-of-rails-secret>
export SECRET_KEY_BASE=<output-of-rails-secret>
```

Habilite el entorno de producción Rails para atender archivos JavaScript y CSS.

```bash
export RAILS_SERVE_STATIC_FILES=true
```

Ejecute la aplicación de ejemplo en el entorno de producción.

```bash
rails server -e production
```

Vaya a `http://localhost:3000`. Si la página se carga sin errores, significa que la aplicación Ruby on Rails se está conectado a la base de datos Postgres en Azure.

Agregue algunas tareas a la página.

![Conexión correcta de Ruby on Rails a Azure Database for PostgreSQL](./media/tutorial-ruby-postgres-app/azure-postgres-connect-success.png)

Para detener el servidor Rails, escriba `Ctrl + C` en el terminal.

### <a name="commit-your-changes"></a>Confirmación de los cambios

Ejecute los siguientes comandos de Git para confirmar los cambios:

```bash
git add .
git commit -m "database.yml updates"
```

La aplicación está lista para implementarse.

## <a name="deploy-to-azure"></a>Implementar en Azure

En este paso se implementará la aplicación Rails conectada a Postgres en Azure App Service.

### <a name="configure-a-deployment-user"></a>Configuración de un usuario de implementación

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Creación de un plan de App Service

[!INCLUDE [Create app service plan no h](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Creación de una aplicación web

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

### <a name="configure-database-settings"></a>Configuración de la base de datos

En App Service, las variables de entorno se establecen como _valores de aplicación_ mediante el comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) en Cloud Shell.

El siguiente comando Cloud Shell permite configurar los valores de aplicación `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` y `DB_PASSWORD`. Reemplace los marcadores de posición _&lt;appname>_ y _&lt;postgres-server-name>_ .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<postgres-server-name>.postgres.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="railsappuser@<postgres-server-name>" DB_PASSWORD="MyPostgresAzure2017"
```

### <a name="configure-rails-environment-variables"></a>Configuración de las variables de entorno de Rails

En el terminal local, [genere un secreto nuevo](configure-language-ruby.md#set-secret_key_base-manually) para el entorno de producción de Rails en Azure.

```bash
rails secret
```

Configure las variables que requiere el entorno de producción de Rails.

En el comando Cloud Shell siguiente, reemplace los dos marcadores de posición _&lt;output-of-rails-secret>_ por la clave secreta nueva que generó en el terminal local.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output-of-rails-secret>" SECRET_KEY_BASE="<output-of-rails-secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

`ASSETS_PRECOMPILE="true"` indica al contenedor Ruby predeterminado que precompile los recursos en cada implementación de Git. Para obtener más información, consulte [Precompile assets](configure-language-ruby.md#precompile-assets) (Precompilación de los recursos) y [Serve static assets](configure-language-ruby.md#serve-static-assets) (Servir recursos estáticos).

### <a name="push-to-azure-from-git"></a>Inserción en Azure desde Git

En el terminal local, agregue una instancia remota de Azure al repositorio de Git local.

```bash
git remote add azure <paste-copied-url-here>
```

Insértela en la instancia remota de Azure para implementar la aplicación Ruby on Rails. Se le pedirá la contraseña que especificó anteriormente como parte de la creación del usuario de implementación.

```bash
git push azure master
```

Durante la implementación, Azure App Service comunicará su progreso con Git.

<pre>
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
&lt; Output has been truncated for readability &gt;
</pre>

### <a name="browse-to-the-azure-app"></a>Navegación hasta la aplicación de Azure

Vaya a `http://<app-name>.azurewebsites.net` y agregue algunas tareas a la lista.

![Aplicación Ruby on Rails que se ejecuta en Azure App Service](./media/tutorial-ruby-postgres-app/ruby-postgres-in-azure.png)

Ya está ejecutando una aplicación Ruby on Rails orientada a datos en Azure App Service.

## <a name="update-model-locally-and-redeploy"></a>Actualización local del modelo y nueva implementación

En este paso, se realiza un cambio sencillo en el modelo de datos `task` y en la aplicación web y, después, se publica la actualización en Azure.

Para el escenario de las tareas, modifique la aplicación de forma que pueda marcar una tarea como completada.

### <a name="add-a-column"></a>Adición de una columna

En el terminal, navegue a la raíz del repositorio de Git.

Genere una migración nueva que agregue una columna booleana llamada `Done` en la tabla `Tasks`:

```bash
rails generate migration AddDoneToTasks Done:boolean
```

Este comando genera un archivo de migración nuevo en el directorio _db/migrate_.


En el terminal, ejecute migraciones de base de datos de Rails para realizar el cambio en la base de datos local.

```bash
rake db:migrate
```

### <a name="update-application-logic"></a>Actualización de la lógica de aplicación

Abra el archivo *app/controllers/tasks_controller.rb*. Busque esta línea al final del archivo:

```rb
params.require(:task).permit(:Description)
```

Modifique esta línea para incluir el parámetro `Done` nuevo.

```rb
params.require(:task).permit(:Description, :Done)
```

### <a name="update-the-views"></a>Actualización de las vistas

Abra el archivo *app/views/tasks/_form.html.erb*, que es el formulario Edición.

Busque la línea `<%=f.error_span(:Description) %>` e inserte el código siguiente directamente debajo de ella:

```erb
<%= f.label :Done, :class => 'control-label col-lg-2' %>
<div class="col-lg-10">
  <%= f.check_box :Done, :class => 'form-control' %>
</div>
```

Abra el archivo *app/views/tasks/show.html.erb*, que es la página Vista de un solo registro. 

Busque la línea `<dd><%= @task.Description %></dd>` e inserte el código siguiente directamente debajo de ella:

```erb
  <dt><strong><%= model_class.human_attribute_name(:Done) %>:</strong></dt>
  <dd><%= check_box "task", "Done", {:checked => @task.Done, :disabled => true}%></dd>
```

Abra el archivo *app/views/tasks/index.html.erb*, que es la página de índice para todos los registros.

Busque la línea `<th><%= model_class.human_attribute_name(:Description) %></th>` e inserte el código siguiente directamente debajo de ella:

```erb
<th><%= model_class.human_attribute_name(:Done) %></th>
```

En el mismo archivo, busque la línea `<td><%= task.Description %></td>` e inserte el código siguiente directamente debajo de ella:

```erb
<td><%= check_box "task", "Done", {:checked => task.Done, :disabled => true} %></td>
```

### <a name="test-the-changes-locally"></a>Prueba local de los cambios

En el terminal local, ejecute el servidor Rails.

```bash
rails server
```

Para ver el cambio del estado de la tarea, navegue a `http://localhost:3000` y agregue o edite los elementos.

![Casilla agregada a tarea](./media/tutorial-ruby-postgres-app/complete-checkbox.png)

Para detener el servidor Rails, escriba `Ctrl + C` en el terminal.

### <a name="publish-changes-to-azure"></a>Publicación de los cambios en Azure

En el terminal, ejecute las migraciones de bases de datos Rails para el entorno de producción para hacer el cambio en la base de datos de Azure.

```bash
rake db:migrate RAILS_ENV=production
```

Confirme todos los cambios en Git y, después, inserte los cambios en el código en Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

Una vez que `git push` esté completo, vaya a la aplicación de Azure y pruebe la nueva funcionalidad.

![Modelo y cambios en la base de datos publicados en Azure](media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

Si ha agregado tareas, estas se conservarán en la base de datos. Las actualizaciones que efectúe en el esquema de datos dejan los datos existentes intactos.

## <a name="stream-diagnostic-logs"></a>Transmisión de registros de diagnóstico

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-the-azure-app"></a>Administración de la aplicación de Azure

Vaya a [Azure Portal](https://portal.azure.com) para administrar la aplicación que ha creado.

En el menú izquierdo, haga clic en **App Services** y, luego, en el nombre de la aplicación de Azure.

![Navegación en el portal a la aplicación de Azure](./media/tutorial-php-mysql-app/access-portal.png)

Verá la página de información general de la aplicación. Aquí se pueden realizar tareas de administración básicas como detener, iniciar, reiniciar, examinar y eliminar.

El menú izquierdo proporciona varias páginas para configurar la aplicación.

![Página de App Service en Azure Portal](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a:

> [!div class="checklist"]
> * Crear una base de datos de Postgres en Azure
> * Conectar una aplicación Ruby on Rails a Postgres
> * Implementar la aplicación en Azure
> * Actualizar el modelo de datos y volver a implementar la aplicación
> * Transmitir registros de diagnóstico desde Azure
> * Administrar la aplicación en Azure Portal

Vaya al siguiente tutorial para aprender a asignar un nombre DNS personalizado a una aplicación web.

> [!div class="nextstepaction"]
> [Tutorial: Asignar un nombre DNS personalizado a la aplicación](../app-service-web-tutorial-custom-domain.md)

O bien, eche un vistazo a otros recursos:

> [!div class="nextstepaction"]
> [Configure Ruby app](configure-language-ruby.md) (Configuración de una aplicación de Ruby)

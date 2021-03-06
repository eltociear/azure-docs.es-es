---
title: Publicación de Durable Functions en Azure Event Grid (versión preliminar)
description: Aprenda a configurar la publicación automática en Azure Event Grid de Durable Functions.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 52ffcd4eb81936ffcfa61580288c60bd59ffb744
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2020
ms.locfileid: "78249763"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Publicación de Durable Functions en Azure Event Grid (versión preliminar)

En este artículo se muestra cómo configurar Durable Functions para publicar eventos de ciclo de vida de orquestación (como los creados, los completados y los que contienen errores) en un [tema de Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) personalizado.

A continuación se muestran algunos escenarios donde esta característica resulta útil:

* **Escenarios de DevOps, como las implementaciones Azul/Verde**: por si quiere saber si hay tareas en ejecución antes de implementar la [estrategia de implementación en paralelo](durable-functions-versioning.md#side-by-side-deployments).

* **Compatibilidad con la supervisión y el diagnóstico avanzados**: puede realizar el seguimiento de la información de estado de la orquestación en un almacén externo optimizado para las consultas, como Azure SQL Database o Azure Cosmos DB.

* **Actividad de ejecución en segundo plano de larga duración**: si se utiliza Durable Functions para una actividad de ejecución en segundo plano de larga duración, esta característica le ayuda a conocer el estado actual.

## <a name="prerequisites"></a>Prerrequisitos

* Instale [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) en el proyecto de Durable Functions.
* Instale el [emulador de Azure Storage](../../storage/common/storage-use-emulator.md) (solo para Windows) o use una cuenta de Azure Storage existente.
* Instale la [CLI de Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) o use [Azure Cloud Shell](../../cloud-shell/overview.md).

## <a name="create-a-custom-event-grid-topic"></a>Creación de un tema de Event Grid personalizado

Cree un tema de Event Grid para enviar eventos desde Durable Functions. Las instrucciones siguientes muestran cómo crear un tema con la CLI de Azure. También puede hacerlo [mediante PowerShell](../../event-grid/custom-event-quickstart-powershell.md) o [Azure Portal](../../event-grid/custom-event-quickstart-portal.md).

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos con el comando `az group create`. Actualmente, Azure Event Grid no se admite en todas las regiones. Para información sobre las regiones donde se admite, consulte la [introducción a Azure Event Grid](../../event-grid/overview.md).

```azurecli
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Creación de un tema personalizado

Un tema de Event Grid proporciona un punto de conexión definido por el usuario en el que se publicar eventos. Reemplace `<topic_name>` por un nombre único para el tema. El nombre del tema debe ser único, ya que se convierte en entrada DNS.

```azurecli
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>Obtención del punto de conexión y la clave

Obtenga el punto de conexión del tema. Reemplace `<topic_name>` por el nombre que haya elegido.

```azurecli
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Obtenga la clave del tema. Reemplace `<topic_name>` por el nombre que haya elegido.

```azurecli
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Ahora puede enviar eventos al tema.

## <a name="configure-event-grid-publishing"></a>Configuración de la publicación en Event Grid

En su proyecto de Durable Functions, busque el archivo `host.json`.

Agregue `eventGridTopicEndpoint` y `eventGridKeySettingName` en una propiedad `durableTask`.

```json
{
    "durableTask": {
        "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "eventGridKeySettingName": "EventGridKey"
    }
}
```

Las posibles propiedades de configuración de Azure Event Grid se pueden encontrar en la [documentación de host.json](../functions-host-json.md#durabletask). Después de configurar el archivo `host.json`, la aplicación de función envía eventos del ciclo de vida al tema de Event Grid. Esto funciona cuando se ejecuta la aplicación de función tanto localmente como en Azure.

Establezca la configuración de la aplicación para la clave del tema en la aplicación de función y en `local.settings.json`. El siguiente JSON es un ejemplo de `local.settings.json` para la depuración local. Reemplace `<topic_key>` por la clave del tema.  

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "UseDevelopmentStorage=true",
        "AzureWebJobsDashboard": "UseDevelopmentStorage=true",
        "EventGridKey": "<topic_key>"
    }
}
```

Si usa el [emulador Storage](../../storage/common/storage-use-emulator.md) (solo para Windows), asegúrese de que funciona. Es conveniente ejecutar el comando `AzureStorageEmulator.exe clear all` antes de la ejecución.

Si utiliza una cuenta de Azure Storage existente, reemplace `UseDevelopmentStorage=true` en `local.settings.json` por su cadena de conexión.

## <a name="create-functions-that-listen-for-events"></a>Creación de funciones que realicen escuchas para los eventos

Con Azure Portal, cree otra aplicación de función para escuchar eventos publicados por la aplicación de Durable Functions. Se recomienda ubicarla en la misma región que el tema de Event Grid.

### <a name="create-an-event-grid-trigger-function"></a>Creación de una función de desencadenador de Event Grid

Cree una función que reciba los eventos de ciclo de vida. Seleccione **Función personalizada**.

![Selección de creación de una función personalizada](./media/durable-functions-event-publishing/functions-portal.png)

Elija el desencadenador de Event Grid y seleccione el lenguaje.

![Selección de Desencadenador de Event Grid](./media/durable-functions-event-publishing/eventgrid-trigger.png)

Escriba el nombre de la función y seleccione `Create`.

![Creación de Desencadenador de Event Grid](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

Se creará una función con el código siguiente:

# <a name="c-script"></a>[Script de C#](#tab/csharp-script)

```csharp
#r "Newtonsoft.Json"
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

public static void Run(JObject eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = async function(context, eventGridEvent) {
    context.log(typeof eventGridEvent);
    context.log(eventGridEvent);
}
```

---

Seleccione `Add Event Grid Subscription`. Esta operación agrega una suscripción a Event Grid para el tema que ha creado. Para más información, consulte [Conceptos de Azure Event Grid](https://docs.microsoft.com/azure/event-grid/concepts).

![Selección del vínculo Desencadenador de Event Grid](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Seleccione `Event Grid Topics` como **Tipo de tema**. Seleccione el grupo de recursos que ha creado para el tema de Event Grid. A continuación, seleccione la instancia del tema de Event Grid. Presione `Create`.

![Cree una suscripción de Event Grid.](./media/durable-functions-event-publishing/eventsubscription.png)

Ahora está preparado para recibir eventos de ciclo de vida.

## <a name="run-durable-functions-app-to-send-the-events"></a>Ejecución de una aplicación de Durable Functions para enviar eventos

En el proyecto de Durable Functions que configuró anteriormente, inicie la depuración en la máquina local e inicie una orquestación. La aplicación los eventos de publica de ciclo de vida de Durable Functions en Event Grid. Compruebe que Event Grid desencadena la función de cliente de escucha que ha creado comprobando sus registros en Azure Portal.

```
2019-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2019-04-20T09:28:21.104 [Info] {
    "id": "054fe385-c017-4ce3-b38a-052ac970c39d",
    "subject": "durable/orchestrator/Running",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Running"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2019-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2019-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2019-04-20T09:28:37.098 [Info] {
    "id": "8cf17246-fa9c-4dad-b32a-5a868104f17b",
    "subject": "durable/orchestrator/Completed",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Completed"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2019-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>Esquema de eventos

En la lista siguiente se explica el esquema de los eventos de ciclo de vida:

* **`id`** : identificador único del evento de Event Grid.
* **`subject`** : ruta de acceso al asunto del evento. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` será `Running`, `Completed`, `Failed` y `Terminated`.  
* **`data`** : parámetros específicos de Durable Functions.
  * **`hubName`** : nombre de [TaskHub](durable-functions-task-hubs.md).
  * **`functionName`** : nombre de una función de orquestador.
  * **`instanceId`** : instanceId de Durable Functions.
  * **`reason`** : información adicional asociada al evento de seguimiento. Para más información, consulte [Diagnóstico con Durable Functions (Azure Functions)](durable-functions-diagnostics.md)
  * **`runtimeStatus`** : estado del entorno en tiempo de ejecución de la orquestación. Running, Completed, Failed, Canceled.
* **`eventType`** : "orchestratorEvent"
* **`eventTime`** : hora del evento (UTC).
* **`dataVersion`** : versión del esquema de los eventos de ciclo de vida.
* **`metadataVersion`** :  versión de los metadatos.
* **`topic`** : recurso del tema de Event Grid.

## <a name="how-to-test-locally"></a>Prueba local

Para las pruebas locales, consulte [Depuración local de funciones de Azure desencadenadas por Event Grid](../functions-debug-event-grid-trigger-local.md).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Aprenda a administrar instancias en Durable Functions](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Aprenda sobre el control de versiones en Durable Functions](durable-functions-versioning.md)

---
title: 'Recursos para desarrolladores: Language Understanding'
description: Los SDK, las API REST o la CLI le ayudan a desarrollar aplicaciones de Language Understanding (LUIS) en su lenguaje de programación. Administre los recursos de Azure y predicciones de LUIS.
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 5e375157cef4789bc2980f6154ea8d59e765ff3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2020
ms.locfileid: "79457991"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>Recursos de desarrolladores de SDK, REST y CLI para Language Understanding (LUIS)

Los SDK, las API REST o la CLI le ayudan a desarrollar aplicaciones de Language Understanding (LUIS) en su lenguaje de programación. Administre los recursos de Azure y predicciones de LUIS.

## <a name="azure-resource-management"></a>Administración de recursos de Azure

Use el nivel de Azure Microsoft Cognitive Services para crear, editar, enumerar y eliminar el recurso Language Understanding o Cognitive Service.

Encuentre la documentación de referencia basada en la herramienta:

* [CLI de Azure](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>Solicitudes de creación y predicción de Language Understanding

Al servicio Language Understanding se accede desde un recurso de Azure que tiene que crear. Hay dos recursos:

* Use el recurso de **creación** para entrenar para la creación, edición, entrenamiento y publicación.
* Use el recurso de **predicción** para que entorno de ejecución envíe el texto del usuario y reciba una predicción.

Más información acerca del [punto de conexión de predicción de V3](luis-migration-api-v3.md).

Use el [código de ejemplo de Cognitive Services](https://github.com/Azure-Samples/cognitive-services-quickstart-code) para aprender y usar las tareas más comunes.

### <a name="rest-specifications"></a>Especificaciones de REST

Las [especificaciones de REST de LUIS](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/cognitiveservices/data-plane/LUIS), junto con todas las [especificaciones de REST de Azure](https://github.com/Azure/azure-rest-api-specs), están disponibles públicamente en GitHub.

### <a name="rest-apis"></a>API de REST

Las API de punto de conexión de predicción y de creación están disponibles en las API REST:

|Tipo|Versión|
|--|--|
|Creación|[V2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[versión preliminar V3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|Predicción|[V2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>Puntos de conexión REST

LUIS actualmente tiene dos tipos de puntos de conexión:

* creación en el punto de conexión de entrenamiento
* predicción de consultas en el punto de conexión en tiempo de ejecución

|Propósito|URL|
|--|--|
|Creación en el punto de conexión de entrenamiento|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|Versión v2 del entorno de ejecución: todas las predicciones en el punto de conexión en tiempo de ejecución|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|Versión v3 del entorno de ejecución: la predicción de las versiones en el punto de conexión en tiempo de ejecución|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|Versión v3 del entorno de ejecución: predicciones de espacios en el punto de conexión en tiempo de ejecución|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

En la tabla siguiente se explican los parámetros que se muestran entre llaves `{}` en la tabla anterior.

|Parámetro|Propósito|
|--|--|
|`your-resource-name`|Nombre de recurso de Azure|
|`q` o `query`|Texto de expresión enviado desde la aplicación cliente, como un bot de chat|
|`version`|Nombre de la versión de 10 caracteres|
|`slot`| `production` o `staging`|

### <a name="language-based-sdks"></a>SDK basados en lenguaje

|Idioma |Documentación de referencia|Paquete|Ejemplos|Guías de inicio rápido|
|--|--|--|--|--|
|C#|[Creación](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Predicción](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[Creación en NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[Predicción en NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Ejemplos de SDK de .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[Creación y administración de una aplicación](sdk-authoring.md?pivots=programming-language-csharp)<br>[Consulta de un punto de conexión de predicción](sdk-query-prediction-endpoint.md)|
|Go|[Creación y predicción](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[Creación](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[Predicción](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[Creación y predicción con REST](luis-get-started-get-intent-from-rest.md)|
|Java|[Creación y predicción](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Autorización con Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Predicción con Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[Creación](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[Predicción](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[Creación y predicción](luis-get-started-get-intent-from-rest.md)
|Node.js|[Creación](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Predicción](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[Creación con NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[Predicción con NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Creación](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[Predicción](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[Creación y predicción con REST](luis-get-started-get-intent-from-rest.md)|
|Python|[Creación y predicción](sdk-authoring.md?pivots=programming-language-python)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Creación](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[Creación](sdk-authoring.md?pivots=programming-language-python)<br>[Predicción con REST](luis-get-started-get-intent-from-rest.md)


### <a name="containers"></a>Contenedores

Language Understanding (LUIS) proporciona un [contenedor](luis-container-howto.md) que ofrece versiones locales y contenidas de la aplicación.

### <a name="export-and-import-formats"></a>Exportación e importación de formatos

Language Understanding ofrece la posibilidad de administrar la aplicación y sus modelos en un formato JSON, el formato `.LU` ([LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)) y un paquete comprimido para el contenedor de Language Understanding.

La exportación e importación de estos formatos está disponible desde las API y desde el portal de LUIS. El portal proporciona importación y exportación como parte de la lista de aplicaciones y la lista de versiones.

## <a name="other-tools-and-sdks"></a>Otras herramientas y SDK

Bot Framework está disponible como [un SDK](https://github.com/Microsoft/botframework) en diversos lenguajes y como servicio mediante [Azure Bot Service](https://dev.botframework.com/).

Bot Framework proporciona [varias herramientas](https://github.com/microsoft/botbuilder-tools) para ayudar con Language Understanding, entre las que se incluyen:

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown): compile modelos de descripción del lenguaje LUIS con archivos Markdown
* [CLI de LUIS](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS): cree y administre las aplicaciones LUIS.ai.
* [Dispatch](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch): administre aplicaciones principales y secundarias
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen): genere automáticamente clases C#/Typescript de respaldo para sus intenciones y entidades de LUIS.
* [Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases): una aplicación de escritorio que permite que los desarrolladores de bots prueben y depuren los bots mediante el SDK de Bot Framework
* [Bot Framework Composer](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md): es una herramienta de desarrollo integrado para desarrolladores y equipos multidisciplinares que permite crear bots y experiencias de conversación con Microsoft Bot Framework.
* [microsoft/NLU.DevOps](https://github.com/microsoft/NLU.DevOps): herramientas que admiten la integración y la implementación continuas para servicios de NLU.

## <a name="next-steps"></a>Pasos siguientes

* Obtenga información sobre los [códigos de error HTTP](luis-reference-response-codes.md) comunes.
* [Documentación de referencia](https://docs.microsoft.com/azure/index) para todas las API y los SDK.
* [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) y [Azure Bot Service](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Contenedores cognitivos](../cognitive-services-container-support.md)

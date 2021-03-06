---
title: Obtención del identificador URI de firma de acceso compartido para la imagen de máquina virtual | Azure Marketplace
description: En este artículo se explica cómo obtener el identificador URI de firma de acceso compartido (SAS) para cada disco duro virtual (VHD).
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mingshen
ms.openlocfilehash: c226d35647e4a5a2b1d583dd6328bfb73dae2a1c
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732634"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Obtención del URI de firma de acceso compartido para la imagen de máquina virtual

> [!IMPORTANT]
> Estamos trasladando la administración de las ofertas de máquina virtual de Azure de Cloud Partner Portal al Centro de partners. Hasta que se migren las ofertas, siga las instrucciones de [Obtención del identificador URI de firma de acceso compartido para la imagen de máquina virtual](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-get-sas-uri) para Cloud Partner Portal para administrar las ofertas.

En este artículo se describe cómo generar un identificador uniforme de recursos (URI) de firma de acceso compartido (SAS) para cada disco duro virtual (VHD).

Durante el proceso de publicación, debe proporcionar un identificador URI para cada VHD asociado a sus planes. Estos planes se llamaban previamente SKU o referencias de almacén. Microsoft necesita tener acceso a estos VHD durante el proceso de certificación. Escriba este identificador URI en la pestaña **Planes** del Centro de partners.

Al generar los identificadores URI de SAS para los discos duros virtuales, debe cumplir los requisitos siguientes:

* Solo se admiten los discos duros virtuales no administrados.
* Solo se requieren permisos `List` y `Read`. No proporcione acceso de escritura o eliminación.
* La duración del acceso (fecha de expiración) debe ser un mínimo de tres semanas a partir de la creación del identificador URI de SAS.
* Como protección frente a los cambios de hora UTC, establezca la fecha de inicio en un día antes de la fecha actual. Por ejemplo, si la fecha actual es el 6 de octubre de 2019, seleccione 5/10/2019.

## <a name="generate-the-sas-address"></a>Generación de la dirección de SAS

Hay dos herramientas comunes que se usan para crear una dirección de SAS (URL):

* **Explorador de Microsoft Azure Storage**: herramienta gráfica disponible para Windows, macOS y Linux.
* **CLI de Microsoft Azure**: recomendada para los sistemas operativos que no sean Windows y los entornos automatizados o de integración continua.

### <a name="use-microsoft-storage-explorer"></a>Uso del Explorador de Microsoft Azure Storage

1. Descargue e instale el [Explorador de Microsoft Azure Storage](https://azure.microsoft.com/features/storage-explorer/).
2. Abra el explorador y, en el menú de la izquierda, seleccione **Agregar cuenta**. Aparece el cuadro de diálogo **Conectar con Azure Storage**.
3. Seleccione **Agregar una cuenta de Azure** y, a continuación, **Iniciar sesión**. Complete los pasos necesarios para iniciar sesión en la cuenta de Azure.
4. En el panel de la izquierda del **Explorador**, vaya a **Cuentas de almacenamiento** y expanda este nodo.
5. Haga clic con el botón derecho en el disco duro virtual y seleccione **Obtener firma de acceso compartido**.
6. Aparecerá el cuadro de diálogo **Firma de acceso compartido**. Rellene los siguientes campos:

    * **Fecha de inicio**: fecha de inicio del permiso para acceder al disco duro virtual. Proporcione una fecha que sea un día antes de la fecha actual.
    * **Fecha de expiración**: fecha de expiración del permiso para acceder al disco duro virtual. Proporcione una fecha al menos tres semanas después de la fecha actual.
    * **Permisos**: seleccione los permisos Lista y Lectura.
    * **Nivel de contenedor**: active la casilla **Generar identificador URI de firma de acceso compartido de nivel de contenedor**.

        :::image type="content" source="media/create-sas-uri-storage-explorer.png" alt-text="Muestra el cuadro de diálogo Firma de acceso compartido":::

7. Seleccione **Crear** para crear el identificador URI de SAS asociado a este disco duro virtual. El cuadro de diálogo se actualiza y muestra los detalles de esta operación.
8. Copie el **identificador URI** y guárdelo en un archivo de texto en una ubicación segura.

    :::image type="content" source="media/create-sas-uri-shared-access-signature-details.png" alt-text="Muestra el cuadro de detalles de firma de acceso compartido":::

    Este identificador URI de SAS generado es para el acceso de nivel de contenedor. Para que sea específico, edite el archivo de texto para agregar el nombre del disco duro virtual (paso siguiente).

9. Inserte el nombre del disco duro virtual después de la cadena vhds del identificador URI de SAS (incluya una barra diagonal inicial). El identificador URI de SAS final debe ser similar a este:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>` Por ejemplo, si es el nombre del disco duro virtual es `TestRGVM2.vhd`, el identificador URI de SAS resultante sería:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

10. Repita estos pasos con todos los discos duros virtuales de los planes que va a publicar.

### <a name="using-azure-cli"></a>Uso de la CLI de Azure

1. Descargue e instale la [CLI de Microsoft Azure](https://azure.microsoft.com/documentation/articles/xplat-cli-install/). Hay versiones disponibles para Windows, macOS y varias distribuciones de Linux.
2. Cree un archivo de PowerShell (con la extensión de archivo .ps1), copie el código siguiente y luego guárdelo localmente.

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```

3. Edite el archivo para proporcionar los valores de parámetro siguientes. Proporcione las fechas en formato de fecha y hora UTC, como `2020-04-01T00:00:00Z`.

    * `<account-name>`: nombre de la cuenta de Azure Storage
    * `<account-key>`: clave de la cuenta de Azure Storage
    * `<vhd-name>`: nombre del disco duro virtual
    * `<start-date>`: fecha de inicio del permiso para acceder al disco duro virtual. Proporcione una fecha un día antes de la fecha actual.
    * `<expiry-date>`: fecha de expiración del permiso para acceder al disco duro virtual. Proporcione una fecha al menos tres semanas después de la fecha actual.

    En este ejemplo se muestran los valores de parámetro adecuados (en el momento de redactar este artículo):

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2020-04-01T00:00:00Z' --expiry '2021-04-01T00:00:00Z'
    ```

4. Guarde los cambios.
5. Use uno de los siguientes métodos para ejecutar este script con privilegios administrativos para crear una **cadena de conexión de SAS** para el acceso de nivel de contenedor:

    * Ejecutar el script desde la consola. En Windows, haga clic con el botón derecho en el script y seleccione **Ejecutar como administrador**.
    * Ejecutar el script desde un editor de scripts de PowerShell, como [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). En esta pantalla se muestra la creación de una cadena de conexión de SAS en este editor:

     :::image type="content" source="media/create-sas-uri-power-shell-ise.png" alt-text="Muestra la creación de una cadena de conexión de SAS con Windows PowerShell ISE":::

6. Copie la cadena de conexión de SAS y guárdela en un archivo de texto en una ubicación segura. Edite esta cadena para agregar la información de ubicación del disco duro virtual para crear el identificador URI de SAS final.
7. En Azure Portal, vaya al almacenamiento de blobs que contiene el disco duro virtual asociado con el nuevo identificador URI.
8. Copie la dirección URL del **punto de conexión de Blob service**, como se muestra en la siguiente captura de pantalla.

    :::image type="content" source="media/create-sas-uri-blob-endpoint.png" alt-text="Muestra el punto de conexión de Blob service":::

9. Modifique el archivo de texto con la cadena de conexión de SAS del paso 6. Cree el identificador URI de SAS completo con este formato:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

    Por ejemplo, si el nombre del disco duro virtual es `TestRGVM2.vhd`, el identificador URI de SAS será:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Repita estos pasos con todos los discos duros virtuales de las SKU que quiera publicar.

## <a name="verify-the-sas-uri"></a>Comprobación del URI de SAS

Revise los identificadores URI de SAS creados según la lista de comprobación siguiente para verificar que:

* El identificador URI tiene el siguiente aspecto: `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
* El identificador URI incluye el nombre de archivo de la imagen de disco duro virtual, incluida la extensión ".vhd".
* Aparece `sp=rl` en la parte central del identificador URI. Esta cadena muestra que se ha especificado el acceso `Read` y `List`.
* Cuando aparece `sr=c`, significa que se especifica el acceso de nivel de contenedor.
* Copie y pegue el identificador URI en un explorador para probar y descargar el blob (puede cancelar la operación antes de que se complete la descarga).

## <a name="next-step"></a>Paso siguiente

Si tiene dificultades para crear un identificador URI de SAS, consulte [Problemas comunes de la dirección URL de SAS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-sas-uri-issues). En caso contrario, guarde el URI de SAS en una ubicación segura para su uso posterior. Lo necesitará para publicar la oferta de máquina virtual en el Centro de partners.

* [Creación de una oferta de máquina virtual de Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer)

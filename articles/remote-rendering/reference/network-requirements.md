---
title: Requisitos de red
description: Requisitos de red y procedimientos recomendados para una experiencia óptima en la red
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: reference
ms.openlocfilehash: 73bbfad4c0535fa00b1aa53764eb52acb83124f8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2020
ms.locfileid: "80678950"
---
# <a name="network-requirements"></a>Requisitos de red

Una conexión de red estable y de baja latencia a un centro de datos de Azure es fundamental para una buena experiencia del usuario en Azure Remote Rendering. Unas condiciones de red insuficientes pueden dar lugar a que se interrumpan las conexiones, inestabilidad, vibraciones u hologramas de "salto", y a un retraso perceptible al actualizar el grafo de escenas del lado servidor.

## <a name="guidelines-for-network-connectivity"></a>Directrices para la conectividad de red

Los requisitos exactos de la red dependen de su caso de uso específico, por ejemplo del número y la frecuencia de las modificaciones realizadas en el grafo de escenas remotas, así como de la complejidad de la vista representada. Pero hay una serie de instrucciones que permiten garantizar que su experiencia sea la mejor posible:

* La conectividad a Internet debe admitir al menos **50 Mbps de bajada** y **10 Mbps de subida** para una sesión de un solo usuario de Azure Remote Rendering, suponiendo que no haya tráfico en competencia en la red. La experiencia mejora con tasas mayores. Con más usuarios en la misma red, estos requisitos se escalan verticalmente según corresponda.
* El uso de la **banda Wi-Fi de 5 GHz** normalmente producirá mejores resultados que la banda Wi-Fi de 2,4 GHz, aunque ambas deberían funcionar.
* Si hay otras redes Wi-Fi cercanas, evite usar canales Wi-Fi que estas otras redes utilicen. Puede usar herramientas de análisis de red como [WifiInfoView](https://www.nirsoft.net/utils/wifi_information_view.html) para comprobar si los canales que usa la red Wi-Fi son gratuitos para el tráfico en competencia.
* **Evite siempre utilizar repetidores de Wi-Fi** o el reenvío en LAN a través de Powerline.
* **Evite la competencia de tráfico con mucho uso de ancho de banda**, como el streaming de vídeo o juegos, en la misma red Wi-Fi.
* Disponer de una **buena intensidad de señal Wi-Fi** es fundamental. Si es posible, manténgase cerca de su punto de acceso Wi-Fi y evite obstáculos entre el dispositivo cliente y los puntos de acceso.
* Asegúrese de conectarse siempre al **centro de datos de Azure más cercano** para su [región](regions.md). Cuanto más se acerque al centro de datos, menor será la latencia de la red, lo que afecta enormemente a la estabilidad de los hologramas.

## <a name="network-performance-tests"></a>Pruebas sobre el rendimiento de red

Si desea obtener información inicial sobre si la calidad de la conectividad de red es suficiente para ejecutar Azure Remote Rendering, dispone de herramientas en línea. Es muy aconsejable ejecutarlas desde un equipo portátil razonablemente eficaz conectado a la misma red Wi-Fi que el dispositivo en el que está planeando ejecutar la aplicación cliente de Azure Remote Rendering. Los resultados obtenidos al ejecutar las pruebas en un teléfono móvil u HoloLens2 suelen ser menos útiles, ya que han demostrado una variación significativa en los dispositivos de punto de conexión de bajo consumo. La ubicación en la que se coloca el equipo portátil debe estar aproximadamente en el mismo lugar en el que se espera usar el dispositivo que ejecuta la aplicación cliente de Azure Remote Rendering.

Estos son algunos pasos sencillos para realizar una prueba rápida de la conectividad de red:

1. **Ejecute una herramienta de prueba de red como www.speedtest.net para obtener datos sobre la latencia general y el ancho de banda de subida y bajada de la conexión de red.**
Elija un servidor más cercano a usted y ejecute la prueba. Aunque el servidor no será el centro de datos de Azure al que Azure Remote Rendering se conectará, los datos resultantes seguirán siendo útiles para conocer el rendimiento de la conexión a Internet y Wi-Fi.
   * **Requisito mínimo** para Azure Remote Rendering: aprox. 40 Mbps de bajada y 5 Mbps de flujo de subida.
   * **Se recomienda** para Azure Remote Rendering: aproximadamente 100 Mbps de bajada y 10 Mbps de subida.
Se recomienda ejecutar la prueba varias veces y tomar los peores resultados.
1. **Use una herramienta como www.azurespeed.com que mida la latencia a los centros de datos de Azure**. Seleccione el centro de datos de Azure más cercano que Azure Remote Rendering admita (consulte las [regiones admitidas](regions.md)) y ejecute una **prueba de latencia**. Si hay variaciones en las cifras, espere a que los resultados se estabilicen.
   * **Requisito mínimo** para Azure Remote Rendering: la latencia debe ser inferior a 100 ms.
   * **Se recomienda** para Azure Remote Rendering: la latencia debe ser inferior a 70 ms de forma continuada.

Aunque una latencia baja no es garantía de que Azure Remote Rendering funcione bien en la red, normalmente hemos notado que se comporta bien en las situaciones en las que estas pruebas se han superado de modo correcto.
Si está detectando artefactos como hologramas con saltos, vibraciones o inestabilidad al ejecutar Azure Remote Rendering, consulte la [guía de solución de problemas](../resources/troubleshoot.md).

## <a name="next-steps"></a>Pasos siguientes

* [Inicio rápido: Representación de un modelo con Unity](../quickstarts/render-model.md)

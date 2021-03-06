---
title: 'Inicio rápido: Traducción de voz a voz: servicio de voz'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: c5f0a0fe032d18cd4f01aebe9a5c736d6d511a74
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980547"
---
En este inicio rápido, va a usar el SDK de [Voz](~/articles/cognitive-services/speech-service/speech-sdk.md) para traducir interactivamente la voz de un idioma a voz en otro idioma. Una vez que se cumplen los requisitos previos, para la traducción de voz a voz solo son necesarios seis pasos:
> [!div class="checklist"]
> * Cree un objeto ````SpeechTranslationConfig```` a partir de la clave y la región de suscripción.
> * Actualice el objeto ````SpeechTranslationConfig```` para especificar los idiomas de origen y destino.
> * Actualice el objeto ````SpeechTranslationConfig```` para especificar el nombre de la voz de la salida de voz.
> * Cree un objeto ````TranslationRecognizer```` con el objeto ````SpeechTranslationConfig```` anterior.
> * Con el objeto ````TranslationRecognizer````, inicie el proceso de reconocimiento de una única expresión.
> * Inspeccione el objeto ````TranslationRecognitionResult```` devuelto.

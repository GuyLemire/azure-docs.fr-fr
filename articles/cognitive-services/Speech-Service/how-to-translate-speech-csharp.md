---
title: Traduire une entrée vocale à l’aide du kit de développement logiciel (SDK) Speech pour C#
titleSuffix: Azure Cognitive Services
description: Cet article comprend des exemples de code permettant de traduire une entrée vocale à l’aide du kit SDK Speech dans un environnement C#.
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: 4846145f6a87ebf9255494fe18eaa11b1317653a
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53092262"
---
# <a name="translate-speech-with-the-cognitive-services-speech-sdk-for-c"></a>Traduire une entrée vocale avec le kit de développement logiciel Cognitive Services Speech pour C#

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-how-to-translate-speech-selector.md)]

[!INCLUDE [Introduction](../../../includes/cognitive-services-speech-service-how-to-translate-speech-intro.md)]

[!INCLUDE [Introduction to top-level declarations](../../../includes/cognitive-services-speech-service-how-to-toplevel-declarations.md)]

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/translation_samples.cs#toplevel)]

[!INCLUDE [Introduction to using a microphone](../../../includes/cognitive-services-speech-service-how-to-translate-speech-microphone.md)]

[!code-csharp[Translation from a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/translation_samples.cs#TranslationWithMicrophoneAsync)]

[!INCLUDE [Introduction to using a file](../../../includes/cognitive-services-speech-service-how-to-translate-speech-file.md)]

[!code-csharp[Translation from file input](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/translation_samples.cs#TranslationWithFileAsync)]

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Recherchez le code utilisé dans cet article au sein du dossier samples/csharp/sharedcontent/console.

## <a name="next-steps"></a>Étapes suivantes

- [Comment effectuer une reconnaissance vocale](how-to-recognize-speech-csharp.md)
- [Comment reconnaître les intentions vocales](how-to-recognize-intents-from-speech-csharp.md)

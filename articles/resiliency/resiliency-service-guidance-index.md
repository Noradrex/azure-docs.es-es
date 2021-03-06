---
title: "Información orientativa acerca de la resistencia del servicio | Microsoft Azure"
description: "Vínculos a una guía de recuperación ante desastres y resistencia y disponibilidad proactivas para los servicios de Microsoft Azure."
services: 
documentationcenter: na
author: adamglick
manager: saladki
editor: 
ms.assetid: 919a197d-ba47-4e49-a64c-118e27d5a7df
ms.service: resiliency
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: aglick
translationtype: Human Translation
ms.sourcegitcommit: aafb03c86884d1af3e2cae1134c9e6d7ac42397b
ms.openlocfilehash: 4186211af944c0c0109799a94e4163cfceeee4c4


---
# <a name="microsoft-azure-service-resiliency-guidance"></a>Información orientativa acerca de la resistencia del servicio de Microsoft Azure
Se ha diseñado Microsoft Azure para proporcionarle los recursos que necesita, cuando los necesita. Como parte de unas buenas recomendaciones operativas y de diseño, debe conocer cómo diseñar el uso de los servicios de Azure para lograr una alta disponibilidad y qué hacer si su aplicación se ve afectada por una interrupción del servicio. Para ayudarle en este proceso, este documento contiene vínculos a una guía de recuperación ante desastres, así como a una guía de diseño para los distintos servicios de Azure.

## <a name="disaster-recovery-guidance"></a>Guía de recuperación ante desastres
Los vínculos a la guía de recuperación ante desastres que aparecen a continuación le proporcionan la información que necesita para ayudarle a que la aplicación vuelva a estar en línea rápidamente si se ve afectada por una interrupción del servicio de Azure. Estos vínculos se crearon para ayudarle a dar respuesta a esta pregunta: "Se ha producido una interrupción del servicio de Azure, ¿Qué puedo hacer?"

## <a name="design-guidance"></a>Guía de diseño
Los siguientes vínculos conducen a una guía de diseño que se ha creado para ayudarle a comprender mejor cómo utilizar cada servicio de Azure de forma que se maximice el tiempo de actividad de la aplicación. Estos vínculos se crearon para ayudarle a dar respuesta a la pregunta: "¿Cómo me aseguro de que un error, error de hardware, interrupción del servicio o cualquier otro tipo de error no afectará a la disponibilidad general de mi aplicación?" Si no hay ninguna orientación específica para el servicio que está buscando, puede que la información del artículo [Alta disponibilidad para aplicaciones creadas en Microsoft Azure](resiliency-high-availability-azure-applications.md) le ayude con su diseño.

## <a name="service-guidance"></a>Guía de servicio
| Servicio | Guía de recuperación ante desastres | Guía de diseño |
|:--- |:---:|:---:|
| [Servicios en la nube](https://azure.microsoft.com/services/cloud-services/ "Azure Servicios en la nube") |[link](../cloud-services/cloud-services-disaster-recovery-guidance.md "Guía de recuperación ante desastres para los Servicios en la nube de Azure") |No disponible |
| [Almacén de claves](https://azure.microsoft.com/services/key-vault/ "Azure Almacén de claves") |[link](../key-vault/key-vault-disaster-recovery-guidance.md "Guía de recuperación ante desastres para el servicio de Almacén de claves de Azure") |No disponible |
| [Almacenamiento](https://azure.microsoft.com/services/storage/ "Azure Almacenamiento") |[link](../storage/storage-disaster-recovery-guidance.md "Guía de recuperación ante desastres para el servicio de Almacenamiento de Azure") |No disponible |
| [Bases de datos SQL](https://azure.microsoft.com/services/sql-database/ "Azure Bases de datos SQL") |[link](../sql-database/sql-database-disaster-recovery.md "Guía de recuperación ante desastres para el servicio de Base de datos SQL de Azure") |[link](../sql-database/sql-database-performance-guidance.md "Guía de diseño de Bases de datos SQL de Azure") |
| [Máquinas virtuales](https://azure.microsoft.com/services/virtual-machines/ "Azure Máquinas virtuales") |[link](../virtual-machines/virtual-machines-disaster-recovery-guidance.md "Guía de recuperación ante desastres para el servicio de Máquinas virtuales de Azure") |No disponible |
| [Red virtual](https://azure.microsoft.com/services/virtual-network/ "Azure Red virtual") |[link](../virtual-network/virtual-network-disaster-recovery-guidance.md "Guía de recuperación ante desastres para Red virtual de Azure") |No disponible |

## <a name="next-steps"></a>Pasos siguientes
Si busca una guía que se centre de forma más general en sistemas y soluciones, lea el artículo [Recuperación ante desastres y alta disponibilidad para aplicaciones creadas en Microsoft Azure](https://aka.ms/drtechguide).



<!--HONumber=Nov16_HO3-->



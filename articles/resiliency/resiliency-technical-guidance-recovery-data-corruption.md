---
title: "Guía técnica sobre resistencia para la recuperación ante datos dañados o eliminación accidental | Microsoft Docs"
description: "Artículo para entender cómo recuperarse ante datos dañados o eliminación accidental de datos y para diseñar aplicaciones resistentes, con alta disponibilidad y con tolerancia a errores, así como para planear la recuperación ante desastres"
services: 
documentationcenter: na
author: adamglick
manager: saladki
editor: 
ms.assetid: ecdfa1a8-d93f-43f2-b4a3-f5cfd13201e7
ms.service: resiliency
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: aglick
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8dd45fb6dceddbd982be400f5d9af99e5023112e


---
# <a name="azure-resiliency-technical-guidance-recovery-from-data-corruption-or-accidental-deletion"></a>Guía técnica sobre resistencia en Azure: recuperación de datos dañados o de eliminaciones accidentales
Una parte de un plan de continuidad empresarial sólido consiste en tener un plan por si los datos resultan dañados o se eliminan accidentalmente. A continuación se proporciona información acerca de la recuperación en caso de datos dañados o eliminados accidentalmente debido a errores de la aplicación o a un error del operador.

## <a name="virtual-machines"></a>Máquinas virtuales
Para proteger máquinas virtuales de Azure (que a veces se denominan máquinas virtuales de infraestructura como servicio) de errores de las aplicaciones o eliminaciones accidentales, use [Copia de seguridad de Azure](https://azure.microsoft.com/services/backup/). Copia de seguridad de Azure permite la creación de copias de seguridad consistentes en varios discos de máquinas virtuales. Además, el almacén de copia de seguridad se puede replicar entre varias regiones para proporcionar recuperación en caso de pérdida de la región.

## <a name="storage"></a>Almacenamiento
Tenga en cuenta que aunque Almacenamiento de Azure ofrece resistencia de datos mediante réplicas automatizadas, esto no evita que se puedan dañar los datos del código de la aplicación (ni el de los desarrolladores y usuarios) a causa de una eliminación o actualización accidental o no intencionada. Mantener la fidelidad de los datos en caso de error de la aplicación o del usuario requiere técnicas más avanzadas, como copiar los datos en una ubicación de almacenamiento secundaria con un registro de auditoría. Los desarrolladores pueden sacar provecho de la [funcionalidad de instantánea](https://msdn.microsoft.com/library/azure/ee691971.aspx)de blobs, que permite crear instantáneas de solo lectura del contenido del blob en un momento dado. Esta funcionalidad puede utilizarse como base de una solución de fidelidad de datos para los blobs de Almacenamiento de Azure.

### <a name="blob-and-table-storage-backup"></a>Copia de seguridad de blobs y de Almacenamiento de tablas
Aunque el contenido de los blobs y las tablas es muy duradero, siempre representan el estado actual de los datos. La recuperación ante una modificación o eliminación no deseada puede necesitar la restauración de datos a un estado anterior. Esto se puede conseguir aprovechando las funcionalidades proporcionadas por Azure para almacenar y mantener copias de un estado anterior.

Para Blobs de Azure, puede realizar copias de seguridad de un estado anterior mediante la [característica de instantáneas de blob](https://msdn.microsoft.com/library/ee691971.aspx). Por cada instantánea, solo se le cobrará por el almacenamiento que necesite para almacenar las diferencias producidas en el blob desde el último estado de instantánea. Las instantáneas dependen de la existencia del blob original en el que se basan, por lo que se recomienda realizar una operación de copia a otro blob, o incluso a otra cuenta de almacenamiento. Esto garantiza que los datos de copia de seguridad que los datos de la copia de seguridad estén protegidos correctamente contra la eliminación accidental. Para las tablas de Azure, puede realizar copias de un estado anterior en una tabla diferente o en los Blobs de Azure. Puede consultar una guía más detallada y ejemplos de cómo realizar copias de seguridad de tablas y blobs en el nivel de aplicación, aquí:

* [Protecting Your Tables Against Application Errors](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/05/03/protecting-your-tables-against-application-errors/)
* [Protecting Your Blobs Against Application Errors](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/04/29/protecting-your-blobs-against-application-errors/)

## <a name="database"></a>Base de datos
Hay varias opciones de [continuidad empresarial](../sql-database/sql-database-business-continuity.md) (copia de seguridad, restauración) disponibles para la Base de datos SQL de Azure. Las bases de datos se pueden copiar mediante la funcionalidad de [copia de base de datos](../sql-database/sql-database-copy.md) o mediante la [exportación](../sql-database/sql-database-export.md) e [importación](https://msdn.microsoft.com/library/hh710052.aspx) de un archivo bacpac de SQL Server. Esta funcionalidad proporciona resultados transaccionales coherentes, cosa que no sucede en el caso de un archivo bacpac (a través del servicio de importación y exportación). Ambas opciones se ejecutan como servicios basados en cola en el centro de datos y no proporcionan actualmente un Acuerdo de Nivel de Servicio con tiempo de finalización.

> [!NOTE]
> Las opciones de copia de base de datos e importación y exportación colocan un nivel significativo de carga en la base de datos de origen, lo que puede desencadenar eventos de contención o limitación de recursos.
> 
> 

### <a name="sql-database-backup"></a>Copia de seguridad de Base de datos SQL
Las copias de seguridad de un estado anterior de la Base de datos SQL de Microsoft Azure se consiguen mediante [la copia de la Base de datos SQL de Azure](../sql-database/sql-database-copy.md). Este comando se puede usar para crear una copia transaccionalmente coherente de una base de datos en el mismo servidor de bases de datos lógicas o en otro. En cualquier caso, la copia de la base de datos es totalmente funcional y completamente independiente de la base de datos de origen. Cada copia que cree representa una opción de recuperación de un estado anterior. Puede recuperar completamente el estado de la base de datos cambiando el nombre de la nueva base de datos con el nombre de la base de datos de origen. De forma alternativa, puede recuperar un subconjunto específico de datos de la nueva base de datos mediante consultas de Transact-SQL. Para obtener más información sobre la base de datos SQL, consulte [Introducción a la continuidad empresarial con Base de datos SQL de Azure](../sql-database/sql-database-business-continuity.md).

### <a name="sql-server-on-virtual-machines-backup"></a>SQL Server en la copia de seguridad de Máquinas virtuales
En los casos en que SQL Server se utiliza con la infraestructura de Azure como máquinas virtuales de servicio (a menudo denominadas IaaS o máquinas virtuales de IaaS), hay dos opciones: las copias de seguridad tradicionales y el trasvase de registros. El uso de copias de seguridad tradicionales le permite restaurar a un estado anterior específico, pero el proceso de recuperación es lento. La restauración de copias de seguridad tradicionales requiere empezar con una copia de seguridad completa inicial y, a continuación, aplicar las copias de seguridad realizadas después de ella. La segunda opción es configurar una sesión de trasvase de registros para retrasar la restauración de las copias de seguridad del registro (por ejemplo, en dos horas). Esto proporciona un tiempo para recuperarse de los errores que se produzcan en el servidor principal.

## <a name="other-azure-platform-services"></a>Otros servicios de la plataforma de Azure
Algunos servicios de la plataforma de Azure almacenan información en una cuenta de almacenamiento controlada por el usuario o en la Base de datos SQL de Azure. Si el recurso de almacenamiento o la cuenta se eliminan o se dañan, esto podría provocar errores graves con el servicio. En estos casos, es importante mantener copias de seguridad que le permitan volver a crear los recursos si estos se han eliminado o dañado.

Para los Sitios web y los Servicios móviles de Azure, debe realizar la copia de seguridad y mantener las bases de datos asociadas. Para los servicios multimedia y las Máquinas virtuales de Azure, debe mantener la cuenta de Almacenamiento de Azure asociada y todos los recursos de esa cuenta. Por ejemplo, en el caso de Máquinas virtuales, debe realizar la copia de seguridad y administrar los discos de las máquinas virtuales en el Almacenamiento de blobs de Azure.

## <a name="checklists-for-data-corruption-or-accidental-deletion"></a>Listas de comprobación de datos dañados o eliminados accidentalmente
## <a name="virtual-machines-checklist"></a>Lista de comprobación de Máquinas virtuales
1. Revise la sección Máquinas virtuales de este documento.
2. Realice una copia de seguridad y mantenga los discos de las máquinas virtuales mediante Copia de seguridad de Azure (o a través de su propio sistema de copias de seguridad mediante Almacenamiento de blobs de Azure y las instantáneas de disco duro virtual).

## <a name="storage-checklist"></a>Lista de comprobación de almacenamiento
1. Revise la sección Almacenamiento de este documento.
2. Realice copias de seguridad de recursos de almacenamiento críticos de forma regular.
3. Considere el uso de la característica de instantáneas de blobs.

## <a name="database-checklist"></a>Lista de comprobación de base de datos
1. Revise la sección Base de datos de este documento.
2. Cree copias de seguridad en un momento dado mediante el comando Database Copy.

## <a name="sql-server-on-virtual-machines-backup-checklist"></a>Lista de comprobación de SQL Server en la copia de seguridad de Máquinas virtuales
1. Consulte la sección SQL Server en la copia de seguridad de Máquinas virtuales de este documento.
2. Use técnicas tradicionales de copia de seguridad y restauración.
3. Cree una sesión de trasvase de registros con retraso.

## <a name="web-apps-checklist"></a>Lista de comprobación de las aplicaciones web
1. Realice una copia de seguridad y mantenga la base de datos asociada, si hay alguna.

## <a name="media-services-checklist"></a>Lista de comprobación de Servicios multimedia
1. Realice una copia de seguridad y mantenga los recursos de almacenamiento asociados.

## <a name="more-information"></a>Más información
Para obtener más información acerca de las características de copia de seguridad y restauración de Azure, consulte [Copia de seguridad y archivado](https://azure.microsoft.com/documentation/scenarios/storage-backup-recovery/).

## <a name="next-steps"></a>Pasos siguientes
Este artículo forma parte de una serie que se centra en la [Guía técnica sobre resistencia en Azure](resiliency-technical-guidance.md). Si busca más recursos de resistencia, recuperación ante desastres y alta disponibilidad, consulte los [recursos adicionales](resiliency-technical-guidance.md#additional-resources)de la Guía técnica sobre resistencia en Azure.




<!--HONumber=Nov16_HO3-->



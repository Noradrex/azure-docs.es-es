---
title: "Administración de la potencia de proceso en Azure SQL Data Warehouse (REST) | Microsoft Docs"
description: Tareas de Transact-SQL (T-SQL) para el escalado horizontal del rendimiento ajustando DWU. Ahorre costes reduciendo el escalado fuera de horas punta.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: a970d939-2adf-4856-8a78-d4fe8ab2cceb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 41ab1c4b2709c2ea6890ca526db1dea177b7da1b


---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-t-sql"></a>Administración de la potencia de proceso en Almacenamiento de datos SQL de Azure (T-SQL)
> [!div class="op_single_selector"]
> * [Información general](sql-data-warehouse-manage-compute-overview.md)
> * [Portal](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
> 
> 

Escale el rendimiento mediante el escalado horizontal de los recursos de proceso y la memoria para satisfacer las necesidades cambiantes de la carga de trabajo. Ahorre costes reduciendo el escalado de los recursos fuera de horas punta o pausando el proceso por completo. 

Esta colección de tareas usa T-SQL para:

* Ver la configuración de DWU actual
* Cambiar recursos de proceso ajustando DWU

Para pausar o reanudar una base de datos, elija una de las otras opciones de plataforma en la parte superior de este artículo.

Para más información sobre esto, vea [Manage compute power overview][Manage compute power overview].

<a name="current-dwu-bk"></a>

## <a name="view-current-dwu-settings"></a>Ver la configuración de DWU actual
Para ver la configuración actual de DWU para las bases de datos:

1. Abra el Explorador de objetos de SQL Server en Visual Studio 2015.
2. Conéctese a la base de datos maestra asociada al servidor lógico de Base de datos SQL.
3. Seleccione en la vista de administración dinámica sys.database_service_objectives. Este es un ejemplo: 

```
SELECT
 db.name [Database],
 ds.edition [Edition],
 ds.service_objective [Service Objective]
FROM
 sys.database_service_objectives ds
 JOIN sys.databases db ON ds.database_id = db.database_id
```

<a name="scale-dwu-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute"></a>Escalado de proceso
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Para cambiar las DWU:

1. Conéctese a la base de datos maestra asociada al servidor lógico de Base de datos SQL.
2. Use la instrucción TSQL [ALTER DATABASE][ALTER DATABASE]. En el ejemplo siguiente se establece el objetivo de nivel de servicio en DW1000 para la base de datos MySQLDW. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Pasos siguientes
Para otras tareas de administración, vea [Introducción a la administración][Introducción a la administración].

<!--Image references-->

<!--Article references-->
[Límites de capacidad de servicio]: ./sql-data-warehouse-service-capacity-limits.md
[Introducción a la administración]: ./sql-data-warehouse-overview-manage.md
[Manage compute power overview]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->

[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx


<!--Other Web references-->

[Azure Portal]: http://portal.azure.com/



<!--HONumber=Nov16_HO3-->



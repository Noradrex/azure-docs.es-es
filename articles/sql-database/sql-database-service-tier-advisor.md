---
title: Recomendaciones sobre el nivel de precios de Base de datos SQL de Azure
description: "Si se cambian los niveles de precios en el Portal de Azure, se recomienda el nivel más apropiado para ejecutar la carga de trabajo de una Base de datos SQL de Azure existente. Los niveles de precios describen el nivel de servicio y el nivel de rendimiento de una base de datos SQL."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: 265beec9-7478-4abd-9ab5-4094e2e75fe4
ms.service: sql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 08/08/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 5a101aa78dbac4f1a0edb7f414b44c14db392652
ms.openlocfilehash: 1596e1d146af13fba965a0db45c2d9bbc0c6f6be


---
# <a name="sql-database-pricing-tier-recommendations"></a>Recomendaciones sobre el nivel de precios de Base de datos SQL
 Se ofrecen recomendaciones del plan de tarifa en el sentido del nivel de servicio y el nivel de rendimiento más apropiados para ejecutar la carga de trabajo de una Base de datos SQL de Azure existente.

> [!NOTE]
> Las recomendaciones sobre el plan de tarifa solo están disponibles para las bases de datos Web y Business y los grupos de bases de datos elásticas, y solo en el [Portal de Azure](https://portal.azure.com/).
>
>

Obtenga recomendaciones del plan de tarifa durante las siguientes tareas:

* [Cambio del nivel de servicio y del nivel de rendimiento (nivel de precios) de una base de datos SQL](sql-database-scale-up.md)
* [Actualizar un servidor SQL de Azure a V12](sql-database-upgrade-server-portal.md)
* Busque el servidor V12. Consulte [Recomendaciones sobre el plan de tarifa de Base de datos SQL](sql-database-service-tier-advisor.md).
* [Creación de un grupo de bases de datos elásticas](sql-database-elastic-pool.md#which-databases-go-in-a-pool)

## <a name="overview"></a>Información general
El servicio de Base de datos de SQL analiza el rendimiento actual y los requisitos de características mediante la evaluación del uso de recursos históricos para una base de datos SQL. Además, el nivel de servicio mínimo aceptable se determina en función del tamaño de la base de datos y las características de [continuidad del negocio](sql-database-business-continuity.md) habilitadas.

Esta información se analiza y se recomienda el nivel de servicio y el nivel de rendimiento que mejor se adapte para ejecutar la carga de trabajo típica de la base de datos y para mantener el conjunto de características actual.

* El servicio examina entre 15 a 30 días anteriores de datos históricos (uso de recursos, tamaño de base de datos y actividad de la base de datos) y realiza una comparación entre la cantidad de recursos consumidos y las limitaciones reales de los niveles de servicio disponibles y los niveles de rendimiento.
* Los datos se analizan en intervalos de 15 segundos y se clasifica el conjunto de resultados de cada intervalo en el nivel de servicio existente y nivel de rendimiento que sean más adecuados para controlar la carga de trabajo de ese conjunto de resultados.
* Después, estos ejemplos de 15 segundos se agregan en el análisis más extenso de 15 a 30 días y se recomienda el nivel de servicio y el del rendimiento que pueda manejar de manera óptima el 95% de la carga de trabajo histórica.

### <a name="recommendations"></a>Recomendaciones
Según el uso de la base de datos, se pueden encontrar dos categorías de recomendaciones:+

| Recomendación | Description |
|:--- |:--- |
| Actualizar |Actualice a un nuevo nivel. |
| No disponible |Las bases de datos requieren una carga de trabajo mínima o aproximadamente 35 días de actividad. No hay suficientes datos para proporcionar una recomendación válida. |

## <a name="getting-pricing-tier-recommendations"></a>Obtención de recomendaciones de nivel de precios
Para obtener recomendaciones sobre los planes de tarifa, seleccione una base de datos Web o Business existente y haga clic en **All settings** (Toda la configuración) y luego en **Plan de tarifa (escalar DTU)**. (Las recomendaciones del plan de tarifa también están disponibles durante la [Actualización de SQL Server de Azure a V12](sql-database-upgrade-server-portal.md)).

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Haga clic en **EXAMINAR** > **Bases de datos SQL**.
3. En la hoja **Bases de datos SQL** , haga clic en la base de datos para la que quiere ver una recomendación:

    ![Seleccionar base de datos][1]
4. En la hoja de la base de datos, seleccione **All settings** (Toda la configuración) y luego seleccione **Plan de tarifa (escalar DTU)**.
5. Se abre **Planes de tarifa recomendados**, donde puede hacer clic en el plan sugerido y luego hacer clic en el botón **Seleccionar** para cambiar a ese plan.

    ![Suscríbase a la vista previa][4]
6. Opcionalmente, haga clic en **Ver detalles de uso** para abrir la hoja **Detalles de recomendación de nivel de precios** donde puede ver el nivel recomendado para la base de datos, una comparación de características entre los niveles actuales y recomendados y un gráfico del análisis de uso de recursos históricos.

    ![Suscríbase a la vista previa][5]

## <a name="summary"></a>Resumen
Las recomendaciones de niveles de precios proporcionan una experiencia automatizada para recopilar datos de telemetría para cada Base de datos SQL y para recomendar la combinación del mejor nivel de servicio/nivel de rendimiento según los requisitos de las características y necesidades de rendimiento real de una base de datos. En la hoja Configuración, haga clic en **Plan de tarifa (escalar DTU)** para ver las recomendaciones sobre los planes de tarifa para todas las bases de datos Web y Business.

## <a name="next-steps"></a>Pasos siguientes
Dependiendo de los detalles de la base de datos específica, la realización de una actualización a un nivel de servicio superior o inferior no se suele producir de manera instantánea. El portal proporcionará notificaciones cuando la base de datos realice la transición a su nuevo nivel, o puede supervisar el estado de actualización mediante la consulta de la vista [sys.dm_operation_status (Azure SQL Database)](https://msdn.microsoft.com/library/dn270022.aspx) en la base de datos maestra del servidor de SQL Database.

<!--Image references-->
[1]: ./media/sql-database-service-tier-advisor/select-database.png
[4]: ./media/sql-database-service-tier-advisor/choose-pricing-tier.png
[5]: ./media/sql-database-service-tier-advisor/usage-details.png



<!--HONumber=Nov16_HO3-->



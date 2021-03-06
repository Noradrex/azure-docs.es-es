---
title: Escenarios de datos relacionados con Data Lake Store | Microsoft Docs
description: "Comprender los diferentes escenarios y herramientas mediante los cuales los datos se pueden recopilar, procesar, descargar y visualizar en un Almacén de Data Lake"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 37409a71-a563-4bb7-bc46-2cbd426a2ece
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 73d3e5577d0702a93b7f4edf3bf4e29f55a053ed
ms.openlocfilehash: 15460ef4add0e0ab94e776ffb715b86d39b89659


---
# <a name="using-azure-data-lake-store-for-big-data-requirements"></a>Uso del Almacén de Azure Data Lake para requisitos de macrodatos
Hay cuatro fases principales en el procesamiento de macrodatos:

* Introducción de grandes cantidades de datos en un almacén de datos, en tiempo real o por lotes
* Procesamiento de los datos
* Descarga de los datos
* Visualización de los datos

En este artículo nos centramos en estas fases relativas al Almacén de Azure Data Lake para comprender las opciones y herramientas disponibles para satisfacer sus necesidades de macrodatos.

## <a name="ingest-data-into-data-lake-store"></a>Introducción de datos en el Almacén de Data Lake
En esta sección se resaltan los distintos orígenes de datos y las distintas formas en que esos datos se pueden introducir en una cuenta de Almacén de Data Lake.

![Introducción de datos en el Almacén de Data Lake](./media/data-lake-store-data-scenarios/ingest-data.png "Ingest data into Data Lake Store")

### <a name="ad-hoc-data"></a>Datos ad-hoc
Representan conjuntos de datos más pequeños que se utilizan para la creación de un prototipo de una aplicación de macrodatos. Hay diferentes maneras de introducir datos ad hoc, según el origen de los datos.

| Origen de datos | Introducir mediante |
| --- | --- |
| Equipo local |<ul> <li>[Portal de Azure](/data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[CLI multiplataforma de Azure](data-lake-store-get-started-cli.md)</li> <li>[Usar herramientas de Data Lake para Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md#upload-source-data-files) </li></ul> |
| Blob de almacenamiento de Azure |<ul> <li>[Factoría de datos de Azure](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store)</li> <li>[herramienta AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp ejecutándose en un clúster de HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Datos de streaming
Representa los datos que se pueden generar por diversos orígenes, como aplicaciones, dispositivos o sensores, entre otros. Estos datos los pueden introducir distintas herramientas en un Almacén de Data Lake. Estas herramientas normalmente capturan y procesan los datos en eventos individuales y en tiempo real y, después, escriben los eventos en lotes en el Almacén de Data Lake para que puedan procesarse posteriormente.

A continuación, se muestran las herramientas que se pueden usar:

* [Azure Stream Analytics](../stream-analytics/stream-analytics-data-lake-output.md): los eventos que se recopilan en Event Hubs se pueden escribir en Azure Data Lake con resultados de Azure Data Lake Store.
* [Storm de HDInsight de Azure](../hdinsight/hdinsight-storm-write-data-lake-store.md) : se pueden escribir los datos directamente en el Almacén de Data Lake desde el clúster de Storm.
* [EventProcessorHost](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost): se pueden recibir eventos desde Event Hubs y, después, escribirlos en Data Lake Store con el [SDK de .NET para Data Lake Store](data-lake-store-get-started-net-sdk.md).

### <a name="relational-data"></a>Datos relacionales
También se pueden originar datos desde bases de datos relacionales. Durante un tiempo, las bases de datos relacionales recopilan grandes cantidades de datos que pueden proporcionar información clave si se procesan a través de una canalización de macrodatos. Puede usar las herramientas siguientes para mover estos datos al Almacén de Data Lake.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Factoría de datos de Azure](../data-factory/data-factory-data-movement-activities.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Datos de registro de servidor web (carga mediante aplicaciones personalizadas)
Este tipo de conjunto de datos es específicamente necesario porque el análisis de los datos de registro del servidor web es un caso de uso común para aplicaciones de macrodatos y requiere que se carguen grandes volúmenes de archivos de registro en el Almacén de Data Lake. Puede utilizar cualquiera de las herramientas siguientes para escribir sus propios scripts o aplicaciones para cargar dichos datos.

* [CLI multiplataforma de Azure](data-lake-store-get-started-cli.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [SDK para .NET del Almacén de Azure Data Lake](data-lake-store-get-started-net-sdk.md)
* [Factoría de datos de Azure](../data-factory/data-factory-data-movement-activities.md)

Para cargar datos de registro del servidor web y también para cargar otros tipos de datos (por ejemplo, datos de opiniones sociales), un buen enfoque es escribir sus propios aplicaciones o scripts personalizados, porque le ofrece la flexibilidad de incluir el componente que carga los datos como parte de su aplicación de macrodatos mayor. En algunos casos, este código puede adoptar la forma de un script o de una utilidad de línea de comandos simple. En otros casos, el código puede utilizarse para integrar el procesamiento de macrodatos en una aplicación o solución de negocio.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Datos asociados con los clústeres de HDInsight de Azure
La mayoría de los tipos de clúster de HDInsight (Hadoop, HBase, Storm) admiten el Almacén de Data Lake como un repositorio de almacenamiento de datos. Los clústeres de HDInsight acceden a los datos de blobs de Almacenamiento de Azure (WASB). Para mejorar el rendimiento, puede copiar los datos de WASB a una cuenta de Almacén de Data Lake asociada con el clúster. Puede usar las herramientas siguientes para copiar los datos.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [Servicio de AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
* [Factoría de datos de Azure](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store)

### <a name="data-stored-in-on-premise-or-iaas-hadoop-clusters"></a>Datos almacenados en clústeres locales o Hadoop IaaS
Es posible que haya grandes cantidades de datos almacenados en clústeres de Hadoop existentes, de forma local en los equipos mediante HDFS. Los clústeres de Hadoop pueden encontrarse en una implementación local o dentro de un clúster de IaaS en Azure. Podrían existir requisitos para copiar estos datos en el Almacén de Azure Data Lake de forma puntual o periódica. Existen diversas opciones que puede usar para lograr esto. A continuación se muestra una lista de alternativas, además de las ventajas y desventajas asociadas.

| Enfoque | Detalles | Ventajas | Consideraciones |
| --- | --- | --- | --- |
| Usar Data Factory de Azure (ADF) para copiar datos directamente desde los clústeres de Hadoop al Almacén de Azure Data Lake |[ADF admite HDFS como origen de datos](../data-factory/data-factory-hdfs-connector.md) |ADF proporciona compatibilidad inmediata con HDFS, así como una supervisión y administración integrales y de primera clase. |Requiere que se implemente Data Management Gateway localmente o en el clúster de IaaS. |
| Exportar datos desde Hadoop como archivos. Después copiar los archivos en el Almacén de Azure Data Lake con el mecanismo adecuado. |Puede copiar archivos al Almacén de Azure Data Lake por medio de:  <ul><li>[Azure PowerShell para SO Windows](data-lake-store-get-started-powershell.md)</li><li>[CLI multiplataforma de Azure para sistemas operativos distintos de Windows](data-lake-store-get-started-cli.md)</li><li>Aplicación personalizada con cualquier SDK de Data Lake Store</li></ul> |Se empieza rápido. Se pueden usar cargas personalizadas. |Proceso de varios pasos en el que participan varias tecnologías. La administración y la supervisión presentarán dificultades con el tiempo, dada la naturaleza personalizada de las herramientas. |
| Usar Distcp para copiar datos de Hadoop en Almacenamiento de Azure. Después copiar los datos de Almacenamiento de Azure al Almacén de Data Lake con el mecanismo adecuado. |Puede copiar datos de Almacenamiento de Azure a Azure Data Lake Store por medio de:  <ul><li>[Factoría de datos de Azure](../data-factory/data-factory-data-movement-activities.md)</li><li>[herramienta AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[Apache DistCp ejecutándose en clústeres de HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |Puede usar herramientas de código abierto. |Proceso de varios pasos en el que participan varias tecnologías. |

### <a name="really-large-datasets"></a>Conjuntos de datos realmente grandes
Para cargar conjuntos de datos cuyo tamaño oscila en varios terabytes, el uso de los métodos descritos anteriormente puede a veces resultar lento y costoso. En tales casos, puede utilizar las opciones siguientes.

* **Uso de Azure ExpressRoute**. Azure ExpressRoute permite crear conexiones privadas entre los centros de datos de Azure y la infraestructura de un entorno local. Esto ofrece una opción confiable para transferir grandes cantidades de datos. Para obtener más información, consulte la [documentación de Azure ExpressRoute](../expressroute/expressroute-introduction.md).
* **Carga "sin conexión" de los datos**. Si por cualquier razón no es posible utilizar Azure ExpressRoute, puede usar el [servicio Importación/Exportación de Azure](../storage/storage-import-export-service.md) para enviar unidades de disco duro con sus datos a un centro de datos de Azure. Los datos se cargan primero a Blobs de almacenamiento de Azure. Después, puede usar [Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store) o la [herramienta AdlCopy](data-lake-store-copy-data-azure-storage-blob.md) para copiar datos desde Azure Storage Blobs a Data Lake Store.

  > [!NOTE]
  > Al utilizar el servicio de importación y exportación, el tamaño de los archivos en los discos que se envían al centro de datos de Azure no debe ser mayor que 200 GB.
  >
  >

## <a name="process-data-stored-in-data-lake-store"></a>Procesamiento de los datos almacenados en el Almacén de Data Lake
Cuando los datos están disponibles en el Almacén de Data Lake, puede ejecutar un análisis en esos datos mediante las aplicaciones de macrodatos admitidas. Actualmente, se pueden utilizar HDInsight de Azure y Análisis de Azure Data Lake para ejecutar trabajos de análisis de datos en los datos almacenados en el Almacén de Data Lake.

![Análisis de datos en el Almacén de Data Lake](./media/data-lake-store-data-scenarios/analyze-data.png "Analyze data in Data Lake Store")

Puede buscar en los ejemplos siguientes.

* [Creación de un clúster de HDInsight con Almacén de Data Lake como almacenamiento](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Uso de Análisis de Azure Data Lake con el Almacén de Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="download-data-from-data-lake-store"></a>Descarga de datos del Almacén de Data Lake
También puede descargar o mover datos del Almacén de Azure Data Lake para situaciones como:

* Mover datos a otros repositorios para interactuar con las canalizaciones de procesamiento de datos existentes. Por ejemplo, puede mover los datos del Almacén de Data Lake a Base de datos SQL de Azure o a SQL Server local.
* Descargar datos en el equipo local para procesarlos en entornos IDE durante la creación de prototipos de aplicaciones.

![Salida de datos del Almacén de Data Lake](./media/data-lake-store-data-scenarios/egress-data.png "Egress data from Data Lake Store")

En tales casos, puede utilizar cualquiera de las opciones siguientes.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Factoría de datos de Azure](../data-factory/data-factory-data-movement-activities.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

También puede utilizar los métodos siguientes para escribir su propio script o aplicación para descargar datos desde el Almacén de Data Lake.

* [CLI multiplataforma de Azure](data-lake-store-get-started-cli.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [SDK para .NET del Almacén de Azure Data Lake](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-store"></a>Visualización de datos en el Almacén de Data Lake
Puede utilizar una combinación de servicios para crear representaciones visuales de los datos almacenados en el Almacén de Data Lake.

![Visualización de datos en el Almacén de Data Lake](./media/data-lake-store-data-scenarios/visualize-data.png "Visualize data in Data Lake Store")

* Para empezar, puede usar [Azure Data Factory para mover datos desde Data Lake Store a Azure SQL Data Warehouse](../data-factory/data-factory-data-movement-activities.md#supported-data-stores-and-formats).
* Después, puede [integrar Power BI con Almacenamiento de datos SQL de Azure](../sql-data-warehouse/sql-data-warehouse-integrate-power-bi.md) para crear una representación visual de los datos.



<!--HONumber=Nov16_HO3-->



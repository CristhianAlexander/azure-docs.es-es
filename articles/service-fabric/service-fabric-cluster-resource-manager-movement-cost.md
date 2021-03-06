---
title: 'Cluster Resource Manager de Service Fabric: costo de movimiento | Microsoft Docs'
description: "Información general del costo de movimiento de los servicios de Service Fabric"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: f022f258-7bc0-4db4-aa85-8c6c8344da32
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: f85365a36aea39b4179805e728c7ddafa140f08b


---
# <a name="service-movement-cost-for-influencing-cluster-resource-manager-choices"></a>Costo del movimiento del servicio para influir en las opciones de Cluster Resource Manager
Un factor importante que Cluster Resource Manager de Service Fabric debe tomar en cuenta cuando se intente determinar qué cambios hacer en un clúster es el costo general de conseguir esa solución. La noción de "costo" se compensa con la cantidad de equilibrio que se puede alcanzar.

Mover instancias o réplicas de un servicio supone unos costos mínimos de tiempo de CPU y ancho de banda de red. Para los servicios con estado, también está el costo de la cantidad de espacio en el disco y en memoria necesario para crear una copia del estado antes de cerrar las réplicas antiguas. Es obvio que querrá reducir el costo de cualquier solución que se elabore con Cluster Resource Manager de Azure Service Fabric. Pero tampoco quiere pasar por alto soluciones que mejorarían significativamente la asignación de recursos en el clúster.

Cluster Resource Manager cuenta con dos formas de calcular los costos y limitarlos, incluso al tratar de administrar el clúster de acuerdo con sus otrose objetivos. La primera es que, cuando Cluster Resource Manager está planeando una nueva distribución del clúster, cuenta cada uno de los movimientos que tendría que hacer. Si se generan dos soluciones con prácticamente el mismo equilibrio (puntuación), elija el que tiene el menor costo (el número total de movimientos).

Esta estrategia da buenos resultados. Pero, al igual que con cargas predeterminadas o estáticas, es poco probable que en cualquier sistema complejo todos los movimientos sean iguales. Probablemente algunos sean mucho más costosos.

## <a name="changing-a-replicas-move-cost-and-factors-to-consider"></a>Cambio del costo de movimiento de una réplica y factores a tener en cuenta
Igual que cuando se informa de la carga (otra característica de Cluster Resource Manager), los servicios pueden informar automáticamente de forma dinámica qué tan costoso es moverse en cualquier momento determinado.

Código:

```csharp
this.ServicePartition.ReportMoveCost(MoveCost.Medium);
```

El costo de un movimiento predeterminado también se puede especificar cuando se crea un servicio.

El valor MoveCost tiene cuatro niveles: Zero (Cero), Low (Bajo), Medium (Medio) y High (Alto). Los valores de MoveCost están relacionados entre sí, excepto en el caso del valor Zero. Zero significa que mover una réplica es gratuito y que no se debe tener en cuenta en la puntuación de la solución. Establecer el costo de movimiento en High *no* garantiza que la réplica se mantenga en un mismo lugar.

<center>
![Costo de movimiento como un factor en la selección de réplicas para el movimiento][Image1]
</center>

MoveCost ayuda a encontrar las soluciones que provocan la menor interrupción posible y que son más sencillas de conseguir a la vez que se llega a un equilibrio equivalente. La noción de costo de un servicio puede ser relativa a muchas cosas. Los factores más comunes para calcular el costo de movimiento son:

* La cantidad de estados o de datos que el servicio tiene que mover.
* El costo de desconexión de los clientes. El costo de mover una réplica principal es normalmente mayor que el costo de mover una réplica secundaria.
* El costo de interrumpir una operación en curso. Algunas operaciones en el nivel de almacén de datos o las operaciones realizadas en respuesta a una llamada del cliente son costosas. Llegadas a un cierto punto, no quiere detenerlas si no es necesario. Por lo tanto, mientras la operación está en ejecución, aumenta el costo de movimiento de este objeto de servicio para disminuir la probabilidad de que se mueva. Cuando finaliza la operación, devuelve el costo a la normalidad.

## <a name="next-steps"></a>Pasos siguientes
* Cluster Resource Manger de Service Fabric usa métricas para administrar el consumo y la capacidad en el clúster. Para más información sobre las métricas y cómo configurarlas, consulte [Administración de consumo y carga de recursos en Service Fabric con métricas](service-fabric-cluster-resource-manager-metrics.md).
* Para más información sobre la forma en que Cluster Resource Manager administra y equilibra la carga en el clúster, consulte [Equilibrio del clúster de Service Fabric](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png



<!--HONumber=Jan17_HO1-->



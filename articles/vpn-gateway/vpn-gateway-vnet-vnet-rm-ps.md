---
title: "Conexión de una red virtual de Azure a otra red virtual: PowerShell | Microsoft Docs"
description: "Este artículo le guiará para conectar redes virtuales entre sí por medio de PowerShell y el Administrador de recursos de Azure."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 0683c664-9c03-40a4-b198-a6529bf1ce8b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/22/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: d9ae8e8948d82b9695d7d144d458fe8180294084
ms.openlocfilehash: 56f101f4aecb6987d7ea6fb81bbf7f19a52baacd
ms.contentlocale: es-es
ms.lasthandoff: 05/23/2017


---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-powershell"></a>Configuración de una conexión de VPN Gateway de red virtual a red virtual mediante PowerShell

En este artículo se explica cómo crear una conexión de VPN Gateway entre redes virtuales. Las redes virtuales pueden estar en la misma región o en distintas, así como pertenecer a una única suscripción o a varias. Los pasos descritos en este artículo se aplican al modelo de implementación de Resource Manager y utilizan PowerShell. También se puede crear esta configuración con una herramienta o modelo de implementación distintos, mediante la selección de una opción diferente en la lista siguiente:

> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Resource Manager - CLI de Azure](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Clásico - Azure Portal](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Conexión de diferentes modelos de implementación - Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Conexión de diferentes modelos de implementación - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

La conexión de una red virtual a otra es muy parecida a la conexión de una red virtual a una ubicación de un sitio local. Ambos tipos de conectividad usan una puerta de enlace de VPN para proporcionar un túnel seguro con IPsec/IKE. Si las redes virtuales están en la misma región, podría pensar en conectarlas mediante emparejamiento de VNET. El emparejamiento de VNET no usa VPN Gateway. Para más información, consulte [Emparejamiento de VNET](../virtual-network/virtual-network-peering-overview.md).

Se puede combinar la comunicación entre redes virtuales con configuraciones de varios sitios. Esto permite establecer topologías de red que combinan la conectividad entre entornos locales con la conectividad entre redes virtuales, como se muestra en el diagrama siguiente:

![Acerca de las conexiones](./media/vpn-gateway-vnet-vnet-rm-ps/aboutconnections.png)

### <a name="why-connect-virtual-networks"></a>¿Por qué debería conectarse a redes virtuales?

Puede que desee conectar redes virtuales por las siguientes razones:

* **Presencia geográfica y redundancia geográfica entre regiones**

  * Puede configurar su propia replicación geográfica o sincronización con conectividad segura sin recurrir a los puntos de conexión a Internet.
  * Con el Equilibrador de carga y el Administrador de tráfico de Azure, puede configurar cargas de trabajo de alta disponibilidad con redundancia geográfica en varias regiones de Azure. Por ejemplo, puede configurar AlwaysOn de SQL con grupos de disponibilidad distribuidos en varias regiones de Azure.
* **Aplicaciones regionales de niveles múltiples con aislamiento o un límite administrativo**

  * Dentro de la misma región, se pueden configurar aplicaciones de niveles múltiples con varias redes virtuales conectadas entre sí para cumplir requisitos administrativos o de aislamiento.

Para más información acerca de las conexiones de red virtual a red virtual, consulte [P+F sobre conexiones de red virtual a red virtual](#faq) al final de este artículo.

## <a name="which-set-of-steps-should-i-use"></a>¿Qué serie de pasos debo seguir?

En este artículo, verá dos conjuntos de pasos diferentes. Un conjunto de pasos para [redes virtuales que residen en la misma suscripción](#samesub) y otro para [redes virtuales que residen en suscripciones diferentes](#difsub). La diferencia clave entre ambos conjuntos es si se pueden crear y configurar todos los recursos de red virtual y puerta de enlace en la misma sesión de PowerShell.

Los pasos de este artículo utilizan variables que se declaran al principio de cada sección. Si ya trabaja con redes virtuales existentes, modifique las variables para reflejar la configuración de su propio entorno. Si desea disponer de resolución de nombres en las redes virtuales, consulte [Resolución de nombres](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="samesub"></a>Conexión de redes virtuales que están en la misma suscripción

![diagrama de v2v](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

### <a name="before-you-begin"></a>Antes de empezar

Antes de empezar, necesitará instalar los cmdlets de PowerShell de Azure Resource Manager. Consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/overview) para más información sobre cómo instalar los cmdlets de PowerShell. 

### <a name="Step1"></a>Paso 1: Planeamiento de los intervalos de direcciones IP

En los pasos siguientes, se crearán dos redes virtuales junto con sus subredes de puerta de enlace y configuraciones correspondientes. A continuación crearemos una conexión VPN entre las dos redes virtuales. Es importante planear los intervalos de direcciones IP para la configuración de red. Tenga en cuenta que hay que asegurarse de que ninguno de los intervalos de VNet o intervalos de red local se solapen. En estos ejemplos, no se incluye ningún servidor DNS. Si desea disponer de resolución de nombres en las redes virtuales, consulte [Resolución de nombres](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

En los ejemplos usamos los siguientes valores:

**Valores para TestVNet1:**

* Nombre de red virtual: TestVNet1
* Grupo de recursos: TestRG1
* Ubicación: Este de EE. UU.
* TestVNet1: 10.11.0.0/16 y 10.12.0.0/16
* FrontEnd: 10.11.0.0/24
* Backend: 10.12.0.0/24
* GatewaySubnet: 10.12.255.0/27
* GatewayName: VNet1GW
* Dirección IP pública: VNet1GWIP
* VPNType: RouteBased
* Connection(1to4): VNet1toVNet4
* Connection(1to5): VNet1toVNet5
* ConnectionType: VNet2VNet

**Valores para TestVNet4:**

* Nombre de red virtual: TestVNet4
* TestVNet2: 10.41.0.0/16 y 10.42.0.0/16
* FrontEnd: 10.41.0.0/24
* Backend: 10.42.0.0/24
* GatewaySubnet: 10.42.255.0/27
* Grupo de recursos: TestRG4
* Ubicación: Oeste de EE. UU.
* GatewayName: VNet4GW
* Dirección IP pública: VNet4GWIP
* VPNType: RouteBased
* Conexión: VNet4toVNet1
* ConnectionType: VNet2VNet


### <a name="Step2"></a>Paso 2: Creación y configuración de TestVNet1

1. Declare las variables. En este ejemplo se declaran las variables con los valores para este ejercicio. En la mayoría de los casos, deberá reemplazar los valores por los suyos propios. No obstante, puede usar estas variables si está practicando los pasos para familiarizarse con este tipo de configuración. Si es necesario, modifique las variables y después cópielas y péguelas en la consola de PowerShell.

  ```powershell
  $Sub1 = "Replace_With_Your_Subcription_Name"
  $RG1 = "TestRG1"
  $Location1 = "East US"
  $VNetName1 = "TestVNet1"
  $FESubName1 = "FrontEnd"
  $BESubName1 = "Backend"
  $GWSubName1 = "GatewaySubnet"
  $VNetPrefix11 = "10.11.0.0/16"
  $VNetPrefix12 = "10.12.0.0/16"
  $FESubPrefix1 = "10.11.0.0/24"
  $BESubPrefix1 = "10.12.0.0/24"
  $GWSubPrefix1 = "10.12.255.0/27"
  $GWName1 = "VNet1GW"
  $GWIPName1 = "VNet1GWIP"
  $GWIPconfName1 = "gwipconf1"
  $Connection14 = "VNet1toVNet4"
  $Connection15 = "VNet1toVNet5"
  ```

2. Conéctese a su cuenta. Use el siguiente ejemplo para conectarse:

  ```powershell
  Login-AzureRmAccount
  ```

  Compruebe las suscripciones para la cuenta.

  ```powershell
  Get-AzureRmSubscription
  ```

  Especifique la suscripción que desea usar.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName $Sub1
  ```
3. Cree un nuevo grupo de recursos.

  ```powershell
  New-AzureRmResourceGroup -Name $RG1 -Location $Location1
  ```
4. Cree las configuraciones de subred para TestVNet1. En este ejemplo se crea una red virtual denominada TestVNet1 y tres subredes llamadas: GatewaySubnet, FrontEnd y Backend. Al reemplazar valores, es importante que siempre asigne el nombre GatewaySubnet a la subred de la puerta de enlace. Si usa otro, se produce un error al crear la puerta de enlace.

  El siguiente ejemplo usa las variables que estableció anteriormente. En este ejemplo, la subred de la puerta de enlace está usando un /27. Aunque es posible crear una subred de puerta de enlace tan pequeña como /29, se recomienda que cree una subred mayor que incluya más direcciones seleccionando al menos /28 o /27. Esto permitirá suficientes direcciones para dar cabida a posibles configuraciones adicionales que desee en el futuro.

  ```powershell
  $fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
  $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
  $gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1
  ```
5. Cree TestVNet1.

  ```powershell
  New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 `
  -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
  ```
6. Solicite que se asigne una dirección IP pública a la puerta de enlace que creará para la red virtual. Observe que AllocationMethod es dinámico. No puede especificar la dirección IP que desea usar. Se asigna dinámicamente a la puerta de enlace. 

  ```powershell
  $gwpip1 = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
  ```
7. Establezca la configuración de la puerta de enlace. La configuración de puerta de enlace define la subred y la dirección IP pública. Use el ejemplo para crear la configuración de la puerta de enlace.

  ```powershell
  $vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
  $subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```
8. Cree la puerta de enlace para TestVNet1. En este paso, creará la puerta de enlace de red virtual para TestVNet1. Las configuraciones VNet a VNet requieren un VpnType RouteBased. La creación de una puerta de enlace suele tardar 45 minutos o más, según la SKU de la puerta de enlace seleccionada.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
  ```

### <a name="step-3---create-and-configure-testvnet4"></a>Paso 3: Creación y configuración de TestVNet4

Una vez que haya configurado TestVNet1, cree TestVNet4. Siga los pasos a continuación y reemplace los valores por los suyos propios cuando sea necesario. Este paso puede realizarse en la misma sesión de PowerShell porque está en la misma suscripción.

1. Declare las variables. Asegúrese de reemplazar los valores por los que desea usar para su configuración.

  ```powershell
  $RG4 = "TestRG4"
  $Location4 = "West US"
  $VnetName4 = "TestVNet4"
  $FESubName4 = "FrontEnd"
  $BESubName4 = "Backend"
  $GWSubName4 = "GatewaySubnet"
  $VnetPrefix41 = "10.41.0.0/16"
  $VnetPrefix42 = "10.42.0.0/16"
  $FESubPrefix4 = "10.41.0.0/24"
  $BESubPrefix4 = "10.42.0.0/24"
  $GWSubPrefix4 = "10.42.255.0/27"
  $GWName4 = "VNet4GW"
  $GWIPName4 = "VNet4GWIP"
  $GWIPconfName4 = "gwipconf4"
  $Connection41 = "VNet4toVNet1"
  ```
2. Cree un nuevo grupo de recursos.

  ```powershell
  New-AzureRmResourceGroup -Name $RG4 -Location $Location4
  ```
3. Cree las configuraciones de subred para TestVNet4.

  ```powershell
  $fesub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
  $besub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
  $gwsub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName4 -AddressPrefix $GWSubPrefix4
  ```
4. Cree TestVNet4.

  ```powershell
  New-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 `
  -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4
  ```
5. Pida una dirección IP pública.

  ```powershell
  $gwpip4 = New-AzureRmPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 `
  -Location $Location4 -AllocationMethod Dynamic
  ```
6. Establezca la configuración de la puerta de enlace.

  ```powershell
  $vnet4 = Get-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
  $subnet4 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
  $gwipconf4 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4
  ```
7. Creación de la puerta de enlace de TestVNet4. La creación de una puerta de enlace suele tardar 45 minutos o más, según la SKU de la puerta de enlace seleccionada.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 `
  -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
  ```

### <a name="step-4---create-the-connections"></a>Paso 4: Creación de las conexiones

1. Obtenga ambas puertas de enlace de red virtual. Si ambas están en la misma suscripción, como en el ejemplo, puede completar este paso en la misma sesión de PowerShell.

  ```powershell
  $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
  $vnet4gw = Get-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4
  ```
2. Cree la conexión de TestVNet1 a TestVNet4. En este paso, creará la conexión de TestVNet1 a TestVNet4. Verá una clave compartida a la que se hace referencia en los ejemplos. Puede utilizar sus propios valores para la clave compartida. Lo importante es que la clave compartida coincida en ambas conexiones. Se tardará unos momentos en terminar de crear la conexión.

  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 `
  -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```
3. Cree la conexión de TestVNet4 a TestVNet1. Este paso es similar al anterior, salvo en que se crea la conexión de TestVNet4 a TestVNet1. Asegúrese de que coincidan las claves compartidas. Después de unos minutos, se habrá establecido la conexión.

  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 `
  -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```
4. Compruebe la conexión. Consulte la sección [Comprobación de la conexión](#verify).

## <a name="difsub"></a>Conexión de redes virtuales que están en suscripciones diferentes

![diagrama de v2v](./media/vpn-gateway-vnet-vnet-rm-ps/v2vdiffsub.png)

En este escenario, conectaremos TestVNet1 y TestVNet5. TestVNet1 y TestVNet5 residen en suscripciones diferentes. La diferencia entre estos pasos y el conjunto anterior es que parte de los pasos de configuración se deben realizar en una sesión de PowerShell distinta en el contexto de la segunda suscripción. Especialmente cuando las dos suscripciones pertenecen a distintas organizaciones.

### <a name="step-5---create-and-configure-testvnet1"></a>Paso 5: Creación y configuración de TestVNet1

Tiene que completar el [paso 1](#Step1) y el [paso 2](#Step2) de la sección anterior para crear y configurar TestVNet1 y la puerta de enlace de VPN para TestVNet1. Para esta configuración, no se necesita crear TestVNet4 de la sección anterior, aunque, si la creó, no entrará en conflicto con estos pasos. Cuando haya completado el paso 1 y el 2, continúe con el paso 6 para crear TestVNet5. 

### <a name="step-6---verify-the-ip-address-ranges"></a>Paso 6: Comprobación de los intervalos de direcciones IP

Es importante asegurarse de que el espacio de direcciones IP de la red virtual nueva, TestVNet5, no se solape con ninguno de los intervalos de red virtual o de puerta de enlace de red local. En este ejemplo, las redes virtuales pueden pertenecer a distintas organizaciones. En este ejercicio, use los siguientes valores para TestVNet5:

**Valores para TestVNet5:**

* Nombre de red virtual: TestVNet5
* Grupo de recursos: TestRG5
* Ubicación: Japón Oriental
* TestVNet5: 10.51.0.0/16 y 10.52.0.0/16
* FrontEnd: 10.51.0.0/24
* Backend: 10.52.0.0/24
* GatewaySubnet: 10.52.255.0.0/27
* GatewayName: VNet5GW
* Dirección IP pública: VNet5GWIP
* VPNType: RouteBased
* Conexión: VNet5toVNet1
* ConnectionType: VNet2VNet

### <a name="step-7---create-and-configure-testvnet5"></a>Paso 7: Creación y configuración de TestVNet5

Este paso debe realizarse en el contexto de la nueva suscripción. Es posible que esta parte la realice el administrador de otra organización que posea la suscripción.

1. Declare las variables. Asegúrese de reemplazar los valores por los que desea usar para su configuración.

  ```powershell
  $Sub5 = "Replace_With_the_New_Subcription_Name"
  $RG5 = "TestRG5"
  $Location5 = "Japan East"
  $VnetName5 = "TestVNet5"
  $FESubName5 = "FrontEnd"
  $BESubName5 = "Backend"
  $GWSubName5 = "GatewaySubnet"
  $VnetPrefix51 = "10.51.0.0/16"
  $VnetPrefix52 = "10.52.0.0/16"
  $FESubPrefix5 = "10.51.0.0/24"
  $BESubPrefix5 = "10.52.0.0/24"
  $GWSubPrefix5 = "10.52.255.0/27"
  $GWName5 = "VNet5GW"
  $GWIPName5 = "VNet5GWIP"
  $GWIPconfName5 = "gwipconf5"
  $Connection51 = "VNet5toVNet1"
  ```
2. Conéctese con la suscripción 5. Abre la consola de PowerShell y conéctate a tu cuenta. Use el siguiente ejemplo para ayudarle a conectarse:

  ```powershell
  Login-AzureRmAccount
  ```

  Compruebe las suscripciones para la cuenta.

  ```powershell
  Get-AzureRmSubscription
  ```

  Especifique la suscripción que desea usar.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName $Sub5
  ```
3. Cree un nuevo grupo de recursos.

  ```powershell
  New-AzureRmResourceGroup -Name $RG5 -Location $Location5
  ```
4. Cree las configuraciones de subred para TestVNet5.

  ```powershell
  $fesub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
  $besub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
  $gwsub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5
  ```
5. Cree TestVNet5.

  ```powershell
  New-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 `
  -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5
  ```
6. Pida una dirección IP pública.

  ```powershell
  $gwpip5 = New-AzureRmPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 `
  -Location $Location5 -AllocationMethod Dynamic
  ```
7. Establezca la configuración de la puerta de enlace.

  ```powershell
  $vnet5 = Get-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
  $subnet5  = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
  $gwipconf5 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName5 -Subnet $subnet5 -PublicIpAddress $gwpip5
  ```
8. Cree la puerta de enlace de TestVNet5.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 `
  -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1
  ```

### <a name="step-8---create-the-connections"></a>Paso 8: Creación de las conexiones

En este ejemplo, como las puertas de enlace están en suscripciones diferentes, hemos dividido el paso en dos sesiones de PowerShell marcadas como [Suscripción 1] y [Suscripción 5].

1. **[Suscripción 1]** Obtenga la puerta de enlace de red virtual para la suscripción 1. Inicie sesión y conéctese a Suscripción 1 antes de ejecutar el ejemplo siguiente:

  ```powershell
  $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
  ```

  Copie la salida de los siguientes elementos y envíesela al administrador de la suscripción 5 por correo electrónico u otro método.

  ```powershell
  $vnet1gw.Name
  $vnet1gw.Id
  ```

  Estos dos elementos tendrán valores similares a la salida de ejemplo siguiente:

  ```
  PS D:\> $vnet1gw.Name
  VNet1GW
  PS D:\> $vnet1gw.Id
  /subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
  ```
2. **[Suscripción 5]** Obtenga la puerta de enlace de red virtual para la suscripción 5. Inicie sesión y conéctese a Suscripción 5 antes de ejecutar el ejemplo siguiente:

  ```powershell
  $vnet5gw = Get-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5
  ```

  Copie la salida de los siguientes elementos y envíesela al administrador de la suscripción 1 por correo electrónico u otro método.

  ```powershell
  $vnet5gw.Name
  $vnet5gw.Id
  ```

  Estos dos elementos tendrán valores similares a la salida de ejemplo siguiente:

  ```
  PS C:\> $vnet5gw.Name
  VNet5GW
  PS C:\> $vnet5gw.Id
  /subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW
  ```
3. **[Suscripción 1]** Cree la conexión entre TestVNet1 y TestVNet5. En este paso, creará la conexión de TestVNet1 a TestVNet5. La diferencia en este caso es que no se puede obtener $vnet5gw directamente porque está en una suscripción diferente. Debe crear un nuevo objeto de PowerShell con los valores que se le hayan proporcionado para la suscripción 1 en los pasos anteriores. Use el ejemplo siguiente. Reemplace el nombre (Name), el identificador (Id) y la clave compartida por sus propios valores. Lo importante es que la clave compartida coincida en ambas conexiones. Se tardará unos momentos en terminar de crear la conexión.

  Conéctese a Suscripción 1 antes de ejecutar el ejemplo siguiente:

  ```powershell
  $vnet5gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
  $vnet5gw.Name = "VNet5GW"
  $vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
  $Connection15 = "VNet1toVNet5"
  New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```
4. **[Suscripción 5]** Cree la conexión entre TestVNet5 y TestVNet1. Este paso es similar al anterior, salvo en que se crea la conexión de TestVNet5 a TestVNet1. Aquí también se aplica el mismo proceso de creación de un objeto de PowerShell basándose en los valores obtenidos de la suscripción 1. En este paso, asegúrese de que las claves compartidas coincidan.

  Conéctese a Suscripción 5 antes de ejecutar el ejemplo siguiente:

  ```powershell
  $vnet1gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
  $vnet1gw.Name = "VNet1GW"
  $vnet1gw.Id = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
  New-AzureRmVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```

## <a name="verify"></a>Comprobación de una conexión

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [verify connections powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="faq"></a>P+F sobre conexiones de red virtual a red virtual

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

## <a name="next-steps"></a>Pasos siguientes

* Una vez completada la conexión, puede agregar máquinas virtuales a las redes virtuales. Consulte la [documentación sobre máquinas virtuales](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) para más información.
* Para más información acerca de BGP, consulte [Información general de BGP](vpn-gateway-bgp-overview.md) y [Configuración de BGP](vpn-gateway-bgp-resource-manager-ps.md).

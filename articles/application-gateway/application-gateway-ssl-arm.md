---
title: Configurar um gateway de aplicativo para descarregamento SSL usando o Gerenciador de Recursos do Azure | Microsoft Docs
description: Esta página fornece instruções para criar um gateway de aplicativo com descarregamento SSL usando o Gerenciador de Recursos do Azure
documentationcenter: na
services: application-gateway
author: georgewallace
manager: carmonm
editor: tysonn

ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/09/2016
ms.author: gwallace

---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-resource-manager"></a>Configurar um gateway de aplicativo para descarregamento SSL usando o Gerenciador de Recursos do Azure
> [!div class="op_single_selector"]
> -[Portal do Azure](application-gateway-ssl-portal.md)
> -[PowerShell do Azure Resource Manager](application-gateway-ssl-arm.md)
> -[PowerShell Clássico do Azure](application-gateway-ssl.md)
> 
> 

 O Azure Application Gateway pode ser configurado para encerrar a sessão SSL no gateway para evitar que a onerosa tarefa de descriptografia de SSL aconteça no web farm. O descarregamento SSL também simplifica a configuração do servidor front-end e o gerenciamento do aplicativo Web.

## <a name="before-you-begin"></a>Antes de começar
1. Instale a versão mais recente dos cmdlets do Azure PowerShell usando o Web Platform Installer. Você pode baixar e instalar a versão mais recente na seção **Windows PowerShell** da [página Downloads](https://azure.microsoft.com/downloads/).
2. Você cria uma rede virtual e uma sub-rede para o gateway de aplicativo. Verifique se não há máquinas virtuais ou implantações em nuvem usando a sub-rede. O Application Gateway deve estar sozinho em uma sub-rede de rede virtual.
3. Os servidores que você configura para usar o gateway de aplicativo devem existir ou ter seus pontos de extremidade criados na rede virtual ou com um IP/VIP público atribuído.

## <a name="what-is-required-to-create-an-application-gateway?"></a>O que é necessário para criar um gateway de aplicativo?
* **Pool de servidores back-end:** a lista de endereços IP dos servidores back-end. Os endereços IP listados devem pertencer à sub-rede da rede virtual ou devem ser um IP/VIP público.
* **Configurações do pool de servidores back-end:** cada pool tem configurações como porta, protocolo e afinidade baseada em cookie. Essas configurações são vinculadas a um pool e aplicadas a todos os servidores no pool.
* **Porta front-end:** essa porta é a porta pública aberta no gateway de aplicativo. O tráfego atinge essa porta e é redirecionado para um dos servidores back-end.
* **Ouvinte:** o ouvinte tem uma porta front-end, um protocolo (HTTP ou HTTPS; essas configurações diferenciam maiúsculas de minúsculas) e o nome do certificado SSL (se estiver configurando o descarregamento SSL).
* **Regra:** a regra vincula o ouvinte e o pool de servidores back-end e define à qual pool de servidores back-end o tráfego deve ser direcionado quando atinge um ouvinte específico. Atualmente, há suporte apenas para a regra *basic* . A regra *básica* é a distribuição de carga round robin.

**Observações adicionais sobre a configuração**

Para a configuração de certificados SSL, o protocolo em **HttpListener** deve ser alterado para *Https* (diferencia maiúsculas de minúsculas). O elemento **SslCertificate** é adicionado ao **HttpListener** com o valor da variável configurado para o certificado SSL. A porta front-end deve ser atualizada para 443.

**Para habilitar a afinidade baseada em cookie**: um gateway de aplicativo pode ser configurado para garantir que uma solicitação de uma sessão de cliente sempre seja direcionada para a mesma VM no web farm. Este cenário é concluído pela injeção de um cookie de sessão que permite que o gateway redirecione o tráfego corretamente. Para habilitar a afinidade baseada em cookie, defina **CookieBasedAffinity** como *Habilitado* no elemento **BackendHttpSettings**.

## <a name="create-an-application-gateway"></a>Criar um Application Gateway
A diferença entre usar o modelo de implantação Clássico do Azure e o Azure Resource Manager é a ordem em que você cria o gateway de aplicativo e os itens que precisam ser configurados.

Com o Gerenciador de Recursos, todos os componentes de um gateway de aplicativo são configurados individualmente e reunidos para criar um recurso do gateway de aplicativo.

A seguir, as etapas necessárias para criar um gateway de aplicativo:

1. Criar um grupo de recursos para o Gerenciador de Recursos
2. Criar uma rede virtual, uma sub-rede e um IP público para o gateway de aplicativo
3. Criar um objeto de configuração do gateway do aplicativo
4. Criar um recurso do gateway de aplicativo

## <a name="create-a-resource-group-for-resource-manager"></a>Criar um grupo de recursos para o Gerenciador de Recursos
Alterne para o modo PowerShell para usar os cmdlets do Gerenciador de Recursos do Azure. Mais informações estão disponíveis em [Usando o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Etapa 1
    Login-AzureRmAccount

### <a name="step-2"></a>Etapa 2
Verificar as assinaturas da conta.

    Get-AzureRmSubscription

Você deve se autenticar com suas credenciais.<BR>

### <a name="step-3"></a>Etapa 3
Escolha quais das suas assinaturas do Azure deseja usar. <BR>

    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### <a name="step-4"></a>Etapa 4
Crie um grupo de recursos (ignore esta etapa se você estiver usando um grupo de recursos existente).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

O Gerenciador de Recursos do Azure requer que todos os grupos de recursos especifiquem um local. Essa configuração é usada como o local padrão para os recursos do grupo de recursos em questão. Verifique se todos os comandos para criar um gateway de aplicativo usam o mesmo grupo de recursos.

No exemplo anterior, criamos um grupo de recursos denominado "appgw-RG" e o local "Oeste dos EUA".

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Criar uma rede virtual e uma sub-rede para o gateway de aplicativo
O exemplo a seguir mostra como criar uma rede virtual usando o Gerenciador de Recursos:

### <a name="step-1"></a>Etapa 1
    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Esta amostra atribui o intervalo de endereços 10.0.0.0/24 a uma variável de sub-rede a ser usada para criar uma rede virtual.

### <a name="step-2"></a>Etapa 2
    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

Esta amostra cria uma rede virtual chamada "appgwvnet" no grupo de recursos "appgw-rg" para a região Oeste dos EUA usando o prefixo 10.0.0.0/16 com a sub-rede 10.0.0.0/24.

### <a name="step-3"></a>Etapa 3
    $subnet = $vnet.Subnets[0]

Esta amostra atribui o objeto de sub-rede à variável $subnet para as próximas etapas.

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Criar um endereço IP público para a configuração de front-end
    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic

Esta amostra cria um recurso de IP público "publicIP01" no grupo de recursos "appgw-rg" para a região Oeste dos EUA.

## <a name="create-an-application-gateway-configuration-object"></a>Criar um objeto de configuração do gateway do aplicativo
### <a name="step-1"></a>Etapa 1
    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Esta amostra cria uma configuração de IP do gateway de aplicativo chamada "gatewayIP01". Quando o Application Gateway é iniciado, ele escolhe um endereço IP na sub-rede configurada e no tráfego de rede da rota para os endereços IP no pool de IPs de back-end. Tenha em mente que cada instância usa um endereço IP.

### <a name="step-2"></a>Etapa 2
    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

Esta amostra configura o pool de endereços IP de back-end denominado "pool01" com os endereços IP "134.170.185.46, 134.170.188.221, 134.170.185.50". Esses valores são os endereços IP que receberão o tráfego de rede proveniente do ponto de extremidade do IP de front-end. Substitua os endereços IP do exemplo anterior pelos endereços IP dos pontos de extremidade do aplicativo Web.

### <a name="step-3"></a>Etapa 3
    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled

Esta amostra define a configuração "poolsetting01" do gateway de aplicativo para o tráfego de rede com carga balanceada no pool de back-end.

### <a name="step-4"></a>Etapa 4
    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443

Esta amostra configura a porta do IP de front-end denominada "frontendport01" para o ponto de extremidade do IP público.

### <a name="step-5"></a>Etapa 5
    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password ‘<password>’

Esta amostra configura o certificado usado para a conexão SSL. O certificado deve estar no formato .pfx e a senha deve ter entre 4 e 12 caracteres.

### <a name="step-6"></a>Etapa 6
    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

Esta amostra cria a configuração de IP de front-end chamada "fipconfig01" e associa o endereço IP público à configuração de IP de front-end.

### <a name="step-7"></a>Etapa 7
    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert


Esta amostra cria o ouvinte de nome "listener01" e associa a porta de front-end à configuração de IP e ao certificado do front-end.

### <a name="step-8"></a>Etapa 8
    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Esta amostra cria a regra de roteamento do balanceador de carga chamada "rule01", que configura o comportamento do balanceador de carga.

### <a name="step-9"></a>Etapa 9
    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Esta amostra configura o tamanho da instância do gateway de aplicativo.

> [!NOTE]
> O valor padrão para *InstanceCount* é 2, com um valor máximo de 10. O valor padrão para *GatewaySize* é Medium. Você pode escolher entre Standard_Small, Standard_Medium e Standard_Large.
> 
> 

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Criar um gateway de aplicativo usando New-AzureApplicationGateway
    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert

Esta amostra cria um gateway de aplicativo com todos os itens de configuração das etapas anteriores. No exemplo, o gateway de aplicativo se chama "appgwtest".

## <a name="next-steps"></a>Próximas etapas
Se desejar configurar um gateway de aplicativo para usar com um balanceador de carga interno (ILB), veja [Criar um gateway de aplicativo com um ILB (balanceador de carga interno)](application-gateway-ilb.md).

Se deseja obter mais informações sobre as opções de balanceamento de carga no geral, consulte:

* [Balanceador de carga do Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Gerenciador de Tráfego do Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!--HONumber=Oct16_HO2-->



---
title: Gerenciar VMs usando o Azure Resource Manager e C# | Microsoft Docs
description: "Gerenciar máquinas virtuais usando o Azure Resource Manager e C#."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 93898bad-b861-4359-9a4b-348e1d491822
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4d0c334a0b1e047208f6f61e5181864b1c2e3a6a


---
# <a name="manage-azure-virtual-machines-using-azure-resource-manager-and-c"></a>Gerenciar máquinas virtuais usando o Azure Resource Manager e C
As tarefas neste artigo mostram como gerenciar máquinas virtuais, por exemplo: iniciar, parar e a atualizar. Deve existir uma máquina virtual em um grupo de recursos para a conclusão das tarefas neste artigo.

Para concluir as tarefas deste artigo, você precisará do seguinte:

* [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
* [Um token de autenticação](../resource-group-authenticate-service-principal.md)

## <a name="create-a-visual-studio-project-and-install-packages"></a>Criar um projeto do Visual Studio e instalar os pacotes
Os pacotes NuGet são a maneira mais fácil de instalar as bibliotecas que você precisa para concluir as tarefas deste artigo. As bibliotecas instaladas para este artigo são a biblioteca de autenticação do Azure Active Directory e a biblioteca do Provedor de Recursos de Computação. Conclua estas etapas para obter as bibliotecas no Visual Studio:

1. Clique em **Arquivo** > **Novo** > **Projeto**.
2. Em **Modelos** > **Visual C#**, selecione **Aplicativo de Console**, digite o nome e o local do projeto e, em seguida, clique em **OK**.
3. Clique com o botão direito do mouse no nome no Gerenciador de Soluções e clique em **Gerenciar Pacotes NuGet**.
4. Digite *Active Directory* na caixa de pesquisa, clique em **Instalar** para o pacote da Biblioteca de Autenticação do Active Directory e siga as instruções para instalar o pacote.
5. Na parte superior da página, selecione **Incluir Pré-lançamento**. Digite *Microsoft.Azure.Management.Compute* na caixa de pesquisa, clique em **Instalar** para as Bibliotecas do .NET de Computação e siga as instruções para instalar o pacote.

Agora você está pronto para começar a usar as bibliotecas para gerenciar suas máquinas virtuais.

## <a name="set-up-the-project"></a>Configurar o projeto
Agora que o aplicativo foi criado e as bibliotecas estão instaladas, você criará um token usando as informações do aplicativo. Esse token é usado para autenticar solicitações para o Azure Resource Manager.

1. Abra o arquivo Program.cs para o projeto que você criou e, em seguida, adicione o seguinte usando instruções na parte superior do arquivo:
   
        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.Compute;
        using Microsoft.Azure.Management.Compute.Models;
        using Microsoft.Rest;
2. Adicione variáveis ao método Main da classe Program para especificar o nome do grupo de recursos e o nome da máquina virtual e o identificador de assinatura:
   
        var groupName = "resource group name";
        var vmName = "virtual machine name";  
        var subscriptionId = "subsciption id";
   
    Você pode encontrar o identificador da assinatura executando Get-AzureRmSubscription.
3. Para obter o token necessário para criar as credenciais, adicione este método à classe Program:
   
        private static async Task<AuthenticationResult> GetAccessTokenAsync()
        {
          var cc = new ClientCredential("{client-id}", "{client-secret}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (token == null)
          {
            throw new InvalidOperationException("Could not get the token");
          }
          return token;
        }
   
    Substitua {client-id} pelo identificador do aplicativo do Azure Active Directory, {client-secret} pela chave de acesso do aplicativo do AD e {tenant-id} pelo identificador do locatário da sua assinatura. Você pode encontrar a ID do locatário executando Get-AzureRmSubscription. Você pode encontrar a chave de acesso usando o portal do Azure.
4. Para criar as credenciais, adicione este código ao método Principal em Program.cs:
   
        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);
5. Salve o arquivo Program.cs.

## <a name="display-information-about-a-virtual-machine"></a>Exibir informações sobre uma máquina virtual
1. Adicione este método à classe Programa no projeto que você criou anteriormente:
   
        public static async void GetVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Getting information about the virtual machine...");
   
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var vmResult = await computeManagementClient.VirtualMachines.GetAsync(
            groupName, 
            vmName, 
            InstanceViewTypes.InstanceView);
   
          Console.WriteLine("hardwareProfile");
          Console.WriteLine("   vmSize: " + vmResult.HardwareProfile.VmSize);
   
          Console.WriteLine("\nstorageProfile");
          Console.WriteLine("  imageReference");
          Console.WriteLine("    publisher: " + vmResult.StorageProfile.ImageReference.Publisher);
          Console.WriteLine("    offer: " + vmResult.StorageProfile.ImageReference.Offer);
          Console.WriteLine("    sku: " + vmResult.StorageProfile.ImageReference.Sku);
          Console.WriteLine("    version: " + vmResult.StorageProfile.ImageReference.Version);
          Console.WriteLine("  osDisk");
          Console.WriteLine("    osType: " + vmResult.StorageProfile.OsDisk.OsType);
          Console.WriteLine("    name: " + vmResult.StorageProfile.OsDisk.Name);
          Console.WriteLine("    createOption: " + vmResult.StorageProfile.OsDisk.CreateOption);
          Console.WriteLine("    uri: " + vmResult.StorageProfile.OsDisk.Vhd.Uri);
          Console.WriteLine("    caching: " + vmResult.StorageProfile.OsDisk.Caching);
   
          Console.WriteLine("\nosProfile");
          Console.WriteLine("  computerName: " + vmResult.OsProfile.ComputerName);
          Console.WriteLine("  adminUsername: " + vmResult.OsProfile.AdminUsername);
          Console.WriteLine("  provisionVMAgent: " + vmResult.OsProfile.WindowsConfiguration.ProvisionVMAgent.Value);
          Console.WriteLine("  enableAutomaticUpdates: " + vmResult.OsProfile.WindowsConfiguration.EnableAutomaticUpdates.Value);
   
          Console.WriteLine("\nnetworkProfile");
          foreach (NetworkInterfaceReference nic in vmResult.NetworkProfile.NetworkInterfaces)
          {
            Console.WriteLine("  networkInterface id: " + nic.Id);
          }
   
          Console.WriteLine("\nvmAgent");
          Console.WriteLine("  vmAgentVersion" + vmResult.InstanceView.VmAgent.VmAgentVersion);
          Console.WriteLine("    statuses");
          foreach (InstanceViewStatus stat in vmResult.InstanceView.VmAgent.Statuses)
          {
            Console.WriteLine("    code: " + stat.Code);
            Console.WriteLine("    level: " + stat.Level);
            Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
            Console.WriteLine("    message: " + stat.Message);
            Console.WriteLine("    time: " + stat.Time);
          }
   
          Console.WriteLine("\ndisks");
          foreach (DiskInstanceView idisk in vmResult.InstanceView.Disks)
          {
            Console.WriteLine("  name: " + idisk.Name);
            Console.WriteLine("  statuses");
            foreach (InstanceViewStatus istat in idisk.Statuses)
            {
              Console.WriteLine("    code: " + istat.Code);
              Console.WriteLine("    level: " + istat.Level);
              Console.WriteLine("    displayStatus: " + istat.DisplayStatus);
              Console.WriteLine("    time: " + istat.Time);
            }
          }
   
          Console.WriteLine("\nVM general status");
          Console.WriteLine("  provisioningStatus: " + vmResult.ProvisioningState);
          Console.WriteLine("  id: " + vmResult.Id);
          Console.WriteLine("  name: " + vmResult.Name);
          Console.WriteLine("  type: " + vmResult.Type);
          Console.WriteLine("  location: " + vmResult.Location);
          Console.WriteLine("\nVM instance status");
          foreach (InstanceViewStatus istat in vmResult.InstanceView.Statuses)
          {
            Console.WriteLine("\n  code: " + istat.Code);
            Console.WriteLine("  level: " + istat.Level);
            Console.WriteLine("  displayStatus: " + istat.DisplayStatus);
          }
   
        }
2. Para chamar o método que você acabou de adicionar, adicione este código ao método Main:
   
        GetVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
3. Salve o arquivo Program.cs.
4. Clique em **Iniciar** no Visual Studio e entre no Azure AD usando o mesmo nome de usuário e senha que você usa com sua assinatura.
   
    Quando você executar esse método, verá algo como este exemplo:
   
        Getting information about the virtual machine...
        hardwareProfile
          vmSize: Standard_A0
   
        storageProfile
          imageReference
            publisher: MicrosoftWindowsServer
            offer: WindowsServer
            sku: 2012-R2-Datacenter
            version: latest
          osDisk
            osType: Windows
            name: myosdisk
            createOption: FromImage
            uri: http://store1.blob.core.windows.net/vhds/myosdisk.vhd
            caching: ReadWrite
   
          osProfile
            computerName: vm1
            adminUsername: account1
            provisionVMAgent: True
            enableAutomaticUpdates: True
   
          networkProfile
            networkInterface 
              id: /subscriptions/{subscription-id}
                /resourceGroups/rg1/providers/Microsoft.Network/networkInterfaces/nc1
   
          vmAgent
            vmAgentVersion2.7.1198.766
            statuses
            code: ProvisioningState/succeeded
            level: Info
            displayStatus: Ready
            message: GuestAgent is running and accepting new configurations.
            time: 4/13/2016 8:35:32 PM
   
          disks
            name: myosdisk
            statuses
              code: ProvisioningState/succeeded
              level: Info
              displayStatus: Provisioning succeeded
              time: 4/13/2016 8:04:36 PM
   
          VM general status
            provisioningStatus: Succeeded
            id: /subscriptions/{subscription-id}
              /resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1
            name: vm1
            type: Microsoft.Compute/virtualMachines
            location: centralus
   
          VM instance status
            code: ProvisioningState/succeeded
              level: Info
              displayStatus: Provisioning succeeded
            code: PowerState/running
              level: Info
              displayStatus: VM running

## <a name="stop-a-virtual-machine"></a>Parar uma máquina virtual
Você pode interromper uma máquina virtual de duas maneiras. Você pode parar uma máquina virtual e manter todas as suas configurações, mas continuará a ser cobrado por ela, ou pode parar uma máquina virtual e desalocá-la. Quando uma máquina virtual é desalocada, todos os recursos associados a ela também são desalocadas e a cobrança para eles termina.

1. Comente qualquer código que você adicionou anteriormente ao método Principal, exceto o código para obter as credenciais.
2. Adicione este método à classe Programa:
   
        public static async void StopVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Stopping the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.PowerOffAsync(groupName, vmName);
        }
   
    Se você desejar desalocar a máquina virtual, altere a chamada PowerOff para este código:
   
        computeManagementClient.VirtualMachines.Deallocate(groupName, vmName);
3. Para chamar o método que você acabou de adicionar, adicione este código ao método Main:
   
        StopVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
4. Salve o arquivo Program.cs.
5. Clique em **Iniciar** no Visual Studio e entre no Azure AD usando o mesmo nome de usuário e senha que você usa com sua assinatura.
   
    Você deverá ver o status da máquina virtual ser alterado para Parada. Se você executou o método que chamando Deallocate, o status será Parado (desalocada).

## <a name="start-a-virtual-machine"></a>Iniciar uma máquina virtual
1. Comente qualquer código que você adicionou anteriormente ao método Principal, exceto o código para obter as credenciais.
2. Adicione este método à classe Programa:
   
        public static async void StartVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Starting the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.StartAsync(groupName, vmName);
        }
3. Para chamar o método que você acabou de adicionar, adicione este código ao método Main:
   
        StartVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
4. Salve o arquivo Program.cs.
5. Clique em **Iniciar** no Visual Studio e entre no Azure AD usando o mesmo nome de usuário e senha que você usa com sua assinatura.
   
    Você deverá ver o status da máquina virtual ser alterado para Em Execução.

## <a name="restart-a-running-virtual-machine"></a>Reiniciar uma máquina virtual em execução
1. Comente qualquer código que você adicionou anteriormente ao método Principal, exceto o código para obter as credenciais.
2. Adicione este método à classe Programa:
   
        public static async void RestartVirtualMachineAsync(
          TokenCredentials credential,
          string groupName,
          string vmName,
          string subscriptionId)
        {
          Console.WriteLine("Restarting the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.RestartAsync(groupName, vmName);
        }
3. Para chamar o método que você acabou de adicionar, adicione este código ao método Main:
   
        RestartVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
4. Salve o arquivo Program.cs.
5. Clique em **Iniciar** no Visual Studio e entre no Azure AD usando o mesmo nome de usuário e senha que você usa com sua assinatura.

## <a name="resize-a-virtual-machine"></a>Redimensionar uma máquina virtual
Este exemplo mostra como alterar o tamanho de uma máquina virtual em execução.

1. Comente qualquer código que você adicionou anteriormente ao método Principal, exceto o código para obter as credenciais.
2. Adicione este método à classe Programa:
   
        public static async void UpdateVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Updating the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
          vmResult.HardwareProfile.VmSize = "Standard_A1";
          await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
        }
3. Para chamar o método que você acabou de adicionar, adicione este código ao método Main:
   
        UpdateVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
4. Salve o arquivo Program.cs.
5. Clique em **Iniciar** no Visual Studio e entre no Azure AD usando o mesmo nome de usuário e senha que você usa com sua assinatura.
   
    Você deverá ver o status da máquina virtual ser alterado para Standard_A1.

## <a name="add-a-data-disk-to-a-virtual-machine"></a>Adicionar um disco de dados a uma máquina virtual
Este exemplo mostra como adicionar um disco de dados a uma máquina virtual em execução.

1. Comente qualquer código que você adicionou anteriormente ao método Principal, exceto o código para obter as credenciais.
2. Adicione este método à classe Programa:
   
        public static async void AddDataDiskAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Adding the disk to the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
          vmResult.StorageProfile.DataDisks.Add(
            new DataDisk
              {
                Lun = 0,
                Name = "mydatadisk1",
                Vhd = new VirtualHardDisk
                  {
                    Uri = "https://mystorage1.blob.core.windows.net/vhds/mydatadisk1.vhd"
                  },
                CreateOption = DiskCreateOptionTypes.Empty,
                DiskSizeGB = 2,
                Caching = CachingTypes.ReadWrite
              });
          await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
        }
3. Para chamar o método que você acabou de adicionar, adicione este código ao método Main:
   
        AddDataDiskAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
4. Salve o arquivo Program.cs.
5. Clique em **Iniciar** no Visual Studio e entre no Azure AD usando o mesmo nome de usuário e senha que você usa com sua assinatura.

## <a name="delete-a-virtual-machine"></a>Excluir uma máquina virtual
1. Comente qualquer código que você adicionou anteriormente ao método Principal, exceto o código para obter as credenciais.
2. Adicione este método à classe Programa:
   
        public static async void DeleteVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Deleting the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.DeleteAsync(groupName, vmName);
        }
3. Para chamar o método que você acabou de adicionar, adicione este código ao método Main:
   
        DeleteVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
4. Salve o arquivo Program.cs.
5. Clique em **Iniciar** no Visual Studio e entre no Azure AD usando o mesmo nome de usuário e senha que você usa com sua assinatura.

## <a name="next-steps"></a>Próximas etapas
Se houver problemas com a implantação, a próxima etapa será examinar [Solucionando os problemas de implantações do grupo de recursos com o portal do Azure](../resource-manager-troubleshoot-deployments-portal.md)




<!--HONumber=Nov16_HO3-->



---
title: "Redefinir uma senha local do Windows quando o agente convidado do Azure não estiver instalado | Microsoft Docs"
description: "Como redefinir a senha de uma conta de usuário local do Windows quando o agente convidado do Azure não estiver instalado ou funcionando em uma VM"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/05/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: ea637c05a0b30efcce40822556f7bfc58d4e87d9


---
# <a name="how-to-reset-local-windows-password-for-azure-vm"></a>Como redefinir a senha local do Windows para a VM do Azure
Você pode redefinir a senha local do Windows de uma VM no Azure usando o [portal do Azure ou Azure PowerShell](virtual-machines-windows-reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), desde que o agente convidado do Azure esteja instalado. Esse método é a principal maneira de redefinir uma senha para uma VM do Azure. Caso encontre problemas com o agente convidado do Azure, como não responder ou falhar na instalação após carregamento de uma imagem personalizada, você poderá redefinir manualmente uma senha do Windows. Este artigo fornece detalhes sobre como redefinir uma senha de conta local anexando o disco virtual do SO de origem a outra VM. 

> [!WARNING]
> Use esse processo apenas como último recurso. Sempre tentar redefinir uma senha usando o [portal do Azure ou Azure PowerShell](virtual-machines-windows-reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) primeiro.
> 
> 

## <a name="overview-of-the-process"></a>Visão geral do processo
As principais etapas para redefinir uma senha local para uma VM do Windows no Azure quando não há acesso ao agente convidado do Azure são as seguintes:

* Exclua a VM de origem. Os discos virtuais são mantidos.
* Anexe um disco do SO da VM de origem a outra VM na sua assinatura do Azure. Essa VM é conhecida como a VM para solução de problemas.
* Usando a VM para solução de problemas, crie alguns arquivos de configuração no disco do SO da VM de origem.
* Desanexe da VM para solução de problemas o disco do SO da VM.
* Use um modelo do Resource Manager para criar uma VM usando o disco virtual original.
* Quando a nova VM é inicializada, os arquivos de configuração que você cria atualizam a senha do usuário exigido.

## <a name="detailed-steps"></a>Etapas detalhadas
Sempre tentar redefinir uma senha usando o [portal do Azure ou Azure PowerShell](virtual-machines-windows-reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) antes de tentar as etapas seguintes. Verifique se você tem um backup da VM antes de iniciar. 

1. Exclua a VM afetada no portal do Azure. A exclusão da VM exclui apenas os metadados, a referência da VM dentro do Azure. Os discos virtuais são mantidos quando a VM é excluída:
   
   * Escolha a VM no portal do Azure e clique em *Excluir*:
     
     ![Excluir VM existente](./media/virtual-machines-windows-reset-local-password-without-guest-agent/delete_vm.png)
2. Anexe o disco do SO da VM de origem à VM para solução de problemas. A VM para solução de problemas deve estar na mesma região que o disco do SO da VM de origem (como `West US`):
   
   * Escolha a VM para solução de problemas no portal do Azure. Clique em *Discos* | *Anexar existente*:
     
     ![Anexar disco existente](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_attach_existing.png)
     
     Escolha *Arquivo VHD* e, em seguida, a conta de armazenamento que contém sua VM de origem:
     
     ![Escolher conta de armazenamento](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_select_storageaccount.PNG)
     
     Escolha o contêiner de origem. O contêiner de origem normalmente é *vhds*:
     
     ![Escolher contêiner de armazenamento](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_select_container.png)
     
     Escolha o vhd do SO a ser anexado. Clique em *Selecionar* para concluir o processo:
     
     ![Escolher o disco virtual de origem](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_select_source_vhd.png)
3. Conecte-se à VM para solução de problemas usando a Área de Trabalho Remota e assegure-se de que o disco do SO da VM de origem esteja visível:
   
   * Escolha a VM para solução de problemas no portal do Azure e clique em *Conectar*.
   * Abra o arquivo RDP que foi baixado. Insira o nome de usuário e a senha da VM para solução de problemas.
   * No Explorador de Arquivos, procure o disco de dados anexado. Se o VHD da VM de origem for o único disco de dados anexado à VM para solução de problemas, ele deverá ser a unidade F:
     
     ![Exibir o disco de dados anexado](./media/virtual-machines-windows-reset-local-password-without-guest-agent/troubleshooting_vm_fileexplorer.png)
4. Crie `gpt.ini` em `\Windows\System32\GroupPolicy` na unidade da VM de origem (se gpt.ini existir, renomeie para gpt.ini.bak):
   
   > [!WARNING]
   > Tome cuidado para não criar acidentalmente os arquivos a seguir em C:\Windows, a unidade do SO da VM para solução de problemas. Crie os arquivos a seguir na unidade do SO da VM de origem que está anexada como um disco de dados.
   > 
   > 
   
   * Adicione as seguintes linhas no arquivo `gpt.ini` que criou:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Criar gpt.ini](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_gpt_ini.png)
5. Crie `scripts.ini` em `\Windows\System32\GroupPolicy\Machine\Scripts`. Verifique se as pastas ocultas são mostradas. Se necessário, crie as pastas`Machine` ou `Scripts`.
   
   * Adicione as seguintes linhas ao arquivo `scripts.ini` que você criou:
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Criar scripts.ini](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_scripts_ini.png)
6. Crie `FixAzureVM.cmd` em `\Windows\System32` com o seguinte conteúdo, substituindo `<username>` e `<newpassword>` por seus próprios valores:
   
    ```
    NET USER <username> <newpassword>
    ```
   
    ![Criar FixAzureVM.cmd](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_fixazure_cmd.png)
   
    Você deve atender aos requisitos de complexidade de senha configurada para sua VM ao definir a nova senha.
7. No portal do Azure, desanexe o disco da VM para solução de problemas:
   
   * Escolha a VM para solução de problemas no portal do Azure e clique em *Discos*.
   * Selecione o disco de dados anexado na etapa 2 e clique em *Desanexar*:
     
     ![Desanexar disco](./media/virtual-machines-windows-reset-local-password-without-guest-agent/detach_disk.png)
8. Antes de criar uma VM, obtenha o URI para o disco do SO de origem:
   
   * Escolha a conta de armazenamento no portal do Azure e clique em *Blobs*.
   * Escolha o contêiner. O contêiner de origem normalmente é *vhds*:
     
     ![Selecionar o blob da conta de armazenamento](./media/virtual-machines-windows-reset-local-password-without-guest-agent/select_storage_details.png)
     
     Escolha o VHD do SO da VM de origem e clique no botão *Copiar* ao lado do nome da *URL*:
     
     ![Copiar URI do disco](./media/virtual-machines-windows-reset-local-password-without-guest-agent/copy_source_vhd_uri.png)
9. Crie uma VM usando o disco do SO da VM de origem:
   
   * Use [este modelo do Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) para criar uma VM de um VHD especializado. Clique no botão `Deploy to Azure` para abrir o portal do Azure com os detalhes do modelo que você populou.
   * Se quiser manter todas as configurações anteriores para a VM, escolha *Editar modelo* para fornecer a VNet, a sub-rede, o adaptador de rede ou o IP público existentes.
   * Na caixa de texto do parâmetro `OSDISKVHDURI`, cole o URI do VHD de origem obtido na etapa anterior:
     
     ![Criar uma VM usando o modelo](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_new_vm_from_template.png)
10. Depois que a nova VM estiver em execução, conecte-se à ela usando a Área de Trabalho Remota com a nova senha que você especificou no script `FixAzureVM.cmd`.
11. Na sessão remota para a nova VM, remova os seguintes arquivos para limpar o ambiente:
    
    * De %windir%\System32
      * remova FixAzureVM.cmd
    * De %windir%\System32\GroupPolicy\Machine\
      * remova scripts.ini
    * De %windir%\System32\GroupPolicy
      * remova gpt.ini (se gpt.ini já existia e você o renomeou para gpt.ini.bak, renomeie o arquivo .bak de volta para gpt.ini)

## <a name="next-steps"></a>Próximas etapas
Se você ainda não conseguir conectar usando a Área de Trabalho Remota, confira o [guia de solução de problemas do RDP](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). O [guia para solução de problemas detalhada de RDP](virtual-machines-windows-detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) explica os métodos da solução de problemas, e não etapas específicas. Você também pode [abrir uma solicitação de suporte do Azure](https://azure.microsoft.com/support/options/) para obter assistência prática.




<!--HONumber=Nov16_HO3-->



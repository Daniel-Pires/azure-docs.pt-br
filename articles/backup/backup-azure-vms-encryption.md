---
title: "VMs criptografadas de backup e restauração usando o Backup do Azure"
description: "Este artigo fala sobre a experiência de backup e restauração para VMs criptografada usando a Azure Disk Encryption."
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 8387f186-7d7b-400a-8fc3-88a85403ea63
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/25/2016
ms.author: markgal; jimpark; trinadhk
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: fd65e7acc10b3e750025279820bddbdef5de5498


---
# <a name="backup-and-restore-encrypted-vms-using-azure-backup"></a>VMs criptografadas de backup e restauração usando o Backup do Azure
Este artigo fala sobre as etapas para fazer backup e restauração de máquinas virtuais usando o Backup do Azure. Ele também oferece detalhes sobre os cenários com suporte, os pré-requisitos e as etapas de solução de problemas para casos de erro.

## <a name="supported-scenarios"></a>Cenários com suporte
> [!NOTE]
> 1. O backup e a restauração de VMs criptografadas têm suporte somente para as máquinas virtuais implantadas pelo Resource Manager. Não há suporte para as máquinas virtuais Clássicas. <br>
> 2. Isso só tem suporte para as máquinas virtuais criptografadas usando a Chave de Criptografia BitLocker e a Chave de Criptografia. Não tem suporte para as máquinas virtuais criptografadas usando somente a Chave de Criptografia BitLocker. <br>
> 
> 

## <a name="pre-requisites"></a>Pré-requisitos
1. A máquina virtual foi criptografada usando a [Azure Disk Encryption](../security/azure-security-disk-encryption.md). Ele deve ser criptografado usando a Chave de Criptografia BitLocker e a Chave de Criptografia.
2. O cofre de serviços de recuperação foi criado e a replicação de armazenamento definida usando as etapas mencionadas no artigo [Preparar seu ambiente para backup](backup-azure-arm-vms-prepare.md).

## <a name="backup-encrypted-vm"></a>Fazer backup da VM criptografada
Use as etapas a seguir para definir o objetivo do backup, definir a política, configurar itens e disparar o backup.

### <a name="configure-backup"></a>Configurar o backup
1. Se você já tiver um cofre dos Serviços de Recuperação aberto, vá para a próxima etapa. Se você não tiver um cofre dos Serviços de Recuperação aberto, mas estiver no portal do Azure, no menu Hub, clique em **Procurar**.
   
   * Na lista de recursos, digite **Serviços de Recuperação**.
   * Quando você começa a digitar, a lista é filtrada com base em sua entrada. Quando vir a opção **Cofres de Serviços de Recuperação**, clique nela.
     
      ![Criar Cofre de Serviços de Recuperação - etapa 1](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>
     
     A lista de cofres de Serviços de Recuperação aparecerá. Na lista de cofres de Serviços de Recuperação, selecione um cofre.
     
     O painel de cofres selecionados será aberto.
2. Na lista de itens que aparece no cofre, clique em **Backup** para abrir a folha Backup.
   
      ![Abrir a folha Backup](./media/backup-azure-vms-encryption/select-backup.png) 
3. Na folha Backup, clique em **Meta de backup** para abrir a folha Meta de Backup.
   
      ![Abrir a folha Cenário](./media/backup-azure-vms-encryption/select-backup-goal-one.png) 
4. Na folha Meta de Backup, defina **Onde está a carga de trabalho em execução** no Azure, **Do que você deseja fazer backup** na máquina Virtual e clique **OK**.
   
   A folha Meta de Backup fecha e a folha Política de Backup abre.
   
   ![Abrir a folha Cenário](./media/backup-azure-vms-encryption/select-backup-goal-two.png) 
5. Na folha Política de Backup, selecione a política de backup que você deseja aplicar no cofre e clique em **OK**.
   
      ![Selecionar a política de backup](./media/backup-azure-vms-encryption/setting-rs-backup-policy-new.png) 
   
    Os detalhes da política padrão estão listados nos detalhes. Se você quiser criar uma política, selecione **Criar Nova** no menu suspenso. Quando você clicar em **OK**, a política de backup será associada ao cofre.
   
    Em seguida, escolha as VMs para associar ao cofre.
6. Escolha as máquinas virtuais para associar à política especificada e clique em **OK**.
   
      ![Selecionar VMs criptografadas](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)
7. Essa página mostra uma mensagem sobre o cofre de chaves associado às VMs criptografadas selecionadas. O serviço de backup requer acesso somente leitura às chaves e segredos no cofre de chaves. Ele usa essas permissões para fazer backup da chave e do segredo, junto com as VMs associadas. 
   
      ![Mensagem de VMs criptografada](./media/backup-azure-vms-encryption/encrypted-vm-message.png)
   
      Agora que você definiu todas as configurações para o cofre, na folha Backup, clique em Habilitar Backup na parte inferior da página. Habilitar o Backup implanta a política para o cofre e as VMs.
8. A próxima fase de preparação será instalar o Agente de VM ou verificar se ele está instalado. Para fazer o mesmo, use as etapas mencionadas no artigo [Preparar seu ambiente para backup](backup-azure-arm-vms-prepare.md). 

### <a name="triggering-backup-job"></a>Disparar o trabalho de backup
Use as etapas mencionadas no artigo [Fazer backup das VMs do Azure no cofre de serviços de recuperação](backup-azure-arm-vms.md) para disparar o trabalho de backup.

## <a name="restore-encrypted-vm"></a>Restauração a VM criptografada
A experiência de restauração para máquinas virtuais criptografadas e não criptografadas é igual. Use as etapas mencionadas em [restaurar máquinas virtuais no portal do Azure](backup-azure-arm-restore-vms.md) para restaurar a VM criptografada. No caso de você precisar restaurar as chaves e segredos, certifique-se de que o cofre de chaves para restaurá-los caso ele já exista.

## <a name="troubleshooting-errors"></a>Solucionar erros
| Operação | Detalhes do erro | Resolução |
| --- | --- | --- |
| Backup |Falha na validação pois a máquina virtual é criptografada somente com BEK. Os backups podem ser habilitados somente para máquinas virtuais criptografadas com BEK e KEK. |A máquina virtual deve ser criptografada usando BEK e KEK. Depois disso, o backup deve ser habilitado. |
| Restaurar |Você não poderá restaurar essa VM criptografada pois o cofre de chaves associado a essa VM não existe. |Crie o cofre de chaves usando [Introdução ao Cofre de Chaves do Azure](../key-vault/key-vault-get-started.md). Veja o artigo [Restaurar chave do Cofre de Chaves e o segredo usando o Backup do Azure Backup](backup-azure-restore-key-secret.md) para restaurar a chave e o segredo, se não estiverem presentes. |
| Restaurar |Você não poderá restaurar essa VM criptografada pois a chave e o segredo associados a essa VM não existem. |Veja o artigo [Restaurar chave do Cofre de Chaves e o segredo usando o Backup do Azure Backup](backup-azure-restore-key-secret.md) para restaurar a chave e o segredo, se não estiverem presentes. |




<!--HONumber=Nov16_HO3-->



---
title: "Tutorial: Integração do Azure Active Directory com o Panorama9 | Microsoft Docs"
description: "Saiba como usar o Panorama9 com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 5e28d7fa-03be-49f3-96c8-b567f1257d44
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: bf29d754d365320c35cdd44e2116a8b5beb3a27e


---
# <a name="tutorial-azure-active-directory-integration-with-panorama9"></a>Tutorial: Integração do Active Directory do Azure com o Panorama9
O objetivo deste tutorial é mostrar a integração do Azure com o Panorama9.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura habilitada para logon único do Panorama9

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Panorama9 poderão fazer logon único no aplicativo em seu site de empresa do Panorama9 (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para Panorama9
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-panorama9-tutorial/IC790016.png "Scenario")

## <a name="enabling-the-application-integration-for-panorama9"></a>Habilitando a integração de aplicativos para Panorama9
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Panorama9.

### <a name="to-enable-the-application-integration-for-panorama9-perform-the-following-steps"></a>Para habilitar a integração de aplicativos para o Panorama9, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-panorama9-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-panorama9-tutorial/IC700994.png "Applications")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-panorama9-tutorial/IC749321.png "Add application")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-panorama9-tutorial/IC749322.png "Add an application from gallerry")
6. Na **caixa de pesquisa**, digite **Panorama9**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-panorama9-tutorial/IC790017.png "Application Gallery")
7. No painel de resultados, selecione **Panorama9** e clique em **Concluir** para adicionar o aplicativo.
   
   ![Panorama9](./media/active-directory-saas-panorama9-tutorial/IC790018.png "Panorama9")
   
   ## <a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Panorama9 com sua conta do AD do Azure usando federação baseada em protocolo SAML.  
Configurar o logon único para o Panorama9 exige que você recupere um valor de impressão digital de um certificado.  
Se você não estiver familiarizado com esse procedimento, veja [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração do aplicativo **Panorama9**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar Logon Único](./media/active-directory-saas-panorama9-tutorial/IC790019.png "Configure Single Sign-On")
2. Na página **Como você deseja que os usuários façam logon no Panorama9**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar o logon único](./media/active-directory-saas-panorama9-tutorial/IC790020.png "Configure Single Sign-On")
3. Na página **Configurar a URL do Aplicativo**, na caixa de texto **URL de Entrada no Panorama9**, digite a URL usada pelos usuários para entrar no Panorama9 (por exemplo: "*https://dashboard.panorama9.com/saml/access/3262*") e clique em **Avançar**.
   
   ![Configurar a URL do Aplicativo](./media/active-directory-saas-panorama9-tutorial/IC790021.png "Configure App URL")
4. Na página **Configurar logon único no Panorama9**, para baixar seu certificado, clique em **Baixar certificado** e salve-o localmente no computador.
   
   ![Configurar o logon único](./media/active-directory-saas-panorama9-tutorial/IC790022.png "Configure Single Sign-On")
5. Em uma janela de navegador da Web diferente, faça logon no site de sua empresa do Panorama9 como administrador.
6. Na barra de ferramentas na parte superior, clique em **Gerenciar** e em **Extensões**.
   
   ![Extensões](./media/active-directory-saas-panorama9-tutorial/IC790023.png "Extensions")
7. No diálogo **Extensões**, clique em **Logon Único**.
   
   ![Logon Único](./media/active-directory-saas-panorama9-tutorial/IC790024.png "Single Sign-On")
8. Na seção **Configurações** , realize as seguintes etapas:
   
   ![Configurações](./media/active-directory-saas-panorama9-tutorial/IC790025.png "Settings")
   
   1. No portal clássico do Azure, na página de diálogo **Configurar logon único no Panorama9**, copie o valor da **URL do Serviço de Logon Único** e cole-o na caixa de texto **URL do provedor de identidade**.
   2. Copie o valor de **Impressão Digital** do certificado exportado e cole-o na caixa de texto **Impressão digital do certificado**.  
      
      > [!TIP]
      > Para obter mais detalhes, consulte [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)
      > 
      > 
   3. Clique em **Salvar**.
9. No portal clássico do Azure AD, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar o diálogo **Configurar Logon Único**.
   
   ![Configurar o logon único](./media/active-directory-saas-panorama9-tutorial/IC790026.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no Panorama9, eles devem ser provisionados no Panorama9.  
No caso do Panorama9, o provisionamento é uma tarefa manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o provisionamento de usuários, execute as seguintes etapas:
1. Faça logon em seu site de empresa do **Panorama9** como administrador.
2. No menu na parte superior, clique em **Gerenciar** e em **Usuários**.
   
   ![Usuários](./media/active-directory-saas-panorama9-tutorial/IC790027.png "Users")
3. Clique em **+**.
4. Na seção Dados do usuário, execute as seguintes etapas:
   
   ![Usuários](./media/active-directory-saas-panorama9-tutorial/IC790028.png "Users")
   
   1. Na caixa de texto **Email** , digite o endereço de email de um usuário válido do Active Directory do Azure que você deseja provisionar.
   2. Clique em **Salvar**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Panorama9 ou as APIs fornecidas pelo Panorama9 para provisionar as contas de usuário do AAD.
> 
> 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do AD do Azure que você deseja que usem seu aplicativo.

### <a name="to-assign-users-to-panorama9-perform-the-following-steps"></a>Para atribuir usuários ao Panorama9, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração do aplicativo **Panorama9**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-panorama9-tutorial/IC790029.png "Assign Users")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-panorama9-tutorial/IC767830.png "Yes")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->



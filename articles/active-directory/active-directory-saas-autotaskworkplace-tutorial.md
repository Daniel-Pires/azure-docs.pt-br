---
title: "Tutorial: Integração do Azure Active Directory com o Autotask | Microsoft Docs"
description: "Saiba como usar o Autotask com o Azure Active Directory para habilitar o logon único, o provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: a9a7ff71-c389-4169-aafd-d7a505244797
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/21/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: d22f107be306ad47426b89adb99f07f9d1f6617c
ms.openlocfilehash: 10292e19216e01034a597368e0f2570d49e71610


---

# <a name="tutorial-azure-active-directory-integration-with-autotask"></a>Tutorial: Integração do Azure Active Directory ao Autotask

O objetivo desse tutorial é mostrar como integrar o Autotask ao Azure AD (Azure Active Directory).

A integração do Autotask ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem terá acesso ao Autotask 
- Você pode permitir que os usuários façam logon automaticamente no Autotask (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Autotask, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Autotask


> [!NOTE] 
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Autotask por meio da galeria
2. Configurar e testar o logon único do AD do Azure


## <a name="adding-autotask-from-the-gallery"></a>Adicionando o Autotask por meio da galeria
Para configurar a integração do Autotask ao Azure AD, você precisará adicionar o Autotask da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Autotask por meio da galeria, realize as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 

    ![Active Directory][1]

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
    
    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.
    
    ![Aplicativos][3]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **Autotask**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_01.png)

7. No painel de resultados, selecione **Autotask** e clique em **Concluir** para adicionar o aplicativo.

    ![Seleção do aplicativo na galeria](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do AD do Azure com o Autotask, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Autotask é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Autotask.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD como o valor de **Nome de usuário** no Autotask. 

Para configurar e testar o logon único do Azure AD com o Autotask, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar logon único do Azure AD com Britta Simon.
3. **[Criar um usuário de teste do Autotask](#creating-an-autotask-workplace-test-user)** - para ter um equivalente de Brenda Fernandes no Autotask que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal Clássico e configurará o logon único em seu aplicativo do Autotask.

**Para configurar o logon único do Azure AD com o Autotask, realize as seguintes etapas:**

1. No Portal Clássico do Azure, na página de integração de aplicativos do **Autotask**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
     
    ![Configurar Logon Único][6] 

2. Na página **Como você deseja que os usuários entrem no Autotask**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
    
    ![Configurar Logon Único](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_03.png)

3. Na página de diálogo **Definir Configurações de Aplicativo**, se quiser configurar o aplicativo no **modo iniciado pelo IDP**, execute as seguintes etapas e clique em **Avançar**:

    ![Configurar Logon Único](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_04.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<your-subdomain>.awp.autotask.net/singlesignon/saml/metadata`.

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<your-subdomain>.awp.autotask.net/singlesignon/saml/SSO`.

    c. Clique em **Próximo**.

4. Se quiser configurar o aplicativo no **modo iniciado pelo SP**, na página de diálogo **Definir Configurações do Aplicativo**, clique em **"Mostrar configurações avançadas (opcional)"**, insira a **URL de Logon** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_05.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<your-subdomain>.awp.autotask.net/loginsso`.

    b. Clique em **Próximo**.

    > [!NOTE] 
    > Observe que você precisa atualizar esses valores com a URL de Entrada, o Identificador e a URL de Resposta reais. Para obter esses valores, entre em contato com a [equipe de suporte do Autotask](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm).

4. Na página **Configurar o logon único no Autotask**, clique em **Baixar metadados** e salve o arquivo de metadados no computador:

    ![Configurar Logon Único](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_06.png)

5. Para que o SSO seja configurado para o aplicativo, entre em contato com sua [equipe de suporte do Autotask](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) e forneça os **Metadados** baixados.

6. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
    
    ![Logon Único do AD do Azure][10]

7. Na página **Confirmação de logon único**, clique em **Concluir**.  
    
    ![Logon Único do AD do Azure][11]



### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_09.png)

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_03.png)

4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_04.png)

5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_05.png)

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Próximo**.

6.  Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_06.png)

    a. Na caixa de texto **Nome**, digite **Brenda**.  

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**.

    e. Clique em **Próximo**.

7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_07.png)

8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_08.png)

    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.   



### <a name="creating-an-autotask-test-user"></a>Criar um usuário de teste do Autotask

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Autotask. Trabalhe com a [equipe de suporte do Autotask](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) para adicionar os usuários na plataforma Autotask.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

O objetivo desta seção é permitir que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao Autotask.
    
![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao Autotask, realize as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
    
    ![Atribuir usuário][201]

2. Na lista de aplicativos, escolha **Autotask**.
    
    ![Configurar o logon único](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_50.png)

3. No menu na parte superior, clique em **Usuários**.
    
    ![Atribuir usuário][203]

4. Na lista de usuários, selecione **Brenda Fernandes**.

5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
    
    ![Atribuir usuário][205]

### <a name="testing-single-sign-on"></a>Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.
 
Ao clicar no bloco Autotask no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo Autotask.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO4-->



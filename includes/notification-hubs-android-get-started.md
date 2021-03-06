

> [!NOTE]
> Para concluir o procedimento, você deve ter uma conta do Google com um endereço de email verificado. Para criar uma nova conta do Google, vá para <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.
> 
> 

1. Navegue até o site <a href="http://cloud.google.com/console" target="_blank">Google Cloud Console</a>, entre com as credenciais de sua conta do Google e clique em **Criar Projeto**.
   
       ![](./media/notification-hubs-android-get-started/mobile-services-google-new-project.png)
   
   > [!NOTE]
   > Quando já tiver um projeto existente, você será direcionado para a página <strong>Projetos</strong> depois do logon. Para criar um novo projeto no Painel, expanda <strong>Projeto da API</strong>, clique em <strong>Criar...</strong>, sob <strong>Outros projetos</strong>, insira um nome de projeto e clique em <strong>Criar projeto</strong>.
   > 
   > 
2. Digite um nome de projeto, aceite os termos do serviço e clique em **Criar**. Se solicitado, execute a verificação de SMS e clique em **Criar** novamente.
3. Anote o número do projeto na seção **Projetos**.
   
    No tutorial, posteriormente, você definirá esse valor como a variável PROJECT\_ID no cliente.
4. Na coluna à esquerda, expanda **APIs e autorização**, clique em **APIs**, então role para baixo e clique no botão de alternância para habilitar **Google Cloud Messaging para Android** e aceite os termos de serviço.
   
    ![](./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png)
5. Clique em **Credenciais** e em **Criar nova Chave**
   
       ![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png)
6. Em **Criar uma nova chave**, clique em **Chave do servidor**. Na próxima janela, clique em **Create**.
   
       ![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png)
7. Anote o valor da **CHAVE DA API**.
   
       ![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png)
   
    Você usará esse valor de chave de API para habilitar o Azure para autenticar com o GCM e enviar notificações por push em nome do seu aplicativo.

<!---HONumber=Oct15_HO3-->
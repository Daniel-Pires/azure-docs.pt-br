
### <a name="update-app"></a>Atualizar o aplicativo para chamar a API personalizada
1. Adicionaremos um botão rotulado como "Tudo Completo" ao lado do botão existente e moveremos ambos os botões abaixo de uma linha. No Android Studio, abra o arquivo *res\\layout\\activity\_to\_do.xml* do projeto quickstart, localize o elemento **LinearLayout** que contém o elemento **Botão** chamado `buttonAddToDo`. Copie o **LinearLayout** e cole-o logo depois do original. Exclua o elemento **Botão** do primeiro **LinearLayout**.
2. No segundo **LinearLayout**, exclua o elemento **EditText** e adicione o código a seguir imediatamente após o elemento **Botão** existente:
   
        <Button
            android:id="@+id/buttonCompleteItem"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="completeItem"
            android:text="@string/complete_button_text" />
   
    Isso adiciona um novo botão à página, em uma linha separada, ao lado do botão existente.
3. O segundo **LinearLayout** agora tem esta aparência:
   
         <LinearLayout
            android:layout_width="match_parent" 
            android:layout_height="wrap_content" 
            android:background="#71BCFA"
            android:padding="6dip"  >
            <Button
                android:id="@+id/buttonAddToDo"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:onClick="addItem"
                android:text="@string/add_button_text" />
            <Button
                android:id="@+id/buttonCompleteItem"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:onClick="completeItem"
                android:text="@string/complete_button_text" />
        </LinearLayout>
4. Abra o arquivo res\\values\\string.xml e adicione a seguinte linha de código:
   
        <string name="complete_button_text">Complete All</string>
5. No Gerenciador de Projetos, clique com o botão direito do mouse na pasta *src* (`com.example.{your projects name}`), escolha **Novo** depois a **Classe**. Na caixa de diálogo, digite **MarkAllResult** no campo do nome da classe, escolha OK e substitua a definição da classe resultante pelo seguinte código:
   
        import com.google.gson.annotations.SerializedName;
   
        public class MarkAllResult {
            @SerializedName("count")
            public int mCount;
   
            public int getCount() {
                return mCount;
            }
   
            public void setCount(int count) {
                    this.mCount = count;
            }
        }
   
    Essa classe é usada para armazenar o valor da contagem de linhas retornado pela API personalizada.
6. Localize o método **refreshItemsFromTable** no arquivo **ToDoActivity.java**, e certifique-se de que a primeira linha do código no bloco `try` tem esta aparência:
   
        final MobileServiceList<ToDoItem> result = mToDoTable.where().field("complete").eq(false).execute().get();
   
    Isso filtra os itens de forma que os itens concluídos não sejam retornados pela consulta.
7. Verifique se **ToDoActivity.java** contém a instrução as importações a seguir no início do arquivo:
   
        import com.google.common.util.concurrent.FutureCallback;
        import com.google.common.util.concurrent.Futures;
        import com.google.common.util.concurrent.ListenableFuture;
8. No arquivo **ToDoActivity.java**, adicione o seguinte método:
   
    public void completeItem(View view) {
   
        ListenableFuture<MarkAllResult> result = mClient.invokeApi( "completeAll2", MarkAllResult.class ); 
   
            Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
                @Override
                public void onFailure(Throwable exc) {
                    createAndShowDialog((Exception) exc, "Error");
                }
   
                @Override
                public void onSuccess(MarkAllResult result) {
                    createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
                    refreshItemsFromTable();    
                }
            });
        }
   
    Esse método identifica o evento **Click** do novo botão. O método **invokeApi** é chamado no cliente, que envia uma solicitação POST para a nova API personalizada. O resultado retornado pela API personalizada é exibido em uma caixa de diálogo de mensagem, se houver erros.

### Testar o aplicativo
1. No menu **Executar**, clique em **Executar aplicativo** para iniciar o projeto no emulador do Android, ou em um dispositivo Android conectado.
   
    Isso executa seu aplicativo criado com o SDK do Android que usa a biblioteca cliente para enviar uma consulta que retorna os itens de seu serviço móvel.
2. No aplicativo, digite um texto em **Inserir um TodoItem** e clique em **Adicionar**.
3. Repita a etapa anterior até que tenha incluído vários itens pendentes na lista.
4. Clique no botão **Concluir Tudo**.
   
      ![](./media/mobile-services-android-call-custom-api/mobile-custom-api-android-completed.png)
   
    É exibida uma caixa de diálogo de mensagem que indica o número de itens marcados como concluídos, e a consulta filtrada é executada novamente, o que limpa todos os itens da lista.

<!---HONumber=Oct15_HO3-->
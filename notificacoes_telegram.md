### Integração de Notificações Telegram com MikroTik

Este documento oferece um guia passo a passo para criar um bot no Telegram e integrá-lo com scripts no MikroTik para enviar notificações automáticas.

------

### Passo 1: Criar um Bot no Telegram

1. **Acessar o BotFather**:
   - Vá para [BotFather no Telegram](https://telegram.me/BotFather).
   - Clique em 'Start' ou digite `/start`.
2. **Criar um novo bot**:
   - Envie o comando `/newbot`.
   - Escolha um nome para o seu bot. Por exemplo, `mk_home_victor`.
   - Escolha um nome de usuário que deve terminar em ‘bot’. Por exemplo, `mk_home_victor_bot`.
3. **Guardar o Token do Bot**:
   - O BotFather fornecerá um token. Este é seu `SeuBotId` e é crucial para enviar mensagens através do bot.

------

### Passo 2: Obter o Chat ID do Usuário

1. **Acessar o UserInfoBot**:
   - Vá para [UserInfoBot no Telegram](https://telegram.me/userinfobot).
   - Clique em 'Start' ou digite `/start`.
2. **Obter o Chat ID**:
   - O bot retornará o seu `Chat ID`, que será utilizado para enviar mensagens para o seu usuário específico.

------

### Passo 3: Criar o Script de Envio no MikroTik

1. **Criar o Script `telegram-send-function`**:
   - No Winbox: Vá para `System > Scripts` e crie um novo script chamado `telegram-send-function`.
   - Insira o seguinte código:

```
:global TelegramSend do={
  :local tgBotToken "<SeuToken>"
  :local tgGroupId "<SeuChatId>"
  :local tgMessage "$1"
  :local tgUrl ("https://api.telegram.org/bot".$tgBotToken."/sendMessage?chat_id=".$tgGroupId."&text=".$tgMessage)
  /tool fetch url="$tgUrl" keep-result=no
}
```

------

### Passo 4: Usar o Script em Outro Script

1. Exemplo de Uso:
   - Ative uma rota específica e envie uma notificação pelo Telegram:

```
# Esta linha é apenas um exemplo para ativar uma rota específica
/ip route set [/ip route find comment="Link1"] disabled=no

# Aqui é como o script para enviar a mensagem pelo Telegram deve ser chamado
/system script run telegram-send-function {
  :global TelegramSend
  $TelegramSend "Link 1 está online"
}
```

Este script, ao ser executado, ativa uma rota e envia uma mensagem de notificação para o Telegram indicando que a conexão principal está online.


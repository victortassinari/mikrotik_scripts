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

1. Criar o Script `sendTelegramMessage`:
   - Vá para `System > Scripts` no MikroTik.
   - Crie um novo script chamado `sendTelegramMessage`.
   - Insira o seguinte código no script:

```
:global message
:local messageText $message
/tool fetch url="https://api.telegram.org/bot<SeuBotId>/sendMessage" http-method=post http-data="chat_id=<SeuChatId>&text=$messageText" http-header-field="Content-Type:application/x-www-form-urlencoded"
```

- Substitua `<SeuBotId>` pelo token do seu bot.
- Substitua `<SeuChatId>` pelo seu Chat ID.

------

### Passo 4: Usar o Script em Outro Script

1. Exemplo de Uso:
   - Crie ou edite um script existente, por exemplo, `linkup`.
   - Adicione a seguinte lógica para ativar o envio de notificações:

```
# Esta linha é apenas um exemplo para ativar uma rota específica
/ip route set [/ip route find comment="Link1"] disabled=no

# Aqui é como o script para enviar a mensagem pelo Telegram deve ser chamado
:global message "Link principal CityNet ficou online"
/system script run sendTelegramMessage
```

Este script, ao ser executado, ativa uma rota e envia uma mensagem de notificação para o Telegram indicando que a conexão principal está online.

------

Este documento oferece uma maneira integrada de usar notificações do Telegram com scripts MikroTik, melhorando a gestão de eventos e monitoramento de sua rede.

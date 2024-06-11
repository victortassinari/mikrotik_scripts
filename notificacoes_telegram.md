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
  $TelegramSend "Link Principal CityNet Online"
}
```

Este script, ao ser executado, ativa uma rota e envia uma mensagem de notificação para o Telegram indicando que a conexão principal está online.

### Passo 5: Notificação no Telegram na Inicialização do Sistema

Este passo adicional envolve configurar uma notificação automática para ser enviada via Telegram sempre que o RouterOS (MikroTik) for reiniciado, garantindo que você seja notificado sobre a reinicialização do sistema e o status da conexão à internet.

#### Criar o Script "initialize-message"

1. Acessar o Winbox

   :

   - Vá para `System > Scripts`.

2. Criar um novo script

   :

   - Clique em `+` para adicionar um novo script e nomeie-o como `initialize-message`.
   - Insira o seguinte conteúdo no script:

```
:global message "Sistema reiniciado"

:local internetActive false
:local attempt 0
:while ($internetActive = false && $attempt < 30) do={
    :set attempt ($attempt + 1)
    :log info "Tentativa de verificação de conexão #$attempt"
    :local pingResult [/ping 8.8.8.8 count=1]
    if ($pingResult > 0) do={
        :set internetActive true
        :log info "Conexão com a internet estabelecida, enviando mensagem."
        /system script run telegram-send-function {
            :global TelegramSend
            $TelegramSend "RB inicializada"
        }
    } else={
        :log info "Conexão com a internet falhou, nova tentativa em 10 segundos."
        :delay 10s
    }
}
```

#### Criar uma Tarefa no Scheduler

1. Acessar o Scheduler

   :

   - No Winbox, vá para `System > Scheduler`.

2. Adicionar uma nova tarefa

   :

   - Clique em `+` para adicionar uma nova tarefa.
   - Nomeie a tarefa como `startup`.
   - No campo `Start Time`, escolha `startup`.
   - No campo `On Event`, insira o seguinte comando para executar o script criado:

```
/system script run initialize-message
```

Este procedimento garantirá que a mensagem "RB inicializada" seja enviada para seu Telegram sempre que o sistema for reiniciado, após verificar e confirmar que a conexão à internet está ativa.


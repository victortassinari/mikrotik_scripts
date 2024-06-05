### Tutorial: Configuração de Fast-Track para Conexões no Mikrotik

**Objetivo:** Configurar o fast-tracking no Mikrotik para conexões estabelecidas e relacionadas, visando melhorar a performance da rede especialmente quando as conexões não estão ultrapassando 100 Mbps.

#### Passo 1: Acessar o Firewall do Mikrotik

Primeiro, acesse o menu de firewall no seu Mikrotik via terminal ou através da interface WinBox.

#### Passo 2: Configurar Fast-Track para Conexões Estabelecidas e Relacionadas

Adicione a regra de fast-track para processar conexões que já estão estabelecidas ou são relacionadas a outras já existentes, o que ajuda a acelerar o tráfego de dados nessas condições:

```
/ip firewall filter add chain=forward action=fasttrack-connection connection-state=established,related \
  comment="fasttrack established/related"
```

#### Passo 3: Configurar Regra de Aceitação para Conexões Estabelecidas e Relacionadas

Após configurar o fast-track, é importante garantir que essas conexões também sejam aceitas pelo firewall. Isso permite que o tráfego continue fluindo normalmente após ser processado pelo fast-track:

```
/ip firewall filter add chain=forward action=accept connection-state=established,related \
  comment="accept established/related"
```

### Conclusão

Com essas configurações, o Mikrotik estará apto a lidar de maneira mais eficiente com o tráfego de conexões já estabelecidas e suas relacionadas, facilitando um melhor desempenho da rede, principalmente em cenários onde a transmissão de dados não ultrapassa 100 Mbps. Isso é especialmente útil em ambientes com alto volume de dados ou quando a rede está sob carga significativa.

### Tutorial: Detecção e Bloqueio de Varreduras de Portas no Mikrotik

**Objetivo:** Configurar o Mikrotik para detectar atividades suspeitas de varredura de portas e bloquear essas tentativas, melhorando a segurança da rede.

#### Passo 1: Acessar o Firewall do Mikrotik

Primeiro, certifique-se de estar no ambiente do firewall do Mikrotik, acessível via terminal ou interface WinBox.

#### Passo 2: Adicionar Regra para Detecção de Varredura de Portas

Configure uma regra para detectar varreduras de portas. Essa regra identifica padrões suspeitos de tentativas de conexão e adiciona o endereço IP do infrator a uma lista de endereços chamada `PortScan`.

```
/ip firewall filter
add action=add-src-to-address-list address-list=PortScan address-list-timeout=20d chain=input comment="PortScan" protocol=tcp psd=21,3s,3,1
```

- **psd=21,3s,3,1**: Detecta 21 tentativas de conexão TCP (psd: packet sequence detection) que ocorrem dentro de 3 segundos, com uma diferença mínima de 3 pacotes, indicando uma possível varredura de porta.

#### Passo 3: Configurar Regra para Bloquear IPs Detectados

Após a detecção, a próxima regra bloqueia todo o tráfego de entrada desses endereços IP, efetivamente protegendo sua rede contra esses atacantes.

```
/ip firewall filter 
add action=drop chain=input src-address-list=PortScan
```

### Conclusão

Com essas configurações aplicadas, seu Mikrotik agora possui um método eficaz para detectar e bloquear tentativas de varredura de portas, reforçando a segurança contra ataques comuns que exploram portas abertas ou vulneráveis na rede.

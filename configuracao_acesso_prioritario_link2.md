### Tutorial: Configuração de Cliente Específico para Uso de Link Dedicado no Mikrotik

**Objetivo:** Configurar o cliente com IP `10.0.0.98` para sempre utilizar o Link 2, mas com fallback para o Link 1 se o Link 2 falhar.

#### Passo 1: Criar uma Tabela de Roteamento

Primeiro, crie uma tabela de roteamento específica para este cliente:

```
/routing table add fib name=myClient
```

#### Passo 2: Criar uma Regra de Roteamento

Configure uma regra de roteamento que direciona todo o tráfego do IP `10.0.0.98` para usar a tabela de roteamento criada:

```
/routing rule add action=lookup table=myClient src-address=10.0.0.98
```

**Nota:** Você pode optar por `action=lookup-only-in-table` para que, se o Link 2 falhar, o cliente não tenha acesso à internet. Porém, com `action=lookup`, o cliente ainda poderá navegar utilizando o Link 1 caso o Link 2 falhe.

#### Passo 3: Criar uma Rota para essa Regra de Roteamento

Finalmente, crie uma rota dentro da tabela de roteamento `myClient` que direcione todo o tráfego para o gateway do Link 2:

```
/ip route add distance=1 routing-table=myClient dst-address=0.0.0.0/0 gateway=<ip>
```

- Substitua `<ip>` pelo endereço que aparece na coluna "Network" na página "IP -> Address". Este será o IP do gateway para o Link 2. Por exemplo, se o IP do seu ISP for `192.168.144.1`, você usaria:

```
/ip route add distance=1 routing-table=myClient dst-address=0.0.0.0/0 gateway=192.168.144.1
```

### Conclusão

Após configurar esses passos, o cliente com o IP `10.0.0.98` deverá sempre utilizar o Link 2 para acesso à internet, com a possibilidade de usar o Link 1 em caso de falha do Link 2. Essa configuração proporciona uma conexão mais resiliente e dedicada para esse cliente específico.

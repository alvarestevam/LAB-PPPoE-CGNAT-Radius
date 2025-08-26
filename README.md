# LAB-CISCO PPPoE + CGNAT + RADIUS + BGP

## 🎯 Objetivo
Este laboratório simula a rede de um **ISP** com as seguintes funcionalidades:

- **Clientes PPPoE autenticados via FreeRADIUS**.
- Separação de **clientes residenciais (CGNAT)** e **clientes dedicados (IP público)**.
- **BGP** entre o roteador de borda e a operadora.
- **NAT** centralizado para clientes privados.
- Servidor **AAA (RADIUS)** para autenticação PPPoE.

---

## 🗺️ Topologia

### Componentes
- **OPERADORA** → Roteador simulando o upstream (AS 200).  
- **BORDA-BGP** → Roteador de borda do provedor (AS 100).  
- **BNG (PPPoE)** → Broadband Network Gateway (concentrador de assinantes).  
- **CGNAT** → Roteador para tradução de endereços privados.  
- **AAA-1** → Servidor FreeRADIUS (autenticação PPPoE).  
- **Clientes Privados (CL1 e CL2)** → Recebem IP da faixa privada (10.10.10.0/24).  
- **Cliente Público (CL3)** → Recebe IP dedicado (45.100.100.0/24).  
- **OpenWrt** → Simulam CPEs dos assinantes.  
- **DumbSwitch** → Distribuição de acesso aos clientes.



## 🔄 Fluxo de Tráfego

1. Autenticação PPPoE

O cliente conecta no OpenWrt → inicia sessão PPPoE.

O BNG recebe a requisição e consulta o AAA-1 (RADIUS).

O RADIUS valida usuário/senha e entrega atributos (pool privado ou IP fixo).

A sessão PPPoE é estabelecida com o IP atribuído.


2. Clientes Privados (CGNAT)

Cliente → OpenWrt → PPPoE → BNG → CGNAT → BORDA-BGP → OPERADORA

Recebem IP do pool 10.10.10.100 - 10.10.10.200.

O BNG aplica route-map DIRECIONAR_PRIVADO para enviar tráfego ao CGNAT.

O CGNAT aplica NAT overload, convertendo IP privado em IP público.

Tráfego segue para a BORDA, que envia via BGP para a OPERADORA.


3. Cliente Público (IP Dedicado)

Cliente → OpenWrt → PPPoE → BNG → BORDA-BGP → OPERADORA

Recebe IP fixo 45.100.100.100 (via RADIUS).

Tráfego segue diretamente para a BORDA, sem passar pelo CGNAT.

O cliente navega com IP público próprio.


4. Comunicação com a Internet

A OPERADORA (AS 200) anuncia 8.8.8.8/32.

O BORDA-BGP (AS 100) troca rotas via BGP.

O BNG e CGNAT usam a rota default para garantir saída à Internet.


## ✅ Testes

PPPoE autenticando com usuários do RADIUS.

Clientes privados saindo via CGNAT (com IP traduzido).

Cliente público navegando com IP dedicado.

Ping para 8.8.8.8 validado em todos os clientes.


## 📌 Conclusão

Este LAB demonstra o funcionamento de um ambiente típico de ISP:

Autenticação centralizada em RADIUS.

Separação de clientes residenciais (CGNAT) e empresariais (IP dedicado).

BGP na borda para integração com a operadora.

PPPoE como protocolo de acesso dos assinantes.

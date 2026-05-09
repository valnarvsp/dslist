# Prompt 01 - Recebimento do Pedido (Importação)

## Explicação do Fluxo de Recebimento do Pedido

### Como tudo começa

O fluxo se inicia quando um **comprador** realiza uma compra em um marketplace (Amazon, Mercado Livre, Shopee, Magalu, etc.). Nesse momento, o marketplace registra o pedido internamente com todos os dados da transação.

### Importação pelo Anymarket

O Anymarket atua como um **hub integrador** e faz a ponte entre o marketplace e o sistema do seller (ERP). A importação do pedido acontece da seguinte forma:

1. **Polling / Webhook**: O Anymarket consulta periodicamente a API do marketplace (polling) ou recebe notificações em tempo real (webhook) sobre novos pedidos.
2. **Leitura dos dados**: Ao identificar um novo pedido, o Anymarket consome a API do marketplace para obter todos os detalhes.
3. **Persistência**: O pedido é salvo na base do Anymarket com status inicial **NEW** ou **PENDING**.

### Dados importados

Durante a importação, os seguintes dados são capturados:

| Dado                     | Descrição                                                  |
|--------------------------|------------------------------------------------------------|
| **ID do pedido**         | Identificador único no marketplace                         |
| **Itens**                | SKU, quantidade, preço unitário, variações                 |
| **Valores**              | Subtotal, frete, descontos, total do pedido                |
| **Endereço de entrega**  | CEP, rua, número, complemento, bairro, cidade, estado      |
| **Dados do comprador**   | Nome, CPF/CNPJ, e-mail, telefone                           |
| **Forma de pagamento**   | Tipo de pagamento utilizado no marketplace                  |
| **Prazo de envio**       | Data limite para despacho definida pelo marketplace         |

### Status nesta etapa

- **NEW**: Pedido recém-importado, ainda sem confirmação de pagamento.
- **PENDING**: Pedido aguardando aprovação do pagamento pelo marketplace.

> O pedido permanece nesse status até que o marketplace confirme o pagamento, momento em que avança para a próxima etapa (**PAID_WAITING_SHIP**).

### Fluxo visual

```
┌─────────────┐    Compra     ┌─────────────────┐
│  Comprador  │ ────────────► │   Marketplace   │
└─────────────┘               └────────┬────────┘
                                       │
                              Notificação / Polling
                                       │
                                       ▼
                              ┌─────────────────┐
                              │   Anymarket     │
                              │  (Importação)   │
                              └────────┬────────┘
                                       │
                              Salva pedido com
                              status NEW/PENDING
                                       │
                                       ▼
                              ┌─────────────────┐
                              │  Pedido salvo   │
                              │  no Anymarket   │
                              └─────────────────┘
```

### Possíveis erros na importação

| Erro                              | Causa                                                       |
|-----------------------------------|-------------------------------------------------------------|
| **Falha de comunicação**          | API do marketplace indisponível ou com timeout               |
| **SKU não mapeado**              | Produto do marketplace não vinculado a um SKU no Anymarket   |
| **Dados incompletos**            | Endereço ou dados do comprador faltando no marketplace       |
| **Pedido duplicado**             | Falha no controle de idempotência, pedido importado 2x       |
| **Conta desconectada**           | Token de autenticação expirado ou revogado                   |

### Frequência de sincronização

- A frequência varia por marketplace, mas geralmente o Anymarket consulta novos pedidos a cada **1 a 5 minutos**.
- Marketplaces que suportam **webhooks** permitem importação quase em tempo real.
- Em caso de falha, o Anymarket possui mecanismos de **retry automático** para garantir que nenhum pedido seja perdido.

---

## Explicação do Fluxo de Recebimento do Pedido

### Como tudo começa

O fluxo se inicia quando um **comprador** realiza uma compra em um marketplace (Amazon, Mercado Livre, Shopee, Magalu, etc.). Nesse momento, o marketplace registra o pedido internamente com todos os dados da transação.

### Importação pelo Anymarket

O Anymarket atua como um **hub integrador** e faz a ponte entre o marketplace e o sistema do seller (ERP). A importação do pedido acontece da seguinte forma:

1. **Polling / Webhook**: O Anymarket consulta periodicamente a API do marketplace (polling) ou recebe notificações em tempo real (webhook) sobre novos pedidos.
2. **Leitura dos dados**: Ao identificar um novo pedido, o Anymarket consome a API do marketplace para obter todos os detalhes.
3. **Persistência**: O pedido é salvo na base do Anymarket com status inicial **NEW** ou **PENDING**.

### Dados importados

Durante a importação, os seguintes dados são capturados:

| Dado                     | Descrição                                                  |
|--------------------------|------------------------------------------------------------|
| **ID do pedido**         | Identificador único no marketplace                         |
| **Itens**                | SKU, quantidade, preço unitário, variações                 |
| **Valores**              | Subtotal, frete, descontos, total do pedido                |
| **Endereço de entrega**  | CEP, rua, número, complemento, bairro, cidade, estado      |
| **Dados do comprador**   | Nome, CPF/CNPJ, e-mail, telefone                           |
| **Forma de pagamento**   | Tipo de pagamento utilizado no marketplace                  |
| **Prazo de envio**       | Data limite para despacho definida pelo marketplace         |

### Status nesta etapa

- **NEW**: Pedido recém-importado, ainda sem confirmação de pagamento.
- **PENDING**: Pedido aguardando aprovação do pagamento pelo marketplace.

> O pedido permanece nesse status até que o marketplace confirme o pagamento, momento em que avança para a próxima etapa (**PAID_WAITING_SHIP**).

### Fluxo visual

```
┌─────────────┐    Compra     ┌─────────────────┐
│  Comprador  │ ────────────► │   Marketplace   │
└─────────────┘               └────────┬────────┘
                                       │
                              Notificação / Polling
                                       │
                                       ▼
                              ┌─────────────────┐
                              │   Anymarket     │
                              │  (Importação)   │
                              └────────┬────────┘
                                       │
                              Salva pedido com
                              status NEW/PENDING
                                       │
                                       ▼
                              ┌─────────────────┐
                              │  Pedido salvo   │
                              │  no Anymarket   │
                              └─────────────────┘
```

### Possíveis erros na importação

| Erro                              | Causa                                                       |
|-----------------------------------|-------------------------------------------------------------|
| **Falha de comunicação**          | API do marketplace indisponível ou com timeout               |
| **SKU não mapeado**              | Produto do marketplace não vinculado a um SKU no Anymarket   |
| **Dados incompletos**            | Endereço ou dados do comprador faltando no marketplace       |
| **Pedido duplicado**             | Falha no controle de idempotência, pedido importado 2x       |
| **Conta desconectada**           | Token de autenticação expirado ou revogado                   |

### Frequência de sincronização

- A frequência varia por marketplace, mas geralmente o Anymarket consulta novos pedidos a cada **1 a 5 minutos**.
- Marketplaces que suportam **webhooks** permitem importação quase em tempo real.
- Em caso de falha, o Anymarket possui mecanismos de **retry automático** para garantir que nenhum pedido seja perdido.

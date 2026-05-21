# Arquitetura

## Padrão: API Gateway + Trusted Layer

Todo tráfego externo passa pelo **Gateway**, que valida o JWT antes de rotear para os serviços internos. Os serviços nunca são expostos diretamente à internet — eles confiam nos headers injetados pelo Gateway.

```mermaid
graph TD
    Client([Cliente])

    subgraph internet["Internet"]
        Client
    end

    subgraph public["Camada Publica"]
        GW[Gateway :8080]
    end

    subgraph trusted["Camada Confiavel - Trusted Layer"]
        Auth[Auth Service]
        Account[Account Service]
        Order[Order Service]
        Product[Product Service]
        Exchange[Exchange Service]
        DB[(PostgreSQL)]
    end

    Client -->|HTTPS| GW
    GW -->|/auth/**| Auth
    GW -->|/accounts/**| Account
    GW -->|/orders/**| Order

    Auth -->|valida conta| Account
    Order -->|Feign: preco produto| Product
    Order -->|Feign: taxa cambio| Exchange

    Account --> DB
    Order --> DB
    Product --> DB
```

---

## Fluxo de Autenticação

O Gateway intercepta todas as requisições protegidas, extrai o cookie JWT e consulta o Auth Service para obter o `id-account`. Esse header é então injetado na requisição antes de encaminhá-la ao serviço destino.

```mermaid
sequenceDiagram
    actor U as Usuario
    participant GW as Gateway
    participant Auth as Auth Service
    participant SVC as Servico ex Order

    U->>GW: POST /auth/login
    GW->>Auth: POST /auth/login
    Auth-->>GW: Set-Cookie jwt_token
    GW-->>U: Cookie JWT

    U->>GW: GET /orders + cookie
    GW->>Auth: POST /auth/solve token
    Auth-->>GW: idAccount uuid
    GW->>SVC: GET /orders + Header id-account
    SVC-->>GW: 200 OK
    GW-->>U: 200 OK
```

---

## Fluxo de Criação de Pedido

```mermaid
sequenceDiagram
    actor U as Usuario
    participant GW as Gateway
    participant OS as Order Service
    participant PS as Product Service
    participant DB as PostgreSQL

    U->>GW: POST /orders com cookie JWT
    GW->>GW: Valida JWT e extrai id-account
    GW->>OS: POST /orders + id-account header

    loop para cada item
        OS->>PS: GET /products/id
        PS-->>OS: price 10.12
    end

    OS->>OS: Calcula total em USD
    OS->>DB: INSERT orders e order_items
    DB-->>OS: OK
    OS-->>GW: 201 Created com id e total
    GW-->>U: 201 Created
```

---

## Fluxo de Consulta com Conversão de Moeda

```mermaid
sequenceDiagram
    actor U as Usuario
    participant GW as Gateway
    participant OS as Order Service
    participant ES as Exchange Service

    U->>GW: GET /orders/id?currency=BRL
    GW->>OS: GET /orders/id?currency=BRL

    OS->>OS: Busca pedido no banco em USD
    OS->>ES: GET /exchange?from=USD&to=BRL
    alt Exchange disponivel
        ES-->>OS: rate 6.0
        OS->>OS: total x 6.0
        OS-->>GW: 200 currency BRL total 121.44
    else Exchange indisponivel
        OS-->>GW: 200 currency USD total 20.24 fallback
    end
    GW-->>U: 200 OK
```

---

## Topologia de Rede (Docker Compose)

```mermaid
graph LR
    subgraph host["Host"]
        P8080[porta 8080]
        P8085[porta 8085]
    end

    subgraph appnet["app-net - rede interna Docker"]
        GW[gateway]
        Auth[auth]
        Account[account]
        Order[order]
        Product[product WireMock]
        Exchange[exchange WireMock]
        DB[postgres]
    end

    P8080 --> GW
    P8085 --> Order
    GW --> Auth
    GW --> Account
    GW --> Order
    Order --> Product
    Order --> Exchange
    Order --> DB
    Auth --> Account
    Account --> DB
```

Os serviços se comunicam pelo hostname interno (`order`, `account`, etc.). Apenas o Gateway e o Order service têm portas expostas no host.

---

## Schemas do Banco de Dados

Cada serviço possui seu próprio schema, garantindo isolamento de dados:

| Schema | Serviço | Tabelas |
|--------|---------|---------|
| `accounts` | Account Service | `accounts` |
| `orders` | Order Service | `orders`, `order_items` |
| `products` | Product Service | `products` *(em breve)* |

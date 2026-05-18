# Endpoints

Todos os endpoints exigem autenticação. O header `id-account` é injetado automaticamente pelo Gateway após validar o cookie JWT — não é enviado pelo cliente diretamente.

---

## POST /orders

Cria um pedido para a conta autenticada. O serviço consulta o Product Service para obter o preço de cada item e armazena o total em USD.

```bash
curl -X POST http://localhost:8080/orders \
  -H "Content-Type: application/json" \
  -b "__store_jwt_token=<seu-token>" \
  -d '{
    "items": [
      { "idProduct": "0195abfb-7074-73a9-9d26-b4b9fbaab0a8", "quantity": 2 }
    ]
  }'
```

**Respostas**

| Status | Quando |
|--------|--------|
| `201 Created` | Pedido criado com sucesso |
| `400 Bad Request` | Produto não existe |
| `401 Unauthorized` | Sem autenticação válida |
| `502 Bad Gateway` | Product Service indisponível |

**Body 201**

```json
{
  "id": "d4e5f6a7-0000-0000-0000-000000000001",
  "date": "2025-09-01T12:30:00",
  "items": [
    {
      "id": "item-uuid-0001",
      "product": { "id": "0195abfb-7074-73a9-9d26-b4b9fbaab0a8" },
      "quantity": 2,
      "total": 20.24
    }
  ],
  "total": 20.24
}
```

!!! note
    O campo `currency` não aparece na criação — valores são sempre salvos em USD.

---

## GET /orders

Lista todos os pedidos da conta autenticada, em ordem decrescente de data. Retorna apenas o resumo (sem itens detalhados).

```bash
curl http://localhost:8080/orders \
  -b "__store_jwt_token=<seu-token>"
```

**Respostas**

| Status | Quando |
|--------|--------|
| `200 OK` | Lista retornada (pode ser vazia `[]`) |
| `401 Unauthorized` | Sem autenticação válida |

**Body 200**

```json
[
  {
    "id": "d4e5f6a7-0000-0000-0000-000000000001",
    "date": "2025-09-01T12:30:00",
    "total": 20.24
  }
]
```

!!! info "Isolamento"
    Cada conta vê apenas seus próprios pedidos. O filtro é feito internamente com o `id-account` extraído do JWT — não é possível acessar pedidos de outras contas.

---

## GET /orders/{id}

Retorna os detalhes completos de um pedido. Aceita o parâmetro opcional `currency` para converter os totais.

=== "Sem conversão (USD)"

    ```bash
    curl http://localhost:8080/orders/d4e5f6a7-0000-0000-0000-000000000001 \
      -b "__store_jwt_token=<seu-token>"
    ```

=== "Com conversão para BRL"

    ```bash
    curl "http://localhost:8080/orders/d4e5f6a7-0000-0000-0000-000000000001?currency=BRL" \
      -b "__store_jwt_token=<seu-token>"
    ```

**Respostas**

| Status | Quando |
|--------|--------|
| `200 OK` | Pedido encontrado |
| `401 Unauthorized` | Sem autenticação válida |
| `404 Not Found` | Pedido não existe ou pertence a outra conta |
| `422 Unprocessable Entity` | Moeda não suportada pelo Exchange Service |

**Body 200 com `?currency=BRL`**

```json
{
  "id": "d4e5f6a7-0000-0000-0000-000000000001",
  "date": "2025-09-01T12:30:00",
  "currency": "BRL",
  "items": [
    {
      "id": "item-uuid-0001",
      "product": { "id": "0195abfb-7074-73a9-9d26-b4b9fbaab0a8" },
      "quantity": 2,
      "total": 121.44
    }
  ],
  "total": 121.44
}
```

!!! warning "Exchange indisponível"
    Se o Exchange Service estiver fora do ar, o serviço responde `200` com os valores em USD — sem erro para o cliente. Veja [Bottlenecks](../bottlenecks.md).

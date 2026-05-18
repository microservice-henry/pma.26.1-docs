# Exchange API

!!! warning "Em desenvolvimento"
    O Exchange Service será implementado em uma próxima entrega. Esta seção será atualizada com endpoints, fonte de dados e instruções de execução.

## Planejado

O Exchange Service será responsável por:

- Consulta de taxas de câmbio entre moedas (ex: USD → BRL)
- Exposição do endpoint `GET /exchange?from=USD&to=BRL` consumido pela Order API

Durante o desenvolvimento atual da Order API, o Exchange Service é **simulado por WireMock** — veja [Como Rodar](../order/run.md) para detalhes dos mocks.

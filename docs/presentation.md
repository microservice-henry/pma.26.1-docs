# Apresentação Individual

**Aluno:** Henry Idesis  
**Disciplina:** Plataformas, Microsserviços, DevOps e APIs — Insper 2026.1

---

## Vídeo de Apresentação

<iframe width="100%" height="450"
  src="https://www.youtube-nocookie.com/embed/OwebMKRjKwM"
  title="Order API — Apresentação Individual — Henry Idesis"
  frameborder="0"
  allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
  allowfullscreen>
</iframe>

[Assistir no YouTube](https://www.youtube.com/watch?v=OwebMKRjKwM){ .md-button }

---

## O que foi apresentado

### 1. Order API — Endpoint principal

Demonstração ao vivo do `POST /orders` e `GET /orders/{id}?currency=BRL` passando pelo Gateway com autenticação JWT.

### 2. Bottleneck — Resiliência (Fallback)

O `OrderService` implementa fallback gracioso quando o Exchange Service está indisponível: em vez de retornar 500, o pedido é devolvido em USD com taxa 1. Demonstrado ao vivo derrubando o container do Exchange e repetindo a requisição.

```java
} catch (FeignException e) {
    // Fallback to storage currency (USD) when exchange service is unavailable.
    return BigDecimal.ONE;
}
```

### 3. Bottleneck — Observabilidade

Spring Boot Actuator configurado no Gateway para expor métricas no formato Prometheus (`/gateway/actuator/prometheus`). Prometheus coleta a cada 1 segundo e o Grafana exibe em tempo real o volume de requisições por rota.

---

## Repositórios demonstrados

| Serviço | Repositório |
|---------|-------------|
| Order Service | [pma.261.order-service](https://github.com/microservice-henry/pma.261.order-service) |
| Gateway Service | [pma.261.gateway-service](https://github.com/microservice-henry/pma.261.gateway-service) |
| Documentação | [pma.26.1-docs](https://github.com/microservice-henry/pma.26.1-docs) |

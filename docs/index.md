# Plataforma de Microsserviços

**Aluno:** Henry Idesis  
**Entrega:** Individual — PMA 26.1  
**Disciplina:** Plataformas, Microsserviços, DevOps e APIs — Insper 2026.1

---

## Sobre o Projeto

Plataforma de e-commerce baseada em arquitetura de microsserviços, onde usuários autenticados podem criar e consultar pedidos com suporte a múltiplas moedas. Esta entrega cobre o desenvolvimento da **Order API** e sua integração com o Gateway.

---

## Repositórios

| Serviço | Repositório | Status |
|---------|-------------|--------|
| Plataforma (raiz) | [pma.26.1](https://github.com/microservice-henry/pma.26.1) | ✅ |
| Order (contrato) | [pma.261.order](https://github.com/microservice-henry/pma.261.order) | ✅ |
| Order Service | [pma.261.order-service](https://github.com/microservice-henry/pma.261.order-service) | ✅ |
| Gateway Service | [pma.261.gateway-service](https://github.com/microservice-henry/pma.261.gateway-service) | ✅ |
| Account Service | [pma.261.account-service](https://github.com/microservice-henry/pma.26.1.account-service) | ✅ |
| Auth Service | [pma.261.auth-service](https://github.com/microservice-henry/pma.261.auth-service) | ✅ |

---

## Stack Tecnológico

| Camada | Tecnologia |
|--------|-----------|
| Linguagem | Java 25 |
| Framework | Spring Boot 4.0.3 |
| Comunicação entre serviços | Spring Cloud OpenFeign |
| Banco de dados | PostgreSQL 17 |
| Migrations | Flyway |
| Mocks de dependências | WireMock 3.3.1 |
| Containerização | Docker / Docker Compose |
| CI/CD | Jenkins |

---

## Status da Entrega

| Item | Status |
|------|--------|
| `POST /orders` | ✅ Implementado |
| `GET /orders` | ✅ Implementado |
| `GET /orders/{id}` com conversão de moeda | ✅ Implementado |
| Isolamento por conta autenticada | ✅ Implementado |
| Testes unitários (6 casos) | ✅ Implementado |
| Gateway routing `/orders/**` | ✅ Implementado |
| CI/CD Jenkins (order + gateway) | ✅ Implementado |
| Mocks WireMock (product + exchange) | ✅ Implementado |
| Product API | 🔜 Próxima entrega |
| Exchange API | 🔜 Próxima entrega |

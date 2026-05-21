# Order API — Henry Idesis

**Aluno:** Henry Idesis  
**Grupo:** Henry Idesis, Nathan, Kauã  
**Disciplina:** Plataformas, Microsserviços, DevOps e APIs — Insper 2026.1  
**Instrutor:** Humberto Sandmann

---

## Sobre o Projeto

Plataforma de e-commerce baseada em arquitetura de microsserviços. Minha responsabilidade individual foi o desenvolvimento da **Order API** — o serviço responsável por criar e consultar pedidos com suporte a múltiplas moedas.

---

## Entregas

| Atividade | Status | Repositório |
|-----------|--------|-------------|
| Order API (contrato) | ✅ Concluído | [pma.261.order](https://github.com/microservice-henry/pma.261.order) |
| Order Service (implementação) | ✅ Concluído | [pma.261.order-service](https://github.com/microservice-henry/pma.261.order-service) |
| Gateway routing `/orders/**` | ✅ Concluído | [pma.261.gateway-service](https://github.com/microservice-henry/pma.261.gateway-service) |
| CI/CD Jenkins | ✅ Concluído | — |
| Bottleneck: Resiliência (fallback Exchange) | ✅ Implementado e medido | — |
| Bottleneck: Observabilidade (Prometheus + Grafana) | ✅ Implementado e medido | — |
| Bottleneck: Índices no banco | ✅ Implementado e medido | — |

---

## Repositórios

| Serviço | Repositório |
|---------|-------------|
| Order (contrato) | [pma.261.order](https://github.com/microservice-henry/pma.261.order) |
| Order Service | [pma.261.order-service](https://github.com/microservice-henry/pma.261.order-service) |
| Gateway Service | [pma.261.gateway-service](https://github.com/microservice-henry/pma.261.gateway-service) |
| Account Service | [pma.26.1.account-service](https://github.com/microservice-henry/pma.26.1.account-service) |
| Auth Service | [pma.261.auth-service](https://github.com/microservice-henry/pma.261.auth-service) |
| Plataforma (raiz do grupo) | [pma.26.1](https://github.com/microservice-henry/pma.26.1) |

---

## Documentação do Grupo

A documentação da infraestrutura compartilhada (AWS, EKS, CI/CD, Custos) e dos demais microsserviços está na **documentação do grupo**:

[microservice-henry.github.io/pma.26.1](https://microservice-henry.github.io/pma.26.1/){ .md-button }

---

## Uso de Inteligência Artificial

!!! info "Declaração de uso de IA"
    Durante o desenvolvimento, utilizamos o **Claude (Anthropic)** como ferramenta de apoio em tarefas pontuais: geração de código boilerplate, sugestões de melhoria e correção de erros. As decisões de arquitetura, a lógica de negócio e a estrutura dos serviços foram definidas e implementadas pelo grupo. Toda a documentação e o código foram revisados e validados em conjunto pelos membros, garantindo que todos compreendem e conseguem explicar cada parte do projeto.

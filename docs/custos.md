# Custos e SLA

## Modelo de Serviço (PaaS vs IaaS)

| Componente | Modelo | Quem gerencia |
|------------|--------|--------------|
| EKS Control Plane | **PaaS** | AWS gerencia master nodes, etcd, API server |
| EC2 Node Group | IaaS | Você gerencia os workers |
| NLB | **PaaS** | AWS gerencia roteamento e failover |
| Docker Hub | **PaaS** | Docker Inc. gerencia o registry |
| PostgreSQL em pod K8s | IaaS | Você gerencia backup, HA, atualizações |

!!! info "Por que EKS é PaaS?"
    Com EKS, você não gerencia os master nodes do Kubernetes — a AWS cuida de alta disponibilidade, patches e atualizações do control plane. Você só gerencia o que roda nos workers.

---

## Estimativa de Custos por Hora

Valores de referência (us-east-2, Mai/2026 — consulte a calculadora AWS para valores atualizados):

| Recurso | Tipo | Qtd | Custo/hora |
|---------|------|-----|-----------|
| EKS Control Plane | PaaS | 1 cluster | $0,10 |
| EC2 Workers | `t3.medium` | 1 node | $0,0416 |
| NLB | Network Load Balancer | 1 | $0,008 + tráfego |
| **Total estimado** | | | **~$0,15/hora** |

> Cluster `eks-store` ativo em `us-east-2` com node group `ng-store` (`t3.medium × 1`). Valores baseados no pricing público AWS Mai/2026.

!!! warning "GitHub Pages (docs) = PaaS gratuito"
    A documentação deste projeto é servida via **GitHub Pages** — PaaS gratuito para repositórios públicos, sem custo adicional.

---

## Custo Estimado por Evento

| Cenário | Duração | Custo estimado |
|---------|---------|---------------|
| Apresentação (cluster ativo) | ~2 horas | $0,15 × 2 |
| Semana de testes | ~8h/dia × 5 dias | $0,15 × 40 |
| Cluster esquecido ligado | 720h (1 mês) | ⚠️ $0,15 × 720 |

---

## Estratégia de Teardown

Para evitar custos após a apresentação. **O comando exato para o node group**:

```bash
aws eks delete-nodegroup \
  --cluster-name eks-store \
  --nodegroup-name ng-store \
  --region us-east-2 \
  --no-wait
```

Ordem completa:

```bash
# 1. Deletar services LoadBalancer (libera o NLB)
kubectl delete svc gateway

# 2. Deletar deployments
kubectl delete deployment --all

# 3. Deletar node group (para EC2s)
eksctl delete nodegroup \
  --cluster eks-store \
  --name ng-store \
  --region us-east-2

# 4. Deletar cluster EKS (para control plane)
eksctl delete cluster \
  --name eks-store \
  --region us-east-2

# 5. Confirmar que não há recursos órfãos
aws ec2 describe-instances \
  --query 'Reservations[*].Instances[*].[InstanceId,State.Name,InstanceType]' \
  --output table

aws elbv2 describe-load-balancers --output table
```

!!! warning "Verifique sempre após deletar"
    Load Balancers e EBS volumes podem persistir mesmo após `eksctl delete cluster`. Verifique no Console AWS → EC2 → Load Balancers e EC2 → Volumes.

---

## SLA dos Serviços AWS

| Serviço | SLA AWS | Impacto se cair |
|---------|---------|----------------|
| EKS Control Plane | 99,95% | Deploys e scaling param |
| EC2 (nodes) | 99,99% | Pods ficam offline |
| NLB | 99,99% | API inacessível externamente |

O SLA composto da aplicação é limitado pelo componente mais frágil. Com EKS + NLB, o SLA teórico é **≤ 99,95%** — ou seja, até ~4,4 horas de downtime por ano no pior caso.

---

## Comparativo: Docker Compose Local vs EKS

| Aspecto | Docker Compose | EKS |
|---------|---------------|-----|
| Custo | $0 (local) | ~$0,15/hora |
| Escalabilidade | Manual | Automática (HPA) |
| Alta disponibilidade | Não | Sim (multi-node) |
| Complexidade operacional | Baixa | Média/Alta |
| Adequado para | Desenvolvimento | Produção |

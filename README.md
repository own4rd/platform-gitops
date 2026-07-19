# platform-gitops

Repositório GitOps com os manifests/Helm charts sincronizados pelo ArgoCD.

## Criando a aplicação no ArgoCD

Objetivo: registrar o chart `nginx-web` como uma Application do ArgoCD, apontando para este repositório, com sincronização automática (auto-sync, self-heal e auto-prune) e criação automática do namespace de destino.

```bash
argocd app create nginx-web \
  --project default \
  --repo https://github.com/own4rd/platform-gitops.git \
  --path charts/nginx-web \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace nginx-web \
  --sync-policy automated \
  --self-heal \
  --auto-prune \
  --sync-option CreateNamespace=true
```

- `--repo` / `--path`: localizam o chart `nginx-web` dentro deste repositório.
- `--dest-server` / `--dest-namespace`: implantam no cluster local, no namespace `nginx-web`.
- `--sync-policy automated --self-heal --auto-prune`: mantém o cluster em conformidade com o Git, revertendo drifts e removendo recursos órfãos automaticamente.
- `--sync-option CreateNamespace=true`: cria o namespace de destino caso ele não exista.

## Microserviços (projeto de estudo)

Código-fonte em repositórios separados (cada um com CI própria publicando no GHCR); os charts Helm de deploy ficam aqui, em `charts/`. Arquitetura completa: [docs/credit-architecture.md](docs/credit-architecture.md).

| Serviço | Repositório | Chart | HPA |
|---|---|---|---|
| credit-validation-service (MS1) | [own4rd/credit-validation-service](https://github.com/own4rd/credit-validation-service) | `charts/credit-validation-service` | 3–10 |
| credit-release-service (MS2) | [own4rd/credit-release-service](https://github.com/own4rd/credit-release-service) | `charts/credit-release-service` | 4–20 |
| credit-score-service (MS3) | [own4rd/credit-score-service](https://github.com/own4rd/credit-score-service) | `charts/credit-score-service` | 2–6 |

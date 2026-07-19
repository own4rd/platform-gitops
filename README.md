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

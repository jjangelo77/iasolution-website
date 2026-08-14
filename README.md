# iasolution-website

## Deploy na VPS (Hostinger)

```bash
# 1. Acessar a pasta do projeto
cd /root/iasolution-website/

# 2. Baixar a versão mais recente do GitHub
git pull origin main

# 3. Os arquivos já estão no ar (o container monta esta pasta via bind-mount).
#    Reiniciar é opcional — só se quiser garantir o recarregamento.
docker restart iasolution-website
```
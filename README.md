# iasolution-website

## Deploy na VPS (Hostinger)

```bash
# 1. Acessar a pasta do projeto
cd /root/iasolution-website/

# 2. Baixar a versão mais recente do GitHub
git pull origin main

# 3. Reiniciar o container Nginx
docker restart iasolution-website
```
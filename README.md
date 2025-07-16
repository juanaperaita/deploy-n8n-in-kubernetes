# n8n
Subir n8n em cluster kubernetes

Steps:

1) Instalar n8n-service.yaml
2) Instalar n8n-service-postgress.yaml
3) Instalar n8n-pvc.yaml
4) Instalar n8n-pvc-postgres.yaml
5) Instalar n8n-ingress.yaml
6) Instalar n8n-deployment-postgress.yaml
7) Criar database no banco.
- psql -d postgres -U postgres
- CREATE DATABASE n8n;
- CREATE USER n8n WITH PASSWORD 'n8neconomia';
- ALTER DATABASE n8n OWNER TO n8n;
- GRANT ALL PRIVILEGES ON DATABASE n8n TO n8n;
- \q
- psql -d n8n -U n8n -W
8) Instalar n8n-deployment-v2.yaml

# Docker Laravel Vue

Ambiente Docker que orquestra o backend Laravel, o frontend Vue com Vite, o proxy reverso Nginx e um banco MySQL. A proposta é oferecer um setup único para desenvolvimento local, com hot reload em ambos os lados e serviços prontos para testes end-to-end.

## Pré-requisitos
- Docker 24 ou superior
- Docker Compose 2 ou superior

## Serviços disponíveis
- **laravel-backend**: container PHP-FPM com o código em `../back`.
- **vue-frontend**: executa `yarn dev` para servir o Vite com hot reload.
- **nginx**: reverse proxy que entrega o frontend em `http://localhost:91` e roteia `/api` para o backend.
- **mysql**: banco persistido, exposto na porta `3306` (ajustável via variável `MYSQL_PORT`).

## Primeiros passos
1. Garanta os arquivos de ambiente:
   ```bash
   cp back/.env.example back/.env
   cp front/.env.example front/.env
   ```
2. Suba os containers:
   ```bash
   docker compose up -d
   ```
3. Caso ainda não tenha instalado dependências do backend/front:
   ```bash
   docker exec -it laravel-backend composer install
   docker exec -it laravel-backend php artisan key:generate
   docker exec -it vue-frontend yarn install
   ```

## Banco de dados
Migrações e seeds podem ser executadas de dentro do container Laravel. O seeder `InitialSetupSeeder` cria a base `banco`, executa as migrações, popular destinos e usuários de teste.

```bash
docker exec -it laravel-backend php artisan migrate
docker exec -it laravel-backend php artisan db:seed --class=InitialSetupSeeder
```

Se preferir um atalho único após subir os containers:

```bash
docker exec -it laravel-backend php artisan migrate --seed
```

## Usuários padrão
Criados automaticamente pelo `InitialSetupSeeder`. A senha é igual ao e-mail.

| Nome          | E-mail            | Perfil          | Senha              |
|---------------|-------------------|-----------------|--------------------|
| Administrador | admin@gmail.com   | administrador   | admin@gmail.com    |
| Usuario       | usuario@gmail.com | usuario         | usuario@gmail.com  |
| Teste         | teste@gmail.com   | usuario         | teste@gmail.com    |

## Testes automatizados
Execute a suíte completa (unitária + feature) dentro do container Laravel:

```bash
docker exec -it laravel-backend php artisan test
```

Você pode limitar aos testes de unidade com:

```bash
docker exec -it laravel-backend php artisan test --testsuite=Unit
```

## Acesso aos serviços
- Frontend: http://localhost:91/
- API Laravel (via proxy): http://localhost:91/api/
- MySQL: host `localhost`, porta `3306` (padrão), usuário `sib`, senha `sib`. O usuário root usa a senha `root`.

## Comandos úteis
- Reiniciar serviços: `docker compose restart`
- Parar tudo: `docker compose down`
- Parar e limpar volumes: `docker compose down -v`
- Logs do backend: `docker logs -f laravel-backend`
- Logs do frontend: `docker logs -f vue-frontend`
- Acessar Tinker: `docker exec -it laravel-backend php artisan tinker`

## Fluxo recomendado
1. `docker compose up -d`
2. `docker exec -it laravel-backend php artisan db:seed --class=InitialSetupSeeder` (sempre que precisar repovoar).
3. Desenvolva em `back/` e `front/` usando hot reload fornecido pelos containers.
4. Rode `docker exec -it laravel-backend php artisan test` antes de subir alterações.

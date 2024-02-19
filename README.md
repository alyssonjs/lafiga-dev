## Ambiente de desenvolvimento Lafiga


baixe os reposítorios no mesmo nível de arquivos, exemplo:
<br>
<br>
lafiga:<br>
&nbsp;    - lafiga_api<br>
&nbsp;    - docker<br>
&nbsp;    - lafiga_front<br>
<br><br>
Foi criando symlinks com caminho relativo de arquivos para facilitar a vida, no entantno para usa-los, será preciso alterar o nome das pastas,
Caso não queira alterar o nome das pastas, crie um novo symlink e substitua o antigo.
<br><br>

Renomeie o nome da pasta no respectivo formato:<br><br>
 &nbsp;    -projeto backend/api -> api<br>
 &nbsp;    -projeto frontend -> front<br>
<br><br>
## Observações Individuais de cada projeto:<br>
### front
      - Ao atualizar o package.json (normalmente acontece devido a adição de uma nova biblioteca), atualize o package.json dentro do projeto front na  pasta config no docker e rebuilde o container<br>
      ```bash
      front/config/package.json
      ```
### api
    - Ao atualizar o gemfile (normalmente acontece devido a adição de uma nova gem), atualizar o gemfile e o gemfile.lock dentro do projeto api na pasta config no docker e rebuilde o container;<br>
    ```bash
      api/config/Gemfile
      api/config/Gemfile.lock
    ```
#### Para realizar a conexão com o banco de dados, é preciso criar manualmente o usuário e o banco de dados, com o container rodando: 
```bash
docker exec -it lafiga_db bash
psql -U postgres
create user lafiga_api with password 'password';
create database "lafiga-api_db_1"  owner lafiga_api;
alter user lafiga_api superuser createrole createdb replication;
```

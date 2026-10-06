# Respostas · Avaliação Prática de Docker · Cooperativa AgroVale (Turma A)

Nome: Mateus de Alcantara Silva
Matrícula: 26127954
Usuário do GitHub: MateusAlcantara13
Usuário do Docker Hub: mateusggtytyttrt55


Responda com as suas palavras e com o que aconteceu na SUA máquina. Resposta curta e certa vale mais
do que texto longo copiado. Resposta que contradiz o seu próprio Dockerfile ou compose vale zero.

## Parte 1 · Dockerfile do portal

1. Qual imagem base você usou e qual o tamanho final da imagem do portal (saída de `docker images`)?

Usei a imagem oficial `nginx:1.27-alpine`, com tag fixa. O tamanho final da imagem
`mateusggtytyttrt55/agrovale-portal:1.0-26127954` no `docker images` foi de 73.6MB (disk usage), com 21MB de conteúdo compactado

2. Em qual pasta do container o Nginx procura os arquivos do site? Mostre o comando que você usou para
   conferir que o `index.html` está lá dentro.

O Nginx serve os arquivos da pasta `/usr/share/nginx/html`. Conferi com:

```
docker exec teste-portal ls /usr/share/nginx/html
```

A saída mostrou o `index.html` e o `estilo.css` dentro da pasta.

## Parte 2 · Docker Hub

3. Nome completo da imagem publicada e link público do repositório no Docker Hub.

Imagem: `mateusggtytyttrt55/agrovale-portal:1.0-26127954`
Link: https://hub.docker.com/r/mateusggtytyttrt55/agrovale-portal

4. Por que o `docker login` foi feito com um token de acesso e não com a senha da conta?

Porque o token pode ter permissão limitada (só Read & Write nos repositórios) e pode ser revogado a
qualquer momento sem trocar a senha da conta. Como o login foi feito num computador do laboratório,
se o token vazar eu apago só ele, e a senha principal continua protegida.

## Parte 3 · Página de manutenção

5. Preencha uma linha por defeito encontrado. Defeito inexistente listado aqui desconta pontos.

| # | Instrução | O que estava errado | O que você viu acontecer | Como corrigiu |
|---|---|---|---|---|
| 1 | `COPY` (ausente) | O Dockerfile não copiava a pasta `site/` para dentro da imagem | O container subia (`Up` no `docker ps`), mas em http://localhost:7054 aparecia "Welcome to nginx!" em vez do aviso | Adicionei `COPY site/ .` |
| 2 | `WORKDIR` | Apontava para `/usr/share/nginx`, fora da pasta que o Nginx publica | Mesmo com o COPY, continuava "Welcome to nginx!". O `docker exec manut ls /usr/share/nginx` mostrou o `index.html` solto nessa pasta, ao lado da pasta `html` | Troquei para `WORKDIR /usr/share/nginx/html` |

6. Qual a diferença entre `-p 7042:80` e `-p 80:7042` no `docker run`? Qual dos dois números é a porta do container?

No `-p 7054:80`, o 7054 é a porta do meu computador e o 80 é a porta do container, onde o Nginx escuta.
O `-p 80:7054` inverteria: ligaria a porta 80 do PC a uma porta 7054 do container, onde nada está
rodando, e a página não abriria. O número da direita é sempre a porta do container.

## Parte 4 · docker-compose.yml

7. No serviço `blog`, por que `WORDPRESS_DB_HOST` recebe `db` e não `localhost`?

Porque cada serviço roda no seu próprio container. Dentro do container do blog, `localhost` aponta para
o próprio blog, onde não existe banco. Na rede do Compose, cada serviço é encontrado pelo seu nome, então
o WordPress acha o MariaDB pelo nome do serviço, `db`.

8. Por que o serviço `db` não publica a porta 3306? Se precisar consultar o banco, como faz sem publicar
   a porta? Mostre o comando.

Porque só o blog precisa falar com o banco, e isso acontece pela rede interna `agrovale_net`. Publicar a
3306 deixaria o banco exposto para qualquer máquina da rede, sem necessidade. Para consultar o banco,
entro no próprio container:

```
docker compose exec db mariadb -u root -p
```

## Parte 5 · Persistência

9. Quais comandos você usou para derrubar e subir a stack? Qual comando teria apagado o post que você criou,
   e por quê?

Derrubei com `docker compose down` e subi de novo com `docker compose up -d`. O post continuou lá porque
os dados ficam nos volumes nomeados `db_dados` e `blog_dados`, que o `down` não apaga.
O comando que teria apagado o post é `docker compose down -v`, porque o `-v` remove também os volumes,
e com eles o banco de dados onde o post estava salvo.

10. Código de conclusão impresso pelo verificador:

```
AGROVALE-26127954-4AB1F352
```
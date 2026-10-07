# Respostas · Avaliação Prática de Docker · ViaSerra Transportes (Turma C)

**Nome:** isabelli lopes montenegro
**Matrícula:** 26174742
**Usuário do GitHub:** isamontenegro
**Usuário do Docker Hub:** isabellimontenegro

---

## Parte 1 · Dockerfile do portal

**1. Qual imagem base você usou e qual o tamanho final da imagem do portal (saída de `docker images`)?**

Usei a imagem base `nginx:1.27-alpine` (tag fixa, sem `latest`). No `docker images`, a imagem `isabellimontenegro/viaserra-portal:1.0-26174742` aparece com **73.6MB** de disk usage (21MB de content size).

**2. Em qual pasta do container o Nginx procura os arquivos do site? Mostre o comando que você usou para conferir que o `index.html` está lá dentro.**

O Nginx serve os arquivos de `/usr/share/nginx/html`. Para conferir usei:

```bash
docker exec teste-portal ls /usr/share/nginx/html
```

---

## Parte 2 · Docker Hub

**3. Nome completo da imagem publicada e link público do repositório no Docker Hub.**

- **Imagem:** `isabellimontenegro/viaserra-portal:1.0-26174742`
- **Link:** https://hub.docker.com/r/isabellimontenegro/viaserra-portal

**4. Se você mudar o HTML, quais comandos precisa rodar para que a versão nova chegue ao Docker Hub?**

Preciso reconstruir a imagem e enviar de novo:

```bash
docker build -t isabellimontenegro/viaserra-portal:1.0-26174742 ./portal
docker push isabellimontenegro/viaserra-portal:1.0-26174742
```

---

## Parte 3 · Página de manutenção

**5. Preencha uma linha por defeito encontrado.**

| # | Instrução | O que estava errado | O que você viu acontecer | Como corrigiu |
|---|-----------|---------------------|--------------------------|---------------|
| 1 | `COPY pagina/ .` | A pasta com o site se chama `site/`, não `pagina/` | O `docker build` falhou dizendo que não encontrou a pasta `pagina` | Troquei para `COPY site/ ...` |
| 2 | `WORKDIR /usr/share/nginx` (destino `.` do `COPY`) | Os arquivos iam para `/usr/share/nginx`, mas o Nginx serve de `/usr/share/nginx/html` | O container subiu, mas o navegador mostrou a página padrão do Nginx / 404 em vez do "Voltamos em breve" | Usei o destino `/usr/share/nginx/html/` no `COPY` |
| 3 | `CMD ["nginx"]` | Sem `-g "daemon off;"` o Nginx vai para segundo plano e o processo principal termina | O container ficou como `Exited (0)` no `docker ps -a` logo depois de subir | Troquei para `CMD ["nginx", "-g", "daemon off;"]` |

**6. Qual a diferença entre `-p 7042:80` e `-p 80:7042` no `docker run`? Qual dos dois números é a porta do container?**

O formato é `-p porta-do-host:porta-do-container`.

- `-p 7042:80`: a porta **7042** do meu computador leva para a porta **80** dentro do container, onde o Nginx escuta.
- `-p 80:7042`: o contrário. A porta 80 do host leva para a 7042 do container, onde o Nginx não está escutando.

O número da **direita** (depois dos dois pontos) é a porta do container.

---

## Parte 4 · Primeiro docker-compose

**7. Escreva os dois comandos `docker run` que fariam o mesmo que o seu `docker-compose.yml`.**

```bash
docker run -d --name portal -p 8042:80 --restart unless-stopped \
  isabellimontenegro/viaserra-portal:1.0-26174742

docker build -t manutencao:26174742 ./manutencao
docker run -d --name manutencao -p 7042:80 --restart unless-stopped \
  manutencao:26174742
```

**8. Qual comando derruba os dois containers de uma vez?**

```bash
docker compose down
```

**9. Código de conclusão impresso pelo verificador:**

```
VIASERRA-26174742-32648CE1
```

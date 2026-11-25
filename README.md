# Score de risco de crédito (EM DESENVOLVIMENTO)

Estudo de caso de risco de inadimplência com o dataset Home Credit Default Risk, do Kaggle. O repositório traz o banco PostgreSQL já populado, dentro de uma imagem Docker, e os notebooks de análise.

O relatório da análise exploratória está em [report/relatório.md](report/relatório.md).

## O que instalar antes

Você precisa de três programas.

- [Git](https://git-scm.com/downloads), para clonar o repositório.
- [Docker](https://docs.docker.com/get-started/get-docker/). No Windows e no Mac, instale o Docker Desktop e deixe-o aberto durante o uso.
- [uv](https://docs.astral.sh/uv/getting-started/installation/), que instala o Python 3.14 e as bibliotecas do projeto.

A imagem do banco ocupa alguns gigabytes. Reserve pelo menos 10 GB livres no disco.

## Instalação

Clone o repositório e suba o banco.

```bash
git clone https://github.com/AkiraSaito-png/score-de-risco-de-credito.git
cd score-de-risco-de-credito
docker compose up -d
uv sync
```

O primeiro `docker compose up` baixa a imagem e leva alguns minutos. Para saber se o banco está pronto, rode:

```bash
docker compose ps
```

A coluna STATUS deve mostrar `healthy`. Nos primeiros segundos ela mostra `starting`, e é só esperar.

## Como usar

O banco aceita conexões apenas da sua máquina, nestes dados:

- host `localhost`
- porta `5432`
- banco `home_credit`
- usuário `postgres`
- senha `postgres`

A senha é de demonstração e o banco não é acessível pela rede, então ela não protege nada além disso. Não reaproveite essa senha em outro lugar.

Para testar a conexão pelo terminal:

```bash
psql "postgresql://postgres:postgres@localhost:5432/home_credit" -c "SELECT count(*) FROM application_train"
```

O resultado deve ser 307511. Os notebooks ficam em `notebooks/`. Abra-os pelo VS Code, escolhendo o ambiente `.venv` que o `uv sync` criou, ou rode `uv run jupyter lab` se tiver o JupyterLab instalado.

## Parar e recomeçar

```bash
docker compose down
```

O comando remove o container. Como o projeto não usa volume, tudo o que você gravar no banco some junto, e o próximo `docker compose up -d` entrega o banco no estado original.

## Desinstalar

Dentro da pasta do projeto, remova o container e a imagem do banco, que é o que ocupa os gigabytes:

```bash
docker compose down --rmi all
```

Depois saia da pasta e apague o projeto, o que também leva o ambiente `.venv`:

```bash
cd ..
rm -rf score-de-risco-de-credito
```

No Windows, apague a pasta pelo Explorador de Arquivos. Se tiver feito login no registro do GitHub, saia com `docker logout ghcr.io`. Git, Docker e uv continuam instalados e podem ser removidos como qualquer outro programa.

## Sobre os dados

Os dados são os da competição [Home Credit Default Risk](https://www.kaggle.com/c/home-credit-default-risk), no Kaggle, e a imagem os contém por inteiro. O uso aqui é de estudo, e o projeto não participa da competição. Quem pretende reutilizar a imagem ou os dados deve ler antes os termos de uso da competição.

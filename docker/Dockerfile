# ---------- Etapa 1: build (monta o banco populado) ----------
FROM postgres:16 AS build

ARG PG_PASSWORD=postgres
ENV PGDATA=/opt/pgdata

# 1) o diretório precisa existir, com dono postgres e modo 0700
RUN mkdir -p $PGDATA && chown postgres:postgres $PGDATA && chmod 700 $PGDATA

COPY docker/home_credit.dump /tmp/home_credit.dump

# 2) tudo como usuário postgres (initdb recusa root)
USER postgres

RUN echo "$PG_PASSWORD" > /tmp/pw \
 && initdb -D "$PGDATA" --username=postgres --pwfile=/tmp/pw \
          --auth-local=trust --auth-host=scram-sha-256 \
 && echo "host all all all scram-sha-256" >> "$PGDATA/pg_hba.conf" \
 && pg_ctl -w start \
 && createdb -U postgres home_credit \
 && pg_restore -U postgres -d home_credit --no-owner /tmp/home_credit.dump \
 && psql -U postgres -d home_credit -c "ANALYZE" \
 && pg_ctl -m fast -w stop \
 && rm /tmp/pw

# ---------- Etapa 2: final (só os dados prontos) ----------
FROM postgres:16

ENV PGDATA=/opt/pgdata
COPY --from=build --chown=postgres:postgres $PGDATA $PGDATA
RUN chmod 700 $PGDATA
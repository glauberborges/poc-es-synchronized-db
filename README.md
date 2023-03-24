# POC de Elasticsearch, LogStach, MySQL e Kibana

Esta POC é uma demonstração simples de como usar o Docker Compose para configurar um ambiente com Elasticsearch, LogStash, MySQL e Kibana, e demonstrar a configuração básica de um pipeline que envia dados do MySQL para o Elasticsearch.

## Pré-requisitos
* Docker
* Docker Compose

## Como usar

Clone este repositório:
```sh
git clone https://github.com/glauberborges/poc-es-synchronized-db
```


Acesse o diretório do projeto:
```sh
cd poc-es-synchronized-db
```

Inicie o ambiente Docker Compose:
```sh
docker-compose up --build
```
Aguarde até que todos os containers sejam iniciados. Você pode acompanhar o progresso no log do terminal.

Acesse o Kibana através do seu navegador web em http://localhost:5601.

Crie o índice no Elasticsearch:

```curl
curl --location --request PUT 'http://localhost:9200/rdbms_sync_idx' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "settings": {
        "index": {
            "number_of_shards": 1,
            "number_of_replicas": 1
        }
    }
}'
```

Crie a tabela no banco de dados
```sql
CREATE TABLE `es_table` (
    `id` bigint unsigned NOT NULL AUTO_INCREMENT,
    `client_name` varchar(32) NOT NULL,
    `modification_time` timestamp NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    `insertion_time` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (`id`),
    UNIQUE KEY `unique_id` (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=7 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;
```


Sempre que tiver uma insert ou update na tabela será enviado para o ES
```sql
INSERT INTO es_table (client_name) VALUES ('Glauber Borges');

UPDATE es_table SET client_name = 'Glauber Borges update' WHERE id=1;
```

# Observações
* Você pode alterar as configurações do Elasticsearch, LogStash e Kibana no arquivo docker-compose.yml conforme a necessidade.
* O arquivo pipeline.conf é um exemplo simples de como configurar um pipeline básico do LogStash. Você pode alterar as configurações de acordo com a sua necessidade.
* O MySQL pode ser acessado através do seu cliente de banco de dados preferido, usando as credenciais definidas no arquivo docker-compose.yml. 
* Esta POC é apenas para fins educacionais e não é adequada para uso em produção.


# Referências
* Docker
* Docker Compose
* Elasticsearch
* LogStash
* MySQL
* Kibana
* [how-to-keep-elasticsearch-synchronized-with-a-relational-database-using-logstash](https://www.elastic.co/pt/blog/how-to-keep-elasticsearch-synchronized-with-a-relational-database-using-logstash)
# Amazon-athena

 Amazon Athena é o "casamento perfeito" entre a escalabilidade do NoSQL/Big Data (armazenados no S3) e a familiaridade do SQL. Imagine que todos os dados de pedidos que modelamos anteriormente foram exportados para arquivos JSON ou Parquet em um bucket. Com o Athena, você não precisa de um servidor ligado; você apenas "aponta" para os arquivos e roda o SELECT. 
Aqui estão as boas práticas e os códigos para transformar nosso E-commerce em uma solução de Big Data:
1. Definindo a Tabela (External Table)
Diferente do MySQL, no Athena você cria uma tabela que aponta para um diretório no S3. Se novos arquivos chegarem lá, o Athena os lê automaticamente.

sql
CREATE EXTERNAL TABLE IF NOT EXISTS ecommerce_db.pedidos_raw (
  id_pedido STRING,
  id_cliente STRING,
  data_pedido TIMESTAMP,
  valor_total DOUBLE,
  status_pedido STRING
)
ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
LOCATION 's3://seu-bucket-ecommerce/pedidos/';


3. O Poder das Consultas Interativas
Agora, mesmo com terabytes de logs ou históricos de vendas, você usa SQL padrão para análise de negócios: 
sql
-- Qual o faturamento total por dia no último mês?
   
SELECT 
    date_trunc('day', data_pedido) AS dia,
    SUM(valor_total) AS faturamento
FROM ecommerce_db.pedidos_raw
WHERE status_pedido = 'CONCLUIDO'
GROUP BY 1
ORDER BY 1 DESC;


5. Boas Práticas de "Expert" para Athena:

Particionamento: Divida seus dados no S3 por pastas como /ano=2023/mes=10/. Isso faz o Athena ler apenas os arquivos necessários, economizando dinheiro (ele cobra por TB varrido).
Formato Colunar (Parquet/ORC): Converta seus JSONs para Parquet. Consultas em formatos colunares são até 90% mais baratas e mais rápidas no Athena.
CTAS (Create Table As Select): Use o Athena para transformar dados brutos em tabelas otimizadas já filtradas.

7. Integração com o Ecossistema
O resultado de uma consulta no Athena pode alimentar diretamente um dashboard no Amazon QuickSight ou ser consumido por um notebook de Machine Learning no SageMaker. 

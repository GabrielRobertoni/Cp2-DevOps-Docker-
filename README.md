# Cp2-DevOps-Docker-
Cp2-DevOps (Docker)

CP2 - DevOps (Docker)
Integrantes
Bruno Ferreira — RM563489

Gabriel Robertoni Padilha — RM566293

Leonardo Aragaki — RM562944

Estrutura de Pastas do Projeto

cp2-docker/

Código Fonte da API — api/index.js
const express = require('express');

const { Pool } = require('pg');
const app = express();

app.use(express.json());
const pool = new Pool({

host: 'postgres-566293',

user: 'postgres',

password: '1234',

database: 'dimdim',

port: 5432

});
app.post('/cliente', async (req, res) => {

const { nome, email } = req.body;

await pool.query(

'INSERT INTO cliente (nome, email) VALUES ($1, $2)',

[nome, email]

);

res.send('Inserido');

});
app.get('/cliente', async (req, res) => {

const result = await pool.query('SELECT * FROM cliente');

res.json(result.rows);

});
app.put('/cliente/:id', async (req, res) => {

const { nome } = req.body;

await pool.query(

'UPDATE cliente SET nome=$1 WHERE id=$2',

[nome, req.params.id]

);

res.send('Atualizado');

});
app.delete('/cliente/:id', async (req, res) => {

await pool.query(

'DELETE FROM cliente WHERE id=$1',

[req.params.id]

);

res.send('Deletado');

});
app.listen(3000, () => console.log('API rodando'));
api/package.json
{

"name": "api-dimdim",

"version": "1.0.0",

"main": "index.js",

"license": "MIT",

"dependencies": {

"express": "^4.19.2",

"pg": "^8.11.5"

}

}
api/Dockerfile
FROM node:18

WORKDIR /app

COPY . .

RUN npm install

EXPOSE 3000

CMD ["node", "index.js"]
Criação dos Objetos Docker
Criar a rede

docker network create dimdim-net

docker network ls
Criar o volume nomeado

docker volume create dimdim-volume

docker volume ls
Subir o container do Banco — PostgreSQL

docker run -d \

--name postgres-566293 \

--network dimdim-net \

-p 5432:5432 \

-e POSTGRES_PASSWORD=1234 \

-e POSTGRES_DB=dimdim \

-v dimdim-volume:/var/lib/postgresql \

postgres:16
Criar a tabela no banco

docker exec -i postgres-566293 psql -U postgres -d dimdim << 'SQL'

CREATE TABLE cliente (

id SERIAL PRIMARY KEY,

nome VARCHAR(100),

email VARCHAR(100)

);

SQL
Build da imagem da API

docker build -t api-dimdim ./api

docker image ls
Subir o container da API

docker run -d \

--name api-566293 \

--network dimdim-net \

-p 3000:3000 \

api-dimdim
Testes do CRUD
INSERT

curl -X POST http://localhost:3000/cliente \

-H "Content-Type: application/json" \

-d '{"nome":"Bruno","email":"bruno@email.com"}'
SELECT

curl http://localhost:3000/cliente
UPDATE

curl -X PUT http://localhost:3000/cliente/1 \

-H "Content-Type: application/json" \

-d '{"nome":"Bruno Atualizado"}'
DELETE

curl -X DELETE http://localhost:3000/cliente/1
Comandos exibidos no vídeo

docker ps

docker image ls

docker volume ls

docker network ls
Link do vídeo (editar depois)
 

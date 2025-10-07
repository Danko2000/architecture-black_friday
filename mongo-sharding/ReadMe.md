Запускаем mongodb и приложение

docker compose up -d

Инициализировать Config Server Replica Set\
Для этого запускаем команду:\
docker exec -it configSrv mongosh\
Затем в консоли mongosh выполнить:

rs.initiate({ _id: "config_server", members: [ { _id: 0, host: "configSrv:27017" } ] })

Выполняем инициализацию шардов:

Запускаем в терминале для shard1:\
docker exec -it shard1-primary mongosh --port 27018\
затем в консоли:

rs.initiate({ _id: "shard1", members: [ { _id: 0, host: "shard1-primary:27018" }, { _id: 1, host: "shard1-secondary1:27018" }, { _id: 2, host: "shard1-secondary2:27018" } ] })

Аналогично для shard2\
docker exec -it shard2-primary mongosh --port 27019\
затем в консоли:

rs.initiate({ _id: "shard2", members: [ { _id: 0, host: "shard2-primary:27019" }, { _id: 1, host: "shard2-secondary1:27019" }, { _id: 2, host: "shard2-secondary2:27019" } ] })

Далее подключаемся к mongos_router\
docker exec -it mongos_router mongosh --port 27020\
и добавляем шарды:

sh.addShard("shard1/shard1-primary:27018,shard1-secondary1:27018,shard1-secondary2:27018")\
sh.addShard("shard2/shard2-primary:27019,shard2-secondary1:27019,shard2-secondary2:27019")\
sh.status() // (Проверка, что шарды добавлены)

Заполняем mongodb данными

```shell
./scripts/mongo-init.sh
```

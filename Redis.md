1. Pipeline <br>
```java
 for (long i = 0; i < 100_000L; i++) {
        pl.set("string", "value" + i); // строки
        pl.lpush("list", "value" + i); // списки
        pl.sadd("set:key", Long.toString(i));  // множества
        pl.hset("user", "name", "department"); // хеш
        pl.zadd("key",  i, String.valueOf(12*i)); // упорядоченные множества
  }
  pl.sync();
```
2. Вывод в Redis CLI <br>
<set>
smembers set:key
<zset>
zrange key 0 -1 withscores
<string>
get string
<list>
lrange list 0 -1
<hash>
hgetall user
3. Пример <br>
3.1 String - сложность О(1) линейная <br>
![image](https://user-images.githubusercontent.com/94684347/212137874-cbc10f89-eb2c-4637-b600-f3603967ab52.png)
3.2 List - Сложность O(log n)  - логарифмическая от количества элементов <br> Команда lrange list 0 -1 <br>
![image](https://user-images.githubusercontent.com/94684347/212138177-84e7ccb5-489a-4a91-a4f8-642ffe1560ed.png)
3.3 Множества - Сложность O(log n)  - логарифмическая от количества элементов <br> Команда smembers set:key <br>
![image](https://user-images.githubusercontent.com/94684347/212139675-142acec6-f3f2-4c48-83d9-7fa36c6f7a7f.png)
3.4 Hash - линейная сложность О(1) <br> Команда hgetall user
![image](https://user-images.githubusercontent.com/94684347/212140239-21f91119-ce3f-4c5f-844a-29eb4553a736.png)
3.5 ZSet - Сложность O(log n)  - логарифмическая от количества элементов <br> Команда zrange key 0 -1
![image](https://user-images.githubusercontent.com/94684347/212141360-7eec81a8-abab-4717-963d-d180febd42a0.png)





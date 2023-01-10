1. Код для пайплайна на java. Результаты сильно зависят от параметра jedisPoolConfig.setMaxTotal(20) <br>
Если количество подключений указать 1, то всё будет работать намного медленнее. 
```java
package pipeline;

import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisPool;
import redis.clients.jedis.JedisPoolConfig;
import redis.clients.jedis.Pipeline;

public class RedisPipeline {
    public static void main(String[] args) {
        JedisPoolConfig jedisPoolConfig = new JedisPoolConfig();
        jedisPoolConfig.setMaxTotal(20);
        jedisPoolConfig.setMaxIdle(10);
        jedisPoolConfig.setMinIdle(5);

        JedisPool jedisPool = new JedisPool(jedisPoolConfig,
                "localhost", 6379, 2000, null);
        try (Jedis jedis = jedisPool.getResource()) {
            Pipeline pl = jedis.pipelined();
            for (long i = 0; i < 100_000L; i++) {
                pl.set("string" + i, "value" + i); // строки
                //  pl.lpush("list" + i, "value" + i); // списки
                // pl.sadd("set" + i, "value" + i);  // множества
                // pl.hset("user#" + i, "name", "department"); // хеш
              //  pl.zadd("key" + i,  i, String.valueOf(12*i)); // упорядоченные множества
            }
            pl.sync();
        } catch (Exception e) {
            e.printStackTrace();
        }

    }
    }
    
```
2. Строки: setMaxTotal == 30, время выполнения 2 сек 662 мс (последняя итерация, показатели всегда разные, результаты +- одинаковые) <br>
![image](https://user-images.githubusercontent.com/94684347/211653030-26ffc971-922b-473c-8dac-3b3fbd08d42e.png)
3. Списки:  2 сек 464 мс  <br>
![image](https://user-images.githubusercontent.com/94684347/211653629-715e7442-7bf9-49e6-b531-4143ff947d04.png)
4. Множества: 2 сек 485 мс <br>
![image](https://user-images.githubusercontent.com/94684347/211653994-a36a6a31-481f-4dc0-bb44-4ae4e12df5a9.png)
5. Хеш-таблицы: 2 сек 477 мс <br>
![image](https://user-images.githubusercontent.com/94684347/211654587-9bb1ca69-ab60-40ec-ac7a-f6682bd73a2e.png)
6. Упорядоченное множество: 2 сек 335 мс <br>
![image](https://user-images.githubusercontent.com/94684347/211654947-bffe1699-b05d-4fc6-9fcb-e304e71e7eb3.png)
7. Выводы: <br>
В целом, результаты в пределах 2.5 секунд. Но самые первые прогоны с чистым редисом заняли больше времени по сравнению с остальными итерациями <br>
(предполагаю первоначальный прогрев кешей). Также первый прогон с вычисляемым значением занял больше времени (> 3c), но второй и последующие прогоны <br>
показали почти одинаковые результаты в пределах 3х секунд (pl.zadd("key" + i,  i, String.valueOf(12*i))) 



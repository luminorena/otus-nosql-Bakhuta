ETCD

1. Установка шла обычным способом через репозитории, curl, wget (на этот раз без докеров) <br>
2. Версия и статус <br>
![image](https://user-images.githubusercontent.com/94684347/221870536-5b0ec357-5acb-4d9a-8a32-3988263ddb2e.png) <br>
3. Запись и получение данных <br>
![image](https://user-images.githubusercontent.com/94684347/221873357-3f126ab0-3cd0-4a9f-af36-45d7a82c1d71.png) <br>
4. В случае выше получилась одна нода, чтобы сделать несколько (к сожалению, в моем случае на локалхосте только), необходимо в файле /etc/etcd/etcd.conf 
прописать конфигурацию каждой ноды. <br>
![image](https://user-images.githubusercontent.com/94684347/221879926-f779c8f1-86a0-4511-b561-90335f78c1ae.png)
5. Затем смотрим member list <br>
![image](https://user-images.githubusercontent.com/94684347/221880175-e7162cfd-69d9-4dc7-b835-ed1cf4001233.png)

Что касается отказоустойчивости, то тут хорошо бы задеплоить все на разные хосты, и уже ронять инстансы. На одном локалхосте это нереально. Ну или в облако деплоить <br>

CONSUL

1. Уствновка шла через репозитории <br>
2. Статус <br>
![image](https://user-images.githubusercontent.com/94684347/221974685-0449463e-e81d-44c7-93e6-6978cddd5d8f.png)
3. Members <br>
![image](https://user-images.githubusercontent.com/94684347/221974900-cc37105b-911e-439e-88cc-5c2aed37d1ed.png) 
4. Конфиг /etc/consul.d/config.json <br>
![image](https://user-images.githubusercontent.com/94684347/221975166-67e54661-8db7-4b53-accc-363d81be9279.png)
5. UI выдает ошибку 500 <br>
![image](https://user-images.githubusercontent.com/94684347/221975541-192572e5-3e0a-42a3-a496-fcf9681120ff.png)

ACL пробовала настраивать по доке https://developer.hashicorp.com/consul/tutorials/security/access-control-setup-production <br>
Валится на втором шаге <br>
![image](https://user-images.githubusercontent.com/94684347/221975998-37a73838-75f5-464e-a3e3-aea44b9e8483.png)

Гугл, яндекс и еже с ними говорят, что надо:

Чтобы включить ACL, необходимо добавить следующие строки в файл конфигурации consul.hcl или же создать файл с любым названием но с расширением .hcl и положить его в /etc/consul.d

acl = { 
    enabled = true
    default_policy = "deny"
    enable_token_persistence = true
}

Однако, при добавлении в эту дирректорию файла с таким конфигом, в любом случае получаем вот это: <br>

![image](https://user-images.githubusercontent.com/94684347/221977557-17c1e3db-e78d-435e-89ea-971b168d8fa8.png)

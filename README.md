# Домашняя работа "VLAN"

<details>
<summary>Описание задания</summary>

### в Office1 в тестовой подсети появляется сервера с доп интерфесами и адресами в internal сети testLAN:  
  
+ testClient1 - 10.10.10.254  
+ testClient2 - 10.10.10.254  
+ testServer1- 10.10.10.1  
+ testServer2- 10.10.10.1  
  
Развести вланами testClient1 <-> testServer1 testClient2 <-> testServer2 между centralRouter и inetRouter "пробросить" 2 линка (общая inernal сеть) и объединить их в бонд проверить работу c отключением интерфейсов   
  
Формат сдачи ДЗ - vagrant + ansible  

</details>

### Практическая часть

##### [test.sh](test.sh) разворачивает стенд с ДЗ

1. [vagrantfile](vagrantfile) поднимает ВМ  
  
2. [Роль host](./roles/host/) настраивает ВМ testClient* и testServer*  - разводит сети VLANами
  
3. [Роль router](./roles/router/) настраивает ВМ centralRouter и inetRouter - настроивает bonding

4. Проверка работоспособности bondов и VLANов

Сложности с которыми пришлось столкнуться:

С VLAN проблем не возникло, с bond пришлось подумать, так как категорически не хотели подниматься интерфейсы eth*

Попробовал настроить через nmcli - bond поднялся, интерфейсы в UP. Но при тестировании работоспособности оказывалось, что при отключении активного интерфейса bond не переключался на резервный.

Команды nmcli

```
nmcli con add type bond con-name bond0 ifname bond0 mode 1 ip4 192.168.255.1/24
nmcli con add type bond-slave ifname eth1 master bond0
nmcli con add type bond-slave ifname eth2 master bond0
systemctl restart network
```

Помогла [статья](http://www.adminia.ru/linux-bonding-obiedinenie-setevyih-interfeysov-v-linux/), часть Настройка bonding через interfaces.

#### tcpdump для проверки установлен на всех ВМ



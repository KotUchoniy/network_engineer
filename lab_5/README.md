# Доступ к сетевым устройствам по протоколу SSH

## Топология

![Pic_1.png](./Pic_1.png)

| Устройство | Интерфейс | IP-адрес     | Маска подсети | Шлюз по умолчанию |
|------------|-----------|--------------|---------------|-------------------|
| R1         | G 0/0/1   | 192.168.1.1  | 255.255.255.0 |                   |
| S1         | VLAN 1    | 192.168.1.11 | 255.255.255.0 | 192.168.1.1       |
| PC-A       | NIC       | 192.168.1.3  | 255.255.255.0 | 192.168.1.1       |

## Часть 1. Настройка основных параметров устройств.

### Настройка маршрутизатора.
```
Router> enable
Router# conf t
Router(config)# no ip domain-lookup
Router(config)# hostname R1
R1(config)# service password-encryption
R1(config)# enable secret class
R1(config)# line console 0
R1(config-line)# logging synchronous
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# exit
R1(config)# line vty 0 15
R1(config-line)# logging synchronous
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# exit
R1(config)# banner motd "Unauthorized access is strictly prohibited"
R1(config)# interface GigabitEthernet 0/0/1
R1(config-if)# ip 192.168.1.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# end
R1# copy running-config startup-config
```
### Настройка компьютера PC-A
Результаты настройки приведены на рисунке.

![Pic_2.png](./Pic_2.png)

### Проверка подключения сети.
Результаты проверки доступности маршрутизатора R1 эхо-запросами приведены на рисунке.

![Pic_3.png](./Pic_3.png)

## Часть 2. Настройка маршрутизатора для доступа по протоколу SSH.

### Настройка аутентификации устройств.
```
R1# conf t
R1(config)# hostname R1
R1(config)# ip domain name local
```
### Создание ключа шифрования.
```
R1(config)# crypto key generate rsa
The name for the keys will be: R1.local

Choose the size of the key modulus in the range of 360 to 2048 for your

General Purpose Keys. Choosing a key modulus greater than 512 may take a few minutes.

How many bits in the modulus \[512]: 2048

% Generating 2048 bit RSA keys, keys will be non-exportable...\[OK]
```
### Создание пользователя в локальной базе данных.
```
R1(config)# username admin secret Adm1nP@55
```
### Активация протокола SSH.
```
R1(config)# line vty 0 15
R1(config-line)# login local
R1(config-line)# transport input ssh
R1(config-line)# exit
R1(config)# ip ssh version 2
R1(config)# exit
R1# copy running-config startup-config
```
### Проверка подключения по SSH.

![Pic_4.png](./Pic_4.png)

## Часть 3. Настройка коммутатора для доступа по протоколу SSH.
# Лабораторная работа 3

1. Обеспечим доступ в сеть Интернет виртуальной машине ubuntu (дальше - А). Для этого зайдем в настройки виртуальной машины в раздел Сеть и включим Адаптер 1, установим тип подключения NAT. Проверим доступ, введя в терминале `ping google.com`

![изображение](https://github.com/user-attachments/assets/90c5178a-aa07-47bb-be0c-549af6d25156)

Супер, пинг прошел успешно, значит интернет-соединение настроено.

2. Настроим еще одну виртуальную машину ubuntuB (дальше - Б). В настройках этой машины в разделе Сеть включим Адаптер 1, установим тип подключения "Внутренняя сеть", с именем "InternalNet" то же самое сделаем с виртуальной машиной А

3. Настроим IP-адреса на обоих машинах, чтобы обе машины находили в одной подсети и могли напрямую взаимодействовать. Для этого в треминале машины Б введем команду для настройки статического IP-адреса:

```
sudo nano/etc/netplan/01-netcfg.yaml
```
Добавим конфигурацию:

```
network:
  version: 2
  ethernets:
    enp0s3:
      dhcp4: no
      addresses:
        - 192.168.10.2/24
      routes:
        - to: default
          via: 192.168.10.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 8.8.4.4
```
4. Установим правильные разрешения на файл:

```
sudo chmod 600 /etc/netplan/01-netcfg.yaml
sudo chmod 600 /etc/netplan/01-network-manager-all.yaml
```

5. Применим конфигурацию `Netplan`

```
sudo netplan apply
```

6. Удостоверимся, что IP-адрес настроен:

```
ip a
```

7. Повторим то же самое с машиной А, но адрес введем `192.168.10.3`

8. Теперь попробуем выполнить `ping` с машины А на машину Б. В терминале машины А введем:

```
ping 192.168.10.2
```

![изображение](https://github.com/user-attachments/assets/27cb1c75-98af-439c-b610-577cf41352a4)

Всё супер, все работает.

9. Повторим пункты 3-6 с машиной ubuntuC (дальше - В), но IP-адрес будет `192.168.10.4`

10. Теперь доступ настроен между всеми машинами, осталось запретить доступ из Б в В. На машине В создадим правило, запрещающее доступ из машины Б в машину В. Для этого выполним команду в терминале машины В:

```
sudo iptables -A INPUT -s 192.168.56.11 -j DROP
```

11. Сохраним настройки `iptables`

```
sudo apt install iptables-persistent
sudo netfilter-persistent save
```

13. Проверим доступ:

![изображение](https://github.com/user-attachments/assets/af7926cb-f2d6-4c18-b1ab-04e2ce78418b)

## Все работает, победа.


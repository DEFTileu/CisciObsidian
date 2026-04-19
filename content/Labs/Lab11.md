#labs

# Lab 11 — DHCP

> [!abstract] Суть лабы
> Настроить DHCP сервер на Cisco роутере, создать пулы для нескольких подсетей, исключить статические адреса. Проверить выдачу адресов клиентам и защиту от DHCP атак.

## Source

https://drive.google.com/file/d/1PPsIfDkyGzlWsqj_vaiffGDbuYT0o8l/view?usp=drivesdk

## Topic

- `DHCP Server на Cisco IOS`
- `DHCP Pools (несколько подсетей)`
- `DHCP Snooping`

## Related Book Modules

- [[Book 2. Switching, Routing, and Wireless Essentials/07 - DHCPv4/07 - DHCPv4|Book 2 Module 7 — DHCPv4]]
- [[Book 2. Switching, Routing, and Wireless Essentials/11 - Switch Security Configuration/11 - Switch Security Configuration|Book 2 Module 11 — Switch Security Configuration]]

## Related Files

- [[Lab11a and B]]

## Цель

- Настроить DHCP пулы для нескольких подсетей на роутере
- Исключить статические адреса из пулов
- Проверить выдачу адресов клиентам (`show ip dhcp binding`)
- Настроить DHCP Snooping для защиты от rogue DHCP серверов

---

## Термины

**DHCP pool** — именованный набор настроек (сеть, шлюз, DNS, domain), из которого DHCP выдаёт адреса. #networkterm

**excluded-address** — диапазон адресов, зарезервированных и НЕ выдаваемых DHCP сервером. Используется для роутеров, свитчей, серверов. #networkterm

**DHCP lease** — аренда IP адреса. Клиент получает адрес на время (по умолчанию 1 день). #networkterm

**DHCP Snooping** — функция switch, фильтрующая DHCP сообщения. Только доверенные (trusted) порты могут посылать DHCP Offer. #networkterm

**rogue DHCP server** — несанкционированный DHCP сервер, который может выдавать неправильные адреса или шлюзы (атака MITM). #networkterm

---

## Решение

### Настройка DHCP Server на роутере

```cisco
enable
configure terminal

ip dhcp excluded-address 192.168.10.1 192.168.10.10
ip dhcp excluded-address 192.168.20.1 192.168.20.10
ip dhcp excluded-address 192.168.30.1 192.168.30.10

ip dhcp pool VLAN10
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 8.8.8.8
 domain-name vlan10.local

ip dhcp pool VLAN20
 network 192.168.20.0 255.255.255.0
 default-router 192.168.20.1
 dns-server 8.8.8.8
 domain-name vlan20.local

ip dhcp pool VLAN30
 network 192.168.30.0 255.255.255.0
 default-router 192.168.30.1
 dns-server 8.8.8.8
 domain-name vlan30.local

end
write memory
```

`ip dhcp excluded-address 192.168.10.1 192.168.10.10` #ciscoIOScommand Исключает адреса .1–.10 из пула. Эти адреса зарезервированы для статических устройств.

`ip dhcp pool VLAN10` #ciscoIOScommand Создаёт именованный DHCP пул и входит в режим конфигурации пула.

`network 192.168.10.0 255.255.255.0` #ciscoIOScommand Указывает сеть из которой выдаются адреса.

`default-router 192.168.10.1` #ciscoIOScommand Шлюз по умолчанию который получат DHCP клиенты.

`dns-server 8.8.8.8` #ciscoIOScommand DNS сервер для клиентов.

`domain-name vlan10.local` #ciscoIOScommand DNS суффикс домена для клиентов.

### Настройка DHCP Snooping на switch

```cisco
enable
configure terminal

ip dhcp snooping
ip dhcp snooping vlan 10,20,30

interface g0/1
 ip dhcp snooping trust

end
write memory
```

`ip dhcp snooping` #ciscoIOScommand Включает DHCP Snooping глобально.

`ip dhcp snooping vlan 10,20,30` #ciscoIOScommand Активирует Snooping на указанных VLANах.

`ip dhcp snooping trust` #ciscoIOScommand Помечает порт как trusted — DHCP Offer с этого порта разрешён (порт к роутеру или легальному серверу).

### Проверка

```cisco
show ip dhcp binding
show ip dhcp pool
show ip dhcp conflict
ip dhcp snooping binding
```

`show ip dhcp binding` #ciscoIOScommand Таблица выданных адресов: IP, MAC клиента, время аренды.

`show ip dhcp pool` #ciscoIOScommand Статистика пула: сколько адресов выдано, сколько свободно.

`show ip dhcp conflict` #ciscoIOScommand Показывает адреса, которые были в конфликте (уже использовались кем-то).

### Включить DHCP на конечных устройствах

PC → Desktop → IP Configuration → выбрать **DHCP**

> [!tip] Memory Hook
> Порядок DHCP: excluded-address → pool → network → default-router → dns-server. Всегда так.

> [!warning] Частая ошибка
> Если забыть `excluded-address`, DHCP сервер может выдать адрес роутера клиенту — будет конфликт адресов.

> [!danger] Exam Trap
> `ip dhcp snooping trust` ставится на порт к **DHCP серверу или роутеру**, не к клиентам. Все клиентские порты — untrusted по умолчанию.

---

> [!success] Итог
> Если понял лабу — умеешь настроить DHCP сервер с несколькими пулами, исключить статические адреса, проверить выдачу и защитить сеть от rogue DHCP через Snooping.

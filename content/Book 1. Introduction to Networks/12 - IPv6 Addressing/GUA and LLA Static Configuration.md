#book1
## 12.4.1 — Статическая настройка GUA на роутере

### Главная идея

Настройка IPv6 на Cisco IOS очень похожа на IPv4. Разница почти везде только одна: вместо `ip` пишем `ipv6`.

`ipv6 unicast-routing`
#ciscoIOScommand 

|IPv4|IPv6|
|---|---|
|`ip address 192.168.1.1 255.255.255.0`|`ipv6 address 2001:db8:acad:1::1/64`|

> ⚠️ Важно: между адресом и длиной префикса **нет пробела** → `2001:db8:acad:1::1/64`

---

### Топология примера

![[Screenshot 2026-04-16 at 15.30.06.png]]

Роутер **R1** имеет 3 интерфейса:

| Интерфейс             | Подсеть                | Адрес роутера |
| --------------------- | ---------------------- | ------------- |
| GigabitEthernet 0/0/0 | `2001:db8:acad:1::/64` | `::1`         |
| GigabitEthernet 0/0/1 | `2001:db8:acad:2::/64` | `::1`         |
| Serial 0/1/0          | `2001:db8:acad:3::/64` | `::1`         |

PC1 и PC2 находятся в разных подсетях (**:1::** и **:2::**) и подключены через свои коммутаторы к R1.

---

### Команды конфигурации

```
R1(config)# interface gigabitethernet 0/0/0
R1(config-if)# ipv6 address 2001:db8:acad:1::1/64
R1(config-if)# no shutdown
R1(config-if)# exit

R1(config)# interface gigabitethernet 0/0/1
R1(config-if)# ipv6 address 2001:db8:acad:2::1/64
R1(config-if)# no shutdown
R1(config-if)# exit

R1(config)# interface serial 0/1/0
R1(config-if)# ipv6 address 2001:db8:acad:3::1/64
R1(config-if)# no shutdown
```

**Логика адресов:** `2001:db8:acad:1::1/64` расшифровывается так:

- `2001:db8:acad` — Global Routing Prefix (провайдерский префикс)
- `1` — Subnet ID (номер подсети)
- `::1` — Interface ID (адрес роутера в этой подсети, то есть `...0000:0000:0000:0001`)
- `/64` — первые 64 бита это сеть, остальные 64 — хосты

---

## 📌 12.4.2 — Статическая настройка GUA на Windows-хосте

![[Screenshot 2026-04-16 at 15.36.18.png]]

Настройка IPv6 на компьютере практически идентична IPv4. В окне **TCP/IPv6 Properties** вводим:

|Поле|Значение (пример для PC1)|
|---|---|
|IPv6 address|`2001:db8:acad:1::10`|
|Subnet prefix length|`64`|
|Default gateway|`2001:db8:acad:1::1`|

**Default gateway** — здесь указан GUA роутера (`::1`). Но лучшей практикой считается указывать **LLA роутера** (`fe80::1:1`). Оба варианта работают.

**Динамические способы получить GUA** (для крупных сетей, вручную не масштабируется):

- **SLAAC** — Stateless Address Autoconfiguration #abbreviation 
- **Stateful DHCPv6**

> 💡 При использовании SLAAC или DHCPv6 — default gateway выставляется автоматически как **LLA роутера**.

---

## 📌 12.4.3 — Статическая настройка LLA на роутере

Зачем настраивать LLA вручную? Чтобы адреса были **читаемыми и понятными** — по умолчанию LLA генерируется автоматически и выглядит как длинная непонятная строка.

**Команда:**

```
ipv6 address <адрес> link-local
```

Ключевое слово `link-local` обязательно, если адрес начинается с `fe80` — `febf`.

### Конфигурация R1:

![[Screenshot 2026-04-16 at 15.39.14.png]]

```
R1(config)# interface gigabitethernet 0/0/0
R1(config-if)# ipv6 address fe80::1:1 link-local
R1(config-if)# exit

R1(config)# interface gigabitethernet 0/0/1
R1(config-if)# ipv6 address fe80::2:1 link-local
R1(config-if)# exit

R1(config)# interface serial 0/1/0
R1(config-if)# ipv6 address fe80::3:1 link-local
R1(config-if)# exit
```

### Логика именования:

Все интерфейсы R1 имеют LLA вида `fe80::n:1`, где:

- `n` — номер интерфейса (1 = G0/0/0, 2 = G0/0/1, 3 = S0/1/0)
- `1` — означает, что это роутер R1

Это позволяет сразу понять: **какой роутер** и **какой интерфейс**.

> ⚠️ LLA должен быть уникальным только **в пределах одного канала (link)**. Технически на каждом интерфейсе можно использовать одинаковый LLA, но на практике лучше делать разные — для удобства идентификации.


#computernetworks 

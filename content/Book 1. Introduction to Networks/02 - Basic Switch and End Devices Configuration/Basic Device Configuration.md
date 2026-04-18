#book1
## 2.4.1 Имя устройства (hostname)

По умолчанию все коммутаторы называются `Switch`, все роутеры — `Router`. Это неудобно — непонятно, к какому устройству ты подключён.

**Правила имени:**

- Начинается с буквы
- Нет пробелов
- Заканчивается буквой или цифрой
- Только буквы, цифры и дефисы
- Не длиннее 64 символов

**Команды:**

```
Switch# configure terminal
Switch(config)# hostname Sw-Floor-1
Sw-Floor-1(config)#          ← имя сразу изменилось в промпте
```

Чтобы вернуть имя по умолчанию: `no hostname`

---

## 2.4.2 Требования к паролям

- Длиннее 8 символов
- Комбинация букв (верхний/нижний регистр), цифр, спецсимволов
- Разные пароли на разных устройствах
- Не использовать словарные слова

> В лабах курса используют `cisco` и `class` — только для учёбы, в реальной сети так нельзя.

---

## 2.4.3 Настройка паролей

Три места, которые нужно защитить паролем:

**1. Console (физический вход)**

```
Sw-Floor-1# configure terminal
Sw-Floor-1(config)# line console 0
Sw-Floor-1(config-line)# password cisco
Sw-Floor-1(config-line)# login
Sw-Floor-1(config-line)# end
```

**2. Privileged EXEC (команда `enable`)**

```
Sw-Floor-1# configure terminal
Sw-Floor-1(config)# enable secret class
Sw-Floor-1(config)# exit
```

> `enable secret` — хранится в зашифрованном виде, в отличие от `enable password`

**3. VTY-линии (удалённый доступ SSH/Telnet)**

```
Sw-Floor-1# configure terminal
Sw-Floor-1(config)# line vty 0 15
Sw-Floor-1(config-line)# password cisco
Sw-Floor-1(config-line)# login
Sw-Floor-1(config-line)# end
```

> `0 15` — все 16 линий сразу (0 до 15)

---

## 2.4.4 Шифрование паролей

По умолчанию пароли в конфиг-файле хранятся **открытым текстом**. Чтобы зашифровать:

```
Sw-Floor-1(config)# service password-encryption
```

После этого в `show running-config` пароли выглядят так:

```
password 7 094F471A1A0A
```

> Цифра `7` означает слабое шифрование (Cisco Type 7). Защищает от беглого взгляда, но не от серьёзного взлома. `enable secret` использует MD5 — надёжнее.

![[Screenshot 2026-04-13 at 15.00.50.png]]

---

## 2.4.5 Баннер (MOTD)

Баннер — это предупреждение, которое видит каждый, кто подключается к устройству. Нужен по юридическим причинам: без него в некоторых странах нельзя преследовать взломщика.

```
Sw-Floor-1(config)# banner motd #Authorized Access Only#
```

- `#` — символ-разделитель (можно использовать любой символ, которого нет в тексте баннера)
- Текст между `#` — сам баннер
- Отображается при каждом подключении до удаления баннера

#computernetworks 

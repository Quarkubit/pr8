## Настройка сервера

### Конфигурационные файлы

1. **Серверный конфиг** `/etc/myRPC/myRPC.conf`:
   ```conf
   # Порт сервера
   port = 1234
   # Тип сокета: stream (TCP) или dgram (UDP)
   socket_type = stream
   ```

2. **Список разрешённых пользователей** `/etc/myRPC/users.conf`:
   ```conf
   # Разрешённые пользователи
   john
   alice
   ```

### Настройка как службы (systemd)

1. Создайте файл службы `/etc/systemd/system/myrpc-server.service`:
   ```ini
   [Unit]
   Description=myRPC Server
   After=network.target

   [Service]
   ExecStart=/usr/bin/myrpc-server
   Restart=always
   User=root
   Group=root

   [Install]
   WantedBy=multi-user.target
   ```

2. Активируйте службу:
   ```bash
   sudo systemctl daemon-reload
   sudo systemctl enable myrpc-server
   sudo systemctl start myrpc-server
   ```

---

## Использование

### myRPC-client

#### Примеры использования:

1. **Текстовый протокол (TCP):**
   ```bash
   myRPC-client -c "ls -la" -h "127.0.0.1" -p 1234 -s
   ```

2. **JSON-протокол (TCP):**
   ```bash
   myRPC-client -c "date" -h "127.0.0.1" -p 1234 -s --format json
   ```

3. **UDP-сокет:**
   ```bash
   myRPC-client -c "hostname" -h "127.0.0.1" -p 1234 -d
   ```

#### Аргументы командной строки:

| Флаг | Описание |
|------|----------|
| `-c`, `--command` | Команда для выполнения на сервере |
| `-h`, `--host` | IP-адрес сервера |
| `-p`, `--port` | Порт сервера |
| `-s`, `--stream` | Использовать TCP-сокет |
| `-d`, `--dgram` | Использовать UDP-сокет |
| `--format json` | Отправить запрос в JSON-формате |
| `--help` | Показать справку |

---

### myRPC-server

1. Запустите сервер:
   ```bash
   sudo myRPC-server
   ```

2. Логи сервера хранятся в `/var/log/myrpc.log`.

---

## Логирование и отладка

### Форматы логирования

1. **Текстовый формат**:
   ```
   Wed Jun 30 21:49:08 1993 INFO 0 Сервер запущен
   ```

2. **JSON-формат**:
   ```
   {"timestamp":"Wed Jun 30 21:49:08 1993","log_level":"INFO","driver":0,"message":"Сервер запущен"}
   ```

### Уровни логирования

| Уровень | Описание |
|--------|----------|
| `DEBUG` | Отладочная информация |
| `INFO` | Информационные сообщения |
| `WARN` | Предупреждения |
| `ERROR` | Ошибки |
| `CRITICAL` | Критические ошибки |

---

## Тестирование

### Проверка доступа

1. **Разрешённый пользователь**:
   ```bash
   myRPC-client -c "whoami" -h "127.0.0.1" -p 1234 -s
   ```

2. **Запрещённый пользователь**:
   ```bash
   sudo -u forbidden_user myRPC-client -c "whoami" -h "127.0.0.1" -p 1234 -s
   ```

### Проверка логов
```bash
tail -f /var/log/myrpc.log
```

### Проверка временных файлов
```bash
ls /tmp/myRPC_*.stdout
ls /tmp/myRPC_*.stderr
```

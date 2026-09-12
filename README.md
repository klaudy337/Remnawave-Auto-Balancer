# Remnawave-Auto-Balancer
An auto-balancer is used to connect users to specific hosts. Thanks to the auto-balancer, you reduce the load on your servers.
# Подготовка
Для автобалансера вам нужно иметь минимум 3 подключенных ноды к вашей панели.
После того как подключили 3 ноды можете идти к следующему шагу

# Установка
В панели Remnawave открываем Подписка -> Шаблоны -> XRAY JSON

Создаем новый профиль (называем как хотим)

Удаляем дефолтный текст и вставляем этот:
```bash 
{
  "dns": {
    "servers": [
      "1.1.1.1",
      "1.0.0.1"
    ],
    "queryStrategy": "UseIP"
  },
  "routing": {
    "rules": [
      {
        "type": "field",
        "protocol": [
          "bittorrent"
        ],
        "outboundTag": "direct"
      },
      {
        "type": "field",
        "network": "tcp,udp",
        "balancerTag": "Super_Balancer"
      }
    ],
    "balancers": [
      {
        "tag": "Super_Balancer",
        "selector": [
          "proxy"
        ],
        "strategy": {
          "type": "leastLoad",
          "settings": {
            "maxRTT": "1s",
            "expected": 2,
            "baselines": [
              "1s"
            ],
            "tolerance": 0.01
          }
        },
        "fallbackTag": "direct"
      }
    ],
    "domainMatcher": "hybrid",
    "domainStrategy": "IPIfNonMatch"
  },
  "inbounds": [
    {
      "tag": "socks",
      "port": 10808,
      "listen": "127.0.0.1",
      "protocol": "socks",
      "settings": {
        "udp": true,
        "auth": "noauth"
      },
      "sniffing": {
        "enabled": true,
        "routeOnly": false,
        "destOverride": [
          "http",
          "tls",
          "quic"
        ]
      }
    },
    {
      "tag": "http",
      "port": 10809,
      "listen": "127.0.0.1",
      "protocol": "http",
      "settings": {
        "allowTransparent": false
      },
      "sniffing": {
        "enabled": true,
        "routeOnly": false,
        "destOverride": [
          "http",
          "tls",
          "quic"
        ]
      }
    }
  ],
  "outbounds": [
    {
      "tag": "direct",
      "protocol": "freedom"
    },
    {
      "tag": "block",
      "protocol": "blackhole"
    }
  ],
  "remnawave": {
    "injectHosts": [
      {
        "selector": {
          "type": "uuids",
          "values": [
            "[B]СЮДА ВСТАВИТЬ СВОИ ДАННЫЕ С ХОСТА[/B]",
            "[B]СЮДА ВСТАВИТЬ СВОИ ДАННЫЕ С ХОСТА[/B]",
            "[B]СЮДА ВСТАВИТЬ СВОИ ДАННЫЕ С ХОСТА[/B]"
          ]
        },
        "tagPrefix": "proxy",
        "selectFrom": "ALL"
      }
    ]
  },
  "burstObservatory": {
    "pingConfig": {
      "timeout": "3s",
      "interval": "1m",
      "sampling": 1,
      "destination": "http://www.gstatic.com/generate_204",
      "connectivity": ""
    },
    "subjectSelector": [
      "proxy"
    ]
  }
}
```
В поле values нужно вставить id хостов для подключения(они находятся под именем хоста).

# Создание хоста

Клонируем любой имеющийся хост.

Ничего не трогаем, меняем только название.

Далее расширенное -> листаем ниже -> находим Xray Json & Raw и выбираем шаблон балансировщика.

# Успешная установка

Обновите подписку в вашем конфигураторе.

Если вы все сделали правильно балансировщик будет пинговаться и коннектить пользователей к 1 из ваших хостов которые вы указали.

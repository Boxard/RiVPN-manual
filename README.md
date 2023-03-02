# RiVPN-manual

Конфигурация RiVPN для выхода в интернет с удаленного сервера через сеть Yggdrasil
===========

Первое что нужно сделать это установить RiVPN на вашем ПК и на удаленном сервевре. В данном мануале это опустим.

## Конфигурация ПК с OS Windows

#### :black_square_button: 1. Переходим по пути: ```C:\ProgramData\RiV-mesh```
Открываем файл ```mesh.conf``` через любой текстовый редактор и копируем в буфер обмена публичный ключ нашего ПК.


```
# Your public key. Your peers may ask you for this to put
  # into their AllowedPublicKeys configuration.
  PublicKey: ваш паблик кей тут
```
#### :black_square_button: 2. На удаленном сервере переходим по пути ```/etc/mesh.conf```
Открываем файл и копируем ```PublicKey``` сервера, он находится в том же блоке что и в первом шаге

#### :black_square_button: 3. Возвращаемся обратно на ПК под управлением OS Windows

В конфиге ```mesh.conf``` листаем в самый низ к блоку ```FeaturesConfig```

В нем начинается самое интересное :)

Копируем данный кусок и вставляем в свой конфиг редактируя его под свои значения ```PublicKey```
```
FeaturesConfig:
  {
    TunnelRouting:
    {
      # Enable or disable tunnel routing.
      Enable: true

      # IPv6 subnets belonging to remote nodes, mapped to the node's public
      # key, e.g. { "aaaa:bbbb:cccc::/e": "boxpubkey", ... }
      IPv6RemoteSubnets: {}

      # IPv4 subnets belonging to remote nodes, mapped to the node's public
      # key, e.g. { "a.b.c.d/e": "boxpubkey", ... }
      IPv4RemoteSubnets:
      {
      # Пример: "0.0.0.0/0": 0000205555011e30fc72d3d6220e316cf2fce7ddadd63935ab58511f383c1bb4
        "0.0.0.0/0": PublicKey вашего удаленного сервера, который мы уже скопировали во 2 шаге. 
      }
    }
  }
```

Сохраняем конфиг через текстовый редактор и в диспетчере задач останавливаем службу ```Mesh``` с описанием RiV-mesh Service
#### :black_square_button: 4. Редактировавние реестра

Открываем редактор реестра через комбинацию клавиш ```Win + R``` и вводим туда ```regedit``` после чего нажимаем enter и попадаем в редактор реестра

В редакторе реестра переходим по пути 
```Компьютер\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters``` в нем нас интересует параметр ```IPEnableRouter```

В этом параметре нужно установить значение ```0``` и применить изменения нажатием клавишы ОК

<img width="638" alt="123" src="https://user-images.githubusercontent.com/122159872/222551112-825cf1ab-ef6c-45b0-9551-1364f2a0fbee.png">

На этом настройка RiVPN в режиме клиента под OS Windows окончена.


## Конфигурация удаленного сервера

#### :black_square_button: 1. Настраиваем блок ```FeaturesConfig```

Перед настройкой нужно узнать IPv4 адрес, который выдал RiVPN на ПК под OS Windows, для этого переходим в ```cmd``` и вводим туда команду ```ipconfig```, ищем интерфейс RiVPN и копируем IPv4 адрес, который начинается на ```10.x.x.x``` в моем примере это 10.145.145.145
В данном блоке со стороны сервера нужно вставить данный кусок конфигурации:
```
FeaturesConfig:
  {
    TunnelRouting:
    {
      # Enable or disable tunnel routing.
      Enable: true

      # IPv6 subnets belonging to remote nodes, mapped to the node's public
      # key, e.g. { "aaaa:bbbb:cccc::/e": "boxpubkey", ... }
      IPv6RemoteSubnets: {}

      # IPv4 subnets belonging to remote nodes, mapped to the node's public
      # key, e.g. { "a.b.c.d/e": "boxpubkey", ... }
      IPv4RemoteSubnets: {
      "10.145.145.145/32": PublicKey вашего ПК под OS Windows
    }
    }
  }
```

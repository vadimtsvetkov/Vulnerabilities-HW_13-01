#Vulnerabilities-HW_13-01
## VADIM TSVETKOV

«Уязвимости и атаки на информационные системы»

**Задание 1.**

Скачаем ВМ Metasploitable2 (Linux) и подключим ее в Virtual Box:

![img](https://github.com/vadimtsvetkov/Vulnerabilities-HW_13-01/blob/main/metasploitable.jpg)

Узнаем IP адрес:

![img](https://github.com/vadimtsvetkov/Vulnerabilities-HW_13-01/blob/main/ip.jpg)

Просканируем ВМ с помощью утилиты nmap:
sudo nmap -sV 10.0.2.10

![img](https://github.com/vadimtsvetkov/Vulnerabilities-HW_13-01/blob/main/nmap.jpg)

В ВМ разрешены следующие службы:

ftp
ssh
telnet
smtp
domain
http
rpcbind
net-bios-ssn
exec
login
tcpwrapped
bindshell
nfs
mysql
postgresql
vnc
X11
irc
ajp13

Примеры обнаруженных уязвимостей:

vsftpd 2.3.4 - Backdoor Command Execution
ISC BIND 9 - Denial of Service
WinVNC Web Server 3.3.3r7 - GET Overflow (Metasploit)

![img](https://github.com/vadimtsvetkov/Vulnerabilities-HW_13-01/blob/main/vsftpd.jpg)
![img](https://github.com/vadimtsvetkov/Vulnerabilities-HW_13-01/blob/main/ISC.jpg)
![img](https://github.com/vadimtsvetkov/Vulnerabilities-HW_13-01/blob/main/vnc.jpg)

**Задание 2.**

Проведем сканирование nmap в режиме SYN:
sudo nmap -sS 10.0.2.10

SYN это используемый по умолчанию и наиболее популярный тип сканирования. На то есть несколько причин. Он может быть быстро запущен, он способен сканировать тысячи портов в секунду при быстром соединении, его работе не препятствуют ограничивающие бранмауэры. Этот тип сканирования относительно ненавязчив и незаметен, т.к. при таком сканировании TCP соединение никогда не устанавливается до конца. Он работает с любым TCP стеком, не завися от каких-либо особенностей специфичной платформы, как это происходит при сканированиях типа FIN/NULL/Xmas, Maimon и idle сканировании. Он также предоставляет ясную и достоверную дифференциацию между состояниями открыт, закрыт и фильтруется. Ответы SYN/ACK указывают на то, что порт прослушивается (открыт), а RST (сброс) на то, что не прослушивается. Если после нескольких запросов не приходит никакого ответа, то порт помечается как фильтруемый

![img](https://github.com/vadimtsvetkov/Vulnerabilities-HW_13-01/blob/main/syn.jpg)

Проведем сканирование nmap в режиме FIN:
sudo nmap -sA 10.0.2.10

Пакет запроса при таком типе сканирования содержит установленным только ACK флаг. При сканировании нефильтруемых систем, открытые и закрытые порты оба будут возвращать в ответ RST пакет. Nmap помечает их как не фильтруемые, имея ввиду, что они достижимы для ACK пакетов, но неизвестно открыты они или закрыты. Как и предудущий тип сканирования (SYN-сканирование) можно отнести к быстрому типу сканирования, так как не устанавливает соединение.

![img](https://github.com/vadimtsvetkov/Vulnerabilities-HW_13-01/blob/main/fin.jpg)

Проведем сканирование nmap в режиме Xmas:
sudo nmap -sX 10.0.2.10

Устанавливаются FIN, PSH и URG флаги. Если в ответ приходит RST пакет, то порт считается закрытым, отсутствие ответа означает, что порт открыт|фильтруется. Порт помечается как фильтруется, если в ответ приходит ICMP ошибка о недостижимости. Пожалуй следует отнести к медленному типу сканирования. Так как гораздо медленнее SYN-сканирования и FIN-сканирования, но в тоже время гораздо быстрее UDP-сканирования.

![img](https://github.com/vadimtsvetkov/Vulnerabilities-HW_13-01/blob/main/xmas.jpg)

Проведем сканирование nmap в режиме UDP:
sudo nmap -sU 10.0.2.10

UDP сканирование работает путем посылки пустого (без данных) UDP заголовка на каждый целевой порт. Если в ответ приходит ICMP ошибка о недостижимости порта, значит порт закрыт. Другие ICMP ошибки недостижимости указывают на то, что порт фильтруется. Иногда, служба будет отвечать UDP пакетом, указывая на то, что порт открыт. Если после нескольких попыток не было получено никакого ответа, то порт классифицируется как открыт|фильтруется. Это означает, что порт может быть открыт, или, возможно, пакетный фильтр блокирует его. Большой проблемой при UDP сканировании является его медленная скорость работы. Открытые и фильтруемые порты редко посылают какие-либо ответы, заставляя Nmap отправлять повторные запросы, на случай если пакеты были утеряны.

![img](https://github.com/vadimtsvetkov/Vulnerabilities-HW_13-01/blob/main/udp.jpg)

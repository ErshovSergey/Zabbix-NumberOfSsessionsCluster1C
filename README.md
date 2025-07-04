# Мониторинг кластера 1С с помощью zabbix_agent2.  
## Как работает  
Проверялось на ОС WIndows. С большой вероятностью заработает на Linux.  

Zabbix через zabbix_agent2 (установленный на узле с кластером или на другом узле, который имеет доступ к кластеру 1С) с помощью утилиты rac из комплекта платформы 1С подключается к серверу приложений 1С и получает список всех сессий кластера 1С в кластере по определенным полям.  
Список сессий разбирается на стороне сервера zabbix.  
Списое баз кластера 1С обновляется автоматически.  
Для облегчения из кластера получается UUID, но установить значение макроса CLUSTER1C.UUID необходимо вручную т.к. вложенный LLD  в zabbix версии 7.0 не существует.  
# На сервера приложений кластера 1С  
- обеспечить работу утилиты rac для получения данных в кластере 1С, проверить работу можно так

```"C:\Program Files\1cv8\ПУТЬ_ДО\rac.exe" infobase --cluster=CLUSTER1C.UUID --cluster-user=CLUSTER1C.USER --cluster-pwd=CLUSTER1C.PWD  summary list```

должен  быть выведен список имеющихся баз в кластере 1С.

- установить агент zabbix_agent2 на сервер кластера или на узел, который имеет доступ до сервера кластера
- разместить в ...\ZabbixAgent2\zabbix_agentd.conf.d файл 
```
##################################
UnsafeUserParameters=1
# 2025-07-04
# UID кластера
UserParameter=CLUSTER1C.GET.UUID[*],"$1" "$2:$3" cluster list  | find  "cluster"
# Список баз кластера
#UserParameter=CLUSTER1C.GET.LIST.BASE[*],chcp 65001>0 & "$1" "$2:$3" infobase --cluster="$4" --cluster-user="$5" --cluster-pwd="$6" summary list
# Список сессий (выводятся только определенные параметры каждой сессии)
UserParameter=CLUSTER1C.GET.LIST.SESSION[*],chcp 65001>0 & "$1" "$2:$3" session --cluster="$4"  --cluster-user="$5" --cluster-pwd="$6" list --infobase="$7" | findstr /c:"session " /c:"infobase" /c:"user-name" /c:"host" /c:"app-id" /c:"memory-current"
```  
- проверить наличии в zabbix_agent2.conf наличие строки  
```Include=...\ZabbixAgent2\zabbix_agentd.conf.d```  
- перезапустить zabbix_agent2, проверить логи на предмет отсутствия ошибок  
## На сервере zabbix  
- импортировать шаблон в zabbix  
- для узла с сервером приложений 1С добавить шаблон  
- Изменить макросы для узла с агентом (при необходимости)  
{$RAC.EXE} - Путь к утилите rac, без кавычек.  
{$HOST} - ip адрес/FQDN узла на котором работает утилита rac - возможно использование zabbix_agent2 на внешнем по отношению к серверу приложений узле.  
{$PORT} - Порт на котором работает утилита rac  
{$CLUSTER1C.USER} -  Логин пользователя кластера 1С.  
{$CLUSTER1C.PWD} - Пароль пользователя кластера 1С.  
{$CLUSTER1C.UUID} - UUID кластера 1С.  
Пороги срабатывания триггера на количество сессий с memory-current не равное 0  
{$CLUSTER.NOTNULL.MEMORYCURRENT.INFO}  
{$CLUSTER.NOTNULL.MEMORYCURRENT.WARNING}  
{$CLUSTER.NOTNULL.MEMORYCURRENT.DISASTER}  

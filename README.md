## Построение графиков количества сессий во всех базах кластера 1С-Предпрятия v.8.
Количество сессий в базе 1С-Кластер можно получить с помощью службы сервера удаленного администрирования RAS(Remote Aadministration Server).  
Доступ к RAS можно получить с помощью RAC(Remote Administration Client).  
Т.о. через zabbix агент можно получать данные из кластера 1С - Zabbixagent->RAC->RAS-Cluster1C.  
Файл для агента [zabbix cluster1-base-session.conf](./zabbix cluster1-base-session.conf) - разместить в папке _<FolderZabbixAgent>\zabbix_agentd.conf.d\_.  
Файл [шаблона](Zabbix-NumberOfSsessionsCluster1C.yaml) для Zabbix (проверено в версии 5.2).  

### Порядок работ
#### Запустить службу сервер удаленного администрирования RAS
#### Проверить доступ утилитой RAC
#### Добавить в zabbixagent файл с пользовательскими скриптами????
#### Добавить шаблон
#### Дождаться получения UUID кластера и определить в макросах сервера

  

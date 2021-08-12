## Построение графиков количества сессий во всех базах кластера 1С-Предпрятия v.8.
Количество сессий в базе 1С-Кластер можно получить с помощью службы сервера удаленного администрирования RAS(Remote Aadministration Server).  
Доступ к RAS можно получить с помощью RAC(Remote Administration Client).  
Т.о. через zabbix агент можно получать данные из кластера 1С  
    Zabbixagent<-->RAC<-->RAS<-->Cluster1C.  
Файл для агента [zabbix cluster1-base-session.conf](./zabbix-cluster1-base-session.conf) - разместить в папке ```<FolderZabbixAgent>\zabbix_agentd.conf.d\```.  
Файл [шаблона](./Zabbix-NumberOfSsessionsCluster1C.yaml) для Zabbix (проверено в версии 5.2).  
  
### Порядок работ
Версии RAC, RAS и сервера должны быть одинаковы.
#### Запустить службу сервер удаленного администрирования RAS
##### Установка службы  
```
sc create "1C:Enterprise RAS" binpath= "C:\Program Files\1cv8\Х.Х.Х.ХХХХ\bin\ras.exe cluster --service" displayname= "1C:Enterprise RAS" start= auto 
net start "1C:Enterprise RAS"
```
#### Проверить доступ утилитой RAC  
На узле с RAS запустив  
```"C:\Program Files\1cv8\8.3.Х.ХХХХ\bin\rac.exe" cluster list```
должны получить примерно следующее
  
#### Добавить в zabbixagent файл с пользовательскими скриптами????
положить файл [zabbix cluster1-base-session.conf](./zabbix cluster1-base-session.conf) в 
#### Добавить шаблон
#### Настроить шаблон
- указать верный путь до rac.
- дождаться получения UUID кластера и определить в макросах сервера

  

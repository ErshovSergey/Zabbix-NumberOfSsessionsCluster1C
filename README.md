## Построение графиков количества сессий во всех базах кластера 1С-Предпрятия v.8.
Количество сессий в базе 1С-Кластер можно получить с помощью службы сервера удаленного администрирования RAS(Remote Aadministration Server).  
Доступ к RAS можно получить с помощью RAC(Remote Administration Client).  
Т.о. через zabbix агент можно получать данные из кластера 1С  
    Zabbixagent<-->RAC<-->RAS<-->Cluster1C.  
Файл для агента [cluster1-base-session.conf](./cluster1-base-session.conf) - разместить в папке ```<FolderZabbixAgent>\zabbix_agentd.conf.d\```.  
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
```"c:\Program Files\1cv8\8.3.XXX.XXX\bin\rac.exe" cluster list```  
должны получить примерно следующее  
```cluster                       : d555dcae-XXX-XXXX-XXXX-XXXXXXXXXX```  
#### Добавить в zabbixagent файл с пользовательскими скриптами
- положить файл [cluster1-base-session.conf](./cluster1-base-session.conf) в <Path_to_ZabbixAgent>\zabbix_agentd.conf.d\  
- перезапустить zabbixagent  
- проверить работу RAC командой с Zabbix сервера  
```zabbix_get -s XX.XX.XXX.XXX -p 10050 -k UUID["c:\Program Files\1cv8\8.3.XXX.XXX\bin\rac.exe","localhost","1545"]```  
Должно получиться что-то типа  
```cluster                       : d555dcae-XXX-XXXX-XXXX-XXXXXXXXXX```  

#### Добавить и настроить шаблон  
- к узлу кластера 1С в Zabbix добавить шаблон
- проверить путь до RAC - макросы {$HOST}, {$PORT}, {$RAC.EXE}
- дождаться получения UUID кластера
- заполнить макрос {$CLUSTER1C.UUID} - добавить макрос к узлу
### To-Do
- вероятно всё это заработает на \*NIX системах - требуется проверить
- вынести пороги триггеров в макросы - пока не понадобилось
- настроить пороги триггеров через макросы
  

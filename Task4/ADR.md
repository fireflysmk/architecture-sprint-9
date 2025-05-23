﻿### <a name="_b7urdng99y53"></a>**Название задачи:** task-4
### <a name="_hjk0fkfyohdk"></a>**Автор:** Кузнецов Антон
### <a name="_uanumrh8zrui"></a>**Дата:** 03.04.2025
### <a name="_3bfxc9a45514"></a>**Функциональные требования**

Опишите здесь верхнеуровневые Use Cases. Их нужно оформить в виде таблицы с пошаговым описанием:

|**№**|**Действующие лица или системы**|**Use Case**|**Описание**|
| :-: | :- | :- | :- |
|1|	АБС → Интеграционный сервис|	Выгрузка ставок|	Интеграционный сервис забирает данные из АБС по расписанию (раз в час)|
|2|	Интеграционный сервис → SFTP|	Публикация файла|	Сервис выгружает XLS-файл на SFTP с PGP-шифрованием|
|3|	CRM кол-центра → Интеграционный сервис|	Запрос ставок|	Вызов REST API для получения актуальных данных (минуя SFTP)|
|4|	Партнерский кол-центр → SFTP|	Импорт файла|	Автоматическая загрузка файла раз в сутки|

### <a name="_u8xz25hbrgql"></a>**Нефункциональные требования**
Опишите здесь нефункциональные требования и архитектурно-значимые требования.

|**№**|**Требование**|
| :-: | :- |
|1|	Шифрование файлов при передаче (PGP или SSL/TLS для SFTP)|
|2|	Доступность SFTP-сервера 99.5% (ежедневное окно обслуживания ≤ 10 мин)|
|3|	Задержка обновления ставок в CRM кол-центра ≤ 1 сек (кэширование)|
|4|	Совместимость формата файла (CSV с фиксированной структурой)|
### <a name="_qmphm5d6rvi3"></a>**Решение**
[диаграмма контекста](./Context_Diagram.puml)  
[диаграмма контейнеров](./Container_Diagram.puml)  

- SFTP выбран для партнера из-за отсутствия API-интеграции  
- REST API для внутреннего кол-центра — для минимизации задержек  
- экспорт по расписанию(раз в час) вместо реального времени — чтобы снизить нагрузку на АБС  
- Новый компонент "Интеграционный сервис": Разработан на Python (совместимость с текущим стеком)
  
  
- ### <a name="_bjrr7veeh80c"></a>**Альтернативы**
- Прямая интеграция партнера с АБС через API, получим реальное время обновления, но есть проблема, в текущем виде не поддерживается партнером  
- Использование email для передачи файлов: просто, но не безопасность (крайне не рекомендуется)

**Недостатки, ограничения, риски**

- Задержки в обновлении ставок у партнера → контроль через логирование выгрузок  
- Перегрузка АБС при частых API-запросах → лимиты на количество вызовов  


---
title: Справочник аудитных логов {{ yq-full-name }} в {{ at-full-name }}
description: На этой странице приведен справочник событий сервиса {{ yq-name }}, отслеживаемых в {{ at-name }}.
---

# Справочник аудитных логов {{ at-full-name }}

В {{ at-name }} поддерживается отслеживание событий уровня конфигурации (Control Plane) для {{ yq-full-name }}. Подробнее см. [{#T}](../audit-trails/concepts/format.md).

Общий вид значения поля `event_type` (_тип события_):

```text
{{ at-event-prefix }}.audit.yq.<имя_события>
```

{% include [yq-events](../_includes/audit-trails/events/yq-events.md) %}
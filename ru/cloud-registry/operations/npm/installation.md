# Настроить npm

Чтобы сделать настройку:

* для всех проектов, выполните команду:

    `npm config set registry {{ cloud-registry }}/npm/<идентификатор_реестра>`

* для одного проекта:

    1. Создайте файл `.npmrc` в корне проекта.

    1. Добавьте в файл `.npmrc` строку `registry={{ cloud-registry }}/npm/<идентификатор_реестра>`.

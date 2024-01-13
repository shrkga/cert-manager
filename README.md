# Ingress-NGINX и Cert-manager

> [Основное README проекта здесь](../search_engine_deploy/-/blob/main/README.md)

Подключение к веб-интерфейсам приложения и системы мониторинга осуществляется через сетевой балансировщик на базе контроллера Ingress-NGINX по протоколу `https` с использованием TLS сертификатов Let's Encrypt.

Для этой цели первым делом в кластере устанавливается Ingress-NGINX контроллер из официального Helm Chart'а:
```
helm upgrade --install ingress-nginx ingress-nginx \
  --repo https://kubernetes.github.io/ingress-nginx \
  --namespace ingress-nginx --create-namespace
```

Следующим шагом осуществляется развертывание в кластер дополнения `Cert-manager` и объявление ресурсов центров сертификации (CA) типа `ClusterIssuer`. В данном проекте используется ClusterIssuer т.к. данный ресурс является глобальным объектом кластера, и область его видимости распространяется на все пространства имен.

Чтобы запустился пайплайн автоматического развертывания в кластере Cert-manager, необходимо выполнить `git push` в репозиторий `cert-manager` с тэгом соответствующим версии релиза данного дополнения. На текущий момент Latest релизом является `v1.13.3` (см. <https://github.com/cert-manager/cert-manager/releases/latest>). В результате выполнятся стадии пайплайна `deploy` (развернуть, или обновить Cert-manager) и `apply` (применить манифесты ресурсов ClusterIssuer). Если запушить в репозиторий изменения без тэга, выполняется только стадия `apply`.

```
docker run --detach --publish 8050:80 --restart=no --name owasp-bwapp --network domain-bridge hackersploit/bwapp-docker
```
После разворачивания контейнера необходимо проинициализировать приложение, открыв в браузере страницу http://test.domain.org:8050/install.php

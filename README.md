# StaticJinjaPlus-Docker-Port

Этот репозиторий содержит несколько Dockerfile для создания различных образов проекта StaticJinjaPlus. Каждый образ предназначен для работы с разными версиями и базовыми образами.

## Как собрать

Docker должен быть установлен на вашей системе.
Существует 4 Dockerfile с разными базовыми образами для сборки образов с разными версиями проекта. Два из них основаны на образе Ubuntu 20.04: Dockerfile-latest и Dockerfile-develop. А два других основаны на образе Python 3.12.8-slim: Dockerfile-slim и Dockerfile--develop-slim.

Чтобы собрать образы, используйте следующие команды в директории с соответствующими Dockerfile:

```bash
# Сборка образа develop на основе Ubuntu
docker build -t static-jinja-plus:develop -f Dockerfile.develop .

# Сборка образа develop-slim на основе python-slim
docker build -t static-jinja-plus:develop-slim -f Dockerfile.develop-slim .

# Сборка образа latest на основе Ubuntu
docker build -t static-jinja-plus:latest -f Dockerfile.latest .

# Сборка образа slim на основе python-slim
docker build -t static-jinja-plus:slim -f Dockerfile.slim .
```
### Сборка с конкретной версией

```bash
# 1. Получите хэш для нужной версии
export VERSION="0.1.1"
CHECKSUM=$(curl -sL https://github.com/AndreyBychenkow/StaticJinjaPlus/archive/${VERSION}.tar.gz | sha256sum | cut -d' ' -f1) 
```

```bash
# 2. Соберите образ
docker build -f Dockerfile.ubuntu \
  --build-arg APP_VERSION=${VERSION} \
  --build-arg CHECKSUM=sha256:${CHECKSUM} \
  -t staticjinjaplus:${VERSION} .
```

## Как запустить

Перейдите в папку, где находятся ваши шаблоны, и запустите контейнер:

```bash
docker run -it --rm \
  -v $(pwd)/MyTemplates:/app/MyTemplates \
  -v $(pwd)/output:/app/MyAssembly \
  staticjinjaplus:latest
```
В результате папка сборки с отрендеренными файлами будет создана рядом с папкой шаблонов.
Также возможно отслеживать изменения файлов в папке шаблонов и повторно рендерить их в папке сборки. Для этого запустите контейнер следующим образом:

```bash
docker run --rm -v $(pwd):/app/StaticJinjaPlus/data static-jinja-plus:[IMAGE_VERSION] -w --srcpath=data/MyTemplates/ --outpath=data/MyAssembly/
```

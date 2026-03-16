FROM nginx:alpine

# Установка дополнительных утилит для разработки
RUN apk add --no-cache \
    bash \
    curl \
    nano \
    tzdata

# Создание пользователя www-data
RUN adduser -u 82 -S -D -G www-data -g www-data -s /bin/bash www-data

# Настройка временной зоны
ENV TZ=Europe/Moscow

# Копирование конфигураций
COPY common.conf /etc/nginx/common.conf
COPY sites-available/ /etc/nginx/sites-available/
COPY nginx.conf /etc/nginx/nginx.conf

# Создание символических ссылок и директорий
RUN mkdir -p /etc/nginx/sites-enabled \
    && mkdir -p /var/log/nginx \
    && mkdir -p /var/www/www-data \
    && ln -sf /etc/nginx/sites-available/*.conf /etc/nginx/sites-enabled/ \
    && chown -R www-data:www-data /var/www/www-data \
    && chmod -R 755 /var/www/www-data

WORKDIR /var/www/www-data

# Проверка контейнера
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
    CMD curl -f http://localhost:9000/ || exit 1

STOPSIGNAL SIGQUIT

CMD ["nginx", "-g", "daemon off;"]

FROM php:8.2-cli

# Define o diretório de trabalho
WORKDIR /app

# Instala extensões necessárias
RUN apt-get update && apt-get install -y \
    unzip \
    git \
    && docker-php-ext-install opcache \
    && rm -rf /var/lib/apt/lists/*

# Instala Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

RUN composer init \
--no-interaction \
--require slim/slim:"^4" \
--require slim/psr7:"^1"
RUN composer update

# Copia os arquivos do projeto
COPY . .

# Instala as dependências do projeto
RUN composer install

# Instala e configura OpenTelemetry Auto-Instrumentation
RUN pecl install opentelemetry

RUN mv "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini"

RUN echo "extension=opentelemetry.so" >> "$PHP_INI_DIR/php.ini"

RUN php -m | grep opentelemetry

RUN composer config allow-plugins.php-http/discovery false
RUN composer require \
  open-telemetry/sdk \
  open-telemetry/opentelemetry-auto-slim \
  open-telemetry/exporter-otlp \
  open-telemetry/opentelemetry-auto-psr18 \
  php-http/guzzle7-adapter

# Expõe a porta do serviço
EXPOSE 8081

# Comando para rodar a aplicação
CMD ["php", "-S", "0.0.0.0:8081"]

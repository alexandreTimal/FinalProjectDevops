# Image WordPress durcie - base mise a jour + hardening de base
FROM wordpress:6.8-apache

# Durcissement PHP et Apache
COPY hardening/php-hardening.ini /usr/local/etc/php/conf.d/php-hardening.ini
COPY hardening/security.conf /etc/apache2/conf-available/zzz-hardening.conf

# Apache : ecoute en 8080 (port non privilegie) pour tourner sans root,
# active les modules headers et la conf de durcissement
RUN sed -i 's/^Listen 80$/Listen 8080/' /etc/apache2/ports.conf \
    && sed -i 's/:80>/:8080>/' /etc/apache2/sites-enabled/000-default.conf \
    && a2enmod headers \
    && a2enconf zzz-hardening

# Reduction de la surface d'attaque : suppression des themes et plugins par defaut
RUN rm -rf /usr/src/wordpress/wp-content/themes/twentytwenty \
           /usr/src/wordpress/wp-content/themes/twentytwentyone \
           /usr/src/wordpress/wp-content/plugins/akismet \
           /usr/src/wordpress/wp-content/plugins/hello.php

# Nettoyage des paquets et caches inutiles
RUN apt-get purge -y --auto-remove 2>/dev/null || true \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Permissions correctes pour permettre l'execution en non-root
RUN chown -R www-data:www-data /var/www/html /usr/src/wordpress

# Edition de fichiers desactivee dans l'admin
ENV WORDPRESS_CONFIG_EXTRA="define('DISALLOW_FILE_EDIT', true);"

EXPOSE 8080

# Execution en utilisateur non privilegie
USER www-data

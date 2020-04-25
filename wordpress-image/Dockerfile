#1. Docker base image
FROM wordpress:php7.4-apache

#2. Install WP-cli and dependencies to run it
RUN apt-get update \
    && apt-get install -y \
      less \
      subversion \
      sudo \
      default-mysql-client-core \
    && curl https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar -o /usr/local/bin/wp \
    && chmod +x /usr/local/bin/wp

#3. Create the files for the testing environment
RUN \
    #3.1 Install phpunit
    curl -L https://phar.phpunit.de/phpunit-7.phar -o /tmp/phpunit \
    && chmod a+x /tmp/phpunit \
    #3.2 Install wordpress
    && cp -r /usr/src/wordpress /tmp/wordpress \
    && curl https://raw.github.com/markoheijnen/wp-mysqli/master/db.php -o /tmp/wordpress/wp-content/db.php \
    #3.3 Install the testing libraries
    && svn co --quiet https://develop.svn.wordpress.org/tags/5.3.2/tests/phpunit/includes/ /tmp/wordpress-tests-lib/includes \
    && svn co --quiet https://develop.svn.wordpress.org/tags/5.3.2/tests/phpunit/data/ /tmp/wordpress-tests-lib/data \
    #3.4 set owner and permissions
    && chown -R www-data:www-data /tmp/wordpress \
    && chown -R www-data:www-data /tmp/wordpress-tests-lib

#4. Copy the script to create the testing environment when the container is started
COPY init-testing-environment.sh /usr/local/bin/

#5. Run the script and send as an argument the command to run the apache service
ENTRYPOINT ["init-testing-environment.sh"]
CMD ["apache2-foreground"]

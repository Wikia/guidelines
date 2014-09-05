# Python Scenario Guide

This document contains suggestions on how to handle various "scenarios" in Python.

## Web Apps / Services

For web frameworks, we've decided to use [Flask] for most situations. Flask will allow you to create
a [WSGI] application. Flask also has a built-in web server. However, it is not suitable for
production setups. In production, we use [uWSGI] as an application server, and put [Nginx] in front
of it for the web server. This is the industry standard setup for high scale Python applications.

To learn more about Flask, check out the [Flask Mega-Tutorial].

Although Flask is great for most situations, when you're creating much more complex applications
that will have many users and groups with different permissions and a more complex object model,
[Django] will probably better suit your needs. It also contains a built-in web server but should
also be setup with uWSGI and Nginx for production.

To learn more about Django, check out the [official Django tutorial].

## Configuration

For configuration, use [wikia.common.configparser]. You can download it from Wikia's PyPI server.
This will make it easy to automatically read config files from common places. For example, if your
application is named "foo", it can automatically read files in the following locations:

* `/etc/foo/foo.conf`
* `/etc/foo/conf.d/*.conf`
* the environment variable `FOO_CONFIG`
* `~/.foo.conf`

You should also read the [configuration section](ProjectOrganization.md#configuration) of the
Project Organization, Packaging, and Deployment document.

## Logging

For centralized logging, use [wikia.common.logger]. It will log to Wikia's standard centralized
logging setup so you can query and graph your logs in Kibana.

## Database / ORM

If you want an [ORM] for a relational database, we've decided to use [SQLAlchemy].

## Command-Line Tools

For building command-lines tools, use [Click], the "Command Line Interface Creation Kit".

## Daemonization

If you're building something that needs to run as a daemon that's not a web app, you can use
[daemonocle] to daemonize your application.


[Flask]: https://github.com/mitsuhiko/flask
[WSGI]: http://en.wikipedia.org/wiki/Web_Server_Gateway_Interface
[uWSGI]: https://uwsgi-docs.readthedocs.org/en/latest/
[Nginx]: http://nginx.org/
[Flask Mega-Tutorial]: http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world
[Django]: https://www.djangoproject.com/
[official Django tutorial]: https://docs.djangoproject.com/en/1.7/intro/tutorial01/
[wikia.common.configparser]: https://github.com/Wikia/python-commons/tree/master/wikia/common/configparser
[wikia.common.logger]: https://github.com/Wikia/python-commons/tree/master/wikia/common/logger
[SQLAlchemy]: http://www.sqlalchemy.org/
[ORM]: http://en.wikipedia.org/wiki/Object-relational_mapping
[Click]: http://click.pocoo.org/
[daemonocle]: https://pypi.python.org/pypi/daemonocle/

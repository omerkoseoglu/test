# Laravel Octane Dockerfile
<a href="/LICENSE"><img alt="License" src="https://img.shields.io/github/license/exaco/laravel-octane-dockerfile"></a>
<a href="https://github.com/exaco/laravel-octane-dockerfile/releases"><img alt="GitHub release (latest by date)" src="https://img.shields.io/github/v/release/exaco/laravel-octane-dockerfile"></a>
<a href="https://github.com/exaco/laravel-octane-dockerfile/pulls"><img alt="GitHub closed pull requests" src="https://img.shields.io/github/issues-pr-closed/exaco/laravel-octane-dockerfile"></a> <a href="https://github.com/exaco/laravel-octane-dockerfile/actions/workflows/tests.yml"><img alt="GitHub Workflow Status" src="https://github.com/exaco/laravel-octane-dockerfile/actions/workflows/roadrunner-test.yml/badge.svg"></a> <a href="https://github.com/exaco/laravel-octane-dockerfile/actions/workflows/tests.yml"><img alt="GitHub Workflow Status" src="https://github.com/exaco/laravel-octane-dockerfile/actions/workflows/swoole-test.yml/badge.svg"></a>

A pretty configurable, production-ready, and multi-stage Dockerfile for [Laravel Octane](https://github.com/laravel/octane)
powered web services and microservices.

The Docker configuration provides the following setup:

- PHP 8.1 and 8.2 official DebianBookworm-based images
- Preconfigured JIT compiler and OPcache

## Container modes

You can build the Docker image in different modes:

| Mode             | `CONTAINER_MODE` ARG | HTTP server | Exposed port |
|------------------|----------------------|------------| ------------|
| Octane (default) | `app`                | Swoole / RoadRunner | 9000 |
| Horizon          | `horizon`            | - | - |
| Scheduler        | `scheduler`          | - | - |

> If you want to run Horizon in the Octane container, then you should set `APP_WITH_HORIZON` build argument `true`.

> If you want to run Scheduler in the Octane container, then you should set `APP_WITH_SCHEDULER` build argument `true`.

## Usage

1. Clone this repository:
```
git clone --depth 1 git@github.com:exaco/laravel-octane-dockerfile.git
```
2. Copy cloned directory content including `deployment` directory, `Dockerfile`, and `.dockerignore` into your Octane powered Laravel project
3. Change the directory to your Laravel project
4. Build your image:

- Container `app` mode:
```
docker build -t <image-name>:<tag> .
```
- Container `horizon` mode:
```
docker build -t <image-name>:<tag> --build-arg CONTAINER_MODE=horizon .
```
- Container `scheduler` mode:
```
docker build -t <image-name>:<tag> --build-arg CONTAINER_MODE=scheduler .
```
5. Up the container:
```
docker run -p <port>:9000 --rm <image-name>:<tag>
```

## Configuration

There is something that you maybe want to configure:

- Swoole HTTP server config in `supervisord.app.conf`
- PHP configurations in `php.ini`
- `ENTRYPOINT` Bash script in `entrypoint.sh`
- Set `PHP_VERSION` using the `--build-arg` option along with the build command
- Set `TZ` (OS timezone) using the `--build-arg` option along with the build command

## Using RoadRunner instead of Swoole

1. Ensure you have roadrunner as a dependency in your `composer.json`
2. Set `OCTANE_SERVER=roadrunner` using the `--build-arg` option along with the build command

> You can configure RoadRunner through `deployment/octane/.rr.prod.yaml`.

## Recommended Swoole options for `octane.php`

```php
// config/octane.php

return [
    'swoole' => [
        'options' => [
            'http_compression' => true,
            'http_compression_level' => 6, // 1 - 9
            'compression_min_length' => 20,
            'package_max_length' => 20 * 1024 * 1024, // 20MB
            'open_http2_protocol' => true,
            'document_root' => public_path(),
            'enable_static_handler' => true,
        ]
    ]
];
```

## Utilities

Also, some useful Bash functions and aliases are added in `utilities.sh` that maybe help.

## Notes

- Laravel Octane logs request information only in the `local` environment.
- Please be aware of `.dockerignore` content

## ToDo
- [x] Add support for Horizon
- [x] Add support for RoadRunner
- [x] Add support for the full-stack apps (Front-end assets)
- [ ] Add support `testing` environment and CI
- [x] Add support for the Laravel scheduler
- [ ] Add support for Laravel Dusk
- [ ] Support more PHP extensions
- [x] Add tests
- [ ] Add Alpine-based images

## Contributing

Thank you for considering contributing! If you find an issue, or have a better way to do something, feel free to open an
issue, or a PR.

## Credits
- [SMorteza Ebadi](https://github.com/smortexa)
- [All contributors](https://github.com/exaco/laravel-octane-dockerfile/graphs/contributors)

## License

This repository is open-sourced software licensed under the [MIT license](https://opensource.org/licenses/MIT).

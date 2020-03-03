# Toolbox Test

This project provides Docker-based test-harnesses for Crossfire and Matchbox integration testing.

## Prerequisites

To use these test-harnesses, you will need to obtain the following from the Reactive Markets support
team:

- latest Reactive FIX specification.
- secure token for the Reactive container registry.

## Container Registry

Access to the Reactive container registry is available to customers and partners of Reactive
Markets. Login to the Reactive container registry using the username and token provided by the
support team:

```bash
$ docker login -u <username> -p <token> registry.gitlab.com
```

Please contact [support@reactivemarkets.com](mailto:support@reactivemarkets.com) if you unable to
access the registry with the token provided.

## Container Commands

Run the Docker compose `up` command to create and start all containers:

```bash
$ docker-compose up
```

This command will start all containers in the foreground. Use the `-d` option to start them in the
background, and the `logs` command to tail the logs:

```bash
$ docker-compose up -d
$ docker-compose logs -f
```

Containers running in the background should be stopped using the `down` command:

```bash
$ docker-compose down
```

The following Docker `prune` command will purge all containers, networks, volumes and images:

```bash
$ docker system prune --all --volumes
```

This can be useful to ensure a pristine system restart.

## Contributing

Please read [CONTRIBUTING.md](CONTRIBUTING.md) for details on our code of conduct, and the process
for submitting pull requests.

## Versioning

We use [SemVer](https://semver.org/) for versioning. For the versions available, see the [tags on
this repository](https://github.com/reactivemarkets/toolbox-java/tags).

## License

This project is licensed under the [Apache 2.0
License](https://www.apache.org/licenses/LICENSE-2.0). A copy of the license is available in the
[LICENSE.md](LICENSE.md) file in the root directory of the source tree.

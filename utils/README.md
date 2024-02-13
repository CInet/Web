# post-receive hooks

There are two types of `post-receive` hooks used. The [CInet/website](/CInet/website)
repository wants its latest `master` branch HEAD checked out in a specific
location for the webserver to serve. This is what `post-receive-master` does.

The data repositories in [cinet.link/data](https://cinet.link/data) are only
checked out when a new tag is pushed and each tag should remain checked out.
The name of the repository is symlinked to the latest tag (according to
`sort -V`). The `post-receive-tags` script takes care of that.

# systemd integration

To start the CInet::Web server automatically when the server boots but also
running it under a non-privileged user can be achieved with `systemd`.
First make the `www` user under which it should run "linger:

``` console
# loginctl enable-linger www
```

This causes a session for `www` to always run, making it possible for user
services to run as soon as the system is up. There is a template
`CInet.service` in this repository which can be put into
`$HOME/.config/systemd` and enabled:

``` console
$ systemctl --user enable CInet
```

This requires a `CInet.config` file for the CInet::Web application and an
environment file `CInet.env` for the service. The environment is a stripped
down and curated version of your current `env`, to allow, for example,
your `perlbrew`ed Perl installation to be found. The following is a sample
`CInet.config` file when the `hypnotoad` process is reverse-proxied by
`nginx`.

```
# Sample CInet.config file
{
    hypnotoad => {
        listen  => ['http://127.0.0.1:8080'],
        proxy => 1,
    },
    basedir => '/home/www/CInet/website',
    images  => '/home/www/CInet/images',
    data    => '/home/www/CInet/data',
}
```

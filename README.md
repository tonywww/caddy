# caddy
Caddy web server with plugins

(version: linux-amd64)

- Caddy 0.11.1 downloaded from official wesbite.

  Download short url: http://git.io/caddy0

- Caddy 1.05  Compiled by myself with Go 1.16.3.


<!-- TABLE OF CONTENTS -->
<details open="open">
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#filemanager-syntax">Filemanager</a>
    </li>
    <li>
      <a href="#cloudflare-syntax">Cloudflare</a>
    </li>
    <li>
      <a href="#forwardproxy-syntax">Forwardproxy</a>
    </li>
    <li>
      <a href="#webdev-syntax">Webdev</a>
    </li>
    <li>
      <a href="#filebrowser-syntax">Filebrowser</a>
    </li>
    <li><a href="#license">License</a></li>
  </ol>
</details>


## Filemanager syntax:

````
filemanager [url] [scope] {
    database            path
    no_auth
    recaptcha_key       key
    recaptcha_secret    secret
    alternative_recaptcha
    locale              [en|jp|...]
    allow_commands      [true|false]
    allow_edit          [true|false]
    allow_new           [true|false]
    allow_publish       [true|false]
    commands            cmd1 cmd2...
    css                 path
}
````
- **url** is the URL path where you will access File Browser. Defaults to `/`.
- **database** is the path for the database where the settings will be stored.
- **no_auth** disables the authentication. This should be enabled if you will use another login method (such as `basicauth`).
- **recaptcha_key** and **recaptcha_secret** are the Site Key and Secret Key used to enable ReCaptcha on login.
- **alternative_recaptcha** replaces https://www.google.com to https://recaptcha.net in ReCaptcha handling and serving, especially useful in China. See https://github.com/filebrowser/filebrowser/issues/366 for details. Defaults to `false`.


The following options are mere defaults: they will only be used as the default options for **new users**. After creating a user, its settings should be changed through the Web UI. Although, when using `no_auth` option, the following will define the user permissions.


- **scope** is the path, relative or absolute, to the directory you want to browse in. Defaults to `./`.
- **locale** is the default language for new users.
  - `de` - German
  - `en` - English
  - `es` - Spanish
  - `fr` - French
  - `it` - Italian
  - `ja` - Japanese
  - `pl` - Polish
  - `pt` - Portuguese
  - `pt-br` - Portuguese (Brasil)
  - `ru` - Romanian
  - `zh-cn` - Chinese (Simplified)
  - `zh-tw` - Chinese (Traditional)
- **allow_commands** is the default value for allow commands option.
- **allow_edit** is the default value for allow edit option.
- **allow_new** is the default value for allow new option.
- **allow_publish** is the default value for allow publish option.
- **commands** are the default available commands.
- **css** is the path for a file with a custom stylesheet.


## Cloudflare syntax:

Documents:

https://github.com/caddyserver/dnsproviders/

https://go-acme.github.io/lego/dns/cloudflare/

DNS providers for Caddy v1. This can be used to help solve the ACME DNS challenge by plugging them into Caddy 0.9-1.x:

Caddy 0.9 and newer supports solving the ACME DNS challenge. This challenge is unique because the server that is requesting a TLS certificate does not need to start a listener and be accessible from external networks. This quality is essential when behind load balancers or in other advanced networking scenarios.

The DNS challenge sets a DNS record and the ACME server verifies its correctness in order to issue the certificate. Caddy can do this for you automatically, but it needs credentials to your DNS provider to do so. 

You can then use this in your Caddyfile with the `tls` directive like so:

```plain
tls {
     dns cloudflare
}
```

Cloudflare API token should be set in environment variables. For example:

````
export CF_DNS_API_TOKEN=xxxxxxxxxxxxxxxxxxxxxxxx
````
or 
````
export CLOUDFLARE_DNS_API_TOKEN=xxxxxxxxxxxxxxxxxxxxxxxx
````


## Forwardproxy syntax

Document: https://github.com/caddyserver/forwardproxy

The simplest way to enable the forward proxy without authentication just include the forwardproxy directive in your Caddyfile. However, this allows anyone to use your server as a proxy, which might not be desirable.

Open a block for more control; here's an example of all properties in use (note that the syntax is subject to change):

````
forwardproxy {
    basicauth user1 0NtCL2JPJBgPPMmlPcJ
    basicauth user2 密码
    ports     80 443
    hide_ip
    hide_via
    probe_resistance secret-link-kWWL9Q.com # alternatively you can use a real domain, such as caddyserver.com
    serve_pac        /secret-proxy.pac
    response_timeout 30
    dial_timeout     30
    upstream         https://user:password@extra-upstream-hop.com
    acl {
      allow     *.caddyserver.com
      deny      192.168.1.1/32 192.168.0.0/16 *.prohibitedsite.com *.localhost
      allow     ::1/128 8.8.8.8 github.com *.github.io
      allowfile /path/to/whitelist.txt
      denyfile  /path/to/blacklist.txt
      allow     all
      deny      all # unreachable rule, remaining requests are matched by `allow all` above
    }
}
````

- **url** is the URL path where you will access File Browser. Defaults to `/`.
- **database** is the path for the database where the settings will be stored.
- **no_auth** disables the authentication. This should be enabled if you will use another login method (such as `basicauth`).
- **recaptcha_key** and **recaptcha_secret** are the Site Key and Secret Key used to enable ReCaptcha on login.
- **alternative_recaptcha** replaces https://www.google.com to https://recaptcha.net in ReCaptcha handling and serving, especially useful in China. See https://github.com/filebrowser/filebrowser/issues/366 for details. Defaults to `false`.


The following options are mere defaults: they will only be used as the default options for **new users**. After creating a user, its settings should be changed through the Web UI. Although, when using `no_auth` option, the following will define the user permissions.


- **scope** is the path, relative or absolute, to the directory you want to browse in. Defaults to `./`.

## Webdev syntax

Document: https://github.com/hacdias/caddy-v1-webdav

```
webdav [url] {
    scope       path
    modify      [true|false]
    allow       path
    allow_r     regex
    block       path
    block_r     regex
}
```

All the options are optional.

+ **url** is the place where you can access the WebDAV interface. Defaults to `/`.
+ **scope** is an absolute or relative (to the current working directory of Caddy) path that indicates the scope of the WebDAV. Defaults to `.`.
+ **modify** indicates if the user has permission to edit/modify the files. Defaults to `true`.
+ **allow** and **block** are used to allow or deny access to specific files or directories using their relative path to the scope. You can use the magic word `dotfiles` to allow or deny the access to every file starting by a dot.
+ **allow_r** and **block_r** and variations of the previous options but you are able to use regular expressions with them.

It is highly recommended to use this directive alongside with [`basicauth`](https://caddyserver.com/docs/basicauth) to protect the WebDAV interface.

```
webdav {
    # You set the global configurations here and
    # all the users will inherit them.
    user1:
    # Here you can set specific settings for the 'user1'.
    # They will override the global ones for this specific user.
}
```

### Examples

WebDAV on `/` for the current working directory:

```
webdav
```

WebDAV on `/admin` for the whole file system:

```
webdav /admin {
    scope /
}
```

WebDAV on `/` for the whole file system, without access to `/etc` and `/dev` directories:

```
webdav {
    scope /
    block /etc
    block /dev
}
```

WebDAV on `/` for the whole file system. The user `sam` can't access `/var/www` but the others can.

```
basicauth / sam pass
webdav {
    scope /
    
    sam:
    block /var/www
}
```


## Filebrowser syntax:

````
filebrowser [url] [scope] {
    database            path
    auth_method         json
    recaptcha_host      https://www.google.com
    recaptcha_key       key
    recaptcha_secret    secret
}
````

- **url** is the URL path where you will access File Browser. Defaults to `/`.
- **scope** is the path, relative or absolute, to the directory you want to browse in. Defaults to `./`.
- **database** is the path for the database where the settings will be stored.
- **recaptcha_host** By default, we use Google's reCAPTCHA service. If you live in China, or want to use other provider, you can change the host to `https://recaptcha.net`.
- **recaptcha_key** and **recaptcha_secret** are the Site Key and Secret Key used to enable ReCaptcha on login.


## License
[Apache-2.0](LICENSE) © tonywww

Caddy® is a registered trademark of Stack Holdings GmbH

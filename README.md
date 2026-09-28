# W.R.A.P. CMS (Deprecated)

![Stable](https://img.shields.io/github/release/magicoli/wrap3-cms?label=stable&color=green&include_prerelease)
![GitHub Tag](https://img.shields.io/github/tag/magicoli/wrap3-cms?label=latest&include_prereleases)
![GitHub commits since latest release](https://img.shields.io/github/commits-since/magicoli/wrap3-cms/latest?label=dev)
![PHP](https://img.shields.io/badge/PHP-8.2+-7884bf)
[![License](https://img.shields.io/badge/license-AGPL--3.0-552b55)](LICENSE)
![GitHub Downloads (all assets, all releases)](https://img.shields.io/github/downloads/magicoli/wrap3-cms/total)
[![Donate](https://img.shields.io/badge/-Donate-yellow)](https://magiiic.org/donate/)

This is the legacy version of the CMS (3.1.1). It will not receive any further updates. All new development will take place in separate projects within the 6.x branch:

- **[magicoli/wrap-app](https://github.com/magicoli/wrap-app)**: a brand-new, modern application that covers not only the features of the legacy CMS but also a wide range of new capabilities.
- **[magicoli/wrap-tools](https://github.com/magicoli/wrap-tools)**: command-line tools only.

## Original Description

Wrap is a basic CMS, aimed to display mostly galleries of images or videos.
The idea is to allow the website maintainer to push media in subfolders.
The structure of the websites and the menus is detected automatically.

It is not intended to be a full-featured CMS. Instead, it allows to
automatically publish videos and pictures playlists.

It is designed for fast, efficient media transmission. Although it is
possible to make a pretty beautiful website with this system (and I did), it's
not the goal.

It is poorly documented, and requires PHP 8.2 or later.

## Installation

From the Magiiic apt repository, with the package matching your web server: `wrap3-cms-apache` (Apache with PHP) or `wrap3-cms-caddy` (Caddy with PHP-FPM). Both install the CMS itself (`wrap3-cms`) and PHP 8.2 or later (Debian 12+, Ubuntu 24.04+):

```bash
curl -fsSL https://apt.magiiic.com/magiiic-packaging.asc | sudo gpg --dearmor -o /usr/share/keyrings/magiiic-packaging.gpg
echo "deb [signed-by=/usr/share/keyrings/magiiic-packaging.gpg] https://apt.magiiic.com stable main" | sudo tee /etc/apt/sources.list.d/magiiic.list
sudo apt update && sudo apt install wrap3-cms-apache    # or wrap3-cms-caddy
```

The CMS is installed in `/usr/share/wrap3-cms`, and serves the folders of a site without an index file.

With Apache (`wrap3-cms-apache`), enable it at `/wrap/`:

```bash
sudo a2enconf wrap3-cms && sudo systemctl reload apache2
```

Then, in the config of each site using it:

```
DirectoryIndex index.html index.php /wrap/wrap.php
```

With Caddy (`wrap3-cms-caddy`), import `/etc/caddy/wrap3-cms.caddyfile` in the site block, with the site root as argument:

```
example.com {
    root * /var/www/example.com
    import /etc/caddy/wrap3-cms.caddyfile /var/www/example.com
    file_server
}
```

- you can place wrap.css and wrap.html in your web root folder to customize layout
- use legacy/themes/bootstrap/page-template.html as base for wrap.html and be sure to
  include all needed shortcodes

### From source

Put the clone outside your web directory: it contains unprotected scripts aimed to alter your disk content. Install the dependencies with `composer install --no-dev --working-dir=engine`, then set up the web server as in `packaging/wrap3-cms.apache.conf` or `packaging/wrap3-cms.caddyfile`, with the path of the clone.

## Known issues

- MP4 metadata features run `/usr/local/bin/AtomicParsley` and `/usr/local/bin/mp4info`, not provided by the package (Debian installs AtomicParsley in `/usr/bin`): they fail where these tools are missing.

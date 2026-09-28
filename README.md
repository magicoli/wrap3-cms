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

Or without the repository, and without automatic updates: download `wrap3-cms` and the web server package from the [latest release](https://github.com/magicoli/wrap3-cms/releases/latest), then install them together, e.g. `sudo apt install ./wrap3-cms_*.deb ./wrap3-cms-caddy_*.deb`.

The CMS is installed in `/usr/share/wrap3-cms`, and serves the folders of a site without an index file. The install also sets up a default site: put the content in `/var/lib/wrap3/www`, and set the real domain in the site config, which answers at `wrap3.localhost` until then (on the server itself only). The CMS keeps its cache next to the site root, in `/var/lib/wrap3/cache`.

With Apache (`wrap3-cms-apache`), the CMS is served at `/wrap/` by the `wrap3-cms` conf, and the default site is `/etc/apache2/sites-available/wrap3-cms.conf`, both enabled on install. Once the domain is set:

```bash
sudo systemctl reload apache2
```

Other sites use the CMS with `DirectoryIndex index.html index.php /wrap/wrap.php`, as the default one.

With Caddy (`wrap3-cms-caddy`), the default site is `/etc/caddy/sites/wrap3-cms.caddyfile`, loaded when the main Caddyfile imports that folder (`import sites/*.caddyfile`). Replace `wrap3.localhost` with the real domain, then reload Caddy (`sudo systemctl reload caddy`). Other sites import the snippet `/etc/caddy/snippets/wrap3-cms.caddyfile`, with the site root as argument:

```
example.com {
    root * /var/www/example.com
    import /etc/caddy/snippets/wrap3-cms.caddyfile /var/www/example.com
    file_server
}
```

- you can place wrap.css and wrap.html in your web root folder to customize layout
- use legacy/themes/bootstrap/page-template.html as base for wrap.html and be sure to
  include all needed shortcodes

### From source

Put the clone outside your web directory: it contains unprotected scripts aimed to alter your disk content. Install the dependencies with `composer install --no-dev --working-dir=engine`, then set up the web server as in `packaging/` (Apache conf and site, Caddy snippet and site), with the path of the clone.

## Known issues

- MP4 metadata features run `/usr/local/bin/AtomicParsley` and `/usr/local/bin/mp4info`, not provided by the package (Debian installs AtomicParsley in `/usr/bin`): they fail where these tools are missing.

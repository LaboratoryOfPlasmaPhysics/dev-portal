# LPP Developer Portal

The landing page at <https://hephaistos.lpp.polytechnique.fr/landing_page/>.

One static page, `site/index.html`. Apache server-side includes (mod_include) pull in
`site/partials/internal-*.html` only for visitors from the Polytechnique and LPP
networks; everyone else never receives them. Without mod_include nothing is included,
so a misconfiguration shows the public page, never the internal one. The network list
is the `<!--#if expr=... -->` line at the top of `index.html`:

| Network | What |
|---|---|
| `129.104.0.0/16` | École polytechnique (whole RIPE block), includes LPP Palaiseau and the campus Wi-Fi |
| `134.157.77.0/24` | LPP Jussieu |

No build step, no JavaScript, no third-party requests (fonts are self-hosted).

## Deploy on hephaistos

```sh
git clone https://github.com/LaboratoryOfPlasmaPhysics/dev-portal.git /opt/dev-portal
a2enmod include
# add apache/dev-portal.conf inside the :443 <VirtualHost> of hephaistos.conf
ln -sfn /opt/dev-portal/site /var/www/html/landing_page
apache2ctl configtest && systemctl reload apache2
```

Update: `git -C /opt/dev-portal pull`.

The `<Directory>` path in `apache/dev-portal.conf` is the symlink path, not `/opt/...`:
Apache matches `<Directory>` against the requested path without resolving symlinks.

## Preview locally

Opening `site/index.html` directly shows the public view (browsers ignore the SSI
comments). The internal additions are in `site/partials/`. To see both views together,
serve it with Apache and mod_include.

## Credits

- Service logos: [Simple Icons](https://simpleicons.org) (CC0); trademarks belong to their owners.
- Fonts: IBM Plex, SIL Open Font License (`site/fonts/OFL.txt`).

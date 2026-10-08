# Releasing

## Version string

One version string, in five places, all equal:

1. `Version:` in the `fynex-for-woocommerce.php` header
2. `FYNEX_WC_VERSION` in the same file
3. `Stable tag:` in `readme.txt`
4. the top entry of `changelog.txt` (`YYYY-MM-DD - version X.Y.Z`)
5. the Git tag `vX.Y.Z` and the GitHub release zip

Bump it last, in the release pull request, so the number is not used up on an
intermediate build.

## Before tagging

- [ ] CI is green: coding standards, PHPUnit on the oldest and newest supported
      PHP/WordPress/WooCommerce, both order storages.
- [ ] `WC tested up to` (plugin header) and `Tested up to` (readme.txt) name the
      current WooCommerce and WordPress releases.
- [ ] `languages/fynex-for-woocommerce.pot` regenerated if strings changed:
      `wp i18n make-pot . languages/fynex-for-woocommerce.pot --slug=fynex-for-woocommerce --domain=fynex-for-woocommerce --exclude=vendor,tests,dist,bin`
- [ ] Plugin Check passes on the zip (`wp plugin check fynex-for-woocommerce`).
- [ ] Manual pass of `docs/TESTING.md` against a Fynex test key.

## QIT (Woo Marketplace)

Woo runs QIT on every Marketplace submission and update; **activation, security and
malware must pass** for a version to deploy. Until the Woo partner account exists
this is a manual step:

```sh
composer global require woocommerce/qit-cli
qit partner:add          # once, with the Woo partner user and application password
./bin/package
qit run:activation fynex-for-woocommerce --zip=dist/fynex-for-woocommerce.zip
qit run:security fynex-for-woocommerce --zip=dist/fynex-for-woocommerce.zip
qit run:malware fynex-for-woocommerce --zip=dist/fynex-for-woocommerce.zip
qit run:phpcompatibility fynex-for-woocommerce --zip=dist/fynex-for-woocommerce.zip
```

Check the exact commands against <https://qit.woo.com/docs/> when the account is
set up, then move this into CI with the partner credentials as repository secrets.

## Build

```sh
./bin/package
```

`dist/fynex-for-woocommerce.zip` contains only runtime files: the main file,
`uninstall.php`, `includes/`, `assets/`, `languages/`, `readme.txt`,
`changelog.txt`, `README.md` and `LICENSE`.

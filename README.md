# Zero_SizeChart

Size guides on product pages. A chart is a table made in the admin and
shown to the products of chosen categories; the product page gets a
"Size guide" button that opens it in a dialog.

## Install

Requires Magento Open Source 2.4.7–2.4.9 and PHP 8.2–8.4 with the GD extension built with WebP (`php -r 'var_dump(function_exists("imagewebp"));'` says `true`; Magento's own PHP requirements cover the rest). Without WebP in GD, Zero skips its WebP image copies and serves the originals.

```bash
composer require zerofront/module-zero-size-chart
bin/magento setup:upgrade
bin/magento setup:di:compile           # production mode
bin/magento setup:static-content:deploy # production mode
bin/magento cache:flush
```

Composer installs `zerofront/module-zero-core` with it.

Part of [`zerofront/zero`](https://zerofront.dev), which installs the Zero theme and every Zero module at once.

## Storefront

Between the product's options and Add to Cart: a "Size guide" button
(text in Stores > Configuration > Zero > Size Charts). It opens a native
`<dialog>` with the chart's heading, the table and the note; no JavaScript
beyond the theme's `data-dialog-open`. Nothing on products without a chart.

Which chart a product gets:

1. Its **Size Chart** attribute, when set: a chart, or "No size guide".
2. Otherwise the chart of the most specific category the product is in: a
   chart on "Men" covers everything under Men; a chart on "Men > Tops" wins
   over it for the tops. Between two charts of one category, the lower sort
   order wins.

Product pages are cached with the chart; saving or deleting a chart cleans
the page and block caches.

## Admin

Catalog > Size Charts (resource `Zero_SizeChart::charts`): the list and a
form: name, heading, the table as text (one row per line, cells separated
by `|`, the first line the headings; 2–12 columns, up to 60 rows; one
outer `|` at the start or end of a line is a border, not a cell, so an
empty corner cell is written `| | S | M |`, and the form writes that
border back for a saved empty first cell), a note under the table,
categories, sort order, active. The form shows the saved table as a
preview.

`Setup\Patch\Data\AddSizeChartAttribute` adds the `size_chart` product
attribute (General / Product Details group of every attribute set).

## Tests

`Test/Unit/Model/TableTest.php` (the text format);
`dev/tests/zero-e2e/tests/size-chart-admin.spec.ts` (a chart on the root
category shows on a product page, opens, and goes with the chart; writes,
so local or ZERO_E2E_PROD_WRITES=1).

## Disabling or removing

Its data lives in `zero_size_chart`. With the module's code still in place,
`bin/magento module:disable Zero_SizeChart` followed by `setup:upgrade`
**drops that table** (Magento's declarative schema); enabling the module
again brings it back empty. To keep the charts, switch the size guide off in
Stores > Configuration instead, or back up first: `bin/magento setup:upgrade
--safe-mode=1` dumps the data before anything is dropped (`--data-restore=1`
puts it back). Deleting the module's code leaves the table in place. The
`size_chart` product attribute stays either way.

What changed in each release: [CHANGELOG.md](CHANGELOG.md).

## License

OSL-3.0 or AFL-3.0, your choice: see `NOTICE.md`.

---

<a href="https://vilmate.com"><picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/zerofrontdev/zero/main/docs/images/vilmate-logo-dark.svg">
  <img src="https://raw.githubusercontent.com/zerofrontdev/zero/main/docs/images/vilmate-logo.svg" alt="Vilmate" height="34" align="right">
</picture></a>

Zero is made by [Fixstacks](https://fixstacks.com) in partnership with [Vilmate](https://vilmate.com), a software development company.
Documentation: https://zerofront.dev/docs. Demo store: https://demo.zerofront.dev.

---
title: SaaS Price Indexing Installation
description: Installing SaaS Price Indexing
seo-title: SaaS Price Indexing installation
seo-description: Installing SaaS Price indexing
exl-id: a607e852-aa04-4be3-9576-a6bf45f8751f
role: Admin, Developer
---
# SaaS Price Indexing Installation

Setting up SaaS price indexing requires installing new modules and running CLI commands. Admins need command-line access to complete this installation.

## Prerequisites

* Adobe Commerce 2.4.4+
* At least one of the following SaaS services installed:

    * [Catalog Service](../catalog-service/overview.md)
    * [Live Search](../live-search/guide-overview.md)
    * [Product Recommendations](../product-recommendations/guide-overview.md)

## Install required modules

Depending on your setup, the installation process might be slightly different.
There are extensions that add the new feeds and supporting code and there is an extension that removes the default prices feed.

1. Add the following modules to your `composer.json` file:

    ```json
    "magento/module-saas-price": "102.2.0",
    "magento/module-saas-scopes": "102.2.0",
    "magento/module-product-override-price-remover": "102.2.0",
    "magento/module-bundle-product-override-data-exporter": "102.2.0",
    ```

1. Run the upgrade command:

    ```bash
    bin/magento setup:upgrade
    ```

After upgrading, three new feeds are available:

* `prices` - responsible for delivering prices data to the service
* `scopesCustomerGroup` - responsible for delivering Customer Groups to the service 
* `scopesWebsite` - responsible for delivering Websites, Store Groups and Store Views to the service


1. Configure the new feeds to be set to "Update on Schedule" mode:

    ```bash
    bin/magento indexer:set-mode schedule catalog_data_exporter_product_prices scopes_customergroup_data_exporter scopes_website_data_exporter
    ```

1. Reindex the new feeds:

    ```bash
    bin/magento saas:resync --feed=scopesCustomerGroup
    bin/magento saas:resync --feed=scopesWebsite
    bin/magento saas:resync --feed=prices
    ```

Run the above indexers manually, as needed. Otherwise, the data gets refreshed in the standard sync process. Read more about the [Catalog Sync](../landing/catalog-sync.md) service.

Luma and Adobe Commerce Core GraphQL users can install the `catalog-adapter` module that provides Luma and Core GraphQl compatibility and disables the PHP core price indexer.
To use the `catalog-adapter` module, [!DNL Live Search] and [!DNL Catalog Service] must first be installed and configured. Follow the [Install [!DNL Live Search]](../live-search/install.md) and [Catalog Service Installation](../catalog-service/installation.md) instructions before continuing.

To configure Live Search and Catalog Adapter, follow [Commerce Services Connector](https://experienceleague.adobe.com/docs/commerce-merchant-services/user-guides/integration-services/saas.html?lang=en) instructions.

```bash
composer require adobe-commerce/catalog-adapter
```

If needed, the PHP core price indexer can be reenabled with the following command:

```bash
bin/magento module:disable Magento_PriceIndexerDisabler
```

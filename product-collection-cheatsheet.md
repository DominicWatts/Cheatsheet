# Product Collection Cheatsheet

## Import statement

```php
use Magento\Catalog\Model\ResourceModel\Product\CollectionFactory as ProductCollectionFactory;
Constructor

/**
 * @var ProductCollectionFactory
 */
private $productCollectionFactory;

/**
 * @param ProductCollectionFactory $productCollectionFactory
 */
public function __construct(
    ProductCollectionFactory $productCollectionFactory
) {
    $this->productCollectionFactory = $productCollectionFactory;
}
```

## Product Types

```php
\Magento\Catalog\Model\Product\Type::TYPE_SIMPLE
\Magento\Catalog\Model\Product\Type::TYPE_BUNDLE
\Magento\Catalog\Model\Product\Type::TYPE_VIRTUAL
\Magento\Downloadable\Model\Product\Type::TYPE_DOWNLOADABLE
\Magento\ConfigurableProduct\Model\Product\Type\Configurable::TYPE_CODE
\Magento\GroupedProduct\Model\Product\Type\Grouped::TYPE_CODE
```

## Visibility filter

```php
/**
 * @var \Magento\Catalog\Model\Product\Visibility
 */
protected $productVisibility;

public function __construct(
    \Magento\Catalog\Model\Product\Visibility $productVisibility,
) {
    $this->productVisibility = $productVisibility;
}

[...]
$collection->setVisibility($this->productVisibility->getVisibleInSiteIds());
[...]
```

## Filters

```php
// Is Equal To
$collection->addAttributeToFilter('status', ['eq' => 1]);

// Is Not Equal To
$collection->addAttributeToFilter('visibility', ['neq' => 1]);

// Greater Than
$collection->addAttributeToFilter('price', ['gt' => 3]);

// Less Than
$collection->addAttributeToFilter('price', ['lt' => 3]);

// Greater Than or Equal To
$collection->addAttributeToFilter('price', ['gteq' => 4]);

// Less Than or Equal To
$collection->addAttributeToFilter('price', ['lteq' => 4]);

// Contains – with % wildcards
$collection->addAttributeToFilter('sku', ['like' => 'DVD%']);

// Does Not Contain – with % wildcards
$collection->addAttributeToFilter('sku', ['nlike' => 'ABC%']);

// In Array
$collection->addAttributeToFilter('entity_id', ['in' => [1,3,12]]);

// Not In Array
$collection->addAttributeToFilter('entity_id', ['nin' => [1,2,12]]);

// Is NULL
$collection->addAttributeToFilter('description', 'null');
$collection->addAttributeToFilter('description', ['null' => true]);

// Is Not NULL
$collection->addAttributeToFilter('description', 'notnull');
$collection->addAttributeToFilter('description', ['notnull' => true]);
```

## Add Price Data

```php
$collection->addPriceData();
```

## Simple filter

```php
$collection->addAttributeToFilter('type_id', $typeFilter);
```

## More complex filter

```php
$collection->addAttributeToFilter(
    'status',
    ['in' => 'processing']
);
```

## Or filter

```php
$collection->addAttributeToFilter([
    [
        'attribute' => 'download_link',
        'null' => true,
    ],
    [
        'attribute' => 'download_link',
        'eq' => '',
    ],
]);
```

```php
$collection->addFieldToFilter([
    ['download_link', 'download_link'],
    [
        ['is' => new \Zend_Db_Expr('null')],
        ['eq' => '']
    ],
]);
```

## In Stock Filter

```php
/**
 * @var \Magento\CatalogInventory\Helper\Stock
 */
protected $stockHelper;

public function __construct(
     \Magento\CatalogInventory\Helper\Stock $stockHelper
) {
     $this->stockHelper = $stockHelper;
}

[...]
$this->stockHelper->addInStockFilterToCollection($collection);
[...]
```

## Get Price Data

```php
$collection->getMaxPrice();
$collection->getMinPrice();
$collection->getPriceStandardDeviation();
$collection->getPricesCount();
```

## Create

```text
$collection = $this->productCollectionFactory->create();
```

## Filter by Product ID

```text
$collection->addIdFilter($productId);
```

## Filter by Store ID

```text
$collection->addStoreFilter($storeId);
```

## Select Attribute

```text
$collection->addAttributeToSelect($attributeCode);
```

## Get first item

```text
return $collection->getFirstItem();
```

## Get raw attribute value

```text
return $collection->getFirstItem()->getData($attributeCode);
```

## Status Filter

```php
/**
 * @var Magento\Catalog\Model\Product\Attribute\Source\Status
 */
protected $productStatus;

public function __construct(
    \Magento\Catalog\Model\Product\Attribute\Source\Status $productStatus
) {
    $this->productStatus = $productStatus;
}

[...]

$collection->addAttributeToFilter(
    'status',
    ['in' => $this->productStatus->getVisibleStatusIds()
]);
```

## Category Filter

```text
$collection->addCategoryFilter($category);
```

## Return only IDs

```text
return $collection->getAllIds();
```

## Debug Query

```php
echo (string) $collection->getSelect();
$collection->printLogQuery(true);
```

## Add Media

```text
$collection->addMediaGalleryData();
```

## Add Tier Price Data

```php
$collection->addTierPriceData();
$collection->addTierPriceDataByGroupId($customerGroupId);
```

## Add Category IDs

```text
$collection->addCategoryIds();
```

## Add Website Names

```text
$collection->addWebsiteNamesToResult();
```

## Join Stock Qty to collection

```php
$collection->joinField(
    'qty',
    'cataloginventory_stock_item',
    'qty',
    'product_id=entity_id',
    '{{table}}.stock_id=1',
    'left'
);
```

## Generic Collection

```php
// Sort Collection
// Order by Attribute Ascending
$collection->setOrder('price', 'ASC');

// Order by Attribute Descending
$collection->setOrder('name', 'DESC');

// Random Order
$collection->setOrder('rand()');

// Limit Collection
$collection->setPageSize(10);
$collection->setCurPage(1);

// Count Results
$collection->count();
```

## Add URL data

```text
$collection->addUrlRewrite($categoryId);
```

## Add Tax Percents

```text
$collection->addTaxPercents();
```

## Add Options

```text
$collection->addOptionsToResult();
```

## Add Sort

```text
$collection->addAttributeToSort('name', 'DESC');
```

## Free Up Memory

```text
$collection->clear();
```


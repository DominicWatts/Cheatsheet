# Product Collection Cheatsheet

## Import statement

```php
use Magento\Catalog\Model\ResourceModel\Product\CollectionFactory as ProductCollectionFactory;

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
## Create

```php
$collection = $this->productCollectionFactory->create();
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

### Even more complex filter

```php
$collection->addPriceData();
[...]
$complexCollection = clone $collection;
$complexCollection->getSelect()
    ->where('price_index.min_price >= 100')
    ->where('price_index.min_price <= 200');
$ids = $complexCollection->getAllIds();
$collection->addAttributeToFilter('entity_id', array('in' => $ids));
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

```php
$collection->addFieldToFilter(
    ['download_link', 'download_link'],
    [
        ['null' => true],
        ['eq' => '']
    ]
);
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

## Filter by Product ID

```php
$collection->addIdFilter($productId);
```

## Filter by Store ID

```php
$collection->addStoreFilter($storeId);
```

## Select Attribute

```php
$collection->addAttributeToSelect($attributeCode);
$collection->addAttributeToSelect('*'); // all
```
Configured

```php
use Magento\Catalog\Model\Config;
public function __construct(
    [...]
    Config $config
    [...]
} {
    $this->config = $config;
}

$attributes = $this->config->getProductAttributes();
$collection->addAttributeToSelect($attributes);

```

## Get first item

```php
return $collection->getFirstItem();
```

## Get raw attribute value

```php
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

```php
$collection->addCategoryFilter($category);
```

## Return only IDs

```php
return $collection->getAllIds();
```

## Debug Query

```php
echo (string) $collection->getSelect();
$collection->printLogQuery(true);
```

## Add Media

```php
$collection->addMediaGalleryData();
```

## Add Tier Price Data

```php
$collection->addTierPriceData();
$collection->addTierPriceDataByGroupId($customerGroupId);
```

## Add Category IDs

```php
$collection->addCategoryIds();
```

## Add Website Names

```php
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
// or 
$collection->getSelect()->order('price ASC');

// Order by Attribute Descending
$collection->setOrder('name', 'DESC');
// or 
$collection->getSelect()->order('name DESC');

// Magento\Catalog\Model\ResourceModel\Product\Collection
$collection->addAttributeToSort('is_saleable', 'ASC');

// Random Order
$collection->setOrder('rand()');

// reset
$collection->getSelect()->reset('order');

// Limit Collection
$collection->setPageSize(10);
$collection->setCurPage(1);

// Count Results
$collection->count();
```

## Add URL data

```php
$collection->addUrlRewrite($categoryId);
```

## Add Tax Percents

```php
$collection->addTaxPercents();
```

## Add Options

```php
$collection->addOptionsToResult();
```

## Add Sort

```php
$collection->addAttributeToSort('name', 'DESC');
```

## Free Up Memory

```php
$collection->clear();
```


# Category Collection Cheatsheet

## Import statement

```php
use Magento\Catalog\Model\ResourceModel\Category\CollectionFactory as CategoryCollectionFactory;

/**
 * @var CategoryCollectionFactory
 */
protected $categoryCollectionFactory;

/**
 * @param CategoryCollectionFactory $productCollectionFactory
 */
public function __construct(
    CategoryCollectionFactory $categoryCollectionFactory
) {
    $this->categoryCollectionFactory = $categoryCollectionFactory;
}
```

## Create Collection

```php
$collection = $this->categoryCollectionFactory
    ->create()
    ->addAttributeToSelect('*');
```

## Filters

```php
// Is Equal To
$collection->addAttributeToFilter('is_active', ['eq' => 1]);

// Is Not Equal To
$collection->addAttributeToFilter('is_active', ['neq' => 1]);

// Greater Than
$collection->addAttributeToFilter('level', ['gt' => 3]);

// Less Than
$collection->addAttributeToFilter('level', ['lt' => 3]);

// Greater Than or Equal To
$collection->addAttributeToFilter('entity_id', ['gteq' => 4]);

// Less Than or Equal To
$collection->addAttributeToFilter('entity_id', ['lteq' => 4]);

// Contains – with % wildcards
$collection->addAttributeToFilter('name', ['like' => 'DVD%']);

// Does Not Contain – with % wildcards
$collection->addAttributeToFilter('name', ['nlike' => 'ABC%']);

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

## Simple filter

```php
$collection->addAttributeToFilter('attribute', $filter);
```

## More complex filter

```php
$collection->addAttributeToFilter(
    'attribute',
    ['in' => 'filter']
);
```

## Or filter

```php
$collection->addAttributeToFilter([
    [
        'attribute' => 'description',
        'null' => true,
    ],
    [
        'attribute' => 'description',
        'eq' => '',
    ],
]);
```

```php
$collection->addFieldToFilter([
    ['description', 'description'],
    [
        ['is' => new \Zend_Db_Expr('null')],
        ['eq' => '']
    ],
]);
```

## Filter by Category ID

```php
$categoryId = 1; // int
$categoryId = "1,2,3"; // comma string
$categoryId = [1, 2, 3]; // array
$collection->addIdFilter($categoryId);
```

## Filter by Store ID

```php
$collection->setStore($storeId);
$collection->setStoreId($storeId); // AbstractCollection
```

## Select Attribute

```php
$collection->addAttributeToSelect($attributeCode);
```

## Get first item

```php
return $collection->getFirstItem();
```

## Get raw attribute value

```php
return $collection->getFirstItem()->getData($attributeCode);
```

## Is Active Filter

```php
$collection->addIsActiveFilter();
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

## Add Paths Filter

```php
$paths = "1/2/3";
$paths = ["1/2/3", "4/5/6"];
$collection->addPathsFilter($paths);
```

## Level filter

```php
$collection->addLevelFilter($level); // lteq $level
$collection->addRootLevelFilter(); // neq 1
```

## Order By

```php
$collection->addOrderField($field);
```

## Add Website Names

```php
$collection->addWebsiteNamesToResult();
```

## Joins url rewrite rules to collection

```php
$collection->joinUrlRewrite();
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

## Free Up Memory

```php
$collection->clear();
```

## Category Helper

### Tree

```php
use Magento\Catalog\Helper\Category as HelperCategory;

public function __construct(
    HelperCategory $helper
) {
    $this->helper = $helper;
}
/**
 * Retrieve current store categories
 * @param bool|string $sorted
 * @param bool $asCollection
 * @param bool $toLoad
 * @return \Magento\Framework\Data\Tree\Node\Collection or
 * \Magento\Catalog\Model\ResourceModel\Category\Collection or array
 */
$categories = $this->helper->getStoreCategories(false, true, true);
```

### Get URL

```php
$url = $this->helper->getCategoryUrl($category);
```

### Can Show

```php
$bool = $this->helper->canShow($category);
```

## Category Usefuls

### Get associated products

```php
// @return \Magento\Framework\Data\Collection\AbstractDb
$category->getProductCollection();
```

### URL

```php
$category->getUrl();
$category->getImageUrl();
```

### Parent

```php
$category->getParentCategory();
$category->getParentId();
$category->getParentIds();
```

### Children

```php
/**
 * Retrieve children ids comma separated
 *
 * @param boolean $recursive
 * @param boolean $isActive
 * @param boolean $sortByPosition
 * @return string
 */
$category->getAllChildren(true, true, false);
$category->hasChildren(); // bool
```

### Product Count

```php
$category->getProductCount();
```



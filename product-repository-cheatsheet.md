# Product Repository Cheatsheet

## Import Statement

```php
use Magento\Catalog\Api\ProductRepositoryInterface;
use Magento\Catalog\Api\Data\ProductInterfaceFactory;
```

## Constructor

```php
/**
 * @var ProductRepositoryInterface
 */
protected $productRepositoryInterface;

/**
 * @var ProductInterfaceFactory
 */
protected $productInterfaceFactory;

/**
 * @param ProductRepositoryInterface $productRepositoryInterface
 */
public function __construct(
    ProductRepositoryInterface $productRepositoryInterface,
    ProductInterfaceFactory $productInterfaceFactory,
) {
    $this->productRepositoryInterface = $productRepositoryInterface;
    $this->productInterfaceFactory = $productInterfaceFactory;
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

## Product Visibility

```php
\Magento\Catalog\Model\Product\Visibility::VISIBILITY_NOT_VISIBLE
\Magento\Catalog\Model\Product\Visibility::VISIBILITY_IN_CATALOG
\Magento\Catalog\Model\Product\Visibility::VISIBILITY_IN_SEARCH
\Magento\Catalog\Model\Product\Visibility::VISIBILITY_BOTH
```

## Product Status

```xml
\Magento\Catalog\Model\Product\Attribute\Source\Status::STATUS_ENABLED
\Magento\Catalog\Model\Product\Attribute\Source\Status::STATUS_DISABLED
```

## Get info about product by product SKU

```text
$product = $this->productRepositoryInterface->get($sku);
```

```php
/**
* @param string $sku
* @param bool $editMode
* @param int|null $storeId
* @param bool $forceReload
* @return \Magento\Catalog\Api\Data\ProductInterface
* @throws \Magento\Framework\Exception\NoSuchEntityException
*/
```

## Get info about product by product id

```text
$product = $this->productRepositoryInterface->getById($productId);
```

```php
/**
* @param int $productId
* @param bool $editMode
* @param int|null $storeId
* @param bool $forceReload
* @return \Magento\Catalog\Api\Data\ProductInterface
* @throws \Magento\Framework\Exception\NoSuchEntityException
*/
```

## Delete by SKU

```text
$this->productRepositoryInterface->deleteById($sku);
```

```php
/**
* @param string $sku
* @return bool Will returned True if deleted
* @throws \Magento\Framework\Exception\NoSuchEntityException
* @throws \Magento\Framework\Exception\StateException
*/
```

## Create New Product

```php
$product = $this->productInterfaceFactory
    ->create()
    ->setSku('SKU');
// other required attributes
$product = $this->productRepositoryInterface->save($product);
```

## Update Existing Product

```php
$product = $this->productRepositoryInterface->get('SKU');
$product->setStatus(\Magento\Catalog\Model\Product\Attribute\Source\Status::STATUS_DISABLED);
$updated = $this->productRepositoryInterface->save($product);
```

## Advanced Search Filter Group

```php
/**
 * @var \Magento\Catalog\Api\ProductRepositoryInterface
 */
private $productRepository;

/**
 * @var \Magento\Framework\Api\SearchCriteriaBuilder
 */
private $searchCriteriaBuilder;

/**
 * @var \Magento\Framework\Api\FilterBuilder
 */
private $filterBuilder;

/**
 * @var \Magento\Framework\Api\Search\FilterGroupBuilder
 */
private $filterGroupBuilder;

/**
 * @var \Magento\Framework\Api\SortOrderFactory
 */
private $sortOrderFactory;

/**
 * @param \Magento\Catalog\Api\ProductRepositoryInterface $productRepository
 * @param \Magento\Framework\Api\SearchCriteriaBuilder $searchCriteriaBuilder
 * @param \Magento\Framework\Api\FilterBuilder $filterBuilder
 * @param \Magento\Framework\Api\Search\FilterGroupBuilder $filterGroupBuilder
 * @param \Magento\Framework\Api\SortOrderFactory $sortOrderFactory
 */
public function __construct(
    \Magento\Catalog\Api\ProductRepositoryInterface $productRepository,
    \Magento\Framework\Api\SearchCriteriaBuilder $searchCriteriaBuilder,
    \Magento\Framework\Api\FilterBuilder $filterBuilder,
    \Magento\Framework\Api\Search\FilterGroupBuilder $filterGroupBuilder,
    \Magento\Framework\Api\SortOrderFactory $sortOrderFactory
) {
    $this->productRepository = $productRepository;
    $this->searchCriteriaBuilder = $searchCriteriaBuilder;
    $this->filterBuilder = $filterBuilder;
    $this->filterGroupBuilder = $filterGroupBuilder;
    $this->sortOrderFactory = $sortOrderFactory;
}

[...]

$filter1 = $this->filterBuilder
    ->setField("url")
    ->setValue("%magento.com")
    ->setConditionType("like")
    ->create();

$filter2 = $this->filterBuilder
    ->setField("store_id")
    ->setValue("1")
    ->setConditionType("eq")
    ->create();

$filterGroup1 = $this->filterGroupBuilder
    ->setFilters([$filter1, $filter2])
    ->create();

$filter3 = $this->filterBuilder
    ->setField("url_type")
    ->setValue(1)
    ->setConditionType("eq")
    ->create();

$filterGroup2 = $this->filterGroupBuilder
    ->setFilters([$filter3])
    ->create();

$searchCriteria = $this->searchCriteriaBuilder->create();
$searchCriteria->setFilterGroups([$filterGroup1, $filterGroup2]);
$this->productRepository->getList($searchCriteria)->getItems();
```

```text
The code above creates a Search Criteria with the Filters put together in the following way: (url like %magen­to.com OR store_id eq 1) AND (url_type eq 1)
```

## Search Criteria

```php
/**
 * @var ProductRepositoryInterface
 */
private $productRepositoryInterface;

/**
 * @var SearchCriteriaBuilder
 */
private $searchCriteriaBuilder;

/**
 * @param ProductRepositoryInterface $productRepositoryInterface
 * @param SearchCriteriaBuilder $searchCriteriaBuilder
 */
public function __construct(
    ProductRepositoryInterface $productRepositoryInterface,
    SearchCriteriaBuilder $searchCriteriaBuilder
) {
    $this->productRepository = $productRepositoryInterface;
    $this->searchCriteriaBuilder = $searchCriteriaBuilder;
}

/**
 * Get products by ids
 * @param array $productIds
 * @return ProductInterface[]
 */
public function getProducts(array $productIds): array
{
    $this->searchCriteriaBuilder->addFilter('entity_id', $productIds, 'in');
    $searchCriteria = $this->searchCriteriaBuilder->create();
    $products = $this->productRepositoryInterface->getList($searchCriteria)->getItems();
    $count = $products->getTotalCount();
    return $products;
}
```

## Useful constants

```php
\Magento\Catalog\Api\Data\ProductInterface::SKU
\Magento\Catalog\Api\Data\ProductInterface::NAME
\Magento\Catalog\Api\Data\ProductInterface::PRICE
\Magento\Catalog\Api\Data\ProductInterface::WEIGHT
\Magento\Catalog\Api\Data\ProductInterface::STATUS
\Magento\Catalog\Api\Data\ProductInterface::VISIBILITY
\Magento\Catalog\Api\Data\ProductInterface::ATTRIBUTE_SET_ID
\Magento\Catalog\Api\Data\ProductInterface::TYPE_ID
\Magento\Catalog\Api\Data\ProductInterface::CREATED_AT
\Magento\Catalog\Api\Data\ProductInterface::UPDATED_AT
\Magento\Catalog\Api\Data\ProductInterface::MEDIA_GALLERY
\Magento\Catalog\Api\Data\ProductInterface::TIER_PRICE

\Magento\CatalogInventory\Api\Data\StockItemInterface::QTY
\Magento\CatalogInventory\Api\Data\StockItemInterface::IS_IN_STOCK
\Magento\CatalogInventory\Api\Data\StockItemInterface::BACKORDERS

\Magento\Eav\Model\Entity::DEFAULT_ENTITY_ID_FIELD
```

## Advanced Search

```php
/**
 * @var \Magento\Catalog\Api\ProductRepositoryInterface
 */
private $productRepository;

/**
 * @var \Magento\Framework\Api\SearchCriteriaBuilder
 */
private $searchCriteriaBuilder;

/**
 * @var \Magento\Framework\Api\FilterBuilder
 */
private $filterBuilder;

/**
 * @var \Magento\Framework\Api\SortOrderFactory
 */
private $sortOrderFactory;

/**
 * @param \Magento\Catalog\Api\ProductRepositoryInterface $productRepository
 * @param \Magento\Framework\Api\SearchCriteriaBuilder $searchCriteriaBuilder
 * @param \Magento\Framework\Api\FilterBuilder $filterBuilder
 * @param \Magento\Framework\Api\SortOrderFactory $sortOrderFactory
 */
public function __construct(
    \Magento\Catalog\Api\ProductRepositoryInterface $productRepository,
    \Magento\Framework\Api\SearchCriteriaBuilder $searchCriteriaBuilder,
    \Magento\Framework\Api\FilterBuilder $filterBuilder,
    \Magento\Framework\Api\SortOrderFactory $sortOrderFactory
) {
    $this->productRepository = $productRepository;
    $this->searchCriteriaBuilder = $searchCriteriaBuilder;
    $this->filterBuilder = $filterBuilder;
    $this->sortOrderFactory = $sortOrderFactory;
}

/**
 * Retrieves products to be sent
 * @param array $excludedIds
 * @param array $filterIds
 * @param int $page
 * @param int $pageSize
 * @return ProductInterface[]
 * @throws \Magento\Framework\Exception\InputException
 */
public function retrieve(
    array $excludedIds,
    array $filterIds,
    int $page = 1,
    int $pageSize = 100
) : array {
    $this->searchCriteriaBuilder->addFilter('type_id', [
        \Magento\Catalog\Model\Product\Type::TYPE_SIMPLE,
        \Magento\Downloadable\Model\Product\Type::TYPE_DOWNLOADABLE
    ], 'in');

    if (count($filterIds)) {
        $this->searchCriteriaBuilder->addFilter('entity_id', $filterIds, 'in');
    }

    if (count($excludedIds)) {
        $this->searchCriteriaBuilder->addFilter('entity_id', $excludedIds, 'nin');
    }

    $sortOrder = $this->sortOrderFactory->create()
        ->setField('updated_at')
        ->setDirection(\Magento\Framework\Api\SortOrder::SORT_DESC);
    $this->searchCriteriaBuilder->setSortOrders([$sortOrder]);

    $this->searchCriteriaBuilder->setCurrentPage($page);
    $this->searchCriteriaBuilder->setPageSize($pageSize);
    $searchCriteria = $this->searchCriteriaBuilder->create();
    return $this->productRepository->getList($searchCriteria)->getItems();
}
```

## More Useful Constants

```php
\Magento\Framework\Api\SortOrder::SORT_ASC
\Magento\Framework\Api\SortOrder::SORT_DESC
```

## Print Query String

```php
$products = $this->productRepositoryInterface->getList($searchCriteria)
echo $products->getSelect()->assemble();
```


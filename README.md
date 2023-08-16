# Display featured product first in woocommerce shop loop
Display all products and always show featured products first.
```php
// Modify product query to display featured products first
// Function to get an array of all product IDs
function get_all_product_ids() {
    $product_ids = get_posts( array(
        'post_type'      => 'product',
        'post_status'    => 'publish',
        'fields'         => 'ids',
        'posts_per_page' => -1,
    ) );

    return $product_ids;
}

// Function to sort product IDs with featured products first
function sort_products_by_featured( $product_ids ) {
    $featured_product_ids = wc_get_featured_product_ids();
    $non_featured_product_ids = array_diff( $product_ids, $featured_product_ids );

    return array_merge( $featured_product_ids, $non_featured_product_ids );
}

// Modify product query to display products sorted by featured
function custom_sort_products_by_featured( $q ) {
    if ( ! is_admin() && $q->is_main_query() && is_shop() || is_product_category() ) {
        $product_ids = get_all_product_ids();
        $sorted_product_ids = sort_products_by_featured( $product_ids );

        $q->set( 'post__in', $sorted_product_ids );
	$q->set( 'orderby', 'post__in' );
    }
}
add_action( 'pre_get_posts', 'custom_sort_products_by_featured' );
```

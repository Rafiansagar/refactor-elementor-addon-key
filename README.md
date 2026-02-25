# Refactor/Migrate elementor addon key

Previews key => Present key.  
Reload once in the dashboard.
```php
function migrate_elementor_widgets_single_site() {

    if ( is_multisite() ) {
        return;
    }
	
    $map = [
        'ts-blog-post-grid' => 'test-blog-post-grid',
        'ts-business-hours' => 'test-business-hours',
        'ts-button' => 'test-button',
        'ts-counter' => 'test-counter',
        'ts-heading' => 'test-heading',
        'ts-icon-box' => 'test-icon-box',
        'ts-icon-list' => 'test-icon-list',
        'ts-logo-showcase' => 'test-logo-showcase',
    ];

    $query = new WP_Query( [
        'post_type'      => 'any',
        'post_status'    => 'any',
        'posts_per_page' => -1,
        'meta_key'       => '_elementor_data',
        'fields'         => 'ids',
        'no_found_rows'  => true,
    ] );

    if ( empty( $query->posts ) ) {
        return;
    }

    foreach ( $query->posts as $post_id ) {

        $data = get_post_meta( $post_id, '_elementor_data', true );

        if ( empty( $data ) ) {
            continue;
        }

        if ( ! is_string( $data ) ) {
            $data = wp_json_encode( $data );
        }

        $original = $data;

        $data = str_replace(
            array_keys( $map ),
            array_values( $map ),
            $data
        );

        if ( $data !== $original ) {
            update_post_meta( $post_id, '_elementor_data', $data );
        }
    }
}

add_action( 'admin_init', 'migrate_elementor_widgets_single_site' );
```
After complete regenerate elementor css.

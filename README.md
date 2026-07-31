# eqezao
SEO Content wordpress
www.eqezao.com
https://eqezao.com
https://www.eqezao.com
https://eqezao.com/product
https://www



/**
 * =====================================================
 * EQEZAO SEO
 * PART 1
 * Register Meta Box
 * =====================================================
 */

add_action('add_meta_boxes', 'eqezao_register_seo_box');

function eqezao_register_seo_box() {

    $screens = array(
        'post',
        'page'
    );

    foreach ($screens as $screen) {

        add_meta_box(
            'eqezao_seo_box',
            'EQEZAO SEO',
            'eqezao_seo_box_callback',
            $screen,
            'normal',
            'high'
        );

    }

}

/**
 * =====================================================
 * Meta Box Layout
 * =====================================================
 */

function eqezao_seo_box_callback($post) {

    wp_nonce_field(
        'eqezao_save_seo',
        'eqezao_seo_nonce'
    );

    $focus_keyword = get_post_meta(
        $post->ID,
        '_custom_focus_keyword',
        true
    );

    $description = get_post_meta(
        $post->ID,
        '_custom_meta_description',
        true
    );

    $keywords = get_post_meta(
        $post->ID,
        '_custom_meta_keywords',
        true
    );

    $canonical = get_post_meta(
        $post->ID,
        '_custom_canonical_url',
        true
    );

?>

<table class="form-table">

<tr>

<th style="width:180px;">
Focus Keyword
</th>

<td>

<input
type="text"
name="eqezao_focus_keyword"
class="widefat"
value="<?php echo esc_attr($focus_keyword); ?>"
placeholder="example keyword">

</td>

</tr>

<tr>

<th>
Meta Description
</th>

<td>

<textarea
name="eqezao_meta_description"
rows="4"
maxlength="160"
class="widefat"><?php
echo esc_textarea($description);
?></textarea>

</td>

</tr>

<tr>

<th>
Meta Keywords
</th>

<td>

<input
type="text"
name="eqezao_meta_keywords"
class="widefat"
value="<?php echo esc_attr($keywords); ?>">

</td>

</tr>

<tr>

<th>
Canonical URL
</th>

<td>

<input
type="url"
name="eqezao_canonical_url"
class="widefat"
value="<?php echo esc_attr($canonical); ?>">

</td>

</tr>

</table>

<?php

}


/**
 * =====================================================
 * EQEZAO SEO
 * PART 2
 * Save SEO Fields
 * =====================================================
 */

add_action('save_post', 'eqezao_save_seo_fields');

function eqezao_save_seo_fields($post_id) {

    /*
    ---------------------------------------
    Security Check
    ---------------------------------------
    */

    if (!isset($_POST['eqezao_seo_nonce'])) {
        return;
    }

    if (!wp_verify_nonce($_POST['eqezao_seo_nonce'], 'eqezao_save_seo')) {
        return;
    }

    /*
    ---------------------------------------
    Autosave
    ---------------------------------------
    */

    if (defined('DOING_AUTOSAVE') && DOING_AUTOSAVE) {
        return;
    }

    /*
    ---------------------------------------
    Revision
    ---------------------------------------
    */

    if (wp_is_post_revision($post_id)) {
        return;
    }

    /*
    ---------------------------------------
    Permission
    ---------------------------------------
    */

    if (!current_user_can('edit_post', $post_id)) {
        return;
    }

    /*
    ---------------------------------------
    Focus Keyword
    ---------------------------------------
    */

    if (isset($_POST['eqezao_focus_keyword'])) {

        update_post_meta(
            $post_id,
            '_custom_focus_keyword',
            sanitize_text_field($_POST['eqezao_focus_keyword'])
        );

    }

    /*
    ---------------------------------------
    Meta Description
    ---------------------------------------
    */

    if (isset($_POST['eqezao_meta_description'])) {

        update_post_meta(
            $post_id,
            '_custom_meta_description',
            sanitize_textarea_field($_POST['eqezao_meta_description'])
        );

    }

    /*
    ---------------------------------------
    Meta Keywords
    ---------------------------------------
    */

    if (isset($_POST['eqezao_meta_keywords'])) {

        update_post_meta(
            $post_id,
            '_custom_meta_keywords',
            sanitize_text_field($_POST['eqezao_meta_keywords'])
        );

    }

    /*
    ---------------------------------------
    Canonical URL
    ---------------------------------------
    */

    if (isset($_POST['eqezao_canonical_url'])) {

        update_post_meta(
            $post_id,
            '_custom_canonical_url',
            esc_url_raw($_POST['eqezao_canonical_url'])
        );

    }

}



/**
 * =====================================================
 * EQEZAO SEO
 * PART 3
 * Output SEO Tags
 * =====================================================
 */

add_action('wp_head', 'eqezao_output_seo_meta', 1);

function eqezao_output_seo_meta() {

    if (!is_singular(array('post', 'page'))) {
        return;
    }

    $post_id = get_queried_object_id();

    $title = get_the_title($post_id);

    $description = get_post_meta(
        $post_id,
        '_custom_meta_description',
        true
    );

    if (empty($description)) {
        $description = wp_strip_all_tags(get_the_excerpt($post_id));
    }

    $keywords = get_post_meta(
        $post_id,
        '_custom_meta_keywords',
        true
    );

    $canonical = get_post_meta(
        $post_id,
        '_custom_canonical_url',
        true
    );

    if (empty($canonical)) {
        $canonical = get_permalink($post_id);
    }

    $image = '';

    if (has_post_thumbnail($post_id)) {

        $image = wp_get_attachment_image_url(
            get_post_thumbnail_id($post_id),
            'full'
        );

    }

    ?>

<!-- EQEZAO SEO -->

<meta name="description" content="<?php echo esc_attr($description); ?>">

<?php if (!empty($keywords)) : ?>

<meta name="keywords" content="<?php echo esc_attr($keywords); ?>">

<?php endif; ?>

<link rel="canonical" href="<?php echo esc_url($canonical); ?>">

<!-- Open Graph -->

<meta property="og:type" content="<?php echo is_page() ? 'website' : 'article'; ?>">

<meta property="og:title" content="<?php echo esc_attr($title); ?>">

<meta property="og:description" content="<?php echo esc_attr($description); ?>">

<meta property="og:url" content="<?php echo esc_url(get_permalink($post_id)); ?>">

<meta property="og:site_name" content="<?php echo esc_attr(get_bloginfo('name')); ?>">

<?php if (!empty($image)) : ?>

<meta property="og:image:secure_url" content="<?php echo esc_url($image); ?>">

<meta property="og:image:alt" content="<?php echo esc_attr($title); ?>">

<?php endif; ?>

<meta property="og:locale" content="en_US">

<?php

    /*
    -------------------------------------------------
    JSON-LD Schema
    -------------------------------------------------
    */

    $schema = array(

        "@context" => "https://schema.org",

        "@type" => is_page() ? "WebPage" : "Article",

        "headline" => $title,

        "description" => $description,

        "url" => get_permalink($post_id),

        "datePublished" => get_the_date('c', $post_id),

        "dateModified" => get_the_modified_date('c', $post_id),

        "mainEntityOfPage" => array(
            "@type" => "WebPage",
            "@id" => get_permalink($post_id)
        ),

        "author" => array(
            "@type" => "Person",
            "name" => get_the_author_meta(
                'display_name',
                get_post_field('post_author', $post_id)
            )
        ),

        "publisher" => array(
            "@type" => "Organization",
            "name" => get_bloginfo('name'),
            "url" => home_url()
        )

    );

    if (!empty($image)) {

        $schema['image'] = array(
            "@type" => "ImageObject",
            "url" => $image
        );

    }

    ?>

<script type="application/ld+json">
<?php
echo wp_json_encode(
    $schema,
    JSON_UNESCAPED_SLASHES | JSON_UNESCAPED_UNICODE
);
?>
</script>

<!-- /EQEZAO SEO -->

<?php

}

# tokendrop-skill: finggu-wordpress
# author: sudarshanpjadhav (Finggu) — finggu.com
# version: 1.0
# tokens: ~210
# compatible: Cursor, Windsurf, Copilot, Continue, Claude, ChatGPT

## Stack
WordPress 6.x · PHP 8.x · Custom Plugin · WP REST API · WP Cron · Custom DB tables with `finggu_` prefix

---

## Plugin File Structure

```
finggu-[plugin-name]/
├── finggu-[plugin-name].php     ← main plugin file
├── uninstall.php
├── includes/
│   ├── class-finggu-[name].php
│   ├── finggu-[name]-functions.php
│   └── finggu-[name]-db.php
├── admin/
│   ├── class-finggu-[name]-admin.php
│   └── views/finggu-[name]-admin-page.php
├── assets/
│   ├── css/finggu-[plugin-name]-admin.css
│   └── js/finggu-[plugin-name]-admin.js
└── languages/
    └── finggu-[plugin-name].pot
```

---

## Main Plugin File Header

```php
<?php
/**
 * Plugin Name:       Finggu [Plugin Name]
 * Plugin URI:        https://finggu.com
 * Description:       [Description]
 * Version:           1.0.0
 * Author:            sudarshanpjadhav
 * Author URI:        https://finggu.com
 * License:           GPL-2.0+
 * Text Domain:       finggu-[plugin-name]
 *
 * @package  Finggu
 * @author   sudarshanpjadhav
 */
defined('ABSPATH') || exit;

define('FINGGU_PLUGIN_VERSION', '1.0.0');
define('FINGGU_PLUGIN_FILE', __FILE__);
define('FINGGU_PLUGIN_DIR', plugin_dir_path(__FILE__));
define('FINGGU_PLUGIN_URL', plugin_dir_url(__FILE__));
```

---

## Main Class Pattern

```php
class Finggu_Plugin_Name {

    private static ?Finggu_Plugin_Name $fingguVar_instance = null;

    public static function fingguFn_getInstance(): self {
        if (null === self::$fingguVar_instance) {
            self::$fingguVar_instance = new self();
        }
        return self::$fingguVar_instance;
    }

    private function __construct() {
        add_action('init', [$this, 'fingguFn_init']);
        add_action('admin_menu', [$this, 'fingguFn_addMenuPages']);
        add_action('wp_enqueue_scripts', [$this, 'fingguFn_enqueueAssets']);
    }

    public function fingguFn_init(): void { /* boot logic */ }

    public function fingguFn_addMenuPages(): void {
        add_menu_page(
            __('Finggu Plugin', 'finggu-plugin-name'),
            'Finggu Plugin',
            'manage_options',
            'finggu-plugin-name',
            [$this, 'fingguFn_renderAdminPage'],
            'dashicons-admin-generic'
        );
    }

    public function fingguFn_enqueueAssets(): void {
        wp_enqueue_style('finggu-plugin-style', FINGGU_PLUGIN_URL . 'assets/css/finggu-plugin.css', [], FINGGU_PLUGIN_VERSION);
        wp_enqueue_script('finggu-plugin-script', FINGGU_PLUGIN_URL . 'assets/js/finggu-plugin.js', ['jquery'], FINGGU_PLUGIN_VERSION, true);
        wp_localize_script('finggu-plugin-script', 'fingguPluginVars', [
            'ajaxUrl' => admin_url('admin-ajax.php'),
            'nonce'   => wp_create_nonce('finggu_nonce'),
        ]);
    }
}

add_action('plugins_loaded', ['Finggu_Plugin_Name', 'fingguFn_getInstance']);
```

---

## Custom DB Table (Install Hook)

```php
function fingguFn_createPluginTables(): void {
    global $wpdb;
    $fingguVar_charset = $wpdb->get_charset_collate();
    $fingguVar_sql = "CREATE TABLE IF NOT EXISTS {$wpdb->prefix}finggu_plugin_data (
        id BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
        user_id BIGINT UNSIGNED NOT NULL,
        data_key VARCHAR(191) NOT NULL,
        data_value LONGTEXT,
        created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
        INDEX idx_finggu_plugin_user (user_id),
        INDEX idx_finggu_plugin_key (data_key)
    ) $fingguVar_charset;";
    require_once ABSPATH . 'wp-admin/includes/upgrade.php';
    dbDelta($fingguVar_sql);
}
register_activation_hook(FINGGU_PLUGIN_FILE, 'fingguFn_createPluginTables');
```

---

## AJAX Handler Pattern

```php
// Public AJAX
add_action('wp_ajax_finggu_action_name', 'fingguFn_handleAjaxRequest');
add_action('wp_ajax_nopriv_finggu_action_name', 'fingguFn_handleAjaxRequest');

function fingguFn_handleAjaxRequest(): void {
    check_ajax_referer('finggu_nonce', 'nonce');
    $fingguVar_input = sanitize_text_field($_POST['input'] ?? '');
    wp_send_json_success(['result' => $fingguVar_input]);
}
```

---

## WP Options Keys
```
finggu_[plugin]_settings
finggu_[plugin]_version
finggu_[plugin]_api_key
```

---

## DO NOT
- Use `$wpdb->query()` for SELECT — use `$wpdb->get_results()` or `$wpdb->get_row()`
- Echo raw user input without `esc_html()` / `esc_attr()`
- Skip nonce verification on AJAX handlers
- Register hooks outside of class constructor or main init function
- Use WordPress table prefix alone without `finggu_` suffix

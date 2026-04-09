# BeeClear Smart Link Manager

**Version:** 2.0  
**Author:** BeeClear  
**License:** GPLv2 or later  
**Requires WordPress:** 5.8+  
**Requires PHP:** 7.4+  
**Tested up to:** WordPress 6.9

## Description

BeeClear Smart Link Manager automatically injects internal links into your content at render time based on keyword rules you define. Links are never saved into post content — they are applied dynamically through the `the_content` filter, so the database stays clean and all changes are instantly reversible.

## Features

- Rule-based internal linking with phrase/token syntax and case-sensitivity control
- Full attribute control: `rel`, `title`, `aria-label`, `class`
- Per-page link limits
- One-click manual index rebuild with live summary
- Automatic index rebuild on post save
- Import / export of rule sets
- Per-post metabox for individual overrides
- Gutenberg and Classic Editor compatible
- Zero HTTP requests during scanning — uses WP_Query and WordPress options internally

## Installation

1. Upload the plugin ZIP via **Plugins → Add New → Upload Plugin**, or unpack and copy the `beeclear-smart-link-manager` folder to `/wp-content/plugins/`.
2. Activate **BeeClear Smart Link Manager** in the Plugins screen.
3. Open **BeeClear Smart Link Manager** in the admin menu, add your rules, and click **Rebuild Index**.

## License

This plugin is licensed under the **GNU General Public License v2.0 or later**.  
You may use, modify, and distribute it under the same terms.

## Changelog

### 2.0
- Refactored index build to use WP_Query internally (no loopback HTTP requests)
- Background scanning via WP-Cron with batch processing
- Lightweight AJAX status polling replaces high-frequency request model

### 1.8
- Fixed invalid HTML in admin column output
- Removed duplicate `admin_enqueue_scripts` hook registration
- Improved promo notice wording
- Removed error suppression from `preg_match` call

### 1.7.5
- First public release

## Support

For support or more information, visit [beeclear.pl](https://beeclear.pl) or contact **info@beeclear.pl**.

© BeeClear

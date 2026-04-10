=== BeeClear Smart Link Manager ===
Contributors: beeclear
Donate link: https://beeclear.pl/en/
Tags: internal linking, external links, autolink, seo, anchor text
Requires at least: 5.8
Tested up to: 6.9.4
Requires PHP: 7.4
Stable tag: 2.0
License: GPLv2 or later
License URI: https://www.gnu.org/licenses/gpl-2.0.html

Automatic internal linking for SEO: define keyword rules, set attributes and limits, rebuild the index in one click — no content changes.

== Description ==

**BeeClear Smart Link Manager** automatically injects internal links into your content at render time, based on keyword rules you define. Links are never saved into post content — they are applied dynamically through the `the_content` filter, so your database stays clean and all changes are instantly reversible.

= How it works =

1. Define a linking rule: choose a phrase, target URL, and optional attributes (rel, title, aria-label, class).
2. Build the index — either manually with one click or automatically on each post save.
3. The plugin injects matching links on the fly whenever a post is rendered.

No content is permanently modified. Deactivating the plugin removes all links instantly.

= Free version features =

* **Rule-based internal linking** — phrase and token syntax with optional case-sensitivity matching.
* **Full attribute control** — set `rel`, `title`, `aria-label`, and `class` on each link for SEO and accessibility.
* **Per-page link limits** — cap how many times a phrase can be linked on a single page.
* **One-click index rebuild** — manual rebuild with a live summary, or automatic rebuild triggered by post updates.
* **Import / export** — move rule sets between sites or environments in seconds.
* **Clean admin UI** — sortable rule tables, inline editing, and a metabox for per-post overrides.
* **Gutenberg and Classic Editor compatible** — works with any content editor.
* **Zero HTTP requests** — scanning and index building use internal WordPress APIs (WP_Query + options), not loopback HTTP calls.

= Premium version features =

* Semantic keyword enhancement
* Custom external linking rules
* Extended rule logic and priorities
* Page-level priority settings
* Activity log with operation history

[Learn more about BeeClear Smart Link Manager →](https://beeclear.pl/en/internal-external-link-manager/)

== Installation ==

= Installation from within WordPress =

1. Go to **Plugins > Add New**.
2. Search for **BeeClear Smart Link Manager**.
3. Click **Install Now**, then **Activate**.

= Manual installation =

1. Download the plugin ZIP file.
2. Go to **Plugins > Add New > Upload Plugin** and upload the ZIP, or unpack it and upload the `beeclear-smart-link-manager` folder to `/wp-content/plugins/`.
3. Go to **Plugins** and activate **BeeClear Smart Link Manager**.

= After activation =

1. Open **BeeClear Smart Link Manager** in the WordPress admin menu.
2. Add your first internal linking rule in the **Rules** tab.
3. Click **Rebuild Index** to apply rules to existing content.

== Frequently Asked Questions ==

= Does this plugin modify post content in the database? =
No. Links are injected dynamically at render time via the `the_content` filter. Your database is never altered — deactivating the plugin removes all links immediately.

= Does it work with the latest WordPress? =
Yes. The plugin is tested up to WordPress 6.9.

= Can I limit the number of links per page? =
Yes. Each rule supports a per-page link cap so the same phrase is not linked more than a set number of times on one page.

= How do I apply rule changes to existing posts? =
Go to the plugin settings and click **Rebuild Index**. You can also enable automatic rebuilding, which triggers on every post save.

= Why are some phrases not being linked? =
The most common causes: the phrase does not match due to case-sensitivity settings, the per-page link limit has been reached, or the post type is excluded in the plugin settings.

= Can I exclude individual posts or post types from auto-linking? =
Yes. Use the plugin settings to exclude specific post types globally, or use the per-post metabox to disable linking for individual posts.

= How do I migrate rules to another site? =
Use the **Export** button to download your rules as a file, then use **Import** on the target site. No manual re-entry needed.

= Is it compatible with page builders (Elementor, Divi, Beaver Builder)? =
The plugin hooks into `the_content` filter, which most page builders respect. Compatibility may vary for builders that bypass standard WordPress rendering.

== Screenshots ==

1. Global settings panel — central configuration screen with default link attributes (rel, title, aria-label), CSS class, link template, cross-inline formatting tags and placement & targeting options. The right sidebar provides quick actions for rebuilding the index, a Last scan summary and maintenance tools.
2. Per-post metabox in the editor — define phrases and rules directly on a single post, with case-sensitive toggles, search scope settings (HTML elements allowed to link in the target) and per-target linking rules, including the option to disable auto-linking for outgoing links.
3. Linking overview — targets & sources report listing every target with its number of defined phrases, number of inbound links, the defined phrases themselves and quick access to the list of source posts.
4. Target details popup — a quick view opened from the overview showing all phrases linking to a specific target together with the source posts where those links are injected.

== Changelog ==

= 2.0 =
* Refactored index build process — uses WP_Query internally instead of loopback HTTP requests, eliminating WAF/firewall conflicts.
* Background scanning via WP-Cron with batch processing for large sites.
* Lightweight AJAX status polling (one read-only request every ~3 seconds) replaces the previous high-frequency request model.
* Improved admin interface and sortable rule tables.

= 1.8 =
* Fixed invalid HTML in admin column output.
* Removed duplicate `admin_enqueue_scripts` hook registration.
* Improved promo notice: button label updated to avoid misleading wording.
* Removed aggressive reset of per-user promo dismissal on plugin activation.
* Removed error suppression operator from `preg_match` call.

= 1.7.5 =
* First public release.

== Upgrade Notice ==

= 2.0 =
The index rebuild mechanism has been fully rewritten to use internal WordPress APIs. No loopback HTTP requests are made during scanning — this resolves conflicts reported on sites with strict WAF rules. After upgrading, click **Rebuild Index** once to regenerate the index with the new engine.

# wpforo
Wordpress wpforo functionality extensions

Currently includes:
WPForo yoast SEO sitemap integration. implements Yoast SEO interface for sitemap provider. <b>Does:</b> The code creates an entry in the Yoast sitemap index (default: sitemap_index.xml) with the url postfix "topic-sitemap.xml" and also the sitemap entries themselves. In use in glj.io (<a href="https://www.glj.io/sitemap_index.xml">here</a>), production. file version: 0.01. <b>Doesn't:</b>
counts images in topics, has documentation beyond the yoast documentation and also, it doesn't go around - all via the yoast interface

How to include:
add the following code in your plugin or functions.php - this adds our custom WPForo sitemap provider. Remember to have put the right path to the wpforo_smp.php file
function add_external_sitemaps_providers($providers) {
	
	require_once('wpforo_smp.php'); // change path if needed
	$providers[] = new WPSEO_WPForo_Sitemap_Provider();
	
	return $providers;
}
add_filter('wpseo_sitemaps_providers', 'add_external_sitemaps_providers', 10, 1);

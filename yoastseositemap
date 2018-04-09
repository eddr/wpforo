<?php
/**
 * WPSEO WPFםרם plugin file.
 *
 * @package WPSEO\XML_Sitemaps
 */

/**
 * Sitemap provider for WPForo.
 */
class WPSEO_WPForo_Sitemap_Provider implements WPSEO_Sitemap_Provider {


	
	/** @var WPSEO_Sitemap_Image_Parser $image_parser Holds image parser instance. */
	protected static $image_parser;

	public function get_wpf_last_item_date($offset, $field = "topics") {
		
		$offset_html = " OFFSET $offset";
		global $wpdb;
		
		$topics_sql = "SELECT `modified` as last_date FROM `wp_wpforo_$field` ORDER BY `modified` DESC LIMIT 1 $offset_html;";
		
		$res = $wpdb->get_results($topics_sql, ARRAY_A);
		
		return $res[0]['last_date'];
	}
	public function get_posts_number() { return WPSEO_WPForo_Sitemap_Provider::get_wpforo_stat("posts"); }
	public function get_topics_number() { return WPSEO_WPForo_Sitemap_Provider::get_wpforo_stat("topics"); }
	public function get_wpforo_stat($field = "posts") {
		
		global $wpdb;
		
		$table_prefix = $wpdb->prefix;
		$sql = "SELECT sum($field) as sum FROM `{$table_prefix}wpforo_forums`;";
		
		$res = $wpdb->get_results($sql, ARRAY_A);
		return intval($res[0]["sum"]);
	}
	
	/**
	 * Check if provider supports given item type.
	 *
	 * @param string $type Type string to check for.
	 *
	 * @return boolean
	 */
	public function handles_type( $type ) {
		//var_dump('handles_type');
		$acceptable_types = ["topic"]; // should also include the replies?
		
		return in_array($type, $acceptable_types);
	}


	/**
	 * @param int $max_entries Entries per sitemap.
	 *
	 * @return array
	 */
	
	public function get_index_links( $max_entries ) {
		
		$index = [];
		$topics_index_links = WPSEO_WPForo_Sitemap_Provider::get_index_links_type($max_entries, "topics", ["sitemap_type_name" => "topic"]);
		
		$index = array_merge($index, $topics_index_links);
		
		return $index;
	}
	public function get_index_links_type( $max_entries, $field = "topics", $args = [] ) {
		
		global $wpdb;
		
		$sitemap_type_name = $field;
		extract($args);
		
		$index = [];
		
		$items_number = WPSEO_WPForo_Sitemap_Provider::get_wpforo_stat($field);
		
		$pages_num = (int) ceil( $items_number / $max_entries );
		
		for ( $page = 0; $page < $pages_num; $page++ ) {
			
			$offset = $page * $max_entries;
			$last_date = WPSEO_WPForo_Sitemap_Provider::get_wpf_last_item_date($offset, $field);
			$page_html = $pages_num > 1 ? $page + 1 : ""; 
			$index[] = [
				'loc'     => WPSEO_Sitemaps_Router::get_base_url( "$sitemap_type_name-sitemap{$page_html}.xml" ),
				'lastmod' => $last_date,
			];
		}
		
		return $index;
	}
	
	
	public function get_topics_page($offset = 0, $max_entries = 1) {
		
		global $wpdb;
		
		
		$table_prefix = $wpdb->prefix;
		
		$limit_html = " LIMIT $max_entries";
		$offset_html = $offset === 0 ? "" : " OFFSET $offset";
		
		$topics_sql = "SELECT topicid, modified FROM `wp_wpforo_topics` ORDER BY `{$table_prefix}wpforo_topics`.`modified` DESC$limit_html$offset_html;";
		
		$res = $wpdb->get_results($topics_sql, ARRAY_A);
		
		return $res;		
	}
	

	/**
	 * Get set of sitemap link data.
	 *
	 * @param string $type         Sitemap type.
	 * @param int    $max_entries  Entries per sitemap.
	 * @param int    $current_page Current page of the sitemap.
	 *
	 * @return array
	 */
	public function get_sitemap_links( $type, $max_entries, $current_page ) {
		
		$links    = [];
		
		$offset = ($current_page - 1) * $max_entries;		
		$topics = WPSEO_WPForo_Sitemap_Provider::get_topics_page($offset, $max_entries);	

		foreach ( $topics as $topic ) {

			$item_data = [];

			$item_data['loc'] = wpforo_topic($topic["topicid"], 'url');

			$item_data['mod']    = $topic["modified"]; //$wpdb->get_var( $wpdb->prepare( $sql, $term->taxonomy, $term->term_id ) );
			$item_data['images'] = 0;

	
			/** This filter is documented at inc/sitemaps/class-post-type-sitemap-provider.php */
			$item_data = apply_filters( 'wpseo_sitemap_entry', $item_data, $type, $topic );

			if ( ! empty( $item_data ) ) {
				$links[] = $item_data;
			}
		}

		return $links;
	}
	
	/**
	 * Get the Image Parser
	 *
	 * @return WPSEO_Sitemap_Image_Parser
	 */
	protected function get_image_parser() {
		/*
		if ( ! isset( self::$image_parser ) ) {
			self::$image_parser = new WPSEO_Sitemap_Image_Parser();
		}

		return self::$image_parser;
		*/
		return null;
	}
}

# wholesaleprice
woo-conditional-product-fees-for-checkout
//wp-content/plugins/woo-conditional-product-fees-for-checkout/woocommerce/checkout/review-order.php

	<?php
/**
 * Review order table
 *
 * This template can be overridden by copying it to yourtheme/woocommerce/checkout/review-order.php.
 *
 * HOWEVER, on occasion WooCommerce will need to update template files and you
 * (the theme developer) will need to copy the new files to your theme to
 * maintain compatibility. We try to do this as little as possible, but it does
 * happen. When this occurs the version of the template file will be bumped and
 * the readme will list any important changes.
 *
 * @see         https://docs.woocommerce.com/document/template-structure/
 * @package     WooCommerce/Templates
 * @version     3.3.0
 */
if ( ! defined( 'ABSPATH' ) ) {
	exit;
}
?>

<?php 

  $user = wp_get_current_user();
  $roles = ( array ) $user->roles;
  $userrole = $roles[0];
?>

<table class="shop_table woocommerce-checkout-review-order-table">
	<thead>
	<tr>
		<th class="product-name"><?php esc_html_e( 'Product', 'woocommerce-conditional-product-fees-for-checkout' ); ?></th>
		<th class="product-total"><?php esc_html_e( 'Total', 'woocommerce-conditional-product-fees-for-checkout' ); ?></th>
	</tr>
	</thead>
	<tbody>
	<?php
	do_action( 'woocommerce_review_order_before_cart_contents' );
	foreach ( WC()->cart->get_cart() as $cart_item_key => $cart_item ) {
		$_product = apply_filters( 'woocommerce_cart_item_product', $cart_item['data'], $cart_item, $cart_item_key );
		if ( $_product && $_product->exists() && $cart_item['quantity'] > 0 && apply_filters( 'woocommerce_checkout_cart_item_visible', true, $cart_item, $cart_item_key ) ) {
			?>
			<tr class="<?php echo esc_attr( apply_filters( 'woocommerce_cart_item_class', 'cart_item', $cart_item, $cart_item_key ) ); ?>">
				<td class="product-name">
					<?php echo wp_kses_post( apply_filters( 'woocommerce_cart_item_name', $_product->get_name(), $cart_item, $cart_item_key ) ) . ' '; ?>
					<?php echo wp_kses_post( apply_filters( 'woocommerce_checkout_cart_item_quantity', ' <strong class="product-quantity">' . sprintf( '× %s', $cart_item['quantity'] ) . '</strong>', $cart_item, $cart_item_key ) ); 
					
					?>
					<?php echo wp_kses_post( wc_get_formatted_cart_item_data( $cart_item ) ); ?>
				</td>
				<td class="product-total">
					<?php 
			
					if($userrole == "wholesale_customer")	
					{
						$productvalue = $_product->get_data();

						$product_id = $productvalue['id'];
						$pricearray = get_post_meta( $product_id, 'wholesale_customer_wholesale_price' );
						$quantity = apply_filters( 'woocommerce_checkout_cart_item_quantity', $cart_item['quantity'], $cart_item, $cart_item_key ); 
						$price = $pricearray[0] * $quantity;

						echo "£".$price;
					}else if($userrole == "eu_wholesale_customer"){
						
						$productvalue = $_product->get_data();

						$product_id = $productvalue['id'];
						$pricearray = get_post_meta( $product_id, 'wholesale_customer_wholesale_price' );
						$quantity = apply_filters( 'woocommerce_checkout_cart_item_quantity', $cart_item['quantity'], $cart_item, $cart_item_key ); 
						$price = $pricearray[0] * $quantity;

						echo "£".$price;
						
					}else if($userrole == "11_discount"){
						
						$productvalue = $_product->get_data();

						$product_id = $productvalue['id'];
						$pricearray = get_post_meta( $product_id, 'wholesale_customer_wholesale_price' );
						$quantity = apply_filters( 'woocommerce_checkout_cart_item_quantity', $cart_item['quantity'], $cart_item, $cart_item_key ); 
						$price = $pricearray[0] * $quantity;

						echo "£".$price;
						
					}else if($userrole == "ad_wholesale_customer"){
						
						$productvalue = $_product->get_data();

						$product_id = $productvalue['id'];
						$pricearray = get_post_meta( $product_id, 'wholesale_customer_wholesale_price' );
						$quantity = apply_filters( 'woocommerce_checkout_cart_item_quantity', $cart_item['quantity'], $cart_item, $cart_item_key ); 
						$price = $pricearray[0] * $quantity;

						echo "£".$price;
						
					}else {
						
						echo wp_kses_post( apply_filters( 'woocommerce_cart_item_subtotal', WC()->cart->get_product_subtotal( $_product, $cart_item['quantity'] ), $cart_item, $cart_item_key ) );
						
					}			 
		 
					?>
					<?php //echo wp_kses_post( apply_filters( 'woocommerce_cart_item_subtotal', WC()->cart->get_product_subtotal( $_product, $cart_item['quantity'] ), $cart_item, $cart_item_key ) ); ?>
				</td>
			</tr>
			<?php
		}
	}
	do_action( 'woocommerce_review_order_after_cart_contents' );
	?>
	</tbody>
	<tfoot>

	<tr class="cart-subtotal">
		<th><?php esc_html_e( 'Subtotal', 'woocommerce-conditional-product-fees-for-checkout' ); ?></th>
		<td><?php wc_cart_totals_subtotal_html(); ?></td>
	</tr>
	
	<?php foreach ( WC()->cart->get_coupons() as $code => $coupon ) : ?>
		<tr class="cart-discount coupon-<?php echo esc_attr( sanitize_title( $code ) ); ?>">
			<th><?php wc_cart_totals_coupon_label( $coupon ); ?></th>
			<td><?php wc_cart_totals_coupon_html( $coupon ); ?></td>
		</tr>
	<?php endforeach; ?>
	
	<?php if ( WC()->cart->needs_shipping() && WC()->cart->show_shipping() ) : ?>
		
		<?php do_action( 'woocommerce_review_order_before_shipping' ); ?>
		
		<?php wc_cart_totals_shipping_html(); ?>
		
		<?php do_action( 'woocommerce_review_order_after_shipping' ); ?>
	
	<?php endif; ?>
	
	<?php
	$wcpfc_obj = new Woocommerce_Conditional_Product_Fees_For_Checkout_Pro_Public( '', '' );
	$fees      = $wcpfc_obj->wcpfc_pro_get_applied_fees();
	foreach ( $fees as $fee ) : ?>
		<tr class="fee">
			<th><?php echo esc_html( $fee->name ); ?></th>
			<td>
				<?php
				$final_fees_label = get_post_meta( $fee->id, 'fee_settings_tooltip_desc', true );
				if ( ! empty( $final_fees_label ) ) {
					echo '<div class="extra-flate-tool-tip"><a data-tooltip="' . esc_attr( wp_strip_all_tags( $final_fees_label ) ) . '"><i class="fa fa-question-circle fa-lg"></i></a></div>';
				}
				?>
				<?php wc_cart_totals_fee_html( $fee ); ?></td>
		</tr>
	<?php endforeach; ?>
	
	<?php if ( wc_tax_enabled() && ! WC()->cart->display_prices_including_tax() ) : ?>
		<?php if ( 'itemized' === get_option( 'woocommerce_tax_total_display' ) ) : ?>
			<?php foreach ( WC()->cart->get_tax_totals() as $code => $get_tax ) : ?>
				<tr class="tax-rate tax-rate-<?php echo esc_attr( sanitize_title( $code ) ); ?>">
					<th><?php echo esc_html( $get_tax->label ); ?></th>
					<td><?php echo wp_kses_post( $get_tax->formatted_amount ); ?></td>
				</tr>
			<?php endforeach; ?>
		<?php else : ?>
			<tr class="tax-total">
				<th><?php echo esc_html( WC()->countries->tax_or_vat() ); ?></th>
				<td><?php wc_cart_totals_taxes_total_html(); ?></td>
			</tr>
		<?php endif; ?>
	<?php endif; ?>
	
	<?php do_action( 'woocommerce_review_order_before_order_total' ); ?>

	<tr class="order-total">
		<th><?php esc_html_e( 'Total', 'woocommerce-conditional-product-fees-for-checkout' ); ?></th>
		<td><?php wc_cart_totals_order_total_html(); ?></td>
	</tr>
	
	<?php do_action( 'woocommerce_review_order_after_order_total' ); ?>

	</tfoot>
</table>

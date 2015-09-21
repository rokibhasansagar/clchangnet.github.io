---
layout: post
title: SYNC WooCOMMERCE cart with Wordpress Theme
---

Synchronize Woocommerce cart in EAT theme from Yootheme. EAT theme has a headerbar on the upper right corner which shows the number of items and the total amount. When clicked, it triggers a javascript popup modal dialog showing the cart contents. Unfortunately, when you installed the theme, it does not comes with the code to integrate with Woocommerce, instead it is just a static mockup html code.

We can make it to work with Woocommerce by adding few lines of code. Install a Wordpress plugin called exec-php to enable php code to work in widgets. Then we can start modifying the code the themes headerbar widget.

Example site: [T Shirt Design][1]

[1]: http://utwear.com/

### To show items and amount

```php
<?php global $woocommerce; ?>
  <a class="uk-link-muted" href="#cart-modal" data-uk-modal><i class="uk-icon-shopping-cart"></i>&nbsp;<?php echo sprintf(_n('%d item', '%d items', $woocommerce->cart->cart_contents_count, 'woothemes'), $woocommerce->cart->cart_contents_count);?> - <?php echo $woocommerce->cart->get_cart_total(); ?></a>
```


### To add sync data with the data-modal popup

```php
        <tbody>
          <?php
          foreach ( WC()->cart->get_cart() as $cart_item_key => $cart_item ) {
            $_product     = apply_filters( 'woocommerce_cart_item_product', $cart_item['data'], $cart_item, $cart_item_key );
            $product_id   = apply_filters( 'woocommerce_cart_item_product_id', $cart_item['product_id'], $cart_item, $cart_item_key );

            if ( $_product && $_product->exists() && $cart_item['quantity'] > 0 && apply_filters( 'woocommerce_cart_item_visible', true, $cart_item, $cart_item_key ) ) {
              ?>
              <tr class="<?php echo esc_attr( apply_filters( 'woocommerce_cart_item_class', 'cart_item', $cart_item, $cart_item_key ) ); ?>">


                <td class="product-name">
                  <?php
                  if ( ! $_product->is_visible() ) {
                    echo apply_filters( 'woocommerce_cart_item_name', $_product->get_title(), $cart_item, $cart_item_key ) . '&nbsp;';
                  } else {
                    echo apply_filters( 'woocommerce_cart_item_name', sprintf( '<a href="%s">%s </a>', get_permalink( $cart_item['product_id'] ), $_product->get_title() ), $cart_item, $cart_item_key );
                  }

                  echo WC()->cart->get_item_data( $cart_item );

                  if ( $_product->backorders_require_notification() && $_product->is_on_backorder( $cart_item['quantity'] ) ) {
                    echo '<p class="backorder_notification">' . esc_html__( 'Available on backorder', 'woocommerce' ) . '</p>';
                  }
                  ?>
                </td>
                <td class="product-price">
                  <?php
                  echo apply_filters( 'woocommerce_cart_item_price', WC()->cart->get_product_price( $_product ), $cart_item, $cart_item_key );
                  ?>
                </td>
                <td class="product-quantity">
                  <?php
                  echo $cart_item['quantity']
                  ?>
                </td>
                <td class="product-subtotal">
                  <?php
                  echo apply_filters( 'woocommerce_cart_item_subtotal', WC()->cart->get_product_subtotal( $_product, $cart_item['quantity'] ), $cart_item, $cart_item_key );
                  ?>
                </td>
              </tr>
              <?php
            }
          }
          ?>
        </tbody>
```

### To show cart total and show checkout button if cart not empty

```php
      <div class="uk-text-large uk-text-bold uk-margin-top"><?php echo WC()->cart->get_cart_total(); ?></div>
      <?php
      if ( sizeof( $woocommerce->cart->cart_contents) > 0 ) :
        echo '<a class="uk-button uk-button-primary uk-margin-top" href="' . esc_url( WC()->cart->get_checkout_url() ) . '" class="checkout-button button alt wc-forward">' . __( 'Proceed to Checkout', 'woocommerce' ) . '</a>'; 
      endif;
      ?>  
```


### Complete Code

```php
<div class="uk-text-right">
  <?php global $woocommerce; ?>
  <a class="uk-link-muted" href="#cart-modal" data-uk-modal><i class="uk-icon-shopping-cart"></i>&nbsp;<?php echo sprintf(_n('%d item', '%d items', $woocommerce->cart->cart_contents_count, 'woothemes'), $woocommerce->cart->cart_contents_count);?> - <?php echo $woocommerce->cart->get_cart_total(); ?></a>
</div>
<div id="cart-modal" class="uk-modal">
  <div class="uk-modal-dialog">
    <a class="uk-modal-close uk-close"></a>
    <h3 class="uk-text-center">Your Shopping Cart</h3> 
    <div class="uk-text-center">
      <table class="uk-table uk-table-condensed uk-table-hover">
        <thead>
          <tr>
            <th><?php _e( 'Product', 'woocommerce' ); ?></th>
            <th><?php _e( 'Price', 'woocommerce' ); ?></th>
            <th><?php _e( 'Quantity', 'woocommerce' ); ?></th>
            <th><?php _e( 'Total', 'woocommerce' ); ?></th>
          </tr>
        </thead>
        <tbody>
          <?php
          foreach ( WC()->cart->get_cart() as $cart_item_key => $cart_item ) {
            $_product     = apply_filters( 'woocommerce_cart_item_product', $cart_item['data'], $cart_item, $cart_item_key );
            $product_id   = apply_filters( 'woocommerce_cart_item_product_id', $cart_item['product_id'], $cart_item, $cart_item_key );

            if ( $_product && $_product->exists() && $cart_item['quantity'] > 0 && apply_filters( 'woocommerce_cart_item_visible', true, $cart_item, $cart_item_key ) ) {
              ?>
              <tr class="<?php echo esc_attr( apply_filters( 'woocommerce_cart_item_class', 'cart_item', $cart_item, $cart_item_key ) ); ?>">


                <td class="product-name">
                  <?php
                  if ( ! $_product->is_visible() ) {
                    echo apply_filters( 'woocommerce_cart_item_name', $_product->get_title(), $cart_item, $cart_item_key ) . '&nbsp;';
                  } else {
                    echo apply_filters( 'woocommerce_cart_item_name', sprintf( '<a href="%s">%s </a>', get_permalink( $cart_item['product_id'] ), $_product->get_title() ), $cart_item, $cart_item_key );
                  }

                  echo WC()->cart->get_item_data( $cart_item );

                  if ( $_product->backorders_require_notification() && $_product->is_on_backorder( $cart_item['quantity'] ) ) {
                    echo '<p class="backorder_notification">' . esc_html__( 'Available on backorder', 'woocommerce' ) . '</p>';
                  }
                  ?>
                </td>
                <td class="product-price">
                  <?php
                  echo apply_filters( 'woocommerce_cart_item_price', WC()->cart->get_product_price( $_product ), $cart_item, $cart_item_key );
                  ?>
                </td>
                <td class="product-quantity">
                  <?php
                  echo $cart_item['quantity']
                  ?>
                </td>
                <td class="product-subtotal">
                  <?php
                  echo apply_filters( 'woocommerce_cart_item_subtotal', WC()->cart->get_product_subtotal( $_product, $cart_item['quantity'] ), $cart_item, $cart_item_key );
                  ?>
                </td>
              </tr>
              <?php
            }
          }
          ?>
        </tbody>

      </table>
      <div class="uk-text-large uk-text-bold uk-margin-top"><?php echo WC()->cart->get_cart_total(); ?></div>
      <?php
      if ( sizeof( $woocommerce->cart->cart_contents) > 0 ) :
        echo '<a class="uk-button uk-button-primary uk-margin-top" href="' . esc_url( WC()->cart->get_checkout_url() ) . '" class="checkout-button button alt wc-forward">' . __( 'Proceed to Checkout', 'woocommerce' ) . '</a>'; 
      endif;
      ?>  
    </div>
  </div>
</div>
```
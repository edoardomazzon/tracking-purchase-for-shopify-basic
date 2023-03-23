# Simple tracking for Shopify Basic
This guide is for the Shopify basic plan.

For tracking purchases on Shopify, we will not use the usual GTM script because unfortunately everything happens on separate files from those of the theme.

However, remember that GTM must be installed in any case in the global theme file by going to `Sales Channels->Online Store->Themes->...->Edit Code->Layout->theme.liquid` and inserting the two classics script inside the `<head>` tag and right after the `<body>` tag.

Once GTM is installed in the theme, to enter the purchase tracking we will have to go to `Settings->Checkout->Order Status Page->Additional Scripts`.
Here we will insert, based on where we want to send the data, one of the following scripts:


### Google Analytics
```
<!-- Global site tag (gtag.js) - Google Analytics 4 -->
<script async src="https://www.googletagmanager.com/gtag/js?id=G-XXXXXXXXXX"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'G-XXXXXXXXXX');
  {% if first_time_accessed %}
    gtag('event', 'purchase', {
        "transaction_id": "{{order.order_number}}",
        "value": {{total_price | times: 0.01}},
        "currency": "{{ order.currency }}",
        "tax": {{tax_price | times: 0.01}},
        "shipping": {{shipping_price | times: 0.01}},
        "items": [
          {% for line_item in line_items %} {
            "id": "{{line_item.product_id}}",
            "name": "{{line_item.title}}",
            "quantity": {{line_item.quantity}},
            "price": {{line_item.line_price | times: 0.01}}
          },
          {% endfor %}
        ]
    });
  {% endif %}
</script>
```

### Google Ads
```
<!-- Google ADS -->
<script>
  {% if first_time_accessed %}
    gtag('event', 'conversion', {
        'send_to': 'AW-00000000000/xxxxxxxxxx',
        'transaction_id': "{{order.order_number}}"
    });
  {% endif %}
</script>
```

### Facebook Pixel
```
<!-- FB Pixel -->
<script>
  {% if first_time_accessed %}
    !function(f,b,e,v,n,t,s){if(f.fbq)return;n=f.fbq=function(){n.callMethod?
    n.callMethod.apply(n,arguments):n.queue.push(arguments)};if(!f._fbq)f._fbq=n;
    n.push=n;n.loaded=!0;n.version='2.0';n.queue=[];t=b.createElement(e);t.async=!0;
    t.src=v;s=b.getElementsByTagName(e)[0];s.parentNode.insertBefore(t,s)}(window,
    document,'script','https://connect.facebook.net/en_US/fbevents.js');
    fbq('init', '000000000000000');
    fbq('track', 'Purchase', { 
      'currency': "{{ order.currency }}",
      'order_id': "{{order.order_number}}",
      'value': {{total_price | times: 0.01}},
      'contents': [ {% for line_item in line_items %}{
          "id": "{{line_item.product_id}}",
          "name": "{{line_item.title}}",
          "quantity": {{line_item.quantity}},
          "price": {{line_item.line_price | times: 0.01}}
        },{% endfor %}
      ],
      'content_type': 'product_group'
    });
  {% endif %}
</script>
```


In all three cases, the service variables will obviously have to be changed, i.e. in order `G-XXXXXXXXXX`, `AW-00000000000/xxxxxxxxxx`, `000000000000000`.

---
title: Checkout
---

{% capture version_note %}
{{ site.version_note_part1 }} 1.4 {{ site.version_note_part2 }}
{% endcapture %}

{% include docs/feature_version.html content=version_note %}

## Overview

The checkout display and structure for TUA differs from core commerce as product offering prices include one-time charges, recurring charges, and usage charges. 

## Checkout Components

- `TmaReviewSubmitComponent` extends `ReviewSubmitComponent` to display Pay on Checkout and other prices of the Product Offering in the Review Order. Information displayed is the same as for `TmaCartItemComponent`.
- `TmaCheckoutOrderSummaryComponent` extends `CheckoutOrderSummaryComponent` to display Subtotal, Delivery cost, and Pay on Checkout Total price in the Review Order. Information displayed is the same as for `TmaOrderSummaryComponent`, and the only difference is that the Delivery Cost is always displayed.

## Further Reading

For more information, see [Pricing at Cart and Order Level](https://help.sap.com/viewer/c762d9007c5c4f38bafbe4788446983e/2007/en-US/91a9faae27bb4a7f8baa46a57078cd61.html) in the TUA Help portal.
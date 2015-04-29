# ApplePay and Adyen

## Integration on the iOS

There are two possibilities on setting up ApplePay on Client iOS device.

1. Use this library `AdyenPay`
2. Use the Apple `PassKit` directly

### Using ApplePay via `PassKit`

First, you need to create the `PKPaymentRequest`:

``` obj-c
PKPaymentRequest *request = [PKPaymentRequest new];
request.merchantIdentifier = self.merchantId;
request.supportedNetworks = @[PKPaymentNetworkMasterCard, PKPaymentNetworkVisa, PKPaymentNetworkAmex];
request.merchantCapabilities = PKMerchantCapability3DS; // Adyen supports only 3DS types
request.countryCode = @"GB";    // ISO 3166 country code
request.currencyCode = @"EUR";  // Currency code

NSDecimalNumber *totalAmount = [NSDecimalNumber decimalNumberWithString:amount];
PKPaymentSummaryItem *totalItem = [PKPaymentSummaryItem summaryItemWithLabel:reference amount:totalAmount];
request.paymentSummaryItems = @[totalItem];
```

Then, you will need to present it using `PKPaymentAuthorizationViewController`:

``` obj-c
PKPaymentAuthorizationViewController *vc = [[PKPaymentAuthorizationViewController alloc] initWithPaymentRequest:request];
vc.delegate = self;
[viewController presentViewController:vc animated:YES completion:nil];
```

*NOTE:* if you are requiring the shipping address, 
the `ABRecordRef` that will be provided in delegate method 

`paymentAuthorizationViewController:didSelectShippingAddress:completion:` 

will contain only Country, City and Postal code. 
The street field will not be provided due to privacy issues. 
So do your checks on possible delivery with city and postal code.
The full address will be provided only when the user Authorizes the payment.

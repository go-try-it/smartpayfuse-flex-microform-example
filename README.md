## GO-TRY-IT.COM - Smartpay Fuse Flex Microform Example
The contents of this repo have been created by the team here at go-try-it.com as part of our ongoing exploration of different payment gateway capabilities. We are building our own knowledge at this point but will soon start to create articles and reviews across a wide range of payment service solutions.

**Disclaimer**: The contents of this repo are provided 'as is' with no express or implied warranties, including the implied warranties of merchantability and fitness for a particular purpose are disclaimed. In no event shall the authors be liable for any direct, indirect, incidental, special, exemplary, or consequential damages (including, but not limited to procurement of substitute goods or services; loss of use, data, or profits; or business interruption) sustained by you or a third party, however caused and on any theory of liability, whether in contract, strict liability, or tort arising in any way out of the use of this sample collection, even if advised of the possibility of such damage.

## About Flex Microforms
Flex Microform is the [Smartpay Fuse hosted field solution](https://developer.smartpayfuse.barclaycard/barclays/quick-start-guides/hosted-fields.html). It allows you to temporarily tokenise card data, generating a *Transient Token* that can be used in Smartpay Fuse REST API payment processing calls.

This repo contains a simple HTML page that demonstrates how a *Transient Token* is generated from a simple form with the help of a Javascript library from the Smartpay Fuse platform provider. 

> IMPORTANT: a note on PCI: Handling plain cardholder data in your systems will bring you in to scope of significant PCI overheads (SAQ D requirement) so it is important to handle customer card details in a PCI safe way. The JWT *Transient Token* output of a Flex Microform integration can be safely passed through your back-office systems as it doesn't itself contain cardholder data, but merely represents it.  

The following steps are required to generate a *Transient Token* and perform a transaction with the tokenized payment card:

1. **A server-side request**: requires a call from your back-end to Smartpay Fuse to generate a signed JWT, called the “capture context”. See the example capture context request in the go-try-it.com [Smartpay Fuse Postman Collection](https://documenter.getpostman.com/view/6354187/2s93RTRsZC#33ea5078-4bb3-43c8-b318-6ea0b2558116). Once generated, the capture context can be passed to your front end application and used to prime a Smartpay Fuse supplied client side JavaScript library.  
   
2. **Client-side implementation (*HTML example in this repo*)**: The capture context generated in step 1 contains a URL reference to a client side JavaScript library. Decode the JWT returned from step 1 (e.g. for test purposes use [jwt.io](https://jwt.io/)) and note the path identified under `ctx.data.clientLibrary`. Your implementation will need to do this programmatically when receiving the capture context generated in step 1 and reference the `clientLibrary` as a `<SCRIPT>` in your HTML. This library is initialised with the “capture context” generated by your server-side request (step 1) and renders iFrames within the HTML form placeholder elements to capture sensitive cardholder data. On-page submission the JavaScript library will use the capture context and field contents to generate a PCI safe *Transient Token* that represents the cardholder details. 
   
3. **Use the Transient Token**: pass the *Transient Token* representing the cardholder details to your service and make API calls to progress a transaction using the token. Use the *Transient Token* to make standard REST API calls to Smartpay Fuse 

## Using the simpleflex.html example
This is a simple one-page example showing how to use the Capture Context that you generated from your back-end (step 1), see the [go-try-it.com Smartpay Fuse Postman Collection example](https://documenter.getpostman.com/view/6354187/2s93RTRsZC#33ea5078-4bb3-43c8-b318-6ea0b2558116). 

Steps to use this sample:

1. Clone this repo 
2. Set-up your webserver to serve `simpleflex.html`; either on your own cloud based hosting, or via a local webserver on `localhost`.
3. Use the go-try-it.com [Create (v2) capture context] (https://documenter.getpostman.com/view/6354187/2s93RTRsZC#33ea5078-4bb3-43c8-b318-6ea0b2558116) request to create a Flex Microform Capture Context JWT
   1. Note 1: be sure to update `targetOrigins` in your version of `simpleflex.html` to match the full path URL where you host the page
   2. Note 2: the URL scheme can *only* be `http` for `localhost`, if hosting `simpleflex.html` somewhere other than `localhost` then you *must* use `https`
4. Clone this repo
5. Take Capture Context JTW returned from [Create (v2) capture context] (https://documenter.getpostman.com/view/6354187/2s93RTRsZC#33ea5078-4bb3-43c8-b318-6ea0b2558116) and update the initialisation value of `captureContext` in `simpleflex.html` with the Capture Context JWT 
   
```
var captureContext = 'YOUR CAPTURE CONTEXT JWT GOES HERE';
```

6. Deploy `simpleflex.html` to your hosting location
7. Load the page in a browser

On clicking `submit`, all being well, the page will use the `<SCRIIPT>` referenced JavaScript library to tokenize the payment card details and generate a *Transient Token*. You can then use the Transient Token in any REST REQUEST.

> NOTE: a Transient Token is short lived and is only valid for 15 minutes. 

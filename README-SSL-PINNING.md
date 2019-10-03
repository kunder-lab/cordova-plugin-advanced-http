# SSL Pinning

## Implementation

1. Add SSL Certificates (*.cer) to www/certificates folder
2. Use "[setSSLCertMode](https://github.com/kunder-lab/cordova-plugin-advanced-http#setsslcertmode)" to enable SSL Pinning (mode "pinned")
3. Use "[sendRequest](https://github.com/kunder-lab/cordova-plugin-advanced-http#sendrequest)" to make a request to server and wait for callback
4. If error callback was trigger, check if the response.error equals -2, because that would mean that the network is not secure

## sendRequest Callback Error Examples

### Android

| Error Code | Description | Exception |
| --- | --- | --- |
| 1 | The request timed out | SocketTimeoutException |
| 0 | The host could not be resolved | UnknownHostException |
| -1 | Could not decode response data due to invalid or unknown charset encoding | CharacterCodingException |
| -1 | Could not decode response data due to malformed data | MalformedInputException |
| -1 | There was an error generating the response | JSONException |
| -1 | There was an error with the request: + ex.getMessage() | HttpRequestException (Generic) |
| **-3** | **SSL handshake failed** | **SSLHandshakeException** |
| **-2** | **Bad SSL key** | **SSLKeyException** |
| **-2** | **Peer's identity has not been verified** | **SSLPeerUnverifiedException** |
| **-2** | **Error in the operation of the SSL protocol** | **SSLProtocolException** |

### iOS

| Error Code | Description | NSError |
| --- | --- | --- |
| 1 | The connection timed out. | -1001 NSURLErrorTimedOut |
| 2 | The connection failed due to an unsupported URL scheme. | -1002 NSURLErrorUnsupportedURL |
| 3 | The connection failed because the device is not connected to the internet. | -1009 NSURLErrorNotConnectedToInternet |
| 0 | The connection failed because the host could not be found. / A server with the specified hostname could not be found. | -1003 NSURLErrorCannotFindHost |
| -1 | An unknown error occurred. | -998 NSURLErrorTimedOut |
| -1 | The connection failed due to a malformed URL. | -1000 NSURLErrorBadURL |
| -1 | The connection failed because a connection cannot be made to the host. | -1004 NSURLErrorCannotConnectToHost |
| -1 | The connection failed because the network connection was lost. | -1005 NSURLErrorNetworkConnectionLost |
| -1 | The connection failed because the DNS lookup failed. | -1006 NSURLErrorDNSLookupFailed |
| -1 | The HTTP connection failed due to too many redirects. | -1007 NSURLErrorHTTPTooManyRedirects |
| -1 | The connection’s resource is unavailable. | -1008 NSURLErrorResourceUnavailable |
| -1 | The connection was redirected to a nonexistent location. | -1010 NSURLErrorRedirectToNonExistentLocation |
| -1 | The connection received an invalid server response. | -1011 NSURLErrorBadServerResponse |
| -1 | The connection failed because the user cancelled required authentication. | -1012 NSURLErrorUserCancelledAuthentication |
| -1 | The connection failed because authentication is required. | -1013 NSURLErrorUserAuthenticationRequired |
| -1 | The resource retrieved by the connection is zero bytes. | -1014 NSURLErrorZeroByteResource |
| -1 | The connection cannot decode data encoded with a known content encoding. | -1015 NSURLErrorCannotDecodeRawData |
| -1 | The connection cannot decode data encoded with an unknown content encoding. | -1016 NSURLErrorCannotDecodeContentData |
| -1 | The connection cannot parse the server’s response. | -1017 NSURLErrorCannotParseResponse |
| -1 | The connection failed because international roaming is disabled on the device. | -1018 kCFURLErrorInternationalRoamingOff |
| -1 | The connection failed because a call is active. | -1019 kCFURLErrorCallIsActive |
| -1 | The connection failed because data use is currently not allowed on the device. | -1020 kCFURLErrorDataNotAllowed |
| -1 | The connection failed because its request’s body stream was exhausted. | -1021 kCFURLErrorRequestBodyStreamExhausted |
| **-3** | **The connection was cancelled.** | **-999 NSURLErrorCancelled** |

## Testing & Simulating MITM

### You will need
- Desktop/Notebook
- Mobile device (Android/iOS)

Note: Both must be connected to the same network

###  Setup

1. Download & install [mitmproxy](https://mitmproxy.org/)
2. In terminal run `mitmproxy`
3. On device:
- Go to "Edit network" (on Android, long press current conneted network at Wi-Fi's menu)
- Set proxy to "manual"
- Set proxy hostname to PC's local ip address
- Set proxy port to 8080
- Save changes
- Open web browser, load any website and you should see mitproxy intercepting the traffic:

![mitmproxy intercepting traffic](https://i.imgur.com/Towh4eT.png)

4. Now check your app and you should see how when you make a request through sendRequest, it will fail with code -2, indicating an insecure network:

![App cancelled request](https://i.imgur.com/KqS72dD.png)
[PinnigTest demo app](https://github.com/avidueira/PinningTest)
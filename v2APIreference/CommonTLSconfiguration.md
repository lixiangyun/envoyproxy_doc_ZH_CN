## TLS配置

### Common TLS configuration
### DataSource
[DataSource proto]()

```
{
  "filename": "..."
}
```
- **filename**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) Local filesystem data source.


Precisely one of filename must be set.

### TlsParameters
[TlsParameters proto]()

```
{
  "tls_minimum_protocol_version": "...",
  "tls_maximum_protocol_version": "...",
  "cipher_suites": [],
  "ecdh_curves": []
}
```
- **tls_minimum_protocol_version**</br>
	([TlsParameters.TlsProtocol](#)) Minimum TLS protocol version.

- **tls_maximum_protocol_version**</br>
	([TlsParameters.TlsProtocol](#)) Maximum TLS protocol version.

- **cipher_suites**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) If specified, the TLS listener will only support the specified cipher list. If not specified, the default list:


[ECDHE-ECDSA-AES128-GCM-SHA256|ECDHE-ECDSA-CHACHA20-POLY1305]()
[ECDHE-RSA-AES128-GCM-SHA256|ECDHE-RSA-CHACHA20-POLY1305]()
### ECDHE-ECDSA-AES128-SHA256
### ECDHE-RSA-AES128-SHA256
### ECDHE-ECDSA-AES128-SHA
### ECDHE-RSA-AES128-SHA
### AES128-GCM-SHA256
### AES128-SHA256
### AES128-SHA
### ECDHE-ECDSA-AES256-GCM-SHA384
### ECDHE-RSA-AES256-GCM-SHA384
### ECDHE-ECDSA-AES256-SHA384
### ECDHE-RSA-AES256-SHA384
### ECDHE-ECDSA-AES256-SHA
### ECDHE-RSA-AES256-SHA
### AES256-GCM-SHA384
### AES256-SHA256
### AES256-SHA
- **will be used.**</br>

- **ecdh_curves**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) If specified, the TLS connection will only support the specified ECDH curves. If not specified, the default curves (X25519, P-256) will be used.

Enum TlsParameters.TlsProtocol
[TlsParameters.TlsProtocol proto]()

### TLS_AUTO
	(DEFAULT) ?Envoy will choose the optimal TLS version.

### TLSv1_0
?TLS 1.0
### TLSv1_1
?TLS 1.1
### TLSv1_2
?TLS 1.2
### TLSv1_3
?TLS 1.3
### TlsCertificate
[TlsCertificate proto]()

```
{
  "certificate_chain": "{...}",
  "private_key": "{...}"
}
```
- **certificate_chain**</br>
	([DataSource](#)) The TLS certificate chain.

- **private_key**</br>
	([DataSource](#)) The TLS private key.

### TlsSessionTicketKeys
[TlsSessionTicketKeys proto]()

```
{
  "keys": []
}
```
- **keys**</br>
	([DataSource](#), REQUIRED) Keys for encrypting and decrypting TLS session tickets. The first key in the array contains the key to encrypt all new sessions created by this context. All keys are candidates for decrypting received tickets. This allows for easy rotation of keys by, for example, putting the new key first, and the previous key second.


If session_ticket_keys is not specified, the TLS library will still support resuming sessions via tickets, but it will use an internally-generated and managed key, so sessions cannot be resumed across hot restarts or on different hosts.

Each key must contain exactly 80 bytes of cryptographically-secure random data. For example, the output of openssl rand 80.

### Attention

Using this feature has serious security considerations and risks. Improper handling of keys may result in loss of secrecy in connections, even if ciphers supporting perfect forward secrecy are used. See https://www.imperialviolet.org/2013/06/27/botchingpfs.html for some discussion. To minimize the risk, you must:

### Keep the session ticket keys at least as secure as your TLS certificate private keys
### Rotate session ticket keys at least daily, and preferably hourly
### Always generate keys using a cryptographically-secure random data source
### CertificateValidationContext
[CertificateValidationContext proto]()

```
{
  "trusted_ca": "{...}",
  "verify_certificate_hash": [],
  "verify_subject_alt_name": []
}
```
- **trusted_ca**</br>
	([DataSource](#)) TLS certificate data containing certificate authority certificates to use in verifying a presented client side certificate. If not specified and a client certificate is presented it will not be verified. By default, a client certificate is optional, unless one of the additional options (require_client_certificate, verify_certificate_hash, or verify_subject_alt_name) is also specified.

- **verify_certificate_hash**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) If specified, Envoy will verify (pin) the hex-encoded SHA-256 hash of the presented certificate.

- **verify_subject_alt_name**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) An optional list of subject alternative names. If specified, Envoy will verify that the certificate’s subject alternative name matches one of the specified values.

### CommonTlsContext
[CommonTlsContext proto]()

TLS context shared by both client and server TLS contexts.

```
{
  "tls_params": "{...}",
  "tls_certificates": [],
  "validation_context": "{...}",
  "alpn_protocols": []
}
```
- **tls_params**</br>
	([TlsParameters](#)) TLS protocol versions, cipher suites etc.

- **tls_certificates**</br>
	([TlsCertificate](#)) Multiple TLS certificates can be associated with the same context. E.g. to allow both RSA and ECDSA certificates, two TLS certificates can be configured.


### Attention

Although this is a list, currently only a single certificate is supported. This will be relaxed in the future.

- **validation_context**</br>
	([CertificateValidationContext](#)) How to validate peer certificates.

- **alpn_protocols**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) Supplies the list of ALPN protocols that the listener should expose. In practice this is likely to be set to one of two values (see the codec_type parameter in the HTTP connection manager for more information):


“h2,http/1.1” If the listener is going to support both HTTP/2 and HTTP/1.1.
“http/1.1” If the listener is only going to support HTTP/1.1.
There is no default for this parameter. If empty, Envoy will not expose ALPN.

### UpstreamTlsContext
[UpstreamTlsContext proto]()

```
{
  "common_tls_context": "{...}",
  "sni": "..."
}
```
- **common_tls_context**</br>
	([CommonTlsContext](#)) Common TLS context settings.

- **sni**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) SNI string to use when creating TLS backend connections.

### DownstreamTlsContext
[DownstreamTlsContext proto]()

```
{
  "common_tls_context": "{...}",
  "require_client_certificate": "{...}",
  "session_ticket_keys": "{...}"
}
```
- **common_tls_context**</br>
	([CommonTlsContext](#)) Common TLS context settings.

- **require_client_certificate**</br>
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) If specified, Envoy will reject connections without a valid client certificate.

- **session_ticket_keys**</br>
	([TlsSessionTicketKeys](#)) TLS session ticket key settings.


Only one of session_ticket_keys may be set.


## 返回
- [上一级](../v2APIreference.md)
- [首页目录](../README.md)
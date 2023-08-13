# ASN.1, DER, PEM

File extension is immaterial

`ASN.1 <-> DER <-> PEM`

- **Abstract Syntax Notation One**(ASN.1) - is interface description language(IDL) for describing data structures. Widely used in telecommunications, networking, cryptography

- **Distinguished Encoding Rules (DER)** - binary. which is one of main encoding format for ASN.1. It is a subset of Basic Encoding Rules (BER). widely used for cryptography. `.der`, `.cer` can be used

- **Privacy-Enhanced Mail (PEM)** - base64 encoded DER. Block or blocks(e.g. a chain of certificates) of base-64 with plain-text headers and footers to mark the start and end. Main purpose is used for cryptography. It is difficult to transmit binary DER file, for example mail, which supports ASCII. You can find that there are a lot of file extensions with PEM inside like `.pem`, `.crt`, `.cer` `.key` (for public or private keys), but you should not rely on it. You should open this file and check header/footer

```
-----BEGIN CERTIFICATE-----
...
-----END CERTIFICATE-----
```

- Public-Key Cryptography Standards #7: Cryptographic Message Syntax(PKCS#7: CMS). It can be stored as DER or PAM. It is a multi-purpose format for encrypted data, authenticated data or signed data(except private keys)... It can contains attached or detached signature with chain of certificates. `.p7b` - usually PEM, `.p7s` - signature file in DER format

- Public-Key Cryptography Standards #12: Cryptographic Message Syntax(PKCS #12: CMS) the same as PKCS#7 but contains private key included

PKCS #7 in PEM format

```
-----BEGIN PKCS7-----
...
-----END PKCS7-----
```

**Example of detached PKCS#7 signature**

data.txt: `Hello, World!`

1. create private key and self-signed certificate, fill fields like: pass phrase for your private key and fields for your certificate:

    ```
    //openssl req -x509 -newkey rsa:4096 -keyout <name_for_new_private_key> -out <name_for_new_certificate> -days 365
    openssl req -x509 -newkey rsa:4096 -keyout myPrivateKey -out myCert -days 365
    ```

    myPrivateKey has PEM format:

    ```
    -----BEGIN ENCRYPTED PRIVATE KEY-----
    MIIJnzBJBgkqhkiG9w0BBQ0wPDAbBgkqhkiG9w0BBQwwDgQI9Qxgmq0oK+ICAggA
    ...
    -----END ENCRYPTED PRIVATE KEY-----
    ```

    myCert has PEM format:
    ```
    -----BEGIN CERTIFICATE-----
    MIIFMjCCAxoCCQD3+UllOGhfkTANBgkqhkiG9w0BAQsFADBbMQswCQYDVQQGEwJV
    ...
    -----END CERTIFICATE-----
    ```

2. sign data.txt using phrase for your private key

    ```
    //openssl cms -sign -signer <cert_file> -inkey <private_key_file> -binary -in <data_file> -outform [DER, PEM] -out <name_for_new_signature>
    ```

    generating dataSignature in PEM format:

    ```
    openssl cms -sign -signer myCert -inkey myPrivateKey -binary -in data.txt -outform PEM -out dataSignature
    ```

    ```
    -----BEGIN CMS-----
    MIII/gYJKoZIhvcNAQcCoIII7zCCCOsCAQExDTALBglghkgBZQMEAgEwCwYJKoZI
    ...
    -----END CMS-----
    ```
    generating dataSignature in DER format:

    ```
    openssl cms -sign -signer myCert -inkey myPrivateKey -binary -in data.txt -outform DER -out dataSignature
    3082 08fe 0609 2a86 4886 f70d 0107 02a0
    ...
    ```

3. Verify signature

    ```
    //openssl cms -verify -binary -inform <PEM_or_DER_format_of_signature> -in <signature_file> -content <data_file> -noverify > /dev/null
    ```

    //let's say that we generated signature in PEM format on previous step

    ```
    openssl cms -verify -binary -inform PEM -in dataSignature -content data.txt -noverify > /dev/null

    //Verification successful

    //-noverify - Do not verify the signers certificate of a signed message
    //-nointern - By default searching signing certificate inside. With this option only the certificates specified in the -certfile option are used.
    ```

    You are able to review PKCS7:

    ```
    //openssl cms -cmsout -in <signature_file> -inform [PEM, DER] -noout -print
    openssl cms -cmsout -in dataSignature -inform PEM -noout -print
    CMS_ContentInfo: 
    contentType: pkcs7-signedData (1.2.840.113549.1.7.2)
    d.signedData: 
        version: 1
        digestAlgorithms:
            algorithm: sha256 (2.16.840.1.101.3.4.2.1)
            parameter: <ABSENT>
        encapContentInfo: 
        eContentType: pkcs7-data (1.2.840.113549.1.7.1)
        eContent: <ABSENT>
        certificates:
        d.certificate: 
            cert_info: 
            version: <ABSENT>
            serialNumber: 17868393695656042385
    ...
    ```

    ASN.1 looks like or use lapo.it:

    ```
    //openssl asn1parse -inform [PEM, DER] -i -in <(fold -w 64 <signature_file>)
    openssl asn1parse -inform PEM -i -in dataSignature

    //for PEM sometimes helps next command: openssl asn1parse -i -in dataSignature <(fold -w 64 dataSignature)
    0:d=0  hl=4 l=2302 cons: SEQUENCE          
        4:d=1  hl=2 l=   9 prim:  OBJECT            :pkcs7-signedData
    15:d=1  hl=4 l=2287 cons:  cont [ 0 ]        
    19:d=2  hl=4 l=2283 cons:   SEQUENCE          
    23:d=3  hl=2 l=   1 prim:    INTEGER           :01
    26:d=3  hl=2 l=  13 cons:    SET               
    28:d=4  hl=2 l=  11 cons:     SEQUENCE          
    30:d=5  hl=2 l=   9 prim:      OBJECT            :sha256
    41:d=3  hl=2 l=  11 cons:    SEQUENCE          
    43:d=4  hl=2 l=   9 prim:     OBJECT            :pkcs7-data
    54:d=3  hl=4 l=1334 cons:    cont [ 0 ]        
    58:d=4  hl=4 l=1330 cons:     SEQUENCE          
    62:d=5  hl=4 l= 794 cons:      SEQUENCE          
    66:d=6  hl=2 l=   9 prim:       INTEGER           :F7F9496538685F91
    77:d=6  hl=2 l=  13 cons:       SEQUENCE          
    79:d=7  hl=2 l=   9 prim:        OBJECT            :sha256WithRSAEncryption
    ...
    ```

[Signature, Certificate](https://stackoverflow.com/questions/40061263/what-is-ca-certificate-and-why-do-we-need-it/59307015#59307015)

Source: [https://stackoverflow.com/a/75592073/5872952](https://stackoverflow.com/a/75592073/5872952)
    
[[Back to Top]](#asn1-der-pem)
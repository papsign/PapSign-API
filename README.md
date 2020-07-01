# PapSign-API
PapSign electronic signature API

## URLs

[API](https://api.papsign.com)

[OpenAPI](https://api.papsign.com/openapi.json)

## Get OAuth keys

1. Go to [papsign.com/account](https://papsign.com/account)
2. Click `Add OAuth Client`
3. Fill In the information
4. Give a (Java) Regex for the authorized redirect addresses

## Create a printable signature

[PNG](https://api.papsign.com/swagger-ui/index.html?url=/openapi.json#/Signature/post_v1_signature_sign_datamatrix_png)

[JPG](https://api.papsign.com/swagger-ui/index.html?url=/openapi.json#/Signature/post_v1_signature_sign_datamatrix_jpg)

Payload:
```javascript
{
  "identityID": 0, // The id of the identity you wish to sign. Get yours from /v1/identities/me/{permission}
  "keySize": 0, // 1024, 2048, 3072, bigger can contain more data
  "text": "string", // arbitrary text, generally for context, not suitable for automation
  "userData": { // CSV-like key/values, best for automation. Make sure you agree on a standard if you are going to interoperate on different systems
    "additionalProp1": "string",
    "additionalProp2": "string",
    "additionalProp3": "string"
  }
}
```

The response will be a binary stream with the image.

Only the data contained in the signature is protected. Only the verified elements of your identity will be used to sign the signature.

## Sign A PDF document

1. [Create a file](https://api.papsign.com/swagger-ui/index.html?url=/openapi.json#/File/post_v1_files_pdf)
2. [Create a signature process](https://api.papsign.com/swagger-ui/index.html?url=/openapi.json#/SignatureProcess/post_v1_signature_process_pdf)  
`keep`: Keep the file when the process is deleted, set to false to skip step 7  
`oneOf`: Array determining who is allowed to sign, if one of those mentioned in the array have signed the slot it is valid.
3. (optional) [Send an invitation email to sign the document](https://api.papsign.com/swagger-ui/index.html?url=/openapi.json#/SignatureProcess/post_v1_signature_process_pdf__processID__invite)
4. (optional if you are not a signer) [Sign the PDF](https://api.papsign.com/swagger-ui/index.html?url=/openapi.json#/SignatureProcess/put_v1_signature_sign_pdf)
5. [Download the signed document](https://api.papsign.com/swagger-ui/index.html?url=/openapi.json#/File/get_v1_files_pdf__fileID__file)
6. [Delete the signature process](https://api.papsign.com/swagger-ui/index.html?url=/openapi.json#/SignatureProcess/delete_v1_signature_process_pdf__processID_)
7. (optional if `keep` was set to false) [Delete the file](https://api.papsign.com/swagger-ui/index.html?url=/openapi.json#/File/delete_v1_files_pdf__fileID_)

Deletion is necessary as the account has storage limits.

## Verify a PDF, scan, or image

[Verify a document](https://api.papsign.com/swagger-ui/index.html?url=/openapi.json#/Verify/post_v1_verification_verify_document)

If the response contains `pdf`, it is a signed PDF.
If the response contains `standard`, each of those entries is a printable signature
A signed PDF can contain printable signatures.

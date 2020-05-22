# JupyterHub tokenauthenticator - A JWT Token Authenticator for JupyterHub(forked from https://github.com/mogthesprog/jwtauthenticator)

Authenticate to Jupyterhub using a query parameter for the JSONWebToken, or by an authenticating proxy that can set the Authorization header with the content of a JSONWebToken. Authorisation of users can be made through the domains claim in JSONWebToken by listing all the permitted list of domains in the cnfiguration file.

## Installation

This package can be installed with pip:

```
pip install git+git://github.com/cibinsb/jwtauthenticator.git
```

Alternately, you can clone this repository and run:

```
cd jwtauthenticator
pip install -e .
```

## Configuration

You should edit your :file:`jupyterhub_config.py` to set the authenticator class,
You'll also need to set some configuration options including the location of the signing certificate (in PEM format), field containing the userPrincipalName or sAMAccountName/username, and the expected audience of the JSONWebToken. This last part is optional, if you set audience to an empty string then the authenticator will skip the validation of that field.

```
# one of "secret" or "signing_certificate" must be given.  If both, then "secret" will be the signing method used.
c.JSONWebTokenAuthenticator.secret = '<insert-256-bit-secret-key-here>'            # The secrect key used to generate the given token
# -OR-
c.JSONWebTokenAuthenticator.signing_certificate = '/foo/bar/adfs-signature.crt'    # The certificate used to sign the incoming JSONWebToken, must be in PEM Format

c.JSONWebTokenAuthenticator.username_claim_field = 'upn'                           # The claim field contianing the username/sAMAccountNAme/userPrincipalName
c.JSONWebTokenAuthenticator.expected_audience = 'https://myApp.domain.local/'               # This config option should match the aud field of the JSONWebToken, empty string to disable the validation of this field.
#c.JSONWebLocalTokenAuthenticator.create_system_users = True                       # This will enable local user creation upon authentication, requires JSONWebTokenLocalAuthenticator
#c.JSONWebTokenAuthenticator.header_name = 'Authorization'                         # default value
c.JSONWebTokenAuthenticator.permitted_domains = [''] # List of permitted domain names for authorisation
c.JSONWebTokenAuthenticator.domains_claim_field = 'domains'
```

## How to add configurations to Helm chart

1. Add the configurations inline
```
hub:
  image:
         name: jupyterhub-k8s-hub
         tag: '<version>'
  extraConfig:  
        newConfig: |
            # one of "secret" or "signing_certificate" must be given.  If both, then "secret" will be the signing method used.
            c.JSONWebTokenAuthenticator.secret = '<insert-256-bit-secret-key-here>'            # The secrect key used to generate the given token
            # -OR-
            c.JSONWebTokenAuthenticator.signing_certificate = '/foo/bar/adfs-signature.crt'    # The certificate used to sign the incoming JSONWebToken, must be in PEM Format

            c.JSONWebTokenAuthenticator.username_claim_field = 'upn'                           # The claim field contianing the username/sAMAccountNAme/userPrincipalName
            c.JSONWebTokenAuthenticator.expected_audience = 'https://myApp.domain.local/'               # This config option should match the aud field of the JSONWebToken, empty string to disable the validation of this field.
            #c.JSONWebLocalTokenAuthenticator.create_system_users = True                       # This will enable local user creation upon authentication, requires JSONWebTokenLocalAuthenticator
            #c.JSONWebTokenAuthenticator.header_name = 'Authorization'                         # default value
            c.JSONWebTokenAuthenticator.permitted_domains = [''] # List of permitted domain names for authorisation
            c.JSONWebTokenAuthenticator.domains_claim_field = 'domains'
```
2. Add the configurations under auth namespace
```
hub:
  image:
         name: jupyterhub-k8s-hub
         tag: '<version>'
auth:
    type: custom
    custom:
      className: jwtauthenticator.jwtauthenticator.JSONWebTokenAuthenticator
      config:
        signing_certificate: '/path/to/file.pem'
        username_claim_field: "upn"  # The user claim feild name
        header_name: 'Authorization' # default value
        permitted_domains: [''] # List of permitted domain names for authorisation
        domains_claim_field: 'domains' # default value
```
You should be able to start jupyterhub. :)

## Issues

If you have any issues or bug reports, all are welcome in the issues section. I'll do my best to respond quickly.

## Contribution

If you want to fix the bugs yourself then raise a PR and I'll take a look :)

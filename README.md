This is a WIP fork of google-id-token that works with firebase id tokens

# GoogleIDToken

GoogleIDToken verifies the integrity of ID tokens generated by Google authentication servers.

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'google-id-token', git: 'https://github.com/hanrelan/firebase-id-token'
```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install google-id-token

## Usage

GoogleIDToken currently provides a single useful class `Validator`, which provides a single method `#check`, which parses and validates the integrity of an ID Token allegedly generated by Google auth servers.

```ruby
validator = GoogleIDToken::Validator.new(issuers: ["https://securetoken.google.com/<projectId>"]) # where projectId is your firebase project id
begin
  payload = validator.check(token, required_audience, optional_client_id)
  email = payload['email']
rescue GoogleIDToken::ValidationError => e
  report "Cannot validate: #{e}"
end
```

## Configuration

Creating a new validator takes a single optional hash argument. If the hash has an entry for :x509_key, that value is taken to be a key as created by OpenSSL::X509::Certificate.new, and the token is validated using that key. If there is no such entry, the keys are fetched from the Google certs endpoint https://www.googleapis.com/oauth2/v1/certs. The certificates are cached for some time (default: 1 hour) which can be configured by adding the :expiry argument to the initialization hash (in seconds).

### expiry

Expiry defines the the time (in seconds) in which the cached Google certificates are valid.

```ruby
GoogleIDToken::Validator.new(expiry: 1800) # 30 minutes
```

### x509_cert

x509_cert can be provided to manually define a certificate to validate the tokens.

```ruby
cert = OpenSSL::X509::Certificate.new(File.read('my-cert.pem'))
validator = GoogleIDToken::Validator.new(x509_cert: cert)
```

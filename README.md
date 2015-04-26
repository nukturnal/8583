### ISO 8583 Financial Messaging for Ruby

This package currently contains code for coding an decoding ISO 8583
Financial Message.

#####This library has the following features:

  1. Supports 128 bits bitmap (automatically).
  2. Supports hexadecimal bitmaps.

## Usage

The best place to understand what this library has to offer is reading the
[message.rb](https://github.com/MaG21/8583/blob/master/lib/iso8583/message.rb)
file. However, one can use it like so:

#### Create defs
```ruby
class CustomMessage < ISO8583::Message
        include ISO8583

        mti_format N, :length => 4
        mti 100, "Authorization Request Acquirer Gateway"
        mti 110, "Authorization Request Response Issuer Gateway"

        bmp  2, "Primary Account Number (PAN)",               LLVAR_N,   :max    => 19
        bmp  3,  "Processing Code",                           N,         :length =>  6
        bmp  4,  "Amount (Transaction)",                      N,         :length => 12
        bmp  5,  "Amount, Reconciliation" ,                   N,         :length => 12
        bmp  6,  "Amount, Cardholder Billing" ,               N,         :length => 12

        bmp 65, "new bitmap", LLVAR_ANS, :max => 99

        bmp_alias 2, :pan
        bmp_alias 3, :proc_code
        bmp_alias 4, :amount
        bmp_alias 6, :reconciliation
end
```

Once you've defined the fields you'll be using, then you can proceed to create
your messages.

#### Build Message
```ruby
msg = CustomMessage.new(nil)

msg.mti = 100
msg[3]  = 3
msg[4]  = 4
msg[5]  = 5
msg[6]  = 6
msg[65] = 'STRING'

puts msg
```
######Output:

```text
MTI:100 (Authorization Request Acquirer Gateway)

003            Processing Code : 3
004       Amount (Transaction) : 4
005     Amount, Reconciliation : 5
006 Amount, Cardholder Billing : 6
065                 new bitmap : STRING
```

#### ISO
```ruby
iso = msg.to_b
p iso
```

###### Output

```text
"0100\xBC\x00\x00\x00\x00\x00\x00\x00\x80\x00\x00\x00\x00\x00\x00\x0000000300000000000400000000000500000000000606STRING"
```

#### Parse Message
```ruby
parsed = CustomMessage.parse(iso)
puts parsed
```
###### Output

```text
MTI:100 (Authorization Request Acquirer Gateway)

003            Processing Code : 3
004       Amount (Transaction) : 4
005     Amount, Reconciliation : 5
006 Amount, Cardholder Billing : 6
065                 new bitmap : STRING
```

## Source
[a2800276](https://github.com/a2800276) is the original author, please give them all the credits.

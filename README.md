
### How to fetch Polygon (MATIC) Plasma Deposit History

[Polygon Scan](https://polygonscan.com/) team is working on exposing endpoint, in the mean time you can use following methods.

1. Grab from HTML reponse from polygonscan site. (using RUBY for eg)
```ruby
module Blockchain
  class MaticPlasmaDeposit
    def self.fetch(address = '0xffbc10d5520d549f36dd6f4774710e121d9ea1d1')
      doc = Nokogiri::HTML(URI.open("https://polygonscan.com/address-deposit?a=#{address}"))
      headers = %w[txn_hash timestamp age from type to amount token]
      # get table rows
      rows = []
      doc.xpath('//*/table/tbody/tr').each_with_index do |row, i|
        rows[i] = {}
        row.xpath('td').each_with_index do |td, j|
          rows[i][headers[j]] = td.text
        end
      end
      return [] if rows.empty?

      deposit_response_data(rows)
    end

    def self.deposit_response_data(rows)
      deposits = []
      rows.each do |row|
        deposits << {
          isError: '0',
          tokenSymbol: 'MATIC',
          tokenDecimal: 18,
          contractAddress: '',
          hash: row[:txn_hash],
          timeStamp: row[:timestamp],
          from: row[:from],
          to: row[:to],
        }
      end
      deposits
    end
  end
end
```
2. [Use etherscan API](https://docs.etherscan.io/api-endpoints/accounts#get-a-list-of-erc20-token-transfer-events-by-address)

```ruby 
response = HTTParty.get('https://api.etherscan.io/api?module=account&action=tokentx&address=0xffbc10d5520d549f36dd6f4774710e121d9ea1d1&startblock=0&endblock=999999999&sort=asc')
```
from response only fetch data with `contractAddress: 0x401f6c983ea34274ec46f84d70b31c151321188b` which is Polygon's plasma bridge address.










